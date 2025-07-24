# Tools zum Aufspüren von veralteten Helm - Charts und Images 

## Trivvy -> speziell Trivvy Operator 

```
helm repo add aqua https://aquasecurity.github.io/helm-charts/
helm repo update
```

```
helm install trivy-operator aqua/trivy-operator \
     --namespace trivy-system \
     --create-namespace \
     --version 0.29.3
```

## Vorgehen. In regelmäßigen Abständen per cronjob scannen 


## VulnerabilityReport werden erstellt 

```yaml

apiVersion: aquasecurity.github.io/v1alpha1
kind: VulnerabilityReport
metadata:
  name: replicaset-nginx-6d4cf56db6-nginx
  namespace: default
  labels:
    trivy-operator.container.name: nginx
    trivy-operator.resource.kind: ReplicaSet
    trivy-operator.resource.name: nginx-6d4cf56db6
    trivy-operator.resource.namespace: default
    resource-spec-hash: 7cb64cb677
  uid: 8aa1a7cb-a319-4b93-850d-5a67827dfbbf
  ownerReferences:
    - apiVersion: apps/v1
      controller: true
      kind: ReplicaSet
      name: nginx-6d4cf56db6
      uid: aa345200-cf24-443a-8f11-ddb438ff8659
report:
  artifact:
    repository: library/nginx
    tag: '1.16'                                   # 👈 indicates this outdated version
  registry:
    server: index.docker.io
  scanner:
    name: Trivy
    vendor: Aqua Security
    version: 0.35.0
  summary:
    criticalCount: 2
    highCount: 0
    mediumCount: 0
    lowCount: 0
    unknownCount: 0
  vulnerabilities:
    - vulnerabilityID: CVE-2019-20367
      resource: libbsd0
      severity: CRITICAL
      installedVersion: 0.9.1-2
      fixedVersion: 0.9.1-2+deb10u1
      primaryLink: https://avd.aquasec.com/nvd/cve-2019-20367
      target: library/nginx:1.21.6               # indicates version where it's fixed
    - vulnerabilityID: CVE-2018-25009
      resource: libwebp6
      severity: CRITICAL
      installedVersion: 0.6.1-2
      fixedVersion: ""                          # no patch yet
      primaryLink: https://avd.aquasec.com/nvd/cve-2018-25009
      target: library/nginx:1.16

```

