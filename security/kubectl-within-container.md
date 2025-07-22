# Use kubectl with inClusterConfig in Containe 

```
kubectl create ns busybox-sa
kubectl -n busybox-sa run -it podtest --image=alpine
```

```
# in der shell
apk add kubectl 
# Verwendet /var/run/secrets/kubernetes.io/serviceaccount
kubectl auth can-i get pods
kubectl get pods
``` 
