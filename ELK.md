## Some example to get index data

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
