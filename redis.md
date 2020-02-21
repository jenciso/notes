
## Instalação

Exemplos: 

### Redis Simple (master only)

```
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install \
  --name cache-cobranca \
  --namespace cobranca \
  --set cluster.enabled=false \ 
  stable/redis
```

https://github.com/helm/charts/tree/master/stable/redis

### Redis ha (with Sentinel)

> A nova versão do stage/redis também suporta Sentinel

```shell
helm install \
  --name canais-cache \
  --namespace canais \
  --set tag=5.0.3-alpine \
  --set redisPassword=****** \
  --set auth=enabled \
  stable/redis-ha
```

https://github.com/helm/charts/tree/master/stable/redis-ha
