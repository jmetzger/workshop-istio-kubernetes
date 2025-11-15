# Installatios-Konfigurations-Profile 

  * istio verwendet verschiedene vorgefertigte Profile, die das Ausrollen (installieren) erleichtern
  * Diese können in istioctl verwendet werden, aber auch mit dem helm-chart

## Welcher Profile gibt es ? 

  * Es gibt deployment - profile und platform profile

## Übersicht der Deployment - Profile 

| Core components        | default | demo | minimal | remote | empty | preview | ambient |
|------------------------|---------|------|---------|--------|-------|---------|---------|
| istio-egressgateway    |         | ✓    |         |        |       |         |         |
| istio-ingressgateway   | ✓       | ✓    |         |        |       | ✓       |         |
| istiod                 | ✓       | ✓    | ✓       |        |       | ✓       | ✓       |
| CNI                    |         |      |         |        |       |         | ✓       |
| Ztunnel                |         |      |         |        |       |         | ✓       |


## Reference:

  * https://istio.io/latest/docs/setup/additional-setup/config-profiles/
