# Ingress Istio-Gateway vs. Kubernetes Gateway API 

## Istio Gateway

<img width="1267" height="184" alt="image" src="https://github.com/user-attachments/assets/41d95be5-8713-4533-9821-ea8e2ed5a0f5" />

## Gateway API 

<img width="1024" height="703" alt="image" src="https://github.com/user-attachments/assets/a8be8bb3-a761-4fc2-b482-9a6798b49eda" />


   * Achtung:  Beim Sidecar-Mode wird nachwievor VirtualService benötigt (aber nur intern innerhalb des Cluster), der Traffik
     * von ausserhalb wird über das Gateway dargestellt  

## Bild-Quelltext (Istio)

```
graph LR
    Client[Client/Browser]
    IG[Istio IngressGateway<br/>istio-ingressgateway Pod]
    GW[Gateway Resource<br/>istio.networking.v1beta1]
    VS[VirtualService<br/>routing rules]
    SvcA[Service A]
    SvcB[Service B]
    
    Client -->|HTTP/HTTPS| IG
    IG -.->|references| GW
    GW -.->|bound to| VS
    VS -->|route to| SvcA
    VS -->|route to| SvcB
    
    style IG fill:#e1f5ff
    style GW fill:#fff3cd
    style VS fill:#fff3cd
```

## Bild-Quelltext (Kubernetes Gateway API)

```mermaid
graph TB
    Client[Client/Browser]
    GWC[GatewayClass<br/>infrastructure.cluster.x-k8s.io]
    GW[Gateway<br/>gateway.networking.k8s.io]
    HR[HTTPRoute<br/>routing rules]
    SvcA[Service A]
    SvcB[Service B]
    
    Client -->|HTTP/HTTPS| GW
    GW -.->|instance of| GWC
    HR -.->|attached to| GW
    HR -->|route to| SvcA
    HR -->|route to| SvcB
    
    style GWC fill:#d4edda
    style GW fill:#d4edda
    style HR fill:#d4edda
```
