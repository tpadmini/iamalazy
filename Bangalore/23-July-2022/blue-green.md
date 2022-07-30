# Blue Green Deployment

- First we are going to deploy Blue Environment
- Secondly we are going to deploy Green Environment

```
kubectl apply -f https://raw.githubusercontent.com/cloudnloud/Kubernetes_Admin_Training/main/class17-blue-green-deployment/blue-deployment.yml -n app
```

```
kubectl apply -f https://raw.githubusercontent.com/cloudnloud/Kubernetes_Admin_Training/main/class17-blue-green-deployment/green-deployment.yml -n app
```

```
kubectl apply -f https://raw.githubusercontent.com/cloudnloud/Kubernetes_Admin_Training/main/class17-blue-green-deployment/blue-green-service.yml -n app
```

```
wget https://raw.githubusercontent.com/cloudnloud/Kubernetes_Admin_Training/main/class17-blue-green-deployment/blue-green-service.yml

```

vim blue-green-service.yml 

```
kubectl apply -f blue-green-service.yml -n app

```
vim blue-green-service.yml 

```
kubectl apply -f blue-green-service.yml -n app
```
