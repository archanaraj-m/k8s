# Kubernetes tasks – 11-05-2023
* Create 1 master node and 2 worker nodes – run app on node1 and db on node2 by using
	1. Node selector
```yml
---
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluentd-ds
  annotations:
    kubernetes.io/change-cause: "update to latest"
spec:
  minReadySeconds: 5
  selector:
    matchLabels:
      app: fluentd
  template:
    metadata:
      name: fluentd
      labels:
        app: fluentd
    spec:
      containers:
        - name: fluentd
          image: fluentd:latest 
```
* check the pod and node selector node name``kubectl get po -o wode``		  
* Now lets select node by its name
```yml
---
apiVersion: v1
kind: Pod
metadata:
  name: nodeselector
  labels:
    app: nginx
    purpose: nodeselector
spec:
  nodeName: "aks-nodepool1-32808340-vmss000000"
  containers:
    - name: jenkins
      image: jenkins/jenkins:jdk11
```
2. Affinity
-----------
# Assign Pods to Nodes using Node Affinity
* Node affinity is a property of Pods that attracts them to a set of nodes (either as a preference or a hard requirement). 

* For this Kubernetes server must be at or later than version v1.10. To check the version, enter ``kubectl version``.
* List the nodes in your cluster, along with their labels``kubectl get nodes --show-labels``

* Schedule a Pod using required node affinity
-----------------------------------------------
* This manifest describes a Pod that has a requiredDuringSchedulingIgnoredDuringExecution node affinity,disktype: ssd. This means that the pod will get scheduled only on a node that has a disktype=ssd label.

* yml file for nginx required affinity with key value disktype

```yml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: disktype
            operator: In
            values:
            - ssd            
  containers:
  - name: nginx
    image: nginx
```
* kubectl apply -f pod-nginx-required-affinity.yaml

* check the pods``kubectl get pods -o wide``
* Schedule a Pod using preferred node affinity 
----------------------------------------------

* This manifest describes a Pod that has a preferredDuringSchedulingIgnoredDuringExecution node affinity,disktype: ssd. This means that the pod will prefer a node that has a disktype=ssd label.

```yml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  affinity:
    nodeAffinity:
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 1
        preference:
          matchExpressions:
          - key: disktype
            operator: In
            values:
            - ssd          
  containers:
  - name: nginx
    image: nginx
```
* create pod``kubectl apply -f pod-nginx-preferred-affinity.yaml``
* verify pod ``kubectl get pods -o wide``
  

1. Taints and tolerances
------------------------
* Taints are the opposite to node affinity and they allow a node to repel(force) a set of pods.
* Tolerations are applied to pods.
* Taints and tolerations work together to ensure that pods are not scheduled onto inappropriate nodes. One or more taints are applied to a node; this marks that the node should not accept any pods that do not tolerate the taints.
* commands For add a taint to a node using ``kubectl taint``For example,``kubectl taint nodes node1 key1=value1:NoSchedule``
* To remove the taint``kubectl taint nodes node1 key1=value1:NoSchedule-``

* yml file for using tolerations
```yml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    env: test
spec:
  containers:
  - name: nginx
    image: nginx
  tolerations:
  - key: "example-key"
    operator: "Exists"
    effect: "NoSchedule"
```	



* Create k8s cluster with version 1.25 and run any deployment(nginx/any) and then upgrade  cluster to version 1.27   

* For install specfic version command is ``sudo apt-get install -qy kubelet=<version> kubectl=<version> kubeadm=<version>``
* Choose a version to upgrade to, and run the appropriate command. For example:
``sudo kubeadm upgrade apply v1.27.x``(replace x with the patch version you picked for this upgrade)
