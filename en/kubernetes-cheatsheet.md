````markdown
# Kubernetes Cheatsheet

## Cluster Information
```bash
kubectl cluster-info                    # Display cluster information
kubectl version                         # Display client and server version
kubectl get nodes                       # List all nodes
kubectl describe node <node-name>       # Complete node information
```

## Pods
```bash
kubectl get pods                        # List pods in current namespace
kubectl get pods -A                     # List all pods in all namespaces
kubectl get pods -o wide                # List pods with more details
kubectl describe pod <pod-name>         # Complete pod information
kubectl logs <pod-name>                 # View pod logs
kubectl logs <pod-name> -f              # Follow logs in real-time
kubectl logs <pod-name> -c <container>  # Log from a specific container
kubectl exec -it <pod-name> -- /bin/bash # Execute command line in pod
kubectl delete pod <pod-name>           # Delete pod
kubectl port-forward <pod-name> 8080:80 # Forward local port to pod
```

## Deployments
```bash
kubectl get deployments                 # List deployments
kubectl describe deployment <name>      # Deployment details
kubectl create deployment <name> --image=<image> # Create deployment
kubectl scale deployment <name> --replicas=3     # Scale deployment
kubectl set image deployment/<name> <container>=<image> # Update image
kubectl rollout status deployment/<name>         # Check rollout status
kubectl rollout history deployment/<name>        # View rollout history
kubectl rollout undo deployment/<name>           # Rollback to previous version
kubectl delete deployment <name>                 # Delete deployment
```

## Services
```bash
kubectl get services                    # List services
kubectl get svc                         # Short form
kubectl describe service <name>         # Service details
kubectl expose deployment <name> --port=80 --type=NodePort # Expose deployment
kubectl delete service <name>           # Delete service
```

## Namespaces
```bash
kubectl get namespaces                  # List namespaces
kubectl get ns                          # Short form
kubectl create namespace <name>         # Create namespace
kubectl delete namespace <name>         # Delete namespace
kubectl config set-context --current --namespace=<name> # Set default namespace
```

## ConfigMaps and Secrets
```bash
kubectl get configmaps                  # List configmaps
kubectl get cm                          # Short form
kubectl create configmap <name> --from-file=<file> # Create from file
kubectl create configmap <name> --from-literal=key=value # Create from value
kubectl get secrets                     # List secrets
kubectl create secret generic <name> --from-literal=password=secret # Create secret
kubectl describe secret <name>          # Secret details
```

## Context and Configuration
```bash
kubectl config view                     # View kubeconfig
kubectl config get-contexts             # List contexts
kubectl config current-context          # Display current context
kubectl config use-context <context>    # Change context
kubectl config set-context <context> --namespace=<ns> # Set namespace for context
```

## Apply and Delete Resources
```bash
kubectl apply -f <file.yaml>            # Create/update resources from file
kubectl apply -f <directory>            # Apply all YAML files in directory
kubectl delete -f <file.yaml>           # Delete resources from file
kubectl replace -f <file.yaml>          # Replace resource from file
```

## Labels and Selectors
```bash
kubectl get pods -l app=nginx           # Get pods based on label
kubectl label pods <pod-name> env=prod  # Add label to pod
kubectl label pods <pod-name> env-      # Remove label from pod
```

## Resource Management
```bash
kubectl top nodes                       # Display node resource usage
kubectl top pods                        # Display pod resource usage
kubectl get all                         # Get all resources
kubectl get all -A                      # Get all resources in all namespaces
```

## Debugging
```bash
kubectl describe <resource> <name>      # Complete resource information
kubectl logs <pod-name> --previous      # Log from previous container
kubectl get events                      # List recent events
kubectl get events --sort-by=.metadata.creationTimestamp # Sorted events
```

## YAML Output and Editing
```bash
kubectl get pod <name> -o yaml          # Get pod in YAML format
kubectl get pod <name> -o json          # Get pod in JSON format
kubectl edit pod <name>                 # Edit pod directly
kubectl explain pod                     # Pod resource documentation
kubectl explain pod.spec                # Pod spec documentation
```

## DaemonSets, StatefulSets, Jobs
```bash
kubectl get daemonsets                  # List daemonsets
kubectl get statefulsets                # List statefulsets
kubectl get jobs                        # List jobs
kubectl get cronjobs                    # List cronjobs
```

## Persistent Volumes
```bash
kubectl get pv                          # List persistent volumes
kubectl get pvc                         # List persistent volume claims
kubectl describe pv <name>              # PV details
kubectl describe pvc <name>             # PVC details
```

## Common Flags
```bash
-n, --namespace=<namespace>             # Specify namespace
-A, --all-namespaces                    # All namespaces
-o, --output=<format>                   # Output format (json, yaml, wide, name)
-w, --watch                             # Watch for changes
--dry-run=client                        # Test command without execution
-f, --filename=<file>                   # File to use
-l, --selector=<label>                  # Label selector
```

````
