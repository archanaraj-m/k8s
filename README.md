Kubernetes practise:
-------------------
# Kubernetes (K8s)(devops 23april2023 morning class and evening class refer)
* Need for K8s
* High Availability (HA):
   * When we run our applications in docker container and if the container fails, we need to manually start the container
   *  If the node i.e. the machine fails all the containers running on the machine should be re-created on other machine
   * K8s can do both of the above
* Autoscaling
   * Containers don’t scale on their own.
   * Scaling is of two types
        * Vertical Scaling
        * Horizontal Scaling
   * K8s can do both horizontal and vertical scaling of containers

Kubernetes
----------
Kubernetes also known as K8s, is an open-source system for automating deployment, scaling, and management of containerized applications.

K8s described as Production grade container management

Kubernetes Architecture
-------------------------

* Pod
A group of one or more containers.The smallest unit of k8s.The container has no ip address Pod has an IP address.
If the pod fails, then that pod will not be created again, another new pod will be created and its IP will be different.
* kubelet
Kublet is a small, lightweight Kubernetes node agent that runs on each node in a Kubernetes cluster.
It's responsible for managing the nodes and communicating with the Kubernetes master.
It's also responsible for making sure that the containers running on the nodes are healthy and running correctly.
* Kube-proxy
Kube-proxy is a network proxy service for Kubernetes that is responsible for routing traffic to different services within the cluster.
It is responsible for forwarding traffic from one service to another, allowing for communication between different components of the Kubernetes cluster.
* Service
In Kubernetes, a service is an object that abstracts the underlying infrastructure and provides a unified access point for the applications that are running on the cluster.
Services allow the applications to communicate with each other and are used to provide load balancing and service discovery.
* cluster
In Kubernetes, a cluster is a set of nodes (physical or virtual machines) that are connected and managed by the Kubernetes software.
* Container Engine(Docker, Rocket, ContainerD)
A container engine is a software system that enables applications and services to be packaged and run in an isolated environment.
Docker, Rocket, and Container are all examples of container engines that are used to run applications in containers.
* API Server (Application Programeble Interface)
The API Server is the entry point of K8S Services.
The Kubernetes API server receives the REST commands which are sent by the user.
After receiving them, it validates the REST requests, processes them, and then executes them. After the execution of REST commands, the resulting state of a cluster is saved in 'etcd' as a distributed key-value store.
This API server is meant to scale automatically as per load.
* ETCD
etcd is a consistent and highly-available key value store used as Kubernetes’ backing store for all cluster data. If your Kubernetes cluster uses etcd as its backing store, make sure you have a back up plan for those data. You can find in-depth information about etcd in the official documentation.

* Controller Manager
The Kubernetes Controller Manager (also called kube-controller-manager) is a daemon that acts as a continuous control loop in a Kubernetes cluster.
The controller monitors the current state of the cluster via calls made to the API Server and changes the current state to match the desired state described in the cluster’s declarative configuration.
* Scheduler
The scheduler in a master node schedules the tasks for the worker nodes.
And, for every worker node, it is used to store the resource usage information.
* What is kubectl stand for?
Kubectl stands for Kubernetes Command-line interface. It is a command-line tool for the Kubernetes platform to perform API calls.
Kubectl is the main interface that allows users to create (and manage) individual objects or groups of objects inside a Kubernetes cluster.
Kubernetes resources are defined by a manifest file written in YAML. When the manifest is deployed, an object is created that aims to reach the desired state within the cluster. From that point, the appropriate controller watches the object and updates the cluster’s existing state to match the desired state.

k8s install commands:
* First we can install docker in all nodes
* then install cri-dockered

```
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh

git clone https://github.com/Mirantis/cri-dockerd.git

cd cri-dockerd
mkdir bin
VERSION=$((git describe --abbrev=0 --tags | sed -e 's/v//') || echo $(cat VERSION)-$(git log -1 --pretty='%h')) PRERELEASE=$(grep -q dev <<< "${VERSION}" && echo "pre" || echo "") REVISION=$(git log -1 --pretty='%h')
go build -ldflags="-X github.com/Mirantis/cri-dockerd/version.Version='$VERSION}' -X github.com/Mirantis/cri-dockerd/version.PreRelease='$PRERELEASE' -X github.com/Mirantis/cri-dockerd/version.BuildTime='$BUILD_DATE' -X github.com/Mirantis/cri-dockerd/version.GitCommit='$REVISION'" -o cri-dockerd

# Run these commands as root
###Install GO###
wget https://storage.googleapis.com/golang/getgo/installer_linux
chmod +x ./installer_linux
./installer_linux
source ~/.bash_profile

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
```
[Referhere](https://github.com/Mirantis/cri-dockerd)
* Follow same commands in the above documentation.



# scaling up and down
# scaling in and out
# in k8s everthing stores in etcd controllers