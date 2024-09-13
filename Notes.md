# useful commands
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

# kcs prep notes

```
ways of finding pod-CIDR:

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