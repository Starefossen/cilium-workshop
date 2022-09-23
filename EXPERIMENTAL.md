# Experimental Configurations

### With kind on Coloima

[`kind`][kind] is a tool for running local Kubernetes clusters using Docker container “nodes”.

[kind]: https://kind.sigs.k8s.io/

Install kind and colima using brew:

```bash
brew install kind colima
```

Start colima (we reccomend at least `4 vCPU` and `8GB RAM`):

```bash

```bash
colima start --cpu 4 --memory 8
```

Check that docker is working:

```bash
docker ps
```

Create a new KIND cluster:

```bash
kind create cluster --name cilium-workshop --config config/kind-config.yaml
```

<details>
  <summary>kind create cluster output</summary>

  ```bash
  Creating cluster "cilium-workshop" ...
   ✓ Ensuring node image (kindest/node:v1.25.2) 🖼
   ✓ Preparing nodes 📦 📦 📦 📦
   ✓ Writing configuration 📜
   ✓ Starting control-plane 🕹️
   ✓ Installing StorageClass 💾
   ✓ Joining worker nodes 🚜
  Set kubectl context to "kind-cilium-workshop"
  You can now use your cluster with:

  kubectl cluster-info --context kind-cilium-workshop

  Have a nice day! 👋
  ```
</details>

Set your current kubeconfig context to the new cluster:

```bash
kubectl config use-context kind-cilium-workshop
```

Check that you have a working cluster:

```bash
kubectl cluster-info
```

<details>
  <summary>kubectl cluster-info output</summary>

  ```bash
  Kubernetes control plane is running at https://127.0.0.1:61148
  CoreDNS is running at https://127.0.0.1:61148/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

  To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
  ```
</details>

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

