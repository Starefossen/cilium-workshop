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

### With Minikube on Colima

Install minikube, colima and docker cli.

```bash
brew install minikube colima docker-compose
```

Compose is now a Docker plugin. For Docker to find this plugin, symlink it:

```bash
mkdir -p ~/.docker/cli-plugins
ln -sfn /opt/homebrew/opt/docker-compose/bin/docker-compose ~/.docker/cli-plugins/docker-compose
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
  --cpus max \
  --memory max \
  --network-plugin=cni \
  --cni=false \
  --kubernetes-version v1.23.10
```

<!---
--nnodes 2
--docker-opt="default-ulimit=nofile=102400:102400"
-->

## Install Cilium

Verify that you have a working Kubernetes connection:

```bash
kubectl version
```

Run the following commands in order to set up Cilium:

```bash
cilium install
```

Verify that Cilium is running:

```bash
cilium status
```

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
