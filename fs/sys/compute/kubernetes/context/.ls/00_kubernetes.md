# Choose a Kubernetes context

A Kubernetes
[context](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/)
defines access to a cluster as a particular user.

```shell
kubectl=$(which /usr/local/bin/kubectl/$(uname)/$(uname -m))

($kubectl config get-contexts -o name | grep -E . >& /dev/null && $kubectl config get-contexts -o name) \
    || ($kubectl version | grep Server >& /dev/null && echo "${KUBE_CONTEXT_FOR_TEST-In-cluster}" || exit 1)
```
