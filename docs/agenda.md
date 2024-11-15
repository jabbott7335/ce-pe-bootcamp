---
Title: Agenda
hide:
    - toc
---

# Agenda

The following agenda is designed for a five-day bootcamp experience.

## Day 1 All Day - Deploying OpenShift on VMware

| Task                              | Activity Type               |
| :-------------------------------- | :-------------------------- |
| Introductions                     |                             |
| Hands-On Exercise Introduction    |                             |
| [Prepare for Installation](./ocp-on-vmware/prepare.md)                          | Hands-On |
| [Create the Install Config Asset](./ocp-on-vmware/create-install-config.md)     | Hands-On |
| [Install the Cluster](ocp-on-vmware/./install-cluster.md)                       | Hands-On |
| [Configure the Identity Provider](./ocp-on-vmware/config-identity-provider.md)  | Hands-On |
| [Install OpenShift Data Foundation](./ocp-on-vmware/install-data-foundation.md) | Hands-On |
| [Configure Internal Image Registry](./ocp-on-vmware/config-image-registry.md)   | Hands-On |

## Day 2 Morning - Installing OpenShift on IBM Cloud

| Task                              | Activity Type               |
| :-------------------------------- | :-------------------------- |
| Hands-On Exercise Introduction    | Presentation                |
| [Prepare for Installation](./ocp-on-ibm-cloud/prepare.md)                       | Hands-On |
| [Install the Cluster](./ocp-on-ibm-cloud/install-cluster.md)                    | Hands-On |
| [Backup etcd](./ocp-on-ibm-cloud/backup-etcd.md)                                | Hands-On |

## Day 2 Afternoon - Cloud Native

| Task                              | Activity Type               |
| :-------------------------------- | :-------------------------- |
| [Intro to Cloud Native](./cn/cloud-native.md)        | Presentation |
| [App Dev](./cn/app-dev.md)                           | Presentation |
| [Containers](./cn/containers.md                      | Presentation |
| [Image Registries](./cn/imageregistry.md)            | Hands-On |
| [Docker](./cn/docker.md)                             | Hands-On |
| [Container Registries](./cn/ibm-registries.md)       | Hands-On |

## Day 3 Morning - Kubernetes & OpenShift

| Task                              | Activity Type               |
| :-------------------------------- | :-------------------------- |
| [K8s & OpenShift Intro](./k8s/openshift/index.md)                                   | Presentation |
| [API Primitives](./k8s/openshift/core-concepts/index.md)                            | Presentation |
| [Namespaces & Projects](./k8s/openshift/core-concepts/namespaces-projects/index.md) | Presentation |
| [Pod Basics](./k8s/openshift/pods/index.md)                                         | Presentation |
| [Labels-Selectors](./k8s/openshift/pods/tagging/index.md)                           | Presentation |
| [Pod Creation](./k8s/pod-creation.md)                                               | Hands-On     |
| [Multi-Container](./k8s/openshift/pods/multi-container/index.md)                    | Presentation |
| [Multiple Containers](./k8s/multi-containers.md)                                    | Hands-On     |
| [Health & Monitoring](./k8s/openshift/pods/health-checks/index.md)                  | Presentation |
| [Probes](./k8s/probes.md)                                                           | Hands-On     |
| [Troubleshooting](./k8s/openshift/pods/troubleshooting/index.md)                    | Presentation |
| [Debugging](./k8s/debugging.md)                                                     | Hands-On     | 

## Day 3 Afternoon - Kubernetes & OpenShift (Continued)

| Task                              | Activity Type               |
| :-------------------------------- | :-------------------------- |
| [Configuration](./k8s/openshift/configuration/index.md)                        | Presentation |
| [Config Maps](./k8s/openshift/configuration/config-map/index.md)               | Presentation |
| [Secrets](./k8s/openshift/configuration/secrets/index.md)                      | Presentation |
| [Security Contexts](./k8s/openshift/configuration/security-contexts/index.md)  | Presentation |
| [Service Accounts](./k8s/openshift/configuration/service-accounts/index.md)    | Presentation |
| [Pod Configuration](./k8s/pod-config.md)                                       | Hands-On     |
| [Deployments](./k8s/openshift/deployments/index.md)                            | Presentation |
| [Rolling Updates](./k8s/openshift/deployments/updates/index.md)                | Presentation |
| [Lab K8s 6 - Rolling Updates](./k8s/rolling-updates.md)                        | Hands-On     |
| [Jobs & CronJobs](./k8s/openshift/pods/jobs/index.md)                          | Presentation |
| [Lab K8s 7 - Cron Jobs](./k8s/cron-jobs.md)                                    | Hands-On     |
    
## Day 4 Morning - Kubernetes & OpenShift (Continued)
    - Learn - Networking: k8s/openshift/services-networking/index.md
    - Learn - Services: k8s/openshift/services-networking/services.md
    - Learn - Routes: k8s/openshift/services-networking/routes.md
    - Lab K8s 8 - Creating Services: k8s/creating-services.md
    - Lab K8s 9 - Network Policies: k8s/network-policies.md
    - Learn - Ingresses: k8s/openshift/services-networking/ingress.md
    - Lab K8s 10 - Ingress IKS: k8s/ingress.md
    - Learn - State Persistence: k8s/openshift/state-persistence/index.md
    - Learn - Volumes: k8s/openshift/state-persistence/volumes.md
    - Learn - Persistent Volumes & Claims: k8s/openshift/state-persistence/pv-pvc.md
    - Lab K8s 11 - Persistent Volumes: k8s/persistent-volumes.md
    - Reference - K8s Lab Solutions: k8s/cheats.md

## Day 4 Morning - GitOps

| Task                              | Activity Type               |
| :-------------------------------- | :-------------------------- |
      - DevOps & GitOps:
    - devops/tekton/index.md
    - Learn CI: devops/tekton/index.md
    - Lab CI 1 - Tekton: devops/tekton/tekton.md
    - Learn IBM ToolChain: devops/ibm-toolchain/index.md
    
    
    Day 5
    - Lab CI 2 - Tekton on IBM Cloud: devops/ibm-toolchain/ibm-toolchain.md
    - Lab CI 3 - Jenkins: devops/jenkins/jenkins.md
    - Learn CD: devops/argocd/index.md
    - Walkthrough - GitOps & OCP: devops/gitops-ocp.md
    - Lab CD 1 - ArgoCD: devops/argocd/argocd.md
  - Cloud Native Challenge (Optional): 
    - cn-challenge/index.md