---
Title: Configure the Identity Provider
hide:
    - toc
---

# Configure the Identity Provider

The developers need to be able to log in the cluster hence the need for an identity provider. Next to that requirement, it is a good / best practice to delete user `kubeadmin`. OpenShift supports many identity providers, we will use the HTPasswd provider.

1. Ensure the `htpasswd` command is available on the bastion host.
    ```
    sudo dnf install -y httpd-tools
    ```

2. Create the htpasswd file with user `admin` and password `OCP4all!`.
    
    ```
    htpasswd -Bbc /tmp/htpasswd admin OCP4all!
    ```

3. Add the developers.
    
    ```sh
    for dev in rob ken robert
    do
        htpasswd -b /tmp/htpasswd ${dev} OCP4all!
    done
    ```

4. Create a Secret in the `openshift-config` namespace with data from the htpasswd file, name it `localusers`.
    
    ```
    oc -n openshift-config create secret generic localusers \
    --from-file htpasswd=/tmp/htpasswd
    ```

5. Export the OAuth resource.
    
    ```
    oc get oauth cluster -o yaml > /tmp/oauth.yaml
    ```

6. Edit `/tmp/oauth.yaml`, under `spec:` add the HTPasswd identity provider.
    
    ```yaml title="/tmp/oauth.yaml"
    #...
    spec:
      identityProviders:
        - htpasswd:
            fileData:
              name: localusers
          name: localusers
          type: HTPasswd
          mappingMethod: claim
    ```
    
7. Update the OAuth resource.
    
    ```
    oc replace -f /tmp/oauth.yaml
    ```

8. Monitor the Pods in namespace `openshift-authentication`, they must restart, the rollout is not instant.
    ```
    watch oc -n openshift-authentication get pods
    ```

9. Assign cluster-admin privileges to user admin.
    
    ```
    oc adm policy add-cluster-role-to-user cluster-admin admin
    ```

10. Ensure user admin can log in.
    
    ```
    oc login -u admin
    ```

11. Verify user admin has cluster role cluster-admin, only users with this role are authorized to use get nodes.
    
    ```
    oc auth can-i get nodes
    ```

    ``` title="Example Output"
    Warning: resource 'nodes' is not namespace scoped

    yes
    ```

12. Log in as one of the developers and verify the use of get nodes.
    
    ```
    oc login -u rob
    ```
    
    ```
    oc auth can-i get nodes
    ```

    ``` title="Example Output"
    Warning: resource 'nodes' is not namespace scoped

    no
    ```

13. Switch back to user `admin` and delete `kubeadmin`.
    
    ```
    oc login -u admin
    ```

14. Delete user `kubeadmin`.
    
    ```
    oc -n kube-system delete secret kubeadmin
    ```

15. List the users.
    
    ```
    oc get users
    ```

    ``` title="Example Output"
    NAME    UID                                    FULL NAME   IDENTITIES
    admin   a7cfbf3a-0892-40e0-9dcb-7ba37ecc1824               localusers:admin
    rob     0e3884e4-529b-4b5f-a7e6-2e333796d03a               localusers:rob
    ```

Note that only users who have logged in are listed by this command.