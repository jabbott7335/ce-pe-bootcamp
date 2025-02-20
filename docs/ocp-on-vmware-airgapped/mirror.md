For mirroring OpenShift content, Red Hat provides the `oc-mirror` command-line interface. What content is mirrored is configured by defining an `ImageSetConfiguration` in a file.

## Install the OpenShift CLI and the oc-mirror plugin

1. Install the OpenShift CLI. Repeat this step from the VMware week exercise.

1. Download the plugin.
   
    !!! WARNING "Watch Out"
        Due to a serious bug with the 4.17 version of the `oc-mirror` cli, we will need to perform this section of the lab using **4.16** version of the `oc-mirror` cli instead

    ```sh
    OCP_VERSION=stable-4.16
    ```
    ```sh
    curl -Lo oc-mirror.tar.gz \
        https://mirror.openshift.com/pub/openshift-v4/x86_64/clients/ocp/${OCP_VERSION}/oc-mirror.tar.gz
    ```

2. Extract the plugin.

    ```sh
    tar xf oc-mirror.tar.gz oc-mirror
    ```

3. Move the plugin to `/usr/local/bin`.

    ```sh
    chmod +x oc-mirror
    sudo install oc-mirror /usr/local/bin
    ```

4. Verify.

    ```sh
    oc mirror version
    ```
    ``` {.text .no-copy title="Example output"}
    WARNING: This version information is deprecated and will be replaced with the output from --short. Use --output=yaml|json to get the full version.
    Client Version: version.Info{Major:"", Minor:"", GitVersion:"4.17.0-202410112132.p0.g07714b7.assembly.stream.el9-07714b7", GitCommit:"07714b7c836ec3ad1b776f25b44c3b2c2f083aa2", GitTreeState:"clean", BuildDate:"2024-10-12T03:10:41Z", GoVersion:"go1.22.7 (Red Hat 1.22.7-1.module+el8.10.0+22325+dc584f75) X:strictfipsruntime", Compiler:"gc", Platform:"linux/amd64"}
    ```

5. Clean up.

    ```sh
    rm oc-mirror.tar.gz oc-mirror
    ```

## Collect information for the mirror operation

In order to limit the size of the mirrored content you specify minimal versions of both the platform and operator images.

1. Copy your pull secret from the [Red Hat Hybrid Cloud Console](https://console.redhat.com/openshift/install/pull-secret) and save it in file `${XDG_RUNTIME_DIR}/containers/auth.json`.

1. List all OpenShift versions for channel stable-4.17.

    ```sh
    oc mirror list releases --channel stable-4.17
    ```
    ``` {.text .no-copy title="Example output"}
    Listing stable channels. Use --channel=<channel-name> to filter.
    Use oc-mirror list release --channels to discover other channels.

    Channel: stable-4.17
    Architecture: amd64
    #...
    4.17.0
    4.17.1
    4.17.2
    4.17.3
    ```

1. List available OpenShift OperatorHub catalogs for version 4.17.

    ```sh
    oc mirror list operators --catalogs --version=4.17
    ```
    **Note:** You may find that this command hangs and/or times out. During testing the instructors have found this as well, but it does not affect the subsequent steps. We believe this is an issue on the Red Hat side, or because of configuration in Tech Zone (for example, traffic shaping).

    ``` {.text .no-copy title="Example output"}
    Available OpenShift OperatorHub catalogs:
    OpenShift 4.17:
    registry.redhat.io/redhat/redhat-operator-index:v4.17
    registry.redhat.io/redhat/certified-operator-index:v4.17
    registry.redhat.io/redhat/community-operator-index:v4.17
    registry.redhat.io/redhat/redhat-marketplace-index:v4.17
    ```

1. List the OpenShift Data Foundation operators in catalog `registry.redhat.io/redhat/redhat-operator-index:v4.17`.

    ```sh
    oc mirror list operators \
        --catalog registry.redhat.io/redhat/redhat-operator-index:v4.17 \
        | grep -E 'ocs-operator|odf-operator|mcg-operator|odf-csi-addons-operator'
    ```
    ``` {.text .no-copy title="Example output"}
    mcg-operator                                                stable-4.17
    ocs-operator                                                stable-4.17
    odf-csi-addons-operator                                     stable-4.17
    odf-operator                                                stable-4.17
    ```

1. List all versions in channel `stable-4.17` for package `odf-operator`.

    ```sh
    oc mirror list operators \
      --catalog registry.redhat.io/redhat/redhat-operator-index:v4.17 \
      --package odf-operator \
      --channel stable-4.17
    ```
    ``` {.text .no-copy title="Example output"}
    VERSIONS
    4.17.0-rhodf
    ```
    Repeat this step for operators `ocs-operator`, `mcg-operator` and `odf-csi-addons-operator`.

## Mirror the platform and operator images

Although the mirror registry runs on the same host, the destination (storageConfig) of the mirror operation will be a file instead of a registry. In practice, a bastion host usually isn't running or has access to a registry, so you'll have to bring in the contents as a file.

1. Create the `ImageSetConfiguration` file.

    ```sh
    cat <<EOF > ${HOME}/isc-platform-odf.yaml
    kind: ImageSetConfiguration
    apiVersion: mirror.openshift.io/v1alpha2
    storageConfig:
      local:
        path: ./
    mirror:
      platform:
        channels:
        - name: stable-4.17
          type: ocp
          minVersion: 4.17.12
        graph: true
      operators:
      - catalog: registry.redhat.io/redhat/redhat-operator-index:v4.17
        packages:
        - name: odf-operator
          channels:
          - name: stable-4.17
          minVersion: '4.17.0-rhodf'
        - name: ocs-operator
          channels:
          - name: stable-4.17
          minVersion: '4.17.0-rhodf'
        - name: odf-csi-addons-operator
          channels:
          - name: stable-4.17
          minVersion: '4.17.0-rhodf'
        - name: mcg-operator
          channels: 
          - name: stable-4.17
          minVersion: '4.17.0-rhodf'
    EOF
    ```

1. Initiate the mirroring.

    ```sh
    oc mirror --config=${HOME}/isc-platform-odf.yaml file://ocp-4.17
    ```

    **Note:** you may find that this stage fails. In this case issue the command again. During testing the instructors have found this as well, but it does not affect the subsequent steps. We believe this is an issue on the Red Hat side, or because of configuration in Tech Zone (for example, traffic shaping).

    ``` {.text .no-copy title="Example output"}
    #...
    info: Mirroring completed in 2m51.18s (192.9MB/s)
    Creating archive /home/admin/ocp-4.17/mirror_seq1_000000.tar
    ```

## Copy the mirrored content to the mirror registry

1. Log in.

    ```sh
    podman login 192.168.252.2:8443 --tls-verify=false
    ```

1. Copy the content.

    ```sh
    oc mirror --from ocp-4.17/mirror_seq1_000000.tar docker://192.168.252.2:8443/ocp4 --dest-skip-tls
    ```
    ``` {.text .no-copy title="Example output"}
    #...
    Rendering catalog image "192.168.252.2:8443/ocp4/redhat/redhat-operator-index:v4.17" with file-based catalog
    Writing image mapping to oc-mirror-workspace/results-1731338801/mapping.txt
    Writing UpdateService manifests to oc-mirror-workspace/results-1731338801
    Writing CatalogSource manifests to oc-mirror-workspace/results-1731338801
    Writing ICSP manifests to oc-mirror-workspace/results-1731338801
    ```
