Plex


#echo '{"pkgs":["plexmediaserver","ca_root_nss"]}' > pkg.json
#iocage create -n "plex01" -p pkg.json -r 11.1-RELEASE allow_sysvipc=1 bpf="yes" vnet="on" allow_raw_sockets="1" boot="on"
#iocage create -n "plex01" -p pkg.json -r 11.1-RELEASE dhcp=on vnet="on" allow_raw_sockets="1" boot="on"
#iocage create -n "plex01" -p pkg.json -r 11.1-RELEASE ip4_addr="em|192.168.0.11/24" defaultrouter="192.168.0.1" vnet="on" allow_raw_sockets="1" boot="on"

#iocage create -n plex01 dhcp=on allow_sysvipc=1 -r11.1-RELEASE boot="on" bpf="yes"
#iocage fstab -a plex /mnt/tank1/video /mnt/video nullfs ro 0 0

iocage create -n "plex01" -p pkg.json -r 11.1-RELEASE ip4_addr="re0|192.168.0.11/24" defaultrouter="192.168.0.1" boot="on"

iocage fstab -a plex01 /mnt/vol01/apps/plex /config nullfs rw 0 0
iocage exec plex01 pkg install -y plexmediaserver ca_root_nss
iocage exec plex01 chown -R plex:plex /config
iocage exec plex01 sysrc "plexmediaserver_enable=YES"
iocage exec plex01 sysrc plexmediaserver_support_path="/config"
iocage exec plex01 service plexmediaserver start



echo '{"pkgs":["python2","py27-sqlite3","py27-openssl","ca_root_nss","git"]}' > pkg.json
#iocage create -n "plexpi" -p pkg.json -r 11.1-RELEASE ip4_addr="re0|192.168.0.102/24" defaultrouter="192.168.0.1" vnet="on" allow_raw_sockets="1" boot="on"
iocage create -n "plexpi" -p pkg.json -r 11.1-RELEASE ip4_addr="re0|192.168.0.102/24" defaultrouter="192.168.0.1" boot="on"

iocage fstab -a tautulli /mnt/vol01/apps/plexpi /config nullfs rw 0 0
iocage exec tautulli git clone https://github.com/Tautulli/Tautulli.git /usr/local/share/Tautulli
iocage exec tautulli "pw user add tautulli -c tautulli -u 109 -d /nonexistent -s /usr/bin/nologin"
iocage exec tautulli chown -R tautulli:tautulli /usr/local/share/Tautulli /config
iocage exec tautulli cp /usr/local/share/Tautulli/init-scripts/init.freenas /usr/local/etc/rc.d/tautulli
iocage exec tautulli chmod u+x /usr/local/etc/rc.d/tautulli
iocage exec tautulli sysrc "tautulli_enable=YES"
iocage exec tautulli sysrc "tautulli_flags=--datadir /config"
iocage exec tautulli service tautulli start
