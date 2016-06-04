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

