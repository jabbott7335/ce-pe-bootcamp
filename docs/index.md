---
hide:
    - toc
---

# Welcome to the Client Engineering Platform Engineer (PE) Bootcamp

**Welcome Platform Engineers!**

As we embark on this 5-day bootcamp together, We're excited to introduce you to the fundamentals of Hybrid Cloud and Cloud Native concepts on popular cloud platforms. This structured learning experience is designed to cater to your varying levels of expertise and experience.

Throughout the course, you'll delve into a series of topics, each accompanied by practical tasks that will challenge your skills and encourage hands-on learning. Our goal is not only to impart theoretical knowledge but also to foster critical thinking, proactivity, and resource utilization – skills honed through real-world Client Engineering Pilots.

As we navigate this journey together, you'll have access to a wealth of resources at your fingertips. You'll be encouraged to explore, experiment, and learn from your mistakes, mirroring the fast-paced and dynamic environment of a real-world Client Engineering Pilot.

By the end of this bootcamp, you'll possess a solid foundation in Hybrid Cloud and Cloud Native concepts, empowering you to tackle complex problems and drive innovative solutions. Let's get started on this exciting journey!

## Guiding Principles

As we begin this journey together, it's essential to establish a framework that fosters collaboration, innovation, and individual growth. In this program, students will embark on a path of self-directed learning, supported by regular assignments and opportunities for peer interaction.

Through these collaborative challenges, students will not only work together to tackle complex problems but also share knowledge, expertise, and best practices. This approach encourages active learning, fosters a sense of community, and helps students develop the skills needed to navigate real-world Client Engineering Pilots.

As we progress through the program, student progress will be evaluated through a combination of playback reviews – providing a detailed record of their work – and authorized access to course systems. This dual assessment method ensures that students meet learning objectives while also receiving timely feedback on their performance.

To support students in overcoming challenges and achieving success, instructors will offer expert guidance and mentorship. This personalized support empowers students to not only receive technical expertise but also develop essential problem-solving skills – a critical component of any effective engineering practice.

By embracing this guiding principle of collaboration, we create an environment that fosters growth, innovation, and self-directed learning. Let's work together to help you achieve your full potential as a Platform Engineer.

## Required skills

To successfully navigate this bootcamp, it's essential to have a solid foundation in working from the command line using a Linux shell. Specifically, you should be proficient in the following tasks:

- Managing files: copying, moving, and renaming files with ease
- Understanding Linux file permissions: navigating the complexities of ownership, group membership, and access control
- Text editing: efficiently editing text files using popular editors like vi, vim, emacs, or others
- Environment variables: modifying and managing your $PATH settings to streamline your workflow

If you're looking to refresh your knowledge or learn from scratch, we recommend supplementing this bootcamp with the [Linux Command Line Basics](https://ibm-learning.udemy.com/course/linux-command-line-volume1/){target="_blank"} course. This comprehensive resource will provide you with a solid foundation in Linux command line fundamentals.

## Prerequisite Class Requirement

Before proceeding to this course, it's essential to confirm that you have met the prerequisite class requirement. To do so, students must successfully complete [Red Hat OpenShift Installation Lab (DO322)](https://training-lms.redhat.com/sso/saml/auth/rhopen?RelayState=deeplinkoffering%3D44800093){target="_blank"} by passing its assessment test.

This is a mandatory requirement for enrolling in our program and fully engaging with the material. By fulfilling this prerequisite, you'll ensure that you have a solid foundation of knowledge and skills required for success in our Platform Engineering Bootcamp.

Please note that completing the prerequisite class is not only a necessary step but also a critical component of your learning journey. We're committed to supporting your growth and success, and we want to ensure that you're fully prepared to take on the challenges and opportunities presented in this course.

**Completion Criteria**:  Complete and provide evidence of passing the [DO322 Assessment](https://training-lms.redhat.com/sso/saml/auth/rhopen?RelayState=deeplinkoffering%3D44800093){target="_blank"} on the Red Hat Partner Training Portal.

!!! Note "No Red Hat Learning Subscription is Required"
    No Red Hat Learning Subscription is needed to access the course, access to the Red Hat Partner Training Portal is required. [Follow these instructions on Seismic](https://ibm.seismic.com/Link/Content/DCWfXWHpFXTRgGhWRDhTQpTbPDp8){target="_blank"} to gain access to Red Hat Connect (which unlocks access to the Red Hat Partner Training Portal).

## Workstation Setup

=== "Openshift (MacOS/Linux)"

    ## Create accounts

    You'll need these accounts to use the Developer Tools environment.

    - [GitHub account](http://github.com){target="_blank"} (public, not enterprise): Create one if you do not have one already. If you have not logged in for a while, make sure your login is working.
    
    - [Red Hat Account](https://w3.ibm.com/w3publisher/redhat/nfr){target="_blank"}: Request a Red Hat Partner Subscription. Ensure to follow the instructions closely. 

    <!-- 
    - [O'Reilly Account](https://learning.oreilly.com/home/){target="_blank"}: The account is free and easy to create.
    --->

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

    !!! Note
        Ignore the requirement for the Docker CLI, IBM Software Policies prohibit the use of the Docker CLI and encourages the use of Podman CLI instead.

    ## Install CLIs and tools

    The following is a list of desktop tools required to help with installation and development.

    - [Git Client](https://git-scm.com/downloads){target="_blank"}: Needs to be installed in your development operating system, it comes as standard for Mac OS

    - [IBM Cloud CLI](https://cloud.ibm.com/docs/cli?topic=cloud-cli-getting-started){target="_blank"}: Required for management of IBM Cloud

    - [Podman Desktop](https://podman-desktop.io/downloads){target="_blank"}: Required for building and running container images.
        - Installed and running on your local machine

    - [Visual Studio Code](https://code.visualstudio.com/download){target="_blank"}: A popular code editor
        - You will be required to edit some files, having a good quality editor is always best practice
        - Enabling [launching VSCode from a terminal](https://code.visualstudio.com/docs/setup/mac#_launching-from-the-command-line){target="_blank"}

    - [JDK 21](https://developer.ibm.com/languages/java/semeru-runtimes/downloads/){target="_blank"}: *Optional* installed on your local machine
        - Used for SpringBoot content

=== "Openshift (Windows)"

    ## Create accounts

    You'll need these accounts to use the Developer Tools environment.

    - [GitHub account](http://github.com){target="_blank"} (public, not enterprise): Create one if you do not have one already. If you have not logged in for a while, make sure your login is working.
    
    - [Red Hat Account](https://w3.ibm.com/w3publisher/redhat/nfr){target="_blank"}: Request a Red Hat Partner Subscription. Ensure to follow the instructions closely. 

    ## Cloud Native VM

    Use the [Cloud Native VM](https://github.com/csantanapr/vagrant-cloud-native#install){target="_blank"} it comes pre-installed with kubernetes and all cloud native CLIs.

    Is highly recommended for Windows users to use this VM.

    ## Install CLIs and tools

    The following is a list of desktop tools required to help with installation and development.

    - [Git Client](https://git-scm.com/){target="_blank"}: Needs to be installed in your development operating system

    - [IBM Cloud CLI](https://cloud.ibm.com/docs/cli?topic=cloud-cli-getting-started){target="_blank"}: Required for management of IBM Cloud

    - [Podman Desktop](https://podman-desktop.io/){target="_blank"}: Required for building and running container images.
        - Installed and running on your local machine

    - [Visual Studio Code](https://code.visualstudio.com/download){target="_blank"}: A popular code editor
        - You will be required to edit some files, having a good quality editor is always best practice
        - Enabling [launching VSCode from a terminal](https://code.visualstudio.com/docs/setup/mac#_launching-from-the-command-line){target="_blank"}

    - [JDK 11](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html){target="_blank"}: *Optional* installed on your local machine
        - Used for SpringBoot content

    <InlineNotification kind="warning">

    **Warning:** Make sure you have Cisco VPN turned off when using CRC.

    </InlineNotification>

=== "Kubernetes (MacOS/Linux)"
    ## Create accounts

    You'll need these accounts to use the Developer Tools environment.

    - [GitHub account](http://github.com){target="_blank"} (public, not enterprise): Create one if you do not have one already. If you have not logged in for a while, make sure your login is working.
    
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

    !!! Note
        Ignore the requirement for the Docker CLI, IBM Software Policies prohibit the use of the Docker CLI and encourages the use of Podman CLI instead.

    ## Install CLIs and tools

    The following is a list of desktop tools required to help with installation and development.

    - [Git Client](https://git-scm.com/){target="_blank"}: Needs to be installed in your development operating system, it comes as standard for Mac OS

    - [IBM Cloud CLI](https://cloud.ibm.com/docs/cli?topic=cloud-cli-getting-started){target="_blank"}: Required for management of IBM Cloud.

    - [Podman Desktop](https://podman-desktop.io/){target="_blank"}: Required for building and running container images.
        - Installed and running on your local machine

    - [Visual Studio Code](https://code.visualstudio.com/download){target="_blank"}: A popular code editor
        - You will be required to edit some files, having a good quality editor is always best practice
        - Enabling [launching VSCode from a terminal](https://code.visualstudio.com/docs/setup/mac#_launching-from-the-command-line)

    - [JDK 11](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html){target="_blank"}: *Optional* installed on your local machine
        - Used for SpringBoot content

=== "Kubernetes (Windows)"

    ## Create accounts

    You'll need these accounts to use the Developer Tools environment.

    - [GitHub account](http://github.com){target="_blank"} (public, not enterprise): Create one if you do not have one already. If you have not logged in for a while, make sure your login is working.
    
    ## Cloud Native VM

    Use the [Cloud Native VM](https://github.com/csantanapr/vagrant-cloud-native#install){target="_blank"} it comes pre-installed with kubernetes and all cloud native CLIs.

    Is highly recommended for Windows users to use this VM.

    ## Install CLIs and tools

    The following is a list of desktop tools required to help with installation and development.

    - [Git Client](https://git-scm.com/){target="_blank"}: Needs to be installed in your development operating system, it comes as standard for Mac OS

    - [IBM Cloud CLI](https://cloud.ibm.com/docs/cli?topic=cloud-cli-getting-started){target="_blank"}: Required for management of IBM Cloud 

    - [Podman Desktop](https://podman-desktop.io/){target="_blank"}: Required for building and running container images.
        - Installed and running on your local machine

    - [Visual Studio Code](https://code.visualstudio.com/download){target="_blank"}: A popular code editor
        - You will be required to edit some files, having a good quality editor is always best practice
        - Enabling [launching VSCode from a terminal](https://code.visualstudio.com/docs/setup/mac#_launching-from-the-command-line){target="_blank"}

    - [JDK 11](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html){target="_blank"}: *Optional* installed on your local machine
        - Used for SpringBoot content

## Environment Setup

=== "OpenShift Local"

    Ensure OpenShift Local is installed. Check out the  [OpenShift Local](https://console.redhat.com/openshift/create/local){target="_blank"} Page.

    - Setup OpenShift Local

        ```bash
        crc setup
        ```
    
    - Start OpenShift Local

        ```bash
        crc start
        ```

    <InlineNotification kind="warning">
    **Warning:** Make sure you have Cisco VPN turned off when using OpenShift Local.
    </InlineNotification>

=== "MiniKube"

    Ensure Minikube is installed. Check out the [Minikube](https://minikube.sigs.k8s.io/docs/start/?arch=%2Fmacos%2Fx86-64%2Fstable%2Fhomebrew){target="_blank"} Page.

    - Verify your `driver` is set for `podman`     

        ```bash
        minikube config set driver podman
        ```

    - Start minikube

        ```bash
        minikube start --driver=podman --container-runtime=cri-o
        ``` 

    - In case memory is not set, or need to increase set the memory and recreate the VM

        ```bash
        minikube config set memory 4096
        minikube delete
        minikube start --driver=podman --container-runtime=cri-o
        ```

    - Kubernetes should be v1.31+

        ```bash
        kubectl version
        ```
    
    <InlineNotification kind="warning">
    **Warning:** Make sure you have Cisco VPN turned off when using Minikube.
    </InlineNotification>

!!! Note "Get a Jump on Cloud Native Concepts"

    Once Setup is complete, you can get an early jump on some of the Cloud Native topics we will hit during the bootcamp:

    - About [Cloud Native](./cn/cloud-native.md){target="_blank"}
    - Cloud Native [App Development](./cn/app-dev.md){target="_blank"}
