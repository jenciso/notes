## Remove service in systemd
```
systemctl stop [servicename]
systemctl disable [servicename]
rm /etc/systemd/system/[servicename]
rm /etc/systemd/system/[servicename] symlinks that might be related
systemctl daemon-reload
systemctl reset-failed
```

## Add disk without reboot

http://linoxide.com/linux-how-to/add-new-disk-centos-7-without-rebooting/

## Add LVM disk
Step 1: Change table partition
```
fdisk /dev/sdb
```
Choose type LVM:8e
Step 2: Create VG and LVM group
```
pvcreate /dev/sdb1
vgcreate vg-data /dev/sdb1
lvcreate -l 100%FREE vg-data -n lv-data
mkfs.xfs /dev/vg-data/lv-data
mkdir /data
```
Alternative 2, use xfs

```
mkfs.ext4 -m 0 /dev/vg-data/lv-data
```

Step 3: Add entry in fstab
```
/dev/vg-data/lv-data    /data           xfs    defaults        1 1
```

## Change default boot entry in CentOS
```
vim /etc/default/grub 
grub2-mkconfig -o /boot/grub2/grub.cfg
```
http://ask.xmodulo.com/change-default-boot-kernel-centos.html

## Generate SHA2-512 password 

Ubuntu:
```
mkpasswd --method=SHA-512 -S password_plain
```

REDHAT 7
``` 
python -c 'import crypt,getpass; print(crypt.crypt(getpass.getpass(), crypt.mksalt(crypt.METHOD_SHA512)))'
```
(with prompt#)

## Equivalente repoquery command for ubuntu
```
apt-cache showpkg ansible
``` 


## Using ngrep against tcpdump

TCP 80
```
ngrep -d any -pqt -W single '' port 80
```

TCP 10050 (ZABBIX)
```
ngrep -d any -pqt -W single '' port 10050
``` 

TCP 10051 (ZABBIX-ACTIVE)
```
ngrep -d any -pqt -W single '' port 10051
```

## Mount windows filesystem using cifs
Step 1: Pre-requisites
```
yum -y install cifs-utils
```
Step 2: Mount
```
mount -t cifs -o username=<share user>,password=<share password> //WIN_PC_IP/<share name> /mnt
or
mount -t cifs -o username=<share user>,password=<share password>,domain=example.com //WIN_PC_IP/<share name> /mnt
```
By default windows mount with the full permission 777, to change:
```
mount -t cifs -o username=<share user>,password=<share password>,dir_mode=0755,file_mode=0755 //WIN_PC_IP/<share name> /mnt
```
Step 3: Mount via /etc/fstab
```
//WIN_PC_IP/<share name>    /<mntpoint>   cifs  _netdev,username=<share user>,password=<share password>,dir_mode=0755,file_mode=0755,uid=500,gid=500 0 0
```

## Backup using dump
Example 1: 
```
dump u0f /mnt/Server216/raiz_216.dump /
```
Example 2:
```
rsh -n otherbox /sbin/dump u0f - /usr | dd of=/dev/nrsa0 obs=32k
```
Example 3:
```
dump -0 -b 32 -u -f otherbox:/dev/nrsa0 /usr
```

## Change Keyboard map in CentOS 7
Step 1: Download package 
```
yum -y install system-config-keyboard
```
Step 2: Run command and change keyboard map
```
system-config-keyboard
```
Manually, setup Brazilian Keymaps
```
[root@localhost ~]# system-config-keyboard
Loading /lib/kbd/keymaps/legacy/i386/qwerty/br-abnt2.map.gz
[root@localhost ~]#
```
## Show processes and threads  
```
ps auxf
```
or 
```
pstree
```

## Kill subprocess in daemontools
Add this line in your config

```
stopasgroup = true
```
https://coderwall.com/p/4tcw7w/setting-supervisor-to-really-stop-django-runserver

## Installing Unattended Upgrades - Ubuntu
```
sudo unattended-upgrade
sudo dpkg-reconfigure --priority=low unattended-upgrades
```

## Change localtime - Centos 7

List all available timezones
```
timedatectl list-timezones
```

Set for the correct timezone
```
timedatectl set-timezone America/Sao_Paulo
```

## Change Network Interface Name from enp* to eth*

Step 1: Edit */etc/sysconfig/grub* config file and append the follow line: **net.ifnames=0 biosdevname=0**

Example:
```
GRUB_CMDLINE_LINUX="rd.lvm.lv=centos/swap vconsole.font=latarcyrheb-sun16 rd.lvm.lv=centos/root crashkernel=auto  vconsole.keymap=us rhgb quiet net.ifnames=0 biosdevname=0"
```

Step 2: Re-generate a new grub using the new config file
```
grub2-mkconfig -o /boot/grub2/grug.cfg
```

Step 3: Rename the "enp0s3" network file to "eth0", and modify its content
```
mv /etc/sysconfig/network-scripts/ifcfg-enps03  /etc/sysconfig/network-scripts/ifcfg-eth0
sed -i -e "s/enp0s3/eth0/" /etc/sysconfig/network-scripts/ifcfg-eth0
```

Step 4: Reboot


## Example for Ldap Search
```
ldapsearch -h e-unicred.com.br -x -D "CN=svc.infra,OU=Servicos,OU=TS,DC=e-unicred,DC=com,DC=br" -W -b "dc=e-unicred,dc=com,dc=br" "(sAMAccountName=juan.enciso)"
```

## Create loop storage
```
#dd if=/dev/zero of=~/file.img bs=1MiB count=10
#losetup --find --show ~/file.img
/dev/loop0
#mkfs -t ext2 /dev/loop0
#mount /dev/loop0 /mnt
...
#umount /dev/loop0
#losetup --detach /dev/loop0
```

## Bonding Interfaces
https://www.unixmen.com/linux-basics-create-network-bonding-on-centos-76-5/

## Extract RPM Files
```
$ mkdir test
$ cd test
$ wget https://www.cyberciti.biz/files/lighttpd/rhel4-php5-fastcgi/php-5.1.4-1.esp1.x86_64.rpm
$ rpm2cpio php-5.1.4-1.esp1.x86_64.rpm | cpio -idmv
```

## See scripts in RPM files
```
rpm -qp --scripts foo.rpm
```
```
rpm -q --scripts httpd
```

## Check Threads system wide settings

```
echo 100000 > /proc/sys/kernel/threads-max
```

```
$ cat /proc/loadavg
0.41 0.45 0.57 3/749 28174
```

Number of process 
```
$ ps -elf | wc -l  
220
```
Number of process including threads
```
$ ps -elfT | wc -l  
385
```

From a specific pid

```
 ps -p JBOSSPID -lfT | wc -l
```
Source 
http://www.mastertheboss.com/jboss-server/jboss-monitoring/how-to-solve-javalangoutofmemoryerror-unable-to-create-new-native-thread

