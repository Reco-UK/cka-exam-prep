# Designing Kubernetes Cluster

Answer the following questions:
 - Purpose
   - Education
     - Minikube / Kind / K3 etc
     - Single node cluster with kubeadm/GCP/AWS
   - Dev
     - Multi-node cluster with single master and multiple workers
     - kubeadm/GCP/AWS
   - Prod
     - HA / Multi node cluster with multiple masters
     - Kubeadm or GCP or other platforms
     - Up to 5000 nodes
     - Up to 15000 Pods in the cluster
     - Up to 300000 Total Containers
     - Up to 100 pods per node

|Nodes|GCP|AWS|
|------|---|---|
|1-5|n1-standard-1|M3.medium|
|6-10|n1-standard-2|M3.large|
|11-100|n1-standard-4|M3.xlarge|
|101-350|n1-standard-8|M3.2xlarge|
|251-500|n1-standard-16|C4.4xlarge|
|> 500|n1-standard-32|C4.8xlarg|

 - Cloud or OnPrem
   - Kubeadm for onprem
   - GKE
   - Kops
   - AKS
 - Storage
   - High Performance
   - Multi Concurrent connections for network storage
   - Persistent shared volumes for shared access across pods
   - Label nodes with specific disk types
   - Use Node selectors to assign application to nodes with specific disks
 - Nodes
   - Virtual or Physical
   - Min of 4 Node Cluster depending on workload
   - Master vs Worker
   - x64 Arch
 - Workloads
   - Host many?
   - What kind?
   - Resource Requirements
   - Traffic

## Deploying Kubernetes CLuster locally

Minikube
 - Deploys VMS
 - Single Node Cluster

KubeADM
 - Requires VMs to be ready
 - Single / Multi node cluster

Turnkey Solutions
 - You provision VMs
 - You configure VMs
 - You use scripts to deploy Cluster
 - You maintain VMs
 - eg: Cluster on AWS using KOPS
   - Openshift
   - Cloud Foundry CR
   - VMware Cloud PKS
   - Vagrant

Managed Solutions:
 - K8s as a service
 - Provider provisions VMs
 - Provider installs Kubernetes
 - Provider maintains VMs
 - eg: Cluster on Google using GKE
   - GKE
   - Openshift Online
   - AKS
   - EKS

# HA Kubernetes Clusters

The aim of HA is to prevent single points of failure, so you should have a minimum of two masters and two nodes within your product Cluster.

# Multiple Masters

When you have multiple instances of the master node running, then you will have multiple instances of the following processes running aswell:
  - API Server
  
The api server listens for all incoming requests to the cluster and can be running in an active / active mode.

The url of the api server is defined within the kubeconfig and used by the kubectl utility.

As there are now two endpoints that can receive requests, then they should be fronted by a load balancer, which shares the requests between the two instances.

  - Controller Manager

The controller manager must run in active / passive mode as it cannot run in parallel.
The active member in the cluster is promoted through a leader election process.
On startup there is a race condition between the two instances, the first to update the kube-controller-manager-endpoint and obtain a lock is active. (retry every 2 seconds)

  - Scheduler

The Scheduler must run in active / passive mode as it cannot run in parallel.
The active member in the cluster is promoted through a leader election process.
The scheduler follows a similar process to the controller manager when electing a leader.

  - ETCD

There are two topologies you can adopt when deploy ETCD:
 - Stacked Topology
This when you have the ETCD instances running on each of the masters.
Higher risk but easier setup
 - External ETCD Topology
This is where the ECTD instances are deployed on a separate instances to the control plane nodes.
Lower Risk more complex setup

Inboth of the above scenarios the ETCD instances will be peered with each other.

**API Server is the only component which talks to ECTD**