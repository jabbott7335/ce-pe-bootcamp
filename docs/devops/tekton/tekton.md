---
Title: Lab CI 1 - Tekton
hide:
    - toc
---

## Prerequisites

Make sure your OpenShift environment is properly setup.

Follow the instructions [here](../../index.md#environment-setup)

## SetUp

### Tekton CLI Installation

[Tekton CLI](https://github.com/tektoncd/cli) is command line utility used to interact with the Tekton resources.

Follow the instructions on the tekton CLI github repository https://github.com/tektoncd/cli#installing-tkn

For MacOS for example you can use brew

```bash 
brew tap tektoncd/tools
brew install tektoncd/tools/tektoncd-cli
```
Verify the Tekton cli
```
tkn version
```
The command should show a result like:
```
Client version: 0.39.1
```
If you already have the `tkn` install you can upgrade running
```
brew upgrade tektoncd/tools/tektoncd-cli
```

### OpenShift Pipelines Operator Installation

Install the OpenShift Pipelines Operator ([You can find some instructions here](https://docs.openshift.com/pipelines/1.17/install_config/installing-pipelines.html))


!!! Note
    Version will vary based on the version of OpenShift you are running.
 
    
***Note***: It will take few mins for the OpenShift Pipelines components to be installed, you an watch the status using the command:
```
oc get pods -n openshift-operators -w
```
You can use `Ctrl+c` to terminate the watch
A successful deployment of Tekton pipelines will show the following pods:

```bash
NAME                                         READY   STATUS    RESTARTS   AGE
openshift-pipelines-operator-9cdbbb854-x9tvs   1/1     Running   0          25s
```

!!! note "OpenShift Pipelines vs Tekton"
    You may notice we sometimes use Tekton and OpenShift Pipelines interchangeably in this guide. OpenShift Pipelines is a Red-Hat-provided Operator that installs and manages Tekton on OpenShift. All Tekton concepts apply to `OpenShift Pipelines`.
 

## Create Target Namespace

Set the environment variable `NAMESPACE` to `tekton-demo`, if you open a new terminal remember to set this environment again
```
export NAMESPACE=tekton-demo
```

Create a the namespace using the variable `NAMESPACE`
```
oc new-project $NAMESPACE
```

## Tasks

### Task Creation

- Create the below yaml files.
- The following snippet shows what a Tekton Task YAML looks like:
- Create the file **task-test.yaml**
```yaml
apiVersion: tekton.dev/v1beta1
kind: Task
metadata:
  name: java-test
spec:
  params:
      - name: url
        default: https://github.com/ibm-cloud-architecture/cloudnative_sample_app
      - name: revision
        default: master
  steps:
      - name: git-clone
        image: alpine/git
        script: |
          git clone -b $(params.revision) --depth 1 $(params.url) /workspace/source
      - name: test
        image: maven:3.3-jdk-8
        workingdir: /workspace/source
        script: |
          mvn test
          echo "tests passed with rc=$?"
        volumeMounts:
          - name: m2-repository
            mountPath: /root/.m2
  volumes:
      - name: m2-repository
        emptyDir: {}
  workspaces:
    - description: |
        Container build context, like for instance a application source code
        followed by a `Dockerfile`.
      name: source
```

Each Task has the following:

#### Parameters  
Allows passing configurable values to the Task.  
- **Required fields:**  
  - `name`: The unique identifier for the parameter.  
  - `type`: The data type of the parameter (e.g., `string`, `array`).  

#### Workspaces  
Provides shared storage for data between steps or Tasks.  
- **Required fields:**  
  - `name`: The unique identifier for the workspace.  

#### Steps  
Defines the containerized commands executed in the Task.  
- **Required fields:**  
  - `name`: The unique identifier for the step.  
  - `image`: The container image used to execute the step.
  

### Task Deploy

The application test task could be created using the command:

```bash
oc apply -f task-test.yaml -n $NAMESPACE
```

We will use the Tekton cli to inspect the created resources
```
tkn task ls -n $NAMESPACE
```

The above command should list one Task as shown below:

```bash
NAME        AGE
java-test   22 seconds ago
```

### TaskRun

The [TaskRun](https://github.com/tektoncd/pipeline/blob/master/docs/taskruns.md) is used to run a specific task independently. In the following section we will run the build-app task created in the previous step

#### TaskRun Creation

The following snippet shows what a Tekton TaskRun YAML looks like. Create the file **taskrun-test.yaml**:
```yaml
apiVersion: tekton.dev/v1
kind: TaskRun
metadata:
  generateName: java-test-
spec:
  params:
    - name: url
      value: 'https://github.com/ibm-cloud-architecture/cloudnative_sample_app.git'
    - name: revision
      value: master
  serviceAccountName: pipeline
  taskRef:
    kind: Task
    name: java-test
  timeout: 1h0m0s
  workspaces:
    - name: source
      emptyDir: {}
```
- **generateName** - since the TaskRun can be run many times, in order to have unique name across the TaskRun ( helpful when checking the TaskRun history) we use this generateName instead of name. When Kubernetes sees generateName it will generate unquie set of characters and suffix the same to build-app-, similar to how pod names are generated
- **taskRef** - this is used to refer to the Task by its name that will be run as part of this TaskRun. In this example we use build-app Task.
- As described in the earlier section that the Task inputs and outputs could be overridden via TaskRun.
- **params** - this are the parameter values that are passed to the task

The application test task(java-maven-test) could be run using the command:

```bash
oc create -f taskrun-test.yaml -n $NAMESPACE 
```
- **Note** - As tasks will use generated name, never use `oc apply -f taskrun-test.yaml`

We will use the Tekton cli to inspect the created resources:

```bash
tkn tr ls -n $NAMESPACE
```

The above command should list one TaskRun as shown below:
```bash
NAME                   STARTED        DURATION   STATUS
java-test-9rkkt        1 minute ago   ---        Running(Pending)
```
**Note** - It will take few seconds for the TaskRun to show status as Running as it needs to download the container images.


To check the logs of the Task Run using the `tkn`:
```bash
tkn tr logs -f --last -n $NAMESPACE
```

**Note** - Each task step will be run within a container of its own.
The -f or -a allows to tail the logs from all the containers of the task. For more options run `tkn tr logs --help`

If you see the TaskRun status as Failed or Error use the following command to check the reason for error:
```bash
tkn tr describe --last -n $NAMESPACE
```

If it is successful, you will see something like below:
```bash
tkn tr ls -n $NAMESPACE
```

The above command should list one TaskRun as shown below:
```bash
NAME                                STARTED          DURATION   STATUS
java-test-9rkkt                     36 minutes ago   33s        Succeeded
```

### Creating additional tasks and deploying them

Create a Task to build a container image and push to the registry. This task will be later used by the pipeline:

```yaml
apiVersion: tekton.dev/v1
kind: Task
metadata:
  name: buildah-build
spec:
  description: |
    Buildah task builds source into a container image and
    then pushes it to a container registry.
  params:
    - description: |
        Fully qualified container image name to be built by buildah.
      name: IMAGE
      type: string
    - default: ./Dockerfile
      description: |
        Path to the `Dockerfile` (or `Containerfile`) relative to the `source` workspace.
      name: DOCKERFILE
      type: string
    - default: .
      description: |
        Path to the directory to use as context.
      name: CONTEXT
      type: string
    - default: vfs
      description: |
        Set buildah storage driver to reflect the current cluster node's
        settings.
      name: STORAGE_DRIVER
      type: string
    - default: oci
      description: The format of the built container, oci or docker
      name: FORMAT
      type: string
    - default: ""
      description: |
        Extra parameters passed for the build command when building images.
      name: BUILD_EXTRA_ARGS
      type: string
    - default: ""
      description: |
        Extra parameters passed for the push command when pushing images.
      name: PUSH_EXTRA_ARGS
      type: string
    - default: "false"
      description: |
        Skip pushing the image to the container registry.
      name: SKIP_PUSH
      type: string
    - default: "true"
      description: |
        Sets the TLS verification flag, `true` is recommended.
      name: TLS_VERIFY
      type: string
    - default: "false"
      description: |
        Turns on verbose logging, all commands executed will be printed out.
      name: VERBOSE
      type: string
  results:
    - description: |
        Fully qualified image name.
      name: IMAGE_URL
      type: string
    - description: |
        Digest of the image just built.
      name: IMAGE_DIGEST
      type: string
  stepTemplate:
    computeResources: {}
    env:
      - name: PARAMS_IMAGE
        value: $(params.IMAGE)
      - name: PARAMS_CONTEXT
        value: $(params.CONTEXT)
      - name: PARAMS_DOCKERFILE
        value: $(params.DOCKERFILE)
      - name: PARAMS_FORMAT
        value: $(params.FORMAT)
      - name: PARAMS_STORAGE_DRIVER
        value: $(params.STORAGE_DRIVER)
      - name: PARAMS_BUILD_EXTRA_ARGS
        value: $(params.BUILD_EXTRA_ARGS)
      - name: PARAMS_PUSH_EXTRA_ARGS
        value: $(params.PUSH_EXTRA_ARGS)
      - name: PARAMS_SKIP_PUSH
        value: $(params.SKIP_PUSH)
      - name: PARAMS_TLS_VERIFY
        value: $(params.TLS_VERIFY)
      - name: PARAMS_VERBOSE
        value: $(params.VERBOSE)
      - name: WORKSPACES_SOURCE_BOUND
        value: $(workspaces.source.bound)
      - name: WORKSPACES_SOURCE_PATH
        value: $(workspaces.source.path)
      - name: WORKSPACES_DOCKERCONFIG_BOUND
        value: $(workspaces.dockerconfig.bound)
      - name: WORKSPACES_DOCKERCONFIG_PATH
        value: $(workspaces.dockerconfig.path)
      - name: WORKSPACES_RHEL_ENTITLEMENT_BOUND
        value: $(workspaces.rhel-entitlement.bound)
      - name: WORKSPACES_RHEL_ENTITLEMENT_PATH
        value: $(workspaces.rhel-entitlement.path)
      - name: RESULTS_IMAGE_URL_PATH
        value: $(results.IMAGE_URL.path)
      - name: RESULTS_IMAGE_DIGEST_PATH
        value: $(results.IMAGE_DIGEST.path)
  steps:
    - computeResources: {}
      image: registry.redhat.io/ubi8/ubi-minimal@sha256:f729a7f5685ea823e87ffd68aff988f2b8ff8d52126ade4e6de7c68088f28ebd
      name: load-scripts
      script: |
        set -e
        printf '%s' "IyEvdXNyL2Jpbi9lbnYgYmFzaAojCiMgV3JhcHBlciBhcm91bmQgImJ1aWxkYWggYnVkIiB0byBidWlsZCBhbmQgcHVzaCBhIGNvbnRhaW5lciBpbWFnZSBiYXNlZCBvbiBhIERvY2tlcmZpbGUuCiMKCnNob3B0IC1zIGluaGVyaXRfZXJyZXhpdApzZXQgLWV1IC1vIHBpcGVmYWlsCgpzb3VyY2UgIiQoZGlybmFtZSAke0JBU0hfU09VUkNFWzBdfSkvY29tbW9uLnNoIgpzb3VyY2UgIiQoZGlybmFtZSAke0JBU0hfU09VUkNFWzBdfSkvYnVpbGRhaC1jb21tb24uc2giCgpmdW5jdGlvbiBfYnVpbGRhaCgpIHsKICAgIGJ1aWxkYWggXAogICAgICAgIC0tc3RvcmFnZS1kcml2ZXI9IiR7UEFSQU1TX1NUT1JBR0VfRFJJVkVSfSIgXAogICAgICAgIC0tdGxzLXZlcmlmeT0iJHtQQVJBTVNfVExTX1ZFUklGWX0iIFwKICAgICAgICAkeyp9Cn0KCiMKIyBQcmVwYXJlCiMKCiMgbWFraW5nIHN1cmUgdGhlIHJlcXVpcmVkIHdvcmtzcGFjZSAic291cmNlIiBpcyBib3VuZGVkLCB3aGljaCBtZWFucyBpdHMgdm9sdW1lIGlzIGN1cnJlbnRseSBtb3VudGVkCiMgYW5kIHJlYWR5IHRvIHVzZQpwaGFzZSAiSW5zcGVjdGluZyBzb3VyY2Ugd29ya3NwYWNlICcke1dPUktTUEFDRVNfU09VUkNFX1BBVEh9JyAoUFdEPScke1BXRH0nKSIKW1sgIiR7V09SS1NQQUNFU19TT1VSQ0VfQk9VTkR9IiAhPSAidHJ1ZSIgXV0gJiYKICAgIGZhaWwgIldvcmtzcGFjZSAnc291cmNlJyBpcyBub3QgYm91bmRlZCIKCnBoYXNlICJBc3NlcnRpbmcgdGhlIGRvY2tlcmZpbGUvY29udGFpbmVyZmlsZSAnJHtET0NLRVJGSUxFX0ZVTEx9JyBleGlzdHMiCltbICEgLWYgIiR7RE9DS0VSRklMRV9GVUxMfSIgXV0gJiYKICAgIGZhaWwgIkRvY2tlcmZpbGUgbm90IGZvdW5kIGF0OiAnJHtET0NLRVJGSUxFX0ZVTEx9JyIKCnBoYXNlICJJbnNwZWN0aW5nIGNvbnRleHQgJyR7UEFSQU1TX0NPTlRFWFR9JyIKW1sgISAtZCAiJHtQQVJBTVNfQ09OVEVYVH0iIF1dICYmCiAgICBmYWlsICJDT05URVhUIHBhcmFtIGlzIG5vdCBmb3VuZCBhdCAnJHtQQVJBTVNfQ09OVEVYVH0nLCBvbiBzb3VyY2Ugd29ya3NwYWNlIgoKIyBIYW5kbGUgb3B0aW9uYWwgZG9ja2VyY29uZmlnIHNlY3JldAppZiBbWyAiJHtXT1JLU1BBQ0VTX0RPQ0tFUkNPTkZJR19CT1VORH0iID09ICJ0cnVlIiBdXTsgdGhlbgoKICAgICMgaWYgY29uZmlnLmpzb24gZXhpc3RzIGF0IHdvcmtzcGFjZSByb290LCB3ZSB1c2UgdGhhdAogICAgaWYgdGVzdCAtZiAiJHtXT1JLU1BBQ0VTX0RPQ0tFUkNPTkZJR19QQVRIfS9jb25maWcuanNvbiI7IHRoZW4KICAgICAgICBleHBvcnQgRE9DS0VSX0NPTkZJRz0iJHtXT1JLU1BBQ0VTX0RPQ0tFUkNPTkZJR19QQVRIfSIKCiAgICAgICAgIyBlbHNlIHdlIGxvb2sgZm9yIC5kb2NrZXJjb25maWdqc29uIGF0IHRoZSByb290CiAgICBlbGlmIHRlc3QgLWYgIiR7V09SS1NQQUNFU19ET0NLRVJDT05GSUdfUEFUSH0vLmRvY2tlcmNvbmZpZ2pzb24iOyB0aGVuCiAgICAgICAgIyBlbnN1cmUgLmRvY2tlciBleGlzdCBiZWZvcmUgdGhlIGNvcHlpbmcgdGhlIGNvbnRlbnQKICAgICAgICBpZiBbICEgLWQgIiRIT01FLy5kb2NrZXIiIF07IHRoZW4KICAgICAgICAgICBta2RpciAtcCAiJEhPTUUvLmRvY2tlciIKICAgICAgICBmaQogICAgICAgIGNwICIke1dPUktTUEFDRVNfRE9DS0VSQ09ORklHX1BBVEh9Ly5kb2NrZXJjb25maWdqc29uIiAiJEhPTUUvLmRvY2tlci9jb25maWcuanNvbiIKICAgICAgICBleHBvcnQgRE9DS0VSX0NPTkZJRz0iJEhPTUUvLmRvY2tlciIKCiAgICAgICAgIyBuZWVkIHRvIGVycm9yIG91dCBpZiBuZWl0aGVyIGZpbGVzIGFyZSBwcmVzZW50CiAgICBlbHNlCiAgICAgICAgZWNobyAibmVpdGhlciAnY29uZmlnLmpzb24nIG5vciAnLmRvY2tlcmNvbmZpZ2pzb24nIGZvdW5kIGF0IHdvcmtzcGFjZSByb290IgogICAgICAgIGV4aXQgMQogICAgZmkKZmkKCkVOVElUTEVNRU5UX1ZPTFVNRT0iIgppZiBbWyAiJHtXT1JLU1BBQ0VTX1JIRUxfRU5USVRMRU1FTlRfQk9VTkR9IiA9PSAidHJ1ZSIgXV07IHRoZW4KICAgIEVOVElUTEVNRU5UX1ZPTFVNRT0iLS12b2x1bWUgJHtXT1JLU1BBQ0VTX1JIRUxfRU5USVRMRU1FTlRfUEFUSH06L2V0Yy9wa2kvZW50aXRsZW1lbnQiCmZpCgojCiMgQnVpbGQKIwoKcGhhc2UgIkJ1aWxkaW5nICcke1BBUkFNU19JTUFHRX0nIGJhc2VkIG9uICcke0RPQ0tFUkZJTEVfRlVMTH0nIgoKW1sgLW4gIiR7UEFSQU1TX0JVSUxEX0VYVFJBX0FSR1N9IiBdXSAmJgogICAgcGhhc2UgIkV4dHJhICdidWlsZGFoIGJ1ZCcgYXJndW1lbnRzIGluZm9ybWVkOiAnJHtQQVJBTVNfQlVJTERfRVhUUkFfQVJHU30nIgoKX2J1aWxkYWggYnVkICR7UEFSQU1TX0JVSUxEX0VYVFJBX0FSR1N9IFwKICAgICRFTlRJVExFTUVOVF9WT0xVTUUgXAogICAgLS1uby1jYWNoZSBcCiAgICAtLWZpbGU9IiR7RE9DS0VSRklMRV9GVUxMfSIgXAogICAgLS10YWc9IiR7UEFSQU1TX0lNQUdFfSIgXAogICAgJHtQQVJBTVNfQ09OVEVYVH0KCmlmIFtbICIke1BBUkFNU19TS0lQX1BVU0h9IiA9PSAidHJ1ZSIgXV07IHRoZW4KICAgIHBoYXNlICJTa2lwcGluZyBwdXNoaW5nICcke1BBUkFNU19JTUFHRX0nIHRvIHRoZSBjb250YWluZXIgcmVnaXN0cnkhIgogICAgZXhpdCAwCmZpCgojCiMgUHVzaAojCgpwaGFzZSAiUHVzaGluZyAnJHtQQVJBTVNfSU1BR0V9JyB0byB0aGUgY29udGFpbmVyIHJlZ2lzdHJ5IgoKW1sgLW4gIiR7UEFSQU1TX1BVU0hfRVhUUkFfQVJHU30iIF1dICYmCiAgICBwaGFzZSAiRXh0cmEgJ2J1aWxkYWggYnVkJyBhcmd1bWVudHMgaW5mb3JtZWQ6ICcke1BBUkFNU19QVVNIX0VYVFJBX0FSR1N9JyIKCiMgdGVtcG9yYXJ5IGZpbGUgdG8gc3RvcmUgdGhlIGltYWdlIGRpZ2VzdCwgaW5mb3JtYXRpb24gb25seSBvYnRhaW5lZCBhZnRlciBwdXNoaW5nIHRoZSBpbWFnZSB0byB0aGUKIyBjb250YWluZXIgcmVnaXN0cnkKZGVjbGFyZSAtciBkaWdlc3RfZmlsZT0iL3RtcC9idWlsZGFoLWRpZ2VzdC50eHQiCgpfYnVpbGRhaCBwdXNoICR7UEFSQU1TX1BVU0hfRVhUUkFfQVJHU30gXAogICAgLS1kaWdlc3RmaWxlPSIke2RpZ2VzdF9maWxlfSIgXAogICAgJHtQQVJBTVNfSU1BR0V9IFwKICAgIGRvY2tlcjovLyR7UEFSQU1TX0lNQUdFfQoKIwojIFJlc3VsdHMKIwoKcGhhc2UgIkluc3BlY3RpbmcgZGlnZXN0IHJlcG9ydCAoJyR7ZGlnZXN0X2ZpbGV9JykiCgpbWyAhIC1yICIke2RpZ2VzdF9maWxlfSIgXV0gJiYKICAgIGZhaWwgIlVuYWJsZSB0byBmaW5kIGRpZ2VzdC1maWxlIGF0ICcke2RpZ2VzdF9maWxlfSciCgpkZWNsYXJlIC1yIGRpZ2VzdF9zdW09IiQoY2F0ICR7ZGlnZXN0X2ZpbGV9KSIKCltbIC16ICIke2RpZ2VzdF9zdW19IiBdXSAmJgogICAgZmFpbCAiRGlnZXN0IGZpbGUgJyR7ZGlnZXN0X2ZpbGV9JyBpcyBlbXB0eSEiCgpwaGFzZSAiU3VjY2Vzc2Z1bHkgYnVpbHQgY29udGFpbmVyIGltYWdlICcke1BBUkFNU19JTUFHRX0nICgnJHtkaWdlc3Rfc3VtfScpIgplY2hvIC1uICIke1BBUkFNU19JTUFHRX0iIHwgdGVlICR7UkVTVUxUU19JTUFHRV9VUkxfUEFUSH0KZWNobyAtbiAiJHtkaWdlc3Rfc3VtfSIgfCB0ZWUgJHtSRVNVTFRTX0lNQUdFX0RJR0VTVF9QQVRIfQo=" |base64 -d >buildah-bud.sh
        printf '%s' "IyEvdXNyL2Jpbi9lbnYgYmFzaAoKZGVjbGFyZSAtcnggUEFSQU1TX0lNQUdFPSIke1BBUkFNU19JTUFHRTotfSIKZGVjbGFyZSAtcnggUEFSQU1TX0RPQ0tFUkZJTEU9IiR7UEFSQU1TX0RPQ0tFUkZJTEU6LX0iCmRlY2xhcmUgLXJ4IFBBUkFNU19DT05URVhUPSIke1BBUkFNU19DT05URVhUOi19IgpkZWNsYXJlIC1yeCBQQVJBTVNfU1RPUkFHRV9EUklWRVI9IiR7UEFSQU1TX1NUT1JBR0VfRFJJVkVSOi19IgpkZWNsYXJlIC1yeCBQQVJBTVNfQlVJTERfRVhUUkFfQVJHUz0iJHtQQVJBTVNfQlVJTERfRVhUUkFfQVJHUzotfSIKZGVjbGFyZSAtcnggUEFSQU1TX1BVU0hfRVhUUkFfQVJHUz0iJHtQQVJBTVNfUFVTSF9FWFRSQV9BUkdTOi19IgpkZWNsYXJlIC1yeCBQQVJBTVNfU0tJUF9QVVNIPSIke1BBUkFNU19TS0lQX1BVU0g6LX0iCmRlY2xhcmUgLXJ4IFBBUkFNU19UTFNfVkVSSUZZPSIke1BBUkFNU19UTFNfVkVSSUZZOi19IgpkZWNsYXJlIC1yeCBQQVJBTVNfVkVSQk9TRT0iJHtQQVJBTVNfVkVSQk9TRTotfSIKCmRlY2xhcmUgLXJ4IFdPUktTUEFDRVNfU09VUkNFX1BBVEg9IiR7V09SS1NQQUNFU19TT1VSQ0VfUEFUSDotfSIKZGVjbGFyZSAtcnggV09SS1NQQUNFU19TT1VSQ0VfQk9VTkQ9IiR7V09SS1NQQUNFU19TT1VSQ0VfQk9VTkQ6LX0iCmRlY2xhcmUgLXJ4IFdPUktTUEFDRVNfRE9DS0VSQ09ORklHX1BBVEg9IiR7V09SS1NQQUNFU19ET0NLRVJDT05GSUdfUEFUSDotfSIKZGVjbGFyZSAtcnggV09SS1NQQUNFU19ET0NLRVJDT05GSUdfQk9VTkQ9IiR7V09SS1NQQUNFU19ET0NLRVJDT05GSUdfQk9VTkQ6LX0iCmRlY2xhcmUgLXJ4IFdPUktTUEFDRVNfUkhFTF9FTlRJVExFTUVOVF9QQVRIPSIke1dPUktTUEFDRVNfUkhFTF9FTlRJVExFTUVOVF9QQVRIOi19IgpkZWNsYXJlIC1yeCBXT1JLU1BBQ0VTX1JIRUxfRU5USVRMRU1FTlRfQk9VTkQ9IiR7V09SS1NQQUNFU19SSEVMX0VOVElUTEVNRU5UX0JPVU5EOi19IgoKZGVjbGFyZSAtcnggUkVTVUxUU19JTUFHRV9ESUdFU1RfUEFUSD0iJHtSRVNVTFRTX0lNQUdFX0RJR0VTVF9QQVRIOi19IgpkZWNsYXJlIC1yeCBSRVNVTFRTX0lNQUdFX1VSTF9QQVRIPSIke1JFU1VMVFNfSU1BR0VfVVJMX1BBVEg6LX0iCgojCiMgRG9ja2VyZmlsZQojCgojIGV4cG9zaW5nIHRoZSBmdWxsIHBhdGggdG8gdGhlIGNvbnRhaW5lciBmaWxlLCB3aGljaCBieSBkZWZhdWx0IHNob3VsZCBiZSByZWxhdGl2ZSB0byB0aGUgcHJpbWFyeQojIHdvcmtzcGFjZSwgdG8gcmVjZWl2ZSBhIGRpZmZlcmVudCBjb250YWluZXItZmlsZSBsb2NhdGlvbgpkZWNsYXJlIC1yIGRvY2tlcmZpbGVfb25fd3M9IiR7V09SS1NQQUNFU19TT1VSQ0VfUEFUSH0vJHtQQVJBTVNfRE9DS0VSRklMRX0iCmRlY2xhcmUgLXggRE9DS0VSRklMRV9GVUxMPSIke0RPQ0tFUkZJTEVfRlVMTDotJHtkb2NrZXJmaWxlX29uX3dzfX0iCgojCiMgQXNzZXJ0aW5nIEVudmlyb25tZW50CiMKCltbIC16ICIke0RPQ0tFUkZJTEVfRlVMTH0iIF1dICYmCiAgICBmYWlsICJ1bmFibGUgdG8gZmluZCB0aGUgRG9ja2VyZmlsZSwgRE9DS0VSRklMRSBtYXkgaGF2ZSBhbiBpbmNvcnJlY3QgbG9jYXRpb24iCgpleHBvcnRlZF9vcl9mYWlsIFwKICAgIFdPUktTUEFDRVNfU09VUkNFX1BBVEggXAogICAgUEFSQU1TX0lNQUdFCgojCiMgVmVyYm9zZSBPdXRwdXQKIwoKaWYgW1sgIiR7UEFSQU1TX1ZFUkJPU0V9IiA9PSAidHJ1ZSIgXV07IHRoZW4KICAgIHNldCAteApmaQo=" |base64 -d >buildah-common.sh
        printf '%s' "IyEvdXNyL2Jpbi9lbnYgYmFzaAoKIyB0ZWt0b24ncyBob21lIGRpcmVjdG9yeQpkZWNsYXJlIC1yeCBURUtUT05fSE9NRT0iJHtURUtUT05fSE9NRTotL3Rla3Rvbi9ob21lfSIKCiMKIyBGdW5jdGlvbnMKIwoKZnVuY3Rpb24gZmFpbCgpIHsKICAgIGVjaG8gIkVSUk9SOiAkeyp9IiAyPiYxCiAgICBleGl0IDEKfQoKZnVuY3Rpb24gcGhhc2UoKSB7CiAgICBlY2hvICItLS0+IFBoYXNlOiAkeyp9Li4uIgp9CgojIGFzc2VydCBsb2NhbCB2YXJpYWJsZXMgYXJlIGV4cG9yZXRlZCBvbiB0aGUgZW52aXJvbm1lbnQKZnVuY3Rpb24gZXhwb3J0ZWRfb3JfZmFpbCgpIHsKICAgIGRlY2xhcmUgLWEgX3JlcXVpcmVkX3ZhcnM9IiR7QH0iCgogICAgZm9yIHYgaW4gJHtfcmVxdWlyZWRfdmFyc1tAXX07IGRvCiAgICAgICAgW1sgLXogIiR7IXZ9IiBdXSAmJgogICAgICAgICAgICBmYWlsICInJHt2fScgZW52aXJvbm1lbnQgdmFyaWFibGUgaXMgbm90IHNldCEiCiAgICBkb25lCgogICAgcmV0dXJuIDAKfQo=" |base64 -d >common.sh
        chmod +x buildah-*.sh
      volumeMounts:
        - mountPath: /scripts
          name: scripts-dir
      workingDir: /scripts
    - command:
        - /scripts/buildah-bud.sh
      computeResources: {}
      image: registry.redhat.io/rhel8/buildah@sha256:aac6629389db17e99894c5bee0da01d4c8065d11d8c6f6e1602f9484290baa70
      name: build
      securityContext:
        capabilities:
          add:
            - SETFCAP
      volumeMounts:
        - mountPath: /scripts
          name: scripts-dir
      workingDir: /workspace/source
  volumes:
    - emptyDir: {}
      name: scripts-dir
  workspaces:
    - description: |
        Container build context, like for instance a application source code
        followed by a `Dockerfile`.
      name: source
    - description: An optional workspace that allows providing a .docker/config.json file for Buildah to access the container registry. The file should be placed at the root of the Workspace with name config.json or .dockerconfigjson.
      name: dockerconfig
      optional: true
    - description: An optional workspace that allows providing the entitlement keys for Buildah to access subscription. The mounted workspace contains entitlement.pem and entitlement-key.pem.
      mountPath: /tmp/entitlement
      name: rhel-entitlement
      optional: true
```

Create the `buildah` Task using the file and the command:
```bash
oc apply -f task-buildah.yaml -n $NAMESPACE
```

Use the Tekton cli to inspect the created resources:

```bash
tkn task ls -n $NAMESPACE
```

The above command should list one Task as shown below:

```bash
NAME              AGE
buildah-build     4 seconds ago
java-test         46 minutes ago
```
        
Create an environment variable for location to push the image to be build:

```bash
export REGISTRY_SERVER=image-registry.openshift-image-registry.svc:5000
export IMAGE_URL=${REGISTRY_SERVER}/${NAMESPACE}/cloud-native-sample-app
echo IMAGE_URL=${IMAGE_URL}
```

Lets create a Task Run for `buildah` Task using the `tkn` CLI passing the inputs, outputs and service account:

```bash
tkn task start buildah-build --showlog \
-p IMAGE="${IMAGE_URL}" --use-param-defaults \
--workspace name=source,emptyDir=
```

The task will start and logs will start printing automatically:

```bash
Waiting for logs to be available...

[build] ---> Phase: Inspecting source workspace '/workspace/source' (PWD='/workspace/source')...
[build] ---> Phase: Asserting the dockerfile/containerfile '/workspace/source/./Dockerfile' exists...
[build] ERROR: Dockerfile not found at: '/workspace/source/./Dockerfile'

container step-build has failed  : [{"key":"StartedAt","value":"2025-02-06T01:57:32.982Z","type":3}]
```

Verify the status of the Task Run:

```bash
tkn tr ls -n $NAMESPACE
```
Output should look like this:

```bash
NAME                  STARTED          DURATION     STATUS
buildah-run-zbsrv      2 minutes ago   1 minute     Failed
```

!!! Warning

    As you can see in the output above, the above task has failed. Tasks should be modular, reusable, and ideally focus on achieving one... task. Tasks that have dependencies with one another need to be orchestrated together via a `Pipeline` as shown in the following section.

## Pipelines

### Pipeline Creation

Pipelines allows to start multiple Tasks, in parallel or in a [certain order](https://github.com/tektoncd/pipeline/blob/master/docs/pipelines.md#runafter)

Create the file **pipeline.yaml**, the Pipeline contains two Tasks:

```yaml
apiVersion: tekton.dev/v1
kind: Pipeline
metadata:
  name: app-build
spec:
  params:
    - name: source_repo
      type: string
    - name: image_registry
      type: string
  tasks:
    - name: java-test
      params:
        - name: url
          value: $(params.source_repo)
        - name: revision
          value: master
      taskRef:
        kind: Task
        name: java-test
      workspaces:
        - name: source
          workspace: source
    - name: buildah-build
      params:
        - name: IMAGE
          value: $(params.image_registry)
        - name: DOCKERFILE
          value: ./Dockerfile
        - name: CONTEXT
          value: .
        - name: STORAGE_DRIVER
          value: vfs
        - name: FORMAT
          value: oci
        - name: BUILD_EXTRA_ARGS
          value: ''
        - name: PUSH_EXTRA_ARGS
          value: ''
        - name: SKIP_PUSH
          value: 'false'
        - name: TLS_VERIFY
          value: 'true'
        - name: VERBOSE
          value: 'false'
      runAfter:
        - java-test
      taskRef:
        kind: Task
        name: buildah-build
      workspaces:
        - name: source
          workspace: source
  workspaces:
    - name: source
```

Pipeline defines a list of Tasks to execute in order, while also indicating if any outputs should be used as inputs of a following Task by using the from field and also indicating the order of executing (using the runAfter and from fields). The same variable substitution you used in Tasks is also available in a Pipeline.
Create the Pipeline using the command:

```bash
oc apply -f pipeline.yaml -n $NAMESPACE
```
Use the Tekton cli to inspect the created resources
```bash
tkn pipeline ls -n $NAMESPACE
```
The above command should list one Pipeline as shown below:

```bash
NAME        AGE              LAST RUN   STARTED   DURATION   STATUS
app-build   31 seconds ago   ---        ---       ---        ---
```

### PipelineRun

#### PipelineRun Creation

To execute the Tasks in the Pipeline, you must create a PipelineRun. Creation of a PipelineRun will trigger the creation of TaskRuns for each Task in your pipeline.

Create the file **pipelinerun.yaml**:

```yaml
apiVersion: tekton.dev/v1
kind: PipelineRun
metadata:
  generateName: app-build-run-
  labels:
    tekton.dev/pipeline: app-build
spec:
  params:
    - name: source_repo
      value: 'https://github.com/ibm-cloud-architecture/cloudnative_sample_app.git'
    - name: image_registry
      value: 'image-registry.openshift-image-registry.svc:5000/tekton-demo/cloud-native-sample-app:v1.0.0'
  pipelineRef:
    name: app-build
  taskRunTemplate:
    serviceAccountName: pipeline
  timeouts:
    pipeline: 1h0m0s
  workspaces:
    - name: source
      volumeClaimTemplate:
        metadata:
          creationTimestamp: null
        spec:
          accessModes:
            - ReadWriteOnce
          resources:
            requests:
              storage: 1Gi
          volumeMode: Filesystem
          storageClassName: ocs-storagecluster-cephfs
        status: {}
```

**serviceAccount** - it is always recommended to have a service account associated with PipelineRun, which can then be used to define fine grained roles.

Create the PipelineRun using the command:
```bash
oc create -f pipelinerun.yaml -n $NAMESPACE
```
We will use the Tekton cli to inspect the created resources
```bash
tkn pipelinerun ls -n $NAMESPACE
```
The above command should list one PipelineRun as shown below:

```bash
NAME                  STARTED          DURATION   STATUS
app-build-run-2cfbx   1 minutes ago    ---        Running
```

Wait for few minutes for your pipeline to complete all the tasks. If it is successful, you will see something like below.
```bash
tkn pipeline ls -n $NAMESPACE
```
```bash
NAME              AGE              LAST RUN                    STARTED         DURATION    STATUS
app-build   33 minutes ago         app-build-run-2cfbx         2 minutes ago   2 minutes   Succeeded
```

Run the pipeline ls command again:

```bash
tkn pipelinerun ls -n $NAMESPACE
```
```bash
NAME                  STARTED          DURATION   STATUS
app-build-run-2cfbx   8 minutes ago    3m41s      Succeeded
```

If it is successful, check that the imagestream has been created successfully:

```bash
oc get imagestreams -n $NAMESPACE
```

```bash
NAME                      IMAGE REPOSITORY     TAGS     UPDATED
cloud-native-sample-app   default-route...     v1.0.0   5 minutes ago
```

(Optional) Run the pipeline again using the `tkn` CLI
```bash
tkn pipeline start app-build --showlog \
        -s pipeline \
        -n $NAMESPACE \
        --last
```

## Deploy Application

Create a deployment
```bash
oc create deployment cloudnative --image=${IMAGE_URL}:v1.0.0 -n $NAMESPACE
```

Verify if the pods are running:
```bash
oc get pods -l app=cloudnative -n $NAMESPACE
```

```bash
NAME                           READY   STATUS    RESTARTS   AGE
cloudnative-77df47cfbc-962vx   1/1     Running   0          10s
```

Expose the deployment as a service
```bash
oc expose deployment cloudnative --port=9080 -n $NAMESPACE
```

Expose the service as a route
```bash
oc expose service cloudnative -n $NAMESPACE
```

Now access the compose the URL of the App using IP and NodePort
```bash
export APP_URL="$(oc get route cloudnative --template 'http://{{.spec.host}}')/greeting?name=World"
echo APP_URL=$APP_URL
```
```bash
http://cloudnative-tekton-demo.apps-crc.testing/greeting?name=World
```
Now access the app from terminal or browser
```bash
curl $APP_URL
```
Output should be
```json
{"id":4,"content":"Welcome to Cloudnative bootcamp !!! Hello, World :)"}
```

## Additional Challenge

!!! note "Optional Challenge"
    You have now successfully deployed your first application using Tekton! However, we deployed the application to the cluster manually. As an extra challenge, add application deployment to the `app-build`pipeline.