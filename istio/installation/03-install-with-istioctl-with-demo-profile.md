# Normal installation ("demo") with classical istiogateway (legacy)

[Searching for installation with gateway api](istio/installation/gateway-api/03-install-with-istioctl-with-demo-profile.md)


# Install with istioctl 

  * Most simplistic way
  * Doing the right setup is done with profiles
  * Interestingly it uses an compile-in helm chart  (see also: Show what a profile does)

## Hint for production 

  * Best option (in most cases) is default 

## in our case: Including demo (tracing is activated) 

  * Not suitable for production !!

## Show what a profile does 

```
istioctl manifest generate > istio-manifest.yaml
# If not profile is mentioned, it uses the default profile
# it does not use an operator 
cat istio-manifest.yaml | grep -i -A20 "^Kind" | less
# If you want you can apply it like so:
# kubectl apply -f istio-manifest.yaml 

```

## Installation including Demo 

> [!CAUTION]
> This profile (demo) enables high levels of tracing and access logging so it is not suitable for performance tests.

### Schritt 1: istio runterladen und installieren 

```
cd 
# current version of istio is 1.28.0
curl -L https://istio.io/downloadIstio | sh -
ln -s ~/istio-1.28.0 ~/istio
echo "export PATH=~/istio-1.28.0/bin:$PATH" >> ~/.bashrc
source ~/.bashrc 
```

### Schritt 2: bash completion integrieren 

```
cp ~/istio/tools/istioctl.bash ~/istioctl.bash
echo "source ~/istioctl.bash" >> ~/.bashrc
source ~/istioctl.bash
```

### Schritt 2.5. See what it would install 

```
# dry-run
istioctl x precheck 
istioctl install -f  ~/istio/manifests/profiles/demo.yaml --dry-run
```

### Schritt 3: Installation with demo (by using operator)

```
apiVersion: install.istio.io/v1alpha1
kind: IstioOperator
spec:
  components:
    egressGateways:
    - name: istio-egressgateway
      enabled: true
  values:
    profile: demo
```


```
istioctl install -f ~/istio/manifests/profiles/demo.yaml -y
```

### Schritt 4: Let us check, if it is running 

```
kubectl -n istio-system get all
``` 
