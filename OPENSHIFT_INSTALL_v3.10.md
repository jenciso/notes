### MASTER PROVISION

Prepare the disks template

OS disk:

	SO: 40GB

Aditional disks 

	OCP_DOCKER 40GB /dev/xvdb1 (LVM 8e) 
	OCP_VAR	   30GB /dev/xvdc 
	OCP_ETCD   50GB /dev/xvde


For docker partition, only create the LVM /dev/xvdb1 using type `8e` 

	fdisk /dev/xvdb


* Create the file

```
[root@tpl-rh75 ~]# cat /etc/sysconfig/docker-storage-setup 
DEVS="/dev/xvdb1"
VG="docker-vol"
DATA_SIZE="95%VG"
STORAGE_DRIVER=overlay2
CONTAINER_ROOT_LV_NAME="dockerlv"
CONTAINER_ROOT_LV_MOUNT_PATH="/var/lib/docker"
``` 

For other partitions:

* List the disk block

	lsblk 

* Look at for the uuid disk 

	ls -l /dev/disk/by-uuid/
	
* create directories

	mkdir -p /var/lib/origin/openshift.local.volumes
	mkdir -p /var/lib/etcd

* Mount using UUID, and use `gquota` in option for Openshift var partition. Put the examples lines in  /etc/fstab


	UUID=a796c793-e8e3-4288-8267-bb2de8c5ff58 /var/lib/origin/openshift.local.volumes  xfs   gquota 0 0
	UUID=52fe3fd8-7e82-44b4-b26d-a61bac55b13a /var/lib/etcd  xfs   defaults 0 0

* Mount them:

	mount -a	


## NODE TEMPLATE

To do the sames steps, only you dont't have to do `mkdir -p /var/lib/etcd` and mount it 


## LB TEMPLATE

Same like a  node template


## DEPLOY BASTION

```
ssh-keygen -N '' -f ~/.ssh/id_rsa

```
Register


Steps:

	subscription-manager register
	subscription-manager refresh
	subscription-manager attach --auto
	subscription-manager list --available
	subscription-manager attach --pool=8a85f98b64f079fc01650fc1df043d71

Another short line command:

	subscription-manager register --username <username> --password <password> --auto-attach
	subscription-manager attach --pool=8a85f98b64f079fc01650fc1df043d71

Verify 
	subscription-manager list --available --all


Install 

```
subscription-manager repos \
    --enable="rhel-7-server-rpms" \
    --enable="rhel-7-server-extras-rpms" \
    --enable="rhel-7-server-ose-3.10-rpms" \
    --enable="rhel-7-fast-datapath-rpms" \
    --enable="rhel-7-server-ansible-2.4-rpms"
```

```
yum install openshift-ansible
``` 

Modify your ansible.cfg

```
[defaults]
forks = 20
host_key_checking = False
roles_path = roles/
gathering = smart
remote_user = root
private_key = ~/.ssh/id_rsa
fact_caching = jsonfile
fact_caching_connection = $HOME/ansible/facts
fact_caching_timeout = 600
log_path = $HOME/ansible.log
nocows = 1
callback_whitelist = profile_tasks

[ssh_connection]
ssh_args = -C -o ControlMaster=auto -o ControlPersist=900s -o GSSAPIAuthentication=no -o PreferredAuthentications=publickey
control_path = %(directory)s/%%h-%%r
pipelining = True
timeout = 10

[persistent_connection]
connect_timeout = 30
connect_retries = 30
connect_interval = 1
```


## CREATION INDIVIDUAL HOSTS

To modify the template


Copying key:

	ssh-copy-id 10.64.13.9


COMMAND TO MODIFY THE TEMPLATE

	ansible -m lineinfile -a "dest=/etc/sysconfig/network-scripts/ifcfg-eth0 regexp=^IPADDR\= line='IPADDR=10.64.13.131'" template
	ansible -m copy -a "content='dcbvm090lb211.e-unicred.com.br\n' dest=/etc/hostname" template


## INSTALL PREREQ  - ALL HOSTS
On host deployment (Bastion)

Create another inventory

`/etc/ansible/inventory/inventory-preinstall`


With the following content:

``` 
[lb]
dcbvm090lb211.e-unicred.com.br

[master]
dcbvm090lb221.e-unicred.com.br
dcbvm090lb222.e-unicred.com.br
dcbvm090lb223.e-unicred.com.br

[node_infra]
dcbvm090lb231.e-unicred.com.br
dcbvm090lb232.e-unicred.com.br
dcbvm090lb233.e-unicred.com.br

[node_app]
dcbvm090lb241.e-unicred.com.br
dcbvm090lb242.e-unicred.com.br
dcbvm090lb243.e-unicred.com.br

```

Then, execute:

``` 
ansible -m shell -a "subscription-manager register --username=juan.enciso@unicred.com.br --password=xxxxx" -i /etc/ansible/inventory/inventory-preinstall all
ansible -m shell -a 'subscription-manager refresh' -i /etc/ansible/inventory/inventory-preinstall all
ansible -m shell -a "subscription-manager attach --pool=8a85f98b64f079fc01650fc1df043d71" -i /etc/ansible/inventory/inventory-preinstall all
ansible -m shell -a 'subscription-manager repos --disable="*"' -i /etc/ansible/inventory/inventory-preinstall all
```

```
ansible -m shell -a 'subscription-manager repos \
--enable="rhel-7-server-rpms" \
--enable="rhel-7-server-extras-rpms" \
--enable="rhel-7-server-ose-3.10-rpms" \
--enable="rhel-7-fast-datapath-rpms" \
--enable="rhel-7-server-ansible-2.4-rpms"' \
-i /etc/ansible/inventory/inventory-preinstall all 
```

Install Base package:

```
ansible -m shell -a "yum -y install wget git net-tools bind-utils yum-utils \
iptables-services bridge-utils bash-completion kexec-tools sos psacct" \
-i /etc/ansible/inventory/inventory-preinstall all

ansible -m shell -a "yum -y update" -i /etc/ansible/inventory/inventory-preinstall all

ansible -m command -a "reboot" all
```
