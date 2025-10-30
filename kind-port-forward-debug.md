# Debug Diary: Kind Cluster Port-Forward Not Working After EC2 Restart

## Problem Summary
After restarting the EC2 instance, the deployed application running on a **Kind (Kubernetes in Docker)** cluster became inaccessible. Even after re-running the previous `kubectl port-forward` command, the application could not be accessed using the public IP.

## Root Cause
`kubectl port-forward` binds to **127.0.0.1 (localhost)** by default.  
So although the port-forward was active, it was only accessible *inside the EC2 instance*, not externally.

Additionally:
- Docker containers (Kind cluster nodes) restart after reboot.
- Service was exposed via port-forward, not NodePort or Ingress.

## Steps Taken to Debug

### 1. Checked Kind Cluster Containers
```bash
docker ps
```
Confirmed cluster nodes running (`control-plane`, `worker`, etc).

### 2. Verified App Pod Status
```bash
kubectl get pods -n notes-app -o wide
```
Pod was running correctly.

### 3. Port-Forward Was Re-Established
```bash
kubectl port-forward deployment/notes-app-deployment 8000:80 -n notes-app
```
But still not accessible from the browser.

### 4. Discovered Key Issue
Port-forward binds to **localhost only**, so external access fails.

### 5. Fixed Port-Forward by Binding to All Interfaces
```bash
kubectl port-forward deployment/notes-app-deployment 8000:80 -n notes-app --address 0.0.0.0
```

### 6. Allowed Traffic (If UFW Was Active)
```bash
sudo ufw allow 8000
```

### 7. Ensured Security Group Allows the Port
AWS Console → EC2 → Security Group → Inbound → Add:

| Type | Port | Source |
|------|------|---------|
| Custom TCP | 8000 | 0.0.0.0/0 |

## Final Working Access URL
```
http://<EC2-PUBLIC-IP>:8000
```

## Key Takeaway
When using `kubectl port-forward` on a remote server (EC2, VPS, etc.), **always bind to 0.0.0.0**:

```bash
kubectl port-forward <resource> <local-port>:<pod-port> --address 0.0.0.0
```

This makes the forwarded port reachable externally.

---

Logged as part of **Debug Diaries**.