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
```


operator.yaml

> `cat operator.yaml | grep -v ^\# | egrep -v " {1,}#" | grep -v "^$"`

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: rook-ceph-operator
  namespace: rook-ceph
  labels:
    operator: rook
    storage-backend: ceph
spec:
  selector:
    matchLabels:
      app: rook-ceph-operator
  replicas: 1
  template:
    metadata:
      labels:
        app: rook-ceph-operator
    spec:
      serviceAccountName: rook-ceph-system
      containers:
      - name: rook-ceph-operator
        image: rook/ceph:v1.2.3
        args: ["ceph", "operator"]
        volumeMounts:
        - mountPath: /var/lib/rook
          name: rook-config
        - mountPath: /etc/ceph
          name: default-config-dir
        env:
        - name: ROOK_CURRENT_NAMESPACE_ONLY
          value: "false"
        - name: AGENT_TOLERATION
          value: "NoSchedule"
        - name: AGENT_TOLERATION_KEY
          value: "node-role.kubernetes.io/infra"
        - name: AGENT_NODE_AFFINITY
          value: "node-role.kubernetes.io/infra=true"
        - name: DISCOVER_TOLERATION
          value: "NoSchedule"
        - name: DISCOVER_TOLERATION_KEY
          value: "node-role.kubernetes.io/infra"
        - name: DISCOVER_AGENT_NODE_AFFINITY
          value: "node-role.kubernetes.io/infra=true"
        - name: ROOK_ALLOW_MULTIPLE_FILESYSTEMS
          value: "false"
        - name: ROOK_LOG_LEVEL
          value: "INFO"
        - name: ROOK_CEPH_STATUS_CHECK_INTERVAL
          value: "60s"
        - name: ROOK_MON_HEALTHCHECK_INTERVAL
          value: "45s"
        - name: ROOK_MON_OUT_TIMEOUT
          value: "600s"
        - name: ROOK_DISCOVER_DEVICES_INTERVAL
          value: "60m"
        - name: ROOK_HOSTPATH_REQUIRES_PRIVILEGED
          value: "false"
        - name: ROOK_ENABLE_SELINUX_RELABELING
          value: "true"
        - name: ROOK_ENABLE_FSGROUP
          value: "true"
        - name: ROOK_DISABLE_DEVICE_HOTPLUG
          value: "false"
        - name: DISCOVER_DAEMON_UDEV_BLACKLIST
          value: "(?i)dm-[0-9]+,(?i)rbd[0-9]+,(?i)nbd[0-9]+"
        - name: ROOK_ENABLE_FLEX_DRIVER
          value: "false"
        - name: ROOK_ENABLE_DISCOVERY_DAEMON
          value: "true"
        - name: ROOK_CSI_ENABLE_CEPHFS
          value: "true"
        - name: ROOK_CSI_ENABLE_RBD
          value: "true"
        - name: ROOK_CSI_ENABLE_GRPC_METRICS
          value: "true"
        - name: CSI_ENABLE_SNAPSHOTTER
          value: "true"
        - name: CSI_FORCE_CEPHFS_KERNEL_CLIENT
          value: "true"
        - name: CSI_PROVISIONER_NODE_AFFINITY
          value: "node-role.kubernetes.io/infra=true"
        - name: CSI_PROVISIONER_TOLERATIONS
          value: |
            - effect: NoSchedule
              key: node-role.kubernetes.io/infra
              operator: Exists
        - name: CSI_PLUGIN_NODE_AFFINITY
          value: "node-role.kubernetes.io/infra=true"
        - name: CSI_PLUGIN_TOLERATIONS
          value: |
            - effect: NoSchedule
              key: node-role.kubernetes.io/infra
              operator: Exists
        - name: ROOK_UNREACHABLE_NODE_TOLERATION_SECONDS
          value: "5"
        - name: NODE_NAME
          valueFrom:
            fieldRef:
              fieldPath: spec.nodeName
        - name: POD_NAME
          valueFrom:
            fieldRef:
              fieldPath: metadata.name
        - name: POD_NAMESPACE
          valueFrom:
            fieldRef:
              fieldPath: metadata.namespace
      volumes:
      - name: rook-config
        emptyDir: {}
      - name: default-config-dir
        emptyDir: {}
```

cluster.yaml

> `cat cluster.yaml | grep -v ^\# | egrep -v " {1,}#" | grep -v "^$"`

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
    workers: 0
  crashCollector:
    disable: false
  placement:
    all:
      nodeAffinity:
        requiredDuringSchedulingIgnoredDuringExecution:
          nodeSelectorTerms:
          - matchExpressions:
            - key: "node-role.kubernetes.io/infra"
              operator: In
              values:
              - "true"
      podAffinity:
      podAntiAffinity:
      tolerations:
      - key: "node-role.kubernetes.io/infra"
        operator: Exists
  annotations:
  resources:
  removeOSDsIfOutAndSafeToRemove: false
    useAllNodes: false
    useAllDevices: false
    config:
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
  disruptionManagement:
    managePodBudgets: false
    osdMaintenanceTimeout: 30
    manageMachineDisruptionBudgets: false
    machineDisruptionBudgetNamespace: openshift-machine-api
```

``` 
kubectl create -f operator.yaml
kubectl create -f cluster.yaml
```
Create storageClass and more
```
kubectl apply -f cluster/examples/kubernetes/ceph/csi/rbd/storageclass.yaml
kubectl apply -f cluster/examples/kubernetes/ceph/filesystem.yaml
kubectl apply -f cluster/examples/kubernetes/ceph/csi/cephfs/storageclass.yaml
kubectl apply -f toolbox.yaml
```

Verify the installation using toolbox
```
kubectl -n rook-ceph exec -it $(kubectl -n rook-ceph get pod -l "app=rook-ceph-tools" -o jsonpath='{.items[0].metadata.name}') bash

ceph status
ceph osd status
ceph df
rados df
```

Source:

https://rook.io/docs/rook/v1.2/ceph-quickstart.html


