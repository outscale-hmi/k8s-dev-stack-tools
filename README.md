# k8s-dev-stack-tools

k8s-dev-stack-tools install infrastructure app in order to devellop with a kubernetes stack

## What is deployed 

This stack is deployed by fluxv2.

This stack is currently composed of:

* Harbor

* Prometheus

* osc-bsu-csi-driver

* cert-manager

* cloud-provider-osc

## How to deployed it

### Delete ingress nginx validationWebhookConfiguration

```
kubectl delete -A ValidatingWebhookConfiguration ingress-nginx-admission
```

### Create local secret

You need to create your own grafana secret:
```
kubectl create ns prometheus
kubectl create secret generic grafana-secret --from-literal=username=myuser --from-literal=password=mypassword -n prometheus
```

### Install flux
Install flux:
https://fluxcd.io/docs/installation/

### Patch nginx to loadbalancer

We patch nginx service to loadbalancer
```
kubectl patch svc ingress-nginx-controller-admission -n ingress-nginx  -p '{"spec": {"ports": [{"port": 443,"targetPort": 443,"name": "https"},{"port": 80,"targetPort": 80,"name": "http"}],"type": "LoadBalancer"}}'
```

