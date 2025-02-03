---
hide:
    - toc
---

<details>
<summary>
N) Deploy App Connect: 
</summary>

1. Install App Connect Catalog Source:
   ```
   oc apply -f catalog-sources/${CP4I_VER}/10-app-connect-catalog-source.yaml 
   ```
   Confirm the catalog source has been deployed successfully before moving to the next step running the following command: 
   ```
   oc get catalogsources appconnect-operator-catalogsource -n openshift-marketplace -o jsonpath='{.status.connectionState.lastObservedState}';echo
   ```
   You should get a response like this:
   ```
   READY
   ```
2. Install App Connect Operator:
   ```
   oc apply -f subscriptions/${CP4I_VER}/07-app-connect-subscription.yaml
   ```
   Confirm the operator has been deployed successfully before moving to the next step running the following command:
   ```
   SUB_NAME=$(oc get deployment ibm-appconnect-operator -n openshift-operators --ignore-not-found -o jsonpath='{.metadata.labels.olm\.owner}');if [ ! -z "$SUB_NAME" ]; then oc get csv/$SUB_NAME --ignore-not-found -o jsonpath='{.status.phase}';fi;echo
   ```
   You should get a response like this:
   ```
   Succeeded
   ```
3. Deploy Dashboard instance:
   ```
   scripts/09a-ace-dashboard-inst-deploy.sh
   ```
   Confirm the instance has been deployed successfully before moving to the next step running the following command:
   ```
   oc get dashboard ace-dashboard -n tools -o jsonpath='{.status.phase}';echo
   ```
   Note this will take few minutes, but at the end you should get a response like this:
   ```
   Ready
   ```
4. Deploy Designer Authoring instance with support for Callable Flows (optional): 
   1. Deploy Switch Server instance:
   ```
   scripts/09b-ace-switch-server-inst-deploy.sh
   ```
   Confirm the instance has been deployed successfully before moving to the next step running the following command:
   ```
   oc get switchserver ace-switch-server -n tools -o jsonpath='{.status.phase}';echo
   ```
   Note this will take few minutes, but at the end you should get a response like this:
   ```
   Ready
   ```
   2. Deploy Designer Authoring instance (optional)
   ```
   scripts/09c-ace-designer-inst-deploy.sh
   ```
   Confirm the instance has been deployed successfully before moving to the next step running the following command:
   ```
   oc get designerauthoring ace-designer-ai -n tools -o jsonpath='{.status.phase}';echo
   ```
   Note this will take few minutes, but at the end you should get a response like this:
   ```
   Ready
   ```
5. Create Bar Auth Configuration:
   ```
   scripts/11-ace-config-barauth-github.sh
   ```
6. Create Policy Configuration to integrate with MQ:
      ```
      scripts/12a-ace-config-policy-mq.sh
      ```
7. Deploy Integration Runtime instances related to MQ and the API:
      ```
      scripts/12c-ace-is-apis-inst-deploy.sh
      ```
      You can check the status using the following command:
      ```
      oc get integrationruntimes -n tools
      ```
8. Configure Sales Force Connector (optional):
      1. Set Environment Variables:  
         ```
         export SF_USER=<my-sf-user>
         export SF_PWD=<my-sf-pwd>
         export SF_CLIENT_ID=<my-sf-client-id>
         export SF_CLIENT_SECRET=<my-sf-client-secret>
         export SF_LOGIN_URL=<my-sf-login-url>
         ```
      2. Create Sales Force Account Configuration:
         ```
         scripts/12b-ace-config-accounts-sf.sh
         ```
      3. Set Environment Variable:
         ```
         export SF_CONNECTOR=YES
         ```
9. Deploy Integration Runtime instance related to SF:
      ```
      scripts/12d-ace-is-sf-inst-deploy.sh
      ```
10. Create Configurations related to ES:
      ```
      scripts/15a-ace-config-policy-es-scram.sh
      scripts/15b-ace-config-setdbparms-es-scram.sh
      scripts/15c-ace-config-truststore-es.sh
      ```
11. Deploy Integration Runtime instance related to ES:
      ```
      scripts/15d-ace-is-es-inst-deploy.sh
      ```
12. Configure Integration with PGSQL DB (Optional):
   1. Create namespace:
      ```
      oc create namespace pgsql
      ```
   2. Enable Operator Group in namespace:
      ```
      oc apply -f resources/12d-pgsql-operatorgroup.yaml
      ```
   3. Install PGSQL Operator at namespace level:
      ```
      oc apply -f resources/12a-pgsql-subscription.yaml
      ```
      Confirm the operator has been deployed successfully before moving to the next step running the following command:
      ```
      SUB_NAME=$(oc get deployment pgo -n pgsql --ignore-not-found -o jsonpath='{.metadata.labels.olm\.owner}');if [ ! -z "$SUB_NAME" ]; then oc get csv/$SUB_NAME -n pgsql --ignore-not-found -o jsonpath='{.status.phase}';fi;echo
      ```
      You should get a response like this:
      ```
      Succeeded
      ```
   4. Create configmap with db configuration:
      ```
      oc apply -f resources/12b-pgsql-config.yaml -n pgsql
      ```
   5. Create a PGSQL DB instance:
      ```
      oc apply -f resources/12c-pgsql-db.yaml -n pgsql
      ```
      Confirm the instance has been deployed successfully before moving to the next step running the following command:
      ```
      oc get pods -l "postgres-operator.crunchydata.com/role=master" -n pgsql -o jsonpath='{.items[0].status.conditions[1].status}' 2>/dev/null;echo
      ```
      After a few minutes you should get a response like this:
      ```
      True
      ``` 
   6. Create Configurations related to PGSQL:
         ```
         scripts/22a-ace-config-odbc-ini-pgsql.sh
         scripts/22b-ace-config-setdbparms-pgsql.sh
         ```
   7. Set Environment Variable:
      ```
      export PGSQL_DB=YES
      ```
13. Deploy Integration Runtime instance to simulate BackEnd:
      ```
      scripts/22c-ace-is-be-inst-deploy.sh
      ```
14. Create Configuration for User Defined Policy:
      ```
      scripts/16-ace-config-policy-udp.sh
      ```
15. Create Configurations related to eMail server:
      ```
      scripts/17a-ace-config-policy-email.sh
      scripts/17b-ace-config-setdbparms-email.sh
      ```
16. Deploy Integration Runtime instance related to ES & eMail:
      ```
      scripts/18a-ace-is-kafka-inst-deploy.sh
      ```
17. Deploy Integration Runtime instances with fry approach (optional):
      ```
      oc apply -f instances/${CP4I_VER}/18a-ace-is-aceivt-instance-fry.yaml -n tools
      oc apply -f instances/${CP4I_VER}/18b-ace-is-aceivt-instance-fry.yaml -n tools
      ```
18. Deploy Integration Runtime instance with bake approach (optional):
      ```
      oc apply -f instances/${CP4I_VER}/19a-ace-is-aceivt-instance-bake.yaml -n tools
      ```
      Confirm the instance has been deployed successfully before moving to the next step running the following command:
      ```
      oc get integrationruntimes -n tools
      ```
      You should get a response like this:
      ```
      NAME                        RESOLVEDVERSION   STATUS   REPLICAS   AVAILABLEREPLICAS   URL                                                                                                AGE     CUSTOMIMAGES
      jgr-ace-bake-cp4i           12.0.9.0-r3       Ready    2          2                   http://jgr-ace-bake-cp4i-http-tools.apps.6597480c8e1478001153ba0d.cloud.techzone.ibm.com           4d      true
      ``` 
19. Configure HPA for the integration runtime previously deployed (optional):
      ```
      oc apply -f resources/09b-ace-hpa-demo.yaml -n tools
      ```
      Confirm HPA has been applied successfully running the following command:
      ```
      oc get hpa -n tools | grep ace-is-hpa-demo
      ```
      You should get a response like this:
      ```
      ace-is-hpa-demo    IntegrationRuntime/jgr-ace-bake-cp4i   0%/10%    2         5         2          3d23h
      ```
20. Create configuration for Server Config related to MLLP & MQ Request/Reply (optional):
      ```
      scripts/21-ace-config-server-config-ach.sh
      ```
21. Deploy Integration Runtime with none HTTP protocol, aka MLLP (optional):
      ```
      oc apply -f instances/${CP4I_VER}/common/23-ace-is-ach-hl7-instance.yaml -n tools
      ```
22. Deploy Integration Runtime instances for MQ Request/Reply scenario (optional):
      ```
      oc apply -f instances/${CP4I_VER}/27-ace-is-mqreqresp-backend-instance.yaml -n tools
      oc apply -f instances/${CP4I_VER}/28-ace-is-mqreqresp-frontend-instance.yaml -n tools
      ```
23. Deploy Integration Runtime to show Multiple BAR files and WebService protocol forcing HTTPS with default Queue Manager plus PVC configuration and secret creation for HTTPS traffic (optional):
      ```
      oc apply -f resources/08-ace-mount-volume-demo.yaml -n tools
      scripts/20-ace-is-certificate-config.sh
      oc apply -f instances/${CP4I_VER}/common/25-ace-is-mqivt-ws-instance.yaml -n tools
      ```
24. Deploy Assembly with managed Integration Runtime and Declarative API related to ES (optional):
      ```
      scripts/23a-assembly-inst-deploy.sh
      ```
25. Deploy Assembly with managed Integration Runtime and Declarative API Product without security (optional):
      ```
      scripts/23b-assembly-inst-deploy.sh
      ```
</details>
&nbsp; 
