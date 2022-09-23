# Workshop: Securing (and Observing) Kubernetes clusters with Cilium and eBPF

Getting Kubernetes up and running and deploying your first application is
relatively easy, managing them securely on scale however can be quite a
challenge. Knowing what applications are communicating with each other and how
to restrict, verify, and debug traffic policies is a real game changer for
complex environments.

## Getting Started

### Pre-requisites

* [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
* [cilium-cli](https://github.com/cilium/cilium-cli/releases)

<details>
  <summary>Install cilium-cli with asdf</summary>

  ```bash
  brew install asdf

  asdf plugin add cilium-cli
  asdf install cilium-cli latest
  asdf global cilium-cli latest
  ```
</details>

<details>
  <summary>Install kubectl with asdf</summary>

  ```bash
  brew install asdf

  asdf plugin add kubectl
  asdf install kubectl v1.23.6
  asdf global kubectl v1.23.6 latest
  ```

</details>

<details>
  <summary>Install docker cli with brew</summary>

  ```bash
  brew install docker-compose
  ```

  Compose is now a Docker plugin. For Docker to find this plugin, symlink it:

  ```bash
  mkdir -p ~/.docker/cli-plugins
  ln -sfn /opt/homebrew/opt/docker-compose/bin/docker-compose ~/.docker/cli-plugins/docker-compose
  ```
</details>

### Install Kubernetes with Minikube on Colima

[Minikube][minkube] is a tool that makes it easy to run Kubernetes locally.
Minikube runs a single-node Kubernetes cluster inside a VM on your laptop for
users looking to try out Kubernetes or develop with it day-to-day.

[Colima][colima] is a tool that makes it easy to run Kubernetes locally on Apple
Silicon Macs.

[minikube]: https://minikube.sigs.k8s.io/docs/start/
[colima]: https://github.com/abiosoft/colima

```bash

Install minikube and colima:

```bash
brew install minikube colima
```

Start colima:

```bash
colima start --cpu 4 --memory 8
```

Check that docker is working:

```bash
docker ps
```

Configure minikube to use colima:

```bash
minikube config set driver docker
minikube config set container-runtime docker
```

Start minikube:

```bash
minikube start \
  --profile cilium-workshop \
  --cpus max --memory max \
  --network-plugin=cni --cni=false \
  --kubernetes-version v1.24.6
```

<!---
nnodes 2
docker-opt="default-ulimit=nofile=102400:102400"
-->

## Install Cilium using cilium-cli

Verify that you have a working Kubernetes connection:

```bash
kubectl version
```

Run the following commands in order to set up Cilium:

```bash
cilium install \
  --version 1.12.2 \
  --helm-set image.pullPolicy=IfNotPresent \
  --helm-set ipam.mode=kubernetes
```

Verify that Cilium is running:

```bash
cilium status
```

<details>
  <summary>cilium status output</summary>

  ```bash
      /¯¯\
   /¯¯\__/¯¯\    Cilium:         OK
   \__/¯¯\__/    Operator:       OK
   /¯¯\__/¯¯\    Hubble:         disabled
   \__/¯¯\__/    ClusterMesh:    disabled
      \__/

  Deployment        cilium-operator    Desired: 1, Ready: 1/1, Available: 1/1
  DaemonSet         cilium             Desired: 4, Ready: 4/4, Available: 4/4
  Containers:       cilium             Running: 4
                    cilium-operator    Running: 1
  Cluster Pods:     3/3 managed by Cilium
  Image versions    cilium             quay.io/cilium/cilium:v1.12.2@sha256:986f8b04cfdb35cf714701e58e35da0ee63da2b8a048ab596ccb49de58d5ba36: 4
                    cilium-operator    quay.io/cilium/operator-generic:v1.12.2@sha256:00508f78dae5412161fa40ee30069c2802aef20f7bdd20e91423103ba8c0df6e: 1
  ```
</details>

<details>
  <summary>Check cilium conectivity (optional)</summary>

  ```bash
  cilium connectivity test
  ```
</details>

Enable Cilium Hubble:

```bash
cilium hubble enable --ui
```

Open Hubble UI in your browser:

```bash
cilium hubble ui
```
