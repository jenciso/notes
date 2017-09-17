## Basic commands

* status
version 2:
```sh
[root@dcbvm090dv201 ~]# etcdctl cluster-health
member 1b2a364da68a475e is healthy: got healthy result from http://10.64.14.26:2379
member 2ec976d882e9fdd8 is healthy: got healthy result from http://10.64.14.24:2379
member b1697ac406e9c55d is healthy: got healthy result from http://10.64.14.25:2379
cluster is healthy
[root@dcbvm090dv201 ~]# 
````

version 3:
```sh
[root@dcbvm090dv831 ~]# ETCDCTL_API=3 etcdctl -w table endpoint status
+----------------+------------------+---------+---------+-----------+-----------+------------+
|    ENDPOINT    |        ID        | VERSION | DB SIZE | IS LEADER | RAFT TERM | RAFT INDEX |
+----------------+------------------+---------+---------+-----------+-----------+------------+
| 127.0.0.1:2379 | 68d909f6e04f2193 |   3.2.7 |  2.4 MB |      true |        19 |     272364 |
+----------------+------------------+---------+---------+-----------+-----------+------------+
[root@dcbvm090dv831 ~]# 
```
* List all keys
version 2:
```sh
 etcdctl ls -r
``` 
version 3:
```sh
ETCDCTL_API=3 ./etcdctl get / --prefix --keys-only
```
* List specific key

version 2:  
```sh
etcdctl ls registry
```
version 3:
```
ETCDCTL_API=3 ./etcdctl get /registry --prefix --keys-only
``` 

* From another host using ssl certificates

```sh
[root@dcbvm090dv821 bin]# ETCDCTL_API=3 etcdctl --cert /var/lib/kubernetes/kubernetes.pem --key /var/lib/kubernetes/kubernetes-key.pem --cacert /var/lib/kubernetes/ca.pem --endpoints "https://10.64.14.74:2379,https://10.64.14.75:2379,https://10.64.14.76:2379" -w table endpoint status 
+--------------------------+------------------+---------+---------+-----------+-----------+------------+
|         ENDPOINT         |        ID        | VERSION | DB SIZE | IS LEADER | RAFT TERM | RAFT INDEX |
+--------------------------+------------------+---------+---------+-----------+-----------+------------+
| https://10.64.14.74:2379 | 68d909f6e04f2193 |   3.2.7 |  2.4 MB |      true |        19 |     273444 |
| https://10.64.14.75:2379 | 43b6744b10f501ea |   3.2.7 |  2.4 MB |     false |        19 |     273444 |
| https://10.64.14.76:2379 | c4de0e0948366a6d |   3.2.7 |  2.4 MB |     false |        19 |     273444 |
+--------------------------+------------------+---------+---------+-----------+-----------+------------+
[root@dcbvm090dv821 bin]#
```
