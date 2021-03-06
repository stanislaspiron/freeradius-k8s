# Deploy FreeRadius server in kubernetes

## Description
This replository contains Yaml Files to deploy freeradius in Kubernetes cluster.
This repository is not certified to work in a production environment. 

## Config Files
This freeradius deployment uses following files:
- radiusd.conf : contains the radius configuration. this file was edited to stdout to see authentication logs with *kubectl logs* command. This file is stored in a configmap object.
- clients.conf : contains the RADIUS clients with shared secrets. This file is stored in a secret object as it contains sensitive data.
- authorize : contains usernames, passwords and attributes for each users. This file is stored in a secret object as it contains sensitive data.

All other configuration files are inherited from docker image, including EAP certificates and trusted CA.

## Requirements
This deployment requires following configuration:
- kubernetes cluster. This was tested in [Microk8s](https://github.com/stanislaspiron/microk8s_awx/blob/main/microk8s/microk8s_install.md)
- [metallb](https://github.com/stanislaspiron/microk8s_awx/blob/main/microk8s/install_metallb.md) load balancer

## Install 
Update the service file with expected Metallb IP address.


Deploy RADIUS deployment and service
```
kubectl create ns radius
kubectl create configmap -n radius freeradius-files --from-file radiusd.conf
kubectl create secret generic -n radius freeradius-secrets --from-file authorize --from-file clients.conf 
kubectl apply -f freeradius.yml
```

## Update configuration files
Update configmap with new files and reload deployment
```
kubectl delete configmaps -n radius freeradius-files 
kubectl create configmap -n radius freeradius-files --from-file radiusd.conf
kubectl rollout restart deployment -n radius freeradius 
```

## Update configuration files with sensitive data (secret)
Update secret with new files and reload deployment
```
kubectl delete secret -n radius freeradius-secrets 
kubectl create secret generic -n radius freeradius-secrets --from-file authorize --from-file clients.conf 
kubectl rollout restart deployment -n radius freeradius 
```

# Use kubernetes service name from other deployments in same cluster

To allow other deployments within the same kubernetes cluster, use following name:

```
lb-freeradius.radius.svc.cluster.local
```

- lb-freeradius : service name
- radius : namespace name
- svc.cluster.local : internal kubernetes dns suffix
