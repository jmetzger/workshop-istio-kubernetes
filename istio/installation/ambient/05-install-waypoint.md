# Install waypoint

## Walkthrough 

```
# Deploy it
istioctl waypoint apply -n bookinfo --enroll-namespace 
```

```
kubectl -n bookinfo get pods | grep way 
```

## Reference 

  * https://istio.io/latest/docs/ambient/usage/waypoint/#do-you-need-a-waypoint-proxy
