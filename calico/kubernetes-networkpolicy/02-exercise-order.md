# Exercise: Calico NetworkPolicy Order

## Übersicht
Diese Übung demonstriert, wie die `order` Eigenschaft bei Calico NetworkPolicies die Evaluierungs-Reihenfolge bestimmt.

## Vorbereitung

### Step 1: Test-Namespace und Pods erstellen

```bash
kubectl create namespace order-test
kubectl config set-context --current --namespace=order-test
```

```bash
nano test-pods.yaml
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app
  namespace: order-test
spec:
  replicas: 1
  selector:
    matchLabels:
      app: web-app
  template:
    metadata:
      labels:
        app: web-app
        role: backend
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: client-app
  namespace: order-test
spec:
  replicas: 1
  selector:
    matchLabels:
      app: client-app
  template:
    metadata:
      labels:
        app: client-app
        role: frontend
    spec:
      containers:
      - name: busybox
        image: busybox
        command: ["sleep", "3600"]
---
apiVersion: v1
kind: Service
metadata:
  name: web-service
  namespace: order-test
spec:
  selector:
    app: web-app
  ports:
  - port: 80
    targetPort: 80
```

```bash
kubectl apply -f test-pods.yaml
```

## Step 2: Baseline Test - Verbindung funktioniert

```bash
kubectl get pods -n order-test
```

```bash
# Test: Client kann Web-App erreichen
kubectl exec -it deployment/client-app -n order-test -- wget -O- --timeout=5 http://web-service
```

**Erwartung:** Verbindung erfolgreich (nginx HTML-Seite)

## Step 3: Policies mit unterschiedlicher Order erstellen

### Policy 1: Default Deny (hohe Order = später evaluiert)

```bash
nano 01-default-deny.yaml
```

```yaml
apiVersion: projectcalico.org/v3
kind: NetworkPolicy
metadata:
  name: default-deny
  namespace: order-test
spec:
  order: 1000  # Hohe Order = wird zuletzt evaluiert
  selector: all()
  types:
  - Ingress
  - Egress
  ingress:
  - action: Deny
  egress:
  - action: Deny
```

```bash
kubectl apply -f 01-default-deny.yaml
```

### Test nach Default Deny

```bash
# Test: Sollte jetzt fehlschlagen
kubectl exec -it deployment/client-app -n order-test -- wget -O- --timeout=5 http://web-service
```

**Erwartung:** Verbindung blockiert (Timeout)

### Policy 2: Allow für Frontend (mittlere Order)

```bash
nano 02-allow-frontend.yaml
```

```yaml
apiVersion: projectcalico.org/v3
kind: NetworkPolicy
metadata:
  name: allow-frontend-egress
  namespace: order-test
spec:
  order: 500  # Mittlere Order = wird vor Default Deny evaluiert
  selector: role == 'frontend'
  types:
  - Egress
  egress:
  - action: Allow
    destination:
      selector: role == 'backend'
      ports: [80]
```

```bash
kubectl apply -f 02-allow-frontend.yaml
```

### Policy 3: Allow für Backend Ingress (mittlere Order)

```bash
nano 03-allow-backend.yaml
```

```yaml
apiVersion: projectcalico.org/v3
kind: NetworkPolicy
metadata:
  name: allow-backend-ingress
  namespace: order-test
spec:
  order: 500  # Mittlere Order = wird vor Default Deny evaluiert
  selector: role == 'backend'
  types:
  - Ingress
  ingress:
  - action: Allow
    source:
      selector: role == 'frontend'
    destination:
      ports: [80]
```

```bash
kubectl apply -f 03-allow-backend.yaml
```

### Test nach Allow-Policies

```bash
# Test: Sollte wieder funktionieren
kubectl exec -it deployment/client-app -n order-test -- wget -O- --timeout=5 http://web-service
```

**Erwartung:** Verbindung erfolgreich (Allow-Policies mit Order 500 überschreiben Deny mit Order 1000)

## Step 4: Override mit niedriger Order demonstrieren

### Policy 4: Emergency Deny (niedrigste Order = zuerst evaluiert)

```bash
nano 04-emergency-deny.yaml
```

```yaml
apiVersion: projectcalico.org/v3
kind: NetworkPolicy
metadata:
  name: emergency-deny
  namespace: order-test
spec:
  order: 100  # Niedrigste Order = wird zuerst evaluiert
  selector: role == 'frontend'
  types:
  - Egress
  egress:
  - action: Deny
    destination:
      ports: [80]
```

```bash
kubectl apply -f 04-emergency-deny.yaml
```

### Test nach Emergency Deny

```bash
# Test: Sollte wieder fehlschlagen
kubectl exec -it deployment/client-app -n order-test -- wget -O- --timeout=5 http://web-service
```

**Erwartung:** Verbindung blockiert (Emergency Deny mit Order 100 hat Vorrang vor Allow mit Order 500)

## Step 5: Policy-Reihenfolge verstehen

### Aktuelle Policies anzeigen

```bash
# Alle Policies mit Order anzeigen
kubectl get networkpolicy -n order-test -o custom-columns=NAME:.metadata.name,ORDER:.spec.order
```

### Evaluierungs-Reihenfolge:
1. **Order 100:** emergency-deny (DENY für Port 80)
2. **Order 500:** allow-frontend-egress (ALLOW für Backend)
3. **Order 500:** allow-backend-ingress (ALLOW von Frontend)
4. **Order 1000:** default-deny (DENY all)

**Resultat:** Emergency Deny (Order 100) gewinnt → Verbindung blockiert

## Step 6: Override mit noch niedrigerer Order

### Policy 5: Super Emergency Allow (niedrigste Order)

```bash
nano 05-super-emergency-allow.yaml
```

```yaml
apiVersion: projectcalico.org/v3
kind: NetworkPolicy
metadata:
  name: super-emergency-allow
  namespace: order-test
spec:
  order: 50  # Niedrigste Order = allererste Evaluation
  selector: role == 'frontend'
  types:
  - Egress
  egress:
  - action: Allow
    destination:
      selector: role == 'backend'
      ports: [80]
```

```bash
kubectl apply -f 05-super-emergency-allow.yaml
```

### Finale Tests

```bash
# Test: Sollte wieder funktionieren
kubectl exec -it deployment/client-app -n order-test -- wget -O- --timeout=5 http://web-service
```

**Erwartung:** Verbindung erfolgreich (Super Emergency Allow mit Order 50 überschreibt alle anderen)

### Final Policy Order:
1. **Order 50:** super-emergency-allow (ALLOW) ← **GEWINNT**
2. **Order 100:** emergency-deny (DENY)
3. **Order 500:** allow-policies (ALLOW)
4. **Order 1000:** default-deny (DENY)

## Step 7: GlobalNetworkPolicy vs NetworkPolicy Order

### Wichtiges Verhalten testen: Global kommt immer zuerst!

```bash
nano 06-global-deny.yaml
```

```yaml
apiVersion: projectcalico.org/v3
kind: GlobalNetworkPolicy
metadata:
  name: global-emergency-deny
spec:
  order: 9999  # Sehr hohe Order - sollte eigentlich zuletzt kommen
  namespaceSelector: kubernetes.io/metadata.name == "order-test"
  selector: role == 'frontend'
  types:
  - Egress
  egress:
  - action: Deny
    destination:
      ports: [80]
```

```bash
kubectl apply -f 06-global-deny.yaml
```

### Test: Global Policy überschreibt lokale Policy

```bash
# Test: Sollte jetzt fehlschlagen!
kubectl exec -it deployment/client-app -n order-test -- wget -O- --timeout=5 http://web-service
```

**Erwartung:** Verbindung blockiert - **obwohl die GlobalNetworkPolicy Order 9999 hat!**

### Warum? Evaluierungs-Reihenfolge:

```
1. GlobalNetworkPolicy (order: 9999) ← Wird ZUERST evaluiert trotz hoher Order
2. NetworkPolicy (order: 50)         ← Wird DANACH evaluiert  
3. NetworkPolicy (order: 100)
4. NetworkPolicy (order: 500)
5. NetworkPolicy (order: 1000)
```

### Global Policy mit niedriger Order

```bash
nano 07-global-allow.yaml
```

```yaml
apiVersion: projectcalico.org/v3
kind: GlobalNetworkPolicy
metadata:
  name: global-emergency-allow
spec:
  order: 1  # Niedrigste Order bei Global Policies
  namespaceSelector: kubernetes.io/metadata.name == "order-test"
  selector: role == 'frontend'
  types:
  - Egress
  egress:
  - action: Allow
    destination:
      selector: role == 'backend'
      ports: [80]
```

```bash
kubectl apply -f 07-global-allow.yaml
```

### Test: Erste Global Policy gewinnt

```bash
# Test: Sollte funktionieren
kubectl exec -it deployment/client-app -n order-test -- wget -O- --timeout=5 http://web-service
```

**Erwartung:** Verbindung erfolgreich (Global Allow Order 1 überschreibt Global Deny Order 9999)

### Finale Evaluierungs-Reihenfolge:

```
GLOBAL POLICIES (werden immer zuerst evaluiert):
1. GlobalNetworkPolicy global-emergency-allow (order: 1)     ← GEWINNT
2. GlobalNetworkPolicy global-emergency-deny (order: 9999)

NAMESPACE POLICIES (werden danach evaluiert):
3. NetworkPolicy super-emergency-allow (order: 50)
4. NetworkPolicy emergency-deny (order: 100)
5. NetworkPolicy allow-policies (order: 500)  
6. NetworkPolicy default-deny (order: 1000)
```

## Step 8: Cleanup

```bash
# Global Policies löschen
kubectl delete globalnetworkpolicy global-emergency-deny
kubectl delete globalnetworkpolicy global-emergency-allow

# Namespace löschen (löscht auch alle NetworkPolicies)
kubectl delete namespace order-test
```

## Zusammenfassung

- **Niedrige Order-Zahlen werden ZUERST evaluiert**
- **GlobalNetworkPolicy wird IMMER vor NetworkPolicy evaluiert** (unabhängig von Order!)
- **Order gilt nur innerhalb des Policy-Typs** (Global oder Namespace)
- **Erste matchende Regel gewinnt**
- **Order ermöglicht präzise Kontrolle über Policy-Hierarchien**
- **Nützlich für Override-Patterns und Emergency-Scenarios**

### Wichtige Erkenntnis:
```
GlobalNetworkPolicy (order: 9999) schlägt NetworkPolicy (order: 1)
```