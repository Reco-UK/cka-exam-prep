# Cluster Networking

A standard Kubernetes cluster will exist of Masters and worker nodes.

Each of these machines must have at lease one interface attached to the network with address assigned and also have unique hostname and mac address.

The following ports will also need to be accessible between the machines.

The Kube-api on the master will be listening on 6443 and will need to be accessible to all the components (kube-scheduler, kube-controller-manager etc) running on the master, plus kubectl and the kubelet running on each of the nodes.

Kube-scheduler is listening on 10251.
Kube-contaoller-manager is on 10252.
ETCD is on 2379.

The above ports will be the same for each master node you are running, but when running multiple masters you will also need to open up port 2380 for ETCD replication.

The kubelet itself will be listening 10250.

The nodes will expose services on port ranging between 30000-32767.