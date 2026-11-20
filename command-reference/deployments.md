# Inspect
kubectl get deployments
kubectl describe deployment <name>
kubectl get replicasets
kubectl rollout history deployment/<name>

# Scaling
kubectl scale deployment/<name> --replicas=5

# Updates
kubectl set image deployment/<name> <container>=<image>
kubectl rollout status deployment/<name>

# Rollback
kubectl rollout undo deployment/<name>
kubectl rollout history deployment/<name> --revision=2

# Pause and resume
kubectl rollout pause deployment/<name>
kubectl rollout resume deployment/<name>