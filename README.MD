# NGINX Demo Deployment

This repository contains a directory named `app/nginxdemo` which contains all the necessary manifests for deploying an nginxhelo demo application.

## Prerequisites
Before deploying the nginxhelo demo application, ensure the following prerequisites are met:
- Kubernetes cluster is running (local/ cloud based).
- `kubectl` command-line tool is installed and configured to communicate with the Kubernetes cluster on which you want to apply the manifest.

## Deployment Instructions

### Step 1: Deploy the namespace
Execute the following command to deploy the namespace:
```bash
kubectl apply -f app/nginxdemo/namespace.yaml
```

I chose to create a separate namespace for the NGINX demo application to keep the resources isolated from other applications on my local k8s cluster. All of the resources created in the steps below will be deployed in this namespace.

### Step 2: Deploy the ServiceAccount
Execute the following command to deploy the ServiceAccount:
```bash
kubectl apply -f app/nginxdemo/serviceaccount.yaml
```

### Step 3: Deploy the secret
Execute the following command to deploy
```bash
kubectl apply -f app/nginxdemo/secret.yaml
```

Secret contains api_key for as a proof of concept on the usage of secrets in k8s. This secret is later used in deployment via environment variables. The value of the api_key is encoded in base64 and is only a **placeholder**.

### Step 4: Deploy nginxdemos/hello Deployment
Execute the following command to deploy the NGINX Deployment:
```bash
kubectl apply -f app/nginxdemo/deployment.yaml
```

The img used in the deployment is [nginxdemos/hello](https://hub.docker.com/r/nginxdemos/hello). I chose this image as it is a simple NGINX web server that returns a page containing its server address, server name (pod name), date of request and URI. The deployment also contains an environment variable `API_KEY`. And it's strategy is set to `RollingUpdate` with maxSurge and maxUnavailable set to 1 to meet the requriements of the assignment.

### Step 5: Deploy the Service
Execute the following command to deploy the NGINX Service:
```bash
kubectl apply -f app/nginxdemo/service.yaml
```

The service is of type `NodePort` and exposes the deployment on port 30000. So to access the application, you can use the following URL: `localhost:30000`. The localhost only works if you are running the same cluster as me so the docker desktop k8s cluster. In other cases you have to use the IP of the node where the service is running.

### Step 6: Deploy the hpa
Execute the following command to deploy the HPA:
```bash
kubectl apply -f app/nginxdemo/hpa.yaml
```

The HPA is set to scale the deployment to a minimum of 2 pod and a maximum of 5 pods based on the CPU utilization. The target CPU utilization is set to 80%.

### Step 7: Deploy the pdb
Execute the following command to deploy the PDB:
```bash
kubectl apply -f app/nginxdemo/pdb.yaml
```

The PDB is set to not allow more than 1 pod to be unavailable at a time. This is to ensure that the application is always available.

### Step 8: Verify the deployment
To verify the deployment, execute the following command:
```bash
kubectl get all -n=devopsgroup
```

This command will list all the resources created in the `devopsgroup` namespace. You should see the following resources:
- Deployment
- Pod
- ReplicaSet
- Service
- HPA
You can also verify that the pods are running via the URL mentioned in Step 5. I verified all of the resources via OpenLens as I'm used to it.

## Deployment environment
- Docker Desktop Kubernetes Cluster
- kubectl version: v1.28.3
- Docker version: 20.10.23
- OS: macOS Sonoma 14.4.1
