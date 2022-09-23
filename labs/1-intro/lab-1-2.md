# Lab 2

To simulate our connectivity tests, we will be executing simple API calls using curl.

Let's test if we can land our TIE fighter on the Death Star by running the following command:

```
kubectl exec tiefighter -- curl -s -XPOST deathstar.default.svc.cluster.local/v1/request-landing
```

The command above lets us get a shell on the tiefighter pod and run a HTTP POST request to the deathstar Service to request landing.

The command should work —as the TIE fighter and the Death Star are on the same side of the galactic wars (i.e. the bad guys).

Now test if you can land your X-wing (i.e. the good guys) with:

```
kubectl exec xwing -- curl -s -XPOST deathstar.default.svc.cluster.local/v1/request-landing
```

So far, it seems access is allowed! This is good for the rebel alliance —unfretted access to the DeathStar— but this should not be allowed, right?

There is a security policy missing!
