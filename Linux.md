## Add LVM disk
Step 1: Change table partition
```
fdisk /dev/sdc
```
Choose type LVM:8e
Step 2: Create VG and LVM group
```
pvcreate /dev/sdc1
vgcreate vg-data /dev/sdc1
lvcreate -l 100%FREE vg-data -n lv-data
mkfs.ext4 -m 0 /dev/vg-data/lv-data
mkdir /data
```
Step 3: Add entry in fstab
```
/dev/vg-data/lv-data    /data           ext4    defaults        1 1
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

## Show processes and threads  
```
ps auxf
```
or 
```
pstree
```

