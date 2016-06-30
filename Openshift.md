# OpenShift Tutorial

## Installing Openshift Origin - Node Master

### Pre-requisites

* Step 1: Install packages
```
yum -y install wget git net-tools bind-utils iptables-services bridge-utils bash-completion
yum -y update
```

* Step 2: Reboot 
```
systemctl reboot
```

* Step 3: Install epel repo
```
yum -y install epel-release
```

* Step 4: Disable the EPEL repository globally
```
sed -i -e "s/^enabled=1/enabled=0/" /etc/yum.repos.d/epel.repo
```

* Step 5: Install the packages for ansible
```
yum -y --enablerepo=epel install ansible pyOpenSSL
```

* Step 6: Clone Ansible
```
cd ~
git clone https://github.com/openshift/openshift-ansible
cd openshift-ansible
```

* Step 7: Install Docker
```
yum -y install docker
```

* Step 8: Edit the /etc/sysconfig/docker file and add --insecure-registry 172.30.0.0/16 to the OPTIONS parameter
```
OPTIONS='--selinux-enabled --log-driver=journald --insecure-registry 172.30.0.0/16'
```

### Configuring Docker Storage

* Step 9: 
