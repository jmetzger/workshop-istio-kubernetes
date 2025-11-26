# Training istio & Kubernetes - Aufzeichnungen 


```
Training Istio 
24.11. - 26.11.2025 

# Jochen bewerten 

https://g.page/r/CZcuN2PgwThxEAE/review



# Dokumentation 

## Übung 3.12. Jaeger 

https://github.com/jmetzger/workshop-istio-kubernetes/blob/main/istio/exercises/ambient/09-tracing-jaeger.md


## Übung 3.11.5 jaeger installieren 

https://github.com/jmetzger/workshop-istio-kubernetes/blob/main/istio/installation/addons/jaeger.md

## Übung 3.11 Kiali 

https://github.com/jmetzger/workshop-istio-kubernetes/blob/main/istio/installation/addons/kiali.md


## Übung 3.10  Prometheus 

https://github.com/jmetzger/workshop-istio-kubernetes/blob/main/istio/installation/addons/prometheus.md




## Übung 3.9. request routing

https://github.com/jmetzger/workshop-istio-kubernetes/blob/main/istio/exercises/gateway-api/02-exercise-request-routing.md


## Übung 3.8 install waypoint 

https://github.com/jmetzger/workshop-istio-kubernetes/blob/main/istio/installation/ambient/05-install-waypoint.md


## Übuung 3.7 demo - app installieren 

https://github.com/jmetzger/workshop-istio-kubernetes/blob/main/istio/installation/ambient/04-install-demo-app.md

## Übung 3.6 installation ambient 

https://github.com/jmetzger/workshop-istio-kubernetes/blob/main/istio/installation/ambient/03-install-with-istioctl-with-demo-profile.md
kubectl -n istio-system edit felixconfiguration default
# 
 bpfConnectTimeLoadBalancing: Disabled


## Übung 3.5 Installation 



## Übung 3.4 Aufräumen 

istioctl uninstall -y --purge 
kubectl delete ns bookinfo 


## Übung 3.3 logs von envoy 

https://github.com/jmetzger/workshop-istio-kubernetes/blob/main/istio/exercises/14-logging-in-envoy.md

## Übung 3.2 


## Übung 3.1. authorizationPolicy 

https://github.com/jmetzger/workshop-istio-kubernetes/blob/main/istio/exercises/12-deny-all-plus-rules.md



## Übung 2.9 bookinfo traffic-shifting

https://github.com/jmetzger/workshop-istio-kubernetes/blob/main/istio/exercises/04-traffic-shifting.md

## Analyse 

kubectl -n bookinfo exec "$(kubectl -n bookinfo get pod -l app=ratings -o jsonpath='{.items[0].metadata.name}')" -c ratings -- curl -sS productpage:9080/productpage | grep -o "<title>.*</title>"


kubectl -n bookinfo run -it --rm podtest  --image=busybox -- sh 

# In der Shell 
wget -O -  productpage:9080/productpage
wget -O -  productpage:9080/productpage | grep -o "<title>.*</title>"



https://github.com/jmetzger/workshop-istio-kubernetes

## Übung 2.8 bookinfo installieren 

https://github.com/jmetzger/workshop-istio-kubernetes/blob/main/istio/installation/04-install-demo-app-bookinfo.md

##  Übung 2.7 istiocl inject 

https://github.com/jmetzger/workshop-istio-kubernetes/blob/main/istio/exercises/01-exercise-injection.md

## Übung 2.6 istio installieren / istioctl und demo 

https://github.com/jmetzger/workshop-istio-kubernetes/blob/main/istio/installation/03-install-with-istioctl-with-demo-profile.md#schritt-1-istio-runterladen-und-installieren


## Info 2.5 istio performance 

https://github.com/jmetzger/workshop-istio-kubernetes/blob/main/istio/overview/performance-comparison-baseline-sidecar-ambient.md

## Info 2.4 istio Architektur

https://github.com/jmetzger/workshop-istio-kubernetes/blob/main/istio/overview/01-introduction.md


## Übung 2.3. helm

https://github.com/jmetzger/workshop-istio-kubernetes/blob/main/helm/exercises/04a-create-chart-my-app-gruppenarbeit.md



## Übung 2.2 secret 

kubectl create secret generic mariadb-secret --from-literal=MARIADB_ROOT_PASSWORD=11abc432 --dry-run=client -o yaml > 01-secrets.yml

kubectl apply -f 01-secrets.yml
kubectl describe secret mariadb-secret 
kubectl get secret mariadb-secret -o yaml 

# in dem deployment ändern configmap -> secretRef 
# auch den name in mariadb-secret und neu applien 
# Deployment 
kubectl apply -f 02-deploy.yml 

echo  MTFhYmM0MzI=  | base64 -d 
kubectl exec deployment/mariadb-deployment -- env



nur als Anhaltspunkt:
    https://github.com/jmetzger/workshop-istio-kubernetes/tree/main/kubectl-examples/kubectl-examples

## Übung 2.1 configmap 

https://github.com/jmetzger/workshop-istio-kubernetes/blob/main/kubectl-examples/06a-configmap-mariadb.md


## Übung 1.23 Solution 

https://github.com/jmetzger/workshop-istio-kubernetes/blob/main/kubectl-examples/04-ingress-nginx-with-hostnames.md#solution


## Übung 1.22 Schritt 1:
     https://github.com/jmetzger/workshop-istio-kubernetes/blob/main/kubectl-examples/04-ingress-nginx-with-hostnames.md#step-1-walkthrough   


## Info 1.21 Eure Subdomain 

*.tln1.do.t3isp.de 




## Übung 1.20 Wo ist der IngressController 

kubectl get ns 
kubectl -n ingress-nginx get all 

## Übung 1.19 type:LoadBalancer

# in 02-svc.yaml 
# type: NodePort ändern in type:LoadBalancer 
kubectl apply -f . 
kubectl get svc my-nginx 



## Übung 1.18 nodePort

kubectl -n ingress-nginx get svc 
cd 
cd manifests 
cd 04-service
nano 02-svc.yml 
# im Service -> ClusterIP auf NodePort 
kubectl apply -f .
kubectl get svc my-nginx  

# <external-ip>:32326 
# 164.90.236.159:32326 




## Übung 1.17 Statefulset

https://github.com/jmetzger/workshop-istio-kubernetes/blob/main/kubectl-examples/10-statefulset.md


## Übung 1.16 DNS Auflösung

https://github.com/jmetzger/workshop-istio-kubernetes/blob/main/kubernetes-networks/dns-resolution-services.md

## Übung 1.15 Debugging pod 

## rauslesen service ip und die pod ips 
kubectl describe svc my-nginx 

https://github.com/jmetzger/workshop-istio-kubernetes/blob/main/tipps-tricks/verbindung-zu-pod-testen.md

## Übung 1.14 Service 

https://github.com/jmetzger/workshop-istio-kubernetes/blob/main/kubectl-examples/03b-service.md#example-service

## Übung 1.13 explain 

kubectl explain deploy 
kubectl explain deploy.spec 

## Übung 1.12 Deployment 

https://github.com/jmetzger/workshop-istio-kubernetes/blob/main/kubectl-examples/03-nginx-deployment.md


### Übung 1.11 replicaset 

https://github.com/jmetzger/workshop-istio-kubernetes/blob/main/kubectl-examples/01a-replicaset-nginx.md

### Übung 1.10 get nodes

kubectl get nodes 
kubectl get nodes -o wide 

## Info 1.9 Replicaset 

https://github.com/jmetzger/training-kubernetes-einfuehrung/blob/main/kubectl-examples/01a-replicaset-nginx.md

## INfo 1.8. Beispielapplikation 

https://github.com/jmetzger/workshop-istio-kubernetes/blob/main/bauen-einer-webanwendung.md


## Übung 1.7. 

https://github.com/jmetzger/workshop-istio-kubernetes/blob/main/kubectl/kubectl-einrichten.md#arbeitsbereich-konfigurieren

## Übung 1.6 does not work 

https://github.com/jmetzger/workshop-istio-kubernetes/blob/main/kubectl/run-with-example.md#example-that-does-not-work

## Übung 1.5 pod describe 

kubectl describe po nginx 



## Übung 1.4 Pod starten 

https://github.com/jmetzger/workshop-istio-kubernetes/blob/main/kubectl/run-with-example.md
https://github.com/jmetzger/training-kubernetes-einfuehrung/blob/main/kubectl/run-with-example.md#example-that-does-work

## Übung 1.3 Funktion testen kubernetes

cd 
cd .kube
cat config 

kubectl cl<tab>
kubectl cluster-info 
kubecl config view 

## Übung 1.2 

https://github.com/jmetzger/workshop-istio-kubernetes/blob/main/istio/training-stack/install.md


## Übung 1.1



# Zeiten 

09.00 - 10:30 Block I 
10:30 - 10:45 Frühstückspause 
10:45 - 12:00 Block II 
12:00 - 13:00 Mittag 
13:00 -14:30 Block III 
14:30 - 14:45 Teatime
14:45 - 16:30 Block IV 


# Agenda 

Grundlagen & Installation von Istio
Einführung in Istio & Service Mesh-Architekturen
Warum Service Mesh? Herausforderungen & Vorteile
Architektur & Komponenten von Istio
Vergleich mit Linkerd, Cilium, ConsulInstallation

Bereitstellung von Istio
Systemanforderungen & Kubernetes-Cluster-Vorbereitung
Istio-Installation mit istioctl, Helm & Istio-Operator
Einführung in Sidecar Injection & Proxy-Konzepte

Steuerung des Netzwerkverhaltens in Istio
Virtual Services, Destination Rules & Gateway-API
Load Balancing, Header- & Pfadbasiertes Routing
Erstellen von Routing- & Load-Balancing-Regeln

Sicherheit, Fehlertoleranz & Observability
Sicherheit & Zero Trust mit Istio
Service-zu-Service-Sicherheit mit mTLS (Mutual TLS)
Zugriffskontrolle mit RBAC & JWT-Authentifizierung
Istio Authorization Policies (ingress/egress Security)
Sichere Service-Kommunikation mit Istio konfigurieren

Service Resilience & Fehlertoleranz
Circuit Breaker, Retries, Rate Limiting
Fehlerinjektion & Chaos Engineering mit Istio
Belastungstests mit Fehlerinjektionen

Monitoring, Logging & Observability
Distributed Tracing mit Jaeger
Metriken & Dashboards mit Prometheus & Grafana
Service-Visualisierung mit Kiali
Analyse & Debugging von Service-Mesh-Daten

(Skalierung, Erweiterbarkeit & Performance-Optimierung) - niedrige Prio 
Skalierung von Istio & Performance-Optimierung
Sidecar-Overhead & Ressourcenoptimierung
Ambient Mesh (sidecar-less Istio für Performance-Gewinn)
Multi-Cluster- & Hybrid-Umgebungen mit Istio
Istio Federation & Cross-Cluster Traffic

Erweiterte Routing-Techniken & Traffic-Optimierung
Canary Releases & Progressive Deployments
A/B-Tests & Traffic Mirroring
Blue-Green- und Canary-Deployments mit Istio

Erweiterbarkeit & Automatisierung mit Istio (niedrige Prio)
WebAssembly (Wasm) für Istio-Erweiterungen
Automatisierung mit GitOps & (ArgoCD)
Eigene Istio-Erweiterungen mit WebAssembly schreiben

FAQ & Best Practices
Zusammenfassung der wichtigsten Erkenntnisse
Diskussion von Best Practices für Enterprise-Anwendungen

```
Fragen & weiterführende Ressourcen

