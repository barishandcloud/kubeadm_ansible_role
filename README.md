# This repository host an ansible playbook that can be used to bootstrap a cluster.

Assumption in this scenario is that we have ubuntu 22.04 vms created in virtualbox that has adapters attached to NAT and Host-only. This way we usually get two IP ranges. Off these one of the ranges commences with 192.168.56.10X. The pre-req for running this playbook is 
* keys generated with the usage of ```ssh-keygen -f filename ```, in this case the filename is udkeys and the private key file path is marked in hosts.ini (present in this repo). 
* The afore mentioned IPs with a bare minimum of two IPs belonging to atleast two vms (to be used as master and worker and named km and kw1 in the hosts.ini). The IP along with hostname entries are made in hosts.ini
* The afore-mentioned IPs with a bare minimum of two IPs belonging to atleast two vms (to be used as master and worker). The IP along with hostname entries are made in hosts.ini
* The user should be part of sudoers in those two vms (i.e. km & kw1) ```echo "$USER ALL=(ALL:ALL) NOPASSWD: ALL" | sudo tee /etc/sudoers.d/$USER ``` in this case, manju and entry marked in hosts.ini

If everything above is done an ad-hoc ping check should yield:
```bash
ansible all -m ping -i hosts.ini --ssh-common-args='-o StrictHostKeyChecking=no'                                                     
kw1 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
km | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```
Also to verify if sudoers without passwords working as expected, we can expect:
```bash
ansible all -m shell -i hosts.ini --ssh-common-args='-o StrictHostKeyChecking=no' -a "date" --become
kw1 | CHANGED | rc=0 >>
Tue Mar 19 12:59:57 PM UTC 2024
km | CHANGED | rc=0 >>
Tue Mar 19 12:59:58 PM UTC 2024
```

## Running the playbook

Use the package manager [pip](https://pip.pypa.io/en/stable/) to install foobar.
The main task is invoked by

```bash
ansible-playbook -i hosts.ini main.yaml
```

## Sample output

```bash
PLAY [cluster] *******************************************************************************************

TASK [Gathering Facts] ***********************************************************************************
ok: [kw1]
ok: [km]

TASK [common-configurations : Turn off swap] *************************************************************
changed: [km]
changed: [kw1]

TASK [common-configurations : Turn off swap permanently by editing fstab entry] **************************
changed: [km]
changed: [kw1]

TASK [common-configurations : create containerd systemctl file at modules-load.d] ************************
changed: [kw1]
changed: [km]

TASK [common-configurations : overlay] *******************************************************************
changed: [km]
changed: [kw1]

TASK [common-configurations : br_netfilter] **************************************************************
changed: [km]
changed: [kw1]

TASK [common-configurations : create kubernetes systemctl file at sysctl.d] ******************************
changed: [kw1]
changed: [km]

TASK [common-configurations : Apply sysctl settings] *****************************************************
changed: [km]
changed: [kw1]

TASK [common-configurations : install misc binaries] *****************************************************
changed: [km]
changed: [kw1]

TASK [common-configurations : Download Docker GPG key] ***************************************************
changed: [kw1]
changed: [km]

TASK [common-configurations : Import Docker GPG key] *****************************************************
changed: [km]
changed: [kw1]

TASK [common-configurations : Add Docker APT repository] *************************************************
changed: [km]
changed: [kw1]

TASK [common-configurations : install containerd] ********************************************************
changed: [kw1]
changed: [km]

TASK [common-configurations : populate containerd defaults] **********************************************
changed: [kw1]
changed: [km]

TASK [common-configurations : Update containerd config] **************************************************
changed: [km]
changed: [kw1]

TASK [common-configurations : Restart and enable containerd service] *************************************
changed: [kw1]
changed: [km]

TASK [common-configurations : Download Kubernetes key] ***************************************************
changed: [kw1]
changed: [km]

TASK [common-configurations : Import Kubernetes GPG key] *************************************************
changed: [km]
changed: [kw1]

TASK [common-configurations : Add Kubernetes APT repository] *********************************************
changed: [kw1]
changed: [km]

TASK [common-configurations : install kubernetes binaries] ***********************************************
changed: [kw1]
changed: [km]

PLAY [masters] *******************************************************************************************

TASK [Gathering Facts] ***********************************************************************************
ok: [km]

TASK [master : Check if Kubernetes cluster is already initialized] ***************************************
ok: [km]

TASK [master : Initialize Kubernetes master] *************************************************************
[WARNING]: Consider using 'become', 'become_method', and 'become_user' rather than running sudo
changed: [km]

TASK [master : debug] ************************************************************************************
ok: [km] => {
    "initcluster.stdout_lines": [
        "[init] Using Kubernetes version: v1.28.8",
        "[preflight] Running pre-flight checks",
        "[preflight] Pulling images required for setting up a Kubernetes cluster",
        "[preflight] This might take a minute or two, depending on the speed of your internet connection",
        "[preflight] You can also perform this action in beforehand using 'kubeadm config images pull'",
        "[certs] Using certificateDir folder \"/etc/kubernetes/pki\"",
        "[certs] Generating \"ca\" certificate and key",
        "[certs] Generating \"apiserver\" certificate and key",
        "[certs] apiserver serving cert is signed for DNS names [km kubernetes kubernetes.default kubernetes.default.svc kubernetes.default.svc.cluster.local] and IPs [10.96.0.1 192.168.59.103]",
        "[certs] Generating \"apiserver-kubelet-client\" certificate and key",
        "[certs] Generating \"front-proxy-ca\" certificate and key",
        "[certs] Generating \"front-proxy-client\" certificate and key",
        "[certs] Generating \"etcd/ca\" certificate and key",
        "[certs] Generating \"etcd/server\" certificate and key",
        "[certs] etcd/server serving cert is signed for DNS names [km localhost] and IPs [192.168.59.103 127.0.0.1 ::1]",
        "[certs] Generating \"etcd/peer\" certificate and key",
        "[certs] etcd/peer serving cert is signed for DNS names [km localhost] and IPs [192.168.59.103 127.0.0.1 ::1]",
        "[certs] Generating \"etcd/healthcheck-client\" certificate and key",
        "[certs] Generating \"apiserver-etcd-client\" certificate and key",
        "[certs] Generating \"sa\" key and public key",
        "[kubeconfig] Using kubeconfig folder \"/etc/kubernetes\"",
        "[kubeconfig] Writing \"admin.conf\" kubeconfig file",
        "[kubeconfig] Writing \"kubelet.conf\" kubeconfig file",
        "[kubeconfig] Writing \"controller-manager.conf\" kubeconfig file",
        "[kubeconfig] Writing \"scheduler.conf\" kubeconfig file",
        "[etcd] Creating static Pod manifest for local etcd in \"/etc/kubernetes/manifests\"",
        "[control-plane] Using manifest folder \"/etc/kubernetes/manifests\"",
        "[control-plane] Creating static Pod manifest for \"kube-apiserver\"",
        "[control-plane] Creating static Pod manifest for \"kube-controller-manager\"",
        "[control-plane] Creating static Pod manifest for \"kube-scheduler\"",
        "[kubelet-start] Writing kubelet environment file with flags to file \"/var/lib/kubelet/kubeadm-flags.env\"",
        "[kubelet-start] Writing kubelet configuration to file \"/var/lib/kubelet/config.yaml\"",
        "[kubelet-start] Starting the kubelet",
        "[wait-control-plane] Waiting for the kubelet to boot up the control plane as static Pods from directory \"/etc/kubernetes/manifests\". This can take up to 4m0s",
        "[apiclient] All control plane components are healthy after 16.003718 seconds",
        "[upload-config] Storing the configuration used in ConfigMap \"kubeadm-config\" in the \"kube-system\" Namespace",
        "[kubelet] Creating a ConfigMap \"kubelet-config\" in namespace kube-system with the configuration for the kubelets in the cluster",
        "[upload-certs] Skipping phase. Please see --upload-certs",
        "[mark-control-plane] Marking the node km as control-plane by adding the labels: [node-role.kubernetes.io/control-plane node.kubernetes.io/exclude-from-external-load-balancers]",
        "[mark-control-plane] Marking the node km as control-plane by adding the taints [node-role.kubernetes.io/control-plane:NoSchedule]",
        "[bootstrap-token] Using token: a2vvng.uv7ojp2pgj7o4tp9",
        "[bootstrap-token] Configuring bootstrap tokens, cluster-info ConfigMap, RBAC Roles",
        "[bootstrap-token] Configured RBAC rules to allow Node Bootstrap tokens to get nodes",
        "[bootstrap-token] Configured RBAC rules to allow Node Bootstrap tokens to post CSRs in order for nodes to get long term certificate credentials",
        "[bootstrap-token] Configured RBAC rules to allow the csrapprover controller automatically approve CSRs from a Node Bootstrap Token",
        "[bootstrap-token] Configured RBAC rules to allow certificate rotation for all node client certificates in the cluster",
        "[bootstrap-token] Creating the \"cluster-info\" ConfigMap in the \"kube-public\" namespace",
        "[kubelet-finalize] Updating \"/etc/kubernetes/kubelet.conf\" to point to a rotatable kubelet client certificate and key",
        "[addons] Applied essential addon: CoreDNS",
        "[addons] Applied essential addon: kube-proxy",
        "",
        "Your Kubernetes control-plane has initialized successfully!",
        "",
        "To start using your cluster, you need to run the following as a regular user:",
        "",
        "  mkdir -p $HOME/.kube",
        "  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config",
        "  sudo chown $(id -u):$(id -g) $HOME/.kube/config",
        "",
        "Alternatively, if you are the root user, you can run:",
        "",
        "  export KUBECONFIG=/etc/kubernetes/admin.conf",
        "",
        "You should now deploy a pod network to the cluster.",
        "Run \"kubectl apply -f [podnetwork].yaml\" with one of the options listed at:",
        "  https://kubernetes.io/docs/concepts/cluster-administration/addons/",
        "",
        "Then you can join any number of worker nodes by running the following on each as root:",
        "",
        "kubeadm join 192.168.59.103:6443 --token a2vvng.uv7ojp2pgj7o4tp9 \\",
        "\t--discovery-token-ca-cert-hash sha256:135f09d91b34bb61b6f74a4554b34aba3eb4c4b4cdfdea1c6a665fa98fe22550 "
    ]
}

TASK [master : Create .kube directory] *******************************************************************
changed: [km]

TASK [master : Copy admin.conf to .kube/config] **********************************************************
changed: [km]

PLAY [masters] *******************************************************************************************

TASK [cluster_join : Check if node is a master] **********************************************************
ok: [km]

TASK [cluster_join : Get join command] *******************************************************************
changed: [km]

TASK [cluster_join : Add join command to placeholder host] ***********************************************
changed: [km]

TASK [cluster_join : debug] ******************************************************************************
ok: [km] => {
    "msg": "kubeadm join 192.168.59.103:6443 --token x4hwtt.7lyudr41ce0u6ezt --discovery-token-ca-cert-hash sha256:135f09d91b34bb61b6f74a4554b34aba3eb4c4b4cdfdea1c6a665fa98fe22550 "
}

TASK [cluster_join : Check if node is a worker] **********************************************************
ok: [km]

TASK [cluster_join : Check if worker has already joined the cluster] *************************************
skipping: [km]

TASK [cluster_join : Join cluster if not already joined] *************************************************
skipping: [km]

PLAY [workers] *******************************************************************************************

TASK [Gathering Facts] ***********************************************************************************
ok: [kw1]

TASK [cluster_join : Check if node is a master] **********************************************************
ok: [kw1]

TASK [cluster_join : Get join command] *******************************************************************
skipping: [kw1]

TASK [cluster_join : Add join command to placeholder host] ***********************************************
skipping: [kw1]

TASK [cluster_join : debug] ******************************************************************************
skipping: [kw1]

TASK [cluster_join : Check if node is a worker] **********************************************************
ok: [kw1]

TASK [cluster_join : Check if worker has already joined the cluster] *************************************
ok: [kw1]

TASK [cluster_join : Join cluster if not already joined] *************************************************
changed: [kw1]

PLAY [masters] *******************************************************************************************

TASK [Gathering Facts] ***********************************************************************************
ok: [km]

TASK [plugins : Check if plugins are installed] **********************************************************
ok: [km]

TASK [plugins : Install Plugins] *************************************************************************
changed: [km]

TASK [plugins : debug] ***********************************************************************************
ok: [km] => {
    "plugin.stdout_lines": [
        "serviceaccount/weave-net created",
        "clusterrole.rbac.authorization.k8s.io/weave-net created",
        "clusterrolebinding.rbac.authorization.k8s.io/weave-net created",
        "role.rbac.authorization.k8s.io/weave-net created",
        "rolebinding.rbac.authorization.k8s.io/weave-net created",
        "daemonset.apps/weave-net created"
    ]
}

PLAY RECAP ***********************************************************************************************
km                         : ok=35   changed=25   unreachable=0    failed=0    skipped=2    rescued=0    ignored=0   
kw1                        : ok=25   changed=20   unreachable=0    failed=0    skipped=3    rescued=0    ignored=0 
```

## Other checks

Once the playbook is run we can run kubectl commands on master:

```bash
ssh 192.168.59.103 "kubectl get no -o wide"
NAME   STATUS   ROLES           AGE     VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION       CONTAINER-RUNTIME
km     Ready    control-plane   2m41s   v1.28.8   10.0.2.15     <none>        Ubuntu 22.04.4 LTS   5.15.0-101-generic   containerd://1.6.28
kw1    Ready    <none>          2m21s   v1.28.8   10.0.2.15     <none>        Ubuntu 22.04.4 LTS   5.15.0-101-generic   containerd://1.6.28

ssh 192.168.59.103 "kubectl get all -A"
NAMESPACE     NAME                             READY   STATUS              RESTARTS        AGE
kube-system   pod/coredns-5dd5756b68-fjh8h     0/1     ContainerCreating   0               2m47s
kube-system   pod/coredns-5dd5756b68-r2jx4     0/1     ContainerCreating   0               2m47s
kube-system   pod/etcd-km                      1/1     Running             0               3m4s
kube-system   pod/kube-apiserver-km            1/1     Running             0               3m1s
kube-system   pod/kube-controller-manager-km   1/1     Running             0               2m58s
kube-system   pod/kube-proxy-996bh             1/1     Running             0               2m47s
kube-system   pod/kube-proxy-nl5sd             1/1     Running             0               2m44s
kube-system   pod/kube-scheduler-km            1/1     Running             0               3m1s
kube-system   pod/weave-net-ffb2m              2/2     Running             1 (2m23s ago)   2m38s
kube-system   pod/weave-net-phd9j              2/2     Running             1 (2m18s ago)   2m38s

NAMESPACE     NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)                  AGE
default       service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP                  3m2s
kube-system   service/kube-dns     ClusterIP   10.96.0.10   <none>        53/UDP,53/TCP,9153/TCP   2m59s

NAMESPACE     NAME                        DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR            AGE
kube-system   daemonset.apps/kube-proxy   2         2         2       2            2           kubernetes.io/os=linux   2m59s
kube-system   daemonset.apps/weave-net    2         2         2       2            2           <none>                   2m38s

NAMESPACE     NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
kube-system   deployment.apps/coredns   0/2     2            0           2m59s

NAMESPACE     NAME                                 DESIRED   CURRENT   READY   AGE
kube-system   replicaset.apps/coredns-5dd5756b68   2         2         0       2m48s
```