## Load template
```
docker run --network=host docker.elastic.co/beats/filebeat:6.2.3 setup --template -E output.logstash.enabled=false -E 'setup.template.pattern="filebeat-*"'  -E 'output.elasticsearch.hosts=["127.0.0.1:9200"]' -E 'setup.template.name="filebeat"'
```

## Some examples to get metrics for indices data

```
curl  'http://elasticsearch.iplanet.work:9200/_cat/indices?v&s=index
curl  'http://elasticsearch.iplanet.work:9200/_cat/indices?v&s=index'
curl  'http://elasticsearch.iplanet.work:9200/_cat/indices?v&h=i,tm&s=tm:desc'
curl  'http://elasticsearch.iplanet.work:9200/_cat/indices?h=i,tm&s=tm:desc' 
curl  'http://elasticsearch.iplanet.work:9200/_cat/indices?v&s=docs.count:desc'
curl  'http://elasticsearch.iplanet.work:9200/_cat/indices/filebeat-2017*?v&s=docs.count:desc'
curl  'http://elasticsearch.iplanet.work:9200/_cat/indices/filebeat-2017*?v&s=index:desc'
curl  'http://elasticsearch.iplanet.work:9200/_cat/indices/filebeat-*?v&s=index:desc'
```

Source:
https://www.elastic.co/guide/en/elasticsearch/reference/current/cat-indices.html

## Change priority to repair index

```
curl -XPUT 'http://elasticsearch-hlg.iplanet.work:9200/.kibana/_settings?pretty' -H 'Content-Type: application/json' -d '{ "index.priority": 99 }'
```
Check changes
```
[root@satelite ~]# 
[root@satelite ~]# curl -XGET 'http://elasticsearch-hlg.iplanet.work:9200/.kibana/_settings?pretty'                                                                {
  ".kibana" : {
    "settings" : {
      "index" : {
        "number_of_shards" : "5",
        "provided_name" : ".kibana",
        "creation_date" : "1490120564912",
        "priority" : "99",
        "number_of_replicas" : "1",
        "uuid" : "YaWrBWNnTSKMQDpqp_-R9w",
        "version" : {
          "created" : "5020299"
        }
      }
    }
  }
}
[root@satelite ~]# 
```
Source: https://www.elastic.co/guide/en/elasticsearch/reference/current/recovery-prioritization.html

## Check the thread_pool
```
curl http://elasticsearch:9200/_cat/thread_pool/bulk?v&h=id,name,active,rejected,completed,queue_size,queue,host

id                     name active rejected completed queue_size queue host
ADTJTATWQtyARVF3lwy9Rg bulk      0        0         0        400     0 10.64.12.55
FyxOEC5wSNG3Ycb7pI5pZA bulk      0        0         0        400     0 10.64.12.51
VaLh52bTRXSB7xwRInsFLA bulk      1        0    954647        400     0 10.64.12.58
m6q7hXg8Q1O2pbl9hoqoIQ bulk      0        0         0        400     0 10.64.12.52
hLETdmwER6CGmZpWgEnSwA bulk      0        0   1225911        400     0 10.64.12.57
2xuNTDnnQemz9Ro-bJhh1w bulk      0        0         0        400     0 10.64.12.54
BdDPz3hXTLeusB818taN1g bulk      0        0    911982        400     0 10.64.12.59
```
