## Setup StandAlone Server

Install Centos 7.4


## Install windows client to get the VM console
```
virt-manager.org/download/
```
## Console Enable

Add Iptables Rule in /etc/rc.local

## Install Guest

```
yum -y install epel-release
yum -y install ovirt-guest-agent
systemctl start ovirt-guest-agent.service
systemctl enable  ovirt-guest-agent.service
```

Based from

```
https://www.youtube.com/watch?v=Pasz-lv3gTY
```
## Upload ISO images

Create a `/iso`directory 

	mkdir /iso

Upload the image file `.iso` into `/iso` and execute this command: (RedHat iso)

	engine-iso-uploader -i ISO_DOMAIN upload /iso/rhel-server-7.6-x86_64-dvd.iso --insecure


