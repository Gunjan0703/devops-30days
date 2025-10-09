# Architecture of K8s Cluster

![k8s](https://kubernetes.io/images/docs/kubernetes-cluster-architecture.svg)

**Core Components**
- Control Plane
- One or more Worker Node

**Note-** Every cluster needs at least one worker node in order to run Pods.

## Control Plane
The control plane manages the worker nodes and the Pods in the cluster.
**Note-**  In production environments, the control plane usually runs across multiple computers and a cluster usually runs multiple nodes, providing fault-tolerance and high availability.

## Control Plane Componets
The control plane's components make global decisions about the cluster (for example, scheduling),
as well as detecting and responding to cluster events (for example, starting up a new pod when a Deployment's replicas field is unsatisfied).

- **kube-apiserver**

The API server is a component of the Kubernetes control plane that exposes the Kubernetes API. The API server is the front end for the Kubernetes control plane.
