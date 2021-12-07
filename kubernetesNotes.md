## Concepts

### What is Kubernetes

* Open source container orchestration tool
* Originally developed by google

### Features 

* High Availability
* Scalibilty
* Disaster Recovery

### Docker

But there’s more. Both instructions support two different forms: 

 shell form—For example, ENTRYPOINT node app.js.

 exec form—For example, ENTRYPOINT ["node", "app.js"].

## Components

### Node

A physical or a virtual machine

Master node :

* API Server cluster gateway, authenticaiton, forward request to corresponding services
* Scheduler  decide where to shedule new pods
* Controll Manager detect state changes
* etcd the store of cluster state

Each worker node has 3 processes

* Container runtime(e.g. docker, containerd)
* kuberlet , kubernetes agent to interact the container and nodes
* kubeproxy



### Pods

A Pod a set of collocated containers.

Kubernetes achieves this by configuring Docker to have all containers of a pod **share the same set of Linux namespaces instead of each container having its own set**.

All pods in a Kubernetes cluster reside in a single flat, shared, network-address space

*Pods* are the **smallest deployable units of computing** that you can create and manage in Kubernetes, A pod contains one or more containers that **share the storage/network resources, namespace** , and specification on how to run these containers

Pods are normally not created directly but with workload resource

* Abstraction over container
  * Each Pod contains its own IP
* Kubernetes gives  a single DNS name for a set of Pods
* Pods are dynamic

### Service

A Service is an abstraction which defines a logical set of Pods and a policy by which to access them.

* Definition : this creates a service with name "my-service" and targets the pods with cable app=MyApp 

  ```yml
  apiVersion: v1
  kind: Service
  metadata:
    name: my-service
  spec:
    selector:
      app: MyApp
    ports:
      - protocol: TCP
        port: 80
        targetPort: 9376
  ```

  Kubernetes assigns a Service an IP address (sometimes called the "**cluster IP**"),

* How it works: the controller for the service selector continuesly scan for pods that matches its selector and updates the service object with name "my-service"

The motivation of service is to decouple the Pods and the clients that used the pod services, since Pods come and go, where this should be transparent to clients, client should be care which IP address(es) to connect.









Each Pod has its own IP address, once it get recreated, it will have different IP address,



* Stable IP Addresses
* Services and Pods are idependent
* Pods communicate through services

#### Publishing Service

If we need to expose k8s service onto an external IP address, which is outside the cluster, k8s has 

Service types

* Cluster IP service (Internal service)  :Exposes the Service on a cluster-internal IP

  , service uses the "selector" to match pod's metadata key/value pairs.

  ```yaml
  # service-config.yaml
  selector:
  	app: my-app
  	type: microservice
  ports:
   - name: mongoDB-port
     protocol: TCP
     port: 27017
     targetPort: 27017
   - name: mongoDB-export-port
     protocol: TCP
     port: 9216
     targetPort: 9216
  
   
  # my-app-config.yaml
  labels:
  	app: my-app
  	type: microservice
  	
  ```

* Headless service 

  It is used for discovering individual pods(especially IPs) which allows another service **to interact directly with the Pods instead of a proxy**, dns lookup will return a set of records that point to indivi

   pods, instead of single IP of like in clusterIP service. 

  

  Used for clients to talk specific pod directly, the reason is for stateful applications, pod replicas are not identical,(e.g. one is maseter , others are slaves)

  set the clusterIP to None

  ```yaml
  spec:
   clusterIP: None
  ```

* NodePort Service expose node port to outside,  it exposes the Service **on each Node's IP at a static port (the `NodePort`)**

  . A `ClusterIP` Service, to which the `NodePort` Service routes, is automatically created. You'll be able to contact the `NodePort` Service, from outside the cluster, by requesting NodeIP:NodePort.

  It  is usually used for testing, not for external connections in production
  
  ```yaml
  spec:
  	type: NodePort
  	selector: 
  		app: my-app
  	ports:
     - protocol: TCP
       port: 3200
       targetPort :3000
       nodePort: 30000 # must be in range of 30000-32767
  	
  ```
  
  It creates the port 30000 on each Node IP
  
* LoadBalancer services --  Exposes the Service externally using a cloud provider's load balancer. `NodePort` and `ClusterIP` Services, to which the external load balancer routes, are automatically created.load 

  ```yaml
  spec:
  	type: LoadBalancer
  	selector: 
  		app: my-app
  	ports:
     - protocol: TCP
       port: 3200
       targetPort :3000
       nodePort: 30000 # must be in range of 30000-32767
  
  ```

  

* K8s create endpoint objects with the same name of services to keep track of which pod are the member of endpoints

### DNS

k8s creates DNS records for services and pods.

"Normal" (not headless) Services are assigned a DNS A or AAAA record, depending on the IP family of the service, 

### Ingress

* Route trafic to cluster

```yaml
api: k8s.io/v1beta1
kind: Ingress
metadata:
	name: myapp-ingress
spec:
	rules: # routing rules
	- host: myapp.com  
		http:
			paths: 
			- backend:
					serviceName: app-internal-service  # clusterIP service name
					servicePort:
```



## Ingress Controller

* Entrypoint to K8s cluster
* Evaluate  the rules and manage redirections
* Many third-party implementations, one out of box implementations is k8s nginx )

Public traffice must go through cloud load balancer or web proxy server to  connect Ingress controller, which normally run on its own pod.

```shell
# it starts the k8s nginx ingress controller
minikube addons enable ingress

```



### ConfigMap and Secret

* Externalized application configuration
* secret used to store password

### Volume

Kubernetes volumes are a component of a pod and are thus defined in the pod’s spec- ification—much like containers.

* Data Storage

### Deployments

* Blueprint for your app-pods
* Used for **stateless apps**	
* Abstraction over pods
* Users works with deployments instead of Pods

### StatefulSet

* Used for stateful apps such as databases; but databased are usually hosted outsied k8s cluster

### Replicaset

Replicaset manages the replication of pods

## Minikube

MiniKube a k8s cluster that runs on a single machine , it

* Creates a virtual box VM
* VM has all the processes to run both master and worker
* for testing purpose



## Common commands

```shell
kubectl create deployment <name> --image=<dockerimageName>
kubectl apply -f <deploymentconfigurationFile>

# replicaset
kubectl get replicaset

# pod
kubectl get pod <podname> -o wide # will show IP address

# login to conainter
kubectl exec -it 	<podname> --bin/bash
```

