# Kubernetes tasks – 11-05-2023
* 1.Create 1 master node and 2 worker nodes – run app on node1 and db on node2 by using
* First i can create 3 t2.medium nodes(instances) in AWS
* In that 3 nodes we have to install docker with use of below commands
* Next create shell script to install kubeadm
# run the below script in root user
```
vi k8s.yml
chmod +x k8s.yml
./k8s.yml
```
![preview](k8s_images/k8s56.png)
* shell script for install kubeadm
```
#!/bin/bash
apt-get update
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh
sudo usermod -aG docker ubuntu
wget https://storage.googleapis.com/golang/getgo/installer_linux
chmod +x ./installer_linux
./installer_linux
source ~/.bash_profile
git clone https://github.com/Mirantis/cri-dockerd.git
cd cri-dockerd
mkdir bin
go build -o bin/cri-dockerd
mkdir -p /usr/local/bin
install -o root -g root -m 0755 bin/cri-dockerd /usr/local/bin/cri-dockerd
cp -a packaging/systemd/* /etc/systemd/system
sed -i -e 's,/usr/bin/cri-dockerd,/usr/local/bin/cri-dockerd,' /etc/systemd/system/cri-docker.service
systemctl daemon-reload
systemctl enable cri-docker.service
systemctl enable --now cri-docker.socket
cd ~
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl
sudo curl -fsSLo /etc/apt/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
echo "deb [signed-by=/etc/apt/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```
# exit and relogin 
* because docker also installed with script so relogin is manditory.
![preview](k8s_images/k8s57.png)
# run the above script in root user
* To create cluster in master node use this command ``kubeadm init --pod-network-cidr "10.244.0.0/16" --cri-socket "unix:///var/run/cri-dockerd.sock"``
![preview](k8s_images/k8s58.png)
* To configure we have to follow below commands in reguler user so we can exit from root user.
* To start using your cluster, you need to run the following as a regular user(ubuntu user)
* below commands execute only in master node 
  ```
  exit
  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config
  ```
* To setup configure run this command to install flannel ``kubectl apply -f https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml``
![preview](k8s_images/k8s59.png)
* To attach nodes to the master node run this command by providing CRI-DOCKER in node1,node2 as a root user ``kubeadm join 172.31.47.212:6443 --token o3w92f.36k6full7pu2ygi0 \
                       --cri-socket "unix:///var/run/cri-dockerd.sock" \
                --discovery-token-ca-cert-hash sha256:bac5374430a738a01b6914e68058ac1f95c5682b2f113462779c908bf4411ebe``
![preview](k8s_images/k8s60.png)in this control plane means master node.(master node also called as control plane)             
* In master node ``kubectl get nodes -w`` ![preview](k8s_images/k8s61.png)




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
* check the pod and node selector node name``kubectl get po -o wide``		  
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
1. Affinity
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
