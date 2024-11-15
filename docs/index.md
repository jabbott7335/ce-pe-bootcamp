---
Title: Welcome
hide:
    - toc
---


# Welcome to the Client Engineering Platform Engineer Bootcamp

Welcome Platform Engineers! This one-week course is designed to gradually introduce students to Hybrid Cloud and Cloud Native concepts delivered common cloud platforms and technology.  The bootcamp is ideal for guiding students through a structured learning experience. The course material is organized into several topics, each featuring a set of tasks that students are required to complete. As you work through this program, you'll be encouraged to think critically, learn proactively, and leverage the provided resources in a manner similar to how you would approach a real-world Client Engineering Pilot.

## Guiding Principles

In this program, students will complete individual assignments, while also being strongly encouraged to collaborate with peers to tackle complex challenges and share knowledge.  Student progress will be assessed through a combination of playback reviews and authorized access to course systems, ensuring that learning objectives are being met.  Instructors will offer expert guidance and support to help students navigate challenges, while also empowering them to find their own solutions and develop problem-solving skills.

## Required skills

This activities contained here require you to be proficient in working from the command line with a linux shell (Bash, Zsh, etc.) Below is a partial list of activities you should be able to perform. 

- Copy, move, and rename files
- Understand linux file permissions
- Edit text files (vi, vim, emacs, etc)
- Edit environment variables ($PATH)

Here is a course for learning (or brushing up) on working from the linux command line [Linux Command Line Basics](https://www.udacity.com/course/linux-command-line-basics--ud595){target="_blank"}

## Prerequisite Class

To participate in this course, students must successfully complete the prerequisite course, DO322, by passing its assessment test. This completion is a mandatory requirement for enrolling in and fully engaging with our program.

!!! Note "No Red Hat Learning Subscription is Required"
    No Red Hat Learning Subscription is needed to access the course, access to the Red Hat Partner Training Portal is required. [Follow these instructions on Seismic](https://ibm.seismic.com/Link/Content/DCWfXWHpFXTRgGhWRDhTQpTbPDp8){target="_blank"} to gain access to Red Hat Connect (which unlocks access to the Red Hat Partner Training Portal).

Red Hat OpenShift Installation Lab (DO322) (16 hours):  Installing OpenShift on a cloud, virtual, or physical infrastructure. Red Hat OpenShift Installation Lab (DO322) teaches essential skills for installing an OpenShift cluster in a range of environments, from proof of concept to production, and how to identify customizations that may be required because of the underlying cloud, virtual, or physical infrastructure. This course is based on Red Hat OpenShift Container Platform 4.6.

Completion Criteria:  Complete and provide evidence of passing the [DO322 Assessment on the Red Hat Partner Training Portal](https://training-lms.redhat.com/sso/saml/auth/rhopen?RelayState=deeplinkoffering%3D44800093){target="_blank"}

## Workstation Setup

=== "Openshift (MacOS/Linux)"

    ## Create accounts

    You'll need these accounts to use the Developer Tools environment.

    - [GitHub account](http://github.com){target="_blank"} (public, not enterprise): Create one if you do not have one aleady. If you have not logged in for a while, make sure your login is working.

    - [IBM Cloud Account](https://cloud.ibm.com){target="_blank"}: Create one if needed, make sure you can log in. 

    - [O'Reilly Account](https://learning.oreilly.com/home/){target="_blank"}: The account is free and easy to create.

    - [RedHat Account](https://www.redhat.com/en){target="_blank"}: Needed for CodeReady Containers.

    ## Run System Check Script

    Run the following command in your terminal to check which tools need to be installed.

    Using `wget`:

    ```
    wget -O - https://cloudbootcamp.dev/scripts/system-check.sh | sh
    ```

    Using `curl`:

    ```
    curl -s https://cloudbootcamp.dev/scripts/system-check.sh | sh
    ```

    After the script is run, make sure to install any missing tools.

    ## Install CLIs and tools

    The following is a list of desktop tools required to help with installation and development.

    - [Git Client](https://git-scm.com/){target="_blank"}: Needs to be installed in your development operating system, it comes as standard for Mac OS

    - [IBM Cloud CLI](https://cloud.ibm.com/docs/cli?topic=cloud-cli-getting-started){target="_blank"}: Required for management of IBM Cloud Account and management of your managed IBM Kubernetes and Red Hat OpenShift clusters
        - Don't install just the [IBM Cloud CLI](https://cloud.ibm.com/docs/cli?topic=cloud-cli-install-ibmcloud-cli){target="_blank"}, install the [IBM Cloud CLI and Developer Tools](https://cloud.ibm.com/docs/cli?topic=cloud-cli-getting-started#step1-install-idt){target="_blank"}
        ```
        curl -sL https://ibm.biz/idt-installer | bash
        ```

    !!! Note
        If you log in to the web UI using SSO, you'll need to [create an API key](https://cloud.ibm.com/docs/iam?topic=iam-federated_id){target="_blank"} for logging into the CLI. 

    - [Podman Desktop](https://podman-desktop.io/){target="_blank"}: Required for building and running container images.
        - Installed and running on your local machine

    - [Tekton CLI](https://github.com/tektoncd/cli){target="_blank"}: Used to help control Tekton
    pipelines from the command line.
        ```
            brew tap tektoncd/tools
            brew install tektoncd/tools/tektoncd-cli
        ```

    - [Visual Studio Code](https://code.visualstudio.com/download){target="_blank"}: A popular code editor
        - You will be required to edit some files, having a good quality editor is always best practice
        - Enabling [launching VSCode from a terminal](https://code.visualstudio.com/docs/setup/mac#_launching-from-the-command-line){target="_blank"}

    - [JDK 11](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html){target="_blank"}: _Optional_ installed on your local machine
        - Used for SpringBoot content


=== "Openshift (Windows)"

    ## Create accounts

    You'll need these accounts to use the Developer Tools environment.

    - [GitHub account](http://github.com){target="_blank"} (public, not enterprise): Create one if you do not have one aleady. If you have not logged in for a while, make sure your login is working.

    - [IBM Cloud Account](https://cloud.ibm.com){target="_blank"}: Create one if needed, make sure you can log in. 

    - [O'Reilly Account](https://learning.oreilly.com/home/){target="_blank"}: The account is free and easy to create.

    - [RedHat Account](https://www.redhat.com/en){target="_blank"}: Needed for CodeReady Containers.

    ## Cloud Native VM

    Use the [Cloud Native VM](https://github.com/csantanapr/vagrant-cloud-native#install){target="_blank"} it comes pre-installed with kubernetes and all cloud native CLIs.

    Is highly recommended for Windows users to use this VM.

    ## Install CLIs and tools

    The following is a list of desktop tools required to help with installation and development.

    - [Git Client](https://git-scm.com/){target="_blank"}: Needs to be installed in your development operating system, it comes as standard for Mac OS

    - [IBM Cloud CLI](https://cloud.ibm.com/docs/cli?topic=cloud-cli-getting-started){target="_blank"}: Required for management of IBM Cloud Account and management of your managed IBM Kubernetes and Red Hat OpenShift clusters
        - Don't install just the [IBM Cloud CLI](https://cloud.ibm.com/docs/cli?topic=cloud-cli-install-ibmcloud-cli){target="_blank"}, install the [IBM Cloud CLI and Developer Tools](https://cloud.ibm.com/docs/cli?topic=cloud-cli-getting-started#step1-install-idt){target="_blank"}
        ```
        curl -sL https://ibm.biz/idt-installer | bash
        ```

    !!! Note 
        If you log in to the web UI using SSO, you'll need to [create an API key](https://cloud.ibm.com/docs/iam?topic=iam-federated_id){target="_blank"} for logging into the CLI. 

    - [Podman Desktop](https://podman-desktop.io/){target="_blank"}: Required for building and running container images.
        - Installed and running on your local machine

    - [Tekton CLI](https://github.com/tektoncd/cli){target="_blank"}: Used to help control Tekton pipelines from the command line.


    - [Visual Studio Code](https://code.visualstudio.com/download){target="_blank"}: A popular code editor
        - You will be required to edit some files, having a good quality editor is always best practice
        - Enabling [launching VSCode from a terminal](https://code.visualstudio.com/docs/setup/mac#_launching-from-the-command-line){target="_blank"}

    - [JDK 11](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html){target="_blank"}: _Optional_ installed on your local machine
        - Used for SpringBoot content

    - [OpenShift CodeReady Containers (CRC)](https://code-ready.github.io/crc/#installing-codeready-containers_gsg){target="_blank"}

    <InlineNotification kind="warning">

    **Warning:** Make sure you have Cisco VPN turned off when using CRC.

    </InlineNotification>

=== "Kubernetes (MacOS/Linux)"
    ## Create accounts

    You'll need these accounts to use the Developer Tools environment.

    - [GitHub account](http://github.com){target="_blank"} (public, not enterprise): Create one if you do not have one aleady. If you have not logged in for a while, make sure your login is working.

    - [IBM Cloud Account](https://cloud.ibm.com){target="_blank"}: Create one if needed, make sure you can log in. 

    - [O'Reilly Account](https://learning.oreilly.com/home/){target="_blank"}: The account is free and easy to create.


    ## Run System Check Script

    Run the following command in your terminal to check which tools need to be installed.

    Using wget:
    ```
    wget -O - https://cloudbootcamp.dev/scripts/system-check.sh | sh
    ```

    Using curl:
    ```
    curl -s https://cloudbootcamp.dev/scripts/system-check.sh | sh
    ```

    After the script is run, make sure to install any missing tools.

    ## Install CLIs and tools

    The following is a list of desktop tools required to help with installation and development.

    - [Git Client](https://git-scm.com/){target="_blank"}: Needs to be installed in your development operating system, it comes as standard for Mac OS

    - [IBM Cloud CLI](https://cloud.ibm.com/docs/cli?topic=cloud-cli-getting-started){target="_blank"}: Required for management of IBM Cloud Account and management of your managed IBM Kubernetes and Red Hat OpenShift clusters
        - Don't install just the [IBM Cloud CLI](https://cloud.ibm.com/docs/cli?topic=cloud-cli-install-ibmcloud-cli){target="_blank"}, install the [IBM Cloud CLI and Developer Tools](https://cloud.ibm.com/docs/cli?topic=cloud-cli-getting-started#step1-install-idt){target="_blank"}
        ```
        curl -sL https://ibm.biz/idt-installer | bash
        ```

     !!! Note
        If you log in to the web UI using SSO, you'll need to [create an API key](https://cloud.ibm.com/docs/iam?topic=iam-federated_id){target="_blank"} for logging into the CLI. 
    

    - [Podman Desktop](https://podman-desktop.io/){target="_blank"}: Required for building and running container images.
        - Installed and running on your local machine

    - [Tekton CLI](https://github.com/tektoncd/cli){target="_blank"}: Used to help control Tekton
    pipelines from the command line.
        ```
            brew tap tektoncd/tools
            brew install tektoncd/tools/tektoncd-cli
        ```

    - [Visual Studio Code](https://code.visualstudio.com/download){target="_blank"}: A popular code editor
        - You will be required to edit some files, having a good quality editor is always best practice
        - Enabling [launching VSCode from a terminal](https://code.visualstudio.com/docs/setup/mac#_launching-from-the-command-line)

    - [JDK 11](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html){target="_blank"}: _Optional_ installed on your local machine
        - Used for SpringBoot content

    - [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/){target="_blank"}: Follow the instructions for your Operating System.

    <InlineNotification kind="warning">

    **Warning:** Make sure you have Cisco VPN turned off when using minikube.

    </InlineNotification>

=== "Kubernetes (Windows)"

    ## Create accounts

    You'll need these accounts to use the Developer Tools environment.

    - [GitHub account](http://github.com){target="_blank"} (public, not enterprise): Create one if you do not have one aleady. If you have not logged in for a while, make sure your login is working.

    - [IBM Cloud Account](https://cloud.ibm.com){target="_blank"}: Create one if needed, make sure you can log in. 

    - [O'Reilly Account](https://learning.oreilly.com/home/){target="_blank"}: The account is free and easy to create.

    ## Cloud Native VM

    Use the [Cloud Native VM](https://github.com/csantanapr/vagrant-cloud-native#install){target="_blank"} it comes pre-installed with kubernetes and all cloud native CLIs.

    Is highly recommended for Windows users to use this VM.

    ## Install CLIs and tools

    The following is a list of desktop tools required to help with installation and development.

    - [Git Client](https://git-scm.com/){target="_blank"}: Needs to be installed in your development operating system, it comes as standard for Mac OS

    - [IBM Cloud CLI](https://cloud.ibm.com/docs/cli?topic=cloud-cli-getting-started){target="_blank"}: Required for management of IBM Cloud Account and management of your managed IBM Kubernetes and Red Hat OpenShift clusters
        - Don't install just the [IBM Cloud CLI](https://cloud.ibm.com/docs/cli?topic=cloud-cli-install-ibmcloud-cli){target="_blank"}, install the [IBM Cloud CLI and Developer Tools](https://cloud.ibm.com/docs/cli?topic=cloud-cli-getting-started#step1-install-idt){target="_blank"}
        ```
        curl -sL https://ibm.biz/idt-installer | bash
        ```

    !!! Note
        If you log in to the web UI using SSO, you'll need to [create an API key](https://cloud.ibm.com/docs/iam?topic=iam-federated_id){target="_blank"} for logging into the CLI. 


    - [Podman Desktop](https://podman-desktop.io/){target="_blank"}: Required for building and running container images.
        - Installed and running on your local machine

    - [Tekton CLI](https://github.com/tektoncd/cli){target="_blank"}: Used to help control Tekton
    pipelines from the command line.
        ```
            brew tap tektoncd/tools
            brew install tektoncd/tools/tektoncd-cli
        ```

    - [Visual Studio Code](https://code.visualstudio.com/download){target="_blank"}: A popular code editor
        - You will be required to edit some files, having a good quality editor is always best practice
        - Enabling [launching VSCode from a terminal](https://code.visualstudio.com/docs/setup/mac#_launching-from-the-command-line){target="_blank"}

    - [JDK 11](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html){target="_blank"}: _Optional_ installed on your local machine
        - Used for SpringBoot content

    - [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/){target="_blank"}: Follow the instructions for your Operating System.

    <InlineNotification kind="warning">

    **Warning:** Make sure you have Cisco VPN turned off when using minikube.

    </InlineNotification>

## Environment Setup 

=== "MiniKube"

    - Verify your cluster has 4GB+ memory, and kubernetes 1.16+
        ```
        minikube config view
        ```
    - Verify your `vm-driver` is set for `hyperkit`
        ```
        minikube config set vm-driver hyperkit
        ```
    - In case memory is not set, or need to increase set the memory and recreate the VM
        ```
        minikube config set memory 4096
        minikube config set kubernetes-version v1.16.6
        minikube delete
        minikube start
        ```
    - Kubernetes should be v1.15+
        ```
        kubectl version
        ```

=== "CRC (MiniShift)"

    Make sure CRC is installed. Check out the [CRC Page](https://access.redhat.com/documentation/en-us/red_hat_codeready_containers/1.0/html/getting_started_guide/getting-started-with-codeready-containers_gsg){target="_blank"} 

    ** Setup CRC **
    ```
    crc setup
    ```
    ** Start CRC **
    ```
    crc start
    ```
=== "IKS"

    - Login to [IBM Cloud](https://cloud.ibm.com){target="_blank"} with your IBM ID.

    - Click "Create Resource" and search for "kubernetes service".

    - Select the tile for "Kubernetes Service" and do the following:
    - Select the "Free Cluster" plan.
    - Name your cluster.
    - Select "Create" at the bottom right of the screen.

    - Once the Cluster is provisioned, Click on the "Connect via CLI" in the top right corner.

    - Follow the instructions to connect and you are set to go.

=== "OpenShift on IBM Cloud (4.x)"

    - In this approach you share an OpenShift cluster on IBM Cloud with other bootcamp attendees.

    - Considering 10-15 attendees we recommend a cluster with 3 worker nodes (each 8 vCPUs + 32GB RAM - b3c.8x32).

    - Ask your IBM cloud account owner to provide access to an OpenShift cluster.

    - In addition to the IBM Cloud CLI also install the [OpenShift Origin CLI](https://cloud.ibm.com/docs/openshift?topic=openshift-openshift-cli#cli_oc){target="_blank"} to be able to execute all commands.

    - Open your OpenShift web console from within your IBM cloud account, select your profile and choose "copy login command" to retrieve an access token for the login.

    - Login with your OpenShift Origin CLI.
        ```
        oc login --token=<token> --server=<server-url>:<server-port>
        ```

    - Create your own project / namespace in OpenShift that you will leverage across all labs.
        ```
        oc new-project <dev-your_initials>
        ```

    - Validate in the OpenShift web console that your project has been created (Administrator view -> Home -> Projects)


!!! Note "Get a Jump on CLoud Native Concepts"

    Once Setup is complete, you can get an early jump on some of the Cloud Native topics we will hit during the bootcamp:

    - About [Cloud Native](./cn/cloud-native.md){target="_blank"}
    - Cloud Native [App Development](./cn/app-dev.md)
