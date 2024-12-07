# CKA TIPS

# Links with tasks and exercises to practice :

- https://github.com/chadmcrowell/CKA-Exercises/blob/main/cluster-architecture.md
- https://github.com/devopshubproject/cka-lab/tree/main/Task1
- https://github.com/stretchcloud/cka-lab-practice/blob/master/ApplicationLifecycleManagement.md
- https://github.com/moabukar/CKA-Exercises/tree/main/7-mock-exam-questions
- https://killer.sh/order
- https://kodekloud.com/courses/ultimate-certified-kubernetes-administrator-cka-mock-exam/
- https://cloudacademy.com/exam/landing/35174/
- https://www.youtube.com/results?search_query=CKA+Exam+Question



# Practice 

- https://killercoda.com/killer-shell-cka
- https://killercoda.com/sachin/course/CKA
- https://killercoda.com/amitk
- https://killercoda.com/killer-shell-cka
- https://killercoda.com/
- 

# Kubernetes architecture

1) etcd - key value to keep kubernetes state ( pods, secrets, service, config map ...... )
2) kube-apiserver - manage kubectl commond and manage all request Cluster Architecture components  ()
3) kube-controller-manager - list of controller processes.
   - Node controller - check node every 5s 
   - Replication controller - check ReplicaSet and number of pods 
   - Job controller - 
   - EndpointSlice controller - 
   - ServiceAccount controller -
   - etc...
4) kube-scheduler - deciding and schedule pod on node (but not create)
5) kubelet - run pod on node 
6) kube-proxy - run on each node and manage networking in cluster (use ip table rule )


# Get process of k8s
- `ps -aux | grep kube-scheduler`
- `ps -ef | grep etcd`


# Get list process 
- Get kubernetes process list -  `systemctl list-unit-files --type service --all | grep kube`


# Static pods
- Info about it - https://kubernetes.io/docs/tasks/configure-pod-container/static-pod/
- Path - `/etc/kubernetes/manifests`
- Create static pod -` kubectl run --image=nginx static-nginx --dry-run=client -o yaml  > /etc/kubernetes/manifests/static-busybox.yaml`


# Kube Scheduler Configuration
- Info about it - https://kubernetes.io/docs/reference/scheduling/config/
- Info about it - https://kubernetes.io/docs/tasks/extend-kubernetes/configure-multiple-schedulers/


# How does the Kubernetes scheduler work?
- https://stackoverflow.com/questions/28857993/how-does-kubernetes-scheduler-work
- https://jvns.ca/blog/2017/07/27/how-does-the-kubernetes-scheduler-work/
- https://kubernetes.io/blog/2017/03/advanced-scheduling-in-kubernetes/
- https://github.com/kubernetes/community/blob/master/contributors/devel/sig-scheduling/scheduling_code_hierarchy_overview.md


# Pod Priority and Preemption 
- Info about it - https://kubernetes.io/docs/concepts/scheduling-eviction/pod-priority-preemption/


# Add Encryption 
- https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/


# Monitoring 
- Get memory and CPU of nodes - `kubectl top node`
- Get memory and CPU of pods - `kubectl top pod`


# Drain and uncordon
- Drain node (delete and move all pods (which have replicaset ) to another node + mark node unschedulable) - `kubectl drain node_name`
- Drain node - `kubectl drain node_name --ignore-daemonsets`
- Cordon node (only mark node unschedulable) - `kubectl cordon node_name`
- Uncordon node (only mark node schedulable) - `kubectl uncordon node_name`


# Get OS release (Linix)
- Get release name - `cat /etc/*release*`


# Get Info about upgrade 
- Get info about upgrade - `kubeadm upgrade plan`


# Update cluster (master node) components (kubeadmin, kubelet)
- More info - https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/
- Drain controlplane node - `kubectl drain controlplane`
- Update the package index - `apt-get update`
- Update kubeadm - `apt-get upgrade -y kubeadm=1.27.0-00`
- Upgrade cluster - `kubeadm upgrade apply v1.27.0`
- Upgrade kubelet -  `apt-get upgrade -y kubelet=1.27.0-00`
- Restart daemon - `systemctl daemon-reload` - Reload systemd manager configuration, reload all unit files, and recreate the entire dependency tree.
- Restart kubelet - `systemctl restart kubelet`
- Uncordon node - `kubectl uncordon controlplane`


# Update cluster (worker node) components (kubeadmin, kubelet)
- More info - https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/
- Drain node - `kubectl drain node_name`
- Update the package index - `apt-get update`
- Update kubeadm - `apt-get upgrade -y kubeadm=1.27.0-00`
- Upgrade node config - `kubeadm upgrade node`
- Upgrade kubelet - `apt-get upgrade -y kubelet=1.27.0-00`
- Restart daemon - `systemctl daemon-reload` - Reload systemd manager configuration, reload all unit files, and recreate the entire dependency tree.
- Restart kubelet - `systemctl restart kubelet`
- Uncordon node - `kubectl uncordon node_name`


# Backup etcd
- ???? - `cat /etc/system.d/system/etcd.service`

- More info - https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/#backing-up-an-etcd-cluster
- Get etcd version - `kubectl -n kube-system describe pod etcd-controlplane | grep Image:`
- Get client address - `kubectl -n kube-system describe pod etcd-controlplane | grep '\--listen-client-urls'`
- Get etcd certificate file located - `kubectl -n kube-system describe pod etcd-controlplane | grep '\--cert-file'`
- Get etcd trusted CA Certificate file located - `kubectl -n kube-system describe pod etcd-controlplane | grep '\--trusted-ca-file'`
- Get etcd key file location - `kubectl -n kube-system describe pod etcd-controlplane | grep peer-key-file`

- Create snapshot - ETCDCTL_API=3 etcdctl snapshot save /opt/snapshot-pre-boot.db \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key 


# Restore etcd
- More info - https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/#restoring-an-etcd-cluster
- Restore etcd - `ETCDCTL_API=3 etcdctl snapshot restore --data-dir /var/lib/etcd-from-backup  /opt/snapshot-pre-boot.db`
- Update volume in etcd static pod 
    - vi /etc/kubernetes/manifests/etcd.yaml
    - In volume "etcd-data" change old path to new from "--data-dir"
```
volumes:
- hostPath:
  path: /var/lib/etcd-from-backup
  type: DirectoryOrCreate
  name: etcd-data
```


# Kubeconfig - Context  
- Get kubectl config - `kubectl config view`
- Get kubectl specific config - `kubectl config view --kubeconfig path_to_config`
- Switch to cluster from config - `kubectl config use-context cluster_name`
- Get current context - `kubectl config current-context --kubeconfig path_to_config`
- Get context name - `kubectl config get-contexts -o name`


# Certification OpenSSL
- Generate key - `openssl genrsa -out admin.key 2048`
- Generate certificate signing request - ` openssl req -new  -key admin.key -subj "/CN=kube-admin" -out admin.csr`
- Test - ``


# KubeDNS
- CoreDNS - deployed to kube-system  ns (2 pods)  -> service_name.namespace_name.service.cluster.local


# Install Cluster - kubeadm
- More info - https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/
- Install Container Runtimes - https://kubernetes.io/docs/setup/production-environment/container-runtimes/
- Each node : 
```shell

cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
br_netfilter
EOF

cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF

sudo sysctl --system

```
- Install kubeadm, kubectl and kubelet on all nodes: - https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/
```shell

sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl

sudo mkdir -m 755 /etc/apt/keyrings

curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.27/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.27/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo apt-get update

# To see the new version labels
sudo apt-cache madison kubeadm

sudo apt-get install -y kubelet=1.27.0-2.1 kubeadm=1.27.0-2.1 kubectl=1.27.0-2.1

sudo apt-mark hold kubelet kubeadm kubectl

```
- Creating a cluster with kubeadm - https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/
```shell
IP_ADDR=$(ip addr show eth0 | grep -oP '(?<=inet\s)\d+(\.\d+){3}')
kubeadm init --apiserver-cert-extra-sans=controlplane --apiserver-advertise-address $IP_ADDR --pod-network-cidr=10.244.0.0/16
```
- Set up the kubeconfig:
```shell
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
Add node to kube cluster from "kubeadm init"
- kubeadm join 192.19.145.9:6443 --token vknise.9dxdc280u3bqpmwk --discovery-token-ca-cert-hash sha256:3926995399da0f1ed7d2a935a5a0f547d7384446ee4fdc11bab5a138202ea6b0

- Install a network plugin:
```shell

```


# Control plane failure
- Get status of control plane - `kubectl get po -n kube-system`


# Node failure
- Get status of node - `kubectl get nodes`
- Describe node - `kubectl describe node node_name`
- Get disk space - `df -h`
- Get kubelet status - `service kubelet status`
- Get kubelet logs - `sudo journalctl -u kubelet` or with follow `sudo journalctl -u kubelet -f `
- Check certificates - `openssl x509 -in /var/lib/kubelet/node_name.crt -text`
- All folders with configs - `ls -l /var/lib/` -->> /var/lib/kubelet


