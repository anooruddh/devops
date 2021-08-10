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

## install hyperhit and minikube

brew update

brew install hyperkit

brew install minikube

kubectl

minikube

## create minikube cluster

minikube start --vm-driver=hyperkit

kubectl get nodes

minikube status

kubectl version

## delete cluster and restart in debug mode

minikube delete

minikube start --vm-driver=hyperkit --v=7 --alsologtostderr

minikube status

## kubectl commands

kubectl get nodes

kubectl get pod

kubectl get services

kubectl create deployment nginx-depl --image=nginx

kubectl get deployment

kubectl get replicaset

kubectl edit deployment nginx-depl


## debugging

kubectl logs {pod-name}

kubectl exec -it {pod-name} -- bin/bash


## create mongo deployment

kubectl create deployment mongo-depl --image=mongo

kubectl logs mongo-depl-{pod-name}

kubectl describe pod mongo-depl-{pod-name}

## delete deplyoment

kubectl delete deployment mongo-depl

kubectl delete deployment nginx-depl

## create or edit config file

vim nginx-deployment.yaml

kubectl apply -f nginx-deployment.yaml

kubectl get pod

kubectl get deployment

## delete with config

kubectl delete -f nginx-deployment.yaml

#Metrics

kubectl top 

The kubectl top command returns current CPU and memory usage for a cluster’s pods or nodes, or for a particular pod or node if specified.
