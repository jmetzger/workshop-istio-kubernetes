# HashiCorp Vault sidecar 

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: example-app
  namespace: myapp
spec:
  replicas: 1
  selector:
    matchLabels: { app: example-app }
  template:
    metadata:
      labels:
        app: example-app
    spec:
      serviceAccountName: vault-auth
      containers:
      - name: app
        image: mycompany/app:latest
        volumeMounts:
        - name: config
          mountPath: /home/app/

      - name: vault-agent
        image: hashicorp/vault:1.15
        args:
          - "agent"
          - "-config=/etc/vault/agent/vault-agent-config.hcl"
        volumeMounts:
        - name: vault-agent-config
          mountPath: /etc/vault/agent/
        - name: templates
          mountPath: /etc/vault/templates/
        - name: config
          mountPath: /home/app/

      volumes:
      - name: vault-agent-config
        configMap:
          name: vault-agent-config
      - name: templates
        configMap:
          name: vault-templates
      - name: config
        emptyDir: {}
```

