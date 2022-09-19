# Lab 1

Check if all Cilium components have been properly deployed with the following
command. Note that it might take a few seconds to display the results!

```
kubectl rollout status -n kube-system daemonset/cilium
```

Now let's deploy a simple empire demo application. It is made of several
microservices, each identified by Kubernetes labels:

* the Death Star: org=empire, class=deathstar
* the Imperial TIE fighter: org=empire, class=tiefighter
* the Rebel X-Wing: org=alliance, class=xwing

The deployment also includes a deathstar-service, which load-balances traffic to
all pods with label org=empire, class=deathstar.

Let's install everything via the manifest http-sw-app.yaml:

```
kubectl apply -f https://raw.githubusercontent.com/cilium/cilium/HEAD/examples/minikube/http-sw-app.yaml
```

In the output, note the newly created objects. To verify that everything is
properly deployed, run:

```
kubectl get pods,svc
```

Each pod will go through several states until it reaches Running at which point
the pod is ready. Re-launch the command until all pods are in a Running state.

Each pod will also be represented in Cilium as an Endpoint. To retrieve a list
of all endpoints managed by Cilium, the Cilium Endpoint (or cep) resource can be
used:

```
kubectl get cep --all-namespaces
```

As you can see the demo application is properly installed now. Let's launch our
first test!
