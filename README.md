# freeradius-k8s

Deploy RADIUS deployment and service
```
kubectl apply -f 01-deployment.yml 
kubectl apply -f 02-service.yml 
kubectl create configmap freeradius --from-file authorize --from-file clients.conf 
```

Update authorize file
```
kubectl delete configmaps freeradius 
kubectl create configmap freeradius --from-file authorize --from-file clients.conf --from-file radiusd.conf
kubectl rollout restart deployment freeradius 
```
