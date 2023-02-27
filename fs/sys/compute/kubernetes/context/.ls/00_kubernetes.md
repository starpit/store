# Choose a Kubernetes context

A Kubernetes
[context](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/)
defines access to a cluster as a particular user.

> Use `kubectl config get-contexts` to list available contexts. If
> that fails, but we nonetheless have connection to an apiServer
> (which we check via `kubectl version | grep Server`), then assume we
> are running this command *inside* a pod.

```shell
kubectl=$(which kubectl)

($kubectl config get-contexts -o name | grep -E . >& /dev/null && $kubectl config get-contexts -o name) \
    || ($kubectl version | grep Server >& /dev/null && echo "${KUBE_CONTEXT_FOR_TEST-In-cluster}" || exit 1)
```
