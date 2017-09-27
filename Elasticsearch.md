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

## Explain some details for disk allocation. Ex. 
```
curl -vs "http://localhost:9200/_cluster/allocation/explain?include_disk_info=true"
```
Ex: show deciders
```
....
            "node_attributes": {
                "ml.enabled": "true"
            },
            "node_decision": "throttled",
            "node_id": "VaLh52bTRXSB7xwRInsFLA",
            "node_name": "dcavm090pr722",
            "transport_address": "10.64.12.58:9300",
            "weight_ranking": 3
        },
        {
            "deciders": [
                {
                    "decider": "same_shard",
                    "decision": "NO",
                    "explanation": "the shard cannot be allocated to the same node on which a copy of the shard already exists [[filebeat-kube-audit-2017.09.27][2], node[BdDPz3hXTLeusB818taN1g], [P], s[STARTED], a[id=3EsL9hRySWyo6SXvmAFOfA]]"
                },
                {
                    "decider": "throttling",
                    "decision": "THROTTLE",
                    "explanation": "reached the limit of outgoing shard recoveries [2] on the node [BdDPz3hXTLeusB818taN1g] which holds the primary, cluster setting [cluster.routing.allocation.node_concurrent_outgoing_recoveries=2] (can also be set via [cluster.routing.allocation.node_concurrent_recoveries])"
                }
            ],
```            
      
## Set watermark

```
PUT _cluster/settings
{
  "transient": {
    "cluster.routing.allocation.disk.watermark.low": "90%",
    "cluster.routing.allocation.disk.watermark.high": "95%"
  }
}
```
> source https://www.elastic.co/guide/en/elasticsearch/reference/5.4/disk-allocator.html#disk-allocator

