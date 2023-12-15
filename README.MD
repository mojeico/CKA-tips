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
- `px -aux | grep kube-scheduler`


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


