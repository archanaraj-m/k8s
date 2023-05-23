JOIP Task for (kubernetes trained batch)
----------------------------------------
1. Installing EKS and Aks
EKS Cluster
-----------
* Elastic kubernetes Services is a managed k8s from aws
* EKS cluster can be created in many ways
       * aws console
       * aws cli
       * terraform
       * eksctl this will be used
* Features [referhere](https://aws.amazon.com/eks/features/)
* Create a linux instance, install aws cli, create iam credentials
* After connecting the ubuntu ``sudo apt update``&&``sudo apt install unzip -y``
![preview](./k8s_images/k8s101.png)
* For install awscli
```
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```
* Next check it ``aws --version`` and ``awsconfigure``
* install kubectl [Refer Here](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/#install-using-other-package-management)
* we had followed direct installation [Refer Here](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/#install-kubectl-binary-with-curl-on-linux)
* For install kubectl command is``curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl" ``&&``sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl``
* After install kubectl check ``kubectl version``
![preview](./k8s_images/k8s102.png)
* Install eksctl [Refer Here](https://eksctl.io/introduction/#for-unix)
```bash
# for ARM systems, set ARCH to: `arm64`, `armv6` or `armv7`
ARCH=amd64
PLATFORM=$(uname -s)_$ARCH

curl -sLO "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$PLATFORM.tar.gz"

# (Optional) Verify checksum
curl -sL "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_checksums.txt" | grep $PLATFORM | sha256sum --check

tar -xzf eksctl_$PLATFORM.tar.gz -C /tmp && rm eksctl_$PLATFORM.tar.gz

sudo mv /tmp/eksctl /usr/local/bin
```
![preview](./k8s_images/k8s103.png)
* before that cluster creation with yml file we have to do aws configure and kubectl configure must.
* Create a file called as cluster.yml with the following content
```yml
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig

metadata:
  name: basic-cluster
  region: us-west-2

nodeGroups:
  - name: basic
    instanceType: t2.large
    desiredCapacity: 2
    volumeSize: 20
    ssh:
      allow: true # will use ~/.ssh/id_rsa.pub as the default ssh key
```
* before creating cluster we execute ssh-keygen ``ssh-keygen``
* Now execute the command ``eksctl create cluster -f cluster.yml``
* After creation execute``kubectl get nodes``&&``kubectl get pods --all-namespaces``
![preview](./k8s_images/k8s104.png)

Helm chart
----------
* creating helm chart commands are
```
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
```
![preview](./k8s_images/k8s105.png)
* After that run this command ``helm install my-release oci://registry-1.docker.io/bitnamicharts/wordpress``
* [referhere](https://bitnami.com/stack/wordpress/helm)(googlesearch-helm wordpress)
![preview](./k8s_images/k8s106.png)

* for uninstall the helm command is ``helm uninstall my-release oci://registry-1.docker.io/bitnamicharts/wordpress``
* creating helm chart [referhere](https://phoenixnap.com/kb/create-helm-chart)
* command is ``helm create <chartname>``
![preview](./k8s_images/k8s107.png)
* For install the hem chart ``helm install <chart name> <flagname>`` like ``helm install nop nop/``
* when we create the helm chart this files are autometically in that helm chart 
values.yaml,charts,chatrs.yaml,templetes.
![preview](./k8s_images/k8s108.png)
* In the above process coreect bit ngnix page not came because i did not change clusterIP type in values.yaml so that page not came. 
* now i got to ``cd nop`` then change the type name as LoadBalncer``vi values.yaml``
* In the ``vi values.yaml`` we can change <type: LoadBalncer>
![preview](./k8s_images/k8s109.png)
* so again i create another helm chart with name nop1
![preview](./k8s_images/k8s110.png)
![preview](./k8s_images/k8s111.png)
* For deleting the helmchart ``helm delete <chartname>``
![preview](./k8s_images/k8s112.png)
1. installations using helm chart
  1. Mysql
* [referhere](https://dev.mysql.com/doc/mysql-operator/en/mysql-operator-installation-helm.html)
![preview](./k8s_images/k8s113.png)

  2. PostgreSql
* [referhere]()
        1. mongoDB
        2. Redis cache
1. make Mysql as Stateful set
2. write headless service for Mysql1
3. write kostomize file by creating files for 3 environments
        1.dev-environment
        2.qa-environment
        3.test-environment
4. every environment should have their own secrets