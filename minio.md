## Create minio service using helm into kubernetes cluster

```
helm install --set serviceType=NodePort --name storage-realiza --namespace=realiza  stable/minio
```
