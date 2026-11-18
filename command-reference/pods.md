# Kubernetes Pod Command Reference

Commands used for inspecting, monitoring, debugging, and managing Pods.

## 1. List and Monitor Pods

| Command | Purpose |
|---|---|
| `kubectl get pods` | List Pods in the current namespace |
| `kubectl get pods -A` | List Pods across all namespaces |
| `kubectl get pods -o wide` | Show additional details, including Pod IP and node |
| `kubectl get pods -w` | Watch Pod changes continuously |
| `kubectl get pods -n <namespace>` | List Pods in a specific namespace |

## 2. Inspect Pod Details

| Command | Purpose |
|---|---|
| `kubectl describe pod <pod-name>` | Inspect Pod configuration, status, conditions and events |
| `kubectl get pod <pod-name> -o yaml` | Display the complete Pod specification and current status |
| `kubectl get pod <pod-name> -o wide` | Inspect Pod IP and assigned node |

**When to use:**
- Use `get` for a quick overview.
- Use `describe` when investigating scheduling, image-pull, container and other Pod issues.
- Use `-o yaml` when you need to inspect the actual resource definition and runtime status.

## 3. View Container Logs

| Command | Purpose |
|---|---|
| `kubectl logs <pod-name>` | View logs from a Pod's default container |
| `kubectl logs <pod-name> -f` | Follow logs continuously |
| `kubectl logs <pod-name> --previous` | View logs from the previous terminated container instance |
| `kubectl logs <pod-name> -c <container-name>` | View logs from a specific container |
| `kubectl logs <pod-name> --all-containers=true` | View logs from all containers in a Pod |

**When to use:**
- Use normal logs to inspect current application output.
- Use `--previous` when a container has restarted and you need to investigate its previous execution.
- Use `-c` when a Pod contains multiple containers.

## 4. Execute Commands Inside Containers

| Command | Purpose |
|---|---|
| `kubectl exec <pod-name> -- <command>` | Execute a command inside a container |
| `kubectl exec -it <pod-name> -- sh` | Open an interactive shell |
| `kubectl exec -it <pod-name> -c <container-name> -- sh` | Open a shell in a specific container |

**Example:**

```bash
kubectl exec -it nginx-pod -- sh
```

## 5. Create, Update and Delete Pods

| Command | Purpose |
|---|---|
| `kubectl apply -f <file.yaml>` | Create or update a Pod from a manifest |
| `kubectl delete pod <pod-name>` | Delete a specific Pod |
| `kubectl delete -f <file.yaml>` | Delete resources defined in a manifest |

**Important:** Deleting a standalone Pod does not recreate it. A Deployment or another controller is needed to maintain the desired number of Pods.

## 6. Troubleshooting Commands

| Command | Purpose |
|---|---|
| `kubectl describe pod <pod-name>` | Inspect conditions and events |
| `kubectl logs <pod-name>` | Investigate application output |
| `kubectl logs <pod-name> --previous` | Investigate the previous container execution |
| `kubectl get events --sort-by=.metadata.creationTimestamp` | Inspect recent cluster events |
| `kubectl get pod <pod-name> -o yaml` | Inspect detailed Pod state and configuration |

## 7. Useful Flags

| Flag | Purpose |
|---|---|
| `-o wide` | Display additional columns |
| `-o yaml` | Display resource as YAML |
| `-w` | Watch resource changes |
| `-n <namespace>` | Select a namespace |
| `-A` | Select all namespaces |
| `-f <file>` | Specify a manifest file |
| `-c <container-name>` | Select a container |
| `-it` | Interactive terminal |
| `--previous` | Access previous terminated container logs |

---

## Important Concepts

- A Pod can contain one or multiple containers.
- Container restarts do not necessarily mean the Pod was recreated.
- `RESTARTS` represents container restarts, not Pod replacements.
- `CrashLoopBackOff` indicates repeated container termination and restart backoff.
- `Completed` indicates successful container termination; it does not mean the Pod was deleted.
- A standalone Pod is not automatically recreated after deletion.

**Reference:** [Kubernetes — Debugging Pods](https://kubernetes.io/docs/tasks/debug/debug-application/debug-pods/)