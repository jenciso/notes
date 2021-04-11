## Install Docker centos7 

```
curl -sL https://gist.githubusercontent.com/jenciso/1bfba6b9dca8bec6b8856374c8dfbf4f/raw/e629c5f23f36633c5d2887bb2959db04d5d6cfd4/install-docker-centos7.sh## Install Docker centos7  | bash - 

```

## Cgroups swap memory limits

```
Your kernel does not support swap limit capabilities or the cgroup is not mounted. Memory limited without swap.
```

* https://fabianlee.org/2020/01/18/docker-placing-limits-on-container-memory-using-cgroups/
* https://stackoverflow.com/questions/48685667/what-does-docker-mean-when-it-says-memory-limited-without-swap

## Capture logs from /dev/stderr in docker logs command

```sh
docker logs 2>&1 | grep hello
docker logs 2>/dev/null | grep hello
```

## Install docker and docker-compose - Ubuntu

Install docker daemon

```
sudo apt-get remove docker docker-engine docker.io containerd runc
sudo apt-get update

sudo apt-get install     apt-transport-https     ca-certificates     curl     gnupg-agent     software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo apt-key fingerprint 0EBFCD88

sudo add-apt-repository    "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"

sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io

sudo docker run hello-world
```

Setup your username to run docker without sudo

```shell
$ sudo usermod -a -G docker $(id -nu)
$ reboot
```

Install docker-compose

```
sudo curl -L "https://github.com/docker/compose/releases/download/1.25.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
docker-compose --version
``` 

## Docker Compose

If you've updated your code, you can do the build and reload in a single step with:
	
	docker-compose up -d --build

For most specific 

	docker-compose up -d --build worker

For restart container

	docker-compose restart worker

or for set the time to wait for stop before killing the container (in seconds):

	docker-compose restart -t 30 worker


## Stop and rm all containers
```
docker stop $(docker ps -a -q)
docker rm $(docker ps -a -q)
```

## Delete and purge some specific container images   
```
docker rmi $(docker images -q -f "dangling=true")
docker images | grep ^nexus | awk '{print $1":"$2}' | xargs docker rmi
docker ps -a | grep "pattern" | awk '{print $3}' | xargs docker rmi
```

## Installing docker-compose on Centos 7

```
sudo yum install -y python-pip python-devel
sudo pip install docker-compose
```
Verify
```
[root@pachacutec asciinema-server]# docker-compose --version
docker-compose version 1.16.1, build 6d1ac219
[root@pachacutec asciinema-server]#
```
## CLEAN all containers with state=Exited
```
docker rm $(docker ps -a -q -f status=exited)
````

## DELETE all containers
```
docker rm $(docker ps -a -q)
```

## DELETE all images
```
docker rmi $(docker images -q)
```

## Running privileged mode

```
docker run -i -t --privileged --rm fedora  /bin/bash
```

## Attach container

```
sudo docker attach 665b4a1e17b6
```

```
sudo docker exec -i -t 665b4a1e17b6 /bin/bash       #by id
sudo docker exec -i -t loving_heisenberg /bin/bash  #by Name
```

## Running container and delete it

```
sudo docker run -i -t --rm 665b4a1e17b6 /bin/bash
```

## Modify images

```
docker commit -a 'Add ps command' 3d27b94917a2 Myfedora
```

## Add capabilities to do ping in container

In the host machine search the capabilities for command
```
[root@tst-docker01-poa ~]# getcap $(which ping)
/bin/ping = cap_net_admin,cap_net_raw+p
[root@tst-docker01-poa ~]#
```

Afterwards, run the docker adding this capabilities in the command line
```
docker run -it --cap-add net_raw --cap-add net_admin fedora  /bin/bash
```

Permit strace in ubuntu
```
docker run -i -t --cap-add=SYS_PTRACE --security-opt=apparmor:unconfined fedora /bin/bash 
```

## Clean Storage Docker

Delete image without tag
```
docker images --no-trunc | grep '<none>' | awk '{ print $3 }' | xargs -r docker rmi
```
Delete exited containers
```
docker ps --filter status=dead --filter status=exited -aq | xargs docker rm -v
```

More info, [here](https://lebkowski.name/docker-volumes/)

### Docker order by size

```shell 
$ docker images --format '{{.Size}}\t{{.Repository}}\t{{.Tag}}\t{{.ID}}' | sed 's/ //' | sort -h -r | column -t
```

### Docker save and load

```
docker save gitlab-ce:latest > gitlab-ce-latest.tar
docker load -i gitlab-ce-latest.tar
```
