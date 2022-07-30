# k8s-admin-training

# Before start this training,you must have the below Pre-Requisites to loud better in the Azure kubernetes Administration

- Linux Admiistration knowledge is must
- Ansible Automation/Administration Knowledge is must
- Portal.Azure.com cloud account should be created and activated.
- Hub.docker.com login need to be created
- github.com account need to be created


# Create AKS Cluster

```
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --generate-ssh-keys
```

# how to login AKS worker nodes

```
kubectl debug node/aks-nodepool1-33290996-vmss000000 -it --image=mcr.microsoft.com/dotnet/runtime-deps:6.0
```

# Part 1 session :

## create name space

```
kubectl create ns <namespace name>

```

```

kubectl apply -f https://raw.githubusercontent.com/cloudnloud/Kubernetes_Admin_Training/main/class4-namespace-Pod/namespace/namespace.yaml
```

```
kubectl delete -f https://raw.githubusercontent.com/cloudnloud/Kubernetes_Admin_Training/main/class4-namespace-Pod/namespace/namespace.yaml
```


## create single pod

```
kubectl create ns twitter
```

```

kubectl apply -f https://raw.githubusercontent.com/cloudnloud/Kubernetes_Admin_Training/main/class4-namespace-Pod/pod/1-singlepod.yml
```

```
kubectl apply -f 1-singlepod.yml
```

```

kubectl get all -n twitter -l app=nginx
```

```
kubectl get all -n twitter -l version=v1
```

```
kubectl exec -it pod/webserver bash -n twitter
```

now you need to understand that lablels will help you to create exact report and find the particular resources

```
kubectl delete -f https://raw.githubusercontent.com/cloudnloud/Kubernetes_Admin_Training/main/class4-namespace-Pod/pod/1-singlepod.yml
```


**************************************************************************************************************************************
## create Multipod

```
kubectl create ns facebook
```

```
kubectl apply -f https://raw.githubusercontent.com/cloudnloud/Kubernetes_Admin_Training/main/class4-namespace-Pod/pod/2-multipods.yml
```

```
kubectl get all -n facebook -l app=httpd
```


```
kubectl describe pod/multicontainer-pods -n facebook
```

```

kubectl exec -it pod/multicontainer-pods bash -n facebook
```

```
kubectl exec -it pod/multicontainer-pods bash -n facebook -c web
```

```
kubectl exec -it pod/multicontainer-pods bash -n facebook -c db
```
```
kubectl delete -f https://raw.githubusercontent.com/cloudnloud/Kubernetes_Admin_Training/main/class4-namespace-Pod/pod/2-multipods.yml
```

************************************************************************************************************************************
## create Multipod with Duplicate Port

```
kubectl apply -f https://raw.githubusercontent.com/cloudnloud/Kubernetes_Admin_Training/main/class4-namespace-Pod/pod/2.1-duplicate-port-multipod.yaml
```

```
kubectl apply -f 2.1-duplicate-port-multipod.yaml
```


same port it wont work ---


```
kubectl get all -n facebook -l app=httpd
```

kubectl describe pod/multicontainer-pods -n facebook

in this only one container will run another one will give you error

```
kubectl logs pod/multicontainer-pods -n facebook -c httpd2
```

```

kubectl logs pod/multicontainer-pods -n twitter -c httpd1
```

```
kubectl delete -f https://raw.githubusercontent.com/cloudnloud/Kubernetes_Admin_Training/main/class4-namespace-Pod/pod/2.1-duplicate-port-multipod.yaml
```


***********************************************************************************************************************************
## create Multipod with single storage

```
kubectl create ns twitter
```

```
kubectl apply -f https://raw.githubusercontent.com/cloudnloud/Kubernetes_Admin_Training/main/class4-namespace-Pod/pod/3-storage.yml
```

```
kubectl apply -f 3-storage.yml
```

```
kubectl get pods -n twitter -o wide
```

```
kubectl describe pod/database -n twitter
```

check the directory has been created to that node

```
ls -ltrh /var/lib/postgres
```

```
kubectl delete -f https://raw.githubusercontent.com/cloudnloud/Kubernetes_Admin_Training/main/class4-namespace-Pod/pod/3-storage.yml
```


****************************************************************************************************************************************
## create Multipod with Multi storage

```
kubectl apply -f https://raw.githubusercontent.com/cloudnloud/Kubernetes_Admin_Training/main/class4-namespace-Pod/pod/4-multistorage.yml
```

```
kubectl get pods -n facebook -o wide
```

```
kubectl get all -n facebook -o wide
```

```
kubectl describe pod/db -n facebook
```

```
kubectl exec -it pod/db bash -n facebook -c web
```

```
kubectl exec -it pod/db bash -n facebook -c db
```

```
ls -ltrh /var/www/html/
ls -ltrh /var/lib/postgres
```

```
kubectl delete -f https://raw.githubusercontent.com/cloudnloud/Kubernetes_Admin_Training/main/class4-namespace-Pod/pod/4-multistorage.yml
```



***************************************************************************************************************************************
# services

# expose service using loadbalancer

```
kubectl apply -f https://raw.githubusercontent.com/cloudnloud/Kubernetes_Admin_Training/main/class6-service/service/loadbalancer-twitter.yml
```
kubectl get all -n twitter -o wide
kubectl get pod,services -n twitter -o wide

35.227.119.203:32001

access like above from all node machine ip addresses.it should work from all client machine ip address.then the proxy cluster is working good.
```
kubectl get services
kubectl describe services
```
*******************************************************************************************************************************
# Replica set

## single replica copies

```
kubectl apply -f https://raw.githubusercontent.com/cloudnloud/Kubernetes_Admin_Training/main/class8-replicaset/replicaset/1-singlereplica.yml
```

```
kubectl get all -n twitter
```

```
kubectl describe replicas -n twitter
```

```
kubectl get pod,service,replicaset -n twitter -o wide
```


labels --> service -- selector , replica -- match ---> should be same


```
kubectl delete -f https://raw.githubusercontent.com/cloudnloud/Kubernetes_Admin_Training/main/class8-replicaset/replicaset/1-singlereplica.yml
```



**************************************************************************************************************************************
## Multi POD Multi replica copies


```
kubectl apply -f https://raw.githubusercontent.com/cloudnloud/Kubernetes_Admin_Training/main/class8-replicaset/replicaset/3-multireplica.yaml
```

```
kubectl get all -n facebook
```

```
kubectl describe replicas -n facebook
```
```
kubectl get pod,service,replicaset -n facebook -o wide
```


labels --> service -- selector , replica -- match ---> should be same

```
kubectl delete -f https://raw.githubusercontent.com/cloudnloud/Kubernetes_Admin_Training/main/class8-replicaset/replicaset/3-multireplica.yaml
```


********************************************************************************************************************************************
# Deployments

```
kubectl apply -f https://raw.githubusercontent.com/cloudnloud/Kubernetes_Admin_Training/main/class9-Deployments/deployments/1-deployment-facebook.yaml
```

```
kubectl get all -n facebook -o wide
```
```
kubectl describe deploy
```

```
kubectl get pod,service,replicaset,deploy -n facebook -o wide
```


to understand please delete one pod (before delete in another master ssh window keep run the watch -n 1 kubectl get all -n facebook -o wide command)

kubectl delete pod/nginx-74fcc59689-kn5j2 -n facebook

then 

run the below command again

```
kubectl get pod,service,replicaset,deploy -n facebook -o wide
```


rolling update --> https://raw.githubusercontent.com/cloudnloud/Kubernetes_Admin_Training/main/class9-Deployments/deployments/commands.txt



************************************************************************************************************************************************


# Daemonset

- DaemonSets can improve the performance of a Kubernetes cluster by distributing maintenance tasks and support services via deploying Pods across all nodes. They are well suited for long-running services like monitoring or log collectio

kubectl get nodes

If all nodes are ready state then we can start the below daemonset handson lab.

goto mater node and run the below yaml file

```
kubectl apply -f https://raw.githubusercontent.com/cloudnloud/Kubernetes_Admin_Training/main/class10-Daemonset/daemonset-nginx.yaml
```

```
kubectl get daemonsets
```

```
kubectl descibe daemonsets
```

**************************************************************************************************************************************************
# init Container

You must have 1 master and 2 worker nodes setup.

```
kubectl get nodes
```

the above command output should show all nodes are ready state.

create new namespace called app1

```
kubectl create ns app1
```

run the below command in one separate putty window in master server

kubectl apply -f https://raw.githubusercontent.com/cloudnloud/Kubernetes_Admin_Training/main/class11-initcontainers/node-redis/node.yml -n app1

first deploy this and run below command

you will see init is waiting.coz the dependency is not yet launched or stopped

kubectl apply -f https://raw.githubusercontent.com/cloudnloud/Kubernetes_Admin_Training/main/class11-initcontainers/node-redis/redis.yml -n app1

if we deploy redis.yaml (kubectl apply -f redis.yaml then you run the kubectl get all command you will now see running state)

**************************************************************************************************************************************************

