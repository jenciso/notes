## Running busybox
```
kubectl run -i -t busybox --image=busybox --restart=Never
kubectl delete pod busybox
```` 
## Running nginx with replicas
```
kubectl run nginx --image=nginx --replicas=2
```
## When generate new certificates, you need to delete all secrtes to recreate it
Ex.
```
kubectl delete secrets --all -n default
kubectl delete secrets --all -n kube-system
kubectl delete secrets --all -n kube-public
```
