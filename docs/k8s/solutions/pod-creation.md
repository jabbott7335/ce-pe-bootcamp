---
title: Kubernetes Lab 1 - Pod Creation Solution
hide:
    - toc
---

# Kubernetes Lab 1 - Pod Creation Solution

## Solution

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  namespace: web
spec:
  containers:
  - name: nginx
    image: nginx
    command: ["nginx"]
    args: ["-g", "daemon off;", "-q"]
    ports:
    - containerPort: 80
```