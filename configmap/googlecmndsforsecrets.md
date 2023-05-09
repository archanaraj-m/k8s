# 1- Specifying ImagePullSecrets directly on the pod spec:
* First, create a pull secret with docker Credentials:

kubectl create secret docker-registry my-registry-cred \
--docker-server=[REGISTRY_URL] --docker-username=[REGISTRY_USERNAME] --docker-password=[REGISTRY_PASSWORD] --docker-email=[YOUR_EMAIL] 
* Alternatively you can create the secret from the docker config file directly:
kubectl create secret generic my-registry-cred \
    --from-file=.dockerconfigjson=<path/to/.docker/config.json> \
    --type=kubernetes.io/dockerconfigjson


```yml
---
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: private
    image: docker.io/archanaraj/nop:latest
  imagePullSecrets:
  - name: my-registry-credentials
```

```yml
  ---
  apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  serviceAccountName: my-service
  containers:
  - name: private
    image: docker.io/archanaraj/nop:latest
