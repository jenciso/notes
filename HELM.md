## HELM rollback

```
helm rollback canais-cache 1  --recreate-pods
```

## Create REDIS service via HELM

```
helm install --name cache-canais --namespace=canais --set password=yY9zvhYf7C --set master.persistence.enabled=false --set metrics.enabled=true stable/redis
```

## Using Helm secret

https://developer.epages.com/blog/tech-stories/kubernetes-deployments-with-helm-secrets/


## Override FullName

```
helm install --name demoapp mychart-0.1.0.tgz --set service.type=NodePort --set fullnameOverride=demoapp
```
