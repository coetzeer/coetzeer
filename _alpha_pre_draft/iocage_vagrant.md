********************************************************************************
fdescfs(5) is not mounted, performance may suffer. Please run:
mount -t fdescfs null /dev/fd
You can also permanently mount it in /etc/fstab with the following entry:
fdescfs /dev/fd  fdescfs  rw  0  0
********************************************************************************
