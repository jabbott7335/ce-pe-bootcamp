---
Title: Prepare the Installation
hide:
    - toc
---

# Prepare the Installation

To ensure a smooth installation process, it is essential that we take care of a few crucial preparations beforehand. Specifically, we need to set up our environment on the bastion host before we begin the installation.

## Verify DNS records

To ensure seamless operation, OpenShift Container Platform relies on a fully functional DNS server in the environment. Fortunately, our OCP Gymnasium environment includes a pre-configured DNS server, and we have already populated it with entries for the virtual IP addresses of both the API and Ingress components. As a good practice, please verify that name resolution is functioning correctly.

1. Open a Terminal and log in the bastion host.

2. Verify both the name and reverse lookup of the API virtual IP address.
    
    ```
    dig +short api.ocpinstall.gym.lan -x 192.168.252.3
    ```
    
    **Example Output**
    
    ```
    192.168.252.3
    api.ocpinstall.gym.lan
    ```

3. Verify the name lookup of the Ingress virtual IP address.
    
    ```
    dig +short foo.apps.ocpinstall.gym.lan
    ```

    **Example Output**
    
    ```
    192.168.252.4
    ```

## Ensure CA certificates from vCenter are trusted

To complete the installation of an OpenShift Container Platform cluster, you will need to ensure that your system trusts the root Certificate Authority (CA) of your vCenter. This is because the installation program relies on access to the vCenter's API. To achieve this, please add the vCenter's trusted root CA certificates to the bastion's trust store.

1. Set the `VCENTER_HOSTNAME` shell variable.

    ```
    VCENTER_HOSTNAME=your_vsphere_hostname
    ```
    
    Extract `your_vsphere_hostname` from the environment details (`vCenter Console URL`).

2. Download the vCenter root CA certificates.
    
    ```
    curl -kL https://${VCENTER_HOSTNAME}/certs/download.zip -o download.zip
    ```

3. Extract the certificates.
    
    ```
    unzip download.zip
    ```

4. Add the certificates to the CA trust store.
    
    ```
    sudo cp certs/lin/* /etc/pki/ca-trust/source/anchors
    ```

5. Update the CA trust store.
    
    ```
    sudo update-ca-trust extract
    ```

6. Clean up.
    
    ```
    rm -fr download.zip certs/
    ```

## Install the OpenShift Installer

The version of the OpenShift installer will dictate the initial version of the OpenShift cluster.

1. Download the OpenShift Installer.
    
    ```
    OI_VERSION=stable-4.15
    ```

    ```
    curl -Lo openshift-install-linux.tar.gz \
    https://mirror.openshift.com/pub/openshift-v4/clients/ocp/${OI_VERSION}/openshift-install-linux.tar.gz
    ```

2. Extract the installer.
    
    ```
    tar xf openshift-install-linux.tar.gz openshift-install
    ```

3. Copy it to a directory in $PATH.
    
    ```
    sudo install openshift-install /usr/local/bin
    ```

4. Verify that the installer is executable.
    
    ```
    openshift-install version
    ```
    
    **You should see output similar to the following:**
    
    ```
    openshift-install 4.15.5
    built from commit 4edfd0e16bc637dc2f593afbb28bba9799023892
    release image quay.io/openshift-release-dev/ocp-release@sha256:b17bc935bfb0fb250e738733af5d3d4ea1f27065de8e2622890782780b36320c
    release architecture amd64
    ```

5. Clean up.
    ```
    rm openshift-install-linux.tar.gz openshift-install
    ```

## Install the OpenShift CLI

1. Download the OpenShift CLI.
    ```
    OC_VERSION=stable-4.15
    ```

    ```
    curl -Lo openshift-client-linux.tar.gz \
    https://mirror.openshift.com/pub/openshift-v4/clients/ocp/${OC_VERSION}/openshift-client-linux.tar.gz
    ```
2. Extract the CLI.
    
    ```
    tar xf openshift-client-linux.tar.gz oc
    ```

3. Copy it to a directory in $PATH.

    ```
    sudo install oc /usr/local/bin
    ```

4. Verify that the CLI is executable.
    
    ```
    oc version
    ```
    
    **You should see output similar to the following**
    
    ```
    Client Version: 4.15.5
    Kustomize Version: v5.0.4-0.20230601165947-6ce0bf390ce3
    ```
    
5. Clean up.
    
    ```
    rm openshift-client-linux.tar.gz oc
    ```

## Generate a key pair for cluster node SSH access

During an OpenShift Container Platform installation, you can provide an SSH public key to the installation program. The key is passed to the Red Hat Enterprise Linux CoreOS (RHCOS) nodes through their Ignition config files and is used to authenticate SSH access to the nodes. After the key is passed to the nodes, you can use the key pair to SSH in to the RHCOS nodes as the user core.

If you want to SSH in to your cluster nodes to perform installation debugging or disaster recovery, you must provide the SSH public key during the installation process. The openshift-install gather command also requires the SSH public key to be in place on the cluster nodes.

**Run ssh-keygen:**

```
ssh-keygen -t ed25519 -N '' -f ~/.ssh/id_core
```

**Example output**

```
Generating public/private ed25519 key pair.
Your identification has been saved in /home/admin/.ssh/id_core.
Your public key has been saved in /home/admin/.ssh/id_core.pub.
The key fingerprint is:
SHA256:sOpiEXVXB5TVc0jCUVlW89qcuv/Gb9DhUVs3yf2BAc8 admin@bastion
The key's randomart image is:
+--[ED25519 256]--+
|         o+****=B|
|    . . . ..+o=*O|
|   . ...     E oX|
|  .    o       Bo|
|   .  . S     oo=|
|  .  .        o..|
|   ..        . o |
|  o.          . +|
| . ..        ..+=|
+----[SHA256]-----+
```