- Software ideas
	- Landscape
	- Spacewalk and open idm
	- nextcloud and open cloud
	- dns - dnsmasq - logging
		- https://www.pcsuggest.com/configure-dnsmasq-caching-dns-server-linux/
		- https://www.iceflatline.com/2010/02/how-to-install-and-configure-dnsmasq/
	- doing jails with ansible - https://www.keltia.net/howtos/jail-mgmt-with-ansible/
	- lets encrypt
		-  ansible module - http://docs.ansible.com/ansible/latest/letsencrypt_module.html
		-  https://forums.freenas.org/index.php?threads/freenas-11-nextcloud-owncloud-letsencrypt-ssl-tls.57797/
	- ansible
	- remote logging server
	- open vpn clients - https://forums.freenas.org/index.php?threads/how-to-use-openvpn-ipfw-in-a-jail-so-it-only-connects-to-the-vpn.18669/
	- monitoring collectd/grafana/graphite
	- gogs
    - avahi
    	- types - https://github.com/lathiat/avahi/blob/master/service-type-database/service-types -
    	- example - https://raw.githubusercontent.com/lathiat/avahi/master/avahi-daemon/example.service
    - postgres - https://forums.freenas.org/index.php?threads/how-to-install-postgresql-in-9-3.27827/
    - monero mining


adding a new CA certificates
----------------------

    https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html

    Ubuntu

    Copy your CA to dir /usr/local/share/ca-certificates/
    Use command: **sudo cp foo.crt /usr/local/share/ca-certificates/foo.crt**
    Update the CA store: **sudo update-ca-certificates**


    Remove your CA.
    Update the CA store: sudo update-ca-certificates --fresh

    Linux (CentOs 6)


    Install the ca-certificates package: **yum install ca-certificates**
    Enable the dynamic CA configuration feature: **update-ca-trust force-enable**
    Add it as a new file to /etc/pki/ca-trust/source/anchors/: **cp foo.crt /etc/pki/ca-trust/source/anchors/**
    Use command: **update-ca-trust extract**


old NAS's
- mvix media things
- mvix box
- verbatim drive
- freenas in the old amd
