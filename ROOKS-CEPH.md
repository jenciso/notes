## Installing rooks and cephs

Simple:

```
git clone --single-branch --branch release-1.2 https://github.com/rook/rook.git
cd cluster/examples/kubernetes/ceph
kubectl create -f common.yaml
kubectl create -f operator.yaml
kubectl create -f cluster-test.yaml
``` 

Complete:

```
git clone --single-branch --branch release-1.2 https://github.com/rook/rook.git
cd cluster/examples/kubernetes/ceph
kubectl create -f common.yaml
kubectl create -f operator.yaml
kubectl create -f cluster.yaml
```

cluster.yaml

```
apiVersion: ceph.rook.io/v1
kind: CephCluster
metadata:
  name: rook-ceph
  namespace: rook-ceph
spec:
  cephVersion:
    image: ceph/ceph:v14.2.6
    allowUnsupported: false
  dataDirHostPath: /var/lib/rook
  skipUpgradeChecks: false
  continueUpgradeAfterChecksEvenIfNotHealthy: false
  mon:
    count: 3
    allowMultiplePerNode: false
  dashboard:
    enabled: true
    ssl: true
  monitoring:
    enabled: false
    rulesNamespace: rook-ceph
  network:
    hostNetwork: false
  rbdMirroring:
    workers: 1
  crashCollector:
    disable: true
  annotations:
  resources:
  removeOSDsIfOutAndSafeToRemove: false
    useAllNodes: false
    useAllDevices: false
    config:
      storeType: bluestore
    nodes:
    - name: "dcbvm090lb347.e-unicred.com.br"
      devices:
      - name: "vdc"
    - name: "dcbvm090lb348.e-unicred.com.br"
      devices:
      - name: "vdc"
    - name: "dcbvm090lb349.e-unicred.com.br"
      devices:
      - name: "vdc"
```

```
kubectl apply -f cluster/examples/kubernetes/ceph/csi/rbd/storageclass.yaml
kubectl apply -f cluster/examples/kubernetes/ceph/filesystem.yaml
kubectl apply -f cluster/examples/kubernetes/ceph/csi/cephfs/storageclass.yaml
kubectl apply -f toolbox.yaml
```
Verify 

```
kubectl -n rook-ceph exec -it $(kubectl -n rook-ceph get pod -l "app=rook-ceph-tools" -o jsonpath='{.items[0].metadata.name}') bash

ceph status
ceph osd status
ceph df
rados df
```

Source:

https://rook.io/docs/rook/v1.2/ceph-quickstart.html


