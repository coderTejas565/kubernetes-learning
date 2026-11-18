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
| `kubectl exec -it <pod-name> -- sh` | Open an interactive shell inside a container |
| `kubectl port-forward pod/<pod-name> 8080:80` | Forward local port 8080 to Pod port 80 |

## 5. Cluster and Runtime Inspection

| Command | Purpose |
|---|---|
| `docker ps` | List running Docker containers |
| `docker exec -it <container-name> sh` | Open a shell inside a container |
| `crictl ps` | List containers managed through the CRI runtime |
| `crictl pods` | List runtime Pod sandboxes |

## 6. Useful Flags

| Flag | Purpose |
|---|---|
| `-n <namespace>` | Select a namespace |
| `-A` | Select all namespaces |
| `-o wide` | Display additional columns |
| `-o yaml` | Output resource in YAML |
| `-f <file>` | Specify a manifest file |
| `-w` | Watch for resource changes |
| `--all-namespaces` | Alternative to `-A` |

---

**Note:** Replace placeholders such as `<pod-name>` and `<file.yaml>` with actual values. Some commands require the relevant resource or container to exist.