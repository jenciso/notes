### Scripts to create vm

```
#!/bin/bash -e


curl -O http://ftp.heanet.ie/pub/centos/7.0.1406/isos/x86_64/CentOS-7.0-1406-x86_64-Minimal.iso
export VM="MASTER"
export VMDISK="$VM-disk"
export REDHAT_IMAGE="/Users/user/Downloads/CentOS-7.0-1406-x86_64-Minimal.iso"

VBoxManage hostonlyif create
VBoxManage hostonlyif ipconfig vboxnet0 --ip 192.168.20.1
VBoxManage dhcpserver add --ifname vboxnet0 --ip 192.168.20.1 --netmask 255.255.255.0 --lowerip 192.168.20.10 --upperip 192.168.20.20 --enable 

VBoxManage createhd --size 4096 --variant Fixed --filename ~/VirtualBox\ VMs/$VMDISK
VBoxManage createvm --register --name $VM --ostype RedHat_64

VBoxManage storagectl $VM --name "SATA Controller" --add sata  --controller IntelAHCI
VBoxManage storageattach $VM --storagectl "SATA Controller" --port 0  --device 0 --type hdd --medium $VMDISK.vdi
VBoxManage storageattach $VM --storagectl "SATA Controller" --port 1 --device 0 --type dvddrive --medium $REDHAT_IMAGE

VBoxManage modifyvm $VM --memory 512 --acpi on --boot1 dvd 
VBoxManage modifyvm $VM --nic1 hostonly --nictype1 virtio --hostonlyadapter1 vboxnet0
VBoxManage modifyvm $VM --nic2 nat --nictype2 virtio
VBoxManage startvm $VM
VBoxManage controlvm $VM poweroff

VBoxManage storageattach $VM --storagectl "SATA Controller" --port 1 --device 0 --type dvddrive --medium none

VBoxManage clonevm $VM --name "SLAVE0" --register
VBoxManage clonevm $VM --name "SLAVE1" --register
VBoxManage startvm "SLAVE0" "SLAVE1" --type headless

ssh root@192.168.20.10 "echo 'HOSTNAME=SLAVE0' >> /etc/sysconfig/network; hostname SLAVE0"
ssh root@192.168.20.11 "echo 'HOSTNAME=SLAVE1' >> /etc/sysconfig/network; hostname SLAVE1"



# There is NO PROBLEM with IP addresses now. VMs are accessbile from the host.
# Guest VMs have access to the Internet via NAT, but host-only connection between each other,
# HOST can reach VMs via the following IP addresses:
# ssh root@192.168.20.10
# ssh root@192.168.20.11
```
