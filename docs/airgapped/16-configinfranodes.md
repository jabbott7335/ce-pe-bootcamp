---
title: Configure Infrastructure Nodes
hide:
    - toc
---

## Move ingress routing and internal registry loads to infrastructure nodes

This operation is performed to move OpenShift infrastructure components from the compute nodes to the infra nodes. The infra nodes do not consume OpenShift licenses.

1. Create the labels for the infra nodes

    ```{ .text .copy title="[root@bastion ocp4]"}
    oc label node infra01.ocp4.platformengineers.xyz node-role.kubernetes.io/infra=
    ```

    ```{ .text .no-copy title="Output"}
    node/infra01.ocp4.platformengineers.xyz labeled
    ```

    ```{ .text .copy title="[root@bastion ocp4]"}
    oc label node infra02.ocp4.platformengineers.xyz node-role.kubernetes.io/infra=
    ```

    ```{ .text .no-copy title="Output"}
    node/infra02.ocp4.platformengineers.xyz labeled
    ```

    ```{ .text .copy title="[root@bastion ocp4]"}
    oc label node infra03.ocp4.platformengineers.xyz node-role.kubernetes.io/infra=
    ```

    ```{ .text .no-copy title="Output"}
    node/infra03.ocp4.platformengineers.xyz labeled
    ```

1. Taint the infra nodes to prevent them from receiving other workloads

    ```{ .text .copy title="[root@bastion ocp4]"}
    oc adm taint nodes -l node-role.kubernetes.io/infra infra=reserved:NoSchedule infra=reserved:NoExecute
    ```

    ```{ .text .no-copy title="Output"}
    node/infra01.ocp4.platformengineers.xyz tainted
    node/infra02.ocp4.platformengineers.xyz tainted
    node/infra03.ocp4.platformengineers.xyz tainted
    ```

1. Move the OpenShift cluster ingress controller to the infra nodes.

    ```{ .text .copy title="[root@bastion ocp4]"}
    oc edit ingresscontroller default -n openshift-ingress-operator
    ```

1. Copy the highlighted lines into your default Ingress Controller specification.

    ```{ .yaml .copy title="deault" hl_lines="25-35"}
    # Please edit the object below. Lines beginning with a '#' will be ignored,
    # and an empty file will abort the edit. If an error occurs while saving this file will be
    # reopened with the relevant failures.
    #
    apiVersion: operator.openshift.io/v1
    kind: IngressController
    metadata:
    creationTimestamp: "2023-09-14T13:30:51Z"
    finalizers:
    - ingresscontroller.operator.openshift.io/finalizer-ingresscontroller
    generation: 2
    name: default
    namespace: openshift-ingress-operator
    resourceVersion: "2658200"
    uid: 40d5648f-98c3-46ac-9223-773e52b65f6a
    spec:
    clientTLS:
        clientCA:
        name: ""
        clientCertificatePolicy: ""
    httpCompression: {}
    httpEmptyRequestsPolicy: Respond
    httpErrorCodePages:
        name: ""
    nodePlacement:
      nodeSelector:
        matchLabels:
          node-role.kubernetes.io/infra: ""
      tolerations:
      - effect: NoSchedule
        key: infra
        value: reserved
      - effect: NoExecute
        key: infra
        value: reserved
    replicas: 2
    tuningOptions:
        reloadInterval: 0s
    [...]
    ```

1. Move the internal OpenShift image registry to the infra nodes

    ```{ .text .copy title="[root@bastion ocp4]"}
    oc patch configs.imageregistry.operator.openshift.io/cluster --type=merge -p '{"spec":{"nodeSelector": {"node-role.kubernetes.io/infra": ""},"tolerations": [{"effect":"NoSchedule","key": "infra","value": "reserved"},{"effect":"NoExecute","key": "infra","value": "reserved"}]}}'
    ```

    ```{ .text .no-copy title="Output"}
    config.imageregistry.operator.openshift.io/cluster patched
    ```
