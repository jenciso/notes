## STEPS TO INSTALL OPENSHIFT

### Templates 

Create a VM with RedHat 7.5 and 2 HD's (20GB each one)
- 20GB to operating system
- 20GB LVM docker partition


[Here](http://ostolc.org/kvm-clone-guests-from-template-image.html) is how you could create a template using KVM libvirt

E.g.  Creating template named `openshift`

```sh
virsh dumpxml tpl-ocp > openshift.xml
cp tpl-ocp.qcow2 openshift.qcow2
cp tpl-ocp-1.qcow2 openshift-1.qcow2
```

Edit the openshift.xml and change the disks name

```
vim openshift.xml
:%s/tpl-ocp.qcow2/openshift.qcow2/g
:%s/tpl-ocp-1.qcow2/openshift-1.qcow2/g
```


```
virt-sysprep -a openshift.qcow2
``` 

Create 9 VM's

```
1 x bastion
1 x load balancer
3 x node master
3 x node infra
2 x node apps
```

use vir-clone to create each server from template `openshift`
E.g.:  intelbras-ocp311 -> load balancer

``` 
virt-clone --connect qemu:///system --original-xml /var/lib/libvirt/images/openshift.xml --name intelbras-ocp311 --file /var/lib/libvirt/images/intelbras-ocp311.qcow2 --file /var/lib/libvirt/images/intelbras-ocp311-1.qcow2
``` 

### Prepare bastion server

Create the ssh-key for root

```
su - 
ssh-keygen
```

Configure `~/.ssh/config` file

```
Host *
    StrictHostKeyChecking no
``` 

```
chmod 400 ~/.ssh/config
```

Register your redhat server

	subscription-manager register --username=<user_name> --password=<password>
	subscription-manager refresh
	subscription-manager list --available --matches '*OpenShift*'
	subscription-manager attach --pool=<pool_id>
	subscription-manager repos --disable="*"

	subscription-manager repos \
    	--enable="rhel-7-server-rpms" \
    	--enable="rhel-7-server-extras-rpms" \
    	--enable="rhel-7-server-ose-3.11-rpms" \
    	--enable="rhel-7-server-ansible-2.6-rpms"

Install pre-req packages

	yum install wget git net-tools bind-utils yum-utils iptables-services bridge-utils bash-completion kexec-tools sos psacct
	yum -y update
	reboot

Install ansible

	yum install openshift-ansible
	reboot

