# Experimental Configurations

## Install Cilium using Helm

Cilium CLI has some disadvantages, for example, it does not support all the Helm
options (some is overriden by the autoconfig functionality). If you want to use
all the Helm options, you can install Cilium using Helm.

Cilium CLI does not support upgrading existing Cilium cluster and thus Helm is a
better option for upgrading Cilium.

```bash
helm upgrade --install \
  --namespace kube-system \
  --repo https://helm.cilium.io cilium cilium \
  --version 1.12.2 \
  --values config/cilium-values.yaml \
  --debug \
  --wait
```

## Install MetalLB on KIND

Loadbalancing in KIND is not supported out of the box. We can use MetalLB to
provide a loadbalancer for our services. This is documented in the [KIND
Loadbalancer documentation][kind-lb-docs].

[kind-lb-docs]: https://kind.sigs.k8s.io/docs/user/loadbalancer/

```bash
kubectl apply -f config/metallb-native.yaml
```

Get the current network range for the kind cluster:

```bash
docker network inspect -f '{{.IPAM.Config}}' kind
```

Configure MetalLB with the correct subnets:

```bash
cat <<EOF | grep kubectl apply -f -
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: example
  namespace: metallb-system
spec:
  addresses:
  - 172.18.255.200-172.18.255.250
---
apiVersion: metallb.io/v1beta1
kind: L2Advertisement
metadata:
  name: empty
  namespace: metallb-system
EOF
```

