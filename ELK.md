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
curl -XPUT 'http://elasticsearch-hlg.e-unicred.com.br:9200/.kibana/_settings?pretty' -H 'Content-Type: application/json' -d '{ "index.priority": 99 }'
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
