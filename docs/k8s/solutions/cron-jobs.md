---
Title: Lab K8s 7 - Cron Jobs Solution
hide:
    - toc
---

# Lab K8s 7 - Cron Jobs Solution

## Solution

```
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: xwing-cronjob
spec:
  schedule: "*/1 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: xwing-status
            image: ibmcase/xwing-status:1.0
            args:
            - /usr/sbin/xwing-status.sh
          restartPolicy: OnFailure
```


```
kubectl get cronjob xwing-cronjob
```
