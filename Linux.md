## Setup DNS via nmcli 

```
nmcli dev show
nmcli con mod VIVOFIBRA-30FA ipv4.dns "1.1.1.1 1.0.0.1"
nmcli con down VIVOFIBRA-30FA
nmcli con up  VIVOFIBRA-30FA
```
https://serverfault.com/questions/810636/how-to-manage-dns-in-networkmanager-via-console-nmcli

## RHEL to Centos 7

```
rpm --erase $( rpm -qa | egrep 'subscription-manager|rhn|redhat-support|redhat-access' )

```
## IP address

```
ip addr add 192.168.2.24/24 dev eth0
ip route add default via 192.168.2.1
```

## how to find DRAC IP address

```
racadm getniccfg
```

Before, you need to install omsa pacakges

```
wget -q -O - http://linux.dell.com/repo/hardware/dsu/bootstrap.cgi | bash
yum install -y srvadmin-all
/opt/dell/srvadmin/sbin/srvadmin-services.sh start
```
## Avoiding ARP Flux

```
sysctl -w net.ipv4.conf.all.arp_announce=1
sysctl -w net.ipv4.conf.all.arp_ignore=2
```
Source: https://netbeez.net/blog/avoiding-arp-flux-in-multi-interface-linux-hosts/


## bash arrays

https://www.linuxjournal.com/content/bash-arrays

## Regexp 

https://regexr.com/

## Process and task by memory sort 

```
ps -eo size,pid,user,command --sort -size | awk '{ hr=$1/1024 ; printf("%13.2f Mb ",hr) } { for ( x=4 ; x<=NF ; x++ ) { printf("%s ",$x) } print "" }' |cut -d "" -f2 | cut -d "-" -f1
```

## Sort Linux ‘ps’ output by memory (RAM), from high to low

	ps aux --sort -rss
	ps aux --sort rss

Another examples:

	ps aux --sort -pid


Source: https://alvinalexander.com/linux/unix-linux-process-memory-sort-ps-command-cpu

## Check URL status


	while true; do (echo -n "`date` --- "; curl -s https://api.infobip.com/status ; echo " RESULT: $?" ) ; done
	
	curl -o /dev/null -s -w "%{http_code}\n" http://${GATEWAY_URL}/productpage
	
	curl ifconfig.co
	

## Install pbcopy ubuntu 

Install xclip

	sudo apt-get install xclip -y


Editar `~/.bashrc`: 

	alias pbcopy='xclip -selection clipboard'
	alias pbpaste='xclip -selection clipboard -o'

Save and test:

	source ~/.bashrc
	
	date | pbcopy
	pbpaste

## JQ

http://blog.librato.com/posts/jq-json

## Mount using uuid 

first, you have to find it 

Ex:

```
[root@ocpvm090lb121 ~]# ls -l /dev/disk/by-uuid/
total 0
lrwxrwxrwx. 1 root root  9 Jul 30 23:22 12bb5d8e-7627-4bd8-820e-8aa952ae8b84 -> ../../sdd
lrwxrwxrwx. 1 root root  9 Jul 30 23:22 2018-07-30-23-22-03-00 -> ../../sr1
lrwxrwxrwx. 1 root root 10 Aug  6 16:29 24c5adc2-3239-489e-b09b-0610de88d35d -> ../../dm-1
lrwxrwxrwx. 1 root root 10 Jul 30 23:22 54762f23-1d72-46ff-89bf-a2d6e651a6f9 -> ../../dm-0
lrwxrwxrwx. 1 root root  9 Jul 30 23:22 a067d397-b047-44c6-9cff-9a62d6388bc9 -> ../../sdb
lrwxrwxrwx. 1 root root 10 Jul 30 23:22 bb65a208-3d3a-4c4e-a8da-3ca0d92c754e -> ../../sda1
[root@ocpvm090lb121 ~]# 
```

Editing the /etc/fstab, to mount /dev/sdd, add the line:

	UUID=12bb5d8e-7627-4bd8-820e-8aa952ae8b84 /var/lib/etcd                            xfs   defaults 0 0

Format it, and mount 

	mkfs -t xfs /dev/sdd ; mount -a


## Extend partition size

Ex. Using these values: 
``` 
Disk: /dev/sdc1
VG: vg-data
LV: lv-data
```

```
fdisk /dev/sdc
pvcreate /dev/sdc1
vgextend vg-data /dev/sdc1
lvextend -L+99G /dev/vg-data/lv-data
xfs_growfs  /dev/vg-data/lv-data
```

or, for extend your lv space to 100% of vg, use:

	lvextend -l 100%FREE /dev/vg-data/lv-data

## how to redirect output of systemd service to a file

Is there a more elegant way to solve the problem: send the `stdout/stderr` to syslog with an identifier and instruct your syslog manager to split its output by programname.

Use the following properties in your systemd service unit file:

```
StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=<your program identifier> # without any quote
```

Then, assuming your distribution is using rsyslog to manage syslogs, create a file in `/etc/rsyslog.d/<new_file>.conf` with the following content:

```
if $programname == '<your program identifier>' then /path/to/log/file.log
if $programname == '<your program identifier>' then ~
```

restart rsyslog (sudo systemctl restart rsyslog) and enjoy! Your program stdout/stderr will still be available through journalctl (`sudo journalctl -u`) but they will also be available in your file of choice.

Source: http://wiki.rsyslog.com/index.php/Filtering_by_program_name


> From the systemd.exec(5) man page:

> StandardOutput=
> Controls where file descriptor 1 (STDOUT) of the executed processes is connected to. Takes one of inherit, null, tty, journal, syslog, kmsg, journal+console, syslog+console, kmsg+console or socket.

## How get a kernel panic

```
echo c > /proc/sysrq-trigger
```

## Remove a service in systemd
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

```shell
awk -F\' '$1=="menuentry " {print $2}' /etc/grub2.cfg
grub2-set-default 3
```
> line number 1 but denoted as entry 0

```shell
grub2-mkconfig -o /boot/grub2/grub.cfg
reboot
```

https://www.thegeekdiary.com/centos-rhel-7-change-default-kernel-boot-with-old-kernel/

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


## Generate TOKEN

	date | md5sum

	date +%s | sha256sum | base64 | head -c 32 ; echo

	< /dev/urandom tr -dc _A-Z-a-z-0-9 | head -c${1:-32};echo;

	openssl rand -base64 32

	tr -cd '[:alnum:]' < /dev/urandom | fold -w30 | head -n1

	strings /dev/urandom | grep -o '[[:alnum:]]' | head -n 30 | tr -d '\n';


Source: https://www.howtogeek.com/howto/30184/10-ways-to-generate-a-random-password-from-the-command-line/


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

## Supervisord

```
http://www.inanzzz.com/index.php/post/axh1/monitoring-a-php-process-example-with-supervisor
https://advancedweb.hu/supervisor-with-docker-lessons-learned/
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
grub2-mkconfig -o /boot/grub2/grub.cfg
```

Step 3: Rename the "enp0s3" network file to "eth0", and modify its content
```
mv /etc/sysconfig/network-scripts/ifcfg-enps03  /etc/sysconfig/network-scripts/ifcfg-eth0
sed -i -e "s/enp0s3/eth0/" /etc/sysconfig/network-scripts/ifcfg-eth0
```

Step 4: Reboot


## Example for Ldap Search

Search by username

```
ldapsearch -h e-unicred.com.br -x -D "CN=svc.infra,OU=Servicos,OU=TS,DC=e-unicred,DC=com,DC=br" -W -b "dc=e-unicred,dc=com,dc=br" "(sAMAccountName=juan.enciso)"
```

Search Groups

```
ldapsearch -h e-unicred.com.br -x -LLL -D "CN=svc.infra,OU=Servicos,OU=TS,DC=e-unicred,DC=com,DC=br" -b "OU=GRUPOS,OU=TS,dc=e-unicred,dc=com,dc=br" "(CN=kubernetes-cluster-admin)" -w '*****' -o ldif-wrap=no -s sub "cn=*" member | grep -b ^dn | grep member | sed -e 's/member: //' > /tmp/users.txt
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


## How change default editor (ex. from nano to vim, in visudo)

```sh
root@TEC1971:~# vim /usr/share/applications/defaults.list
root@TEC1971:~# visudo
visudo: /etc/sudoers.tmp unchanged
root@TEC1971:~# sudo update-alternatives --config editor
There are 4 choices for the alternative editor (providing /usr/bin/editor).

  Selection    Path                Priority   Status
------------------------------------------------------------
* 0            /bin/nano            40        auto mode
  1            /bin/ed             -100       manual mode
  2            /bin/nano            40        manual mode
  3            /usr/bin/vim.basic   30        manual mode
  4            /usr/bin/vim.tiny    10        manual mode

Press enter to keep the current choice[*], or type selection number: 3
update-alternatives: using /usr/bin/vim.basic to provide /usr/bin/editor (editor) in manual mode
root@TEC1971:~#
```

## visudo NOPASSWD

setup your user:
```
echo "$(id -nu) ALL=(ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/$(id -nu)
```

For a single user :
```yaml
superuser ALL=(ALL) NOPASSWD:ALL
```

For a group :
```yaml
%supergroup  ALL=(ALL) NOPASSWD:ALL
```

## SIMPLE, EASY NAT / PORT FORWARDING FOR IPTABLES 

```sh
sudo echo 1 > /proc/sys/net/ipv4/ip_forward
```
or
```sh
sudo vi /etc/sysctl.conf
net.ipv4.ip_forward=1
```
eth0: primary nic (public) / eth1: secondary nic (private)

```sh
sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
sudo iptables -A FORWARD -i eth0 -o eth1 -m state --state RELATED,ESTABLISHED -j ACCEPT
sudo iptables -A FORWARD -i eth1 -o eth0 -j ACCEPT
```

save
```sh
sudo service iptables-persistent save
```

## ONLY FOR PUBLIC ADDRESS

chmod +x /etc/rc.d/rc.local

```
[root@dcbvm090hm300 ~]# cat  /usr/local/bin/router.sh          
#!/bin/bash

echo 1 > /proc/sys/net/ipv4/ip_forward

iptables -t nat -A POSTROUTING -m iprange ! --dst-range 10.0.0.1-10.255.255.255 -o eth0 -j MASQUERADE
iptables -A FORWARD -i eth0 -o eth1 -m state --state RELATED,ESTABLISHED -j ACCEPT
iptables -A FORWARD -i eth1 -o eth0 -j ACCEPT
```

## PORT-FORWARDING

```
sudo iptables -I FORWARD -p tcp -i eth0 -d <private LAN server IP> --dport <inbound port> -j ACCEPT
sudo iptables -t nat -A PREROUTING -p tcp -i eth0 -d <Firewall Public IP> --dport <Inbound Port> -j DNAT --to-destination <private LAN server IP>:<Port where service runs>
```

## Hyper-V - Centos - Selinux

```
cat > /tmp/hyperv-daemons.te << EOT
module hyperv-daemons 1.0;
require {
 type hypervkvp_t;
 type device_t;
 type hypervvssd_t;
 type ifconfig_t;
 class chr_file { read write open };
}
allow hypervkvp_t device_t:chr_file { read write open };
allow hypervvssd_t device_t:chr_file { read write open };
allow ifconfig_t device_t:chr_file { read write open };
EOT
```

```
  checkmodule -M -m -o /tmp/hyperv-daemons.mod /tmp/hyperv-daemons.te
  semodule_package -o /tmp/hyperv-daemons.pp -m /tmp/hyperv-daemons.mod
  sudo semodule -s targeted -i /tmp/hyperv-daemons.pp
```

## Install Process of HyperV daemon in Centos

https://access.redhat.com/solutions/2823991

## Script to install hyper-V using rpm
https://gist.github.com/gildas/4b1c5e19fa8057d90d745c1754cb46b2#file-hyperv-integration-centos-sh-L14

## Make permissive hyperv modules in selinux Config
```
yum -y install policycoreutils-python
semanage permissive -a hypervvssd_t
reboot
```
https://social.technet.microsoft.com/Forums/windows/en-US/cfe15e32-bfbc-47e0-8d2b-382a1293b9aa/vss-issues-with-centos-66-x64?forum=linuxintegrationservices

## Systemd

List all services

```
systemctl list-units --full --all
```

## Sed and Perl to print matched expression

```
sed -n 's/.* \([0-9]*%\),.*/Battery: \1/p'
```

```
perl -ne '/(\d+%)/ && print "Battery: $1\n";'
```
