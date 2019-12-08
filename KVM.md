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
vi /etc/sysconfig/network-scripts/ifcfg-virbr0
```

```
DEVICE="virbr0"
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
vi /etc/sysconfig/network-scripts/ifcfg-enp3s0f0
```

```
DEVICE=enp3s0f0
TYPE=Ethernet
BOOTPROTO=none
ONBOOT=yes
NM_CONTROLLED=no
BRIDGE=virbr0
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

Sources: 
* https://www.cyberciti.biz/faq/how-to-install-kvm-on-centos-7-rhel-7-headless-server/
* https://legalimpurity.com/blog/kvm-on-centos-7-minimal-headless-server/
* https://blog.programster.org/kvm-cheatsheet

