---
Title: OpenShift on IBM Cloud (IPI)
hide:
    - toc
---

#  OpenShift on IBM Cloud (IPI)

This exercise helps prepare you for installing OpenShift on hyperscalers by deploying into IBM Cloud Infrastructure.

Most hyperscalers offer managed OpenShift. IBM offers Red Hat OpenShift Kubernetes Service (ROKS), AWS offers Red Hat OpenShift Service on AWS (ROSA) and Microsoft offers Azure Red Hat OpenShift (ARO).  When it comes to installing OpenShift clusters, what these managed OpenShift offerings have in common is that installation is not performed using the openshift-install command. Instead, hyperscalers provide their specific command-line interfaces and integrate cluster installation into their web consoles.

In this scenario, we have decided not to include managed OpenShift instead the experience is to install self-managed OpenShift.

Common concepts / services in the context of hyperscalers:

- **Region:** A geographic location where a cloud provider offers its services, with one or more data centers located within it.
- **Virtual Private Cloud:** A logical isolation of resources within the cloud.
- **Availability Zone:** Isolated or separated data center(s) located within specific regions.
- **Security Group:** A virtual firewall that controls incoming and outgoing traffic to and from instances within the VPC.
- **Application Load Balancer:** A type of load balancer designed to handle HTTP and HTTPS traffic.
- **Application Gateway:** A device or system that connects two different networks or systems together, allowing data to be exchanged between them.
- **Object Storage:** Object storage is a type of cloud storage that stores data as objects, providing a cost-effective, scalable, and highly available solution.


## Outcomes

- The ability to install a self-managed OpenShift cluster on IBM Cloud with confidence using IPI.
Outcomes
- Install OpenShift on IBM Cloud using Installer Provisioned Infrastructure (IPI).
- Backup the etcd database to IBM Cloud object storage.


## Scenario

You need to create a five node OpenShift cluster with 3 control plane nodes and 2 compute nodes with the specifications outlined below.

| Node type	     | vCPU | Memory in GiB | Disk size in GB |
| :------------- | :--: | :-----------: | :-------------: |
| Control Plane  | 4 | 16 | 100 |
| Compute        | 8 | 32 | 100 |

This installation requires the Cloud Credential Operator.

!!! Information "Credential Operator"
    Would you like to learn more about the Credential Operator?  Check out the OpenShift Documentation for the [Cloud Credential Operator](https://docs.openshift.com/container-platform/4.15/authentication/managing_cloud_provider_credentials/about-cloud-credential-operator.html){target="_blank"}

## Provision the lab environment

1. Navigate to the [OpenShift Installation Cohort](https://techzone.ibm.com/collection/openshift-installation-cohort){target="_blank"} collection in TechZone.

2. Reserve the "Virtual Server on VPC" environment.