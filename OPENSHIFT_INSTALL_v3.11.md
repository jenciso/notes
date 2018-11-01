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

## Prepare bastion server

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


Distribute the key in all hosts

```
for host in intelbras-ocp311.enciso.site \
            intelbras-ocp321.enciso.site \
            intelbras-ocp322.enciso.site \
            intelbras-ocp323.enciso.site \
            intelbras-ocp341.enciso.site \
            intelbras-ocp342.enciso.site \
            intelbras-ocp343.enciso.site \
            intelbras-ocp351.enciso.site \
            intelbras-ocp352.enciso.site \
            intelbras-ocp353.enciso.site; \
            do ssh-copy-id -i ~/.ssh/id_rsa.pub $host; \
            done
```

Setup the /etc/ansible/ansible.cfg

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

Create a temporal inventory

	mkdir /etc/ansible/inventory
	
and create another inventory file `/etc/ansible/inventory/inventory-preinstall` with this content:

```
[lb]
intelbras-ocp311.enciso.site

[master]
intelbras-ocp321.enciso.site
intelbras-ocp322.enciso.site
intelbras-ocp323.enciso.site

[node_infra]
intelbras-ocp341.enciso.site
intelbras-ocp342.enciso.site
intelbras-ocp343.enciso.site

[node_app]
intelbras-ocp351.enciso.site
intelbras-ocp352.enciso.site
intelbras-ocp353.enciso.site
```

## Prepare the others hosts from bastion server

Using `ansible -m shell`


	ansible -m shell -a "hostname" -i inventory-preinstall all

	ansible -m shell -a "subscription-manager register --username=nsconsultores.juan --password=xxxx" -i /etc/ansible/inventory/inventory-preinstall all
	ansible -m shell -a 'subscription-manager refresh' -i /etc/ansible/inventory/inventory-preinstall all
	ansible -m shell -a "subscription-manager attach --pool=8a85f99a65c8c8a10166b25a19a303f3" -i /etc/ansible/inventory/inventory-preinstall all
	ansible -m shell -a 'subscription-manager repos --disable="*"' -i /etc/ansible/inventory/inventory-preinstall all
	ansible -m shell -a 'subscription-manager repos --enable="rhel-7-server-rpms" --enable="rhel-7-server-extras-rpms" --enable="rhel-7-server-ose-3.11-rpms" --enable="rhel-7-server-ansible-2.6-rpms"' -i /etc/ansible/inventory/inventory-preinstall all
	

Install prereq packages

	ansible -m shell -a "yum -y install wget git net-tools bind-utils yum-utils \
	iptables-services bridge-utils bash-completion kexec-tools sos psacct" \
	-i /etc/ansible/inventory/inventory-preinstall all

	ansible -m shell -a "yum -y update" -i /etc/ansible/inventory/inventory-preinstall all
	ansible -m command -a "reboot" -i /etc/ansible/inventory/inventory-preinstall all


Install ansible

	ansible -m shell -a "yum -y install openshift-ansible" -i /etc/ansible/inventory/inventory-preinstall all
	ansible -m command -a "reboot" -i /etc/ansible/inventory/inventory-preinstall all


### Installing Docker 

	ansible -m shell -a "yum -y install docker-1.13.1" -i /etc/ansible/inventory/inventory-preinstall all
	ansible -m shell -a "rpm -V docker-1.13.1" -i /etc/ansible/inventory/inventory-preinstall all
	ansible -m shell -a "docker version" -i /etc/ansible/inventory/inventory-preinstall all


### Installing GlusterFS

	ansible -m shell -a "yum -y install glusterfs-fuse" -i /etc/ansible/inventory/inventory-preinstall all
	ansible -m shell -a "subscription-manager repos --enable=rh-gluster-3-client-for-rhel-7-server-rpms" -i /etc/ansible/inventory/inventory-preinstall all
	ansible -m shell -a "yum -y update glusterfs-fuse" -i /etc/ansible/inventory/inventory-preinstall all


### Setting docker storage setup

Create a file `docker-storage-setup` with the following content:

```
DEVS="/dev/vdb"
VG="docker-vol"
DATA_SIZE="100%VG"
STORAGE_DRIVER=overlay2
CONTAINER_ROOT_LV_NAME="dockerlv"
CONTAINER_ROOT_LV_MOUNT_PATH="/var/lib/docker"
CONTAINER_ROOT_LV_SIZE=100%FREE
```
and copy it in all nodes

```
ansible -m copy -a "src=docker-storage-setup dest=/etc/sysconfig/" -i /etc/ansible/inventory/inventory-preinstall all
```

if you have another partition, you have to delete all them

	dd if=/dev/zero of=/dev/vdb bs=512 count=1 conv=notrunc

Another option is

	wipefs -a /dev/vdb

Apply the configuration to docker storage

	ansible -m shell -a "docker-storage-setup" -i /etc/ansible/inventory/inventory-preinstall all 


Enable docker

	ansible -m shell -a "systemctl enable docker" -i /etc/ansible/inventory/inventory-preinstall all
	ansible -m shell -a "systemctl start docker" -i /etc/ansible/inventory/inventory-preinstall all
	ansible -m shell -a "systemctl is-active docker" -i /etc/ansible/inventory/inventory-preinstall all


Config your docker credential in all the hosts

	ansible -m shell -a "docker login registry.redhat.io -u nsconsultores.juan -p xxxxx" -i /etc/ansible/inventory/inventory-preinstall all
	ansible -m shell -a "docker login registry.access.redhat.com -u nsconsultores.juan -p xxxxx" -i /etc/ansible/inventory/inventory-preinstall all
	ansible -m shell -a "mkdir /var/lib/origin/; cp -pr ~/.docker /var/lib/origin/" -i /root/inventory.single nodes
	ansible -m shell -a "systemctl restart docker" -i /root/inventory.single nodes


Create ssl-certificates via sslfree.com

	mkdir -p /opt/ssl-certs/certificates-openshift.intelbras.com.br
	mkdir -p /opt/ssl-certs/certificates-apps.intelbras.com.br

Unzip

	cd /opt/ssl-certs/certificates-openshift.intelbras.com.br
	unzip sslforfree.zip
        mkdir -p /opt/ssl-certs/certificates-apps.intelbras.com.br
        unzip sslforfree.zip

### Deploy install

	cd /usr/share/ansible/openshift-ansible
	ansible-playbook -i /root/inventory.redhat playbooks/prerequisites.yml -vv
	ansible-playbook -i /root/inventory.redhat playbooks/deploy_cluster.yml -vv


### Post installation

* Create haproxy config for router

Into `/etc/haproxy/haproxy.cfg` add the following block

```
frontend main_router_80
    bind *:80
    default_backend router80
    mode tcp
    option tcplog

backend router80
    balance source
    mode tcp
    server     infra1  sjo-opshift-infra-01.intelbras.local:80 check
    server     infra2  soo-opshift-infra-02.intelbras.local:80 check
    server     infra3  soo-opshift-infra-03.intelbras.local:80 check

frontend main_router_443
    bind *:443
    default_backend router443
    mode tcp
    option tcplog

backend router443
    balance source
    mode tcp
    server     infra1  sjo-opshift-infra-01.intelbras.local:443 check
    server     infra2  soo-opshift-infra-02.intelbras.local:443 check
    server     infra3  soo-opshift-infra-03.intelbras.local:443 check
```

Restart haproxy

```
systemctl restart haproxy
```

Create iptables rules

```
iptables -I INPUT -p tcp -m tcp --dport 80 -j ACCEPT 
iptables -I INPUT -p tcp -m tcp --dport 443 -j ACCEPT 
service iptables save
```

* Given access as Admin

```
oc create clusterrolebinding admin-role-binding --clusterrole=cluster-admin --user=ns.juan
oc adm policy add-cluster-role-to-user cluster-admin ma053293
```
