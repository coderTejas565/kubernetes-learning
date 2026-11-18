# Kubernetes Pods

## First Pod Deployment

Created an Nginx Pod using a Kubernetes manifest.

### Commands Practiced

- kubectl apply -f nginx-pod.yaml
- kubectl get pods
- kubectl describe pod nginx-pod
- kubectl get pods -o wide
- kubectl logs nginx-pod
- kubectl port-forward pod/nginx-pod 8080:80

### Observations

- Scheduler assigned the Pod to a node.
- Kubelet managed the Pod lifecycle.
- Containerd pulled the image and started the container.
- Kubernetes assigned a Pod IP.
- Pod entered Running and Ready states.
- containerPort alone does not expose an application externally.
- Port forwarding provided temporary local access.

### Pod Events

Scheduled -> Pulling -> Pulled -> Created -> Started