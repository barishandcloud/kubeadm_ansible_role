## Creation of Pod with emptyDir Volume Mounted:
```
# Run this command: 
k run regularpo --image=busybox $do -oyaml --command tail -- -f /dev/null
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: regularpo
  name: regularpo
spec:
  containers:
  - command:
    - tail
    - -f
    - /dev/null
    image: busybox
    name: regularpo
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}

#Use this: 
k explain po.spec.volumes | grep required -A 4
  name	<string> -required-
    name of the volume. Must be a DNS_LABEL and unique within the pod. More
    info:
    https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#names

#then this
k explain po.spec.volumes | grep empty -A 4
  emptyDir	<EmptyDirVolumeSource>
    emptyDir represents a temporary directory that shares a pod's lifetime. More
    info: https://kubernetes.io/docs/concepts/storage/volumes#emptydir

#then this
k explain po.spec.containers.volumeMounts | grep required -A 3
  mountPath	<string> -required-
    Path within the container at which the volume should be mounted.  Must not
    contain ':'.
--
  name	<string> -required-
    This must match the Name of a Volume.

To arrive at:
apiVersion: v1
kind: Pod
metadata:
  name: regularpo
spec:
  volumes:
  - name: demo-volume
    emptyDir: {}
  containers:
  - image: busybox
    name: regularpo
    command:
    - tail
    - -f
    - /dev/null
    volumeMounts:
    - name: demo-volume
      mountPath: /data/demo
```