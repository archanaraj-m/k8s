# Kubernetes (k8s) Activities (MAY9th/2023)
* 1.Create a Kubernetes cluster using kubeadm
kubeadm instalation
--------------------
* First we can create 2 instances with t2 medium
* Next that 2 nodes 1 is masternode and another node1(workernode)
* In this 2 nodes install docker with docker commands
```
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh
sudo usermod -aG docker ubuntu
docker info
exit and relogin
```
* After install docker in that 2 nodes exit and relogin because we can give usermod permissions.
* After successful installation re-login into your machine
* After re-login try to get docker info $ docker info
* next create ``vi k8s.yml`` for install kubeadm through shell scripting
* next in that vi file paste below commands
```
#!/bin/bash
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
![preview](./../k8s_images/k8s32.png)
![preview](./../k8s_images/k8s33.png)
![preview](./../k8s_images/k8s34.png)
![preview](./../k8s_images/k8s35.png)
![preview](./../k8s_images/k8s36.png)
![preview](./../k8s_images/k8s37.png)
* 2.Deploy any application using kubectl
```yml
apiVersion: apps/v1 
kind: Deployment
metadata:
  name: spc-deploy
  labels:
    app: spc  
spec:
  minReadySeconds: 3
  replicas: 1
  selector:
    matchLabels:
      app: spc
  template:
    metadata:
      labels: 
        app: spc
    spec:
      containers: 
        - name: spc-cont
          image: archanaraj/myspcimage:latest
          ports :
            - containerPort: 8080    
```
![preview](./../k8s_images/k8s38.png)
* 3.Backup Kubernetes I.e backup etcd
* 4.List out all the pod’s running in kube system namespace
* command is ``kubectl get pods --all-namespaces``
![preview](./../k8s_images/k8s39.png)
* 5.Write down all the steps required to make Kubernetes highly available
* https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/ha-topology/

* 6.Do a rolling update and roll back
* means rolling update is update the version(ex:java17) if we want for the application
* if we want previous version(ex:java11) i.e roll back
* Role and ClusterRole:
They are just a set of rules that represent a set of permissions. A Role can only be used to grant access to resources within namespaces. A ClusterRole can be used to grant the same permissions as a Role but they can also be used to grant access to cluster-scoped resources, non-resource endpoints.
* RoleBinding and ClusterRoleBinding:
As the name implies, it’s just the binding between a subject and a Role or a ClusterRole.

* 7.Ensure usage of secret in MySQL and configmaps
```yml
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: mysql-cm
data:
  MYSQL_USER: archana
  MYSQL_PASSWORD: 1234
  MYSQL_ROOT_PASSWORD: 1234
  MYSQL_DATABASE: employees

---
apiVersion: v1
kind: Pod
metadata:
  name: mysql-env-cm
spec:
  containers:
    - name: mysql
      image: mysql:8
      envFrom:
        - configMapRef:
            name: mysql-config
            optional: false
      ports:
        - containerport: 3306
```
* 8.Create a nop commerce deployment with MySQL statefulset and nop deployment