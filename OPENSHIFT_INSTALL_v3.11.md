## OPENSHIFT INSTALLATION v3.11

### Creating templates in kvm

Create a RedHat 7.6 template using 2 disks 

```
- 50GB to operating system (/dev/xvda)
- 40GB docker partition (/dev/xvdb)
```

the nodes infra should have a additional disk to glusterfs

```
- 200GB glusterfs (/dev/xvdc)
```

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

Create 10 VM's

```
1 x bastion 		(1GB 4vCPU's)
1 x load balancer 	(2GB 2vCPU's)
3 x node master 	(4GB 4vCPU's)
3 x node infra 		(16GB 4vCPU's)
2 x node apps 		(8GB 4vCPU's)
```

use vir-clone to create each server from template `openshift`
E.g.:  ocpvm090lb111 -> load balancer

``` 
virt-clone --connect qemu:///system --original-xml /var/lib/libvirt/images/openshift.xml \
--name ocpvm090lb111 --file /var/lib/libvirt/images/ocpvm090lb111.qcow2 \
--file /var/lib/libvirt/images/ocpvm090lb111-1.qcow2
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

	subscription-manager register --username=<user_name> --password=<password> --force
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

	yum install wget git net-tools bind-utils \
	yum-utils iptables-services bridge-utils \
	bash-completion kexec-tools sos psacct
	
	yum -y update	

Install ansible

	yum -y install openshift-ansible
	reboot


Distribute the key in all hosts

```
for host in ocpvm090lb111.enciso.site \
            ocpvm090lb121.enciso.site \
            ocpvm090lb122.enciso.site \
            ocpvm090lb123.enciso.site \
            ocpvm090lb141.enciso.site \
            ocpvm090lb142.enciso.site \
            ocpvm090lb143.enciso.site \
            ocpvm090lb151.enciso.site \
            ocpvm090lb152.enciso.site \
            ocpvm090lb153.enciso.site; \
            do ssh-copy-id -i ~/.ssh/id_rsa.pub $host; \
            done
```
	
Create an inventory file `inventory.pre` in your home directory ( ~/ ) with this content:

```
[lb]
ocpvm090lb111.ocp.enciso.site

[nodes]
ocpvm090lb121.ocp.enciso.site
ocpvm090lb122.ocp.enciso.site
ocpvm090lb123.ocp.enciso.site
ocpvm090lb131.ocp.enciso.site
ocpvm090lb132.ocp.enciso.site
ocpvm090lb133.ocp.enciso.site
ocpvm090lb141.ocp.enciso.site
ocpvm090lb142.ocp.enciso.site
```

## Prepare the others hosts from bastion server

Using `ansible -m shell`

	cd ~/
	ansible -m shell -a "hostname" -i inventory.pre all

	ansible -m shell -a "subscription-manager register --username=<my_user> --password=<my_pass>" -i inventory.pre all
	ansible -m shell -a 'subscription-manager refresh' -i inventory.pre all
	ansible -m shell -a "subscription-manager attach --pool=8a85f99b65c8c8f10166e56054bc3e47" -i inventory.pre all
	ansible -m shell -a 'subscription-manager repos --disable="*"' -i inventory.pre all


	ansible -m shell -a 'subscription-manager repos --enable="rhel-7-server-rpms" \
	--enable="rhel-7-server-extras-rpms" --enable="rhel-7-server-ose-3.11-rpms" \
	--enable="rhel-7-server-ansible-2.6-rpms"' -i inventory.pre all
	

Install prereq packages

	ansible -m shell -a "yum -y install wget git net-tools bind-utils yum-utils \
	iptables-services bridge-utils bash-completion kexec-tools sos psacct" \
	-i inventory.pre all
	
	ansible -m shell -a "yum -y update" -i inventory.pre all
	
Install ansible

	ansible -m shell -a "yum -y install openshift-ansible" -i inventory.pre all
	ansible -m command -a "reboot" -i inventory.pre all


### Installing Docker 

	ansible -m shell -a "yum -y install docker-1.13.1" -i inventory.pre all
	ansible -m shell -a "rpm -V docker-1.13.1" -i inventory.pre all
	ansible -m shell -a "docker version" -i inventory.pre all


### Installing GlusterFS

	ansible -m shell -a "yum -y install glusterfs-fuse" -i inventory.pre all
	ansible -m shell -a "subscription-manager repos --enable=rh-gluster-3-client-for-rhel-7-server-rpms" -i inventory.pre all
	ansible -m shell -a "yum -y update glusterfs-fuse" -i inventory.pre all


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
ansible -m copy -a "src=docker-storage-setup dest=/etc/sysconfig/" -i inventory.pre all
```

if you have another partition, you have to delete all them

	dd if=/dev/zero of=/dev/vdb bs=512 count=1 conv=notrunc

Another option is

	wipefs -a /dev/vdb

Apply the configuration to docker storage

	ansible -m shell -a "docker-storage-setup" -i inventory.pre all


Enable docker

	ansible -m shell -a "systemctl enable docker" -i inventory.pre all
	ansible -m shell -a "systemctl start docker" -i inventory.pre all
	ansible -m shell -a "systemctl is-active docker" -i inventory.pre all

Go to https://registry.redhat.io and login with your user/pass to create a service account

Ex:
```
sa_user = '12145255|ocpdemo'
sa_pass = 'eyJhbGciOiJSUzUxMiJ9.eyJzdWIiOiJhZTJlNjNkOGU4ZTM0N2E5YTkyNDI0MGI0Y2ZjYWNkYSJ9.L7t22AOMHDzz5W5Wu7NoPY-7eyljCdT9sCBvuVvHGxgO0nn1r4d3aigQwchMP1l93zLc9jb9twqCTfJVX5YxbETOMpkMBBnDJYjl-UF84TjjrPU5L8B4JcvjNj67B1a2iAz28_M1HCp78WwVsTylt8985NVKrX-kyJIDJy8Z90Ufadm9alsRAE-0hwbwNM8tiiy0gA57AAYn9-HwDkKpCOEWjTPg9OjXCxBpwopO7iGj5V5dnJNmZOcZPvrqfi9uj522lH05HY3EdLrFWHYIII9d4t5xNpcaqSIEsDkGQ9cNk0gOUSvvBNlRNK9EI1k5O8Guo9ev6L4tuYNMAvJxUzBFuAJejsvCVsyFG74D3lbLpZuoSbNi6slelwAJas9aZuB-itytiJzUZGN9KhkCXNfTd_3aEq-6OFUcahNbn4c2Wq1IHArzOboVlK9bVrSmsWj4ePQCfIL6nFmXXPzOXEOxgRiR2Rjth-aB2RieqEBabw5KvOAKDfHSQYND32X9VOwy2s0831T-oOIfLnW8iTqtS8dGP3ZzQF4LiWvvWt2kEVNhi_NzT2XD45-fi_qpvcfeHHtroIPX6HiHpi7hTGuenP741GDO2jz-_TS98J2C4D_TuACWiqrkPwvx-eeYQyxXcBOPtgAJxY_amHmP_9AYUgv6I3ZziVZqoL7Z4YU'
```


Config your docker credential in all the hosts

	ansible -m shell -a "docker login registry.redhat.io -u '<sa_user>'-p '<sa_pass>'" -i inventory.pre all
	ansible -m shell -a "docker login registry.access.redhat.com -u '<sa_user>'-p '<sa_pass>'" -i inventory.pre all
	ansible -m shell -a "docker login https://registry.redhat.io -u '<sa_user>'-p '<sa_pass>'" -i inventory.pre all
	ansible -m shell -a "docker login https://registry.access.redhat.com -u '<sa_user>'-p '<sa_pass>'" -i inventory.pre all
	ansible -m shell -a "mkdir /var/lib/origin/; cp -pr ~/.docker /var/lib/origin/" -i inventory.pre all
	ansible -m shell -a "systemctl restart docker" -i inventory.pre all


Create ssl-certificates via sslfree.com

	mkdir -p /opt/openshift/ssl-certs/apps
	mkdir -p /opt/openshift/ssl-certs/console

Unzip

	cd /opt/openshift/ssl-certs/apps
	unzip sslforfree.zip
	cd /opt/openshift/ssl-certs/console
	unzip sslforfree.zip

Convert to Unix type

	cd /opt/openshift/ssl-certs/console
	awk '{ sub("\r$", ""); print }' private.key > openshift_private.key
	awk '{ sub("\r$", ""); print }' certificate.crt > openshift_certificate.crt
	awk '{ sub("\r$", ""); print }' ca_bundle.crt > openshift_ca_bundle.crt

	cd /opt/openshift/ssl-certs/apps
	awk '{ sub("\r$", ""); print }' private.key > apps_private.key
	awk '{ sub("\r$", ""); print }' certificate.crt > apps_certificate.crt
	awk '{ sub("\r$", ""); print }' ca_bundle.crt > apps_ca_bundle.crt


### Install aditional packages 

	ansible -m shell -a "yum install -y dnsmasq chrony ntp" -i inventory.pre all

### Download some docker images
	
	ansible -m shell -a "docker pull registry.redhat.io/openshift3/ose-node:v3.11" -i inventory.pre nodes
	ansible -m shell -a "docker pull registry.redhat.io/openshift3/ose-pod:v3.11" -i inventory.pre nodes
	ansible -m shell -a "docker pull registry.redhat.io/openshift3/ose-node:v3.11.16" -i inventory.pre nodes
	ansible -m shell -a "docker pull registry.redhat.io/openshift3/ose-pod:v3.11.16" -i inventory.pre nodes
	ansible -m shell -a "docker pull registry.access.redhat.com/rhgs3/rhgs-volmanager-rhel7" -i inventory.pre nodes
	ansible -m shell -a "docker pull registry.access.redhat.com/rhgs3/rhgs-gluster-block-prov-rhel7" -i inventory.pre nodes

### Make a inventory.redhat 

Create a inventory file like [this](https://raw.githubusercontent.com/jenciso/notes/master/inventory.redhat)


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
    server     infra1  ocpvm090lb131.ocp.enciso.site:80 check
    server     infra2  ocpvm090lb132.ocp.enciso.site:80 check
    server     infra3  ocpvm090lb133.ocp.enciso.site:80 check

frontend main_router_443
    bind *:443
    default_backend router443
    mode tcp
    option tcplog

backend router443
    balance source
    mode tcp
    server     infra1  ocpvm090lb131.ocp.enciso.site:443 check
    server     infra2  ocpvm090lb132.ocp.enciso.site:443 check
    server     infra3  ocpvm090lb133.ocp.enciso.site:443 check
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
oc create clusterrolebinding juan.enciso-admin-role-binding --clusterrole=cluster-admin --user=juan.enciso
```
or 
```
oc adm policy add-cluster-role-to-user cluster-admin juan.enciso
```

## Final Result

```
INSTALLER STATUS ******************************************************************
Initialization               : Complete (0:02:03)
Health Check                 : Complete (0:01:31)
Node Bootstrap Preparation   : Complete (0:27:48)
etcd Install                 : Complete (0:03:48)
Load Balancer Install        : Complete (0:01:31)
Master Install               : Complete (0:09:05)
Master Additional Install    : Complete (0:02:19)
Node Join                    : Complete (0:01:43)
GlusterFS Install            : Complete (0:04:57)
Hosted Install               : Complete (0:01:35)
Cluster Monitoring Operator  : Complete (0:01:53)
Web Console Install          : Complete (0:00:38)
Console Install              : Complete (0:00:46)
Metrics Install              : Complete (0:02:37)
metrics-server Install       : Complete (0:01:08)
Logging Install              : Complete (0:05:13)
Prometheus Install           : Complete (0:01:43)
Service Catalog Install      : Complete (0:03:53)
Tuesday 13 November 2018  15:37:01 -0200 (0:00:00.066)       1:14:27.787 ****** 
=============================================================================== 
```

## Detail summary

```
Tuesday 13 November 2018  15:37:01 -0200 (0:00:00.066)       1:14:27.787 ****** 
=============================================================================== 
openshift_node : install needed rpm(s) --------------------------------------------------------------------------- 552.41s
/usr/share/ansible/openshift-ansible/roles/openshift_node/tasks/install_rpms.yml:2 ---------------------------------------
openshift_node : Install node, clients, and conntrack packages --------------------------------------------------- 238.90s
/usr/share/ansible/openshift-ansible/roles/openshift_node/tasks/install.yml:2 --------------------------------------------
openshift_node : Install iSCSI storage plugin dependencies ------------------------------------------------------- 197.19s
/usr/share/ansible/openshift-ansible/roles/openshift_node/tasks/storage_plugins/iscsi.yml:2 ------------------------------
openshift_node : Install Ceph storage plugin dependencies -------------------------------------------------------- 147.87s
/usr/share/ansible/openshift-ansible/roles/openshift_node/tasks/storage_plugins/ceph.yml:2 -------------------------------
openshift_node : Install NFS storage plugin dependencies --------------------------------------------------------- 131.87s
/usr/share/ansible/openshift-ansible/roles/openshift_node/tasks/storage_plugins/nfs.yml:2 --------------------------------
etcd : Install etcd ------ 130.13s
/usr/share/ansible/openshift-ansible/roles/etcd/tasks/certificates/fetch_server_certificates_from_ca.yml:2 ---------------
openshift_cluster_monitoring_operator : Wait for the ServiceMonitor CRD to be created ----------------------------- 94.82s
/usr/share/ansible/openshift-ansible/roles/openshift_cluster_monitoring_operator/tasks/install.yaml:115 ------------------
Run health checks (install) - EL ---------------------------------------------------------------------------------- 89.19s
/usr/share/ansible/openshift-ansible/playbooks/openshift-checks/private/install.yml:24 -----------------------------------
openshift_loadbalancer : Install haproxy -------------------------------------------------------------------------- 79.58s
/usr/share/ansible/openshift-ansible/roles/openshift_loadbalancer/tasks/main.yml:10 --------------------------------------
Approve node certificates when bootstrapping ---------------------------------------------------------------------- 79.17s
/usr/share/ansible/openshift-ansible/playbooks/openshift-node/private/join.yml:40 ----------------------------------------
openshift_storage_glusterfs : Wait for GlusterFS pods ------------------------------------------------------------- 75.33s
/usr/share/ansible/openshift-ansible/roles/openshift_storage_glusterfs/tasks/wait_for_pods.yml:2 -------------------------
cockpit : Install cockpit-ws -------------------------------------------------------------------------------------- 67.68s
/usr/share/ansible/openshift-ansible/roles/cockpit/tasks/main.yml:5 ------------------------------------------------------
openshift_control_plane : Wait for all control plane pods to become ready ----------------------------------------- 58.66s
/usr/share/ansible/openshift-ansible/roles/openshift_control_plane/tasks/main.yml:226 ------------------------------------
openshift_control_plane : Wait for control plane pods to appear --------------------------------------------------- 57.99s
/usr/share/ansible/openshift-ansible/roles/openshift_control_plane/tasks/main.yml:174 ------------------------------------
openshift_ca : Install the base package for admin tooling --------------------------------------------------------- 48.05s
/usr/share/ansible/openshift-ansible/roles/openshift_ca/tasks/main.yml:6 -------------------------------------------------
Run variable sanity checks  47.20s
/usr/share/ansible/openshift-ansible/playbooks/init/sanity_checks.yml:14 -------------------------------------------------
openshift_excluder : Install openshift excluder - yum ------------------------------------------------------------- 37.21s
/usr/share/ansible/openshift-ansible/roles/openshift_excluder/tasks/install.yml:34 ---------------------------------------
openshift_storage_glusterfs : Wait for deploy-heketi pod ---------------------------------------------------------- 32.46s
/usr/share/ansible/openshift-ansible/roles/openshift_storage_glusterfs/tasks/heketi_init_deploy.yml:32 -------------------
openshift_cli : Install clients ----------------------------------------------------------------------------------- 28.08s
/usr/share/ansible/openshift-ansible/roles/openshift_cli/tasks/main.yml:2 ------------------------------------------------
openshift_excluder : Install docker excluder - yum ---------------------------------------------------------------- 26.09s
/usr/share/ansible/openshift-ansible/roles/openshift_excluder/tasks/install.yml:9 ----------------------------------------
[root@ocpvm090lb100 openshift-ansible]# 
```

## Troubleshooting

### Cassandra

Verify if you have enought memory to run infra nodes. The recommend is to use 16GB RAM for each node. 

First, delete the replication controllers and pvc
``` 
oc delete rc -n openshift-infra hawkular-cassandra-1 
oc delete rc -n openshift-infra hawkular-metrics
oc delete rc -n openshift-infra heapster
oc get pvc -n openshift-infra 
oc delete pvc -n openshift-infra metrics-cassandra-1
```
and recreate all them using this command
```
ansible-playbook -i /root/inventory.redhat playbooks/openshift-metrics/config.yml -vv
```


## ADD NEW NODES

Modify the inventory file

```
[OSEv3:children]
new_nodes
...

[new_nodes]
ocpvm090lb143.ocp.enciso.site  ipv4addr=10.64.13.123 openshift_node_group_name='node-config-compute'
```

Download images 

	docker pull registry.redhat.io/openshift3/ose-node:v3.11.16
	docker pull registry.redhat.io/openshift3/ose-pod:v3.11.16


Source: [Add nodes](https://docs.openshift.com/container-platform/3.11/install_config/adding_hosts_to_existing_cluster.html)

