# Create a Kubernetes cluster using kubeadm
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
* next create ``vi k8s.yml`` for install kubeadm through shell scripting
* next in that vi file paste below commands
```


* After install docker in that 2 nodes exit and relogin because we can give usermod permissions.
* After successful installation re-login into your machine
* After re-login try to get docker info $ docker info
Deploy any application using kubectl
Backup Kubernetes I.e backup etcd
List out all the pod’s running in kube system namespace
Write down all the steps required to make Kubernetes highly available
Do a rolling update and roll back
Ensure usage of secret in MySQL and configmaps
Create a nop commerce deployment with MySQL statefulset and nop deployment