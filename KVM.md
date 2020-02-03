## KVM INSTALL 

* Check

```
egrep -c '(vmx|svm)' /proc/cpuinfo
sudo apt install cpu-checker
sudo kvm-ok
``` 

* Install - Ubuntu
```
sudo apt update
sudo apt install qemu qemu-kvm libvirt-bin  bridge-utils  virt-manager
sudo service libvirtd start
sudo update-rc.d libvirtd enable
service libvirtd status
``` 

* Install - Centos
```
yum install kvm virt-manager libvirt virt-install qemu-kvm xauth dejavu-lgc-sans-fonts libguestfs-tools
systemctl start libvirtd
systemctl enable libvirtd
```

References:

* https://www.linuxtechi.com/install-configure-kvm-ubuntu-18-04-server/
* https://legalimpurity.com/blog/kvm-on-centos-7-minimal-headless-server/


## Network setup

Prereq
```
yum install bridge-utils -y
modprobe --first-time bridge
```

Test
```
modinfo bridge
```

NIC configuration

```
$ cat /etc/sysconfig/network-scripts/ifcfg-br0
DEVICE="br0"
BOOTPROTO="static"
IPADDR="192.168.12.10"
NETMASK="255.255.255.0"
GATEWAY="192.168.12.2"
DNS1=192.168.12.2
ONBOOT="yes"
TYPE="Bridge"
NM_CONTROLLED="no"
```

```
$ cat  /etc/sysconfig/network-scripts/ifcfg-enp3s0f0
DEVICE=enp3s0f0
TYPE=Ethernet
BOOTPROTO=none
ONBOOT=yes
NM_CONTROLLED=no
BRIDGE=br0
```

```
systemctl restart network
```

Source: https://www.itzgeek.com/how-tos/mini-howtos/create-a-network-bridge-on-centos-7-rhel-7.html

## KVM Headless

Delete VM

```
VM=centos7-vm1
D=/data/vms

virsh shutdown $VM
virsh undefine $VM
virsh pool-destroy $VM
rm -ri $D/$VM
```

### Windows 2012 and Timezone

```
  <clock offset='localtime'>
    <timer name='rtc' tickpolicy='catchup'/>
    <timer name='pit' tickpolicy='delay'/>
    <timer name='hpet' present='no'/>
  </clock>
``` 

* https://stackoverflow.com/questions/20286207/windows-time-drifting-with-qemu-kvm

## KVM thin provision

References:

http://blog.programster.org/kvm-creating-thinly-provisioned-guests

-----

Sources: 
* https://www.cyberciti.biz/faq/how-to-install-kvm-on-centos-7-rhel-7-headless-server/
* https://legalimpurity.com/blog/kvm-on-centos-7-minimal-headless-server/
* https://blog.programster.org/kvm-cheatsheet

