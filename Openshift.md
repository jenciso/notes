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
cat <<EOF > /etc/sysconfig/docker-storage-setup
DEVS=/dev/sdb
VG=docker-vg
EOF
```

* Step 11: Run docker-storage-setup and verify config
```bash
docker-storage-setup 
```
Ex:
```
[root@tst-osf-node01-poa ~]# docker-storage-setup
Checking that no-one is using this disk right now ...
OK

Disk /dev/sdb: 26108 cylinders, 255 heads, 63 sectors/track
sfdisk:  /dev/sdb: unrecognized partition table type

Old situation:
sfdisk: No partitions found

New situation:
Units: sectors of 512 bytes, counting from 0

   Device Boot    Start       End   #sectors  Id  System
/dev/sdb1          2048 419430399  419428352  8e  Linux LVM
/dev/sdb2             0         -          0   0  Empty
/dev/sdb3             0         -          0   0  Empty
/dev/sdb4             0         -          0   0  Empty
Warning: partition 1 does not end at a cylinder boundary
Warning: no primary partition is marked bootable (active)
This does not matter for LILO, but the DOS MBR will not boot this disk.
Successfully wrote the new partition table

Re-reading the partition table ...

If you created or changed a DOS partition, /dev/foo7, say, then use dd(1)
to zero the first 512 bytes:  dd if=/dev/zero of=/dev/foo7 bs=512 count=1
(See fdisk(8).)
INFO: Device node /dev/sdb1 exists.
  Physical volume "/dev/sdb1" successfully created
  Volume group "docker-vg" successfully created
  Rounding up size to full physical extent 208.00 MiB
  Logical volume "docker-poolmeta" created.
  Logical volume "docker-pool" created.
  WARNING: Converting logical volume docker-vg/docker-pool and docker-vg/docker-poolmeta to pool's data and metadata volumes.
  THIS WILL DESTROY CONTENT OF LOGICAL VOLUME (filesystem etc.)
  Converted docker-vg/docker-pool to thin pool.
  Logical volume "docker-pool" changed.
[root@tst-osf-node01-poa ~]# 
```
Then the file /etc/sysconfig/docker-storage will show you
 
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

