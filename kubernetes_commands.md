

<br>


# Kubernetes Commands


<br>




List of general purpose commands for Kubernetes management:

- [all](#all)
- [VIM Setup for Yaml files](#vim-setup-for-yaml-files)
- [Pods](#pods)
- [Nodes](#nodes)
- [Create](#create)
- [Deployments](#deployments)
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
- [Jobs](#jobs)
- [etcd](#etcd)
- [kubectl --help](#kubectl--help)




<br>

## all


```
$ kubectl completion powershell | Out-String | Invoke-Expression
$ kubectl get all
$ kubectl get all -A 
$ kubectl get pods,services, rs
```


<br>



## VIM Setup for Yaml files

Put the following lines in ~/.vimrc:

```
" Yaml file handling
autocmd FileType yaml setlocal ts=2 sts=2 sw=2 expandtab
filetype plugin indent on
autocmd FileType yaml setl indentkeys-=<:>

" Copy paste with ctr+c, ctr+v, etc
:behave mswin
:set clipboard=unnamedplus
:smap <Del> <C-g>"_d
:smap <C-c> <C-g>y
:smap <C-x> <C-g>x
:imap <C-v> <Esc>pi
:smap <C-v> <C-g>p
:smap <Tab> <C-g>1> 
:smap <S-Tab> <C-g>1<
```

Keyboard hints:

- ctrl + f: auto indent line (requires INSERT mode)


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

$ kubectl describe pod TOM
$ kubectl describe pods kuard

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
$ kubectl get nodes -o wide  # VMs
$ kubectl describe nodes kube1
# remove node from cluster
# stop all pods that are running on a node: 
$ kubectl drain node_name --ignore-daemonsets --delete-local-data
$ kubectl delete node node_name # node removed from the cluster 
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
$ kubectl run TOM --image=TOM --record
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


<br><br><br>








































































































































































<br>
<br>
