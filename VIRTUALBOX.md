## NAT network - Comandos

UTC

```
VBoxManage modifyvm $VM --rtcuseutc on
```

Criando NAT Network

```
VBoxManage natnetwork add --netname natnet1 --network "10.0.5.0/24" --enable --dhcp on
```

Apagando uma NAT network
```
VBoxManage natnetwork remove --netname natnet1
```

Desabilitando a opcao de dhcp
```
VBoxManage natnetwork modify --netname natnet1 --dhcp off
```

Inicializar a rede
```
VBoxManage natnetwork start --netname natnet1
```

Criando um port-forward
```
VBoxManage natnetwork modify \
  --netname natnet1 --port-forward-4 "centos7_ssh:tcp:[]:2211:[10.0.5.11]:22"
```

Verificando a rede
```
VBoxManage list natnetworks
```

### Source

https://www.virtualbox.org/manual/ch06.html#network_nat_service

## Hostonly network

```
VBoxManage hostonlyif create
```

