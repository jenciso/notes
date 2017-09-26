## Kubectl man page 
https://kubernetes.io/docs/user-guide/kubectl/

## Running busybox
```
kubectl run -i -t busybox --image=busybox --restart=Never
kubectl delete pod busybox
``` 

## Another busybox 
```
kubectl run curl-demo --image=radial/busyboxplus:curl -i --tty --rm
```

## Running nginx with replicas
```
kubectl run nginx --image=nginx --replicas=2
```

## Scale deployment
```
kubectl scale deployment kubernetes-dashboard -n kube-system --replicas=3
```

## When generate new certificates, you need to delete all secrtes to recreate it
Ex.
```
kubectl delete secrets --all -n default
kubectl delete secrets --all -n kube-system
kubectl delete secrets --all -n kube-public
```
## Delete all pods with crashloopBackoff
```
kubectl get pods --all-namespaces -o wide | grep CrashLoopBackOff | awk '{ system("kubectl delete pod "$2" -n "$1) }'
```
