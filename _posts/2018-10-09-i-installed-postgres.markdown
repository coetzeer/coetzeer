---
layout: post
title: "Re-installing software Part II : Postgres"
date: 2018-10-09
description: Installing Postgres Ansible Shtyle
img: postgres_elephant.jpg # Add image post (optional)
tags: [postgres, software install, ansible] # add tag
toc: true
---

One of the first things that has to be installed is a database to support NextCloud, Owncloud, Gogs and other tools that might need it.

First thing is to make the jail:

```bash
iocage create -n postgres dhcp=on allow_sysvipc=1 bpf=yes vnet=on -t ansible_template boot="on"
# add in the shared storage
/mnt/vol01/apps/postgres/ /mnt/vol01/iocage/jails/postgres/root/var/db/postgres nullfs rw 0 0

```

I had previously looked at installing postgresdb, and did up a standalone playbook that is hosted on github: https://github.com/coetzeer/freebsd_postgres_ansible .

# For the impatient

If you're not willing to step through the detailed breakdown below, simply run the freebsd_postgres_ansible.yml playbook against localhost:

```bash
git clone https://github.com/coetzeer/freebsd_postgres_ansible
cd freebsd_postgres_ansible

# crack open play.yml and change the following to values that suit you:
# pgadmin_password: "changeme"
# pgadmin_username: "coetzeer@gmail.com"
# pgadmin_db_username: pgadmin
# pgadmin_db_password: pgadmin

pkg install ansible
ansible-playbook -i localhost play.yml
```

This sets up a single instance postges instance, with pgadmin running through an nginx on port 80. Log into pgadmin with the top two values you changed above. Pgadmin will be connecting to your postgres database with the bottom two credentials that you changed above.

# Blow by blow

This playbook was based on the instructions over at: https://www.howtoforge.com/tutorial/how-to-install-postgresql-and-pgadmin-on-freebsd-11/

## Step 1: Install the packages

The following step installs each of the listed packages via the __pkg__ program. They're the postgres server and client, python dependencies for pgadmin and some other dependencies to make life easier.

```yaml

- name: "Install packages from pkg"
      pkgng:
        name: "{{item}}"
        state: present
      with_items:
      - postgresql96-client
      - postgresql96-server
      - py27-pip
      - py27-virtualenv
      - wget
      - py27-sqlite3
      - rinetd
      - ca_root_nss

```

## Step 2: Install the remaining python dependencies

These two dependencies are mainly to support this playbook (although I think psycopg2 is required by pgadmin). This step uses the pip python package manager rather than the system package manager.

```yaml
  - name: Pip install into system python
    pip:
       name: "{{item}}"
    with_items:
       - psycopg2
       - pexpect
```

## Step 3: Enable the postgres database

This is a FreeBSD specific step that enables the postgres service i.e. it adds an instruction to /etc/rc.conf that instructs the init system to invoke the postgres init script. Which should start it.

```yaml
  - name: Enable the postgres service
    shell: "sysrc postgresql_enable=yes"
```

## Step 4: initialise the postgresdb

This step uses the postgres init script to initialize the new postgresqldb installation. This creates the postgres data directory that stores all the configuration, data file, WAL files (write ahead logs) - all the things that make a database a database:

```yaml
- name: Intialize the database
  shell: service postgresql initdb
  args:
    creates: "/var/db/postgres/data96"
```

It won't run twice provided that the first run successfully created the __/var/db/postgres/data96__ directory.

## Step 5: Start the postgres database.

```yaml
  - name: Start the db
    service:
       name: postgresql
       state: started
       enabled: yes
```

## Step 6: Open up the db to allow people to connect

```yaml
- lineinfile:
    path: /var/db/postgres/data96/postgresql.conf
    regexp: '^listen_addresses '
    insertafter: '^#listen_addresses '
    line: "listen_addresses = '*'"

- lineinfile:
    path: /var/db/postgres/data96/pg_hba.conf
    line: "host    all             all             0.0.0.0/0               md5"

- name: Start the db
  service:
    name: postgresql
    state: restarted
    enabled: yes

```

Set postgres to listen on all network cards, and allow all hosts to connect to this db using the md5 password mechanism. This is obviously a bit more open that you'd typically want for a production installation but for our home server, it reduces the fiddling when we want to create new users and schemas.

Restart the postgres server to pick up the config changes.


## Step 7: Create the pgadmin user and database

Create the pgadmin databse and user using the built in ansible postgres functions.

```yaml

- postgresql_db:
  name: pgadmin

- postgresql_user:
  db: pgadmin
  name: "{{pgadmin_db_username}}"
  password: "{{pgadmin_db_password}}"
  role_attr_flags: CREATEDB,SUPERUSER,CREATEROLE

```

This is more or less equivalent to

```bash
su postgres -c 'createuser -sdrP pgadmin_db_username'
su postgres -c 'createdb pgadmin'
su postgres -c  "echo 'GRANT ALL PRIVILEGES ON DATABASE pgadmin_db_username TO pgadmin' | psql"
```

## Step 8: Create the pgadmin virtualenv and put some libraries into it

Although it's not strictly required, installing pgadmin into a separate virtual environment (venv) does provide a degree of isolation between versions of pgadmin, and allow easy rollbacks (if you use a new venv). These two steps first create the virtual env, and then uses the built in pip package wrapper provided by ansible (that allows you to specify a virtual environment) to install some dependencies.



```yaml
- name: create the pgadmin virtualenv
  shell: virtualenv pgadmin4
  args:
    chdir: /usr/local/
    creates: "/usr/local/pgadmin4"

- pip:
    name: "{{item}}"
    virtualenv: "/usr/local/pgadmin4"
  with_items:
    - cryptography
    - pyopenssl
    - ndg-httpsclient
    - pyasn1
```

## Step 9: Create the pgadmin virtualenv and put some libraries into it

Download the new version of pgadmin. This comes down as a whl file (a wheel file), and then use the ansible pip wrapper to install it into your new venv.

```yaml
- name: download pgadmin
  shell: wget --no-check-certificate \
 https://ftp.postgresql.org/pub/pgadmin/pgadmin4/v3.3/pip/pgadmin4-3.3-py2.py3-none-any.whl
  args:
    chdir: /usr/local/pgadmin4
    creates: "/usr/local/pgadmin4/pgadmin4-3.3-py2.py3-none-any.whl"

- pip:
    name: file:///usr/local/pgadmin4/pgadmin4-3.3-py2.py3-none-any.whl
    virtualenv: "/usr/local/pgadmin4"
````

## Step 10: Intialize pgAdmin

So with some neat hackery here, the pgadmin4/setup.py script is invoked. The clever bit is that
1. It's invoked inside a virtualenv
2. It has a guard that stops it running twice by checking to see if the __/var/lib/pgadmin/storage__ directory is created
3. The 'expect' module is used to feed the setup script a username (email address) and password for pgadmin. This allows us to run this script in batch mode, without user interaction.

```yaml
- file:
  path: /var/lib/pgadmin
  state: directory

- name: initialize pgadmin
  expect:
    command: bin/python2.7 lib/python2.7/site-packages/pgadmin4/setup.py
    chdir: /usr/local/pgadmin4
    creates: "/var/lib/pgadmin/storage"
    responses:
      "Email address:": "{{pgadmin_username}}"
      "Password:": "{{pgadmin_password}}"
      "Retype password:": "{{pgadmin_password}}"

```

## Step 11: Configure pgAdmin

pgAdmin comes with a default config file called 'config_local.py'. This step makes a copy of that file (only if the copy hasn't been made already), and sets 2 values: the listen port (5050) and the listen address (0.0.0.0 - all adapters).


```yaml
- name: Create the local config
  shell: "cp config.py config_local.py"
  args:
    chdir: "{{pgadmin_install_dir}}"
    creates: "{{pgadmin_install_dir}}/config_local.py"

- lineinfile:
  path: "{{pgadmin_install_dir}}/config_local.py"
  regexp: '^DEFAULT_SERVER ='
  line: "DEFAULT_SERVER = '0.0.0.0'"

- lineinfile:
  path: "{{pgadmin_install_dir}}/config_local.py"
  regexp: '^DEFAULT_SERVER_PORT ='
  line: "DEFAULT_SERVER_PORT = 5050"
```

## Step 12: setup rinetd

Because I will never remember port 5050, I set up rinetd to forward traffic from port 80 to port 5050. First though I enable it in rc.conf and tell it to product a log file. Lastly I start rinetd.

```yaml
- name: Enable the rinetd service
  shell: "sysrc rinetd_enable=YES"

- lineinfile:
  path: /usr/local/etc/rinetd.conf
  regexp: '.*5050$'
  line: "{{ansible_default_ipv4.address}} 80 {{ansible_default_ipv4.address}} 5050"
  create: True

- lineinfile:
  path: /usr/local/etc/rinetd.conf
  line: "logfile /var/log/rinetd.log"

- lineinfile:
  path: /usr/local/etc/rinetd.conf
  line: "logcommon"

- name: Start the rinetd daemon
  service:
    name: rinetd
    state: started
    enabled: yes  
```


## Step 13: Setup the service artefacts for pgadmin

These steps setup the FreeBSD service file, configure the rc.conf file and then start the service. As I am very proud of the service file (it's my first one), I'm including it here verbatim, in all it's glory.

```yaml
- template:
    src: service.j2
    dest: /usr/local/etc/rc.d/pgadmin
    owner: root
    group: wheel
    mode: "u=rwx,g=r,o=r"

- name: Enable the pgadmin service
  shell: "sysrc pgadmin_enable=YES"

- name: Start the pgadmin daemon
  service:
    name: pgadmin
    state: started
    enabled: yes
```

The service file below is probably the simplest service file possible. Some notable aspects are:
1. The name __pgadmin4__ is important, and it's important to keep it consistent throughout the rest of the file e.g. in the start command and the rc var.
2. the start function touches the pid file and then invokes the /usr/sbin/daemon command to start the pgadmin progress in the background and harvest it's pid files.
3. the __daemon__ command executes the venv python binary against the pgAdmin4.py file. This usually starts pgAdmin in the foreground, but __daemon__ neatly stashes it in the background and sets the pid file for you.
4. the stderr and stdout are sent to /var/log/pgadmin.log


```bash
#!/bin/sh
# PROVIDE: pgadmin4

. /etc/rc.subr

name=pgadmin4
rcvar=pgadmin4_enable

start_cmd="pgadmin4_start"

pidfile="/var/run/${name}.pid"
cd /root/pgadmin4

pgadmin4_start()
{
  touch ${pidfile}
  touch /var/run/pgadmin2.pid
  /usr/sbin/daemon -P ${pidfile} \
      -p /var/run/pgadmin2.pid   \
      -u root                    \
      /root/pgadmin4/bin/python \
      /root/pgadmin4/lib/python2.7/site-packages/pgadmin4/pgAdmin4.py \
        >> /var/log/pgadmin.log 2>&1

}

load_rc_config $name
run_rc_command "$1"

```

And there you have it folks - a postgres database with pgadmin in 2 minutes.


{% include figure image_path="/assets/img/pgadmin.jpg" alt="Viola!" class="image-large image-centre" caption="Tada!" %}
