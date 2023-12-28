# podman-tutorial
## This tutorial tested on cent-os 8.0 - should be similar on ubuntu

# Installation of K3s in Centos 8
## Step 1: Update OS and install Kubectl

### Install Kubectl,

Go to the root path of the master node and run the below commands: 
```
curl -LO https://dl.k8s.io/release/v1.26.0/bin/linux/amd64/kubectl
chmod +x kubectl
cp kubectl /usr/bin
```

Check the kubectl version: 
```
kubectl version --short 
```

## Step 2: Install K3s in the Master server

Use below command in master server to install k3s: 
```
curl -sfL https://get.k3s.io | sh -
```

Check k3s status:

```
systemctl status k3s
```

The k3s config file is in the below path (in Master node):
```
cat /etc/rancher/k3s/k3s.yaml
```

Copy the config file:
```
mkdir ~/.kube
cp /etc/rancher/k3s/k3s.yaml ~/.kube/config

```


Check setup:
```
kubectl get nodes
```

## Step 3 : Install k3s agent in WorkerNode
Go to the worker node and execute the following command:
```
curl -sfL https://get.k3s.io | K3S_URL=${k3s_Master_url} K3S_TOKEN=${k3s_master_token} sh -
k3s_Master_url = https://k3smaster.devopsart.com:6443
k3s_master_token= "Get the token from the master by executing the below command"
cat /var/lib/rancher/k3s/server/node-token 
```

Check k3s agent status:
```
systemctl status k3s-agent.service
```

## Step 4: K3s Installation validation

In the master node, check that the new worker node is listed:
```
kubectl get nodes
```


## Step 5: Deploy the Nginx webserver in K3s and validate (We will do this step once we create pod.yaml from podman later or use helm now)
We are using helm chart.

### Helm install
```
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
cp -r /usr/local/bin/helm /usr/bin/helm

Add Bitnami repo in Helm:
helm repo add bitnami https://charts.bitnami.com/bitnami

Deploy Nginx webserver by using below helm command:
helm install nginx-web bitnami/nginx
```

Check the pod status:
```
kubectl get pods -o wide
```

The Nginx pod is running fine now, Access Nginx webserve or test using curl.

```
kubectl port-forward pod/webserver :8080
curl localhost:8080
```

## Installing Podman:
### Ubuntu:
```
sudo apt-get install runc -y
sudo apt-get -y install podman
```

### centOs 8
```
sudo yum -y install podman
podman version
```

## Podman Container Registry Configurations:
find the default Podman container registry configuration in the following file:
```
/etc/containers/registries.conf
```
Default registries:
```
- quay.io
- docker.io
```

### Login docker.io:
```
podman login docker.io
```

## Podman Container Storage:
Each system user has its own container storage. 
For Example:
```
1. For root users, the containers are stored in /var/lib/containers/storage directory.
2. For other users, the containers are stored in $HOME/.local/share/containers/storage/ directory.
```
## Managing Containers With Podman:
We can use same commands as docker but, use podman instead of docker.

For Example:
```
podman pull docker.io/nginx
podman pull quay.io/quay/busybox
```

Run nginx and expose port 8080:
```
podman  run --name nginx -p 8080:80 docker.io/nginx
```

We can't use ports below 1024 in rootless mode. 
Because the normal user container namespace doesn't have privileges to map those ports. 
If We want to map host ports less than 1024 using podman, we should run podman as the root user or with sudo privileges.

```
sudo podman  run --name nginx -p 8080:80 docker.io/nginx
```

To check the mapped port do:
```
podman port -l
```

To Inspect the mapped port do:
```
podman inspect -l
```

Example podman commands:

```
podman images
podman ps
podman ps -a
podman stop <container-name>
podman rm <container-name>
podman -h or podman --help

```

## Building Container Image With Podman









