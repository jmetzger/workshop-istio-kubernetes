# Tracing with jaeger 

## Prerequisites 

  * Jaeger is setup
  * ambbient profile is set up
  * bookinfo is rolled out

## Note: 

 
  * You need so set up a telemetry - object
  * kubectl -n istio-system get cm values # What is actually configured 
    

## Prep:

```
# Activate tracing in istio 
cat <<EOF > ./tracing.yaml
apiVersion: install.istio.io/v1alpha1
kind: IstioOperator
spec:
  profile: ambient
  meshConfig:
    enableTracing: true
    defaultConfig:
      tracing: {} # disable legacy MeshConfig tracing options
    extensionProviders:
    - name: jaeger
      opentelemetry:
        port: 4317
        service: jaeger-collector.istio-system.svc.cluster.local
EOF
istioctl install -f ./tracing.yaml --skip-confirmation
```

## Walktrough 

```
cd
mkdir -p manifests/tracing-jaeger
cd manifests/tracing-jaeger
```

```
nano 01-telemetry-jaeger.yaml 
```

```
apiVersion: telemetry.istio.io/v1
kind: Telemetry
metadata:
  name: mesh-default
  namespace: istio-system
spec:
  tracing:
  - providers:
    - name: jaeger
```

```
kubectl apply -f .
```

## Test it: 

```
# Adjust to your own IP 
GATEWAY_URL=164.90.237.35
for i in $(seq 1 100); do curl -s -o /dev/null "http://$GATEWAY_URL/productpage";
```

## Open jaeger.tlnxx.do.t3isp.de 

```
Search ->
```

<img width="436" height="147" alt="image" src="https://github.com/user-attachments/assets/0bb5069b-ee3c-4799-91fa-35b498a3c047" />

<img width="444" height="61" alt="image" src="https://github.com/user-attachments/assets/97451972-7070-46ab-a4e4-39dc144f60b1" />


## Cleanup 

```
kubectl delete -f .
```



## Reference 

  * https://istio.io/latest/docs/tasks/observability/distributed-tracing/jaeger/
