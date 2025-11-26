# Installation istioctl with profile demo and gateway - api (ambient)

## Walkthrough 



```
istioctl install --set profile=ambient --skip-confirmation
kubectl get crd gateways.gateway.networking.k8s.io &> /dev/null || \
kubectl apply --server-side -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.4.0/experimental-install.yaml
```

```
kubectl -n istio-system edit felixconfiguration default
# auf Disabled setzen
# bpfConnectTimeLoadBalancing: Disabled
```

## Läuft alles ? Alle Pods ready 

```
kubectl -n istio-system get pods
```
