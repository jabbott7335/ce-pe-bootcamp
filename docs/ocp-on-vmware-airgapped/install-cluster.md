1. Create the installation directory (by convention named after the cluster).

    ```sh
    mkdir ocpinstall
    ```

1. Copy the Install Config Asset into the installation directory.

    ```sh
    cp install-config.yaml ocpinstall
    ```

1. Create the manifest files.

    ```sh
    openshift-install create manifests --dir ocpinstall
    ```

1. Set the `managementState` of the cluster samples operator to `Removed`.

    ```sh
    cat <<EOF > ocpinstall/openshift/99_openshift_samples.yaml
    apiVersion: samples.operator.openshift.io/v1
    kind: Config
    metadata:
      name: cluster
    spec:
      architectures:
      - x86_64
      managementState: Removed
    EOF
    ```

1. Create the cluster.

    ```sh
    openshift-install create cluster --dir ocpinstall --log-level debug
    ```
    Wait for the installation to complete.
    ``` {.text .no-copy title="Example output"}
    #...
    DEBUG Obtaining RHCOS image file from 'http://192.168.252.2/images/rhcos-vmware.x86_64.ova?sha256=9b3d5a598928ec52b0d32092d0a9a41f0ec8a238eb9fff8563266b9351919e20'
    #...
    INFO All cluster operators have completed progressing
    INFO Checking to see if there is a route at openshift-console/console...
    DEBUG Route found in openshift-console namespace: console
    DEBUG OpenShift console route is admitted
    INFO Install complete!
    INFO To access the cluster as the system:admin user when using 'oc', run 'export KUBECONFIG=/home/admin/ocpinstall/auth/kubeconfig'
    INFO Access the OpenShift web-console here: https://console-openshift-console.apps.ocpinstall.gym.lan
    INFO Login to the console with user: "kubeadmin", and password: "kfeBh-pgwb3-XN3Fi-JpSFj"
    DEBUG Time elapsed per stage:
    DEBUG               pre-bootstrap: 47s
    DEBUG                   bootstrap: 1m5s
    DEBUG                      master: 2m48s
    DEBUG          Bootstrap Complete: 13m36s
    DEBUG                         API: 45s
    DEBUG           Bootstrap Destroy: 2m17s
    DEBUG Cluster Operators Available: 15m41s
    DEBUG    Cluster Operators Stable: 54s
    INFO Time elapsed: 37m17s
    ```

1. Set the `KUBECONFIG` variable.

    ```sh
    export KUBECONFIG=${HOME}/ocpinstall/auth/kubeconfig
    ```

1. Disable the default OperatorHub catalog sources.

    ```sh
    oc patch OperatorHub cluster --type json \
        -p '[{"op": "add", "path": "/spec/disableAllDefaultSources","value": true}]'
    ```

1. Add the mirrored OperatorHub catalog source 

    ```sh
    oc apply -f ./mirrored-content/oc-mirror-workspace/results-*/catalogSource-cs-redhat-operator-index.yaml
    ```

1. Add release signatures.
    ```sh
    oc apply -f ./mirrored-content/oc-mirror-workspace/results-*/release-signatures/
    ```

1. Verify there is a Pod named `cs-redhat-operator-index-*****` in namespace `openshift-marketplace`
    ```sh
    oc -n openshift-marketplace get pods
    ```
    ``` {.text .no-copy title="Example output"}
    NAME                                    READY   STATUS    RESTARTS      AGE
    cs-redhat-operator-index-zxsgt          1/1     Running   0		41s
    marketplace-operator-6bcd474fbf-6njwx   1/1     Running   2 (18m ago)   38m
    ```
