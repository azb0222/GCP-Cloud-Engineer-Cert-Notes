# GOOGLE KUBERNETES ENGINE (GKE) 

## Overview
Google Kubernetes Engine (GKE) provides a managed environment for deploying, managing, and scaling containerized applications using Google infrastructure.

## Container Registry
**Container Registry**: A private Docker registry that allows you to store, manage, and secure your Docker container images.

## GKE
GKE utilizes Google Compute Engine instances connected as nodes.

### Advanced Cluster Management Features
- **Cloud Load Balancing**
- **Node Pools**
- **Automatic Node Scaling**
- **Automatic Upgrades**
- **Node Auto-Repair**
- **Logging and Monitoring**

Since individual nodes are Compute Engine instances, they have access to machine types. The cluster control plane and nodes do not necessarily have to be running the same version at all times.

### Node Pool
A node pool is a group of nodes within a cluster that have the same configuration. Custom node pools are useful for pods requiring more resources. Existing node pools can be manually or automatically upgraded to Kubernetes versions. You can have multiple types of node pools within a cluster.

## Types of Clusters
### Zonal Cluster
- **Single Zone or Multi-Zonal**: Multi-zonal clusters have a single control plane in a single zone but can have nodes in multiple zones.

### Regional Cluster
- **Regional Cluster**: Multiple replicas of the control plane running in multiple zones, and nodes are also replicated. The same number of nodes will be deployed to each zone (default 3 zones).

### Private Cluster
- **Private Cluster**: Isolates nodes from having inbound and outbound connectivity to the public internet. Nodes have internal IPs only and use Cloud NAT to provide external outbound access.
- In private clusters, the control plane's VPC network and the cluster's VPC network are connected with VPC peering.
- The private control plane has both a private and public endpoint. The private endpoint is an internal IP address, and public endpoint access can be controlled or limited.
- It is recommended to always enable auto-upgrade and choose from a release channel.

### Release Channel
Release channels are used to manage version updates automatically.

- **Rapid**: Updates are released several weeks after upstream open-source general availability.
- **Regular**: Updates are released 2-3 months after the Rapid channel.
- **Stable**: Updates are released 2-3 months after the Regular channel.

Clusters subscribe to a release channel to manage automatic upgrades but can also manage versions manually.

## Node Processes
- **kubelet**: Agent for communicating with the control plane.
- **kube-proxy**: Manages network connectivity.
- **Runtime**: Container runtime (e.g., Docker, containerd).

GKE takes care of exposing the Kubernetes API server IP (the cluster endpoint), which is displayed in the Cloud Console. Use the command `gcloud container clusters get-credentials` to update your kubeconfig.

## Cluster Upgrades
### Control Plane Upgrade
- The control plane is always updated before the nodes.
- **Zonal Cluster**: Cannot launch or edit workloads during an upgrade.
- **Regional Cluster**: Each control plane instance is upgraded one by one.
- Auto upgrade is the default. Maintenance windows and exclusions are available to choose the best times for upgrades.

### Node (Pool) Upgrades
- Pods are scheduled to run on another node during an upgrade.
- The upgrade is complete after all nodes are recreated and the cluster is in the desired state.
- Auto upgrade is the default. Maintenance windows and exclusions are available to choose the best times for upgrades.
- To ensure workloads are not disrupted, create a new node pool with the desired version and migrate workloads to the new node pool.

### Surge Upgrades
Surge upgrades control the number of nodes GKE can upgrade at a time to reduce disruptions to workloads. They work with cluster autoscaler to prevent changes to nodes being upgraded. There are two settings:
- **max-surge-upgrade**: Number of additional nodes added to the node pool during an upgrade. A higher number allows more parallel upgrades but at a higher cost.
- **max-unavailable-upgrade**: Number of nodes that can be simultaneously unavailable during an upgrade. A higher number is more disruptive.

During an upgrade, GKE brings down at most (max-surge-upgrade + max-unavailable-upgrade) nodes.

## Kubernetes Objects
### Structure
- **Object Spec**: Desired state.
- **Object Status**: Current state as described by Kubernetes.

The control plane ensures that Status matches Spec.

### Pods
Pods remain on a node until:
- The pod's process is complete.
- The pod is deleted.
- The pod is evicted from the node due to a lack of resources.
- The node fails.

Pods are ephemeral.

### Namespaces
- **default**: The default namespace.
- **kube-system**: Objects created by Kubernetes.
- **kube-public**: Readable by all users, mostly reserved for cluster usage.
- **kube-node-lease**: Namespace for all lease objects associated with nodes, improving the performance of node heartbeats.

### Workloads
- **Deployments**: Manages multiple replicas of an application and provides automatic healing for pods.
- **StatefulSet**: Manages applications with persistent storage.
- **DaemonSet**: Ensures that a copy of a pod runs on every node in the cluster.
- **Jobs**: Runs a finite task until completion.
- **CronJobs**: Runs jobs on a schedule until completion.
- **ConfigMap**: Provides configuration information that can be referenced by workloads.

### Services 
persistent single IP + DNS. abstraction on top of Pods. allows for routing external traffic into cluster + internal routing. services handle automatic recreation of Pods + their IPs.

```
apiVersion: v1
kind: Service
metadata: 
    name: clusterip-service //DNS name of the service 
spec: 
    selector: 
        app: inventory //forward requests to pods with this label 
    type: ClusterIP
    ports: 
    - protocol: TCP 
      port: 80 //port number exposed internally in cluster
      targetPort: 80 //port on Pod that traffic should be sent to 
```
#### Service Types: 
- ClusterIP: internal, stable IP for within cluster. can still connect via CloudSDK/cloud shell using exposed IP of cluster  
- NodePort: NodePort can be between 30000-32767. connect using [NODE_IP]:[PORT_NUMBER]. NodePorts exposes the service on a static port on EVERY node in the cluster.  
- LoadBalancer: creates Google Cloud Load Balancer with 1) external IP address, 2) forwarding rule to direct traffic to load balancer, 3) backend services to route traffic to appropriate K8 Pods based on selector labels. Load Balancer does health checks and traffic routing  
- Ingress 
- Multi-port Services: define multiple ports within single K8 Service. good for application needing to expose multiple ports, good for different services running on different ports within same set of Pods.   
- ExternalName: ExternalName provides internal alias for an external DNS name. K8 handles DNS resolution, returns CNAME record to redirect traffic to external DNS name specified. useful for integrating external services outside of the cluster   
- Headless: does not assign ClusterIP, provides direct DNS resolution to individual pods matching the service selector. use case: for application needing direct access to individual pod IPs, ie. stateful applications and databases ie. StatefulSet (ie. MYSQL where you need to know IP of master). specify by saying clusterIP: None in spec 

### Ingress 
ingress: defines routing rules within cluster. associated with one or more service objects. GCP creates HTTP/HTTPS load balancer + configures with Ingress and associated services. each ingress uses a URL map: references one or more backend services. 

good for exposing multiple services under one domain
many types of Ingress controllers to choose from:   

### Health Check  
- if not specified, default and inferred parameters are used for Health Check 
- can be explicitly defined using a Backend Config CRD (use if >1 container in Pod, specific port for LB health check, etc., Anthos Ingress controller)

### SSL Certificates 
3 methods: 
1) Google-managed: note it does not support wildcard domains 
2) Self-managed: provisioned by oneself, managed and shared with Google Cloud, list certificate in Ingress annotation for use 
3) Self-managed as Secrets: provision your own certificates, create secret to hold certificate, refer to secret within Ingress specification

can specifiy multiple certificates in an Ingress manifest


### Storage 

#### Storage in Docker 
three ways to store data in Docker: 
1) Docker Volume: sits on Docker area on host file system, can be shared amongst other containers. decoupled from container 
2) Bind Mount: cannot be shared amongst other containers, good for local application development. sits on host file system 
3) tempfs: stored in hosts memory, good for ephemereal data, stored in host's memory (so faster performance bc no longer sits in Docker writable layer)

Volumes: basic storage unit. decouples storage form container, ties it to Pod lifecycle 
Volumes backed by ephemreal storage are ConfigMap, Secret. are tied to Pod 

Volumes are good for caching temp info, share files between containers, load information into Pod 

Volumes can also be Persistent Volumes, used for durable storage. PersistentVolumeClaims tie to PersistentVolumes

PersistentVolumeClaims can request based on specific size, acecss mode, and storage class 

**Types of Volumes**: 
- emptyDir: empty directory for pods to read/write from. when pod is removed from node, data in emptyDir is deleted forever. stored on whatever medium is backing Node (ie. disk, ssd, etc.). good for scratch space 
- configMap: inject config data into Pods 
- secret: make sensitive data availble to applications 
- downward API: let pods consume information about themselves or cluster without using K8 API server, ie. to inject metadata about pod or node its running on into the container env
- persistentVolume: provision durable storage to be used by applications  

 
