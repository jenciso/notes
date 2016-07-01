# OpenShift Tutorial

## Installing Openshift Origin - Node Master

### Introduction

This tutorial assumes that you are using Centos 7.2 in all nodes.

### Pre-requisites

* Step 1: Install packages
```bash
yum -y install wget git net-tools bind-utils iptables-services bridge-utils bash-completion
yum -y update
```

* Step 2: Reboot 
```bash
systemctl reboot
```

* Step 3: Install epel repo
```
yum -y install epel-release
```

* Step 4: Disable the EPEL repository globally
```bash
sed -i -e "s/^enabled=1/enabled=0/" /etc/yum.repos.d/epel.repo
```

* Step 5: Install the packages for ansible
```
yum -y --enablerepo=epel install ansible pyOpenSSL
```

* Step 6: Clone Ansible
```bash
cd ~
git clone https://github.com/openshift/openshift-ansible
cd openshift-ansible
```

* Step 7: Install Docker
```bash
yum -y install docker
```

* Step 8: Edit the /etc/sysconfig/docker file and add --insecure-registry 172.30.0.0/16 to the OPTIONS parameter
```
OPTIONS='--selinux-enabled --insecure-registry 172.30.0.0/16'
```

### Configuring Docker Storage

* Step 9: Attach a new disk in your vagrant or VM management system. For example in vagrant enviromment add the following lines:
```
second_disk = './second_disk_file.vdi'

  config.vm.provider "virtualbox" do |vb|
    unless File.exist?(second_disk)
     vb.customize ['createhd', '--filename', second_disk, '--size', 500 * 1024]
    end
     vb.customize ['storageattach', :id, '--storagectl', 'IDE Controller', '--port', 1, '--device', 0, '--type', 'hdd', '--medium', second_disk]
  end
```

* Step 10: Assuming the name for new disk in master-node /dev/sdb. Configure your docker storage
```bash
[root@tst-osf-master01-poa ~]# cat <<EOF > /etc/sysconfig/docker-storage-setup
> DEVS=/dev/sdb
> VG=docker-vg
> EOF
[root@tst-osf-master01-poa ~]# 
```

* Step 11: Run docker-storage-setup and verify config
```bash
[root@tst-osf-master01-poa ~]# docker-storage-setup 
[root@tst-osf-master01-poa ~]# cat /etc/sysconfig/docker-storage
DOCKER_STORAGE_OPTIONS="--storage-driver devicemapper --storage-opt dm.fs=xfs --storage-opt dm.thinpooldev=/dev/mapper/docker--vg-docker--pool --storage-opt dm.use_deferred_removal=true --storage-opt dm.use_deferred_deletion=true "
[root@tst-osf-master01-poa ~]#
```

* Step 12: Verify if docker is active and start it
```bash
[root@tst-osf-master01-poa ~]# systemctl is-active docker
unknown
[root@tst-osf-master01-poa ~]# systemctl enable docker
Created symlink from /etc/systemd/system/multi-user.target.wants/docker.service to /usr/lib/systemd/system/docker.service.
[root@tst-osf-master01-poa ~]# systemctl start docker
[root@tst-osf-master01-poa ~]# systemctl is-active docker
active
[root@tst-osf-master01-poa ~]# 
``` 

* Step 13: Limit the container logs, modify the OPTIONS line in docker config file
```bash
[root@tst-osf-master01-poa ~]# vi /etc/sysconfig/docker 
[root@tst-osf-master01-poa ~]# cat  /etc/sysconfig/docker | grep OPTIONS
OPTIONS='--selinux-enabled --insecure-registry 172.30.0.0/16 --log-opt max-size=1M --log-opt max-file=3'
[root@tst-osf-master01-poa ~]# systemctl restart docker
```

