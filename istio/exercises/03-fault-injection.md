# Fault Injection (sidecar-mode) 

  * The gateway api does not support fault-injection (so the http-route object)
  * We are using the gateway api, but can still use the VirtualService for Service-2-Service call inside the mesh

## Walkthrough 

### Step 1: Prepare 

```
cd
mkdir -p manifests/fault-injection
cd manifests/fault-injection
```

```
cp -a ~/istio/samples/bookinfo/networking/virtual-service-all-v1.yaml . 
cp -a ~/istio/samples/bookinfo/networking/virtual-service-reviews-test-v2.yaml .
kubectl apply -f . 
```
