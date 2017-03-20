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
docker run -i -t --cap-add=SYS_PTRACE --security-opt=apparmor:unconfined fedora  /bin/bash 
```

