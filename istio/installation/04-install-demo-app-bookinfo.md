# Demo - app installation

[Looking for installation with gateway api](istio/installation/gateway-api/04-install-demo-app-bookinfo.md)

## Überblick 

<img width="992" height="615" alt="image" src="https://github.com/user-attachments/assets/5773ce04-fd83-45a6-9914-d2b1b72c1505" />

## Vorbereitung

```
kubectl create ns bookinfo
kubectl label namespace bookinfo istio-injection=enabled
```

## bookdemo app ausrollen 


```
kubectl -n bookinfo apply -f  ~/istio/samples/bookinfo/platform/kube/bookinfo.yaml
kubectl -n bookinfo get all 
```

## testen ob die app funktioniert 

```
kubectl -n bookinfo exec "$(kubectl -n bookinfo get pod -l app=ratings -o jsonpath='{.items[0].metadata.name}')" -c ratings -- curl -sS productpage:9080/productpage | grep -o "<title>.*</title>"
```

```
kubectl -n bookinfo run -it --rm podtest  --image=busybox -- sh 

# In der Shell 
wget -O -  productpage:9080/productpage
wget -O -  productpage:9080/productpage | grep -o "<title>.*</title>"
```

## App mit gateway (istio-ingress-gateway) nach aussen öffnen  

```
cd
mkdir -p manifests/bookinfo
cd manifests/bookinfo
```

```
nano istio-ingress.yaml 
```

```
apiVersion: networking.istio.io/v1
kind: Gateway
metadata:
  name: bookinfo-gateway
spec:
  # The selector matches the ingress gateway pod labels.
  # If you installed Istio using Helm following the standard documentation, this would be "istio=ingress"
  selector:
    istio: ingressgateway # use istio default controller
  servers:
  - port:
      number: 80
      name: http
      protocol: HTTP
    hosts:
    - "*"
---
apiVersion: networking.istio.io/v1
kind: VirtualService
metadata:
  name: bookinfo
spec:
  hosts:
  - "*"
  gateways:
  - bookinfo-gateway
  http:
  - match:
    - uri:
        exact: /productpage
    - uri:
        prefix: /static
    - uri:
        exact: /login
    - uri:
        exact: /logout
    - uri:
        prefix: /api/v1/products
    route:
    - destination:
        host: productpage
        port:
          number: 9080
```

```
kubectl -n bookinfo apply -f .
```

```
kubectl -n bookinfo get gateways.networking.istio.io
kubectl -n bookinfo get virtualservice  -o yaml 
```

```
kubectl -n istio-system get svc | grep -i external 
http://<external-ip>/productpage 
# or in your browser
```



