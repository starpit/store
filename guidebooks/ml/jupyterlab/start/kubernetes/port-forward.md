---
imports:
    - util/shuf
    - kubernetes/kubectl
    - kubernetes/context
    - kubernetes/choose/ns
    - ./jupyter-server
---

# Port-forward to the Jupyter-lab server

```shell
export JUPYTERLAB_PORT=${JUPYTERLAB_PORT-$(shuf -i 8266-9999 -n1)}
```

Port-forwarding needs to be initiated after the JupyterLab server has started to avoid a failed connection. We wait for the server to start before calling the forwarding command.

```shell.async
date > /tmp/port-forward-jupyterlab

export RAY_HEAD_POD=$(kubectl get pod -l ${KUBE_POD_RAY_HEAD_LABEL_SELECTOR} --no-headers | awk '{print $1}')

num_lines=1

while [ $num_lines = 1 ]; do
    server_list=$(oc exec $RAY_HEAD_POD -- jupyter server list)
    num_lines=$(oc exec $RAY_HEAD_POD -- python3 -c "response = \"\"\"${server_list}\"\"\"; print(len(response.split('\n')))")
    echo "Waiting for server to start..." >> /tmp/port-forward-jupyterlab
    sleep 1
done

kubectl ${KUBE_CONTEXT_ARG} ${KUBE_NS_ARG} port-forward service/${RAY_KUBE_CLUSTER_NAME}-ray-head ${JUPYTERLAB_PORT}:8888 >> /tmp/port-forward-jupyterlab
```

```shell
while true; do if [ -f /tmp/port-forward-jupyterlab ] && [ "$(cat /tmp/port-forward-jupyterlab | grep -q 'Forwarding from' && echo 1 || echo 0)" = "1" ]; then break; sleep 1; fi; done
```
