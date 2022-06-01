
<br>

# Kubernetes Commands

<br>


List of general purpose commands for Kubernetes management:

- [all](#all)
- [Pods](#pods)
- [Nodes](#nodes)
- [Create](#create)
- [Deployments](#deployments)
- [ReplicaSets](#replicaset)
- [Labels](#labels)
- [Scaling PODs](#scaling-pods)
- [Pod Upgrade / History](#pod-upgrade-and-history)
- [Services](#services)
- [Volumes](#volumes)
- [Secrets](#secrets)
- [Port Forwarding](#Port-Forwarding)
- [ConfigMaps](#configmaps)
- [Ingress](#ingress)
- [Horizontal Pod Autoscalers](#horizontal-pod-autoscalers)
- [Scheduler](#scheduler)
- [Taints and Tolerations](#tains-and-tolerations)
- [Troubleshooting](#troubleshooting)
- [Role Based Access Control (RBAC)](#role-based-access-control)
- [Security Contexts](#security-contexts)
- [Pod Security Policies](#pod-security-policies)
- [Network Policies](#network_policies)
- [Context](#context)
- [Jobs](#jobs)
- [etcd](#etcd)
- [kubectl --help](#kubectl--help)
- [yaml](#yaml)
- [use](#use)
- [links](#links)


<br>

## Start


```
# create alias in powershell for speed 
$ New-Alias -Name k -Value kubectl

k api-resources
$ kubectl create --help
$ kubectl cluster-info
$ kubectl completion powershell | Out-String | Invoke-Expression
$ kubectl get all
$ kubectl get all -A 
$ kubectl get pods,services, rs
```




<br>

## PODS

```
$ kubectl get pods
$ kubectl get pods --all-namespaces  # -A
$ kubectl get pod TOM -o wide (or yaml, json)
$ kubectl get pods --show-labels -A
$ kubectl get pods | grep Pending   # Running is goal
$ kubectl get pods -l app=nsp-role-manager -o jsonpath='{.items[0].metadata.name}')
$ kubectl get pods my-pod -o jsonpath --template={.status.podIP}
$ kubectl get pods --selector="ver=2"
$ kubectl describe pod TOM
$ kubectl describe pods kuard
$ kubectl get pods --selector="app=bandicoot,ver=2"
$ kubectl get pods --selector="app in (alpaca,bandicoot)"

$ kubectl delete pod pod-name
$ kubectl delete pods/kuard

$ kubectl explain pods
$ kubectl run kuard --generator=run-pod/v1 --image=gcr.io/kuar-demo/kuard-amd64:blue
$ kubectl apply -f kuard-pod.yaml
```

<br>


## Nodes


```
# listing out Kubernetes Worker Nodes
$ kubectl get nodes
$ kubectl get nodes -o wide   # VMs

$ kubectl describe nodes kube1

# remove node from cluster / stop all pods that are running on a node 
$ kubectl drain node_name --ignore-daemonsets --delete-local-data
$ kubectl delete node node_name # node removed from the cluster 

# manually relabel a node 
$ kubectl label node TOM cool=true
$ kubectl cordon node_name
$ kubectl uncordon node_name
```



## Create

```
# create object 
$ kubectl apply -f obj.yaml
# delete object
$ kubectl delete -f obj.yaml
```



<br>



## Deployments

```
$ kubectl get deployments
$ kubectl get deployments -A
$ kubectl describe deployments

$ kubectl run TOM --image=TOM --record
# how many replicas of each pod are running
$ kubectl describe deployments.apps dns-autoscaler -n kube-system | grep Replicas

# manually scaling a deployment
$ kubectl scale deployment app-cache --replicas=6
$ kubectl get pods
NAME                         READY   STATUS              RESTARTS   AGE
app-cache-5d6748d8b9-6cc4j   1/1     ContainerCreating   0          11s
app-cache-5d6748d8b9-6rmlj   1/1     Running             0          28m
app-cache-5d6748d8b9-6z7g5   1/1     ContainerCreating   0          11s
app-cache-5d6748d8b9-96dzf   1/1     Running             0          28m
app-cache-5d6748d8b9-jkjsv   1/1     Running             0          28m
app-cache-5d6748d8b9-svrxw   1/1     Running             0          28m
$ kubectl get pods
NAME                         READY   STATUS    RESTARTS   AGE
app-cache-5d6748d8b9-6cc4j   1/1     Running   0          3m17s
app-cache-5d6748d8b9-6rmlj   1/1     Running   0          32m
app-cache-5d6748d8b9-6z7g5   1/1     Running   0          3m17s
app-cache-5d6748d8b9-96dzf   1/1     Running   0          32m
app-cache-5d6748d8b9-jkjsv   1/1     Running   0          31m
app-cache-5d6748d8b9-svrxw   1/1     Running   0          32m
```




<br>

## Replicasets
```
$ kubectl get replicasets --namespace kube-system
```





<br>

## Labels
```
$ kubectl label pods bar color=red
$ kubectl label pods bar color- # remove label 
$ kubectl label deployments alpaca-test "canary=true"  # modify labels 
```




<br>




## Scaling PODs

```bash
$ kubectl scale deployment/POD_NAME --replicas=N
$ kubectl scale deployment coredns -n kube-system --replicas=1
# stop the pod
$ kubectl scale deployment pod --replicas=0
# start the pod
$ kubectl scale deployment pod --replicas=1
$ kubectl scale deployments kuard --replicas=2
$ kubectl scale deployment coredns -n kube-system --replicas=0 (or 1) 
```


<br>



## POD Upgrade and history

#### List history of deployments

```
$ kubectl rollout history deployment/DEPLOYMENT_NAME
```

#### Jump to specific revision

```
$ kubectl rollout undo deployment/DEPLOYMENT_NAME --to-revision=N
```


<br>



## Services

List services

```
$ kubectl get services
$ kubectl get services --all-namespaces
```

Expose PODs as services (creates endpoints)

```
$ kubectl expose deployment/TOM --port=2001 --type=NodePort
```


<br>



## Volumes

Lits Persistent Volumes and Persistent Volumes Claims:

```
$ kubectl get pv --all-namespaces
$ kubectl get pvc --all-namespaces
```


<br>



## Secrets

```
$ kubectl get secrets
$ kubectl create secret generic --help
$ kubectl create secret generic mysql --from-literal=password=root
$ kubectl get secrets mysql -o yaml
```

<br>



## Port Forwarding 

```
$ kubectl port-forward <pod-name> 8080:80
$ kubectl port-forward kuard 8080:8080  # access the pod 
```


<br>



## ConfigMaps

```
$ kubectl create configmap foobar --from-file=config.js
$ kubectl get configmap foobar -o yaml
```


<br>



## DNS

List DNS-PODs:

```
$ kubectl get pods --all-namespaces |grep dns
```

Check DNS for pod nginx (assuming a busybox POD/container is running)

```
$ kubectl exec -ti busybox -- nslookup nginx
$ kubectl exec -it $(kubectl get pods | awk '/role-manager/{print $1;exit}') -c role-manager --/opt/isp/os/rolemgr/bin/rmgrctl statusAll
```

> Note: kube-proxy running in the worker nodes manage services and set iptables rules to direct traffic.


<br>



## Ingress

Commands to manage Ingress for ClusterIP service type:

```
$ kubectl get ingress
$ kubectl expose deployment ghost --port=2368
```

Spec for ingress:

- [backend](https://github.com/kubernetes/ingress/tree/master/examples/deployment/nginx)
 


<br>



## Horizontal Pod Autoscalers

When heapster runs:

```
$ kubectl get hpa
$ kubectl autoscale --help
```



<br>



## DaemonSets

```
$ kubectl get daemonsets
$ kubectl get ds
```


<br>



## Scheduler

NodeSelector based policy:

```
$ kubectl label node minikube foo=bar
$ kubectl label node VM mdm=true
```

Node Binding through API Server:

```
$ kubectl proxy 
$ curl -H "Content-Type: application/json" -X POST --data @binding.json http://localhost:8001/api/v1/namespaces/default/pods/foobar-sched/binding
```


<br>



## Tains and Tolerations

```
$ kubectl taint node master foo=bar:NoSchedule
```


<br>



## Troubleshooting

```
$ kubectl describe

$ kubectl logs
$ kubectl logs <pod-name>

$ kubectl exec
$ kubectl exec -it <pod-name> -- bash
$ kubectl exec -it kuard -- ash  # drop it for non interactive 

$ kubectl get nodes --show-labels
$ kubectl get nodes --show-labels -A

$ kubectl get events
$ kubectl get events -A 

$ kubectl top nodes

$ kubectl get pods -n kube-system  # specific core implementation
```



Docs Cluster: 
- https://kubernetes.io/docs/tasks/debug-application-cluster/debug-cluster/
- https://github.com/kubernetes/kubernetes/wiki/Debugging-FAQ


<br>



## Role Based Access Control

- Role
- ClusterRule
- Binding
- ClusterRoleBinding

```
$ kubectl create role fluent-reader --verb=get --verb=list --verb=watch --resource=pods
$ kubectl create rolebinding foo --role=fluent-reader --user=minikube
$ kubectl get rolebinding foo -o yaml
```


<br>



## Security Contexts

Docs: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/

- spec
 - securityCOntext
   - runAsNonRoot: true
   

<br>



## Pod Security Policies

Docs: https://github.com/kubernetes/kubernetes/blob/master/examples/podsecuritypolicy/rbac/README.md




<br>

## Network Policies

Network isolation at Pod level by using annotations

```
$ kubectl annotate ns <namespace> "net.beta.kubernetes.io/network-policy={\"ingress\": {\"isolation\": \"DefaultDeny\"}}"
```

More about Network Policies as a resource: 

https://kubernetes.io/docs/tasks/administer-cluster/declare-network-policy/


<br>





<br>

## Context

```
$ kubectl config current-context
$ kubectl config use-context some-defined-context
# service account
$ kubectl create serviceaccount build-bot
$ kubectl get serviceaccounts
$ kubectl run build-observer --image=alpine --restart=Never --serviceaccount=build-bot

# create role
$ kubectl create role read-only --verb=list,get,watch --resource=pods,deployments,services
$ kubectl get roles
```





<br>

## Jobs


Commands
```
$ kubectl create job etcd --from cronjob/isp-etcd-backup
$ kubectl create job neo4j --from cronjob/ispos-neo4j-backup 
$ kubectl create job postgresql --from cronjob/ispos-postgresql-backup
# if you use local storage, enter to copy the backup files to a local folder
$ kubectl cp $(kubectl get pods | awk '/isp-backup-storage/ {print $1;exit}'):tmp/backups/isp-etcd path/isp-etcd
$ kubectl delete jobs.batch job
```


<br>



## etcd


```
$ systemctl stop etcd
```



<br>



## kubectl--help 

```
kubectl --help

Base
  create        Create a resource from a file or from stdin
  expose        Take a replication controller, service, deployment or pod 
                and expose it as a new Kubernetes service
  run           Run a particular image on the cluster
  set           Set specific features on objects
  explain       Get documentation for a resource
  get           Display one or many resources
  edit          Edit a resource on the server
  delete        Delete resources by file names, stdin, resources and names, 
                or by resources and label selector

Deploy Commands:
  rollout       Manage the rollout of a resource
  scale         Set a new size for a deployment, replica set, 
                or replication controller
  autoscale     Auto-scale a deployment, replica set, 
                stateful set, or replication controller

Cluster Management Commands:
  certificate   Modify certificate resources.
  cluster-info  Display cluster information
  top           Display resource (CPU/memory) usage
  cordon        Mark node as unschedulable
  uncordon      Mark node as schedulable
  drain         Drain node in preparation for maintenance
  taint         Update the taints on one or more nodes

Troubleshooting and Debugging Commands:
  describe      Show details of a specific resource or group of resources
  logs          Print the logs for a container in a pod
  attach        Attach to a running container
  exec          Execute a command in a container
  port-forward  Forward one or more local ports to a pod
  proxy         Run a proxy to the Kubernetes API server
  cp            Copy files and directories to and from containers
  auth          Inspect authorization
  debug         Create debugging sessions for troubleshooting workloads and nodes

Advanced Commands:
  diff          Diff the live version against a would-be applied version
  apply         Apply a configuration to a resource by file name or stdin
  patch         Update fields of a resource
  replace       Replace a resource by file name or stdin
  wait          Experimental: Wait for a specific condition on one or many resources
  kustomize     Build a kustomization target from a directory or URL.

Settings Commands:
  label         Update the labels on a resource
  annotate      Update the annotations on a resource
  completion    Output shell completion code for the 
                specified shell (bash, zsh or fish)

Other Commands:
  alpha         Commands for features in alpha
  api-resources Print the supported API resources on the server
  api-versions  Print the supported API versions on the server, 
                in the form of "group/version"
  config        Modify kubeconfig files
  plugin        Provides utilities for interacting with plugins
  version       Print the client and server version information

Usage:

  kubectl [flags] [options]

Use "kubectl <command> --help" for more information about a given command.

Use "kubectl options" for a list of global command-line options (applies to all commands).
```





<br>

## yaml

```yaml

# pods/simple-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx:1.14.2
    ports:
    - containerPort: 80
```

```
kubectl apply -f https://k8s.io/examples/pods/simple-pod.yaml

(base) PS C:\Windows\system32> kubectl describe pods nginx
Name:         nginx
Namespace:    default
Priority:     0
Node:         docker-desktop/192.168.65.4
Start Time:   Wed, 01 Jun 2022 11:37:10 -0500
Labels:       <none>
Annotations:  <none>
Status:       Running
IP:           10.1.0.111
IPs:
  IP:  10.1.0.111
Containers:
  nginx:
    Container ID:   docker://a06bb062etc2fae54a562a814
    Image:          nginx:1.14.2
    Image ID:       docker-pullable://nginx@sha256:f7988fb6...etc
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Wed, 01 Jun 2022 11:37:19 -0500
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-4hdzn (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  kube-api-access-4hdzn:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age    From               Message
  ----    ------     ----   ----               -------
  Normal  Scheduled  3m44s  default-scheduler  Successfully assigned default/nginx to docker-desktop
  Normal  Pulling    3m43s  kubelet            Pulling image "nginx:1.14.2"
  Normal  Pulled     3m35s  kubelet            Successfully pulled image "nginx:1.14.2" in 7.802192988s
  Normal  Created    3m35s  kubelet            Created container nginx
  Normal  Started    3m35s  kubelet            Started container nginx
```




<br>

## use

Basics 
```
(base) PS C:\Windows\system32> kubectl get replicasets -A
NAMESPACE     NAME                 DESIRED   CURRENT   READY   AGE
kube-system   coredns-6d4b75cb6d   2         2         2       7m3s

(base) PS C:\Windows\system32> kubectl auth can-i --list
Resources                                       Non-Resource URLs   Resource Names   Verbs
*.*                                             []                  []               [*]
                                                [*]                 []               [*]
selfsubjectaccessreviews.authorization.k8s.io   []                  []               [create]
selfsubjectrulesreviews.authorization.k8s.io    []                  []               [create]
                                                [/api/*]            []               [get]
                                                [/api]              []               [get]
                                                [/apis/*]           []               [get]
                                                [/apis]             []               [get]
                                                [/healthz]          []               [get]
                                                [/healthz]          []               [get]
                                                [/livez]            []               [get]
                                                [/livez]            []               [get]
                                                [/openapi/*]        []               [get]
                                                [/openapi]          []               [get]
                                                [/readyz]           []               [get]
                                                [/readyz]           []               [get]
                                                [/version/]         []               [get]
                                                [/version/]         []               [get]
                                                [/version]          []               [get]
                                                [/version]          []               [get]



The most basic topology of a Kubernetes cluster consists of a single node 
that acts as the control plane and the worker node at the same time. 
By default, many developer-centric Kubernetes installations like 
Docker Desktop start with this configuration...

While a single node cluster may be a good option for a Kubernetes playground, 
it is not a good foundation for scalability and high-availability reasons. 
At the very least, you will want to create a cluster with a single control 
plane and one or many nodes handling the workload.


Drain the control plane node by evicting workload. 
New workload won’t be schedulable on the node until uncordoned.
$ kubectl drain kube-control-plane --ignore-daemonsets
node/kube-control-plane cordoned

Restart the kubelet process.
$ sudo systemctl daemon-reload
$ sudo systemctl restart kubelet

# Reenable the control plane node back so 
# that new workload can become schedulable.
$ kubectl uncordon kube-control-plane
node/kube-control-plane uncordoned


# Etcd is deployed as a Pod in the kube-system namespace.

$ kubectl get pods -n kube-system
NAME                                     READY   STATUS    RESTARTS   AGE
coredns-6d4b75cb6d-2p88j                 1/1     Running   0          17m
coredns-6d4b75cb6d-nr5qd                 1/1     Running   0          17m
etcd-docker-desktop                      1/1     Running   19         17m
kube-apiserver-docker-desktop            1/1     Running   19         17m
kube-controller-manager-docker-desktop   1/1     Running   19         17m
kube-proxy-xddd2                         1/1     Running   0          17m
kube-scheduler-docker-desktop            1/1     Running   19         17m
storage-provisioner                      1/1     Running   0          16m
vpnkit-controller                        1/1     Running   0          16m


# --- examining etcd --- 

$ kubectl describe pod etcd-docker-desktop -n kube-system
Name:                 etcd-docker-desktop
Namespace:            kube-system
Priority:             2000001000
Priority Class Name:  system-node-critical
Node:                 docker-desktop/192.168.65.4
Start Time:           Wed, 01 Jun 2022 10:52:20 -0500
Labels:               component=etcd
                      tier=control-plane
Annotations:          kubeadm.kubernetes.io/etcd.advertise-client-urls: https://192.168.65.4:2379
                      kubernetes.io/config.hash: 2449ddc0985e3be8dd23ffc4d12cb53b
                      kubernetes.io/config.mirror: 2449ddc0985e3be8dd23ffc4d12cb53b
                      kubernetes.io/config.seen: 2022-06-01T15:52:20.371801696Z
                      kubernetes.io/config.source: file
                      seccomp.security.alpha.kubernetes.io/pod: runtime/default
Status:               Running
IP:                   192.168.65.4
IPs:
  IP:           192.168.65.4
Controlled By:  Node/docker-desktop
Containers:
  etcd:
    Container ID:  docker://6337190918cd161f193c85bd1fbd03a0f518b21014b60a2136edff412b7d74e5
    Image:         k8s.gcr.io/etcd:3.5.3-0
    Image ID:      docker://sha256:aebe758cef4cd05b9f8cee39758227714d02f42ef3088023c1e3cd454f927a2b
    Port:          <none>
    Host Port:     <none>
    Command:
      etcd
      --advertise-client-urls=https://192.168.65.4:2379
      --cert-file=/run/config/pki/etcd/server.crt
      --client-cert-auth=true
      --data-dir=/var/lib/etcd
      --experimental-initial-corrupt-check=true
      --initial-advertise-peer-urls=https://192.168.65.4:2380
      --initial-cluster=docker-desktop=https://192.168.65.4:2380
      --key-file=/run/config/pki/etcd/server.key
      --listen-client-urls=https://127.0.0.1:2379,https://192.168.65.4:2379
      --listen-metrics-urls=http://127.0.0.1:2381
      --listen-peer-urls=https://192.168.65.4:2380
      --name=docker-desktop
      --peer-cert-file=/run/config/pki/etcd/peer.crt
      --peer-client-cert-auth=true
      --peer-key-file=/run/config/pki/etcd/peer.key
      --peer-trusted-ca-file=/run/config/pki/etcd/ca.crt
      --snapshot-count=10000
      --trusted-ca-file=/run/config/pki/etcd/ca.crt
    State:          Running
      Started:      Wed, 01 Jun 2022 10:52:21 -0500
    Ready:          True
    Restart Count:  19
    Requests:
      cpu:        100m
      memory:     100Mi
    Liveness:     http-get http://127.0.0.1:2381/health delay=10s timeout=15s period=10s #success=1 #failure=8
    Startup:      http-get http://127.0.0.1:2381/health delay=10s timeout=15s period=10s #success=1 #failure=24
    Environment:  <none>
    Mounts:
      /run/config/pki/etcd from etcd-certs (rw)
      /var/lib/etcd from etcd-data (rw)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  etcd-certs:
    Type:          HostPath (bare host directory volume)
    Path:          /run/config/pki/etcd
    HostPathType:  DirectoryOrCreate
  etcd-data:
    Type:          HostPath (bare host directory volume)
    Path:          /var/lib/etcd
    HostPathType:  DirectoryOrCreate
QoS Class:         Burstable
Node-Selectors:    <none>
Tolerations:       :NoExecute op=Exists
Events:
  Type    Reason   Age   From     Message
  ----    ------   ----  ----     -------
  Normal  Pulled   18m   kubelet  Container image "k8s.gcr.io/etcd:3.5.3-0" already present on machine
  Normal  Created  18m   kubelet  Created container etcd
  Normal  Started  18m   kubelet  Started container etcd

# Edit the YAML manifest of the etcd Pod which can be found at 
# /etc/kubernetes/manifests/etcd.yaml


# The central API resource for running an application in a container is the Pod.
```


Nodes: 

```
(base) PS C:\Windows\system32> kubectl get pods -A
NAMESPACE     NAME                                     READY   STATUS    RESTARTS        AGE
kube-system   coredns-6d4b75cb6d-2p88j                 1/1     Running   0               34m
kube-system   coredns-6d4b75cb6d-nr5qd                 1/1     Running   0               34m
kube-system   etcd-docker-desktop                      1/1     Running   19              34m
kube-system   kube-apiserver-docker-desktop            1/1     Running   19              34m
kube-system   kube-controller-manager-docker-desktop   1/1     Running   19              34m
kube-system   kube-proxy-xddd2                         1/1     Running   0               34m
kube-system   kube-scheduler-docker-desktop            1/1     Running   19              34m
kube-system   storage-provisioner                      1/1     Running   0               33m
kube-system   vpnkit-controller                        1/1     Running   2 (8m10s ago)   33m


$ kubectl get nodes
NAME             STATUS   ROLES           AGE   VERSION
docker-desktop   Ready    control-plane   32m   v1.24.0


$ kubectl describe nodes docker-desktop

Name:               docker-desktop

Roles:              control-plane

Labels:             beta.kubernetes.io/arch=amd64
                    beta.kubernetes.io/os=linux
                    kubernetes.io/arch=amd64
                    kubernetes.io/hostname=docker-desktop
                    kubernetes.io/os=linux
                    node-role.kubernetes.io/control-plane=
                    node.kubernetes.io/exclude-from-external-load-balancers=

Annotations:        kubeadm.alpha.kubernetes.io/cri-socket: unix:///var/run/cri-dockerd.sock
                    node.alpha.kubernetes.io/ttl: 0
                    volumes.kubernetes.io/controller-managed-attach-detach: true

CreationTimestamp:  Wed, 01 Jun 2022 10:52:24 -0500

Taints:             <none>

Unschedulable:      false

Lease:
  HolderIdentity:  docker-desktop
  AcquireTime:     <unset>
  RenewTime:       Wed, 01 Jun 2022 11:26:02 -0500

Conditions:
  Type             Status  LastHeartbeatTime                 LastTransitionTime                Reason                       Message
  ----             ------  -----------------                 ------------------                ------                       -------

  # True if pressure exists on the node memory—that is, if the node memory is low
  MemoryPressure   False   Wed, 01 Jun 2022 11:26:07 -0500   Wed, 01 Jun 2022 10:52:21 -0500   KubeletHasSufficientMemory   kubelet has sufficient memory available

  DiskPressure     False   Wed, 01 Jun 2022 11:26:07 -0500   Wed, 01 Jun 2022 10:52:21 -0500   KubeletHasNoDiskPressure     kubelet has no disk pressure

  PIDPressure      False   Wed, 01 Jun 2022 11:26:07 -0500   Wed, 01 Jun 2022 10:52:21 -0500   KubeletHasSufficientPID      kubelet has sufficient PID 
available

  Ready            True    Wed, 01 Jun 2022 11:26:07 -0500   Wed, 01 Jun 2022 10:52:54 -0500   KubeletReady                 kubelet is posting ready status

Addresses:
  InternalIP:  192.168.65.4 < - typically the IP address of the node that is 
                                routable only within the cluster.
  Hostname:    docker-desktop

Capacity:
  cpu:                24
  ephemeral-storage:  263174212Ki
  hugepages-1Gi:      0
  hugepages-2Mi:      0
  memory:             52602964Ki
  pods:               110

Allocatable:
  cpu:                24
  ephemeral-storage:  242541353378
  hugepages-1Gi:      0
  hugepages-2Mi:      0
  memory:             52500564Ki
  pods:               110

System Info:
  Machine ID:                 27bb5c95-c4b7-438c-bed1-ed90bac37fb7
  System UUID:                27bb5c95-c4b7-438c-bed1-ed90bac37fb7
  Boot ID:                    9e96b887-82ca-4468-91cf-121376cb0047
  Kernel Version:             5.10.16.3-microsoft-standard-WSL2
  OS Image:                   Docker Desktop
  Operating System:           linux
  Architecture:               amd64
  Container Runtime Version:  docker://20.10.14
  Kubelet Version:            v1.24.0
  Kube-Proxy Version:         v1.24.0

Non-terminated Pods:          (9 in total)
  Namespace                   Name                                      CPU Requests  CPU Limits  Memory Requests  Memory Limits  Age
  ---------                   ----                                      ------------  ----------  ---------------  -------------  ---
  kube-system                 coredns-6d4b75cb6d-2p88j                  100m (0%)     0 (0%)      70Mi (0%)        170Mi (0%)     33m
  kube-system                 coredns-6d4b75cb6d-nr5qd                  100m (0%)     0 (0%)      70Mi (0%)        170Mi (0%)     33m
  kube-system                 etcd-docker-desktop                       100m (0%)     0 (0%)      100Mi (0%)       0 (0%)         33m
  kube-system                 kube-apiserver-docker-desktop             250m (1%)     0 (0%)      0 (0%)           0 (0%)         33m
  kube-system                 kube-controller-manager-docker-desktop    200m (0%)     0 (0%)      0 (0%)           0 (0%)         33m
  kube-system                 kube-proxy-xddd2                          0 (0%)        0 (0%)      0 (0%)           0 (0%)         33m
  kube-system                 kube-scheduler-docker-desktop             100m (0%)     0 (0%)      0 (0%)           0 (0%)         33m
  kube-system                 storage-provisioner                       0 (0%)        0 (0%)      0 (0%)           0 (0%)         33m
  kube-system                 vpnkit-controller                         0 (0%)        0 (0%)      0 (0%)           0 (0%)         33m

Allocated resources:
  (Total limits may be over 100 percent, i.e., overcommitted.)
  Resource           Requests    Limits
  --------           --------    ------
  cpu                850m (3%)   0 (0%)
  memory             240Mi (0%)  340Mi (0%)
  ephemeral-storage  0 (0%)      0 (0%)
  hugepages-1Gi      0 (0%)      0 (0%)
  hugepages-2Mi      0 (0%)      0 (0%)

Events:
  Type    Reason                   Age                From        Message
  ----    ------                   ----               ----        -------
  Normal  Starting                 33m                kube-proxy
  Normal  NodeHasSufficientMemory  33m (x8 over 33m)  kubelet     Node docker-desktop status is now: NodeHasSufficientMemory
  Normal  NodeHasNoDiskPressure    33m (x7 over 33m)  kubelet     Node docker-desktop status is now: NodeHasNoDiskPressure
  Normal  NodeHasSufficientPID     33m (x7 over 33m)  kubelet     Node docker-desktop status is now: NodeHasSufficientPID
```




<br>

## links

* https://kubernetes.io/docs/concepts/architecture/
* https://kubernetes.io/docs/concepts/architecture/nodes/#addresses
* 




<br><br><br><br><br><br>