For mirroring OpenShift content, Red Hat provides the `oc-mirror` command-line interface. What content is mirrored is configured by defining an `ImageSetConfiguration` in a file.

The bastion host, after provisioning, runs RHEL 8.7. The latest `stable-4.15` version of `oc-mirror` does not run on RHEL 8.x. An attempt to do so results in the error below.

```
oc-mirror: /lib64/libc.so.6: version `GLIBC_2.33' not found (required by oc-mirror)
oc-mirror: /lib64/libc.so.6: version `GLIBC_2.34' not found (required by oc-mirror)
oc-mirror: /lib64/libc.so.6: version `GLIBC_2.32' not found (required by oc-mirror)
```

!!! warning "Attention please!"

    The instructors are aware that on April 15th 2024, Red Hat made two version of `oc-mirror` available, one for RHEL 8 and one for RHEL 9. However, we have experienced in practice that operating systems are not always at the latest level and that this type of operating system library conflicts are a reality. So, we decided to leave the instructions as is.

How to solve this operating system library conflict? Build a container image with the required operating system libraries and the `oc-mirror` command is the way to go.

## Build the `oc-mirror` container image

1. Set shell variables.

    ```sh
    OC_VERSION=stable-4.15
    ```

1. Download the archive.

    ```sh
    curl -Lo oc-mirror.tar.gz \
        https://mirror.openshift.com/pub/openshift-v4/x86_64/clients/ocp/${OC_VERSION}/oc-mirror.rhel9.tar.gz
    ```

1. Create the directory that will hold the mirrored content.

    ```sh
    mkdir -p ${HOME}/mirrored-content
    ```

1. Copy your pull secret from the [Red Hat Hybrid Cloud Console](https://console.redhat.com/openshift/install/pull-secret){: target="_blank" .external} and save it in file `${HOME}/mirrored-content/.pull-secret`.

1. Build the image.

    ```sh
    podman image build --authfile ${HOME}/mirrored-content/.pull-secret -t oc-mirror:4.15 . -f - <<EOF
    FROM registry.redhat.io/ubi9/ubi:9.3-1610
    ADD oc-mirror.tar.gz /usr/local/bin
    RUN chmod +x /usr/local/bin/oc-mirror
    WORKDIR /mnt
    ENTRYPOINT ["/usr/local/bin/oc-mirror"]
    EOF
    ```

1. Create a shell alias named `oc-mirror`.

    ```sh
    echo "alias oc-mirror='podman container run --rm -it \
    -v ${HOME}/mirrored-content:/mnt:Z \
    -v ${HOME}/mirrored-content/.pull-secret:/root/.docker/config.json oc-mirror:4.15'" \
    | tee -a ${HOME}/.bashrc
    ```

1. Test the alias

    ```sh
    source ${HOME}/.bashrc
    ```
    ```sh
    oc-mirror version
    ```
    ``` {.text .no-copy title="Example output"}
    WARNING: This version information is deprecated and will be replaced with the output from --short. Use --output=yaml|json to get the full version.
    Client Version: version.Info{Major:"", Minor:"", GitVersion:"4.15.0-202403220516.p0.g9621d8f.assembly.stream.el9-9621d8f",    GitCommit:"9621d8f72ecc7a0a13e40b9709b5e19cc621117b", GitTreeSta
    te:"clean", BuildDate:"2024-03-22T09:55:53Z", GoVersion:"go1.20.12 X:strictfipsruntime", Compiler:"gc", Platform:"linux/amd64"}
    ```

## Collect information for the mirror operation

In order to limit the size of the mirrored content you specify minimal versions of both the platform and operator images.

1. List all OpenShift versions for channel stable-4.15.

    ```sh
    oc-mirror list releases --channel stable-4.15
    ```
    ``` {.text .no-copy title="Example output"}
    Listing stable channels. Use --channel=<channel-name> to filter.
    Use oc-mirror list release --channels to discover other channels.

    Channel: stable-4.15
    Architecture: amd64
    4.14.0
    4.14.1
    4.14.2
    4.14.3
    4.14.4
    4.14.5
    4.14.6
    4.14.7
    4.14.8
    4.14.9
    4.14.10
    4.14.11
    4.14.12
    4.14.13
    4.14.14
    4.14.15
    4.14.16
    4.14.17
    4.14.18
    4.14.19
    4.14.20
    4.15.0
    4.15.2
    4.15.3
    4.15.5
    4.15.6
    4.15.8
    ```

1. List available OpenShift OperatorHub catalogs for version 4.15.

    ```sh
    oc-mirror list operators --catalogs --version=4.15
    ```
    ``` {.text .no-copy title="Example output"}
    Available OpenShift OperatorHub catalogs:
    OpenShift 4.15:
    registry.redhat.io/redhat/redhat-operator-index:v4.15
    registry.redhat.io/redhat/certified-operator-index:v4.15
    registry.redhat.io/redhat/community-operator-index:v4.15
    registry.redhat.io/redhat/redhat-marketplace-index:v4.15
    ```

1. List the OpenShift Data Foundation operators in catalog `registry.redhat.io/redhat/redhat-operator-index:v4.15`.

    ```sh
    oc-mirror list operators \
        --catalog registry.redhat.io/redhat/redhat-operator-index:v4.15 \
        | grep -E 'ocs-operator|odf-operator|mcg-operator|odf-csi-addons-operator'
    ```
    ``` {.text .no-copy title="Example output"}
    mcg-operator                                  NooBaa Operator                                          stable-4.15
    ocs-operator                                  OpenShift Container Storage                              stable-4.15
    odf-csi-addons-operator                       CSI Addons                                               stable-4.15
    odf-operator                                  OpenShift Data Foundation                                stable-4.15
    ```

1. List all versions in channel `stable-4.15` for package `odf-operator`.

    ```sh
    oc-mirror list operators \
      --catalog registry.redhat.io/redhat/redhat-operator-index:v4.15 \
      --package odf-operator \
      --channel stable-4.15
    ```
    ``` {.text .no-copy title="Example output"}
    VERSIONS
    4.15.0-rhodf
    4.15.1-rhodf
    ```
    Repeat this step for operators `ocs-operator`, `mcg-operator` and `odf-csi-addons-operator`.

## Mirror the platform and operator images

Although the mirror registry runs on the same host, the destination (storageConfig) of the mirror operation will be a file instead of a registry. In practice, a bastion host usually isn't running or has access to a registry, so you'll have to bring in the contents as a file.

1. Create the `ImageSetConfiguration` file.

    ```sh
    cat <<EOF > ${HOME}/mirrored-content/isc-platform-odf.yaml
    kind: ImageSetConfiguration
    apiVersion: mirror.openshift.io/v1alpha2
    storageConfig:
      local:
        path: ./
    mirror:
      platform:
        channels:
        - name: stable-4.15
          type: ocp
          minVersion: 4.15.8
        graph: true
      operators:
      - catalog: registry.redhat.io/redhat/redhat-operator-index:v4.15
        packages:
        - name: odf-operator
          channels:
          - name: stable-4.15
          minVersion: '4.15.1-rhodf'
        - name: ocs-operator
          channels:
          - name: stable-4.15
          minVersion: '4.15.1-rhodf'
        - name: odf-csi-addons-operator
          channels:
          - name: stable-4.15
          minVersion: '4.15.1-rhodf'
        - name: mcg-operator
          channels: 
          - name: stable-4.15
          minVersion: '4.15.1-rhodf'
    EOF
    ```

1. Initiate the mirroring.

    ```sh
    oc-mirror --config=/mnt/isc-platform-odf.yaml file:///mnt/ocp-4.15
    ```
    ``` {.text .no-copy title="Example output"}
    #...
    info: Mirroring completed in 3m2.56s (165.8MB/s)
    Creating archive /mnt/ocp-4.15/mirror_seq1_000000.tar
    ```

## Copy the mirrored content to the mirror registry

1. Log in.

    ```sh
    podman login 192.168.252.2:8443 --authfile ${HOME}/mirrored-content/.pull-secret --tls-verify=false
    ```

1. Copy the content.

    ```sh
    oc-mirror --from /mnt/ocp-4.15/mirror_seq1_000000.tar docker://192.168.252.2:8443/ocp4 --dest-skip-tls
    ```
    ``` {.text .no-copy title="Example output"}
    #...
    Rendering catalog image "192.168.252.2:8443/ocp4/redhat/redhat-operator-index:v4.15" with file-based catalog
    Writing image mapping to oc-mirror-workspace/results-1711288703/mapping.txt
    Writing UpdateService manifests to oc-mirror-workspace/results-1711288703
    Writing CatalogSource manifests to oc-mirror-workspace/results-1711288703
    Writing ICSP manifests to oc-mirror-workspace/results-1711288703
    ```
