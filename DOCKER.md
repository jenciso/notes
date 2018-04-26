## Stop and rm all containers
```
docker stop $(docker ps -a -q)
docker rm $(docker ps -a -q)
```

## Delete and purge some specific container images   
```
docker rmi $(docker images -q -f "dangling=true")
docker images | grep ^nexus | awk '{print $1":"$2}' | xargs docker rmi
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
