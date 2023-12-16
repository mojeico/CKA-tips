# CKA TIPS


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


cat /etc/*release*


# Get Info about upgrade 
- Get info about upgrade - `kubeadm upgrade plan`


# Update cluster (master node) components
- More info - https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/
- Drain controlplane node - `kubectl drain controlplane`
- Update the package index - `apt-get update`
- Update kubeadm - `apt-get upgrade -y kubeadm=1.27.0-00`
- Upgrade cluster - `kubeadm upgrade apply v1.27.0`
- Upgrade kubelet - `apt-get upgrade -y kubelet=1.27.0-00`
- Restart daemon - `systemctl daemon-reload` - Reload systemd manager configuration, reload all unit files, and recreate the entire dependency tree.
- Restart kubelet - `systemctl restart kubelet`


# Update cluster (worker node) components
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
- Update volume in etcd pod 
    - vi /etc/kubernetes/manifests/etcd.yaml
    - In volume "etcd-data" change old path to new from "--data-dir"
```
volumes:
- hostPath:
  path: /var/lib/etcd-from-backup
  type: DirectoryOrCreate
  name: etcd-data
```


# Context 
- Get kubectl config - `kubectl config view`
- Switch to cluster from config - `kubectl config use-context cluster_name`













