# freeradius-k8s

## Limitations
This repository was built for a LAB... The goal is not to be used in production because shared secrets and user password are in clear text.

# Config Files
This freeradius deployment uses following files:
- clients.conf : contains the RADIUS clients with shared secrets
- radiusd.conf : contains the radius configuration. this file was edited to stdout to see authentication logs with *kubectl logs* command
- authorize : contains usernames, passwords and attributes for each users

# Install 
Deploy RADIUS deployment and service
```
kubectl apply -f 01-deployment.yml 
kubectl apply -f 02-service.yml 
kubectl create configmap freeradius --from-file authorize --from-file clients.conf 
```

# Update files
Update configmap with new files and reload deployment
```
kubectl delete configmaps freeradius 
kubectl create configmap freeradius --from-file authorize --from-file clients.conf --from-file radiusd.conf
kubectl rollout restart deployment freeradius 
```
