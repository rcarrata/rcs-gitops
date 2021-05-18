## Installation

### Installation of Openshift GitOps


* Install the GitOps Operator

```
oc apply -k bootstrap/operator-install/
```

* Get the Route

```
oc get route openshift-gitops-server -n openshift-gitops -o jsonpath='{.spec.host}'
```

* Fetch the admin password

```
oc get secret openshift-gitops-cluster -n openshift-gitops -o jsonpath='{.data.admin\.password}' | base64 -d
```

### Integration with Dex with Openshift GitOps

* Update the Subscription to use the Dex Integration
```
oc patch subscription openshift-gitops-operator -n openshift-operators --type=merge -p='{"spec":{"config":{"env":[{"name":"DISABLE_DEX","Value":"false"}]}}}'
```

This will get the argocd-cluster-dex-server instance running.

* To enable login with openshift
```
oc patch argocd openshift-gitops -n openshift-gitops --type=merge -p='{"spec":{"dex":{"openShiftOAuth":true},"rbac":{"defaultPolicy":"role:readonly","policy":"g, system:cluster-admins, role:admin","scopes":"[groups]"}}}'
```

The patch will addd the RBAC of example using the ArgoCD RBAC Configurations

* Enable the ArgoCD Route to accept TLS Edge terminations
```
oc patch argocd openshift-gitops -n openshift-gitops --type=merge -p='{"spec":{"server":{"insecure":true,"route":{"enabled":true,"tls":{"insecureEdgeTerminationPolicy":"Redirect","termination":"edge"}}}}}'
```
