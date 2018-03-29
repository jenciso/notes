## Create minio service using helm into kubernetes cluster

```
helm install --set serviceType=NodePort --name storage-demo --namespace=default stable/minio
```

## Delete 

```
helm delete --purge storage-demo
``` 
