---
Title: Create the Install Config Asset
hide:
    - toc
---

# Creating the Install Config Asset

The OpenShift installer uses a YAML configuration file, known as the install config asset, to set the parameters for the installation process.

!!! Tip "Collecting Install Config Asset Information"

  During the provisioning process for the OCP Gymnasium, a YAML file named `vmware-ipi.yaml` was created in the `/home/admin` directory on the bastion host. This file contains essential information that will be used to create the install config asset.  This information can be found in other locations as well such as by examining the VMware environment from within vSphere.

  ```{ .yaml .no-copy linenums="1" title="Contents of /home/admin/vmware-ipi.yaml similar to the following" }
  ---
  vsphere_username: gymuser-f6ckcc6o@techzone.ibm.local
  vsphere_password: FokZLqZ6
  vsphere_hostname: ocpgymwdc-vc.techzone.ibm.local
  vsphere_datastore: /IBMCloud/datastore/gym-50vmycg18b-f6ckcc6o-storage
  vsphere_cluster: ocpgym-wdc
  vsphere_network: gym-50vmycg18b-f6ckcc6o-segment
  vsphere_datacenter: IBMCloud
  vsphere_folder: /IBMCloud/vm/ocpgym-wdc/gym-50vmycg18b-f6ckcc6o
  vsphere_resource_pool: /IBMCloud/host/ocpgym-wdc/Resources/Cluster Resource Pool/Gym Member Resource Pool/gym-50vmycg18b-f6ckcc6o
  vsphere_api_vip: 192.168.252.3
  vsphere_ingress_vip: 192.168.252.4
  base_domain: gym.lan
  cluster_name: ocpinstall
  ocp_custom_openshift_installer_url: http://10.185.220.3/pub/openshift-v4/clients/ocp/
  ```

## Run the Wizard

We will use the create `install-config` wizard to create the install config asset. You will need to check the provided template file for the values. Additionally, the template file has some parameters which are not requested by the wizard, you need to identify these from the template file.

1. Locate **YOUR** pull secret from Red Hat :fontawesome-brands-redhat:.  You can copy your pull secret from the [Red Hat Hybrid Cloud Console](https://console.redhat.com/openshift/install/pull-secret){target="_blank"} web page.

2. Launch the installation wizard.
    
    ```sh
    openshift-install create install-config
    ```

3. Complete the installer survey.

    For this exercise, the cluster must be named ocpinstall; each participant uses their own instance of the OCP Gymnasium and each participant is completely isolated from the others, so this does not cause a problem.

    ```{ .text .no-copy title="Example" }
    ? SSH Public Key /home/admin/.ssh/id_core.pub
    ? Platform vsphere
    ? vCenter ocpgymwdc-vc.techzone.ibm.local
    ? Username gymuser-f6ckcc6o@techzone.ibm.local
    ? Password [? for help] ********
    INFO Connecting to vCenter ocpgymwdc-vc.techzone.ibm.local
    INFO Defaulting to only available datacenter: IBMCloud
    INFO Defaulting to only available cluster: /IBMCloud/host/ocpgym-wdc
    ? Default Datastore /IBMCloud/datastore/gym-50vmycg18b-f6ckcc6o-storage
    ? Network gym-50vmycg18b-f6ckcc6o-segment
    ? Virtual IP Address for API 192.168.252.3
    ? Virtual IP Address for Ingress 192.168.252.4
    ? Base Domain gym.lan
    ? Cluster Name ocpinstall
    ? Pull Secret [? for help] *********************************************

    INFO Install-Config created in: .
    ```

## Update the install config asset

1. In the editor of your choice, open the `install-config.yaml` created by the wizard and review the parameters created with the openshift-install.You will be required to change / add some of the settings within this file.  Use the highlighted sections in the below **example** to make the required changes.

    ```{ .yaml linenums="1" hl_lines="8-12 17-20 24 30 37 39-59" .no-copy title="Reference install-config.yaml" }
    additionalTrustBundlePolicy: Proxyonly
    apiVersion: v1
    baseDomain: gym.lan
    compute:
    - architecture: amd64
      hyperthreading: Enabled
      name: worker
      platform:
        vsphere:
          cpus: 8
          memoryMB: 16384
      replicas: 2
    controlPlane:
      architecture: amd64
      hyperthreading: Enabled
      name: master
      platform:
        vsphere:
          cpus: 4
          memoryMB: 16384
      replicas: 3
    metadata:
      creationTimestamp: null
      name: ocpinstall
    networking:
      clusterNetwork:
      - cidr: 10.128.0.0/14
        hostPrefix: 23
      machineNetwork:
      - cidr: 192.168.252.0/24
      networkType: OVNKubernetes
      serviceNetwork:
      - 172.30.0.0/16
    platform:
      vsphere:
        apiVIPs:
        - 192.168.252.3
        failureDomains:
        - name: ocpgym-wdc
          region: IBMCloud
          server: ocpgymwdc-vc.techzone.ibm.local
          topology:
            computeCluster: /IBMCloud/host/ocpgym-wdc
            datacenter: IBMCloud
            datastore: /IBMCloud/datastore/gym-50vmycg18b-f6ckcc6o-storage
            folder: /IBMCloud/vm/ocpgym-wdc/gym-50vmycg18b-f6ckcc6o
            networks:
            - gym-50vmycg18b-f6ckcc6o-segment
            resourcePool: /IBMCloud/host/ocpgym-wdc/Resources/Cluster Resource Pool/Gym Member Resource Pool/gym-50vmycg18b-f6ckcc6o
          zone: ocpgym-wdc
        ingressVIPs:
        - 192.168.252.4
        vcenters:
        - datacenters:
          - IBMCloud
          password: FokZLqZ6
          port: 443
          server: ocpgymwdc-vc.techzone.ibm.local
          user: gymuser-f6ckcc6o@techzone.ibm.local
    publish: External
    pullSecret: '{"auths": ...}'
    sshKey: |
      ssh-ed25519 AAAA...
    ```

!!! Hint "Which parameters need changes?"
  Which parameters does the template have that were missing from the survey?
  Which values does the template have pre-populated from the survey?
  
  At a minimum, the platform and network sections require modification and some of this information is unique to YOUR environment.

    ```yaml
    #...
    compute:
    - architecture: amd64
      hyperthreading: Enabled
      name: worker
      platform:
        vsphere:
          cpus: 8
          memoryMB: 16384
      replicas: 2
    #...
    networking:
      #...
      machineNetwork:
      - cidr: 192.168.252.0/24
      #...
    platform:
      vsphere:
        #...
        failureDomains:
        - name: YOUR_VSPHERE_CLUSTER
          region: IBMCloud
          #...
          topology:
            #...
            folder: YOUR_VSPHERE_FOLDER
            #...
            resourcePool: YOUR_VSPHERE_RESOURCE_POOL
          zone: YOUR_VSPHERE_CLUSTER
    #...
    ```

!!! Warning "Hold on!"
    Where are the infrastructure nodes dear instructors? Good catch! Infrastructure nodes currently can not be specified in the install config asset, we will ensure they are created in the next section.
