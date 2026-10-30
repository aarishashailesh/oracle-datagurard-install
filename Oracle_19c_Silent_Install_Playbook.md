[root@lnx103 u01]# rpm -q oracle-database-preinstall-19c
oracle-database-preinstall-19c-1.0-2.el8.x86_64

[root@lnx103 u01]# grep -E "oinstall|dba|asmadmin|asmbba|asmoper" /etc/group
oinstall:x:54321:
dba:x:54322:oracle,grid
backupdba:x:54324:
dgdba:x:54325:
kmdba:x:54326:
racdba:x:54330:
asmadmin:x:54328:grid,oracle
asmbba:x:54329:grid,oracle
asmoper:x:54331:grid
[root@lnx103 u01]#

[root@lnx103 u01]# id grid
uid=54322(grid) gid=54321(oinstall) groups=54321(oinstall),54322(dba),54328(asmadmin),54329(asmbba),54331(asmoper)

[root@lnx103 u01]# id oracle
uid=54321(oracle) gid=54321(oinstall) groups=54321(oinstall),54322(dba),54328(asmadmin),54329(asmbba)
[root@lnx103 u01]#

[root@lnx103 app]# pwd
/u01/app
[root@lnx103 app]# tree
.
├── 19.0.0
│   └── grid
├── grid
└── oracle
    └── product
        └── 19.0.0
            └── dbhome_1

7 directories, 0 files
[root@lnx103 app]# ls -la
total 0
drwxrwxr-x. 5 grid   oinstall 46 May 19 20:18 .
drwxrwxr-x. 4 grid   oinstall 31 May 19 20:19 ..
drwxrwxr-x. 3 grid   oinstall 18 May 19 20:18 19.0.0
drwxrwxr-x. 2 grid   oinstall  6 May 19 20:18 grid
drwxrwxr-x. 3 oracle oinstall 21 May 19 20:18 oracle
[root@lnx103 app]#

[grid@lnx103 ~]$ cat .bash_profile
# .bash_profile

# Get the aliases and functions
if [ -f ~/.bashrc ]; then
        . ~/.bashrc
fi

# User specific environment and startup programs
# Oracle Grid Infrastructure Environment
export TMP=/tmp
export TMPDIR=$TMP
export ORACLE_BASE=/u01/app/grid
export ORACLE_HOME=/u01/app/19.0.0/grid
export ORACLE_SID=+ASM1   # CRS_CONFIG STANDALONE uses instance number 1
export PATH=$ORACLE_HOME/bin:$PATH
export LD_LIBRARY_PATH=$ORACLE_HOME/lib:/lib:/usr/lib
export CLASSPATH=$ORACLE_HOME/JRE:$ORACLE_HOME/jlib
[grid@lnx103 ~]$

[oracle@lnx103 ~]$ cat .bash_profile
# .bash_profile

# Get the aliases and functions
if [ -f ~/.bashrc ]; then
        . ~/.bashrc
fi

# User specific environment and startup programs
# Oracle Database Environment
export TMP=/tmp
export TMPDIR=$TMP
export ORACLE_BASE=/u01/app/oracle
export ORACLE_HOME=/u01/app/oracle/product/19.0.0/dbhome_1
export ORACLE_SID=proddb
export PATH=$ORACLE_HOME/bin:$PATH
export LD_LIBRARY_PATH=$ORACLE_HOME/lib:/lib:/usr/lib
export CLASSPATH=$ORACLE_HOME/JRE:$ORACLE_HOME/jlib:$ORACLE_HOME/rdbms/jlib
[oracle@lnx103 ~]$ ls -al .bash_profile
-rw-r--r--. 1 oracle oinstall 490 May 19 20:18 .bash_profile
[oracle@lnx103 ~]$


root@ns1:/etc/bind# tail -4 db-home-aarisha-com.zone
lnx103                  IN      A       192.168.1.103
lnx103-vip              IN      A       192.168.1.104
lnx103-scan             IN      A       192.168.1.105
lnx103-priv             IN      A       10.10.10.103
root@ns1:/etc/bind#

root@ns1:/etc/bind# tail -3 db-192-168-1
103     IN      PTR     lnx103.home.aarisha.com.
104     IN      PTR     lnx103-vip.home.aarisha.com.
105     IN      PTR     lnx103-scan.home.aarisha.com.
root@ns1:/etc/bind#

root@ns1:/etc/bind# tail -1 db-10-10-10
103     IN      PTR     lnx103-priv.home.aarisha.com.
root@ns1:/etc/bind#

[root@lnx103 app]# cat /etc/resolv.conf
search home.aarisha.com
nameserver 192.168.1.111
[root@lnx103 app]#

[root@lnx103 app]# cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6

# Node public IP — must match ORACLE_HOSTNAME in response file
192.168.1.103   lnx103.home.aarisha.com   lnx103

# Private interconnect
10.10.10.103    lnx103-priv.home.aarisha.com   lnx103-priv

[root@lnx103 app]#


nmcli con add type ethernet con-name ens19 ifname ens19 ipv4.addresses 10.10.10.103/24 ipv4.method manual
nmcli con up ens19
ip addr show ens19

[root@lnx103 ~]# nmcli con add type ethernet con-name ens19 ifname ens19 ipv4.addresses 10.10.10.103/24 ipv4.method manual
Connection 'ens19' (5fa182c0-4951-44ed-ac9f-0ce4b1a987e8) successfully added.
[root@lnx103 ~]# nmcli con add type ethernet con-name ens19 ifname ens19 ipv4.addresses 10.10.10.103/24 ipv4.method manual
Warning: There is another connection with the name 'ens19'. Reference the connection by its uuid 'f6eec5e6-a907-4b66-a9ba-9b58102a25ff'
Connection 'ens19' (f6eec5e6-a907-4b66-a9ba-9b58102a25ff) successfully added.
[root@lnx103 ~]# nmcli con up ens19
Connection successfully activated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/4)
[root@lnx103 ~]# ip addr show ens19
3: ens19: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether bc:24:11:3f:c3:50 brd ff:ff:ff:ff:ff:ff
    altname enp0s19
    inet 10.10.10.103/24 brd 10.10.10.255 scope global noprefixroute ens19
       valid_lft forever preferred_lft forever
    inet6 fe80::ae91:92c:94f5:6754/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
[root@lnx103 ~]#

host lnx103.home.aarisha.com
host lnx103-priv.home.aarisha.com
host lnx103-vip.home.aarisha.com
host lnx103-scan.home.aarisha.com
 
host 192.168.1.103
host 192.168.1.104
host 192.168.1.105
host 10.10.10.103
 
ping -c 2 192.168.1.104 || echo "VIP IP is free — OK"
ping -c 2 192.168.1.105 || echo "SCAN IP is free — OK"
 
ping -c 2 10.10.10.103

[root@lnx103 ~]# host lnx103.home.aarisha.com
lnx103.home.aarisha.com has address 192.168.1.103
[root@lnx103 ~]# host lnx103-priv.home.aarisha.com
lnx103-priv.home.aarisha.com has address 10.10.10.103
[root@lnx103 ~]# host lnx103-vip.home.aarisha.com
lnx103-vip.home.aarisha.com has address 192.168.1.104
[root@lnx103 ~]# host lnx103-scan.home.aarisha.com
lnx103-scan.home.aarisha.com has address 192.168.1.105
[root@lnx103 ~]#

[root@lnx103 ~]# host 192.168.1.103
103.1.168.192.in-addr.arpa domain name pointer lnx103.home.aarisha.com.
[root@lnx103 ~]# host 192.168.1.104
104.1.168.192.in-addr.arpa domain name pointer lnx103-vip.home.aarisha.com.
[root@lnx103 ~]# host 192.168.1.105
105.1.168.192.in-addr.arpa domain name pointer lnx103-scan.home.aarisha.com.
[root@lnx103 ~]# host 10.10.10.103
103.10.10.10.in-addr.arpa domain name pointer lnx103-priv.home.aarisha.com.
[root@lnx103 ~]#

[root@lnx103 ~]# ping -c 2 192.168.1.104 || echo "VIP IP is free — OK"
PING 192.168.1.104 (192.168.1.104) 56(84) bytes of data.
From 192.168.1.103 icmp_seq=1 Destination Host Unreachable
From 192.168.1.103 icmp_seq=2 Destination Host Unreachable

--- 192.168.1.104 ping statistics ---
2 packets transmitted, 0 received, +2 errors, 100% packet loss, time 1022ms
pipe 2
VIP IP is free — OK
[root@lnx103 ~]# ping -c 2 192.168.1.105 || echo "SCAN IP is free — OK"
PING 192.168.1.105 (192.168.1.105) 56(84) bytes of data.
From 192.168.1.103 icmp_seq=1 Destination Host Unreachable
From 192.168.1.103 icmp_seq=2 Destination Host Unreachable

--- 192.168.1.105 ping statistics ---
2 packets transmitted, 0 received, +2 errors, 100% packet loss, time 1022ms
pipe 2
SCAN IP is free — OK

[root@lnx103 ~]# ping -c 2 10.10.10.103
PING 10.10.10.103 (10.10.10.103) 56(84) bytes of data.
64 bytes from 10.10.10.103: icmp_seq=1 ttl=64 time=0.029 ms
64 bytes from 10.10.10.103: icmp_seq=2 ttl=64 time=0.047 ms

--- 10.10.10.103 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1047ms
rtt min/avg/max/mdev = 0.029/0.038/0.047/0.009 ms
[root@lnx103 ~]#

[root@lnx103 ~]# su - grid
[grid@lnx103 ~]$ cd /u01/app/19.0.0/grid
[grid@lnx103 grid]$ unzip -q /u01/shared/software/db/GRID19_V982068-01.zip
[grid@lnx103 grid]$ ls -la /u01/app/19.0.0/grid/gridSetup.sh
-rwxr-x--- 1 grid oinstall 3294 Mar  8  2017 /u01/app/19.0.0/grid/gridSetup.sh
[grid@lnx103 grid]$ ls -la /u01/app/19.0.0/grid/bin/asmcmd
-rwxr-x--- 1 grid oinstall 6966 Jan 30  2018 /u01/app/19.0.0/grid/bin/asmcmd
[grid@lnx103 grid]$ ls -la /u01/app/19.0.0/grid/bin/afddriverstate
-rwxr-xr-x 1 grid oinstall 3078 Mar 28  2017 /u01/app/19.0.0/grid/bin/afddriverstate
[grid@lnx103 grid]$


export GRID_HOME=/u01/app/19.0.0/grid
$GRID_HOME/OPatch/opatch version
mv $GRID_HOME/OPatch $GRID_HOME/OPatch_19.3_orig
unzip -q /u01/shared/software/patches/p6880880_190000_Linux-x86-64.zip -d $GRID_HOME
chown -R grid:oinstall $GRID_HOME/OPatch
chown -R grid:oinstall $GRID_HOME/OPatch
$GRID_HOME/OPatch/opatch version
 
[root@lnx103 ~]# export GRID_HOME=/u01/app/19.0.0/grid
[root@lnx103 ~]# $GRID_HOME/OPatch/opatch version
mv $GRID_HOME/OPatch $GRID_HOME/OPatch_19.3_orig
unzip -q /u01/shared/software/patches/p6880880_190000_Linux-x86-64.zip -d $GRID_HOME
chown -R grid:oinstall $GRID_HOME/OPatch
chown -R grid:oinstall $GRID_HOME/OPatch
$GRID_HOME/OPatch/opatch version
OPatch Version: 12.2.0.1.17

OPatch succeeded.
[root@lnx103 ~]# mv $GRID_HOME/OPatch $GRID_HOME/OPatch_19.3_orig
[root@lnx103 ~]# unzip -q /u01/shared/software/patches/p6880880_190000_Linux-x86-64.zip -d $GRID_HOME
[root@lnx103 ~]# chown -R grid:oinstall $GRID_HOME/OPatch
[root@lnx103 ~]# chown -R grid:oinstall $GRID_HOME/OPatch
[root@lnx103 ~]# $GRID_HOME/OPatch/opatch version
OPatch Version: 12.2.0.1.51

su - oracle
 
# Extract DB home ZIP directly into the DB ORACLE_HOME
cd /u01/app/oracle/product/19.0.0/dbhome_1
unzip -q /u01/shared/software/db/DB19_V982063-01.zip
 
# Verify runInstaller is present
ls -la /u01/app/oracle/product/19.0.0/dbhome_1/runInstaller
 
exit

[root@lnx103 ~]# su - oracle
[oracle@lnx103 ~]$ cd /u01/app/oracle/product/19.0.0/dbhome_1
[oracle@lnx103 dbhome_1]$ unzip -q /u01/shared/software/db/DB19_V982063-01.zip
[oracle@lnx103 dbhome_1]$ ls -la /u01/app/oracle/product/19.0.0/dbhome_1/runInstaller
-rwxr-x--- 1 oracle oinstall 1783 Mar  8  2017 /u01/app/oracle/product/19.0.0/dbhome_1/runInstaller
[oracle@lnx103 dbhome_1]$

root@lnx173:~# grep scsi /etc/pve/nodes/lnx173/qemu-server/129.conf
boot: order=scsi0;ide2;net0
scsi0: local2:129/vm-129-disk-0.qcow2,iothread=1,size=160G
scsi1: local2:129/vm-129-disk-1.qcow2,iothread=1,size=60G
scsi2: local2:129/vm-129-disk-2.qcow2,iothread=1,size=80G
scsihw: virtio-scsi-single
boot: order=scsi0;ide2;net0
scsi0: local2:129/vm-129-disk-0.qcow2,iothread=1,size=160G
scsihw: virtio-scsi-single

root@lnx173:~# VMID=129
qm set $VMID --scsi0 local2:129/vm-129-disk-0.qcow2,iothread=1,size=160G,serial=OSDISK01
qm set $VMID --scsi1 local2:129/vm-129-disk-1.qcow2,iothread=1,size=60G,serial=ASMDATA01
qm set $VMID --scsi2 local2:129/vm-129-disk-2.qcow2,iothread=1,size=80G,serial=ASMFRA01
update VM 129: -scsi0 local2:129/vm-129-disk-0.qcow2,iothread=1,size=160G,serial=OSDISK01
update VM 129: -scsi1 local2:129/vm-129-disk-1.qcow2,iothread=1,size=60G,serial=ASMDATA01
update VM 129: -scsi2 local2:129/vm-129-disk-2.qcow2,iothread=1,size=80G,serial=ASMFRA01

root@lnx173:~# grep scsi /etc/pve/nodes/lnx173/qemu-server/129.conf
boot: order=scsi0;ide2;net0
scsi0: local2:129/vm-129-disk-0.qcow2,iothread=1,serial=OSDISK01,size=160G
scsi1: local2:129/vm-129-disk-1.qcow2,iothread=1,serial=ASMDATA01,size=60G
scsi2: local2:129/vm-129-disk-2.qcow2,iothread=1,serial=ASMFRA01,size=80G
scsihw: virtio-scsi-single
boot: order=scsi0;ide2;net0
scsi0: local2:129/vm-129-disk-0.qcow2,iothread=1,size=160G
scsihw: virtio-scsi-single

root@lnx173:~# qm reboot $VMID
root@lnx173:~#

[root@lnx103 ~]# ls -la /dev/disk/by-id/ | grep -v part | grep QEMU
lrwxrwxrwx 1 root root   9 May 24 22:59 ata-QEMU_DVD-ROM_QM00003 -> ../../sr0
lrwxrwxrwx 1 root root   9 May 24 22:59 scsi-0QEMU_QEMU_HARDDISK_drive-scsi0 -> ../../sda
lrwxrwxrwx 1 root root   9 May 24 22:59 scsi-0QEMU_QEMU_HARDDISK_drive-scsi1 -> ../../sdb
lrwxrwxrwx 1 root root   9 May 24 22:59 scsi-0QEMU_QEMU_HARDDISK_drive-scsi2 -> ../../sdc
lrwxrwxrwx 1 root root   9 May 24 22:59 scsi-SQEMU_QEMU_HARDDISK_ASMDATA01 -> ../../sdb
lrwxrwxrwx 1 root root   9 May 24 22:59 scsi-SQEMU_QEMU_HARDDISK_ASMFRA01 -> ../../sdc
lrwxrwxrwx 1 root root   9 May 24 22:59 scsi-SQEMU_QEMU_HARDDISK_OSDISK01 -> ../../sda
[root@lnx103 ~]#

[root@lnx103 ~]# lsblk /dev/disk/by-id/scsi-0QEMU_QEMU_HARDDISK_drive-scsi1
NAME MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
sdb    8:16   0  60G  0 disk
[root@lnx103 ~]#

[root@lnx103 ~]# lsblk /dev/disk/by-id/scsi-0QEMU_QEMU_HARDDISK_drive-scsi2
NAME MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
sdc    8:32   0  80G  0 disk
[root@lnx103 ~]#

[root@lnx103 ~]# cat /etc/udev/rules.d/99-oracle-asm.rules
# Oracle ASM DATA disk (60G) — Proxmox scsi1, ID_SERIAL confirmed: 0QEMU_QEMU_HARDDISK_drive-scsi1
# Single-line rule — backslash continuations are unreliable in udev rules
KERNEL=="sd*", ENV{ID_SERIAL}=="0QEMU_QEMU_HARDDISK_drive-scsi1", OWNER="grid", GROUP="asmadmin", MODE="0660", SYMLINK+="oracleasm/DATA"

# Oracle ASM FRA disk (80G) — Proxmox scsi2, ID_SERIAL confirmed: 0QEMU_QEMU_HARDDISK_drive-scsi2
KERNEL=="sd*", ENV{ID_SERIAL}=="0QEMU_QEMU_HARDDISK_drive-scsi2", OWNER="grid", GROUP="asmadmin", MODE="0660", SYMLINK+="oracleasm/FRA"
[root@lnx103 ~]#
[root@lnx103 ~]# udevadm control --reload-rules
[root@lnx103 ~]# udevadm trigger --action=add --subsystem-match=block
[root@lnx103 ~]#
[root@lnx103 ~]# ls -la /dev/sdb /dev/sdc
brw-rw---- 1 grid asmadmin 8, 16 May 24 23:06 /dev/sdb
brw-rw---- 1 grid asmadmin 8, 32 May 24 23:06 /dev/sdc
[root@lnx103 ~]#
[root@lnx103 ~]# ls -la /dev/oracleasm/
total 0
drwxr-xr-x  2 root root   80 May 24 23:06 .
drwxr-xr-x 20 root root 3400 May 24 23:04 ..
lrwxrwxrwx  1 root root    6 May 24 23:06 DATA -> ../sdb
lrwxrwxrwx  1 root root    6 May 24 23:06 FRA -> ../sdc
[root@lnx103 ~]#
[root@lnx103 ~]# su - grid -c "dd if=/dev/oracleasm/DATA of=/dev/null bs=512 count=1 2>&1"
1+0 records in
1+0 records out
512 bytes copied, 0.000183836 s, 2.8 MB/s
[root@lnx103 ~]#
[root@lnx103 ~]# su - grid -c "dd if=/dev/oracleasm/FRA  of=/dev/null bs=512 count=1 2>&1"
1+0 records in
1+0 records out
512 bytes copied, 0.000166965 s, 3.1 MB/s
[root@lnx103 ~]#

[root@lnx103 ~]# ls -la /dev/sdb /dev/sdc
brw-rw---- 1 grid asmadmin 8, 16 May 24 23:09 /dev/sdb
brw-rw---- 1 grid asmadmin 8, 32 May 24 23:09 /dev/sdc
[root@lnx103 ~]#
[root@lnx103 ~]# ls -la /dev/oracleasm/
total 0
drwxr-xr-x  2 root root   80 May 24 23:09 .
drwxr-xr-x 20 root root 3400 May 24 23:09 ..
lrwxrwxrwx  1 root root    6 May 24 23:09 DATA -> ../sdb
lrwxrwxrwx  1 root root    6 May 24 23:09 FRA -> ../sdc
[root@lnx103 ~]#


[root@lnx103 ~]# su - grid -c "
>   export CV_ASSUME_DISTID=OEL8 &&
>   cd /u01/app/19.0.0/grid &&
>   ./gridSetup.sh \
>     -silent \
>     -waitForCompletion \
>     -responseFile /u01/shared/software/response/grid_install.rsp \
>     -ignorePrereqFailure"
Launching Oracle Grid Infrastructure Setup Wizard...

[WARNING] [INS-13014] Target environment does not meet some optional requirements.
   CAUSE: Some of the optional prerequisites are not met. See logs for details. gridSetupActions2026-05-25_08-37-00AM.log
   ACTION: Identify the list of failed prerequisite checks from the log: gridSetupActions2026-05-25_08-37-00AM.log. Then either from the log file or from installation manual find the appropriate configuration to meet the prerequisites and fix it manually.
The response file for this session can be found at:
 /u01/app/19.0.0/grid/install/response/grid_2026-05-25_08-37-00AM.rsp

You can find the log of this install session at:
 /tmp/GridSetupActions2026-05-25_08-37-00AM/gridSetupActions2026-05-25_08-37-00AM.log

As a root user, execute the following script(s):
        1. /u01/app/oraInventory/orainstRoot.sh
        2. /u01/app/19.0.0/grid/root.sh

Execute /u01/app/oraInventory/orainstRoot.sh on the following nodes:
[lnx103]
Execute /u01/app/19.0.0/grid/root.sh on the following nodes:
[lnx103]



Successfully Setup Software with warning(s).
As install user, execute the following command to complete the configuration.
        /u01/app/19.0.0/grid/gridSetup.sh -executeConfigTools -responseFile /u01/shared/software/response/grid_install.rsp [-silent]


Moved the install session logs to:
 /u01/app/oraInventory/logs/GridSetupActions2026-05-25_08-37-00AM
[root@lnx103 ~]#

[root@lnx103 ~]# /u01/app/oraInventory/orainstRoot.sh
Changing permissions of /u01/app/oraInventory.
Adding read,write permissions for group.
Removing read,write,execute permissions for world.

Changing groupname of /u01/app/oraInventory to oinstall.
The execution of the script is complete.
[root@lnx103 ~]# /u01/app/19.0.0/grid/root.sh
Check /u01/app/19.0.0/grid/install/root_lnx103.home.aarisha.com_2026-05-25_09-09-04-023656621.log for the output of root script
[root@lnx103 ~]#

[root@lnx103 ~]# su - grid -c "crsctl check crs"
CRS-4638: Oracle High Availability Services is online
CRS-4537: Cluster Ready Services is online
CRS-4529: Cluster Synchronization Services is online
CRS-4533: Event Manager is online
[root@lnx103 ~]#

# Start DB Installation

[root@lnx103 ~]# cat /u01/shared/software/response/db_install.rsp
###############################################################################
## Oracle Database 19c — Software-Only Silent Install Response File          ##
###############################################################################
oracle.install.responseFileVersion=/oracle/install/rspfmt_dbinstall_response_schema_v19.0.0
oracle.install.option=INSTALL_DB_SWONLY
ORACLE_HOSTNAME=lnx103.home.aarisha.com
UNIX_GROUP_NAME=oinstall
INVENTORY_LOCATION=/u01/app/oraInventory
SELECTED_LANGUAGES=en
ORACLE_HOME=/u01/app/oracle/product/19.0.0/dbhome_1
ORACLE_BASE=/u01/app/oracle
oracle.install.db.InstallEdition=EE
oracle.install.db.OSDBA_GROUP=dba
oracle.install.db.OSOPER_GROUP=dba
oracle.install.db.OSBACKUPDBA_GROUP=dba
oracle.install.db.OSDGDBA_GROUP=dba
oracle.install.db.OSKMDBA_GROUP=dba
oracle.install.db.OSRACDBA_GROUP=dba
oracle.installer.autoupdates.option=SKIP_UPDATES
[root@lnx103 ~]#

[root@lnx103 ~]# su - oracle -c " export CV_ASSUME_DISTID=OEL8 &&
>  cd /u01/app/oracle/product/19.0.0/dbhome_1 && \
>   ./runInstaller \
>   -silent \
>   -waitForCompletion \
>   -responseFile /u01/shared/software/response/db_install.rsp \
>   -ignorePrereqFailure"
Launching Oracle Database Setup Wizard...

The response file for this session can be found at:
 /u01/app/oracle/product/19.0.0/dbhome_1/install/response/db_2026-05-25_09-36-33AM.rsp

You can find the log of this install session at:
 /u01/app/oraInventory/logs/InstallActions2026-05-25_09-36-33AM/installActions2026-05-25_09-36-33AM.log

As a root user, execute the following script(s):
        1. /u01/app/oracle/product/19.0.0/dbhome_1/root.sh

Execute /u01/app/oracle/product/19.0.0/dbhome_1/root.sh on the following nodes:
[lnx103]


Successfully Setup Software.
[root@lnx103 ~]#

[root@lnx103 ~]# /u01/app/oracle/product/19.0.0/dbhome_1/root.sh
Check /u01/app/oracle/product/19.0.0/dbhome_1/install/root_lnx103.home.aarisha.com_2026-05-25_09-42-16-657164864.log for the output of root script
[root@lnx103 ~]# more /u01/app/oracle/product/19.0.0/dbhome_1/install/root_lnx103.home.aarisha.com_2026-05-25_09-42-16-657164864.log
Performing root user operation.

The following environment variables are set as:
    ORACLE_OWNER= oracle
    ORACLE_HOME=  /u01/app/oracle/product/19.0.0/dbhome_1
   Copying dbhome to /usr/local/bin ...
   Copying oraenv to /usr/local/bin ...
   Copying coraenv to /usr/local/bin ...

Entries will be added to the /etc/oratab file as needed by
Database Configuration Assistant when a database is created
Finished running generic part of root script.
Now product-specific root actions will be performed.
Oracle Trace File Analyzer (TFA) is available at : /u01/app/oracle/product/19.0.0/dbhome_1/bin/tfactl
[root@lnx103 ~]#

[root@lnx103 ~]# su - grid -c "
>   export ORACLE_HOME=/u01/app/19.0.0/grid
>   export PATH=\$ORACLE_HOME/bin:\$PATH
>   sqlplus / as sysasm << EOF
>   CREATE DISKGROUP FRA
>     EXTERNAL REDUNDANCY
>     DISK '/dev/oracleasm/FRA'
>     ATTRIBUTE 'compatible.asm' = '19.0';
>   EXIT;
> EOF"

SQL*Plus: Release 19.0.0.0.0 - Production on Mon May 25 09:59:54 2026
Version 19.3.0.0.0

Copyright (c) 1982, 2019, Oracle.  All rights reserved.


Connected to:
Oracle Database 19c Enterprise Edition Release 19.0.0.0.0 - Production
Version 19.3.0.0.0

SQL>   2    3    4
Diskgroup created.

SQL> Disconnected from Oracle Database 19c Enterprise Edition Release 19.0.0.0.0 - Production
Version 19.3.0.0.0
[root@lnx103 ~]# su - grid -c "asmcmd lsdg"
State    Type    Rebal  Sector  Logical_Sector  Block       AU  Total_MB  Free_MB  Req_mir_free_MB  Usable_file_MB  Offline_disks  Voting_files  Name
MOUNTED  EXTERN  N         512             512   4096  4194304     61440    61148                0           61148              0             Y  DATA/
MOUNTED  EXTERN  N         512             512   4096  1048576     81920    81866                0           81866              0             N  FRA/
[root@lnx103 ~]#

[oracle@lnx103 ~]$   dbca -silent -createDatabase \
>     -gdbName                     proddb.home.aarisha.com \
>     -sid                         proddb \
>     -createAsContainerDatabase   true \
>     -numberOfPdbs                2 \
>     -pdbName                     pdb \
>     -pdbAdminPassword            Ishan123# \
>     -templateName                General_Purpose.dbc \
>     -storageType                 ASM \
>     -diskGroupName               +DATA \
>     -datafileDestination         +DATA \
>     -recoveryAreaDestination     +FRA \
>     -recoveryAreaSize            81000 \
>     -enableArchive               true \
>     -characterset                AL32UTF8 \
>     -nationalCharacterSet        AL16UTF16 \
>     -sysPassword                 Ishan123# \
>     -systemPassword              Ishan123# \
>     -emConfiguration             NONE \
>     -nodelist                    lnx103.home.aarisha.com \
>     -responseFile                NO_VALUE
Prepare for db operation
7% complete
Registering database with Oracle Restart
11% complete
Copying database files
33% complete
Creating and starting Oracle instance
35% complete
38% complete
42% complete
45% complete
48% complete
Completing Database Creation
53% complete
55% complete
56% complete
Creating Pluggable Databases
60% complete
64% complete
78% complete
Executing Post Configuration Actions
100% complete
Database creation complete. For details check the logfiles at:
 /u01/app/oracle/cfgtoollogs/dbca/proddb.
Database Information:
Global Database Name:proddb.home.aarisha.com
System Identifier(SID):proddb
Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/proddb/proddb.log" for further details.
[oracle@lnx103 ~]$

[oracle@lnx103 ~]$ echo $ORACLE_SID
proddb
[oracle@lnx103 ~]$ sqlplus / as sysdba

SQL*Plus: Release 19.0.0.0.0 - Production on Mon May 25 11:15:49 2026
Version 19.3.0.0.0

Copyright (c) 1982, 2019, Oracle.  All rights reserved.


Connected to:
Oracle Database 19c Enterprise Edition Release 19.0.0.0.0 - Production
Version 19.3.0.0.0

SQL> SELECT name, cdb, con_id FROM v$database;

NAME      CDB     CON_ID
--------- --- ----------
PRODDB    YES          0

SQL> SELECT con_id, name, open_mode FROM v$pdbs ORDER BY con_id;

    CON_ID    NAME        OPEN_MODE
-----------------------------------------------------------------------
         2    PDB$SEED    READ ONLY

         3    PDB1        READ WRITE

         4    PDB2        READ WRITE


SQL> SHOW PARAMETER db_recovery_file_dest

NAME                                 TYPE        VALUE
------------------------------------ ----------- -----------------------
db_recovery_file_dest                string      +FRA
db_recovery_file_dest_size           big integer 81000M
SQL> SELECT log_mode, flashback_on FROM v$database;

LOG_MODE     FLASHBACK_ON
------------ ------------------
ARCHIVELOG   NO

SQL>

[root@lnx103 ~]# su - oracle -c "sqlplus / as sysdba << EOF
> ALTER PLUGGABLE DATABASE ALL OPEN;
> ALTER PLUGGABLE DATABASE ALL SAVE STATE;
>
> -- Verify save state was recorded
> SELECT con_name, state FROM dba_pdb_saved_states;
> EXIT;
> EOF"

SQL*Plus: Release 19.0.0.0.0 - Production on Mon May 25 11:20:35 2026
Version 19.3.0.0.0

Copyright (c) 1982, 2019, Oracle.  All rights reserved.


Connected to:
Oracle Database 19c Enterprise Edition Release 19.0.0.0.0 - Production
Version 19.3.0.0.0

SQL>
Pluggable database altered.

SQL>
Pluggable database altered.

SQL> SQL> SQL>
CON_NAME        STATE
--------------------------------------------------------------------------------
PDB2            OPEN
PDB1            OPEN

SQL> Disconnected from Oracle Database 19c Enterprise Edition Release 19.0.0.0.0 - Production
Version 19.3.0.0.0
[root@lnx103 ~]#

[grid@lnx103 ~]$ crsctl check crs
CRS-4638: Oracle High Availability Services is online
CRS-4537: Cluster Ready Services is online
CRS-4529: Cluster Synchronization Services is online
CRS-4533: Event Manager is online
[grid@lnx103 ~]$ crsctl status resource -t
--------------------------------------------------------------------------------
Name           Target  State        Server                   State details
--------------------------------------------------------------------------------
Local Resources
--------------------------------------------------------------------------------
ora.LISTENER.lsnr
               ONLINE  ONLINE       lnx103                   STABLE
ora.chad
               ONLINE  ONLINE       lnx103                   STABLE
ora.net1.network
               ONLINE  ONLINE       lnx103                   STABLE
ora.ons
               ONLINE  ONLINE       lnx103                   STABLE
--------------------------------------------------------------------------------
Cluster Resources
--------------------------------------------------------------------------------
ora.ASMNET1LSNR_ASM.lsnr(ora.asmgroup)
      1        ONLINE  ONLINE       lnx103                   STABLE
      2        OFFLINE OFFLINE                               STABLE
      3        OFFLINE OFFLINE                               STABLE
ora.DATA.dg(ora.asmgroup)
      1        ONLINE  ONLINE       lnx103                   STABLE
      2        OFFLINE OFFLINE                               STABLE
      3        OFFLINE OFFLINE                               STABLE
ora.FRA.dg(ora.asmgroup)
      1        ONLINE  ONLINE       lnx103                   STABLE
      2        OFFLINE OFFLINE                               STABLE
      3        OFFLINE OFFLINE                               STABLE
ora.LISTENER_SCAN1.lsnr
      1        ONLINE  ONLINE       lnx103                   STABLE
ora.asm(ora.asmgroup)
      1        ONLINE  ONLINE       lnx103                   Started,STABLE
      2        OFFLINE OFFLINE                               STABLE
      3        OFFLINE OFFLINE                               STABLE
ora.asmnet1.asmnetwork(ora.asmgroup)
      1        ONLINE  ONLINE       lnx103                   STABLE
      2        OFFLINE OFFLINE                               STABLE
      3        OFFLINE OFFLINE                               STABLE
ora.cvu
      1        ONLINE  ONLINE       lnx103                   STABLE
ora.lnx103.vip
      1        ONLINE  ONLINE       lnx103                   STABLE
ora.proddb.db
      1        ONLINE  ONLINE       lnx103                   Open,HOME=/u01/app/o
                                                             racle/product/19.0.0
                                                             /dbhome_1,STABLE
ora.qosmserver
      1        ONLINE  ONLINE       lnx103                   STABLE
ora.scan1.vip
      1        ONLINE  ONLINE       lnx103                   STABLE
--------------------------------------------------------------------------------

[grid@lnx103 ~]$ asmcmd lsdg
State    Type    Rebal  Sector  Logical_Sector  Block       AU  Total_MB  Free_MB  Req_mir_free_MB  Usable_file_MB  Offline_disks  Voting_files  Name
MOUNTED  EXTERN  N         512             512   4096  4194304     61440    56460                0           56460              0             Y  DATA/
MOUNTED  EXTERN  N         512             512   4096  1048576     81920    81211                0           81211              0             N  FRA/
[grid@lnx103 ~]$

[grid@lnx103 ~]$ asmcmd lsdsk -t
Create_Date  Mount_Date  Repair_Timer  Path
25-MAY-26    25-MAY-26   0             /dev/oracleasm/DATA
25-MAY-26    25-MAY-26   0             /dev/oracleasm/FRA
[grid@lnx103 ~]$

[oracle@lnx103 proddb]$ sqlplus / as sysdba

SQL*Plus: Release 19.0.0.0.0 - Production on Mon May 25 11:47:44 2026
Version 19.3.0.0.0

Copyright (c) 1982, 2019, Oracle.  All rights reserved.


Connected to:
Oracle Database 19c Enterprise Edition Release 19.0.0.0.0 - Production
Version 19.3.0.0.0

SQL> SELECT instance_name, status, database_status FROM v$instance;

INSTANCE_NAME    STATUS       DATABASE_STATUS
---------------- ------------ -----------------
proddb           OPEN         ACTIVE

SQL> SELECT name, db_unique_name, open_mode FROM v$database;

NAME      DB_UNIQUE_NAME                 OPEN_MODE
--------- ------------------------------ --------------------
PRODDB    proddb                         READ WRITE

SQL> SELECT name, state, type FROM v$asm_diskgroup;

NAME                           STATE       TYPE
------------------------------ ----------- ------
DATA                           CONNECTED   EXTERN
FRA                            CONNECTED   EXTERN

SQL>

[root@lnx103 ~]# su - oracle -c "lsnrctl status"

LSNRCTL for Linux: Version 19.0.0.0.0 - Production on 25-MAY-2026 11:50:13

Copyright (c) 1991, 2019, Oracle.  All rights reserved.

Connecting to (ADDRESS=(PROTOCOL=tcp)(HOST=)(PORT=1521))
STATUS of the LISTENER
------------------------
Alias                     LISTENER
Version                   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
Start Date                25-MAY-2026 09:26:15
Uptime                    0 days 2 hr. 24 min. 11 sec
Trace Level               off
Security                  ON: Local OS Authentication
SNMP                      OFF
Listener Parameter File   /u01/app/19.0.0/grid/network/admin/listener.ora
Listener Log File         /u01/app/grid/diag/tnslsnr/lnx103/listener/alert/log.xml
Listening Endpoints Summary...
  (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=LISTENER)))
  (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=192.168.1.103)(PORT=1521)))
  (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=192.168.1.104)(PORT=1521)))
Services Summary...
Service "+ASM" has 1 instance(s).
  Instance "+ASM1", status READY, has 1 handler(s) for this service...
Service "+ASM_DATA" has 1 instance(s).
  Instance "+ASM1", status READY, has 1 handler(s) for this service...
Service "+ASM_FRA" has 1 instance(s).
  Instance "+ASM1", status READY, has 1 handler(s) for this service...
Service "52a6c5eba4967c06e0636701a8c003e8.home.aarisha.com" has 1 instance(s).
  Instance "proddb", status READY, has 1 handler(s) for this service...
Service "52a6cb329aaa7f1ce0636701a8c01628.home.aarisha.com" has 1 instance(s).
  Instance "proddb", status READY, has 1 handler(s) for this service...
Service "86b637b62fdf7a65e053f706e80a27ca.home.aarisha.com" has 1 instance(s).
  Instance "proddb", status READY, has 1 handler(s) for this service...
Service "pdb1.home.aarisha.com" has 1 instance(s).
  Instance "proddb", status READY, has 1 handler(s) for this service...
Service "pdb2.home.aarisha.com" has 1 instance(s).
  Instance "proddb", status READY, has 1 handler(s) for this service...
Service "proddb.home.aarisha.com" has 1 instance(s).
  Instance "proddb", status READY, has 1 handler(s) for this service...
Service "proddbXDB.home.aarisha.com" has 1 instance(s).
  Instance "proddb", status READY, has 1 handler(s) for this service...
The command completed successfully
[root@lnx103 ~]# su - oracle -c "tnsping proddb"

TNS Ping Utility for Linux: Version 19.0.0.0.0 - Production on 25-MAY-2026 11:50:25

Copyright (c) 1997, 2019, Oracle.  All rights reserved.

Used parameter files:


Used TNSNAMES adapter to resolve the alias
Attempting to contact (DESCRIPTION = (ADDRESS = (PROTOCOL = TCP)(HOST = lnx103)(PORT = 1521)) (CONNECT_DATA = (SERVER = DEDICATED) (SERVICE_NAME = proddb.home.aarisha.com)))
OK (0 msec)
[root@lnx103 ~]#

[oracle@lnx103 proddb]$ ls -l $ORACLE_HOME/network/admin
total 8
drwxr-xr-x 2 oracle oinstall   64 Apr 17  2019 samples
-rw-r--r-- 1 oracle oinstall 1536 Feb 13  2018 shrept.lst
-rw-r----- 1 oracle oinstall  425 May 25 10:44 tnsnames.ora
[oracle@lnx103 proddb]$ cat $ORACLE_HOME/network/admin/tnsnames.ora
# tnsnames.ora Network Configuration File: /u01/app/oracle/product/19.0.0/dbhome_1/network/admin/tnsnames.ora
# Generated by Oracle configuration tools.

PRODDB =
  (DESCRIPTION =
    (ADDRESS = (PROTOCOL = TCP)(HOST = lnx103)(PORT = 1521))
    (CONNECT_DATA =
      (SERVER = DEDICATED)
      (SERVICE_NAME = proddb.home.aarisha.com)
    )
  )

LISTENER_PRODDB =
  (ADDRESS = (PROTOCOL = TCP)(HOST = lnx103)(PORT = 1521))


[oracle@lnx103 proddb]$

[root@lnx103 ~]# ls -l /u01/app/19.0.0/grid/network/admin/
total 12
-rw-r--r-- 1 grid oinstall  826 May 25 09:26 listener.ora
-rw-r--r-- 1 grid oinstall  588 May 25 09:24 listener.ora.bak.lnx103.grid
drwxr-xr-x 2 grid oinstall   64 Apr 17  2019 samples
-rw-r--r-- 1 grid oinstall 1536 Feb 13  2018 shrept.lst

[root@lnx103 ~]# cat /u01/app/19.0.0/grid/network/admin/listener.ora
LISTENER=(DESCRIPTION=(ADDRESS_LIST=(ADDRESS=(PROTOCOL=IPC)(KEY=LISTENER))))            # line added by Agent
LISTENER_SCAN1=(DESCRIPTION=(ADDRESS_LIST=(ADDRESS=(PROTOCOL=IPC)(KEY=LISTENER_SCAN1))))                # line added by Agent
ASMNET1LSNR_ASM=(DESCRIPTION=(ADDRESS_LIST=(ADDRESS=(PROTOCOL=IPC)(KEY=ASMNET1LSNR_ASM))))              # line added by Agent
ENABLE_GLOBAL_DYNAMIC_ENDPOINT_ASMNET1LSNR_ASM=ON               # line added by Agent
VALID_NODE_CHECKING_REGISTRATION_ASMNET1LSNR_ASM=SUBNET         # line added by Agent
ENABLE_GLOBAL_DYNAMIC_ENDPOINT_LISTENER_SCAN1=ON                # line added by Agent
VALID_NODE_CHECKING_REGISTRATION_LISTENER_SCAN1=OFF             # line added by Agent - Disabled by Agent because REMOTE_REGISTRATION_ADDRESS is set
ENABLE_GLOBAL_DYNAMIC_ENDPOINT_LISTENER=ON              # line added by Agent
VALID_NODE_CHECKING_REGISTRATION_LISTENER=SUBNET                # line added by Agent
[root@lnx103 ~]#

[root@lnx103 ~]# ls -l /u01/app/19.0.0/grid/network/admin/
total 12
-rw-r--r-- 1 grid oinstall  826 May 25 09:26 listener.ora
-rw-r--r-- 1 grid oinstall  588 May 25 09:24 listener.ora.bak.lnx103.grid
drwxr-xr-x 2 grid oinstall   64 Apr 17  2019 samples
-rw-r--r-- 1 grid oinstall 1536 Feb 13  2018 shrept.lst

[root@lnx103 ~]# cat /u01/app/19.0.0/grid/network/admin/listener.ora
LISTENER=(DESCRIPTION=(ADDRESS_LIST=(ADDRESS=(PROTOCOL=IPC)(KEY=LISTENER))))            # line added by Agent
LISTENER_SCAN1=(DESCRIPTION=(ADDRESS_LIST=(ADDRESS=(PROTOCOL=IPC)(KEY=LISTENER_SCAN1))))                # line added by Agent
ASMNET1LSNR_ASM=(DESCRIPTION=(ADDRESS_LIST=(ADDRESS=(PROTOCOL=IPC)(KEY=ASMNET1LSNR_ASM))))              # line added by Agent
ENABLE_GLOBAL_DYNAMIC_ENDPOINT_ASMNET1LSNR_ASM=ON               # line added by Agent
VALID_NODE_CHECKING_REGISTRATION_ASMNET1LSNR_ASM=SUBNET         # line added by Agent
ENABLE_GLOBAL_DYNAMIC_ENDPOINT_LISTENER_SCAN1=ON                # line added by Agent
VALID_NODE_CHECKING_REGISTRATION_LISTENER_SCAN1=OFF             # line added by Agent - Disabled by Agent because REMOTE_REGISTRATION_ADDRESS is set
ENABLE_GLOBAL_DYNAMIC_ENDPOINT_LISTENER=ON              # line added by Agent
VALID_NODE_CHECKING_REGISTRATION_LISTENER=SUBNET                # line added by Agent

[root@lnx103 ~]# su - grid -c "srvctl config listener"
Name: LISTENER
Type: Database Listener
Network: 1, Owner: grid
Home: <CRS home>
End points: TCP:1521
Listener is enabled.
Listener is individually enabled on nodes:
Listener is individually disabled on nodes:

[root@lnx103 ~]# su - grid -c "srvctl config scan_listener"
SCAN Listeners for network 1:
Registration invited nodes:
Registration invited subnets:
Endpoints: TCP:1521
SCAN Listener LISTENER_SCAN1 exists
SCAN Listener is enabled.

[root@lnx103 ~]# su - grid -c "srvctl config scan"
SCAN name: lnx103-scan.home.aarisha.com, Network: 1
Subnet IPv4: 192.168.1.0/255.255.255.0/ens18, static
Subnet IPv6:
SCAN 1 IPv4 VIP: 192.168.1.105
SCAN VIP is enabled.
[root@lnx103 ~]# su - grid -c "srvctl status scan_listener"
SCAN Listener LISTENER_SCAN1 is enabled
SCAN listener LISTENER_SCAN1 is running on node lnx103
[root@lnx103 ~]#

[root@lnx103 ~]# su - grid -c "srvctl status database -d proddb"
Instance proddb is running on node lnx103

[root@lnx103 ~]# su - grid -c "srvctl config database -d proddb"
Database unique name: proddb
Database name: proddb
Oracle home: /u01/app/oracle/product/19.0.0/dbhome_1
Oracle user: oracle
Spfile: +DATA/PRODDB/PARAMETERFILE/spfile.271.1234177311
Password file:
Domain: home.aarisha.com
Start options: open
Stop options: immediate
Database role: PRIMARY
Management policy: AUTOMATIC
Server pools:
Disk Groups: FRA,DATA
Mount point paths:
Services:
Type: SINGLE
OSDBA group: dba
OSOPER group: dba
Database instance: proddb
Configured nodes: lnx103
CSS critical: no
CPU count: 0
Memory target: 0
Maximum memory: 0
Default network number for database services:
Database is administrator managed
[root@lnx103 ~]#

# ─── STOP (run as grid) ──────────────────────────────────────────────
# Step 1 — Stop the database (srvctl stops PDBs, closes DB, deregisters)
srvctl stop database -d proddb -o immediate

# Step 2 — Verify database stopped
srvctl status database -d proddb
# Expected: Database is not running.

# Step 3 — Stop the listener (optional — crsctl stop crs does this anyway)
srvctl stop listener -l LISTENER

# Step 4 — Stop the entire Clusterware stack
# This stops: CRS resources → ASM disk groups → ASM instance → OHASD
crsctl stop crs
# Takes 2-5 minutes — waits for clean shutdown of all resources

# ─── VERIFY STOPPED ─────────────────────────────────────────────────
crsctl check crs
# Expected: CRS-4638: Oracle High Availability Services is online
# (OHAS stops last — this is normal)
ps -ef | grep pmon | grep -v grep
# Expected: nothing


# ─── START (run as grid) ─────────────────────────────────────────────
# Step 1 — Start Clusterware stack
# This starts: OHASD → CSSD → CRSD → ASM → disk groups → listener → DB
crsctl start crs
# Takes 3-5 minutes

# Step 2 — Verify everything came up
crsctl check crs
# Expected: all 4 services online

crsctl status resource -t
# Expected: ora.asm, ora.DATA.dg, ora.FRA.dg, ora.LISTENER.lsnr,
#           ora.proddb.db, ora.scan1.vip, ora.lnx103.vip all ONLINE

# Step 3 — Verify database and PDBs
srvctl status database -d proddb
# Expected: Database is running.

su - oracle -c "sqlplus -s / as sysdba << 'EOF'
SELECT name, open_mode FROM v\$pdbs ORDER BY con_id;
EXIT;
EOF"

[grid@lnx103 ~]$ srvctl stop database -d proddb -o immediate
[grid@lnx103 ~]$
[grid@lnx103 ~]$ srvctl status database -d proddb
Instance proddb is not running on node lnx103
[grid@lnx103 ~]$ 

[root@lnx103 ~]# which crsctl
/usr/bin/which: no crsctl in (/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin)
[root@lnx103 ~]# /u01/app/19.0.0/grid/bin/crsctl stop crs
CRS-2791: Starting shutdown of Oracle High Availability Services-managed resources on 'lnx103'
CRS-2673: Attempting to stop 'ora.crsd' on 'lnx103'
CRS-2790: Starting shutdown of Cluster Ready Services-managed resources on server 'lnx103'
CRS-2673: Attempting to stop 'ora.cvu' on 'lnx103'
CRS-2673: Attempting to stop 'ora.LISTENER_SCAN1.lsnr' on 'lnx103'
CRS-2673: Attempting to stop 'ora.LISTENER.lsnr' on 'lnx103'
CRS-2673: Attempting to stop 'ora.chad' on 'lnx103'
CRS-2673: Attempting to stop 'ora.qosmserver' on 'lnx103'
CRS-33673: Attempting to stop resource group 'ora.asmgroup' on server 'lnx103'
CRS-2673: Attempting to stop 'ora.DATA.dg' on 'lnx103'
CRS-2673: Attempting to stop 'ora.FRA.dg' on 'lnx103'
CRS-2677: Stop of 'ora.LISTENER.lsnr' on 'lnx103' succeeded
CRS-2673: Attempting to stop 'ora.lnx103.vip' on 'lnx103'
CRS-2677: Stop of 'ora.LISTENER_SCAN1.lsnr' on 'lnx103' succeeded
CRS-2677: Stop of 'ora.DATA.dg' on 'lnx103' succeeded
CRS-2677: Stop of 'ora.FRA.dg' on 'lnx103' succeeded
CRS-2673: Attempting to stop 'ora.asm' on 'lnx103'
CRS-2673: Attempting to stop 'ora.scan1.vip' on 'lnx103'
CRS-2677: Stop of 'ora.lnx103.vip' on 'lnx103' succeeded
CRS-2677: Stop of 'ora.scan1.vip' on 'lnx103' succeeded
CRS-2677: Stop of 'ora.chad' on 'lnx103' succeeded
CRS-2677: Stop of 'ora.asm' on 'lnx103' succeeded
CRS-2673: Attempting to stop 'ora.ASMNET1LSNR_ASM.lsnr' on 'lnx103'
CRS-2677: Stop of 'ora.cvu' on 'lnx103' succeeded
CRS-2677: Stop of 'ora.qosmserver' on 'lnx103' succeeded
CRS-2677: Stop of 'ora.ASMNET1LSNR_ASM.lsnr' on 'lnx103' succeeded
CRS-2673: Attempting to stop 'ora.asmnet1.asmnetwork' on 'lnx103'
CRS-2677: Stop of 'ora.asmnet1.asmnetwork' on 'lnx103' succeeded
CRS-33677: Stop of resource group 'ora.asmgroup' on server 'lnx103' succeeded.
CRS-2673: Attempting to stop 'ora.ons' on 'lnx103'
CRS-2677: Stop of 'ora.ons' on 'lnx103' succeeded
CRS-2673: Attempting to stop 'ora.net1.network' on 'lnx103'
CRS-2677: Stop of 'ora.net1.network' on 'lnx103' succeeded
CRS-2792: Shutdown of Cluster Ready Services-managed resources on 'lnx103' has completed
CRS-2677: Stop of 'ora.crsd' on 'lnx103' succeeded
CRS-2673: Attempting to stop 'ora.storage' on 'lnx103'
CRS-2673: Attempting to stop 'ora.crf' on 'lnx103'
CRS-2673: Attempting to stop 'ora.mdnsd' on 'lnx103'
CRS-2677: Stop of 'ora.crf' on 'lnx103' succeeded
CRS-2677: Stop of 'ora.storage' on 'lnx103' succeeded
CRS-2673: Attempting to stop 'ora.asm' on 'lnx103'
CRS-2677: Stop of 'ora.mdnsd' on 'lnx103' succeeded
CRS-2677: Stop of 'ora.asm' on 'lnx103' succeeded
CRS-2673: Attempting to stop 'ora.cluster_interconnect.haip' on 'lnx103'
CRS-2677: Stop of 'ora.cluster_interconnect.haip' on 'lnx103' succeeded
CRS-2673: Attempting to stop 'ora.ctssd' on 'lnx103'
CRS-2673: Attempting to stop 'ora.evmd' on 'lnx103'
CRS-2677: Stop of 'ora.evmd' on 'lnx103' succeeded
CRS-2677: Stop of 'ora.ctssd' on 'lnx103' succeeded
CRS-2673: Attempting to stop 'ora.cssd' on 'lnx103'
CRS-2677: Stop of 'ora.cssd' on 'lnx103' succeeded
CRS-2673: Attempting to stop 'ora.gipcd' on 'lnx103'
CRS-2673: Attempting to stop 'ora.gpnpd' on 'lnx103'
CRS-2677: Stop of 'ora.gpnpd' on 'lnx103' succeeded
CRS-2677: Stop of 'ora.gipcd' on 'lnx103' succeeded
CRS-2793: Shutdown of Oracle High Availability Services-managed resources on 'lnx103' has completed
CRS-4133: Oracle High Availability Services has been stopped.
[root@lnx103 ~]# /u01/app/19.0.0/grid/bin/crsctl check crs
CRS-4639: Could not contact Oracle High Availability Services
[root@lnx103 ~]# ps -ef | grep pmon | grep -v grep
[root@lnx103 ~]#

[root@lnx103 ~]# /u01/app/19.0.0/grid/bin/crsctl start crs
CRS-4123: Oracle High Availability Services has been started.

[root@lnx103 ~]# /u01/app/19.0.0/grid/bin/crsctl check crs
CRS-4638: Oracle High Availability Services is online
CRS-4537: Cluster Ready Services is online
CRS-4529: Cluster Synchronization Services is online
CRS-4533: Event Manager is online
[root@lnx103 ~]#

[grid@lnx103 ~]$ srvctl start database -d proddb
[grid@lnx103 ~]$ srvctl status database -d proddb
Instance proddb is running on node lnx103
[grid@lnx103 ~]$ 

[root@lnx103 ~]# su - oracle -c "sqlplus -s / as sysdba << 'EOF'
> SELECT name, open_mode FROM v\$pdbs ORDER BY con_id;
> EXIT;
> EOF"

NAME
--------------------------------------------------------------------------------
OPEN_MODE
----------
PDB$SEED
READ ONLY

PDB1
READ WRITE

PDB2
READ WRITE


[root@lnx103 ~]#

[root@lnx103 ~]# /u01/app/19.0.0/grid/bin/crsctl stop crs
CRS-2791: Starting shutdown of Oracle High Availability Services-managed resources on 'lnx103'
CRS-2673: Attempting to stop 'ora.crsd' on 'lnx103'
CRS-2790: Starting shutdown of Cluster Ready Services-managed resources on server 'lnx103'
CRS-2673: Attempting to stop 'ora.chad' on 'lnx103'
CRS-2673: Attempting to stop 'ora.proddb.db' on 'lnx103'
CRS-2673: Attempting to stop 'ora.qosmserver' on 'lnx103'
CRS-2677: Stop of 'ora.chad' on 'lnx103' succeeded
CRS-2677: Stop of 'ora.proddb.db' on 'lnx103' succeeded
CRS-33673: Attempting to stop resource group 'ora.asmgroup' on server 'lnx103'
CRS-2673: Attempting to stop 'ora.DATA.dg' on 'lnx103'
CRS-2673: Attempting to stop 'ora.FRA.dg' on 'lnx103'
CRS-2673: Attempting to stop 'ora.LISTENER.lsnr' on 'lnx103'
CRS-2673: Attempting to stop 'ora.LISTENER_SCAN1.lsnr' on 'lnx103'
CRS-2673: Attempting to stop 'ora.cvu' on 'lnx103'
CRS-2677: Stop of 'ora.DATA.dg' on 'lnx103' succeeded
CRS-2677: Stop of 'ora.FRA.dg' on 'lnx103' succeeded
CRS-2673: Attempting to stop 'ora.asm' on 'lnx103'
CRS-2677: Stop of 'ora.asm' on 'lnx103' succeeded
CRS-2673: Attempting to stop 'ora.ASMNET1LSNR_ASM.lsnr' on 'lnx103'
CRS-2677: Stop of 'ora.LISTENER.lsnr' on 'lnx103' succeeded
CRS-2673: Attempting to stop 'ora.lnx103.vip' on 'lnx103'
CRS-2677: Stop of 'ora.LISTENER_SCAN1.lsnr' on 'lnx103' succeeded
CRS-2673: Attempting to stop 'ora.scan1.vip' on 'lnx103'
CRS-2677: Stop of 'ora.lnx103.vip' on 'lnx103' succeeded
CRS-2677: Stop of 'ora.scan1.vip' on 'lnx103' succeeded
CRS-2677: Stop of 'ora.qosmserver' on 'lnx103' succeeded
CRS-2677: Stop of 'ora.cvu' on 'lnx103' succeeded
CRS-2677: Stop of 'ora.ASMNET1LSNR_ASM.lsnr' on 'lnx103' succeeded
CRS-2673: Attempting to stop 'ora.asmnet1.asmnetwork' on 'lnx103'
CRS-2677: Stop of 'ora.asmnet1.asmnetwork' on 'lnx103' succeeded
CRS-33677: Stop of resource group 'ora.asmgroup' on server 'lnx103' succeeded.
CRS-2673: Attempting to stop 'ora.ons' on 'lnx103'
CRS-2677: Stop of 'ora.ons' on 'lnx103' succeeded
CRS-2673: Attempting to stop 'ora.net1.network' on 'lnx103'
CRS-2677: Stop of 'ora.net1.network' on 'lnx103' succeeded
CRS-2792: Shutdown of Cluster Ready Services-managed resources on 'lnx103' has completed
CRS-2677: Stop of 'ora.crsd' on 'lnx103' succeeded
CRS-2673: Attempting to stop 'ora.storage' on 'lnx103'
CRS-2673: Attempting to stop 'ora.crf' on 'lnx103'
CRS-2673: Attempting to stop 'ora.mdnsd' on 'lnx103'
CRS-2677: Stop of 'ora.crf' on 'lnx103' succeeded
CRS-2677: Stop of 'ora.storage' on 'lnx103' succeeded
CRS-2673: Attempting to stop 'ora.asm' on 'lnx103'
CRS-2677: Stop of 'ora.mdnsd' on 'lnx103' succeeded
CRS-2677: Stop of 'ora.asm' on 'lnx103' succeeded
CRS-2673: Attempting to stop 'ora.cluster_interconnect.haip' on 'lnx103'
CRS-2677: Stop of 'ora.cluster_interconnect.haip' on 'lnx103' succeeded
CRS-2673: Attempting to stop 'ora.ctssd' on 'lnx103'
CRS-2673: Attempting to stop 'ora.evmd' on 'lnx103'
CRS-2677: Stop of 'ora.ctssd' on 'lnx103' succeeded
CRS-2677: Stop of 'ora.evmd' on 'lnx103' succeeded
CRS-2673: Attempting to stop 'ora.cssd' on 'lnx103'
CRS-2677: Stop of 'ora.cssd' on 'lnx103' succeeded
CRS-2673: Attempting to stop 'ora.gipcd' on 'lnx103'
CRS-2673: Attempting to stop 'ora.gpnpd' on 'lnx103'
CRS-2677: Stop of 'ora.gpnpd' on 'lnx103' succeeded
CRS-2677: Stop of 'ora.gipcd' on 'lnx103' succeeded
CRS-2793: Shutdown of Oracle High Availability Services-managed resources on 'lnx103' has completed
CRS-4133: Oracle High Availability Services has been stopped.
[root@lnx103 ~]# ps -eaf | grep pmon
root      173061    7900  0 13:34 pts/0    00:00:00 grep --color=auto pmon
[root@lnx103 ~]#

[root@lnx103 ~]# /u01/app/19.0.0/grid/bin/crsctl start crs
CRS-4123: Oracle High Availability Services has been started.
[root@lnx103 ~]#

[root@lnx103 ~]# /u01/app/19.0.0/grid/bin/crsctl check crs
CRS-4638: Oracle High Availability Services is online
CRS-4537: Cluster Ready Services is online
CRS-4529: Cluster Synchronization Services is online
CRS-4533: Event Manager is online
[root@lnx103 ~]

[root@lnx103 ~]# /u01/app/19.0.0/grid/bin/crsctl status resource -t
--------------------------------------------------------------------------------
Name           Target  State        Server                   State details
--------------------------------------------------------------------------------
Local Resources
--------------------------------------------------------------------------------
ora.LISTENER.lsnr
               ONLINE  ONLINE       lnx103                   STABLE
ora.chad
               ONLINE  ONLINE       lnx103                   STABLE
ora.net1.network
               ONLINE  ONLINE       lnx103                   STABLE
ora.ons
               ONLINE  ONLINE       lnx103                   STABLE
--------------------------------------------------------------------------------
Cluster Resources
--------------------------------------------------------------------------------
ora.ASMNET1LSNR_ASM.lsnr(ora.asmgroup)
      1        ONLINE  ONLINE       lnx103                   STABLE
      2        ONLINE  OFFLINE                               STABLE
      3        ONLINE  OFFLINE                               STABLE
ora.DATA.dg(ora.asmgroup)
      1        ONLINE  ONLINE       lnx103                   STABLE
      2        OFFLINE OFFLINE                               STABLE
      3        OFFLINE OFFLINE                               STABLE
ora.FRA.dg(ora.asmgroup)
      1        ONLINE  ONLINE       lnx103                   STABLE
      2        OFFLINE OFFLINE                               STABLE
      3        OFFLINE OFFLINE                               STABLE
ora.LISTENER_SCAN1.lsnr
      1        ONLINE  ONLINE       lnx103                   STABLE
ora.asm(ora.asmgroup)
      1        ONLINE  ONLINE       lnx103                   Started,STABLE
      2        OFFLINE OFFLINE                               STABLE
      3        OFFLINE OFFLINE                               STABLE
ora.asmnet1.asmnetwork(ora.asmgroup)
      1        ONLINE  ONLINE       lnx103                   STABLE
      2        OFFLINE OFFLINE                               STABLE
      3        OFFLINE OFFLINE                               STABLE
ora.cvu
      1        ONLINE  ONLINE       lnx103                   STABLE
ora.lnx103.vip
      1        ONLINE  ONLINE       lnx103                   STABLE
ora.proddb.db
      1        ONLINE  ONLINE       lnx103                   Open,HOME=/u01/app/o
                                                             racle/product/19.0.0
                                                             /dbhome_1,STABLE
ora.qosmserver
      1        ONLINE  ONLINE       lnx103                   STABLE
ora.scan1.vip
      1        ONLINE  ONLINE       lnx103                   STABLE
--------------------------------------------------------------------------------
[root@lnx103 ~]#

[root@lnx103 ~]# ps -eaf | grep pmon
grid      174684       1  0 13:36 ?        00:00:00 asm_pmon_+ASM1
oracle    176283       1  0 13:36 ?        00:00:00 ora_pmon_proddb
root      182808    7900  0 13:48 pts/0    00:00:00 grep --color=auto pmon
[root@lnx103 ~]#

[root@lnx103 ~]# ps -eaf | grep pmon
grid        3692       1  0 13:57 ?        00:00:00 asm_pmon_+ASM1
root        3797    3554  0 13:57 pts/0    00:00:00 grep --color=auto pmon

[root@lnx103 ~]# su - grid -c "crsctl check crs"
CRS-4638: Oracle High Availability Services is online
CRS-4537: Cluster Ready Services is online
CRS-4529: Cluster Synchronization Services is online
CRS-4533: Event Manager is online

[root@lnx103 ~]# ps -eaf | grep pmon
grid        3692       1  0 13:57 ?        00:00:00 asm_pmon_+ASM1
oracle      5098       1  0 13:57 ?        00:00:00 ora_pmon_proddb
root        9778    3554  0 14:08 pts/0    00:00:00 grep --color=auto pmon

[root@lnx103 ~]# su - grid -c "srvctl status listener"
Listener LISTENER is enabled
Listener LISTENER is running on node(s): lnx103

[root@lnx103 ~]# su - oracle -c "tnsping proddb"

TNS Ping Utility for Linux: Version 19.0.0.0.0 - Production on 25-MAY-2026 14:10:03

Copyright (c) 1997, 2019, Oracle.  All rights reserved.

Used parameter files:


Used TNSNAMES adapter to resolve the alias
Attempting to contact (DESCRIPTION = (ADDRESS = (PROTOCOL = TCP)(HOST = lnx103)(PORT = 1521)) (CONNECT_DATA = (SERVER = DEDICATED) (SERVICE_NAME = proddb.home.aarisha.com)))
OK (0 msec)
[root@lnx103 ~]#
