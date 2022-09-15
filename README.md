# cilium-workshop

Cilium Workshop for NDCOslo 2022

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

### With Colima

If you have not already install colima we reccomend installing it using brew:

```bash
brew install colima
```

Start colima with Kuberntes enabled:

```bash
colima start --kubernetes
```

Run the following two commands in order to get the things up and running (sourced from cilium/cilium-cli#669 and cilium/cilium#18675):

```bash
colima ssh -- sh -c "sudo mount bpffs /sys/fs/bpf -t bpf && sudo mount --make-shared /sys/fs/bpf"
colima ssh -- sh -c "sudo mount --make-shared /run/cilium/cgroupv2/"
```

## Install Cilium

Verify that you have a working Kubernetes connection:

```bash
kubectl version
```

Run the following command in order to set up Cilium:


```bash
cilium install
```
