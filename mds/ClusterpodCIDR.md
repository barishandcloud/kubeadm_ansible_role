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