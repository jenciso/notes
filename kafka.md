
### Add more partitions

```
 ./kafka-topics.sh --alter --zookeeper localhost:2181 --topic metrics --partitions 6
 ```
 
#### Change retention time

```
 ./kafka-topics.sh --alter --zookeeper localhost:2181 --topic metrics --config retention.ms=86400000
```
