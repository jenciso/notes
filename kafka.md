### Useful commands
http://pavelmakhov.com/2017/03/useful-kafka-commands

### Kafka Monitor
https://github.com/linkedin/kafka-monitor

### Modify timeouts for slow networks

Change your `kafka-properties`
```
zookeeper.connection.timeout.ms=60000
zookeeper.session.timeout.ms=60000
zookeeper.sync.time.ms=20000
```

### Add more partitions

```
 ./kafka-topics.sh --alter --zookeeper localhost:2181 --topic metrics --partitions 6
 ```
 
### Change retention time

```
 ./kafka-topics.sh --alter --zookeeper localhost:2181 --topic metrics --config retention.ms=86400000
```

### kafkacat to order by number of partitions

```
✔ 11:20:37 [lat3480i7] ~ $ kafkacat -L -b 10.64.12.41 | grep topic | sort -nrk 4 | head | column -t
topic  "cobranca.titulo-cip.instrucao"                               with  720  partitions:
topic  "error.cobranca.titulo.instrucoes.p.v3.titulo-us-instrucoes"  with  600  partitions:
topic  "error.cobranca.titulo.instrucoes.p.v3.titulo-protesto"       with  600  partitions:
topic  "error.cobranca.titulo-cip.instrucao.v4.cob-jd-us"            with  600  partitions:
topic  "cobranca.titulo.instrucoes.p.v3"                             with  600  partitions:
topic  "cobranca.titulo-cip.instrucao.v4"                            with  600  partitions:
topic  "error.domicilio.arranjo.agenda.equalizacao"                  with  84   partitions:
topic  "domicilio.arranjo.agenda.lancamento"                         with  84   partitions:
topic  "domicilio.arranjo.agenda.equalizacao"                        with  84   partitions:
topic  "error.domicilio.lancamento.debito.v2"                        with  60   partitions:
✔ 11:20:41 [lat3480i7] ~ $ 
```
