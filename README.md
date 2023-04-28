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