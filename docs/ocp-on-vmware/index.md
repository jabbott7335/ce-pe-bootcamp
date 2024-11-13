---
Title: OpenShift on Virtualized Environments
hide:
    - toc
---

#  OpenShift of VMware Overview

The objective for this exercise is to learn how to install an OpenShift cluster in a VMware virtualized environment.

## Outcomes

- Install OpenShift on vSphere1 using Installer Provisioned Infrastructure (IPI). IPI is also known as full-stack automation.
- Configure the identity provider for htpasswd authentication.
- Install Red Hat OpenShift Data Foundation (ODF).
- Configure ODF storage for the internal image registry.

## Scenario

A modernization project involves containerizing existing applications, requiring an OpenShift cluster. Unfortunately, the client lacks the necessary human resources to build the cluster and has requested that our IBM team construct one on their behalf.

The cluster requires a total of eight nodes, comprising three control plane nodes, two compute nodes, and three infrastructure nodes, which must meet the specifications listed below.

| Node type	     | vCPU | Memory in GiB | Disk size in GB |
| :------------- | :--: | :-----------: | :-------------: |
| Control Plane  | 4 | 16 | 120 |
| Compute        | 8 | 16 | 120 |
| Infrastructure | 16 | 64 | 120 |

In addition, we require a software-defined storage (SDS) solution that supports both read-write many (RWX) and read-write once (RWO) storage classes. Specifically, we have chosen Red Hat OpenShift Data Foundation to meet this need.

As a reference point, please recall that DO322 featured an illustration of the IPI installation process on vSphere, as shown below.

![IPI installation process on vSphere](./images/ipi-installation-vSphere.svg){target="_blank"}

## Provision the lab environment

1. Navigate to the [OpenShift Installation Cohort](https://techzone.ibm.com/collection/openshift-installation-cohort){target="_blank"} collection in TechZone.

2. On the left hand side click **Environments** and then click the **Reserve** button on the **OCP Gymnasium** tile.
    
    ![reserve-gym](./images/reserve-gym.png){target="_blank"}

3. Select **Reserve now**.

4. Fill out the reservation form adding your information where relevant (use the screenshot below for guidance).
    
    ![gym-reservation](./images/gym-reservation.png){target="_blank"}

5. Click **Submit**. Provisioning approximately takes 30 minutes.

6. Once your environment has been provisioned you will receive an e-mail from noreply@techzone.ibm.com with your reservation details.  Open your reservation details by clicking the URL under Reservation ID.

    1. If you want to use Guacamole click the **Open your IBM Cloud environment** button.  Expand the **ALL CONNECTIONS** section and test to make sure you can open the Remote Desktop and SSH sessions. If both are working your environment has been provisioned and is ready for install.
        ![gym-guacamole](./images/gym-guacamole.png){target="_blank"}

    2. If you want to use WireGuard click the **Download WireGuard VPN** config button and use that configuration file to start the VPN tunnel. The bastion's IP address is `192.168.252.2`, the username is `admin` and the password is at the top of your reservation.

!!! Information "DNS and WireGuard on MacOS"
    The DNS server (192.168.253.1) configured in the WireGuard client might not be queried. The Cisco Secure Client enables the DNS Proxy and Transparent Proxy by default. Disabling the proxies is a work around, when you disable them they enable themselves automatically. It might take up to 10 tries to get them in the desired state, disabled.
