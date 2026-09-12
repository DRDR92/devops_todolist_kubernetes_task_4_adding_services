# Django ToDoList Kubernetes Deployment Instructions

## Prerequisites
- Kubernetes cluster running (minikube, Docker Desktop with K8s)
- kubectl configured
- Docker image ikulyk404/todoapp:3.0.0 available

## Deployment Steps

### Deploy Pods
```bash
kubectl apply -f todoapp-pod-1.yaml
kubectl apply -f todoapp-pod-2.yaml
kubectl get pods -l app=todolist
```

### Deploy Services
```bash
kubectl apply -f todoapp-service-clusterip.yaml
kubectl apply -f todoapp-service-nodeport.yaml
kubectl get svc
```

## Testing Methods

### Method 1: ClusterIP with busybox

Run busybox pod:
```bash
kubectl run -it --rm debug --image=busybox --restart=Never -- sh
```

Inside busybox:
```bash
nslookup todoapp-service-clusterip
nslookup todoapp-service-clusterip.default.svc.cluster.local
curl http://todoapp-service-clusterip/
exit
```

### Method 2: kubectl port-forward

Access service from local machine:
```bash
kubectl port-forward svc/todoapp-service-clusterip 8000:80
```

Open: http://localhost:8000

Port-forward to specific pods:
```bash
kubectl port-forward pod/todoapp-pod-1 8001:8080
kubectl port-forward pod/todoapp-pod-2 8002:8080
```

### Method 3: NodePort Service (port 30080)

For minikube:
```bash
minikube ip
# Access: http://<minikube-ip>:30080
```

For Docker Desktop:
```bash
# Access: http://localhost:30080
```

## Monitoring

```bash
kubectl get pods -l app=todolist -o wide
kubectl logs todoapp-pod-1
kubectl logs todoapp-pod-2
kubectl describe svc todoapp-service-nodeport
kubectl get endpoints todoapp-service-clusterip
```

## Cleanup
```bash
kubectl delete pod todoapp-pod-1 todoapp-pod-2
kubectl delete svc todoapp-service-clusterip todoapp-service-nodeport
```