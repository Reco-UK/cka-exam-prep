### Mock Exam

##Question 1 | Contexts

You have access to multiple clusters from your main terminal through kubectl contexts. Write all those context names into /opt/course/1/contexts.

Next write a command to display the current context into /opt/course/1/context_default_kubectl.sh, the command should use kubectl.

Finally write a second command doing the same thing into /opt/course/1/context_default_no_kubectl.sh, but without the use of kubectl.

```shell
#Answer:
#Maybe the fastest way is just to run:
k config get-contexts # copy manually

k config get-contexts -o name > /opt/course/1/contexts

#Or using jsonpath:
k config view -o yaml # overview
k config view -o jsonpath="{.contexts[*].name}"
k config view -o jsonpath="{.contexts[*].name}" | tr " " "\n" # new lines
k config view -o jsonpath="{.contexts[*].name}" | tr " " "\n" > /opt/course/1/contexts 

#Next create the first command:

# /opt/course/1/context_default_kubectl.sh
kubectl config current-context

# /opt/course/1/context_default_no_kubectl.sh
cat ~/.kube/config | grep current

#The second command could also be improved to:

# /opt/course/1/context_default_no_kubectl.sh
cat ~/.kube/config | grep current | sed -e "s/current-context: //"
```

## Question 2 | Schedule Pod on Master Node

Create a single Pod of image httpd:2.4.41-alpine in Namespace default. The Pod should be named pod1 and the container should be named pod1-container. This Pod should only be scheduled on a master node, do not add new labels any nodes.

Shortly write the reason on why Pods are by default not scheduled on master nodes into /opt/course/2/master_schedule_reason.

```shell
#Answer:

#First we find the master node(s) and their taints:

k get node # find master node

k describe node cluster1-master1 | grep Taint # get master node taints

k describe node cluster1-master1 | grep Labels -A 10 # get master node labels

k get node cluster1-master1 --show-labels # OR: get master node labels

k run pod1 --image=httpd:2.4.41-alpine $do > 2.yaml

vim 2.yaml
```

Perform the necessary changes manually. Use the Kubernetes docs and search for example for tolerations and nodeSelector to find examples:

```yaml
# 2.yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: pod1
  name: pod1
spec:
  containers:
  - image: httpd:2.4.41-alpine
    name: pod1-container                  # change
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
  tolerations:                            # add
  - effect: NoSchedule                    # add
    key: node-role.kubernetes.io/master   # add
  nodeSelector:                           # add
    node-role.kubernetes.io/master: ""    # add
status: {}
```

Important here to add the toleration for running on master nodes, but also the nodeSelector to make sure it only runs on master nodes. If we only specify a toleration the Pod can be scheduled on master or worker nodes.

Now we create it:
```shell
k -f 2.yaml create

# /opt/course/2/master_schedule_reason
# master nodes usually have a taint defined
```

## Question 3 | Scale down StatefulSet

There are two Pods named o3db-* in Namespace project-c13. C13 management asked you to scale the Pods down to one replica to save resources. Record the action.

```shell
#Answer:
#If we check the Pods we see two replicas:

k -n project-c13 get pod | grep o3db

k -n project-c13 get deploy,ds,sts | grep o3db

#Confirmed, we have to work with a StatefulSet. To find this out we could also look at the Pod labels:

k -n project-c13 get pod --show-labels | grep o3db

k -n project-c13 scale sts o3db --replicas 1 --record

k -n project-c13 get sts o3db

k -n project-c13 describe sts o3db
```

## Question 4 | Pod Ready if Service is reachable

Do the following in Namespace default. Create a single Pod named ready-if-service-ready of image nginx:1.16.1-alpine. Configure a LivenessProbe which simply runs true. Also configure a ReadinessProbe which does check if the url http://service-am-i-ready:80 is reachable, you can use wget -T2 -O- http://service-am-i-ready:80 for this. Start the Pod and confirm it isn't ready because of the ReadinessProbe.

Create a second Pod named am-i-ready of image nginx:1.16.1-alpine with label id: cross-server-ready. The already existing Service service-am-i-ready should now have that second Pod as endpoint.

Now the first Pod should be in ready state, confirm that.

```shell
#Answer:

#It's a bit of an anti-pattern for one Pod to check another Pod for being ready using probes, hence the normally available readinessProbe.httpGet doesn't work for absolute remote urls. Still the workaround requested in this task should show how probes and Pod<->Service communication works.

#First we create the first Pod:

k run ready-if-service-ready --image=nginx:1.16.1-alpine $do > 4_pod1.yaml

vim 4_pod1.yaml
```
```yaml
# 4_pod1.yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: ready-if-service-ready
  name: ready-if-service-ready
spec:
  containers:
  - image: nginx:1.16.1-alpine
    name: ready-if-service-ready
    resources: {}
    livenessProbe:                               # add from here
      exec:
        command:
        - 'true'
    readinessProbe:
      exec:
        command:
        - sh
        - -c
        - 'wget -T2 -O- http://service-am-i-ready:80'   # to here
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

```shell
# Then create the Pod:
k -f 4_pod1.yaml create
# And confirm its in a non-ready state:
k get pod ready-if-service-ready
# We can also check the reason for this using describe:
k describe pod ready-if-service-ready
# Now we create the second Pod:
k run am-i-ready --image=nginx:1.16.1-alpine --labels="id=cross-server-ready"
# The already existing Service service-am-i-ready should now have an Endpoint:
k describe svc service-am-i-ready
k get ep 
Which will result in our first Pod being ready, just give it a minute for the Readiness probe to check again:
k get pod ready-if-service-ready
```
## Question 5 | Kubectl sorting

Here are various Pods in all namespaces. Write a command into /opt/course/5/find_pods.sh which lists all Pods sorted by their AGE (metadata.creationTimestamp).

Write a second command into /opt/course/5/find_pods_uid.sh which lists all Pods sorted by field metadata.uid. Use kubectl sorting for both commands.


```shell
# Answer:
# A good resources here (and for many other things) is the kubectl-cheat-sheet. You can reach it fast when searching for "cheat sheet" in the Kubernetes docs.

# /opt/course/5/find_pods.sh
kubectl get pod -A --sort-by=.metadata.creationTimestamp


# /opt/course/5/find_pods_uid.sh
kubectl get pod -A --sort-by=.metadata.uid
```

## Question 6 | Storage, PV, PVC, Pod volume

Create a new PersistentVolume named safari-pv. It should have a capacity of 2Gi, accessMode ReadWriteOnce, hostPath /Volumes/Data and no storageClassName defined.

Next create a new PersistentVolumeClaim in Namespace project-tiger named safari-pvc . It should request 2Gi storage, accessMode ReadWriteOnce and should not define a storageClassName. The PVC should bound to the PV correctly.

Finally create a new Deployment safari in Namespace project-tiger which mounts that volume at /tmp/safari-data. The Pods of that Deployment should be of image httpd:2.4.41-alpine.

```shell
# Answer
vim 6_pv.yaml
# Find an example from https://kubernetes.io/docs and alter it:
```
```yaml
# 6_pv.yaml
kind: PersistentVolume
apiVersion: v1
metadata:
name: safari-pv
spec:
capacity:
storage: 2Gi
accessModes:
- ReadWriteOnce
hostPath:
path: "/Volumes/Data"
```
```shell
# Then create it:
k -f 6_pv.yaml create
# Next the PersistentVolumeClaim:
vim 6_pvc.yaml
# Find an example from https://kubernetes.io/docs and alter it:
```
```yaml
# 6_pvc.yaml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
name: safari-pvc
namespace: project-tiger
spec:
accessModes:
    - ReadWriteOnce
resources:
    requests:
     storage: 2Gi
```
```shell
# Then create:
k -f 6_pvc.yaml create
# And check that both have the status Bound:
k -n project-tiger get pv,pvc
# Next we create a Deployment and mount that volume:
k -n project-tiger create deploy safari \
--image=httpd:2.4.41-alpine $do > 6_dep.yaml
vim 6_dep.yaml
```
Alter the yaml to mount the volume:
```yaml
# 6_dep.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
creationTimestamp: null
labels:
    app: safari
name: safari
namespace: project-tiger
spec:
replicas: 1
selector:
    matchLabels:
      app: safari
strategy: {}
template:
    metadata:
      creationTimestamp: null
      labels:
        app: safari
    spec:
      volumes:                                      # add
      - name: data                                  # add
        persistentVolumeClaim:                      # add
          claimName: safari-pvc                     # add
      containers:
      - image: httpd:2.4.41-alpine
        name: container
        volumeMounts:                               # add
        - name: data                                # add
          mountPath: /tmp/safari-data               # add
```
```shell 
k -f 6_dep.yaml create
```

## Question 7 | Node and Pod Resource Usage

The metrics-server hasn't been installed yet in the cluster, but it's something that should be done soon. Your college would already like to know the kubectl commands to:
 - show node resource usage
 - show Pod and their containers resource usage

```shell
# Answer:
# The command we need to use here is top:

k top -h
k top node

# /opt/course/7/node.sh
kubectl top node

k top pod -h

# /opt/course/7/pod.sh
kubectl top pod --containers=true
```

## Question 8 | Get Master Information

Ssh into the master node with ssh cluster1-master1. Check how the master components kubelet, kube-apiserver, kube-scheduler, kube-controller-manager and etcd are started/installed on the master node. Also find out the name of the DNS application and how it's started/installed on the master node.

Write your findings into file /opt/course/8/master-components.txt. The file should be structured like:

# /opt/course/8/master-components.txt
kubelet: [TYPE]
kube-apiserver: [TYPE]
kube-scheduler: [TYPE]
kube-controller-manager: [TYPE]
etcd: [TYPE]
dns: [TYPE] [NAME]
Choices of [TYPE] are: not-installed, process, static-pod, pod

```shell
# Answer:
# We could start by finding processes of the requested components, especially the kubelet at first:

ps aux | grep kubelet # shows kubelet process

find /etc/systemd/system/ | grep kube

find /etc/systemd/system/ | grep etcd

find /etc/kubernetes/manifests/

# (The kubelet could also have a different manifests directory specified via parameter --pod-manifest-path in it's systemd startup config)

kubectl -n kube-system get pod -o wide | grep master1

kubectl -n kube-system get ds

kubectl -n kube-system get deploy

# /opt/course/8/master-components.txt
kubelet: process
kube-apiserver: static-pod
kube-scheduler: static-pod
kube-scheduler-special: static-pod (status CrashLoopBackOff)
kube-controller-manager: static-pod
etcd: static-pod
dns: pod coredns
```

## Question 9 | Kill Scheduler, Manual Scheduling

Ssh into the master node with ssh cluster2-master1. Temporarily stop the kube-scheduler, this means in a way that you can start it again afterwards.

Create a single Pod named manual-schedule of image httpd:2.4-alpine, confirm its started but not scheduled on any node.

Now you're the scheduler and have all its power, manually schedule that Pod on node cluster2-master1. Make sure it's running.

Start the kube-scheduler again and confirm its running correctly by creating a second Pod named manual-schedule2 of image httpd:2.4-alpine and check if it's running on cluster2-worker1.

```shell

# Answer:
# Stop the Scheduler
# First we find the master node:

k get node

kubectl -n kube-system get pod | grep schedule

cd /etc/kubernetes/manifests/

mv kube-scheduler.yaml ..

kubectl -n kube-system get pod | grep schedule

# Create a Pod

k run manual-schedule --image=httpd:2.4-alpine

k get pod manual-schedule -o wide

k get pod manual-schedule -o yaml > 9.yaml
```

```yaml
# 9.yaml
apiVersion: v1
kind: Pod
metadata:
creationTimestamp: "2020-09-04T15:51:02Z"
labels:
    run: manual-schedule
managedFields:
...
    manager: kubectl-run
    operation: Update
    time: "2020-09-04T15:51:02Z"
  name: manual-schedule
  namespace: default
  resourceVersion: "3515"
  selfLink: /api/v1/namespaces/default/pods/manual-schedule
  uid: 8e9d2532-4779-4e63-b5af-feb82c74a935
spec:
  nodeName: cluster2-master1        # add the master node name
  containers:
  - image: httpd:2.4-alpine
    imagePullPolicy: IfNotPresent
    name: manual-schedule
    resources: {}
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: default-token-nxnc7
      readOnly: true
  dnsPolicy: ClusterFirst
...
```
```shell

# The only thing a scheduler does, is that it sets the nodeName for a Pod declaration. How it finds the correct node to schedule on, that's a very much complicated matter and takes many variables into account.

k -f 9.yaml replace --force

k get pod manual-schedule -o wide

# Start the scheduler again
ssh cluster2-master1

cd /etc/kubernetes/manifests/

mv ../kube-scheduler.yaml .

kubectl -n kube-system get pod | grep schedule

k run manual-schedule2 --image=httpd:2.4-alpine
k get pod -o wide | grep schedule
```

## Question 10 | RBAC ServiceAccount Role RoleBinding

Create a new ServiceAccount processor in Namespace project-hamster. Create a Role and RoleBinding, both named processor as well. These should allow the new SA to only create Secrets and ConfigMaps in that Namespace.

```shell
# Answer:
# Let's talk a little about RBAC resources
# A ClusterRole|Role defines a set of permissions and where it is available, in the whole cluster or just a single Namespace.
# A ClusterRoleBinding|RoleBinding connects a set of permissions with an account and defines where it is applied, in the whole cluster or just a single Namespace.
# Because of this there are 4 different RBAC combinations and 3 valid ones:
# Role + RoleBinding (available in single Namespace, applied in single Namespace)
# ClusterRole + ClusterRoleBinding (available cluster-wide, applied cluster-wide)
# ClusterRole + RoleBinding (available cluster-wide, applied in single Namespace)
# Role + ClusterRoleBinding (NOT POSSIBLE: available in single Namespace, applied cluster-wide)

k -n project-hamster create sa processor

k -n project-hamster create role -h # examples

k -n project-hamster create role processor \
--verb=create \
--resource=secret \
--resource=configmap
```

```yaml
# kubectl -n project-hamster create role accessor --verb=create --resource=secret --resource=configmap
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
name: processor
namespace: project-hamster
rules:
- apiGroups:
- ""
resources:
- secrets
- configmaps
verbs:
- create
```

```shell
k -n project-hamster create rolebinding -h # examples

k -n project-hamster create rolebinding processor \
--role processor \
--serviceaccount project-hamster:processor
```

```yaml
# kubectl -n project-hamster create rolebinding processor --role processor --serviceaccount project-hamster:processor
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: processor
  namespace: project-hamster
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: processor
subjects:
- kind: ServiceAccount
  name: processor
  namespace: project-hamster
```  

```shell
k auth can-i -h 

k -n project-hamster auth can-i create secret \
  --as system:serviceaccount:project-hamster:processor

k -n project-hamster auth can-i create configmap \
  --as system:serviceaccount:project-hamster:processor

k -n project-hamster auth can-i create pod \
  --as system:serviceaccount:project-hamster:processor

k -n project-hamster auth can-i delete secret \
  --as system:serviceaccount:project-hamster:processor

k -n project-hamster auth can-i get configmap \
  --as system:serviceaccount:project-hamster:processor
```  

## Question 11 | DaemonSet on all Nodes

Use Namespace project-tiger for the following. Create a DaemonSet named ds-important with image httpd:2.4-alpine and labels id=ds-important and uuid=18426a0b-5f59-4e10-923f-c0e078e82462. The Pods it creates should request 10 millicore cpu and 10 megabytes memory. The Pods of that DaemonSet should run on all nodes.

```shell
# Answer:
# As of now we aren't able to create a DaemonSet directly using kubectl, so we create a Deployment and just change it up:

k -n project-tiger create deployment --image=httpd:2.4-alpine ds-important $do > 11.yaml

vim 11.yaml
```

```yaml
# 11.yaml
apiVersion: apps/v1
kind: DaemonSet                                     # change from Deployment to Daemonset
metadata:
creationTimestamp: null
labels:                                           # add
    id: ds-important                                # add
    uuid: 18426a0b-5f59-4e10-923f-c0e078e82462      # add
name: ds-important
namespace: project-tiger                          # important
spec:
#replicas: 1                                      # remove
selector:
    matchLabels:
      id: ds-important                              # add
      uuid: 18426a0b-5f59-4e10-923f-c0e078e82462    # add
  #strategy: {}                                     # remove
  template:
    metadata:
      creationTimestamp: null
      labels:
        id: ds-important                            # add
        uuid: 18426a0b-5f59-4e10-923f-c0e078e82462  # add
    spec:
      containers:
      - image: httpd:2.4-alpine
        name: ds-important
        resources:
          requests:                                 # add
            cpu: 10m                                # add
            memory: 10Mi                            # add
      tolerations:                                  # add
      - effect: NoSchedule                          # add
        key: node-role.kubernetes.io/master         # add
#status: {}                                         # remove
```
```shell
k -f 11.yaml create

k -n project-tiger get ds

k -n project-tiger get pod -l id=ds-important -o wide
```

## Question 12 | Deployment on all Nodes

Use Namespace project-tiger for the following. Create a Deployment named deploy-important with label id=very-important (the pods should also have this label) and 3 replicas. It should contain two containers, the first named container1 with image nginx:1.17.6-alpine and the second one named container2 with image kubernetes/pause.

There should be only ever one Pod of that Deployment running on one worker node. We have two worker nodes: cluster1-worker1 and cluster1-worker2. Because the Deployment has three replicas the result should be that on both nodes one Pod is running. The third Pod won't be scheduled, unless a new worker node will be added.

In a way we kind of simulate the behaviour of a DaemonSet here, but using a Deployment and a fixed number of replicas.

```shell
# Answer:

k -n project-tiger create deployment \
  --image=nginx:1.17.6-alpine deploy-important $do > 12.yaml

vim 12.yaml
```
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    id: very-important                  # change
  name: deploy-important
  namespace: project-tiger              # important
spec:
  replicas: 3                           # change
  selector:
    matchLabels:
      id: very-important                # change
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        id: very-important              # change
    spec:
      containers:
      - image: nginx:1.17.6-alpine
        name: container1                # change
        resources: {}
      - image: kubernetes/pause         # add
        name: container2                # add
      affinity:                                             # add
        podAntiAffinity:                                    # add
          requiredDuringSchedulingIgnoredDuringExecution:   # add
          - labelSelector:                                  # add
              matchExpressions:                             # add
              - key: id                                     # add
                operator: In                                # add
                values:                                     # add
                - very-important                            # add
            topologyKey: kubernetes.io/hostname             # add
status: {}
# Specify a topologyKey, which is a pre-populated Kubernetes label, you can find this by describing a node.
```
```shell
k -f 12.yaml create

k -n project-tiger get deploy -l id=very-important

k -n project-tiger get pod -o wide -l id=very-important
```

# Question 13 | Multi Containers and Pod shared Volume

Create a Pod named multi-container-playground in Namespace default with three containers, named c1, c2 and c3. There should be a volume attached to that Pod and mounted into every container, but the volume shouldn't be persisted or shared with other Pods.

Container c1 should be of image nginx:1.17.6-alpine and have the name of the node where its Pod is running on value available as environment variable MY_NODE_NAME.

Container c2 should be of image busybox:1.31.1 and write the output of the date command every second in the shared volume into file date.log. You can use while true; do date >> /your/vol/path/date.log; sleep 1; done for this.

Container c3 should be of image busybox:1.31.1 and constantly write the content of file date.log from the shared volume to stdout. You can use tail -f /your/vol/path/date.log for this.

Check the logs of container c3 to confirm correct setup.

```shell
# Answer:
# First we create the Pod template:


```


## Optional Setup
Fast dry-run output
```shell
export do="--dry-run=client -o yaml"
#This way you can just run k run pod1 --image=nginx $do. Short for "dry output", but use whatever name you like.

# Fast pod delete
export now="--force --grace-period 0"
```

## Be fast
Use the history command to reuse already entered commands or use even faster history search through Ctrl r .

If a command takes some time to execute, like sometimes kubectl delete pod x. You can put a task in the background using Ctrl z and pull it back into foreground running command fg.

```shell
# You can delete pods fast with:

k delete pod x --grace-period 0 --force
k delete pod x $now # if export from above is configured
```


