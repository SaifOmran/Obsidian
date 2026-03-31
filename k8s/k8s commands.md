### Pods
- To see all current pods
```Linux
kubectl get pods
```

- To see the master node components
```Linux
kubectl get pods -n kube-system
```

- To create a pod in imperative way 
```Linux
kubectl run [pod_name] --image [image]

kubectl run nginx-pod --image nginx:latest
```

- To create a pod in declarative way we use YAML file
![[Pasted image 20260330201456.png]]

- And then we use
```Linux
kubectl apply -f <file_name>
```

- To get info about a pod
```Linux
kubectl describe pod <pod_name> 
```


