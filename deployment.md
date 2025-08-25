# DEPLOYMENT IN KUBERNETES

-------------------------------------------------------------------------------

## What is deployment in k8s? 

A Deployment is a high-level API object (kind: Deployment) that provides declarative updates for Pods and ReplicaSets.(declarative updates mean we are defining the desired end state rather than step by step instructions .)

It manages the desired state of applications (number of replicas, image versions, labels, update strategy)using deployment controller.(You describe a desired state in a Deployment, and the Deployment Controller changes the actual state to the desired state at a controlled rate.)

Kubernetes automatically ensures the actual state matches the desired state, even after failures.

### Why use a Deployment?
 It automates the process of updating and scaling of application, making it easier to manage. Instead of manually creating and updating individual Pods, we can simply declare your desired state, and Kubernetes handles the complex tasks of ensuring the right number of Pods are running.it also have a self healing property which automatically replaces failed pods.

### How does a Deployment work?
 A Deployment manages a ReplicaSet. When you create a Deployment, it creates a ReplicaSet to maintain the desired number of Pods. When you update the Deployment, it creates a new ReplicaSet and intelligently handles the rollout from the old version to the new version.

#Deployment creates and manages ReplicaSets
Deployment → ReplicaSet → Pods

#Example hierarchy:
my-app-deployment
└── my-app-replicaset-abc123
    ├── my-app-pod-1
    ├── my-app-pod-2
    └── my-app-pod-3

#### BASIC DEPLOYMENT STRUCTURE-
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80

### What are its usecases? 

⦁	Create a Deployment to rollout a ReplicaSet. The ReplicaSet creates Pods in the background. Check the status of the rollout to see if it succeeds or not.

⦁	Declare the new state of the Pods by updating the PodTemplateSpec of the Deployment. A new ReplicaSet is created and the Deployment manages moving the Pods from the old ReplicaSet to the new one at a controlled rate. Each new ReplicaSet updates the revision of the Deployment.

⦁	Rollback to an earlier Deployment revision if the current state of the Deployment is not stable. Each rollback updates the revision of the Deployment.

⦁	Scale up the Deployment to facilitate more load.

⦁	Pause the rollout of a Deployment to apply multiple fixes to its PodTemplateSpec and then resume it to start a new rollout.

⦁	Use the status of the Deployment as an indicator that a rollout has stuck.

⦁	Clean up older ReplicaSets that you don't need anymore.

## Deployment Controller:

The Deployment Controller is a Kubernetes controller that runs in the control plane and continuously monitors the state of Deployment objects, ensuring the actual state matches the desired state.

### Why is it Important?
⦁	State Reconciliation: Continuously ensures desired state is maintained
⦁	Automatic Healing: Recreates failed pods automatically
⦁	Update Management: Handles rolling updates and rollbacks
⦁	ReplicaSet Management: Creates and manages ReplicaSets for different versions

### How does it work? 
It acts like an intelligent feedback loop. When it sees a difference between the desired state (e.g., replicas: 3) and the current state (e.g., only two Pods are running), it takes action to fix the discrepancy.

### Controller responsibilities-
⦁	replicaset management
⦁	pod lifecycle management
⦁	Update Orchestration

During updates, the controller:
- Creates new ReplicaSet with updated specifications
- Gradually scales up new ReplicaSet
- Gradually scales down old ReplicaSet
- Monitors pod readiness and health
- Updates deployment status


## How to create,update a deployment?

Instead of giving a series of commands ("create a Pod," "add this container," etc.), you simply declare the final state you want ("I want three replicas of this container image"). This makes your infrastructure easier to manage, version, and share.

CREATE--

apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80

In this example:

--> A Deployment named nginx-deployment is created, indicated by the .metadata.name field. This name will become the basis for the ReplicaSets and Pods which are created later.

--> The Deployment creates a ReplicaSet that creates three replicated Pods, indicated by the .spec.replicas field.

--> The .spec.selector field defines how the created ReplicaSet finds which Pods to manage. 

--> The .spec.template field contains the following sub-fields:

- The Pods are labeled app: nginx using the .metadata.labels field.
- The Pod template's specification, or .spec field, indicates that the Pods run one container, nginx, which runs the nginx Docker Hub image at version 1.14.2.
- Create one container and name it nginx using the .spec.containers[0].name field.

## How do you update? 

You simply modify the YAML file (e.g., change the image version) and run kubectl apply -f [filename]. The Deployment controller detects the change and automatically starts the update process.

### Why Update?
⦁	Security patches: Update to newer, secure images
⦁	Feature updates: Deploy new application versions
⦁	Configuration changes: Modify environment variables, resource limits
⦁	Bug fixes: Deploy corrected versions

Monitoring Updates
- bash
- #Watch deployment status
--------------------------------------------------
kubectl rollout status deployment/nginx-deployment
--------------------------------------------------

- #Get deployment details
--------------------------------------------------
kubectl describe deployment nginx-deployment
--------------------------------------------------

- #View rollout history
---------------------------------------------------
kubectl rollout history deployment/nginx-deployment
---------------------------------------------------

## ROLLOUT STRATERGIES-

### what is recreate stratergy?
Recreate strategy terminates all existing pods before creating new ones, causing downtime but ensuring no version mixing.
The Recreate strategy is useful for applications that can't have two different versions running at the same time, bcz it results in a brief period of downtime.

#### Why Use Recreate?

Version Isolation: No risk of multiple versions running simultaneously
Resource Constraints: When you can't run multiple versions together
State Conflicts: When old and new versions can't coexist
Database Migrations: When schema changes require exclusive access


### What is Rolling Update?
Rolling Update is the default deployment strategy that gradually replaces old pods with new ones, ensuring zero downtime during updates.

#### Why Use Rolling Update?

Zero Downtime: Application remains available during updates
Gradual Rollout: Risk mitigation by updating pods incrementally
Automatic Rollback: Can detect issues and rollback automatically
Resource Efficient: Doesn't require double resources

## Max Availability and Max Surge
These parameters control how rolling updates behave and are crucial for maintaining service availability while managing resource usage during deployments.

### What is Max Unavailable?
maxUnavailable specifies the maximum number of pods that can be unavailable during the update process.

#### Why Important?

Service Availability: Ensures minimum number of pods remain running
Traffic Handling: Maintains capacity to handle requests
SLA Compliance: Helps meet uptime requirements

### Max Surge
What is Max Surge?
maxSurge specifies the maximum number of pods that can be created above the desired number of pods during updates.

#### Why Important?

Faster Updates: More pods can be created simultaneously
Resource Planning: Controls additional resource usage
Update Speed: Balances speed vs resource consumption

#### How do they work?
 maxUnavailable sets the maximum number of Pods that can be unavailable during the update. maxSurge sets the maximum number of Pods that can be created above the desired replica count.

#### How do they work?(rolling and recreate)

RollingUpdate: A new Pod is created, and once it's ready, an old Pod is terminated. This process repeats until all Pods are updated.

Recreate: All old Pods are deleted, and then all new Pods are created.

## BLUE GREEN UPDATE AND CANARY UPDATE

### What is Canary Deployment?

Canary Deployment is a deployment strategy that gradually rolls out changes to a small subset of users before rolling it out to the entire infrastructure.

#### Why Use Canary Deployments?

1. Risk Mitigation: Test with real users but limit blast radius
2. Gradual Rollout: Increase confidence as rollout progresses
3. Performance Testing: Monitor performance with real traffic
4. User Feedback: Get early feedback from subset of users
5. Automated Rollbacks: Rollback automatically if metrics degrade

#### How Canary Works:
 You expose the new version to a small subset of users (the "canary") and monitor it. If it's stable, you gradually increase the traffic until the new version is fully rolled out.

### What is Blue-Green Deployment?
Blue-Green Deployment is a deployment strategy that reduces downtime and risk by running two identical production environments called Blue and Green. At any time, only one environment is live serving production traffic.

#### Why Use Blue-Green Deployments?

Zero Downtime: Instant switch between versions
Easy Rollback: Switch back to previous version immediately
Full Testing: Test complete environment before switching
Risk Mitigation: Reduced risk of deployment failures affecting users
Database Testing: Test with production data without affecting live system

#### How blue-green works?
You deploy the new version to a separate environment ("Green") and then, with a single command, switch all traffic from the old "Blue" environment to the "Green" environment.

#### Why use them?
They offer more control and less risk than a simple rolling update. They are used for mission-critical applications where downtime is unacceptable and a smooth, safe rollout is paramount.

#### When to Use Each Strategy

⦁	Use Blue-Green When:

1.	Database Schema Changes: Need to test complete data migration
2.	Infrastructure Updates: Major infrastructure or dependency changes
3.	Compliance Requirements: Need identical environments for testing
4.	Simple Applications: Applications without complex user behavior
5.	Sufficient Resources: Have enough resources for 2x deployment

⦁	Use Canary When:

1.	User-Facing Features: Changes that affect user experience
2.	Performance Sensitive: Need to monitor performance impact
3.	Large User Base: Want to limit impact of potential issues
4.	Complex Applications: Applications with complex user interactions
5.	Resource Constraints: Limited additional resources available

### Best Practices
⦁	For Blue-Green:

1.	Database Strategy: Use compatible database schemas or separate databases
2.	Health Checks: Comprehensive health checks before switching
3.	Monitoring: Monitor both environments during deployment
4.	Automation: Automate switching and rollback processes
5.	Testing: Test complete user journeys in green environment

⦁	For Canary:

1.	Gradual Rollout: Start with small percentage (1-5%)
2.	Metrics Monitoring: Monitor error rates, latency, business metrics
3.	Automated Rollback: Implement automatic rollback on metric degradation
4.	User Segmentation: Consider canary for specific user segments
5.	Feature Flags: Combine with feature flags for fine-grained control

## How to Roll Back a Deployment?

Rolling back means reverting your application to a previous, stable version.

### Why Rollback?

Failed Updates: When new version has critical bugs
Performance Issues: New version performs poorly
Configuration Errors: Wrong environment variables or settings
Security Issues: New version introduces vulnerabilities
User Experience: New version degrades user experience

#### Why is this necessary? 
When a new release introduces a bug or causes unexpected behavior, you need a quick way to revert to a working version to minimize impact on your users.

### How do you roll back? 
Kubernetes keeps a history of your changes. You can use the kubectl rollout undo command to revert to the previous or a specific revision.
example: check Kubernetes documentation

1.	Suppose that you made a typo while updating the Deployment, by putting the image name as nginx:1.161 instead of nginx:1.16.1.
2.	The rollout gets stuck.Press Ctrl-C to stop the above rollout status watch .
3.	You see that the number of old replicas (adding the replica count from nginx-deployment-1564180365 and nginx-deployment-2035384211) is 3, and the number of new replicas (from nginx-deployment-3066724191) is 1.
4.	
****note:The Deployment controller stops the bad rollout automatically, and stops scaling up the new ReplicaSet. This depends on the rollingUpdate parameters (maxUnavailable specifically) that you have specified. Kubernetes by default sets the value to 25%.****
5.	Get the description of the Deployment.
6.	To fix this, you need to rollback to a previous revision of Deployment that is stable.

### Types Of Rollback -
- Automatic Rollback on Failure in Kubernetes?

Automatic rollback is a feature in Kubernetes Deployments where the system reverts to the previous stable ReplicaSet if the new rollout fails (e.g., Pods crash, readiness probes fail, insufficient resources).

#### Why is it needed?

To ensure application stability in production.

To reduce downtime by quickly restoring the last working state.

To avoid manual intervention during critical failures.

To protect users from broken or unavailable applications.

#### How does it work?

1.	You apply a new Deployment (say, v2).

2.	Kubernetes tries to roll out new Pods gradually.

3.	Deployment controller continuously checks Pod health

    Liveness probeReadiness probeReplica availability
4.	If rollout conditions fail (e.g., Pods don’t become Ready within progressDeadlineSeconds), Kubernetes marks the Deployment as failed.

5.	The Deployment automatically rolls back to the last known good ReplicaSet (v1).

### Rollback with Validation in Kubernetes?

Rollback with validation means that when Kubernetes reverts a Deployment to a previous revision, it ensures the restored version is valid and working correctly before considering the rollback successful.
It’s not just about going back — it’s about going back safely.

#### Why is it needed?

A rollback may also fail if the previous version has hidden issues (e.g., bad config, outdated dependency).

Validation ensures Kubernetes doesn’t blindly switch to an unstable version.

Provides confidence that the system is actually restored to a working state.

#### How does it work?

1. Trigger: Rollback happens automatically (on failure) or manually (admin request).
   
2. Deployment Controller switches ReplicaSet to the older revision.

3. Validation checks run before rollout is marked successful:

4. Pods must pass readiness probes (respond to traffic).

5. Pods must pass liveness probes (stay healthy).

6. Desired replica count must be available.

7. If Pods fail validation → rollback is itself considered failed → Deployment status shows error → admin intervention needed.

## SCALING IN K8S
- Horizontal  pod scaling 
Scaling means increasing or decreasing the number of Pod replicas.

#### Why scale?

You scale to handle changes in traffic or workload. When traffic increases, you scale up to maintain performance. When traffic decreases, you scale down to save resources.

#### Horizontal Scaling Reasons:

- Increased Load: More users accessing your application
- Performance Issues: Response times increasing
- High Availability: Distribute risk across more instances
- Geographic Distribution: Serve users from multiple regions
- Resource Optimization: Better resource utilization

#### How do you scale?

- Manual: You manually set the replica count using the kubectl scale command.

- Automatic: You use the Horizontal Pod Autoscaler (HPA), which automatically scales based on resource metrics like CPU utilization.

### HPA:

Horizontal Pod Autoscaler (HPA) automatically scales the number of pods in a deployment, replica set, or stateful set based on observed metrics like CPU utilization, memory usage, or custom metrics.

#### Why Use HPA?

1. Performance Benefits:

- Load Distribution: Spread load across multiple instances
- Fault Tolerance: Multiple instances provide redundancy
- Response Time: More instances can handle more concurrent requests
- Throughput: Linear scaling of processing capacity

2. Cost Benefits:

- Dynamic Scaling: Scale down during low traffic
- Resource Efficiency: Only use what you need
- Automatic Response: No manual intervention required

#### How it works:

1. Monitors metrics every 15 seconds (default)
2. Compares current metrics against target values
3. Calculates desired replica count using: desiredReplicas = ceil[currentReplicas * (currentMetricValue / desiredMetricValue)]
4. Scales pods horizontally (adds/removes pod replicas)
Example scenario: If CPU usage exceeds 70%, HPA might scale from 3 to 5 pods to distribute the load.

### Vertical Pod Autoscaler (VPA)

VPA automatically adjusts the CPU and memory requests/limits for containers in pods based on historical usage patterns.

#### How it works:

Analyzes resource usage history and current demand

Provides three modes:
- Off: Only provides recommendations
- Initial: Sets resource requests when pods are created
- Auto: Updates running pods by evicting and recreating them


Uses machine learning algorithms to predict optimal resource requirements

Example scenario: If a pod consistently uses 200m CPU but requests 100m, VPA increases the request to prevent resource starvation.

### Cluster Autoscaler (CA)
CA automatically adjusts the number of nodes in a cluster based on pod scheduling needs.

#### How it works:

Monitors for pods that can't be scheduled due to insufficient resources
Adds nodes when pods are pending due to resource constraints
Removes underutilized nodes (typically when utilization < 50% for 10+ minutes)
Respects node group min/max limits and various safety constraints
Considers pod disruption budgets and local storage before removing nodes

Example scenario: When HPA creates new pods but no nodes have capacity, CA provisions additional nodes to accommodate them.

## METRIC SERVER, DEAMONSET AND DEAMONSET CONTROLLER

### What is Metrics Server?
Metrics Server is a scalable, efficient source of container resource metrics for Kubernetes built-in autoscaling pipelines. It collects resource metrics from kubelets and exposes them in Kubernetes apiserver through Resource Metrics API.

#### Why is Metrics Server Important?

The HPA needs data to make scaling decisions. The Metrics Server is a key component that provides this data by collecting resource usage from all your nodes and Pods.

- Autoscaling Foundation: Required for Horizontal Pod Autoscaler (HPA) and Vertical Pod Autoscaler (VPA)
- Resource Monitoring: Provides CPU and memory usage data
- kubectl top: Enables kubectl top commands
- Scheduler Decisions: Helps scheduler make better placement decisions
- Cluster Monitoring: Essential for cluster resource management

#### How it works?

##### Architecture:
-----------------------------------------------------------------------------------------
  kubelet (on each node) → Metrics Server → Kubernetes API Server → HPA/VPA/kubectl top 
-----------------------------------------------------------------------------------------

Data Flow:

1. kubelet collects metrics from containers via cAdvisor
2. Metrics Server scrapes metrics from kubelet
3. Metrics Server aggregates and stores metrics in memory
4. Kubernetes API serves metrics through Resource Metrics API
5. Consumers (HPA, VPA, kubectl) use metrics for decisions

### Deamonsets:
A DaemonSet ensures that a single copy of a Pod runs on every (or a selected subset of) node. This is needed for cluster-level services like log collectors or monitoring agents that must be present on every machine.

#### Why Use DaemonSets?

1. Node-level Services: Services that need to run on every node
2. Monitoring Agents: Collect metrics from each node
3. Log Collection: Gather logs from all nodes
4. Network Plugins: Networking components on each node
5. Storage Daemons: Node-local storage services
6. Security Agents: Security monitoring on every node

### DaemonSet Controller:
The DaemonSet Controller is a Kubernetes controller that manages DaemonSet objects, ensuring that the desired daemon pods are running on appropriate nodes.

#### How it works?

1. Watch for DaemonSet objects
2. Watch for Node additions/removals
3. Ensure one pod per eligible node
4. Handle node taints and tolerations
5. Update DaemonSet status

## How to Complete Deployment? 

#### What Makes a Deployment "Complete"?

Kubernetes marks a Deployment as complete when it has the following characteristics:

- All replicas updated: All replicas associated with the Deployment have been updated to the latest version specified
- All replicas available: All replicas associated with the Deployment are available and ready to serve traffic
- No old replicas: No old replicas for the Deployment are running

#### Why is Completion Status Important?

Deployment Validation: Confirms successful deployment
Automation Triggers: Can trigger next steps in CI/CD pipelines
Monitoring: Helps monitoring systems understand deployment state
Troubleshooting: Identifies stuck or failed deployments
SLA Compliance: Ensures service level agreements are met

## VERSION UPDATE AND CHANGE CAUSE

### Version Update
When you perform a version update, you are essentially telling the Deployment controller to switch from an old container image to a new one. This is typically done by editing the Deployment's manifest file and changing the spec.template.spec.containers.image field. For example, updating an application from version 1.0 to 2.0

#### Why is it needed?

- New Features: To introduce new functionalities to users.

- Bug Fixes: To resolve issues or errors in the current version.

- Security Patches: To address vulnerabilities and protect your application from threats.

- Performance Improvements: To make the application run faster and more efficiently.

### CHANGE-CAUSE
The change-cause is an annotation you can add to a Deployment.

#### What is the need for change-cause?
It's a simple way to document the reason for a change. When you look at the rollout history, you can see not just what was changed but why it was changed, which is invaluable for troubleshooting.
