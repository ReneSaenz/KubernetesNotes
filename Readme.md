# Kubernetes Notes

## What is Kubernetes

- Kubernetes: greek for captain or pilot
- Inspired from an internal google project called 'Borg'
- Open Source project managed by Linux Foundation
- Has a distributed architecture and is highly API centered
- Decouples applications from hardware via containers
- Automates application configuration through service discovery

## Terminology
- Containers: Units of packaging
- Pod: Units of deployment (contain one or more containers)
- Nodes: Hosts that run applications (contain one or more pods)
- Service: Collection of pods exposed as an endpoint
- Labels: key-value pairs for identification
- Replication Controller: Ensures availability and scalability

### Kubernetes Master
- A host that contains the kubernetes software

### Kubernetes server contains

- Exposes the Kubernetes Server API -> (API, CLI, UI)
- Scheduler: Process to manage pods accross multiple nodes
- Controller: Overall coordination and health of the cluster ensuring the nodes and up and running in the desired configuration state.
- etcd: Central key-value pair DB that stores the cluster state

### Kubernetes Node

- A host that runs an application. Nodes do the heavy lifting
- Responsible for running multiple pods
- A node can contain multiple pods of different sizes

### Kubernetes node contains

- Pod: A group of one or more CONTAINERS that are always co-located, co-scheduled and run in a shared context
- kube-proxy: Responsible for maintaning the network configuration.
- kubelet: Responsible talking to the API, to report the current state of a node to the master
- supervisord: process manager. Allow to run multiple processes inside another process
- fluentd: Responsible for managing logs
- Add-ons: Additional functionality. For example DNS

### Kubernetes Pod

- Documentation
  - [Pod Documentation](http://kubernetes.io/docs/user-guide/pods)
  - [Pod States](http://kubernetes.io/docs/user-guide/pod-states)  (lifecycle)

- Pod: A group of one or more CONTAINERS that are always co-located, co-scheduled and run in a shared context. For example, Redis cache and Apache web server
- A pod models an application-specific "logical host" - It contains one or more application containers which are relatively tightly-coupled

- Containers in the same pod have the same hostname
- Each pod is isolated by
  - Process ID (PID)
  - Network namespace
  - Inter-process communication (IPC) namespace
  - Unix time sharing (UTS) namespace


## Services
- A service is an abstraction to define a logical set of pods bound by a policy to access them (sometimes called microservices)
- Services decouple the front-end and back-end pods. Services enable that abstraction
- Services are exposed through internal and external endpoints
- Services can also point to non-kubernetes endpoints through a virtual IP-Bridge
- Services support communication in TCP and UDP
- Services can be exposed internaly or externaly to the cluster

## Replication Controller
- Ensures that a set of pods are always up and available
- Always maintains desired number of pods
  - If there are excess pods, they are killed
  - New pods are launched when they fail, get deleted or terminated
- Thus, Replication Controller creates and destroys pods dynamically
- Creating a Replication Controller with a count of 1 ensures that a pod is always available


# Summary
- Kubernetes master exposes an API
- Kubernetes suns a scheduler and controller services
- Each node is reponsible for running one or more pods
- Pods are the unit of deployment in kubernetes
- Labels associate one kubernetes object to other objects
- Replication Controller ensures high availability of pods
- Services expose pods to internal and external consumers
- Replication Controller and pods are associated through labels