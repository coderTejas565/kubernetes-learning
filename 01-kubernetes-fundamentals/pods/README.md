# Kubernetes Pods

## 1. First Pod Deployment

Created an Nginx Pod using a Kubernetes manifest.

### Commands Practiced

```bash
kubectl apply -f nginx-pod.yaml
kubectl get pods
kubectl describe pod nginx-pod
kubectl get pods -o wide
kubectl logs nginx-pod
kubectl port-forward pod/nginx-pod 8080:80
kubectl delete pod nginx-pod
```

### Observations

- Scheduler assigned the Pod to a node.
- Kubelet managed the Pod lifecycle.
- Containerd pulled the image and started the container.
- Kubernetes assigned a Pod IP.
- Pod entered Running and Ready states.
- `containerPort` alone does not expose an application externally.
- Port forwarding provided temporary local access.
- Deleting a standalone Pod does not cause Kubernetes to recreate it.

### Pod Events

`Scheduled → Pulling → Pulled → Created → Started`

---

## 2. Container Restart Policies

Experimented with all three Kubernetes restart policies using BusyBox containers.

| Policy | Exit code 0 | Non-zero exit |
|---|---|---|
| Always | Restart | Restart |
| OnFailure | No restart | Restart |
| Never | No restart | No restart |

### Key Observations

- `Always` restarts containers even after successful termination.
- `OnFailure` restarts containers only after unsuccessful termination.
- `Never` does not restart terminated containers.
- Kubelet manages container restarts.
- Container restart does not necessarily create a new Pod.
- Restart policies do not automatically recreate deleted standalone Pods.

---

## 3. Kubernetes Health Probes

Implemented startup, liveness and readiness probes.

### Probe Comparison

| Probe | Purpose | Failure Behavior |
|---|---|---|
| Startup | Checks whether application initialization is complete | Container restarts after failure threshold |
| Liveness | Checks whether application is healthy | Container restarts after failure threshold |
| Readiness | Checks whether application can receive traffic | Pod becomes NotReady |

### Probe Mechanisms

- `httpGet`: Performs an HTTP health check.
- `tcpSocket`: Checks whether a TCP connection can be established.
- `exec`: Executes a command inside the container.

### Practical Experiments

**Experiment 1: Both liveness and readiness succeed**

- Used Nginx with HTTP probes.
- Both probes returned successful responses.
- Pod became Ready.

**Experiment 2: Readiness probe failure**

- Changed readiness endpoint to `/not-found`.
- HTTP 404 caused readiness failure.
- Container continued running.
- Restart count did not increase.
- Pod became NotReady.

**Experiment 3: Liveness probe failure**

- Changed liveness endpoint to `/not-found`.
- Repeated HTTP 404 responses triggered liveness failure.
- Kubelet restarted the container.
- Restart count increased.

**Experiment 4: Startup probe**

- Created a simulated application with a 30-second initialization period.
- Used an `exec` probe to check for `/tmp/started`.
- Liveness and readiness checks were suppressed until startup succeeded.
- Application became Ready after initialization.
- No unnecessary container restarts occurred.

### Key Observations

- A Running container is not necessarily Ready.
- Startup probes protect slow-starting applications.
- Readiness failure does not restart containers.
- Liveness failure triggers container restarts.
- Startup, liveness and readiness probes solve different problems.

---

## 4. Pod Troubleshooting

Practiced diagnosing common container failures.

### ImagePullBackOff

**Cause:** Kubernetes cannot pull the requested container image.

Common reasons:
- Incorrect image name.
- Non-existent image tag.
- Private registry authentication failure.
- Registry/network issues.

Observation:
- Image pulling failed.
- Kubernetes reported `ErrImagePull`.
- Subsequent image pull attempts were delayed using backoff.
- Application container did not start.

### CrashLoopBackOff

**Cause:** Container repeatedly starts and terminates.

Common reasons:
- Application crashes.
- Incorrect startup command.
- Missing environment variables.
- Invalid application configuration.
- Dependency initialization failures.

Observation:
- Container started successfully.
- Application exited with a non-zero status.
- Kubelet attempted to restart the container.
- Repeated failures caused restart backoff.

### Other Useful Statuses

| Status | Meaning |
|---|---|
| Pending | Waiting for scheduling or another prerequisite |
| ContainerCreating | Container environment is being prepared |
| ImagePullBackOff | Image pull retry is delayed |
| CrashLoopBackOff | Container restart retry is delayed |
| OOMKilled | Container was terminated after exceeding its memory limit |
| Completed | Container terminated successfully |

Note: These are not all Pod phases. Some are container states or reasons displayed by kubectl.

---

## 5. Troubleshooting Commands

```bash
# Inspect Pods
kubectl get pods -o wide
kubectl get pods -w
kubectl describe pod <pod-name>
kubectl get pod <pod-name> -o yaml

# Application logs
kubectl logs <pod-name>
kubectl logs <pod-name> --previous

# Execute commands inside a container
kubectl exec -it <pod-name> -- sh

# Inspect Kubernetes events
kubectl get events --sort-by='.lastTimestamp'

# Manage Pods
kubectl apply -f <file.yaml>
kubectl delete pod <pod-name>
kubectl delete -f <file.yaml>
```

### Debugging Approach

1. Check Pod status using `kubectl get pods`.
2. Inspect detailed information using `kubectl describe`.
3. Check application logs.
4. Inspect previous container logs if a restart occurred.
5. Check Kubernetes events.
6. Enter the container using `kubectl exec` if it is running.

---

## 6. Important Concepts to Remember

- A Pod is the smallest deployable unit in Kubernetes.
- Containers inside the same Pod share networking and can communicate through localhost.
- A Pod has its own IP address.
- `containerPort` does not expose a Pod externally.
- Standalone Pods are not self-healing at the Pod level.
- Kubelet manages container lifecycle and restarts.
- Workload controllers such as Deployments maintain the desired Pod count.
- Readiness determines whether a Pod is eligible for normal Service traffic.
- Liveness determines whether a container should be restarted.
- Startup protects applications during initialization.
- Logs explain application behavior; events explain Kubernetes behavior.

## Official Documentation

- https://kubernetes.io/docs/concepts/workloads/pods/
- https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/
- https://kubernetes.io/docs/concepts/configuration/liveness-readiness-startup-probes/
- https://kubernetes.io/docs/tasks/debug/debug-application/debug-pods/