# Maximo One-Click Installaton

## Using Techzone Gym to Install of MAS 9.0.x 

### Using Ansible Oneclick Method 

This document and solution were prepared using a Techzone Gym.  These notes are from my first attempt I received this document in MS Word .docx format from TK (Taeksu Kim - taeksu@ibm.com)

## Original Contributors 

- David Boggs
- Hannah Carr
- Taeksu Kim (TK)

## Definitions and Accronyms

`CBM`
: Conditioned-based Maintenance (on roadmap not available today) - a strategy that monitors the actual condition of equipment and performs maintenance only when necessary, based on real-time data and performance indicators, rather than a fixed schedule. It uses various techniques like sensors, visual inspections, and tests to detect deviations from normal operating conditions and schedule maintenance before potential failures. 

`CP4D`
: IBM CloudPak for Data

`EAM`
: Enterprise Asset Management

`FMEA`
: Failure Mode and Effects Analysis - a systematic approach to identify and assess potential failures in a system, process, or product, and their potential effects. It's used to proactively prevent or minimize failures by analyzing potential problems before they occur. 

`MAS`
: Maximo Application Suite

`MBO`
: Maximo Business Object

`OCP`
: OpenShift Container Platform

## Introduction 

MAS is a complex product both technically and functionality and it takes time to learn.  Not only do you need to understand OCP but you also need to understand MAS dependencies, such as MongoDB, Kafka, CP4D, Certificates, Licensing and others. These dependences are installed as Operators in OCP. Installation order was extremely important in the manual process and very error prone. 

Today we have the one-click install Ansible scripts, which while not exactly one click, is a 1000% improvement over the manual install method. This lab is an attempt to make someone with little OpenShift experience a bit more comfortable with the installation of MAS and Manage.

**NOTE**: I do not know everyone who contributed to the MAS Ansible Devops Playbooks and thank them for the hard work they put into this. My goal here is to make things clearer for a semi-technical person to install MAS and an Manage instance that could be used for self-education and customer demos.

## Installation Methods 

There are a number of ways to install MAS in this lab you will be using the MAS One Click method. Please review the MAS One Click process at [OneClick Install for MAS Core](https://ibm-mas.github.io/ansible-devops/playbooks/oneclick-core/)

## Lab Overview 

In this lab you will install MAS Core, MAS Manage with demo data. The install will take approximately 12 hours to complete, most of which is hopefully just wait time. The one click install uses the DB2 Database Operator to install the necessary data schemas for Manage, it does not install CP4D as there is no dependency on CP4D for Manage. The cluster can be used as a hosting environment, a basic playground, demo, or knowledge transfer. While the lab focus is on Manage, after completion you should be able to install other MAS applications.

## Pre-requisites for One Click Lab 

You will need the following to complete this lab:

1. Large OpenShift Cluster.
    - 3 control plane nodes
    - 5 worker nodes
    - 3 infra nodes (OpenShift Data Foundation)

    **Note:** All nodes have 200GB OS disk, 65566GB RAM and 32vCPU.  The IPI Install will enable platform integration so ODF will use the default 'thin' storage class to build the storage cluster.

2. This document describes the installation and configuration of the tools on the RHEL 8 Linux bastion node that is included with the OpenShift Gym.

3. IBM Rational Key Store Account (IBM Employees Only), Business Partners must have purchased the "Value Package" or renew if they have purchased one in the past. If the BP has purchased and/or renewed they will have access to the License Key Store. The process of license generation is the same. [IBM employees follow guidance here to create account](https://w3.ibm.com/w3publisher/ibm-license-key-mgmt/license-key-management/internal-use-license-keys)

4. Request Access First, then open the License Key Center  
    ![LKC1](../images/lkc1.png){width=80%}

5. Log in to License Key Center  
    ![LKC2](../images/lkc2.png){width=80%}

6. In the 'Get keys' screen scroll down to the bottom for IBM AppPoint Suites.  
    ![LCK3](../images/lkc3.png){width=80%}
    ![LKC4](../images/lkc4.png){width=80%}

7. Select 'IBM Maximo Application Suite AppPoint Lic'
    ![LKC5](../images/lkc5.png){width=80%}

8. Request License Key.  Use the example below for generating the license file.

    | Field             | Content                                                      |
    |-------------------|--------------------------------------------------------------|
    | 1. Number of Keys | 100 (How many AppPoints to assign to the license file)       |
    | 2. Host Type      | Set to Ethernet Addresss                                     |
    | 3. Host ID        | 0050569d700c (MAC Address of bastion)                        |
    | 4. Hostname       | ocpinstall.gym.lan (set to the hostname of the OCP Instance) |
    | 5. Port           | Set to 27000                                                 |

    ![LKC6](../images/lkc6.png){width=80%}

9. Click the 'Generate' Button and Download the AppPoint License.  This license file is also referred to as the SLS License file.

## TechZone Environment

### This example uses a TechZone OpenShift Gym.

The first step in this process is obtaining an OCP Cluster. The amount of time these clusters are available after provisioning is determine by how the environments are requested. If you are setting one of these up for a POC and have a sales opportunity number, extending the timeframe should not be an issue. Note it will take anywhere between 1 to 3 hours before your OCP cluster will be available. Generally, a cluster for enablement is 3 days in length, which can also be extended for a limited time. You must be an IBMer or Business Partner to access the below link in Techzone.

1. Provision and OpenShift gym. [Gym Reservation](https://techzone.ibm.com/my/reservations/create/6421e3b3198c4332a346e403)

2. Use the documented procedures to complete the following tasks in the OpenShift Gym.

    1. Provision and OpenShift Cluster uisng the documente IPI installation
    2. Install OpenShift Data Foundation (ODF)
    4. Create the Storage Cluster for ODF
    5. Enable the local image repository on the OpenShift Cluster


### Other TechZone Environments and Links

- [Access the following Link](https://techzone.ibm.com/collection/tech-zone-certified-base-images/journey-base-open-shift) and select the appropriate TechZone Certified Base Image.  

**Note:** Reservation Name: OpenShift Cluster (OCP-V) - Poughkeepsie On-Premise.  This environment is sized appropiately for MAS Core and Manage install.

- [Techzone Certified Base Images - Maximo Application Suite 9.0.x Manage](https://techzone.ibm.com/collection/tech-zone-certified-base-images/journey-pre-installed-software)

- [Maximo Sizing Calculator](https://www.ibm.com/docs/en/masv-and-l/continuous-delivery?topic=premises-requirements-capacity-planning)
