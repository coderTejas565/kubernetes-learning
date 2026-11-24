# kubectl Command Reference

## 1. Cluster Information

| Command | Purpose |
|---|---|
| `kubectl cluster-info` | Display cluster control-plane information |
| `kubectl get nodes` | List cluster nodes |
| `kubectl get nodes -o wide` | Show additional node details |
| `kubectl describe node <node-name>` | Inspect node configuration, conditions, capacity and events |
| `kubectl config get-contexts` | List available Kubernetes contexts |
| `kubectl config current-context` | Display the active context |

## 2. Pod Inspection

| Command | Purpose |
|---|---|
| `kubectl get pods` | List Pods in the current namespace |
| `kubectl get pods -A` | List Pods across all namespaces |
| `kubectl get pods -n kube-system` | List Pods in kube-system |
| `kubectl get pods -o wide` | Show additional Pod details, including node and IP |
| `kubectl get pods --show-labels` | Display Pod labels |
| `kubectl describe pod <pod-name>` | Inspect Pod configuration, status and events |
| `kubectl get pod <pod-name> -o yaml` | Display the complete Pod resource definition |

## 3. Resource Management

| Command | Purpose |
|---|---|
| `kubectl apply -f <file.yaml>` | Create or update resources from a manifest |
| `kubectl delete -f <file.yaml>` | Delete resources defined in a manifest |
| `kubectl delete pod <pod-name>` | Delete a specific Pod |
| `kubectl get all` | Display common resource types in the current namespace |

## 4. Logs and Application Access

| Command | Purpose |
|---|---|
| `kubectl logs <pod-name>` | View container logs |
| `kubectl logs <pod-name> -f` | Follow container logs |
| `kubectl logs <pod-name> --previous` | View logs from the previous container instance |
| `kubectl exec -it <pod-name> -- sh` | Open an interactive shell inside a container |
| `kubectl exec <pod-name> -- <command>` | Execute a command inside a container |
| `kubectl port-forward pod/<pod-name> 8080:80` | Forward local port 8080 to Pod port 80 |

## 5. Services and Networking

### Service inspection

| Command | Purpose |
|---|---|
| `kubectl get services` | List Services in the current namespace |
| `kubectl get svc -A` | List Services across all namespaces |
| `kubectl describe service <service-name>` | Inspect Service type, selector, ports and endpoints |
| `kubectl get service <service-name> -o yaml` | Display the complete Service definition |
| `kubectl get endpointslices` | List EndpointSlices |
| `kubectl get endpointslices -l kubernetes.io/service-name=<service-name> -o wide` | Inspect EndpointSlices belonging to a Service |
| `kubectl get pods -l app=nginx -o wide` | List Pods matching a label selector |

### Service connectivity and DNS

| Command | Purpose |
|---|---|
| `kubectl run network-test --image=busybox:1.36 --restart=Never --command -- sleep 3600` | Create a temporary Pod for network testing |
| `kubectl exec network-test -- nslookup <service-name>` | Resolve a Service using cluster DNS |
| `kubectl exec network-test -- nslookup <service-name>.<namespace>.svc.cluster.local` | Resolve a Service using its full DNS name |
| `kubectl exec network-test -- wget -qO- http://<service-name>` | Send an HTTP request to a Service |
| `kubectl exec network-test -- wget -qO- http://<service-ip>` | Send an HTTP request directly to a ClusterIP |
| `kubectl exec network-test -- wget -qO- http://<node-ip>:<node-port>` | Test NodePort connectivity from inside the cluster |
| `kubectl delete pod network-test` | Remove the network-testing Pod |

### Service types

| Type | Purpose |
|---|---|
| `ClusterIP` | Exposes a Service internally within the cluster |
| `NodePort` | Exposes a Service through a port on each node |
| `LoadBalancer` | Requests an external load balancer through an appropriate provider integration |

**Port terminology:**

- `port`: Port exposed by the Service.
- `targetPort`: Port where the application listens inside the Pod.
- `nodePort`: Port exposed on Kubernetes nodes.

## 6. kube-proxy and Networking Inspection

These commands were used with the local `kind` cluster.

### Inspect kube-system components

| Command | Purpose |
|---|---|
| `kubectl get pods -n kube-system` | Inspect Kubernetes system Pods |
| `kubectl get daemonsets -n kube-system` | Inspect DaemonSets such as kube-proxy and kindnet |
| `kubectl get configmap kube-proxy -n kube-system -o yaml` | Inspect kube-proxy configuration |

### Inspect iptables rules inside the kind node

| Command | Purpose |
|---|---|
| `docker exec learning-cluster-control-plane iptables --version` | Check iptables version and backend |
| `docker exec learning-cluster-control-plane iptables-save -t nat` | Display NAT table rules |
| `docker exec learning-cluster-control-plane iptables-save -t nat \| grep 'default/nginx-service'` | Inspect rules belonging to nginx-service |
| `docker exec learning-cluster-control-plane iptables-save -t nat \| grep -E '30080\|nginx-nodeport\|KUBE-NODEPORTS'` | Inspect NodePort-related rules |
| `docker exec learning-cluster-control-plane iptables -t nat -S <chain-name>` | Display rules belonging to a specific NAT chain |
| `docker exec learning-cluster-control-plane which conntrack` | Check whether the conntrack utility is installed |

**Important networking concepts:**

- `KUBE-SERVICES`: Matches Service traffic.
- `KUBE-NODEPORTS`: Matches NodePort traffic.
- `KUBE-SVC-*`: Selects a Service backend.
- `KUBE-SEP-*`: Handles an individual endpoint.
- `DNAT`: Rewrites the destination IP and/or port.
- `conntrack`: Tracks connections and their NAT state.

## 7. Cluster and Runtime Inspection

| Command | Purpose |
|---|---|
| `docker ps` | List running Docker containers |
| `docker exec -it <container-name> sh` | Open a shell inside a container |
| `crictl ps` | List containers managed through the CRI runtime |
| `crictl pods` | List runtime Pod sandboxes |

## 8. Useful Flags

| Flag | Purpose |
|---|---|
| `-n <namespace>` | Select a namespace |
| `-A` | Select all namespaces |
| `-o wide` | Display additional columns |
| `-o yaml` | Output resource in YAML |
| `-f <file>` | Specify a manifest file |
| `-w` | Watch for resource changes |
| `--all-namespaces` | Alternative to `-A` |
| `-l <selector>` | Filter resources using labels |

---

## Notes and Cautions

1. Replace placeholders such as `<pod-name>`, `<service-name>` and `<file.yaml>` with actual values. Do not include the angle brackets.
2. `kubectl get endpoints` is deprecated in newer Kubernetes versions. Prefer EndpointSlices.
3. A `LoadBalancer` Service can remain in `EXTERNAL-IP: <pending>` in a local kind cluster without a load-balancer implementation.
4. A kind NodePort is not automatically accessible through Windows `localhost:<nodePort>`. The kind node runs inside a Docker container, and host port mappings must be configured for host access.
5. A temporary `network-test` Pod created with `sleep 3600` exits after one hour. Recreate it if it has completed.
6. `iptables-save` and `conntrack` commands inspect Linux networking inside the kind node container, not directly on the Windows host.

**Command reference principle:** Keep reusable commands here. Keep explanations, architecture diagrams, experiments and troubleshooting findings in their respective learning notes.