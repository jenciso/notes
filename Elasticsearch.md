## Get the list for unassigned shard with the reason for that

```
curl -s http://localhost:9200/_cat/shards?h=index,shard,prirep,state,unassigned.reason| grep UNASSIGNED
```
## Get the size for each node
```
jenciso@TEC1971:~$ curl -s http://elasticsearch-prd.e-unicred.com.br:9200/_cat/allocation?v
shards disk.indices disk.used disk.avail disk.total disk.percent host        ip          node
  2227      261.7gb   262.2gb     37.5gb    299.8gb           87 10.64.12.58 10.64.12.58 dcavm090pr722
  2129      261.4gb   261.9gb     37.9gb    299.8gb           87 10.64.12.57 10.64.12.57 dcbvm090pr721
  1691      147.8gb     152gb    147.8gb    299.8gb           50 10.64.12.59 10.64.12.59 dcbvm090pr723
    63                                                                                   UNASSIGNED
jenciso@TEC1971:~$ 
```
