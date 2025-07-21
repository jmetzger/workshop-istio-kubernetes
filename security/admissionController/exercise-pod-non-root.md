# Exercise: Pod Non-Root User Enforcement

## Objective
Configure admission control to enforce that pods run as non-root users for enhanced security.

## Prerequisites
- Kubernetes cluster with admission controller support
- kubectl configured with cluster admin privileges
- Basic understanding of SecurityContext and Pod Security Standards

## Exercise Steps

### Step 1: Create a Pod Security Policy (if using PSP)

```yaml
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: non-root-policy
spec:
  privileged: false
  allowPrivilegeEscalation: false
  requiredDropCapabilities:
    - ALL
  volumes:
    - 'configMap'
    - 'emptyDir'
    - 'projected'
    - 'secret'
    - 'downwardAPI'
    - 'persistentVolumeClaim'
  runAsUser:
    rule: 'MustRunAsNonRoot'
  seLinux:
    rule: 'RunAsAny'
  fsGroup:
    rule: 'RunAsAny'
```

### Step 2: Configure Pod Security Standards (Recommended)

Create a namespace with restricted security standards:

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: secure-namespace
  labels:
    pod-security.kubernetes.io/enforce: restricted
    pod-security.kubernetes.io/audit: restricted
    pod-security.kubernetes.io/warn: restricted
```

### Step 3: Test with Root User Pod (Should Fail)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: root-pod
  namespace: secure-namespace
spec:
  securityContext:
    runAsUser: 0
  containers:
  - name: test-container
    image: nginx:1.20
    securityContext:
      runAsUser: 0
```

### Step 4: Test with Non-Root User Pod (Should Succeed)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: non-root-pod
  namespace: secure-namespace
spec:
  securityContext:
    runAsUser: 1000
    runAsGroup: 1000
    fsGroup: 1000
  containers:
  - name: test-container
    image: nginx:1.20
    securityContext:
      runAsNonRoot: true
      runAsUser: 1000
      allowPrivilegeEscalation: false
```

## Verification

1. Apply the restricted namespace configuration
2. Try to deploy the root pod - it should be rejected
3. Deploy the non-root pod - it should succeed
4. Verify the pod is running with the specified user ID

```bash
kubectl get pods -n secure-namespace
kubectl exec non-root-pod -n secure-namespace -- id
```

## Expected Results

- Root pod creation should fail with admission controller rejection
- Non-root pod should deploy successfully
- User ID verification should show UID 1000

## Notes

- Pod Security Standards are the recommended approach over Pod Security Policies
- Always test policies in a development environment first
- Consider using OPA Gatekeeper for more complex policy enforcement