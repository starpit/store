# Choose a Kubernetes namespace

In Kubernetes,
[namespaces](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)
provide a mechanism for isolating groups of resources within a single
cluster. Names of resources need to be unique within a namespace, but
not across namespaces. Namespace-based scoping is applicable only for
namespaced objects (e.g. Deployments, Services, etc) and not for
cluster-wide objects (e.g. StorageClass, Nodes, PersistentVolumes,
etc).

> The bit of complexity here is intended to handle the situation where
> we are running in a restricted OpenShift environment. In some such
> cases, we do not have access to the `Namespace` APIs, but we do have
> access to the `Project` APIs. In OpenShift, normally the two are the
> same, but in a restricted environment, a Project can act as a
> Namespace, without the additional security concerns that Namespaces
> bring.

```shell
kubectl=$(which kubectl)
context="$(oneof /sys/compute/kubernetes/context)"

# allow tests to inject a desired namespace
# otherwise, use `kubectl get ns`, falling back on `oc get projects`
X=${KUBE_NS_FOR_TEST-$(kubectl get --context $context ns -o name || oc --context $context get projects -o name)}

# try to filter out system namespaces
echo "$X" \
    | sed -E 's#(namespace|project\.project\.openshift\.io)/##' \
    | grep -Ev 'calico-system|tigera-operator|openshift|kube-'
```
