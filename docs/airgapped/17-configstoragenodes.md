---
title: Air-gapped OpenShift Installation
hide:
    - toc
---

## Deploy OpenShift Data Foundation on Storage nodes

This operation is performed to provide OpenShift with Block, Object and File persistent storage. Storage nodes do not consume OpenShift Licences.

1. Create the labels for the infra nodes

    ```{ .text .copy title="[root@bastion ocp4]"}
    oc label node storage01.ocp4.platformengineers.xyz node-role.kubernetes.io/infra=
    ```

    ```{ .text .no-copy title="Output"}
    node/storage01.ocp4.platformengineers.xyz labeled
    ```
