Ingress:
--------
* What is Kubernetes Ingress and why is it useful?
Kubernetes Ingress is an API object that provides routing rules to manage external users' access to the services in a Kubernetes cluster, typically via HTTPS/HTTP. With Ingress, you can easily set up rules for routing traffic without creating a bunch of Load Balancers or exposing each service on the node. This makes it the best option to use in production environments. 
![preview](./k8s_images/k8s145.png)
[referhere](https://www.ibm.com/cloud/blog/kubernetes-ingress) for ingress documentation.
[referhere](https://kubernetes.io/docs/concepts/services-networking/ingress/)
* Traefik:[referhere](https://doc.traefik.io/traefik/getting-started/concepts/)
Traefik is based on the concept of EntryPoints, Routers, Middelwares and Services.

* The main features include dynamic configuration, automatic service discovery, and support for multiple backends and protocols.

* EntryPoints: EntryPoints are the network entry points into Traefik. They define the port which will receive the packets, and whether to listen for TCP or UDP.

* Routers: A router is in charge of connecting incoming requests to the services that can handle them.

* Middlewares: Attached to the routers, middlewares can modify the requests or responses before they are sent to your service

* Services: Services are responsible for configuring how to reach the actual services that will eventually handle the incoming requMany different rules

Edge Router
-----------
* Traefik is an Edge Router, it means that it's the door to your platform, and that it intercepts and routes every incoming request: it knows all the logic and every rule that determine which services handle which requests (based on the path, the host, headers, etc.).
![preview](./k8s_images/k8s143.png)

Auto Service Discovery
----------------------
* Where traditionally edge routers (or reverse proxies) need a configuration file that contains every possible route to your services, Traefik gets them from the services themselves.
* Deploying your services, you attach information that tells Traefik the characteristics of the requests the services can handle.
![preview](./k8s_images/k8s144.png)

* In the example above, we used the request path rule to determine which service was in charge. Certainly, you can use many other different rules.

* How does Traefik discover the services?
Traefik is able to use your cluster API to discover the services and read the attached information. In Traefik, these connectors are called providers because they provide the configuration to Traefik.

* Using Traefik for Business Applications?
If you are using Traefik in your organization, consider Traefik Enterprise. You can use it as your:
API Gateway
Kubernetes Ingress Controller
Docker Swarm Ingress Controller
Traefik Enterprise simplifies the discovery, security, and deployment of APIs and microservices across any environmentests.
* 

Activity :1
1. Creating the EKS Cluster or AKS cluster
2. Enabling Cloud Monitoring for the Cluster
EKS logs: Viewing Logs and Metrics on the CloudWatch Dashboard for EKS
AKS logs: Once the monitoring is enabled, you can access the Azure Monitor console and navigate to the Log Analytics workspace or the AKS cluster's monitoring section to view the logs and metrics. 
Activity :2 
1. After Activity 1 then enable ELK for K8s Monitoring
2. create sample Dashboard