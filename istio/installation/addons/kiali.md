# Install kiali inkl. Ingress (Ingress-Controller must be installed) 

## Prerequisites 

  * *.tlnx.do.t3isp.de subdomain is already set up and pointing to your cluster
  * 

## Step 1: Install addon kiali 

```
cd 
mkdir -p manifests/kiali
cd manifests/kiali
cp -a ~/istio/samples/addons/kiali.yaml .
kubectl apply -f .
kubectl -n istio-system get pods
kubectl -n istio-system get svc
```

## Step 2: Setup basic auth 

```
nano kiali-basic-auth.yaml
```

```
# Secret for basic auth: user "training" / password "myS3cr3t!"
# Already base64-encoded in htpasswd format.
apiVersion: v1
kind: Secret
metadata:
  name: kiali-basic-auth
  namespace: istio-system
type: Opaque
data:
  # htpasswd-style content:
  # training:$2b$12$CfOZaJ.Tr0zu6PfpbuCjzeKiQ2PzZARfP.CbC6tRU/7OvEHCIOREm
  auth: dHJhaW5pbmc6JDJiJDEyJENmT1phSi5UcjB6dTZQZnBidUNqemVLaVEyUHpaQVJmUC5DYkM2dFJVLzdPdkVIQ0lPUkVtCg==
```

```
kubectl apply -f .
```

### Step 3: Setup Ingress 
