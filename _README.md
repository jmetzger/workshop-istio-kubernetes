# Istio & Kubernetes 


## Agenda
1. Aufzeichnungen aus yopad
   * [Aufzeichnungen](aufzeichnungen-yopad.md)

1. Einführung Kubernetes
   * [Aufbau Kubernetes](/kubernetes/architecture.md)
   * [Pod starten](/kubectl/run-with-example.md)
   * [Namespace/Arbeitsbereich konfigurieren](kubectl/kubectl-einrichten.md#arbeitsbereich-konfigurieren)
   * [Beispiel-Applikation](bauen-einer-webanwendung.md)
   * [Übung ReplicaSet(nicht für Produktion)](kubectl-examples/01a-replicaset-nginx.md)
   * [Übung Deployment](kubectl-examples/03-nginx-deployment.md)

1. Services 
   * [Debug Netzwerkverbindung zu Pod/Service](tipps-tricks/verbindung-zu-pod-testen.md)
   * [DNS Auflösung](/kubernetes-networks/dns-resolution-services.md)

1. StatefulSet
   * [StatefulSet](kubectl-examples/10-statefulset.md)
  
1. ConfigMaps
   * [Exercise configmap](/kubectl-examples/06a-configmap-mariadb.md)

1. Spickzettel kubectl Kubernetes
   * [Spickzettel kubectl](kubectl/spickzettel.md)

1. Grundlagen ServiceMesh & Istio
   * [Einführung in Istio & Service Mesh-Architekturen](istio/overview/01-introduction.md)
   * [Warum ein Service Mesh?](istio/overview/02-warum-servicemesh.md)
   * [Herausforderung & Vorteile](istio/overview/03-herausforderungen-vorteile.md)
   * [Architektur & Komponenten von Istio](istio/overview/04-architektur-komponenten.md)
   * [Istio Ingress Gateway vs. Kubernetes Gateway API](istio/overview/06-ingress-istio-gateway-vs-kubernetes-gateway-api.md)
   * [Ingress vs. Istio (sidecar)](istio/00-istio-vs-ingress.md)
   * [Vergleich mit Linkerd, Cilium, Consul](istio/overview/05-vergleich-linkerd-cilium-consul.md)

1. Setup Cluster
   * [Self-Service Cluster ausrollen](/istio/training-stack/install.md)
   * [Self-Service Cluster destroy](/istio/training-stack/uninstall.md)
    
1. Installation & Bereitstellung von Istio (Gateway API)
   * [Systemanforderungen & Kubernetes-Cluster-Vorbereitung](istio/requirements/overview.md)
   * [Installations-Config-Profile](istio/installation/02-installation-config-profiles.md)
   * [Istio-Installation mit istioctl und der IstioOperator - Resource - Legacy](istio/installation/03-install-with-istioctl-with-demo-profile.md)
   * [Wie ändere ich die Config/Installation von istio - Beispiel egressGateway](istio/installation/04-change-installation-with-istioctl-and-istioOperator.md)
   * [Istio Sidecar-Injection](istio/exercises/01-exercise-injection.md)
   * [Istio demo-app *bookinfo* installieren](istio/installation/04-install-demo-app-bookinfo.md)
   * Istio-Installation mit Helm
   * [Istio Proxy-Konzepte]
   * [Deinstallation von Istio](istio/installation/05-uninstall.md)

1. Installation (ambient)
   * [istio ambient installieren](istio/installation/ambient/03-install-with-istioctl-with-demo-profile.md)
   * [Install demoapp inkl verwendung von ambient](/istio/installation/ambient/04-install-demo-app.md)
   * [Install waypoint](istio/installation/ambient/05-install-waypoint.md)


1. Steuerung des Netzwerkverhaltens in Istio (ohne Gateway API mit klassisch Istio API / sidecar) 
   * Virtual Services, Destination Rules & Gateway-API
   * [Übung: Header-basiertes Routing](istio/exercises/02-exercise-request-routing.md)
   * [Übung: Traffic-Shifting / Load-Balancing](istio/exercises/04-traffic-shifting.md)
   * Load Balancing & Pfadbasiertes Routing
   * Erstellen von Routing- & Load-Balancing-Regeln

1. Steuerung des Netzwerkverhaltens in Istio (mit Gateway API und ambient mode)
   * [Übung: Header-basiertes Routing](istio/exercises/gateway-api/02-exercise-request-routing.md)

1. AuthorizationPolicy
   * [Deny für alles & erlaubt regeln](istio/exercises/12-deny-all-plus-rules.md)
  
1. Debug in Istio
   * [Debug in istio](istio/debug/01-debug-pod.md)

1. Sicherheit, Fehlertoleranz & Observability
   * [Sicherheit & Zero Trust mit Istio](/istio/overview/zero-trust.md)
   * [Was ist in istio deep-defense (defense in depth) ?](/istio/overview/defense-in-depth.md) 
   * [Service-zu-Service-Sicherheit mit mTLS (Mutual TLS) - Hintergründe & Analyse](istio/mtls/hintergruende-analyse.md) 
   * [Übung Zugriffskontrolle mit RBAC & JWT-Authentifizierung](istio/exercises/12-jwt-rbac.md)
   * Istio Authorization Policies (ingress/egress Security)
   * Sichere Service-Kommunikation mit Istio konfigurieren

1. Service Resilience & Fehlertoleranz
   * [Circuit Breaker](istio/exercises/06-circuit-breaker.md)
   * [Retries](istio/exercises/07-retries.md)
   * [Rate Limiting](/istio/exercises/08-local-rate-limit.md)
   * [Fehlerinjektion (z.B. 500er)](istio/exercises/03-fault-injection.md)
   * [Chaos Engineering mit Istio](https://istio.io/latest/docs/examples/microservices-istio/production-testing/)
   * Belastungstests mit Fehlerinjektionen

1. Monitoring, Logging & Observability
   * [Distributed Tracing mit Jaeger](istio/exercises/09-tracing-jaeger.md)
   * Metriken & Dashboards mit Prometheus & Grafana
   * [Installation Prometheus Addon with Ingress](istio/installation/addons/prometheus.md)
   * [Metriken mit Prometheus auswerten](istio/exercises/10-prometheus.md) 
   * [Installation Grafana Addon with Ingress](istio/installation/addons/grafana.md)
   * [Grafana Dashboards für istio](istio/exercises/11-grafana.md)
   * [Installaton Kiali // Installation](istio/installation/addons/kiali.md)
   * [Visualisierung mit Kiali](istio/exercises/05-kiali-traffic.md)
   * Analyse & Debugging von Service-Mesh-Daten

1. Skalierung, Erweiterbarkeit & Performance-Optimierung
   * Skalierung von Istio & Performance-Optimierung
   * Sidecar-Overhead & Ressourcenoptimierung
   * Ambient Mesh (sidecar-less Istio für Performance-Gewinn)
   * Multi-Cluster- & Hybrid-Umgebungen mit Istio
   * Istio Federation & Cross-Cluster Traffic

1. Erweiterte Routing-Techniken & Traffic-Optimierung
   * Canary Releases & Progressive Deployments
   * A/B-Tests & Traffic Mirroring
   * Blue-Green- und Canary-Deployments mit Istio

1. Erweiterbarkeit & Automatisierung mit Istio
   * WebAssembly (Wasm) für Istio-Erweiterungen
   * [Wo läuft WASM (WebAssembly) im Rahmen von istio ?](istio/wasm/plugins/where-is-the-wasm-runtime.md)
   * Automatisierung mit GitOps & ArgoCD
   * Eigene Istio-Erweiterungen mit WebAssembly schreiben

1. FAQ & Best Practices
   * Zusammenfassung der wichtigsten Erkenntnisse
   * Diskussion von Best Practices für Enterprise-Anwendungen
   * Fragen & weiterführende Ressourcen

1. Performance
   * [Performance Benchmark](/istio/overview/performance-comparison-baseline-sidecar-ambient.md)

1. Helm
   * [Artifacthub.io](https://artifacthub.io/)
   * [Eigenes helm-chart erstellen](helm/exercises/04a-create-chart-my-app-gruppenarbeit.md)

## Backlog 

1. Installation & Bereitstellung von Istio (Gateway API)
   * [Systemanforderungen & Kubernetes-Cluster-Vorbereitung](istio/requirements/overview.md)
   * [Installations-Config-Profile](istio/installation/02-installation-config-profiles.md)
   * [Istio-Installation mit istioctl und der IstioOperator - Resource](istio/installation/gateway-api/03-install-with-istioctl-with-demo-profile.md)
   * [Wie ändere ich die Config/Installation von istio - Beispiel egressGateway](istio/installation/04-change-installation-with-istioctl-and-istioOperator.md)
   * [Istio Sidecar-Injection](istio/exercises/01-exercise-injection.md)
   * [Istio demo-app *bookinfo* installieren](istio/installation/04-install-demo-app-bookinfo.md)
   * Istio-Installation mit Helm
   * [Istio Proxy-Konzepte]
   * [Deinstallation von Istio](istio/installation/05-uninstall.md)

<div class="page-break"></div>

