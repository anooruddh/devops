# Difference between Docker and Kubernetes

Docker is a containerization platform, and Kubernetes is a container orchestrator for container platforms like Docker. 

# Kubernetes architecture and its components

![Screenshot](kubernetes_cluster.png)

---------------------------------------------------------------------------------------------------------------------------------------------------------------

![Screenshot](k8arch.jpg)

# Kubernetes Notes

## Master Node Components
1. API Server 2. Controller Manager 3. ETCD 4. Scheduler

## Kubernetes Scheduler

The Kubernetes scheduler stores the resource usage data for each compute node; determines whether a cluster is healthy; and determines whether new containers should be deployed, and if so, where they should be placed. The scheduler considers the health of the cluster generally alongside the pod’s resource demands, such as CPU or memory. Then it selects an appropriate compute node and schedules the task, pod, or service, taking resource limitations or guarantees, data locality, the quality of the service requirements, anti-affinity and affinity specifications, and other factors into account.

## Kubernetes Controller Manager

There are various controllers in a Kubernetes ecosystem that drive the states of endpoints (pods and services), tokens and service accounts (namespaces), nodes, and replication (autoscaling). The controller manager—sometimes called cloud controller manager or simply controller—is a daemon which runs the Kubernetes cluster using several controller functions.

The controller watches the objects it manages in the cluster as it runs the Kubernetes core control loops. It observes them for their desired state and current state via the API server. If the current and desired states of the managed objects don’t match, the controller takes corrective steps to drive object status toward the desired state. The Kubernetes controller also performs core lifecycle functions.

## ETCD

Distributed and fault-tolerant, etcd is an open source, key-value store database that stores configuration data and information about the state of the cluster. etcd may be configured externally, although it is often part of the Kubernetes control plane.

etcd stores the cluster state based on the Raft consensus algorithm. This helps cope with a common problem that arises in the context of replicated state machines and involves multiple servers agreeing on values. Raft defines three different roles: leader, candidate, and follower, and achieves consensus by electing a leader.

In this way, etcd acts as the single source of truth (SSOT) for all Kubernetes cluster components, responding to queries from the control plane and retrieving various parameters of the state of the containers, nodes, and pods. etcd is also used to store configuration details such as ConfigMaps, subnets, and Secrets, along with cluster state data.

## Kubernetes Node Components
1.  Docker  2.  Container Runtime 3.  Kubelet 4.  Kubernetes Proxy Service

## Nodes

A Kubernetes cluster must have at least one compute node, although it may have many, depending on the need for capacity. Pods orchestrated and scheduled to run on nodes, so more nodes are needed to scale up cluster capacity.

Nodes do the work for a Kubernetes cluster. They connect applications and networking, compute, and storage resources.

Nodes may be cloud-native virtual machines (VMs) or bare metal servers in data centers.

Container Runtime Engine

Each compute node runs and manages container life cycles using a container runtime engine. Kubernetes supports Open Container Initiative-compliant runtimes such as Docker, CRI-O, and rkt.

## Kubelet service

Each compute node includes a kubelet, an agent that communicates with the master or control plane to ensure the containers in a pod are running. When the control plane requires a specific action happen in a node, the kubelet receives the pod specifications through the API server and executes the action. It then ensures the associated containers are healthy and running.

## Kube-proxy service

Each compute node contains a network proxy called a kube-proxy that facilitates Kubernetes networking services. The kube-proxy either forwards traffic itself or relies on the packet filtering layer of the operating system to handle network communications both outside and inside the cluster.

The kube-proxy runs on each node to ensure that services are available to external parties and deal with individual host subnetting. It serves as a network proxy and service load balancer on its node, managing the network routing for UDP and TCP packets. In fact, the kube-proxy routes traffic for all service endpoints.

## Pods

Until now, we have covered concepts that are internal and infrastructure-focused. In contrast, pods are central to Kubernetes because they are the key outward facing construct that developers interact with.

A pod represents a single instance of an application, and the simplest unit within the Kubernetes object model. However, pods are central and crucial to Kubernetes. Each pod is composed of a container or tightly coupled containers in a series that logically go together, along with rules that control how the containers run.

Pods have a limited lifespan and eventually die after upgrading or scaling back down. However, although they are ephemeral, pods can run stateful applications by connecting to persistent storage.

Pods are also capable of horizontal autoscaling, meaning they can grow or shrink the number of instances running. They can also perform rolling updates and canary deployments.

Pods run together on nodes, so they share content and storage and can reach other pods via localhost. Containers may span multiple machines, so pods may as well. One node can run multiple pods, each collecting multiple containers.

The pod is the core unit of management in the Kubernetes ecosystem and acts as the logical boundary for containers that share resources and context. Differences in virtualization and containerization are mitigated by the pod grouping mechanism, which enables running multiple dependent processes together.

Achieve scaling in pods at runtime by creating replica sets, which deliver availability by constantly maintaining a predefined set of pods, ensuring that the deployment always runs the desired number. Services can expose a single pod or a replica set to external or internal consumers.

Services associate specific criteria with pods to enable their discovery. Pods and services are associated through key-value pairs called selectors and labels. Any new match between a pod label and selector will be discovered automatically by the service.

--------------------------------------------------------------------------------------------------------------------------------------------------------------------

## The Control plane (master)
The Control plane is made up of the kube-api server, kube scheduler, cloud-controller-manager and kube-controller-manager. 
Kube proxies and kubelets live on each node, talking to the API and managing the workload of each node.

Cloud-controller-manager
The cloud-controller-manager runs in the control plane as a replicated set of processes (typically, these would be containers in Pods). 
The cloud-controller-manager is what allows you to connect your clusters with the cloud provider’s API and only runs controllers specific to the cloud provider you’re using.

Just note: If you’re running Kubernetes on-prem, your cluster won’t have a cloud-controller-manager.


## Etcd
etcd is a distributed key-value store and the primary datastore of Kubernetes. It stores and replicates the Kubernetes cluster state.

To run etcd, you first need to have a Kubernetes cluster and the command-line tool configured to communicate with said cluster.

## Kubelet
The kubelet functions as an agent within nodes and is responsible for the runnings of pod cycles within each node. Its functionality is watching for new or changed pod specifications from master nodes and ensuring that pods within the node that it resides in are healthy, and the state of pods matches the pod specification.

## Kube-proxy
Kube-proxy is a network proxy that runs on each node. It maintains network rules, which allow for network communication to Pods from network sessions inside or outside of a cluster. Kube-proxy is used to reach kubernetes services in addition to load balancing of services.

## Kube-controller-manager
The Kubernetes controller manager is a collection of controllers bundled within a single binary and run in a single process. The following controllers are present within this manager:

>  NODE CONTROLLER:            *Responsible for identifying changes in nodes within the cluste* 

>  REPLICATION CONTROLLER:     *Responsible for maintaining replications of objects in the cluster (such as replicasets)*

>  ENDPOINT CONTROLLER:        *Responsible for provisioning of endpoints (such as service endpoints)*

Service account and token controllers: Responsible the management of service accounts within each namespace, as well as API access tokens


## Features which Kubernetes provides - 

Service Discovery and load balancing: Kubernetes has a feature which assigns the containers with their own IP addresses and a unique DNS name, which can be used to balance the load on them.

- [x] Planning & Placement: Placement of the containers on the node is a crucial feature on which makes the decision based on the resources it requires and other restrictions.
- [x] Auto Scaling: Based on the CPU usage, the vertical scaling of applications is automatically triggered using the command line.
- [x] Self Repair: This is a unique feature in the Kubernetes which will restart the container automatically when it fails. If the Node dies, then containers are replaced or re-planned on the other Nodes. You can stop the containers if they don't respond to the health checks.
- [x] Storage Orchestration: This feature of Kubernetes enables the user to mount the network storage system as a local file system.
- [x] Batch execution: Kubernetes manages both batch and CI workloads along with replacing containers that fail.
- [x] Deployments and Automatic Rollbacks: During the configuration changes for the application hosted on the Kubernetes, progressively monitors the health to ensure that it does not terminate all the instances at once, it makes an automatic rollback only in the case of failure.
- [x] Configuration Management and Secrets: All classifies information like keys and passwords are stored under module called Secrets in Kubernetes. These Secrets are used especially while configuring the application without having to reconstruct the image.


## KUBERNETES CLI-COMMANDS

### install hyperhit and minikube

    brew update

    brew install hyperkit

    brew install minikube

    kubectl

    minikube

### create minikube cluster

    minikube start --vm-driver=hyperkit

    kubectl get nodes

    minikube status

    kubectl version

### delete cluster and restart in debug mode

    minikube delete

    minikube start --vm-driver=hyperkit --v=7 --alsologtostderr

minikube status

### kubectl commands

    kubectl get nodes

    kubectl get pod

    kubectl get services

    kubectl create deployment nginx-depl --image=nginx

    kubectl get deployment

    kubectl get replicaset

    kubectl edit deployment nginx-depl


### debugging

    kubectl logs {pod-name}

    kubectl exec -it {pod-name} -- bin/bash


### create mongo deployment

    kubectl create deployment mongo-depl --image=mongo

    kubectl logs mongo-depl-{pod-name}

    kubectl describe pod mongo-depl-{pod-name}

### delete deplyoment

    kubectl delete deployment mongo-depl

    kubectl delete deployment nginx-depl

### create or edit config file

    vim nginx-deployment.yaml

    kubectl apply -f nginx-deployment.yaml

    kubectl get pod

    kubectl get deployment

### delete with config

    kubectl delete -f nginx-deployment.yaml

### Metrics

    kubectl top 

The kubectl top command returns current CPU and memory usage for a cluster’s pods or nodes, or for a particular pod or node if specified.

### kubectl apply commands in order
    
    kubectl apply -f mongo-secret.yaml
    kubectl apply -f mongo.yaml
    kubectl apply -f mongo-configmap.yaml 
    kubectl apply -f mongo-express.yaml

### kubectl get commands

    kubectl get pod
    kubectl get pod --watch
    kubectl get pod -o wide
    kubectl get service
    kubectl get secret
    kubectl get all | grep mongodb

### kubectl debugging commands

    kubectl describe pod mongodb-deployment-xxxxxx
    kubectl describe service mongodb-service
    kubectl logs mongo-express-xxxxxx

### give a URL to external service in minikube

    minikube service mongo-express-service
    
# Deployment(s) in Kubernetes

In Kubernetes, a deployment is a method of launching a pod with containerized applications and ensuring that the necessary number of replicas is always running on the cluster. 

In Kubernetes, most service-style applications use Deployments to run applications on Kubernetes. Using Deployments, you can describe how to run your application container as a Pod in Kubernetes and how many replicas of the application to run. Kubernetes will then take care of running as many replicas as specified.

Here’s an example deployment manifest in YAML format for running three instances of a simple hello world web app:

    apiVersion: apps/v1
    kind: Deployment
    metadata:
      labels:
        app: myapp
      name: myapp
    spec:
      replicas: 3
      selector:
        matchLabels:
          app: myapp
      template:
        metadata:
          labels:
            app: myapp
        spec:
          containers:
          - image: polarsquad/hello-world-app:master
            name: hello-world
            ports:
            - containerPort: 3000
One of the key features of Deployments is how it manages application updates. By default, updating the Deployment manifest in Kubernetes causes the application to be updated in a rolling fashion. This way you’ll have the previous version of the deployment running while the new one is brought up. In the Deployment manifest, you can specify how many replicas to bring up and down at once during updates.

For example, we can add a rolling update strategy to the spec section of the manifest where we bring one replica up at a time, and make sure there are no missing healthy replicas at any point during the upgrade.

    spec:
     strategy:
       type: RollingUpdate
       rollingUpdate:
         maxUnavailable: 0
         maxSurge: 1
 
To automate the process, you might choose to deploy your app in your CI pipeline using kubectl.
 
    kubectl apply -f deployment.yaml

Kubectl apply does not verify that your application even starts.

In order to properly check that the update proceeds as expected, we need assistance from another kubectl command.

This is where kubectl’s rollout command becomes handy! We can use it to check how our deployment is doing.

By default, the command waits until all of the Pods in the deployment have been started successfully. When the deployment succeeds, the command exits with return code zero to indicate success.

    $ kubectl rollout status deployment myapp
    Waiting for deployment "myapp" rollout to finish: 0 of 3 updated replicas are available…
    Waiting for deployment "myapp" rollout to finish: 1 of 3 updated replicas are available…
    Waiting for deployment "myapp" rollout to finish: 2 of 3 updated replicas are available…
    deployment "myapp" successfully rolled out


#   Kubernetes deployment strategies

In Kubernetes there are a few different ways to release an application, it is necessary to choose the right strategy to make your infrastructure reliable during an application update.

Choosing the right deployment procedure depends on the needs, we listed below some of the possible strategies to adopt:

**recreate**: terminate the old version and release the new one
**ramped**: release a new version on a rolling update fashion, one after the other
**blue/green**: release a new version alongside the old version then switch traffic
**canary**: release a new version to a subset of users, then proceed to a full rollout

![Screenshot](deployment-decision-diagram.png)

# Service(s) in Kubernetes

A Kubernetes service is a logical abstraction for a deployed group of pods in a cluster (which all perform the same function).

Since pods are ephemeral, a service enables a group of pods, which provide specific functions (web services, image processing, etc.) to be assigned a name and unique IP address (clusterIP). As long as the service is running that IP address, it will not change. Services also define policies for their acces

# In Kubernetes, what is the difference between a service and a deployment?

![Screenshot](deployment-service.png)

   In Kubernetes, a deployment is a method of launching a pod with containerized applications and ensuring that the necessary number of replicas is always running on the  cluster. 

   On the other hand, a service is responsible for exposing an interface to those pods, which enables network access from either within the cluster or between external processes and the service.

#   Types of Kubernetes services

 
**ClusterIP**.  Exposes a service which is only accessible from within the cluster.

![Screenshot](clusterip.jpg)

**NodePort**.   Exposes a service via a static port on each node’s IP.

![Screenshot](nodeport.jpg)

**LoadBalancer**. Exposes the service via the cloud provider’s load balancer.

![Screenshot](loadbalancer.jpg)

**ExternalName**. Maps a service to a predefined externalName field by returning a value for the CNAME record.

**Ingress**. This service allows the routing of HTTP(S) traffic according to defined rules like path-based routings. This can be associated with one or more service objects where these services are further associated with Pods. The ingress controller creates HTTP(S) load balancer Layer 7 load balancer which are configured automatically using the definition in the Ingress object.

![Screenshot](ingress.jpg)


# What is a headless service?

A headless service is a service with a service IP but instead of load-balancing it will return the IPs of our associated Pods. This allows us to interact directly with the Pods instead of a proxy. It's as simple as specifying None for .spec.clusterIP and can be utilized with or without selectors

    apiVersion: v1
    kind: Service
    metadata:
      name: my-headless-service
    spec:
      clusterIP: None # <--
      selector:
        app: test-app
      ports:
        - protocol: TCP
          port: 80
          targetPort: 3000 

Headless-services allow us to reach each Pod directly, rather than the service acting as a load-balancer or prox

#   How do Kubernetes services work

   Services simply point to pods using labels. Since services are not node-specific, a service can point to a pod regardless of where it runs in the cluster at any given moment in time. By exposing a service IP address as well as a DNS service name, the application can be reached by either method as long as the service exists.
    
#   Creating service using yml file
    
   Services are defined in YAML, as are all Kubernetes objects. Suppose you deployed pods running a back-end service to process data coming from a web front end. To expose a service named ‘service-backend’ on the deployment ‘deployment-backend’ you would use:
    
    apiVersion: v1
    kind: Service
    metadata:
    name: service-backend
    spec:
    ports:
    - port: 4000
    protocol: TCP
    targetPort: 333
    selector:
    run: deployment-backend
    type: ClusterIP
    
   The service ‘service-backend’ will be created, and any pod in the cluster can access it on their port 333 via http://service-backend:4000, or at the cluster’s IP address using port 4000.


   Kubernetes services can also be created using the ‘kubectl expose’ command, which does not require a YAML file. The same service can be created using the command:

#   Creating service using command

    kubectl expose deployment deployment-backend  - - port=333- - target-port=4000    - - name=service-backend
   
#   Access a Kubernetes service
   
   There are two ways to discover a Kubernetes service:
   
   **DNS (most common)**: The DNS method is the recommended method of discovering services. To use this method, a DNS server must first be installed on the cluster. The DNS server monitors the Kubernetes API, and when a new service is created its name becomes available for easy resolution for requesting applications. 
    
   **ENV variable**: This method relies on the kubelet adding environment variables for each active service for every node a pod is running on.

#   Pause Container

Pause Container holds the network namespace for the pod. Kubernetes creates pause containers to acquire the respective pod’s IP address and set up the network namespace for all other containers that join that pod.

 Pause container make sure that there is a network stack to map, and it doesn’t change. Pause containers are a pod implementation detail where one pause container is used for each pod and shouldn’t be shown when listing containers that are members of pods

Pause containers hold the cgroups, reservations, and namespaces of a pod before its individual containers are created. The pause container’s image is always present, so the allocation of the pod’s resources is instantaneous.

 By default, pause containers are hidden, but you can see them by running docker ps -a.
