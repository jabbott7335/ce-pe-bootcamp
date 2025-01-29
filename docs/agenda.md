---
Title: Agenda
hide:
    - toc
---

# Agenda

The following agenda is designed for a five-day bootcamp experience.

## Day 1 - Deploying OpenShift on VMware

| Task                              | Activity Type               |
| :-------------------------------- | :-------------------------- |
| Introductions                     |                             |
| [Hands-On Exercise Introduction](./ocp-on-vmware/index.md)                     |          |
| [Prepare for Installation](./ocp-on-vmware/prepare.md)                          | Hands-On |
| [Create the Install Config Asset](./ocp-on-vmware/create-install-config.md)     | Hands-On |
| [Install the Cluster](ocp-on-vmware/./install-cluster.md)                       | Hands-On |
| [Configure the Identity Provider](./ocp-on-vmware/config-identity-provider.md)  | Hands-On |
| [Install OpenShift Data Foundation](./ocp-on-vmware/install-data-foundation.md) | Hands-On |
| [Configure Internal Image Registry](./ocp-on-vmware/config-image-registry.md)   | Hands-On |

## Day 2 - Deploying Air-Gapped OpenShift on VMware

| Task                              | Activity Type               |
| :-------------------------------- | :-------------------------- |
| [Hands-On Exercise Introduction](./ocp-on-vmware-airgapped/index.md)           |          |
| [Setup the mirror host](./ocp-on-vmware-airgapped/mirror-host.md)              | Hands-On |
| [Mirror OpenShift Content](./ocp-on-vmware-airgapped/mirror.md)                | Hands-On |
| [Prepare the Installation](./ocp-on-vmware-airgapped/prepare-installation.md)  | Hands-On |
| [Disable Internet Access](./ocp-on-vmware-airgapped/disable-internet.md)       | Hands-On |
| [Create Install Config](./ocp-on-vmware-airgapped/install-config.md)           | Hands-On |
| [Install the Cluster](./ocp-on-vmware-airgapped/install-cluster.md)            | Hands-On |
| [Install ODF](./ocp-on-vmware-airgapped/install-odf.md)                        | Hands-On |

## Day 3 Morning - Deploying OpenShift on IBM Cloud

| Task                              | Activity Type               |
| :-------------------------------- | :-------------------------- |
| [Hands-On Exercise Introduction](./ocp-on-ibm-cloud/index.md)    | Presentation            |
| [Prepare for Installation](./ocp-on-ibm-cloud/prepare.md)                       | Hands-On |
| [Install the Cluster](./ocp-on-ibm-cloud/install-cluster.md)                    | Hands-On |
| [Backup etcd](./ocp-on-ibm-cloud/backup-etcd.md)                                | Hands-On |

## Day 3 Afternoon - Cloud Native

| Task                              | Activity Type               |
| :-------------------------------- | :-------------------------- |
| [Intro to Cloud Native](./cn/cloud-native.md)        | Presentation |
| [App Dev](./cn/app-dev.md)                           | Presentation |
| [Containers](./cn/containers.md)                      | Presentation |
| [Image Registries](./cn/imageregistry.md)            | Hands-On |
| [Podman](./cn/podman.md)                             | Hands-On |
| [Running a Container Locally](./cn/running-containers.md)       | Hands-On |

## Day 4 Morning - Kubernetes & OpenShift

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

## Day 4 Afternoon - Kubernetes & OpenShift (Continued)

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
    
## Day 5 Morning - Kubernetes & OpenShift (Continued)

| Task                              | Activity Type               |
| :-------------------------------- | :-------------------------- |
| [Networking](./k8s/openshift/services-networking/index.md)                 | Presentation |
| [Services](./k8s/openshift/services-networking/services.md)                | Presentation |
| [Routes](./k8s/openshift/services-networking/routes.md)                    | Presentation |
| [Creating Services](./k8s/creating-services.md)                            | Hands-On     |
| [Network Policies](./k8s/network-policies.md)                              | Hands-On     |
| [Ingresses](./k8s/openshift/services-networking/ingress.md)                | Presentation |
| [Ingress IKS](./k8s/ingress.md)                                            | Hands-On     |
| [State Persistence](./k8s/openshift/state-persistence/index.md)            | Presentation |
| [Volumes](./k8s/openshift/state-persistence/volumes.md)                    | Presentation |
| [Persistent Volumes & Claims](./k8s/openshift/state-persistence/pv-pvc.md) | Presentation |
| [Persistent Volumes](./k8s/persistent-volumes.md)                          | Hands-On     |
| [K8s Lab Solutions](./k8s/cheats.md)                                       | Reference     |

## Day 5 Afternoon - DevOps

| Task                              | Activity Type               |
| :-------------------------------- | :-------------------------- |
| [DevOps & GitOps](./devops/tekton/index.md)  | Presentation |
| [Tekton](./devops/tekton/tekton.md)          | Hands-On     |
| [ToolChain](./devops/ibm-toolchain/index.md) | Presentation |
| [Tekton on IBM Cloud](./devops/ibm-toolchain/ibm-toolchain.md) | Hands-On     |
| [Jenkins](./devops/jenkins/jenkins.md)                         | Hands-On     |
| [CD](./devops/argocd/index.md)                                 | Presentation |
| [GitOps & OCP](./devops/gitops-ocp.md)                         | Presentation |
| [ArgoCD](./devops/argocd/argocd.md)                            | Hands-On     |
| [Cloud Native Challenge (Optional)](./cn-challenge/index.md)   | Presentation |