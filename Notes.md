# kcs prep notes

## Scenario for network policies
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


## ways of finding pod-CIDR:
```
kubectl get nodes -o jsonpath='{.items[*].spec.podCIDR}'
172.16.0.0/24 172.16.1.0/24 172.16.2.0/24

ps -ef | grep cluster-cidr
root        1199     993  1 10:43 ?        00:00:16 kube-controller-manager --allocate-node-cidrs=true --authentication-kubeconfig=/etc/kubernetes/controller-manager.conf --authorization-kubeconfig=/etc/kubernetes/controller-manager.conf --bind-address=127.0.0.1 --client-ca-file=/etc/kubernetes/pki/ca.crt --cluster-cidr=172.16.0.0/16 --cluster-name=kubernetes --cluster-signing-cert-file=/etc/kubernetes/pki/ca.crt --cluster-signing-key-file=/etc/kubernetes/pki/ca.key --controllers=*,bootstrapsigner,tokencleaner --kubeconfig=/etc/kubernetes/controller-manager.conf --leader-elect=true --requestheader-client-ca-file=/etc/kubernetes/pki/front-proxy-ca.crt --root-ca-file=/etc/kubernetes/pki/ca.crt --service-account-private-key-file=/etc/kubernetes/pki/sa.key --service-cluster-ip-range=10.96.0.0/12 --use-service-account-credentials=true

kubeadm config print init-defaults
serviceSubnet: 10.96.0.0/12

kubectl cluster-info dump | grep -m 1 pod
"podCIDR": "172.16.0.0/24",

sudo grep cidr /etc/kubernetes/manifests/kube-*
/etc/kubernetes/manifests/kube-controller-manager.yaml:    - --allocate-node-cidrs=true
/etc/kubernetes/manifests/kube-controller-manager.yaml:    - --cluster-cidr=172.16.0.0/16

#Need to update pause container --> maybe not such a big deal
#PAUSE_IMAGE=$(kubeadm config images list | grep pause)
#sudo -E sed -i "s,sandbox_image = .*,sandbox_image = \"$PAUSE_IMAGE\",g" /etc/containerd/config.toml

```
## content of 01-netcfg.yml
```
network :
  version : 2
  renderer : networkd
  ethernets :
    enp0s3 :
      dhcp4 : yes
    enp0s8 :
      dhcp4 : no
      dhcp6 : no
      addresses : [###ipaddress###/24]
      nameservers :
        addresses : [8.8.8.8]
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

k -n ns1 run hecho1ns1 --port=5678 --image=$hecho -- -text="hello world - from hecho1ns1" or maybe
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