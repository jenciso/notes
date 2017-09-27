## Get the list for unassigned shard with the reason for that

```
curl -s http://localhost:9200/_cat/shards?h=index,shard,prirep,state,unassigned.reason| grep UNASSIGNED
```
