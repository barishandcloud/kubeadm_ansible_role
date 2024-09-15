# cks prep notes

## Understanding securityContext:
[Medium Article for reference](https://medium.com/marionete/kubernetes-securitycontext-with-practical-examples-67d890558d11#id_token=eyJhbGciOiJSUzI1NiIsImtpZCI6ImQ3YjkzOTc3MWE3ODAwYzQxM2Y5MDA1MTAxMmQ5NzU5ODE5MTZkNzEiLCJ0eXAiOiJKV1QifQ.eyJpc3MiOiJodHRwczovL2FjY291bnRzLmdvb2dsZS5jb20iLCJhenAiOiIyMTYyOTYwMzU4MzQtazFrNnFlMDYwczJ0cDJhMmphbTRsamRjbXMwMHN0dGcuYXBwcy5nb29nbGV1c2VyY29udGVudC5jb20iLCJhdWQiOiIyMTYyOTYwMzU4MzQtazFrNnFlMDYwczJ0cDJhMmphbTRsamRjbXMwMHN0dGcuYXBwcy5nb29nbGV1c2VyY29udGVudC5jb20iLCJzdWIiOiIxMTA1NDM0MzUyMTAxNTE1OTUyNTciLCJlbWFpbCI6InRlc3Rpbmd1dG9waWFAZ21haWwuY29tIiwiZW1haWxfdmVyaWZpZWQiOnRydWUsIm5iZiI6MTcyNjMzMTg3MywibmFtZSI6Ik1hbmp1bmF0aCBXYWpqYXJhbWF0dGkiLCJwaWN0dXJlIjoiaHR0cHM6Ly9saDMuZ29vZ2xldXNlcmNvbnRlbnQuY29tL2EvQUNnOG9jSnF5VUZUMXc4UlJUcDI4LUJmS0p5S0VqVkdSZTMtR0VGT3lHT3VMQkgzdlA5cENBPXM5Ni1jIiwiZ2l2ZW5fbmFtZSI6Ik1hbmp1bmF0aCIsImZhbWlseV9uYW1lIjoiV2FqamFyYW1hdHRpIiwiaWF0IjoxNzI2MzMyMTczLCJleHAiOjE3MjYzMzU3NzMsImp0aSI6IjE5MTI2MDA3NTRkY2QwZTk1Y2QxODQzNDU1NGMzNThmYThiYjA0NDEifQ.X81C23X4O0wDI4QvHlVTdlK59eP95ekj097aponSTBUDXozajPHOU_MeG8WL8ccBLDGuEWxCed_adlPIawzLFF9qchP5AnrMeD2cbFY9VDZTSWgDp9Aez8TFA1OD1tpjFmUT5EGkxjo8ps6PlfNrcwoqb93XLvUHVLyYX_uWRALtbaql-VcmYBrmmgcpv71Ovra3WxmX-qxtgQue2uTNUO9QQXO9lwMXD5AGs2WqDInaCP2Fq7G1ilhBcirMWbj3xtmEfioO6OGh-jw0uIvItnJnlZI90KIMiue0LDLDoZFBT3hPJ3ZgFq0mNC1czijUtPfEdElVsL8acyvnbUiHiw) 

[How to Create a Pod with emptyDir Volume Mounted](mds/PodemptyDir.md)
```
nano regularpo.yaml #Content as below
apiVersion: v1
kind: Pod
metadata:
  name: regularpo
spec:
  volumes:
  - name: demo-volume
    emptyDir: {}
  containers:
  - name: regularpo
    image: ubuntu
    command:
    - tail
    - -f
    - /dev/null
    volumeMounts:
    - name: demo-volume
      mountPath: /data/demo

#Now exec into the pod:
k exec -it regularpo sh
cat /proc/1/cmdline       ##--> check what is the process 1 of the container : output ->
tail-f/dev/null/          ##--> matches what we supplied

stat -c "%u %g" /proc/1/  ##--> checks who owns the /proc/1 dir, or the main process on container: output ->
0 0                       ##--> returns the uid and gid, reference, run this: 
id                        ##--> output ->
uid=0(root) gid=0(root) groups=0(root),10(wheel)

#For understanding who owns the mounted volumes
ls -l /data              ##--> output ->
total 4
drwxrwxrwx 2 root root 4096 Sep 14 19:51 demo 

#To understand what capabilities(cap) the container possess:
apt-get update && apt-get install libcap2-bin -y
capsh --print             ##--> output ->
Current: cap_chown,cap_dac_override,cap_fowner,cap_fsetid,cap_kill,cap_setgid,cap_setuid,cap_setpcap,cap_net_bind_service,cap_net_raw,cap_sys_chroot,cap_mknod,cap_audit_write,cap_setfcap=ep
Bounding set =cap_chown,cap_dac_override,cap_fowner,cap_fsetid,cap_kill,cap_setgid,cap_setuid,cap_setpcap,cap_net_bind_service,cap_net_raw,cap_sys_chroot,cap_mknod,cap_audit_write,cap_setfcap
Ambient set =
Current IAB: !cap_dac_read_search,!cap_linux_immutable,!cap_net_broadcast,!cap_net_admin,!cap_ipc_lock,!cap_ipc_owner,!cap_sys_module,!cap_sys_rawio,!cap_sys_ptrace,!cap_sys_pacct,!cap_sys_admin,!cap_sys_boot,!cap_sys_nice,!cap_sys_resource,!cap_sys_time,!cap_sys_tty_config,!cap_lease,!cap_audit_control,!cap_mac_override,!cap_mac_admin,!cap_syslog,!cap_wake_alarm,!cap_block_suspend,!cap_audit_read,!cap_perfmon,!cap_bpf,!cap_checkpoint_restore
Securebits: 00/0x0/1'b0 (no-new-privs=0)
 secure-noroot: no (unlocked)
 secure-no-suid-fixup: no (unlocked)
 secure-keep-caps: no (unlocked)
 secure-no-ambient-raise: no (unlocked)
uid=0(root) euid=0(root)
gid=0(root)
groups=0(root)
Guessed mode: HYBRID (4)

#For understanding pod level securityContext:
kubectl explain po.spec.securityContext
#For undestanding container level securityContext:
kubectl explain po.spec.containers.securityContext
#Moral of story, container securityContext has higher precedence over container level securityContext

#Observation before concluding for the final securityContext pod manifest:
    securityContext:
      privileged: false                      ## No changes in capsh output
    securityContext:
      allowPrivilegeEscalation: false        ## changes: from 0 to 1 in --> Securebits: 00/0x0/1'b0 (no-new-privs=1)


```

## Scenario for network policies:
```
Let there be two namespaces
- ns1 : should run two pods hosting echoservers mecho1ns1 & mecho2ns1
- ns2 : should run two pods hosting echoservers mecho1ns2 & mecho2ns2

export do="--dry-run=client"
export mecho="mendhak/http-https-echo:34"
export hecho="hashicorp/http-echo"
export jello="bmuschko/nodejs-hello-world:1.0.0"

export debug="nicolaka/netshoot" or
export debug="praqma/network-multitool"

#Create ns1 components
k create ns ns1 
k -n ns1 run mecho1ns1 --port=8080 --image=$mecho
k -n ns1 run mecho2ns1 --port=8080 --image=$mecho

k get po -n ns1 -o wide
NAME        READY   STATUS    RESTARTS   AGE   IP             NODE      NOMINATED NODE   READINESS GATES
mecho1ns1   1/1     Running   0          34m   172.16.2.139   worker1   <none>           <none>
mecho2ns1   1/1     Running   0          34m   172.16.1.171   worker2   <none>           <none>

#Create ns2 components
k create ns ns2
k -n ns2 run mecho1ns2 --port=8080 --image=$mecho
k -n ns2 run mecho2ns2 --port=8080 --image=$mecho

k get po -n ns2 -o wide
NAME        READY   STATUS    RESTARTS   AGE   IP             NODE      NOMINATED NODE   READINESS GATES
mecho1ns2   1/1     Running   0          51s   172.16.2.102   worker1   <none>           <none>
mecho2ns2   1/1     Running   0          47s   172.16.2.37    worker1   <none>           <none>


#Test from ns1 --> ns1 & ns2 components
k -n ns1 run debug --rm -it --image=$debug -- /bin/bash
curl 172.16.2.139:8080 --> works
curl 172.16.1.171:8080 --> works
curl 172.16.2.102:8080 --> works
curl 172.16.2.37:8080  --> works
```
## useful commands
```vboxmanage list vms
vboxmanage snapshot master list 
vboxmanage snapshot master restore Snapshot1 
vboxmanage startvm master --type headless
vboxmanage snapshot master take Snapshot3
vboxmanage snapshot master delete Snapshot3

for i in {192.168.56.111,192.168.56.112,192.168.56.113}; do ssh -i ~/Documents/udkeys $i sudo init 0; sleep 2s; done
for i in {master,worker1,worker2}; do vboxmanage snapshot $i restore Snapshot1; sleep 10s; done
for i in {master,worker1,worker2}; do vboxmanage startvm $i --type headless; sleep 5s; done
for i in {192.168.56.111,192.168.56.112,192.168.56.113}; do ssh -i ~/Documents/udkeys $i sudo systemctl start kubelet; done;

for i in {master,worker1,worker2}; do vboxmanage snapshot $i take Snapshot2 --description=beforeinit; sleep 10s; done 
for i in {master,worker1,worker2}; do vboxmanage snapshot $i delete Snapshot2; sleep 10s; done 
 ```

```
docker run -p 8080:8080 -p 8443:8443 --rm -t mendhak/http-https-echo:34

k create deployment echodeploy --image=mendhak/http-https-echo:34
k expose deployment echodeploy --port=8080 --target-port=8080 --type=LoadBalancer

http://192.168.56.220:8080/ or
curl 192.168.56.220:8080

dig mecho1ns1-svc.ns1.svc.cluster.local +noall +answer
kubectl run -it --rm --image=curlimages/curl curly -- curl 172.16.2.139:8080

k -n ns1 run hecho1ns1 --port=5678 --image=$hecho -- -text="hello world - from hecho1ns1" #or maybe
k run hecho1ns1 --port=8080 --image=$hecho $do -oyaml -- -text="hello world - from hecho1ns1" -listen=:8080
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: hecho1ns1
  name: hecho1ns1
spec:
  containers:
  - args:
    - -text=hello world - from hecho1ns1
    - -listen=:8080
    image: hashicorp/http-echo
    name: hecho1ns1
    ports:
    - containerPort: 8080
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}

```

