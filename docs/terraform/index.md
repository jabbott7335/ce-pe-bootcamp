---
title: Intro to Terraform on vSphere
---

# Intro
Describe the vSphere Environment
Create the VM Template from Bastion

## Download Terraform

### Linux Bastion

```
# sudo yum install -y yum-utils
# sudo yum-config-manager --add-repo https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo
# sudo yum -y install terraform
# terraform version
Terraform v1.10.5
on linux_amd64
```

### MacOS

```
brew tap hashicorp/tap
brew install hashicorp/tap/terraform
```


## Create & customize source
main.tf & variables.tf

```
terraform {
  required_providers {
    vsphere = {
      source = "hashicorp/vsphere"
      version = "2.11.1"
    }
  }
}

provider "vsphere" {
  user                 = "user from vmware-ipi.yaml"
  password             = "password from vmware-ipi.yaml"
  vsphere_server       = "ocpgym-vc.techzone.ibm.local"
  allow_unverified_ssl  = true
  api_timeout          = 10
}

data "vsphere_datacenter" "datacenter" {
  name = "IBMCloud"
}

data "vsphere_datastore" "datastore" {
  name          = "gym-270001w2nn-03co1ci4-storage"
  datacenter_id = data.vsphere_datacenter.datacenter.id
}

data "vsphere_compute_cluster" "cluster" {
  name          = "ocp-gym"
  datacenter_id = data.vsphere_datacenter.datacenter.id
}

data "vsphere_network" "network" {
  name          = "gym-270001w2nn-03co1ci4-segment"
  datacenter_id = data.vsphere_datacenter.datacenter.id
}

data "vsphere_virtual_machine" "template" {
  name          = "terraform-template"
  datacenter_id = data.vsphere_datacenter.datacenter.id
}

resource "vsphere_virtual_machine" "vm" {
  name             = "foo"
  resource_pool_id = data.vsphere_compute_cluster.cluster.resource_pool_id
  datastore_id     = data.vsphere_datastore.datastore.id
  num_cpus         = 2
  memory           = 2048
  guest_id         = data.vsphere_virtual_machine.template.guest_id
  scsi_type        = data.vsphere_virtual_machine.template.scsi_type
  folder           = "/ocp-gym/gym-270001w2nn-03co1ci4"
  firmware         = "efi"
  network_interface {
    network_id   = data.vsphere_network.network.id
    adapter_type = "vmxnet3"
  }
  disk {
    label = "Hard Disk 1"
    size  = 50
  }
  clone {
    template_uuid = data.vsphere_virtual_machine.template.id
    customize {
      linux_options {
        host_name = "foo"
        domain    = "example.com"
      }
      network_interface {}
    }
  }
}

```

# Terraform Workflow

## Terraform initialize

```terraform initialize``

## Terraform plan

Review the changes that will make when applied

```terraform plan```

## Terraform apply

Apply the changes. 

```terraform apply```

**Note:** To `apply` the changes when prompted must type 'yes'.


## Terrafrom Destroy

```terraform destroy```


### Terraform Stat Files
