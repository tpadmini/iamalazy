# AZURE Kubernetes Automation

# Before start this excercise,you must have running Rocky 8 linux machine.


# AZURE VM
 - create 1 rocky linux 8.5 machine in AZURE Cloud
 - make sure you opened RDP,http,8080 ports for this new linux AZURE VM
 
# Steps

- Step 1:  Install AZURE CLI in rocky linux 8.5 Machine
- Step 2:  First install docker in the local linux machine
- Step 3:  Download sample application
- Step 4:  Test sample application
- Step 5:  Deploy and use Azure Container Registry
- Step 6:  Install kubectl command 
- Step 7:  Deploy an Azure Kubernetes Service (AKS) cluster
- Step 8:  Run applications in Azure Kubernetes Service (AKS)
- Step 9:  Scale applications in Azure Kubernetes Service (AKS)
- Step 10: Autoscale pods
- Step 11: Manually scale AKS nodes (worker nodes)
- Step 12: Update an application in Azure Kubernetes Service (AKS)
- Step 13: now deploy sample application in kubernetes cluster in new namespace


#

# Step 1: Install AZURE CLI

- Add the azure CLI Repository keys
```
sudo rpm --import https://packages.microsoft.com/keys/microsoft.asc
```
- Configure the azure CLI Repository

```
sudo sh -c 'echo -e "[azure-cli]
name=Azure CLI
baseurl=https://packages.microsoft.com/yumrepos/azure-cli
enabled=1
gpgcheck=1
gpgkey=https://packages.microsoft.com/keys/microsoft.asc" > /etc/yum.repos.d/azure-cli.repo'
```
- Install Azure CLI

```
sudo yum install azure-cli -y
```

- Login and sync your Azure CLI (Linux machine) to your portal.azure.com account

```
az login
```

- Now your linux machine has been fully authenticated with AZURE login.  

# Step 2 - first install docker in the local linux machine

```
 yum install epel-release -y
 yum repolist
```

## Step 2.1 - Install the required dependencies:

```
yum install yum-utils device-mapper-persistent-data lvm2  bash-completion yum-utils -y
```

```
source /etc/profile.d/bash_completion.sh
```


## Step 2.2 - Add the stable Docker repository by typing:

```
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

## Step 2.3 - Now that we have Docker repository enabled, we can install the latest version of Docker CE (Community Edition) using yum by typing:

```
yum install docker-ce --allowerasing -y


```

## Step 2.4 - Install Docker Compose and Test the Docker-Compose Version:


- Run the below command to download the current stable release of Docker compose.

```
sudo curl -L "https://github.com/docker/compose/releases/download/1.26.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/bin/docker-compose
```

- Apply the executable permission for the binary file which we have downloaded.

```
chmod +x /usr/bin/docker-compose
```

- If the docker compose is installed on a different location For example: /usr/local/bin/ , You can copy the executable to /usr/bin directory.

- You can check the version of docker-compose using the below command.

```
docker-compose --version
```


## Step 2.5 - Once the Docker package is installed, we start the Docker daemon with:

```
systemctl start docker;systemctl status docker;systemctl enable docker
```

## Step 2.6 - To verify that the Docker service is running type:

```
systemctl status docker
```


## Step 2.7 - Enable the Docker service to be automatically started at boot time:

```
systemctl enable docker
```


## Step 2.8 - At the time of the writing of this article, the current stable version of Docker is 18.03.1, we can check our Docker version by typing:

```
docker -v
```


# Step 3: Download sample application

```
yum install git tree -y

```
- Download the sample azure application

```
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

```
cd azure-voting-app-redis
```
- Build the docker image using docker compose

```
docker-compose up -d
```
- list the docker images

```
docker images
```

# Step 4: Test sample application

- Test application locally

- To see the running application, enter http://localhost:8080 in a local web browser

- To see the running application, enter http://<your VM Public IP>:8080 in a local web browser

# Step 5: Deploy and use Azure Container Registry

- Create AZURE resource Group

```
az group create --name myResourceGroup --location eastus
```

- Create AZURE Container Registry Under the above Created Resource Group.


```
az acr create --resource-group myResourceGroup --name cnlacr1 --sku Basic
```

- Login to the Azure Container registry


```
az acr login --name cnlacr1
```

- List the Docker Images


```
docker images
```

- List the no of ACR in your portal.azure.com


```
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

- you will get the below output.So your AZURE Container Registry Name is below ...


```
cnlacr1.azurecr.io
```


- you need to change the docker image name towards to match your ACR repo name.Or else while you push the image will end up with error.


```
docker tag mcr.microsoft.com/azuredocs/azure-vote-front:v1 cnlacr1.azurecr.io/azure-vote-front:v1
```
- List the docker images

- Make sure you are seeing the docker image name with match to your ACR repository.

```
docker images
```
- Push your prepared custom image to your newly created ACR.

```
docker push cnlacr1.azurecr.io/azure-vote-front:v1
```

- list the ACR repository revisions

```
az acr repository list --name cnlacr1 --output table
```
- List your repository in ACR.

```
az acr repository show-tags --name cnlacr1 --repository azure-vote-front --output table
```


# Step 6: install kubectl command 

- Configure Kubernetes Software package repo.

```
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
EOF
```

- Install Kubectl

```
yum install -y kubectl
```

- To ensure all are ok the following command should work without any error

```
kubectl version --client
```

# Step 7: Deploy an Azure Kubernetes Service (AKS) cluster

- Create kubernetes Cluster with 2 worker Node

- In your learning setup,if you have project portal.azure.com test account then increase node count from 1 to 3.


```
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --generate-ssh-keys \
    --attach-acr cnlacr1
	
```
- Install AZURE AKS CLI

```
az aks install-cli
```
- Reterive the AKS cluster credentials.This will help kubectl command to run without any issues.

```
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```
- List all the nodes in Kubernetes cluster.

```
kubectl get nodes
```


# Step 8: Run applications in Azure Kubernetes Service (AKS)

- List the ACR 

```
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```
- Change your front end to your recently deployed own image repo.

```
vi azure-vote-all-in-one-redis.yaml
```


```
containers:
- name: azure-vote-front
  image: cnlacr1.azurecr.io/azure-vote-front:v1
```
- Apply the changes into AKS k8s cluster.

```
kubectl apply -f azure-vote-all-in-one-redis.yaml
```
- Monitor the change progress.

```
kubectl get service azure-vote-front --watch
```


- from the above command output get the EXTERNAL-IP and access it from browser

# Step 9: Scale applications in Azure Kubernetes Service (AKS)

- in another putty window run the following command

```
watch -n 1 kubectl get all -o wide
```


- in another putty window run the below commands

```
kubectl get pods
```
- in another putty window run the below commands

```
kubectl scale --replicas=2 deployment/azure-vote-front
```

```
kubectl scale --replicas=4 deployment/azure-vote-front
```

# Step 10: Autoscale pods

- **Kubernetes supports horizontal pod autoscaling to adjust the number of pods in a deployment depending on CPU utilization or other select metrics. The Metrics Server is used to provide resource utilization to Kubernetes, and is automatically deployed in AKS clusters versions 1.10 and higher. To see the version of your AKS cluster, use the az aks show command, as shown in the following example:**

```
az aks show --resource-group myResourceGroup --name myAKSCluster --query kubernetesVersion --output table
```

- These days AKS cluster is above 1.10 only.


- now open 3 putty window and do the following


- In putty window 1 run the following command

```
watch -n 1 kubectl get hpa -o wide
```

- In putty window 2 run the following command

```
watch -n 1 kubectl get all -o wide
```

- In putty window 3 run the following command

```
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=2 --max=10
```

- wait for 10 mins and you can see azure-vote-front pod now came down to 2 

# Step 11: Manually scale AKS nodes (worker nodes)

- only you do this if you have more allocated resource portal.azure.com cloud account.


```
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 3
```

# Step 12: Update an application in Azure Kubernetes Service (AKS)

- Now we need to make some change in application

```
vi azure-vote/azure-vote/config_file.cfg
```

```
# UI Configurations
TITLE = 'I never fail'
VOTE1VALUE = 'Fail'
VOTE2VALUE = 'TryFailWin'
SHOWHOST = 'false'
```
- Build the Docker image with v2.
```
docker-compose up --build -d
```


- you need to change the docker image name towards to match your ACR repo name.Or else while you push the image will end up with error.


```
docker tag mcr.microsoft.com/azuredocs/azure-vote-front:v1 cnlacr1.azurecr.io/azure-vote-front:v2
```

- Push your prepared custom image to your newly created ACR.

```
docker push cnlacr1.azurecr.io/azure-vote-front:v2
```

- Scale your pods to 3 replicas

```
kubectl scale --replicas=3 deployment/azure-vote-front
```
- Set and prepare your deployment to new version 2
```
kubectl set image deployment azure-vote-front azure-vote-front=cnlacr1.azurecr.io/azure-vote-front:v2
```

- Run below command
```
kubectl get service azure-vote-front
```

- Now access your (external ip) in browser

- you will see Fail & TryFailWin

# Step 13: now deploy sample application in kubernetes cluster in new namespace

```
kubectl create ns k8sdemo

kubectl apply -f https://raw.githubusercontent.com/bvijaycom/meetup/main/guestbook-all-in-one.yaml -n k8sdemo

```

## Step 13.1: open putty in the 2nd window and run the below command

```
watch -n 1 kubectl get all -n k8sdemo -o wide


```

## Step 13.2: now open new putty window and execute the below load script [replace the external ip with what you get from above command output]


```
while true; do
curl -m 1 http://20.102.24.82/;
sleep 5;
done

```

- go to virtual machine scale sets....

- you will see 2 nodes .After navigating to the pane of the scale set, go to the Instances view, select the instance you want to shut down, and then hit the Stop button


- access http://20.102.24.82/ and keep submit the entries..Keep watch the **watch -n 1 kubectl get all -n k8sdemo -o wide** command output

- **What you see here is the following:**
-  The Redis master pod running on node 2 got terminated as the host became unhealthy.
- A new Redis master pod got created, on host 0. This went through the stages **Pending, ContainerCreating, and then Running**.


## Step 13.3: Solving out-of-resource failure

- Kubernetes uses requests to calculate how much CPU power or memory a certain pod requires. The guestbook application has requests defined for all the deployments. If you open the guestbook-all-in-one.yaml file in the folder. you'll see the following for the redis-replica deployment:

```
63 kind: Deployment
64 metadata:
65 name: redis-replica
...
83 resources:
84 requests:
85 cpu: 200m
86 memory: 100Mi

```

- This section explains that every pod for the redis-replica deployment requires 200m of a CPU core (200 milli or 20%) and 100MiB (Mebibyte) of memory. In your 2 CPU clusters (with node 1 shut down), scaling this to 10 pods will cause issues with the available resources. Let's look into this:
	
- To clarify what's described here in the Kubernetes context, 1 CPU is the same as a core (Also more information here).

- 1000m (milicores) = 1 core = 1 vCPU = 1 AWS vCPU = 1 GCP Core.
- 100m (milicores) = 0.1 core = 0.1 vCPU = 0.1 AWS vCPU = 0.1 GCP Core.
- For example, an Intel Core i7-6700 has four cores, but it has Hyperthreading which doubles what the system sees in terms of cores. So in essence, it will show up in Kubernetes as: **8000m = 8 cores = 8 vCPUs**

## Step 13.4: Let's start by scaling the redis-replica deployment to 10 pods:

- kubectl scale deployment/redis-replica --replicas=10 -n k8sdemo

- **Step 13.5:** This will cause a couple of new pods to be created. We can check our pods  using the following:
- now many are shown in pending state.This occurs if the cluster is out of resources.

## Step 13.6: We can get more information about these pending pods using the following command:
 
```
kubectl describe pod redis-replica-5bc7bcc9c4-svcc8 -n k8sdemo
```

now you will get the following output

```
Events:
  Type     Reason            Age                  From               Message
  ----     ------            ----                 ----               -------
  Warning  FailedScheduling  46s (x2 over 2m14s)  default-scheduler  0/2 nodes are available: 1 Insufficient cpu, 1 node(s) had taint {node.kubernetes.io/unreachable: }, that the pod didn't tolerate.

```

# now start the stopped node from virtual scale set

## Keep watch the **watch -n 1 kubectl get all -n k8sdemo -o wide** command
