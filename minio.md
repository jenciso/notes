## Create minio service using helm into kubernetes cluster

```
helm install --set serviceType=NodePort --name storage-demo --namespace=default stable/minio
```

## Delete 

```
helm delete --purge storage-demo
``` 

## set keys
```
helm install --set accessKey=myaccesskey,secretKey=mysecretkey stable/minio
``` 

### Upgrade

```
helm update
helm upgrade --force storage-realiza stable/minio 
``` 

## Kubernetes
https://docs.minio.io/docs/deploy-minio-on-kubernetes
