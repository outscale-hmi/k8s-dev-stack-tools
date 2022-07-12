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

### Deploy csi

You need to deploy your csi secret:

```
kubectl create secret generic  osc-csi-bsu --from-literal=access_key=my_access_key --from-literal=secret_key=my_secret_key -n kube-system
```

### Deploy ccm

You need also to deploy your own k8s secret and deploy ccm:
(https://github.com/outscale-dev/cloud-provider-osc/blob/OSC-MIGRATION/deploy/README.md)
```
kubectl apply -f secrets.yaml
kubectl apply -f https://raw.githubusercontent.com/outscale-dev/cloud-provider-osc/v0.0.9beta/deploy/osc-ccm-manifest.yml
```
### Patch nginx to loadbalancer

We patch nginx service to loadbalancer

```
kubectl patch svc ingress-nginx-controller-admission -n ingress-nginx  -p '{"spec": {"ports": [{"port": 443,"targetPort": 443,"name": "https"},{"port": 80,"targetPort": 80,"name": "http"}],"type": "LoadBalancer"}}'
```



### Install flux

Install flux:

https://fluxcd.io/docs/installation/


### Change ingress hostname

Get the ip and change in your repo with ssip.io format:
```
ping b3207e05ec3e43d9a525119763f6af78-129110916.eu-west-2.lbu.outscale.com (142.44.33.69)
```


in harbor and prometheus release file:
```
 hosts:
            - grafana-dev.142-44-33-69.sslip.io
```

### Destroy CCM

Please destroy your ccm because it is already include in flux


### Add your registry

You can also add your registry in your cluster.

If you have rkev1:
https://rancher.com/docs/rke/latest/en/config-options/private-registries/
