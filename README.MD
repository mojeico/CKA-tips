# CKA TIPS


# Kubernetes architecture

1) etcd - key value to keep kubernetes state 
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
- px -aux | grep kube-scheduler
