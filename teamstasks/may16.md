JOIP Task for (kubernetes trained batch)(16-05-2023)
---------------------------------------------------
1. Installing EKS and Aks
2. installations using helm chart
        1. Mysql
        2. PostgreSql
        3. mongoDB
        3. Redis cache
3. make Mysql as Stateful set
4. write headless service for Mysql1
5. write kostomize file by creating files for 3 environments
        1.dev-environment
        2.qa-environment
        3.test-environment
6. every environment should have their own secrets
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
* Create a linux instance(t2micro), install aws cli, create iam credentials
* After connecting the ubuntu ``sudo apt update``&&``sudo apt install unzip -y``
![preview](./k8s_images/k8s101.png)
* For install awscli
```
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```
* Next check it ``aws --version`` and ``aws configure``
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
* before creating cluster we execute ssh-keygen ``ssh-keygen``
* Create a file called as cluster.yml with the following content
* paste below yaml file in this ``vi cluster.yml``
```yml
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig

metadata:
  name: mycluster # we can give any name it is cluster name
  region: eu-west-3

nodeGroups:
  - name: basic # node group name
    instanceType: t2.medium
    desiredCapacity: 2
    volumeSize: 20
    ssh:
      allow: true # will use ~/.ssh/id_rsa.pub as the default ssh key
```
* before creating cluster we execute ssh-keygen ``ssh-keygen``
* Now execute the command ``eksctl create cluster -f cluster.yml``
* After creation execute``kubectl get nodes``&&``kubectl get pods --all-namespaces``
![preview](./k8s_images/k8s104.png)
# For deleting the cluster ``eksctl delete cluster --name=<name> [--region=<region>]``example ``eksctl delete cluster --name=mycluster --region=eu-west-3``
![preview](./k8s_images/k8s117.png)
* After delete the cluster check in our aws all regions instances,auto scalling groups,volumes.

2.Helm chart
----------
* After creating cluster we can create helm , helm created in that cluster.
* creating helm chart commands are
[referhere](https://helm.sh/docs/intro/install/)

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
# 1. Mysql
* [referhere](https://bitnami.com/stack/mysql/helm)
![preview](./k8s_images/k8s118.png)
![preview](./k8s_images/k8s113.png)
![preview](./k8s_images/k8s114.png)
* For installation mysql helm command is ``helm install my-release oci://registry-1.docker.io/bitnamicharts/mysql``
![preview](./k8s_images/k8s126.png)
* Above preview they are created helm chart and pods created with the namespaces so we can search pods with namespaces`` kubectl get pods -w --namespace default``
![preview](./k8s_images/k8s115.png)
* pod is running so enter to db ``kubectl -n mysql-operator exec -it mysql-operator-79d96c78c9-69lnp -- /bin/sh``
* in that mysql-operator is my repo name
* enter the details in above they are mentioned in that see preview116 
![preview](./k8s_images/k8s116.png)
* the given password is not working error came.
* Afterenter into mysql"I have no  name!@mysql-0:/" ``mysql -u root -p``  then enter into mysql>use employees_database
* To uninstall chart ``helm uninstall <name>``
# 2. PostgreSql
* [referhere](https://bitnami.com/stack/postgresql/helm)
* For installation postgresql helm command is ``helm install my-release oci://registry-1.docker.io/bitnamicharts/postgresql``
![preview](./k8s_images/k8s127.png)
# 3. mongoDB
* [referhere](https://bitnami.com/stack/mongodb/helm)
* For installation mongodb helm command is ``helm install my-release oci://registry-1.docker.io/bitnamicharts/mongodb``
![preview](./k8s_images/k8s128.png)
# 4. Redis cache
* [Referhere](https://bitnami.com/stack/redis/helm)
* For installation redis cache helm command is``helm install my-release-redis oci://registry-1.docker.io/bitnamicharts/redis``
* kostomize is better then helmchart because in kostomize we can use reusable files.
![preview](./k8s_images/k8s129.png)
* For Next type exit. 
3. make Mysql as Stateful set
* ``vi mysql.yml``
```yml
---
apiVersion: apps/v1
kind:	StatefulSet
metadata:
  name: mysql
  labels:
    app: mysql
spec:
  replicas: 1
  serviceName: mysql-svc 
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      name: mysql
      labels:
        app: mysql
    spec:
      containers:
        - name: mysql
          image: mysql:5
          env: 
            - name: MYSQL_ROOT_PASSWORD
              value: password
            - name: MYSQL_USER
              value: Archana
            - name: MYSQL_PASSWORD
              value: password  
            - name: MYSQL_DATABASE
              value: students
          ports:
            - containerPort: 3306
          
---
apiVersion: v1
kind: Service
metadata: 
  name: mysql-svc
spec:
  selector:
    app: mysql
  ports:
    - name: mysql
      port: 31000
      targetPort: 3306  

```
* For enter into my sql database command is ``mysql --user=Archana --password=password`` 
* For pod connecting ``kubectl exec -it <podname> -- /bin/sh``   
* After enter to mysql `` mysql -u Archana -p password``         
![preview](./k8s_images/k8s130.png)
4. write headless service for Mysql1
```yml
---
apiVersion: apps/v1
kind:	StatefulSet
metadata:
  name: mysql
  labels:
    app: mysql
spec:
  replicas: 1
  serviceName: mysql-svc 
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      name: mysql
      labels:
        app: mysql
    spec:
      containers:
        - name: mysql
          image: mysql:5
          env: 
            - name: MYSQL_ROOT_PASSWORD
              value: password
            - name: MYSQL_USER
              value: Archana
            - name: MYSQL_PASSWORD
              value: password  
            - name: MYSQL_DATABASE
              value: students
          ports:
            - containerPort: 3306

---
apiVersion: v1
kind: Service
metadata:
  name: mysql-svc
  labels:
    app: mysql
spec: 
  clusterIP: "None"
  ports:
    - port: 3306
```

5. write kostomize file by creating files for 3 environments
        1.dev-environment
        2.qa-environment
        3.test-environment
kustomize
---------
* For kustomize[Referhere](https://kustomize.io/)
* for kustomize usage[referhere](https://kubectl.docs.kubernetes.io/guides/introduction/kustomize/)  
* kustimize is a tool where we can natively(natively means In particuler system) mange the configurations.
* Natively manage dynamic configurations to k8s manifests
      * create a service file exposed as LoadBalancer
* For kustomization yaml file [referhere](https://www.digitalocean.com/community/tutorials/how-to-manage-your-kubernetes-configurations-with-kustomize)
* After creating cluster create namespaces
* After creating all folders and kustomization files we can create namespaces``kubectl create ns <namespace name>``like this``kubectl create ns dev``(in <namespace name> dev,qt,uat)check namespaces``kubectl get ns``
![preview](./k8s_images/k8s119.png)
* After creating cluster git clone my kubernetes repo in that see preview 
![preview](./k8s_images/k8s120.png)
* For creating  ``kubectl apply -k ./``
* After apply kubectl svc,hpa created but deployment is in-valid error came so i add in helthchecks livenesprob,redinesprob in that add httpget,path,port then ``git pull`` next apply now deployment created.
![preview](./k8s_images/k8s121.png) 
* For apply ``kubectl apply -k overlays/dev``&&``kubectl apply -k overlays/qt``&&``kubectl apply -k overlays/uat``
![preview](./k8s_images/k8s122.png) 
* For deploy ``kubectl get deploy -n dev``
* For pods  ``kubectl get po -n dev``
![preview](./k8s_images/k8s123.png) 
* For delete dev environment``kubectl delete -k -n dev ./`` (in this command if we didn't clone form windows we can mentioned namespace -n dev otherwise we can goto that dev folder then delete for that command is ``kubectl delete -k ./``)
* For see the all resources in dev namespace ``kubectl get all -n dev``
![preview](./k8s_images/k8s124.png) 
* For build ``kubectl build overlays/dev``
* After creating the labels ``kubectl apply -k ./``or``kubectl apply -k overlays/dev``
* For show pods in dev namespaces``kubectl get po --show-labels -n dev``
* For deleting ``kubectl delete -k ./``
* take that external IP nd paste in new tab with port number<externalIP:portnumber>
* like see preview
![preview](./k8s_images/k8s125.png)
* After that delete cluster`` eksctl delete cluster --name=mycluster --region=eu-west-3``  




1. every environment should have their own secrets