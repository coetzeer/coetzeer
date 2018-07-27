---
layout: post
title: I installed postgres
date: 2018-05-19
description: I installed postgres
img: i_installed_postgres.jpg # Add image post (optional)
tags: [postgres] # add tag
toc: true
---

I installed postgres.


```


su postgres -c 'createuser -sdrP jira'
su postgres -c 'createdb jira'
su postgres -c  "echo 'GRANT ALL PRIVILEGES ON DATABASE jira TO jira' | psql"


#!/bin/sh


# PROVIDE: pgadmin4

. /etc/rc.subr

name=pgadmin4
rcvar=pgadmin4_enable

#extra_commands="status"

start_cmd="pgadmin4_start"
#status_cmd="pgadmin4_status"

pidfile="/var/run/${name}.pid"
cd /root/pgadmin4

nexus_status()
{
/usr/local/share/nexus/nexus-3.11.0-01/bin/nexus status
}

pgadmin4_start()
{
touch ${pidfile}
touch /var/run/pgadmin2.pid
/usr/sbin/daemon -P ${pidfile} -p /var/run/pgadmin2.pid  -u root /root/pgadmin4/bin/python /root/pgadmin4/lib/python2.7/site-packages/pgadmin4/pgAdmin4.py >> /var/log/pgadmin.log 2>&1

}

load_rc_config $name
run_rc_command "$1"

```
