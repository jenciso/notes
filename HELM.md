## Create REDIS service via HELM

```
helm install --name cache-canais --namespace=canais --set password=yY9zvhYf7C --set master.persistence.enabled=false --set metrics.enabled=true stable/redis
```
