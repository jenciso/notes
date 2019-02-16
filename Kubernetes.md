## Forcing rolling restart

```sh
kubectl set env --all deployment --env="LAST_MANUAL_RESTART=$(date +%s)" --namespace=...
kubectl set env --all daemonset --env="LAST_MANUAL_RESTART=$(date +%s)" --namespace=...
```
Ex
```sh
kubectl set env deployment nginx-demo --env="LAST_MANUAL_RESTART=$(date +%s)" --namespace=default
```
https://github.com/kubernetes/kubernetes/issues/13488

## Rolling update

```sh
kubectl rolling-update myapp --image=us.gcr.io/project-107012/myapp:5c3dda6b --image-pull-policy Always
``` 

## Installing Prometheus Operator

1. Install Helm

https://github.com/kubernetes/helm

2. Setup a clusterrolebinding for helm

```
cat <<EOF | kubectl create -f -
apiVersion: v1
kind: ServiceAccount
metadata:
name: tiller
namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
name: tiller
roleRef:
apiGroup: rbac.authorization.k8s.io
kind: ClusterRole
name: cluster-admin
subjects:
- kind: ServiceAccount
name: tiller
namespace: kube-system
EOF
```
3. Create tiller account

```
helm init --service-account tiller
```

4. Install it

```
helm repo add coreos https://s3-eu-west-1.amazonaws.com/coreos-charts/stable/
helm install coreos/prometheus-operator --name prometheus-operator --namespace monitoring
helm install coreos/kube-prometheus --name kube-prometheus --set global.rbacEnable=true --namespace monitoring
```

## List the pods order by memory consume

```
kubectl top pod --all-namespaces | sort -nrk4 | head
```

## Show LivenessProbe

Ex. 

	kubectl get deployment -n cadastro cooperativa-us -o json | jq \
	-r .spec.template.spec.containers[].livenessProbe 

## Copying files using kubectl 

From container to local directory 

	kubectl cp realiza/storage-realiza-minio-cf59b68d8-sr6vw:/export \
	/backup/storage-realiza-minio-cf59b68d8-sr6vw_20180808

From local directory to container

	kubectl cp public realiza/storage-realiza-minio-66449995b5-q2qt7:/export
	
## BusyBox 

	kubectl run -it busybox --image=radial/busyboxplus:curl  


## List pods sort by nodeName

	kubectl get pods -o wide --sort-by="{.spec.nodeName}"

## Get pods sort by restart count 

	kubectl get pods --sort-by="{.status.containerStatuses[:1].restartCount}" --all-namespaces

## View Node CPU request

	kube describe nodes | grep -A 2 -e "^\\s*CPU Requests"

## Force delete pod 
```
kubectl delete pod --grace-period=0 --force --namespace <NAMESPACE> <PODNAME>
```

### Delete pods with sleep
``` 
kubectl get pods --all-namespaces -o wide | grep <node_name> | awk '{ system("kubectl delete pod "$2" -n "$1" ; sleep 30") }'
``` 

## Add certificates SSL in ingress
```
kubectl create secret generic tls-dhparam --from-file=dhparam.pem -n kube-system
kubectl create secret tls tls-certificate-wildcard-unicred --key e-unicred.com.br.key --cert e-unicred.com.br.crt -n kube-system
```

## Creating persistent volumes

```
export size=2Gi; for i in {1..2} ; 
```
```
cat <<EOF | kubectl create -f - 
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
 name: gluster_$PVSIZE_$RANDOM
 annotations:
   volume.beta.kubernetes.io/storage-class: gluster-heketi
spec:
 accessModes:
  - ReadWriteOnce
 resources:
   requests:
     storage: $PVSIZE
EOF
; done
```

## helm upgrade tiller

```
helm init --upgrade
```

## Kubectl man page 
https://kubernetes.io/docs/user-guide/kubectl/

## Order by AGE
```
kubectl get pod --all-namespaces --sort-by=.status.startTime 
```

## Get nodePort of all svc
```
kubectl get svc --all-namespaces -ao jsonpath='{range .items[*]}{@.metadata.name}{"\t"}{@.metadata.namespace}{"\t"}{@.spec.ports[0].nodePort}{"\n"}{end}'
```

## Get all images for each pod
```
kubectl get pods -ao jsonpath='{range .items[*]}{@.metadata.name}{" "}{@.spec.containers[*].image}{"\n"}{end}
```

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

## Encode base 64 User:Pass for basic authentication via curl

```
jenciso@dellPE:~$ echo -n juan.enciso:33d48n22312d | base64                                                                             
anVhbi5lbmNpc286MzNkNDhuYzRkM2Q=
jenciso@dellPE:~$ curl -s -k https://apik8s-dev.iplanet.work/version -H 'Authorization: Basic anVhbi5lbmNpc286MzNkNDhuYzRkM2Q='
{
  "major": "1",
  "minor": "4",
  "gitVersion": "v1.4.7",
  "gitCommit": "92b4f971662de9d8770f8dcd2ee01ec226a6f6c0",
  "gitTreeState": "clean",
  "buildDate": "2016-12-10T04:43:42Z",
  "goVersion": "go1.6.3",
  "compiler": "gc",
  "platform": "linux/amd64"
* Connection #0 to host apik8s-dev.iplanet.work left intact
}jenciso@dellPE:~$  
```

## Patch deployment

	kubectl patch deployment web -p \
  	"{\"spec\":{\"template\":{\"metadata\":{\"labels\":{\"date\":\"`date +'%s'`\"}}}}}"
