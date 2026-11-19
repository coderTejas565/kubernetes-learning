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
| `kubectl get pods --show-labels` | Display Pod labels |

## 2. Inspect Pod Details

| Command | Purpose |
|---|---|
| `kubectl describe pod <pod-name>` | Inspect configuration, status, conditions and events |
| `kubectl get pod <pod-name> -o yaml` | Display complete Pod specification and current status |
| `kubectl get pod <pod-name> -o wide` | Inspect Pod IP and assigned node |
| `kubectl get pod <pod-name> -o json` | Display Pod details in JSON format |

**When to use:**
- `get`: Quick overview.
- `describe`: Investigate scheduling, image pulls, container states and probe failures.
- `-o yaml`: Inspect the resource definition and runtime status.

## 3. View Container Logs

| Command | Purpose |
|---|---|
| `kubectl logs <pod-name>` | View logs from the default container |
| `kubectl logs <pod-name> -f` | Follow logs continuously |
| `kubectl logs <pod-name> --previous` | View logs from the previous terminated container instance |
| `kubectl logs <pod-name> -c <container-name>` | View logs from a specific container |
| `kubectl logs <pod-name> --all-containers=true` | View logs from all containers in a Pod |
| `kubectl logs <pod-name> -n <namespace>` | View logs from a Pod in a specific namespace |

**When to use:**
- Normal logs: Inspect current application output.
- `--previous`: Investigate a container that has restarted.
- `-c`: Select a particular container in a multi-container Pod.
- `-f`: Monitor application output in real time.

## 4. Execute Commands Inside Containers

| Command | Purpose |
|---|---|
| `kubectl exec <pod-name> -- <command>` | Execute a command inside a container |
| `kubectl exec -it <pod-name> -- sh` | Open an interactive shell |
| `kubectl exec -it <pod-name> -c <container-name> -- sh` | Open a shell in a specific container |

Example:

```bash
kubectl exec -it nginx-pod -- sh
```

Useful commands inside a container:

```bash
ps
ls /
printenv
```

## 5. Create, Update and Delete Pods

| Command | Purpose |
|---|---|
| `kubectl apply -f <file.yaml>` | Create or update resources from a manifest |
| `kubectl delete pod <pod-name>` | Delete a specific Pod |
| `kubectl delete -f <file.yaml>` | Delete resources defined in a manifest |

**Important:** Deleting a standalone Pod does not recreate it. A Deployment or another workload controller is needed to maintain the desired number of Pods.

## 6. Events and Troubleshooting

| Command | Purpose |
|---|---|
| `kubectl get events` | List recent events in the current namespace |
| `kubectl get events --sort-by='.lastTimestamp'` | Sort events by last observed timestamp |
| `kubectl describe pod <pod-name>` | Inspect conditions, container states and events |
| `kubectl logs <pod-name>` | Investigate application output |
| `kubectl logs <pod-name> --previous` | Investigate the previous container execution |

### Common troubleshooting situations

| Problem | What to investigate |
|---|---|
| Pending | Scheduling events, node resources, PVC requirements |
| ImagePullBackOff | Image name, tag, registry authentication and pull events |
| CrashLoopBackOff | Current and previous logs, exit code, container state |
| Readiness probe failure | Probe configuration, endpoint and application readiness |
| Liveness probe failure | Health endpoint, timeout and restart count |
| OOMKilled | Container memory limit and application memory usage |

### Basic debugging workflow

1. Check Pod status.
2. Inspect Pod details using `describe`.
3. Check application logs.
4. Check previous container logs if restarted.
5. Inspect Kubernetes events.
6. Execute commands inside the container if it is running.

## 7. Useful Flags

| Flag | Purpose |
|---|---|
| `-o wide` | Display additional columns |
| `-o yaml` | Display resource as YAML |
| `-o json` | Display resource as JSON |
| `-w` | Watch resource changes |
| `-n <namespace>` | Select a namespace |
| `-A` | Select all namespaces |
| `-f <file>` | Specify a manifest file |
| `-c <container-name>` | Select a container |
| `-it` | Interactive terminal |
| `--previous` | Access previous terminated container logs |
| `--all-containers=true` | Include all containers |

## 8. Important Concepts

- A Pod can contain one or multiple containers.
- Containers inside a Pod share networking and can communicate through localhost.
- Container restarts do not necessarily mean the Pod was recreated.
- `RESTARTS` represents container restarts, not Pod replacements.
- `CrashLoopBackOff` indicates repeated container termination and restart backoff.
- `ImagePullBackOff` indicates an image-pull retry is delayed.
- `Completed` indicates successful container termination.
- Readiness failure does not restart a container.
- Liveness failure can trigger a container restart.
- Startup probes delay liveness and readiness checks until initialization succeeds.
- A standalone Pod is not automatically recreated after deletion.

## Official References

- [Kubernetes — Debugging Pods](https://kubernetes.io/docs/tasks/debug/debug-application/debug-pods/)
- [Kubernetes — Pod Lifecycle](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/)
- [Kubernetes — Container Probes](https://kubernetes.io/docs/concepts/configuration/liveness-readiness-startup-probes/)