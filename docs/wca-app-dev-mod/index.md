# Installation guide for WCA on VSCode

This document gives infomration on installing software tools with IDE VSCode that are needed to run ***Watson Code Assistant For Enterprise Java*** **(WCA4EJ)**

Last updated: Nov 13th, 2024

## Environment setup 

### 1. Java installation

#### Install Java21 using this link:
- [Download Java for MacOS - Arm64](https://download.oracle.com/java/21/latest/jdk-21_macos-aarch64_bin.tar.gz)
- [Download Java for MacOS - x86](https://download.oracle.com/java/21/latest/jdk-21_macos-x64_bin.tar.gz)
- [Download Java for Windows](https://download.oracle.com/java/21/latest/jdk-21_windows-x64_bin.zip)

All the above are compressed files, you can extract them to any folder in your local.

Check if Java is installed properly:
```bash
java --version
```

After installing java, add java to `PATH` variable and set `JAVA_HOME` envitonment variable

=== "For Mac"
    Open .zshrc or .bash_profile
    
    ```bash
    nano ~/.zshrc
    ```
    
    Add the following lines
    
    ```bash
    export JAVA_HOME=/Library/Java/JavaVirtualMachines/<java version>/Contents/Home
    ```

    ```bash
    export PATH=$JAVA_HOME/bin:$PATH
    ```
    
    Save the file and exit (press CTRL + X, then Y, and hit Enter) and reload the shell configuration so the changes take effect.
    
    ```bash
    source ~/.zshrc
    ```
    
    Verify the JAVA_HOME with the following command:
    
    ```bash
    echo $JAVA_HOME
    ```

=== "For Windows"
    Open Environment variables using windows search bar (search for edit environment variables in the search bar)
    
    ![image](https://github.com/user-attachments/assets/d0099fe2-72c1-4594-8b5f-8075f2d6bced)

    Set JAVA_HOME variable using Environment variables (click on new if you do not have a JAVA_HOME set or click on edit to change the existing JAVA_HOME, and point it to the Java you installed in the earlier steps:
      
    ![image](https://github.com/user-attachments/assets/cbb009b7-159a-48d2-8bb6-c113968477b0)

    ```bash
    JAVA_HOME= C:\Program Files\Java\jdk-21
    ```
      
    Add Java to PATH using Environment variables:
  
    ![image](https://github.com/user-attachments/assets/8925e501-5db6-449b-9ad4-eef44ea253cf)
  
    ```bash
    %JAVA_HOME%\bin
    ```



### 2. Install Maven

=== "For Mac"
    
    Install maven using homebrew
    ```bash
    brew install maven
    ```
    
    Check if maven is installed properly:
    
    ```bash
    mvn --version
    ```

=== "For Windows"
    Visit the official Maven website: [Maven Download Page](https://maven.apache.org/download.cgi){target="_blank"}
    
    Under "Files", click on the binary zip archive link (e.g., apache-maven-x.x.x-bin.zip). 
    
    Extract the zip file to a location of your choice, e.g., C:\Apache\maven.
    
    Set MAVEN_HOME variable using Environment variables:
    ```bash
    MAVEN_HOME= <path-to-folder>\maven\apache-maven-3.9.9-bin\apache-maven-3.9.9
    ```
    
    Add Maven to PATH using Environment variables: 
    ```bash
    <path-to-folder>\maven\apache-maven-3.9.9-bin\apache-maven-3.9.9\bin
    ```


### 3. Install VSCode

[VSCode Official Website](https://code.visualstudio.com/download){target="_blank"} for installation

### 4. WCA4EJ API Key

XXXXXX Need instructions for getting from Gated env on TZ XXXXXXXX

### 5. Download WCA4EJ Extension

Download watsonx Code Assistant extension from Marketplace

![alt text](./images/wca-marketplace-image.png){width=50%}

Click Install.

Then you will see the product page.

![alt text](./images/wca-product-image.png)

### 6. Log in to the WCA


#### After installing the extension from **Step 5**, login into the extension via following steps:

- Login with WCA4EJ API Key at the bottom left corner of VSCode. After successfully signed in, the number indicator should be gone.

![screenshot](./images/VSC_WCA4J_Sign_in.png)

- If you encoutner issue during autherization that says **"administrator needs to associate you with a deployment space"**, please reach out to IBMers to setup deployment space again for your API Key. 

![screenshot](./images/VSC_WCA4J_Sign_in_error_1.png)


### 7. Installing Liberty Tools and Java Extension

Install the Liberty Tools and extension Pack for Java extensions from VSCode marketplace as shown below.

![screenshot](./images/VSC_LibertyTools.png)

![screenshot](./images/VSCode-pack-for-java.png)

### 8. Start Using WCA4EJ

You can check by navigating to the **watsonx Code Assistant** tab if your API Key is setup correctly by opening the chat window of WCA4EJ and chat with the model.

![screenshot](./images/VSC_chat_with_model.png)
