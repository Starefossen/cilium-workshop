# Tetragon

# Intro 

## Security Observability

Security Observability is a new paradigm that utilizes eBPF, a Linux kernel technology, to allow Security and DevOps teams, SREs, Cloud Engineers, and Solution Architects to gain real-time visibility into Kubernetes and workloads running on top of it. This helps them to secure their production environment.

## Cilium Tetragon

[Cilium Tetragon](https://github.com/cilium/tetragon) is an open source Security Observability and Runtime Enforcement tool from the makers of [Cilium](https://cilium.io/). It captures different process and network event types through a user-supplied configuration to enable Security Observability on arbitrary hook points in the kernel; then translates these events into actionable signals for a Security Team.

## There is a book

The best way to learn about Security Observability and Cilium Tetragon is to read the book ["Security Observability with eBPF"](https://isovalent.com/blog/post/2022-04-oreilly-security) by Jed Salazar and Natália Réka Ivánkó.


# Lab

## intro

In this lab we will look at the Real World Attack example from "Security Observability with eBPF" book. We will take a look at how we can detect a container escape step by step:

## Step.1

Lets install the Tetragon in the cluster:

```bash
helm repo add cilium https://helm.cilium.io
helm repo update
helm install tetragon cilium/tetragon -n kube-system --set tetragon.exportDenyList=""
```

Cilium Tetragon is running as a daemonset which implements the eBPF logic for extracting the Security Observability events as well as event filtering, aggregation and export to external event collectors.

Wait until Tetragon daemonset is successfully rolled out.

```bash
kubectl rollout status -n kube-system ds/tetragon -w
```

Next, let's install the Tetragon CLI in our environment:

### Tetragon-cli for linux

```bash
curl -sL L https://github.com/cilium/tetragon/releases/download/tetragon-cli/tetragon-linux-amd64.tar.gz | \
  tar xvzf - -C .
```

Add current folder to path

```bash
PATH=$PATH:$(pwd)
```

### Tetragon-cli for Macos

M1 Mac :

```bash
curl -sL L https://github.com/cilium/tetragon/releases/download/tetragon-cli/tetragon-darwin-arm64.tar.gz | \
  tar xvzf - -C .
```

Intel mac : 

```bash
curl -sL L https://github.com/cilium/tetragon/releases/download/tetragon-cli/tetragon-darwin-amd64.tar.gz | \
  tar xvzf - -C .
```

Add current folder to path

```bash
PATH=$PATH:$(pwd)
```

## Add a TracingPolicy

To be able to detect a container escape we will need two `TracingPolicies`. A `TracingPolicy` is a user-configurable Kubernetes CRD that allows you to trace arbitrary events in the kernel and define actions to take on match.

The first `TracingPolicy` is going to be used to monitor networking events and track network connections. In our case, we are going to observe `tcp_connect`, `tcp_close` and `tcp_sendmsg` kernel function to track when a TCP connection opens, closes and sends data to the server:

```bash
cat config/tcp-connect.yaml
```





