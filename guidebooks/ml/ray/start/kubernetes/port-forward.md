---
imports:
    - util/shuf
    - kubernetes/context
    - kubernetes/choose/ns
---

# Start up a Kubernetes port-forwards to the cluster

In order to facilitate communication from one's personal computer to
the Kubernetes services, we set up a number of Kubernetes
[port-forwards](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/).

## Port-forward to the Ray API

To facilitate communication to the Ray API.

```shell.async
kubectl --context ${KUBE_CONTEXT} -n ${KUBE_NS} port-forward service/${RAY_KUBE_CLUSTER_NAME-mycluster}-ray-head ${RAY_KUBE_PORT-8266}:8265 > /tmp/port-forward-ray-${RAY_KUBE_CLUSTER_NAME-mycluster}
```

### Wait for the ray port forwarder to become active

```shell
while true; do if [ -f /tmp/port-forward-ray-${RAY_KUBE_CLUSTER_NAME-mycluster} ] && [ "$(cat /tmp/port-forward-ray-${RAY_KUBE_CLUSTER_NAME-mycluster} | grep -q 'Forwarding from' && echo 1 || echo 0)" = "1" ]; then break; sleep 1; fi; done
```

## Port-forward to the MLFlow server

```shell
export MLFLOW_PORT=${MLFLOW_PORT-$(shuf -i 8266-9999 -n1)}
```

```shell.async
kubectl --context ${KUBE_CONTEXT} -n ${KUBE_NS} port-forward service/monitoring-service ${MLFLOW_PORT}:9080 > /tmp/port-forward-mlflow-${RAY_KUBE_CLUSTER_NAME-mycluster}
```

### Wait for the MLFlow port forwarder to become active

```shell
while true; do if [ -f /tmp/port-forward-mlflow-${RAY_KUBE_CLUSTER_NAME-mycluster} ] && [ "$(cat /tmp/port-forward-mlflow-${RAY_KUBE_CLUSTER_NAME-mycluster} | grep -q 'Forwarding from' && echo 1 || echo 0)" = "1" ]; then break; sleep 1; fi; done
```

## Wait, if requested

In some debugging cases it might be useful to wait for a bit. TODO:
this really should be wait, if we are executing this as a top-level
guidebook. That is, if the user only wants to set up the port
forwarding, then we shouldn't exit. This is the hack for now: set
`WAIT=true` when running this guidebook in this scenarios.

```shell
if [ -n "$WAIT" ]; then sleep 100000; fi
```
