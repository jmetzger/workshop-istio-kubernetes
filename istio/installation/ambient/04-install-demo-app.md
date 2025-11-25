# Demo - app installation

## Überblick 

<img width="693" height="465" alt="image" src="https://github.com/user-attachments/assets/22cbf386-5a90-458b-8157-51620ef829ea" />


## Vorbereitung

```
kubectl create ns bookinfo
kubectl label namespace bookinfo istio.io/dataplane-mode=ambient
```

## bookdemo app ausrollen 


```
kubectl -n bookinfo apply -f  ~/istio/samples/bookinfo/platform/kube/bookinfo.yaml
kubectl -n bookinfo apply -f samples/bookinfo/platform/kube/bookinfo-versions.yaml

kubectl -n bookinfo get all 
```

## testen ob die app funktioniert 

```
kubectl -n bookinfo exec "$(kubectl -n bookinfo get pod -l app=ratings -o jsonpath='{.items[0].metadata.name}')" -c ratings -- curl -sS productpage:9080/productpage | grep -o "<title>.*</title>"
```

## App mit gateway api nach aussen öffnen 

```
# That's what we do ....
cat  ~/istio/samples/bookinfo/gateway-api/bookinfo-gateway.yaml
```

```
kubectl -n bookinfo apply -f ~/istio/samples/bookinfo/gateway-api/bookinfo-gateway.yaml
kubectl -n bookinfo get gateways
kubectl -n bookinfo get httproutes -o yaml 
```

```
# not the external-ip from this output
# gateway automatically creates a service 
kubectl -n bookinfo get svc bookinfo-gateway-istio
```

```
http://<external-ip>/productpage 
# or in your browser
```

## Ref: 

  * https://istio.io/latest/docs/ambient/getting-started/deploy-sample-app/
