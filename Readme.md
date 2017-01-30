# Kubernetes Notes

## What is Kubernetes

* Kubernetes: greek for captain or pilot
* Inspired from an internal google project called 'Borg'
* Open Source project managed by Linux Foundation
* Has a distributed architecture and is highly API centered
* Decouples applications from hardware via containers
* Automates application configuration through service discovery

___


## Contributing to the open-source project
### Download the source code
To contribute, create a GitHub account and Fork the project from
[GitHub Kubernetes](https://github.com/kubernetes/kubernetes)

__NOTE:__ Project needs to be downloaded and placed under GOPATH. Please refer to [golang.org](https://golang.org/doc/code.html)
This project expects to be found at the Go package `k8s.io/kubernetes`

In order to have this setup, perform the following:
`git clone https://github.com/kubernetes/kubernetes $GOPATH/src/k8s.io/kubernetes`

### Building the Project
1. The human interface to the kubernetes build is __make__
2. Some parts of the build/test system depend on __docker__
3. Kubernetes is written in __Go__ You need a recent version of [Go toolchain](https://golang.org/dl/)

To build Kubernetes, execute the __make__ command inside the main source code directory.

`cd $GOPATH/src/k8s.io/kubernetes` and then type `make`

If you want to build a subset of code, you can pass the __WHAT__ variable to __make__
`make WHAT="cmd/kubelet"`

To run basic tests, type `make test`. This will run all the tests in the project.
If you want to just test a subset of the project, you can pass the __WHAT__ variable to __make__
`make test WHAT=pkg/kubelet`

[Development Guide](https://github.com/kubernetes/community/blob/master/contributors/devel/development.md)


If your code has dependencies on new packages, manage it with [godep](https://github.com/tools/godep)

___


## Terminology
* Containers: Units of packaging
* Pod: Units of deployment (contain one or more containers)
* Nodes: Hosts that run applications (contain one or more pods)
* Service: Collection of pods exposed as an endpoint
* Labels: key-value pairs for identification
* Replication Controller: Ensures availability and scalability

## Kubernetes Cluster
A Kubernetes cluster is composed by two type of resources
* Master: The Master is responsible for managing the cluster.
The master coordinates all activities in your cluster, such as
scheduling applications, maintaining applications' desired state,
scaling applications, and rolling out new updates.


* Node: A node is a VM or a physical computer that serves as a worker
machine in a Kubernetes cluster.
Each node has a Kubelet, which is an agent for managing the node and
communicating with the Kubernetes master.
The node should also have tools for handling container operations, such as Docker.
A Kubernetes cluster that handles production traffic should have a
minimum of three nodes.


When you deploy applications on Kubernetes, you tell the master to start
the application containers.
The master schedules the containers to run on the cluster's nodes.
The nodes communicate with the master using the Kubernetes API, which
the master exposes.
End users can also use the Kubernetes API directly to interact with the cluster.



### Kubernetes server contains

* Exposes the Kubernetes Server API -> (API, CLI, UI)
* Scheduler: Process to manage pods accross multiple nodes
* Controller: Overall coordination and health of the cluster ensuring the nodes and up and running in the desired configuration state.
* etcd: Central key-value pair DB that stores the cluster state

### Kubernetes Node

* A host that runs an application. Nodes do the heavy lifting
* Responsible for running multiple pods
* A node can contain multiple pods of different sizes

### Kubernetes node contains

* Pod: A group of one or more CONTAINERS that are always co-located, co-scheduled and run in a shared context
* kube-proxy: Responsible for maintaning the network configuration.
* kubelet: Responsible talking to the API, to report the current state of a node to the master
* supervisord: process manager. Allow to run multiple processes inside another process
* fluentd: Responsible for managing logs
* Add-ons: Additional functionality. For example DNS

### Kubernetes Pod

* Documentation
  * [Pod Documentation](http://kubernetes.io/docs/user-guide/pods)
  * [Pod States](http://kubernetes.io/docs/user-guide/pod-states)  (lifecycle)

When you create a deployment, Kubernetes creates a Pod to host your application instance.
A Pod is Kubernetes abstraction that represents a group of one or more
application containers, and some shared resources for those containers.
Those resources include:

* Shared storage, as Volumes
* Networking, as a unique cluster IP address
* Information about how to run each container, such as the container image version or specific ports to use
* A Pod is a group of one or more application containers and includes shared storage (volumes), IP address and information about how to run them.

A Pod models an application-specific “logical host” and can contain different application containers which are relatively tightly coupled.
For example, a Pod might include both the container with your Node.js app as well as a different container that feeds the data to be published by the Node.js webserver.
The containers in a Pod share an IP Address and port space, are always co-located and co-scheduled, and run in a shared context on the same Node.


* Pod: A group of one or more CONTAINERS that are always co-located, co-scheduled and run in a shared context. For example, Redis cache and Apache web server
* A pod models an application-specific "logical host" - It contains one or more application containers which are relatively tightly-coupled
* Fundamental unit of deployment in Kubernetes
* Containers in the same pod have the same hostname
* Modeled like a virtual machine
  * Each container represents a process
  * Tightly coupled with other containers in the same pod
* Each pod is isolated by
  * Process ID (PID)
  * Network namespace
  * Inter-process communication (IPC) namespace
  * Unix time sharing (UTS) namespace


___Pods are the atomic unit on the Kubernetes platform.___ When we create a Deployment on Kubernetes, that Deployment creates Pods with containers inside them (as opposed to creating containers directly).
Each Pod is tied to the Node where it is scheduled, and remains there until termination (according to restart policy) or deletion.
In case of a Node failure, identical Pods are scheduled on other available Nodes in the cluster.

A Pod always runs on a Node. A Node is a worker machine in Kubernetes and may be either a virtual or a physical machine, depending on the cluster.
Each Node is managed by the Master.
A Node can have multiple pods, and the Kubernetes master automatically handles scheduling the pods across the Nodes in the cluster.
The Master's automatic scheduling takes into account the available resources on each Node.

__NOTE:__ Containers should only be scheduled together in a single Pod if they are tightly coupled and need to share resources such as disk.

Every Kubernetes Node runs at least:

* Kubelet, a process responsible for communication between the Kubernetes Master and the Nodes;
it manages the Pods and the containers running on a machine.
* A container runtime responsible for pulling the container image from a registry, unpacking the container,
and running the application.




## Kubernetes Services

While Pods do have their own unique IP across the cluster, those IP’s are not exposed outside Kubernetes.
Taking into account that over time Pods may be terminated, deleted or replaced by other Pods, we need a way
to let other Pods and applications automatically discover each other.
Kubernetes addresses this by grouping Pods in Services.
A Kubernetes Service is an abstraction layer which defines a logical set of Pods and enables external
traffic exposure, load balancing and service discovery for those Pods.

This abstraction will allow us to expose Pods to traffic originating from outside the cluster.
Services have their own unique cluster-private IP address and expose a port to receive traffic.
If you choose to expose the service outside the cluster, the options are:

* LoadBalancer - provides a public IP address (what you would typically use when you run Kubernetes on GKE or AWS)
* NodePort - exposes the Service on the same port on each Node of the cluster using NAT (available on all Kubernetes clusters, and in Minikube)


A Service provides load balancing of traffic across the contained set of Pods.
This is useful when a service is created to group all Pods from a specific Deployment

Services are also responsible for service-discovery within the cluster (covered in Accessing the Service).
This will for example allow a frontend service (like a web server) to receive traffic from a backend service
(like a database) without worrying about Pods.

Services match a set of Pods using Label Selectors, a grouping primitive that allows logical operation on Labels.

*__Labels__* are key/value pairs that are attached to objects, such as Pods and you can think of them as hashtags from social media. They are used to organize related objects in a way meaningful to the users like:

* Production environment (production, test, dev)
* Application version (beta, v1.3)
* Type of service/server (frontend, backend, database)

__NOTE:__ Labels are key/value pairs that are attached to objects

Labels can be attached to objects at the creation time or later and can be modified at any time. The kubectl run command sets some default Labels/Label Selectors on the new Pods/ Deployment.
The link between Labels and Label Selectors defines the relationship between the Deployment and the Pods it creates.


* Services decouple the front-end and back-end pods. Services enable that abstraction
* Services are exposed through internal and external endpoints
* Services can also point to non-kubernetes endpoints through a virtual IP-Bridge
* Services support communication in TCP and UDP
* Services can be exposed internally or externally to the cluster
* Services provide a virtual IP and DNS name to a pod
* Services proxy traffic to selected pods
* Services provide simple load balancing including session affinity


## Scaling
Scaling up a Deployment will ensure new Pods are created and scheduled to Nodes with available resources.
Scaling down will reduce the number of Pods to the new desired state.
Scaling to zero is also possible. It will terminate all Pods of the specified Deployment.

Running multiple instances of an application will require a way to distribute the traffic to all of them.
Services have an integrated load-balancer that will distribute network traffic to all Pods of an exposed Deployment.
Services will monitor continuously the running Pods using endpoints, to ensure the traffic is sent only to available Pods.

Scaling is accomplished by changing the number of replicas in a Deployment.

Once you have multiple instances of an Application running, you would be able to do Rolling updates without downtime.
We’ll cover that in the next module. Now, let’s go to the online terminal and scale our application.


## Replication Controller
* Ensures that a set of pods are always up and available (handles pod failures)
* Always maintains desired number of pods
  * If there are excess pods, they are killed
  * New pods are launched when they fail, get deleted or terminated
* Thus, Replication Controller creates and destroys pods dynamically
* Creating a Replication Controller with a count of 1 ensures that a pod is always available


## Namespaces
* Namespaces group kubernetes resources (pods, replicasets, deployments, etc)
* By default, eveything is in the "default" namespace
* Best practice is to create a namespace for your deployment environment (productions, staging, test) and/or tenants
* Restrict access to specific namespaces for k8s users
* Namespaces can have separated networks (depends on network provider)


## Data Volumes
* Map directories into containers
* Multiple containers in one pod share the same volumes
* Many volume types
  * Empty directory
  * Host directory
  * Google persistent disk
  * Amazon Blob store
  * NFS
  * Git Repo


## PetSet
* A pet is a stateful Pod
* A PetSet is a scalable number of pets
* A pet is bound to a dynamically created data volume
* That data volume will never be deleted automatically
* The pet is bound to the same volume on restart


## Jobs
* A short lived task
* A job ensures that a container which executes such a task runs successfully exactly once
* Retry on failure
* Scheduled jobs can be started at specific times (like cron)


## DaemonSets
* DaemonSets run pods on all (or a selected set of) nodes in the cluster
* Useful for running containers for logging and monotoring


## Autoscaling
* Horizontal Pod autoscaling
* Scales ReplicaSet based on Pods CPU usage or app-provided metrics

* Cluster autoscaling - scale the number of nodes in your cluster based on CPU and mem usage; depends on cloud provider

## Summary
* Kubernetes master exposes an API
* Kubernetes suns a scheduler and controller services
* Each node is reponsible for running one or more pods
* Pods are the unit of deployment in kubernetes
* Labels associate one kubernetes object to other objects
* Replication Controller ensures high availability of pods
* Services expose pods to internal and external consumers
* Replication Controller and pods are associated through labels

## The `kubectl` command

### Syntax
The `kubectl` command has the following syntax.

`kubectl [COMMAND] [TYPE] [NAME] [FLAGS]`

where each of the parameters are:

* `COMMAND` - specifies the operation (`create`, `get`, `describe`, `delete`) that you want to perform on one or more resources.
* `TYPE` - specifies the *resource type*. Resource types are case-sensitive and you can specify the singular, plural or abbreviated forms. The following commands produce the same output:

```
$ kubectl get pod pod1
$ kubectl get pods pod1
$ kubectl get po pod1
```

* `NAME` - specifies the name of the resource. Names are case-sensitive. If the name parameter is not given, all resources are returned.

```
$ kubectl get pods
```

When performing an operation on multiple resources, you can specify each resource by type and name or specify one or more files.
* group resources by type and name `TYPE name1 name2`<br>
Example: `$ kubectl get pod pod1 pod2`
* multiple resource types individually: `TYPE1/name1 TYPE2/name2 TYPE3/name3 ...` <br>
Example: `$ kubectl get pod/pod1 replicationcontroller/rc1`

Specify resources with one or more files `-f file1 -f file2 ...`<br>
For configuration,
[Best practices](https://kubernetes.io/docs/user-guide/config-best-practices/)
point to using YAML over JSON.<br>
Example: `$ kubectl get pod -f pod.yml`

* `FLAGS` - specifie optional flags. For example, you can use `-s` or `--server` flags to specify the address and port of the Kubernetes API server.<br>
___NOTE:___ Flags that you specify from the command line override default values and any corresponding environment variables.
