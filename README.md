# Rapidly deploy and configure your Java applications with WebSphere Liberty and OpenShift Container Platform

![banner](./images/media/image1.jpeg) 

**Last updated:** November 2026

**Status: ** Ready for usage

**Duration:** 60 minutes

Need support? Contact **Lars Besselmann, Sebastian Porro**

## Overview
This lab provides fundamental hands-on experience with modernizing existing Java applications to WebSphere Liberty, deployed into a container platform, such as Red Hat OpenShift. 

The focus of this lab is on the practical aspects of application deployment and configurations and not the analysis of the Application Modernization Accelerator results. 
*Other labs cover Application Modernization Accelerator in detail.*

Upon completion of this lab, you will have gained skills to download and use the `Application Modernization Accelerator migration plan` to deploy and configure your application in the following scenarios: 

1.  **to a locally running WebSphere Liberty** - useful for deploying to Liberty in a VM

2.  **to a container image** – useful for deploying to Kubernetes

3.  **using the Liberty Operator** – useful for deploying to OpenShift


**IBM Application Modernization Accelerator** (Application Modernization Accelerator) is an application modernization tool that helps you quickly evaluate on-premises Java EE applications for deployment to the cloud. 

The Application Modernization Accelerator tool provides the following value:

  - Identifies the Java EE programming models in the app

  - Determines the complexity of replatforming these apps by listing a high-level inventory of the content and structure of each app

  - Highlights Java EE programming model and WebSphere API differences between the WebSphere runtime profile types 

  - Identifies Java EE specification implementation differences that might affect the app

  - Generates accelerators for deploying the application to Liberty and containers in a target environment

Additionally, the tool provides a recommendation for the right-fit IBM WebSphere Application Server edition and offers advice, best practices, and potential solutions to assess the ease of moving apps to Liberty or newer versions of WebSphere traditional. It accelerates application migrating to cloud process, minimizes errors and risks and reduces time to market.


## Objective

The objectives of this lab are to:

  - Learn how to download the application migration plan and use it to deploy an application to WebSphere Liberty running locally

  - Learn how to fully populate the migration plan placeholders and build the application into a container image

  - Learn the role of Kustomize when deploying the migration plan

  - Learn how to deploy your application to OpenShift with a single command

  - Learn how to create multiple configurations for the application and deploy them to OpenShift


## Prerequisites

The following prerequisites must be completed prior to beginning this lab:

  - Familiarity with basic Linux commands

  - Have internet access

  - Access to the TechZone lab environment

## Accessing the lab environment

If you are doing this lab as part of an instructor led workshop (virtual or face to face), an environment has already been provisioned for you. The instructor will provide the details for accessing the lab environment.

Otherwise, you will need to reserve an environment for the lab. You can obtain one here. Follow the on-screen instructions for the “**Reserve now**” option.

[https://techzone.ibm.com/collection/liberty-getting-started-labs-demos/journey-liberty-deployment-on-open-shift](https://techzone.ibm.com/collection/liberty-getting-started-labs-demos/journey-liberty-deployment-on-open-shift)

The lab environment contains of a Desktop machine and several other VMs spinning up an OpenShift cluster five Linux VMs and an OpenShift cluster.



## Lab Tasks

In this lab, you will use the Application Modernization Accelerator `migration plan` to build a container image and deploy an application to a local container for testing.

To simplify the lab and to allow you to focus on the migration plan, certain software and artifacts have already been put in place for you. These are as follows:

- **Application Modernization Accelerator** has been installed

- **podman** (for creating and running images) has been installed

- **oc** (OpenShift command Line tool for running OCP commands) has been installed

- **IBM Semeru Java SDKs** 

- **git, maven and other tools**

- The **PlantsByWebSphere Sample Application**, built as an Enterprise Archive file (EAR) is provided via git repository.

<!--


# Part 0: Prepare environment

**Disable docker**
    sudo mv /bin/docker /bin/docker.sav


**Move/remove files to backup**

    sudo rm -rf ~/Student
    rm -rf ~/binary-scanner
    rm -rf ~/IBM
    rm -rf ~/transformation-advisor-local-3.8.1
    sudo rm -rf /home/techzone
    /tmp/post_deploy_repo/scripts/Firefox_setBookmarks.sh

**Set the OCP environment variables**

    cat /usr/local/share/applications/OpenShiftConsole.desktop | grep http > OCP_URL.txt
    OCP_CONSOLE_URL=$(sed 's/Exec=\/usr\/bin\/xdg-open //g' OCP_URL.txt)
    sudo echo "export OCP_CONSOLE_URL=$OCP_CONSOLE_URL" >> ~/.bashrc
    OCP_BASE_URL="${OCP_CONSOLE_URL:34}"
    sudo echo "export OCP_BASE_URL=$OCP_BASE_URL" >> ~/.bashrc

-->


1. Create Student directory and clone the project repository

       mkdir -p ~/Student/temp
       git clone https://github.com/LarsBesselmann/LibertyGettingStarted-2026-OCP-Lab ~/Student/temp
       mv ~/Student/temp/lab_sources/* ~/Student
       rm -rf ~/Student/temp
       ls -lrt ~/Student
    

2. Create podman container with the PlantsByWebSphere database pbwdb

       cd ~/Student

       sudo podman build -t plantsdb-db2 ./plantsdb-db2

       sudo podman run --privileged=true -d \
        --name plantsdb-db2 \
        -p 50000:50000 \
        -e LICENSE=accept \
        -e DB2INST1_PASSWORD=db2password \
        -e DBNAME=PLANTSDB \
        -v plantsdb-data:/database \
        plantsdb-db2

        netstat -an | grep 50000


# Part 1: Application Assessment    

## 1.1 Launch Application Modernization Accelerator (local)

Application Modernization Accelerator (AMA) provides a **`migration plan`** for each application being assessed for modernization. The migration plan includes a **`migration plan`** of generated artifacts that accelerate the deployment of applications to Liberty in containers and Kubernetes / OpenShift.

The migration plan includes diverse artifacts, depending on the needs of the application to accelerate the build and deployment of an application Container image into an OpenShift container platform.

The Application Modernization Accelerator is installed locally on the **Workstation** VM. AMA might have a higher version than shown in the screenshots. Also the host name might be slightly different.

1. Launch the Application Modernization Accelerator tool using the steps below.

    a.  From **Workstation** VM Desktop Tool Bar, click the `Terminal` icon to open a Terminal window.

    b.  Launch the **Application Modernization Accelerator** with commands:

        cd ~/usr/IBM/application-modernization-accelerator-local-*

        ./launch.sh
        
     If both Docker and Podman are installed on the system, you will  be asked to select a container manager. Select **Podman (1)**
     
     Wait for Application Modernization Accelerator to initialize and display the **action menu** list.

    c.  If AMA is installed but not already started, type **`5`** to “Start” the **Application Modernization Accelerator**.

     ![](./images/media/image12.png)
 
    d. The **Application Modernization Accelerator** application is started, and the URL is displayed in the output similar to `https://desktop-2xxx:3000`. 
 
     ![](./images/media/image13.png)

    **`Right-click`** the application URL link and select **`Open Link`** to launch it in a web browser window.
    (Alternatively you can also access AMA directly in a browser using the URL https://localhost:3000).
 

    e. Accept the security warning (the screen might look different)

     ![Firefox_Security_Alert1.png](./images/media/Firefox_Security_Alert1.png)
 
    ![Firefox_Security_Alert2.png](./images/media/Firefox_Security_Alert2.png)
 
    f. Accept all cookies

     ![](./images/media/image13b.png)
 
    

     The **Application Modernization Accelerator** Home page is displayed in the Web Browser.
 
     ![](./images/media/image14.png)

    <br>

In the next section, you will create a new “**Workspace**” in Application Modernization Accelerator and upload the saved results from the scan of a WebSphere Application Server that has a single application deployed, named “PlantsByWebSphere”.

<br>

## 1.3 Create new workspace and upload scan results from a WebSphere Application Server

In this section, create a new workspace named **'Evaluation'**.

A `workspace` is a designated area that will house the migration recommendations provided by Application Modernization Accelerator based on the Data Collector scan results of your application server environment.

1. Create a new workspace named **Evaluation**

    a.  From the Application Modernization Accelerator Home page, click the **`Create workspace`** button

    b.  Type **`Evaluation`** as the Workspace name. Then click the **`Create`** button

     ![screenshot](./images/media/image15.png)

2. Upload an existing **data collection** file provided for the lab.

    The data collection file is provided for you.

    It was produced by running the **Application Modernization Accelerator Discovery Tool** to inspect a WebSphere Application Server environment that had the **PlantsByWebSphere** application deployed.

    a. Click the **`Upload results'** button.

    ![Upload results](./images/media/image16.png)


    b. Click the **`Drop or add file`** link. 

    ![Upload results](./images/media/image16a.png)


    
    c.  Navigate to **Home \> itzuser \> Student \> ama**

    d.  Select **`Dmgr01.zip`** file, then click the **`Open`** button

    ![Upload data collection](./images/media/image17.png)

    e. Once the **Dmgr.zip** has been added, click on the **`Upload`** button to upload the results into Application Modernization Accelerator.

    ![Upload data collection](./images/media/image18.png)

    After a few moments, the application data will be uploaded to the Application Modernization Accelerator UI. You will be asked to select the target runtime. Select **Liberty** and click on **Confirm**.

    ![Upload data collection](./images/media/image18a.png)

    
    The "**Evaluation**" workspace displays the “Visualization** of all applications in that workspace. The application you will focus on in the lab is the PlantsByWebSphere application which consists of a Java Enterprise application and a database.

    ![AMA_Visualization_View](./images/media/image18c.png)

    
    Click on **Assessment** to see more details about the different applications.

    ![AMA_Assessment_View](./images/media/image19.png)


    You can see five applications with their complexity, migration issues, efforts and more. You will focus one the one called **plantsbywebsphereee6.ear**.
    
    


## 1.4 Analyze the plantsbywebsphereee6 application

 Application Modernization Accelerator provides detailed information about each application that has been analyzed. This application **plantsbywebsphereee6.ear** has a complexity of **`Simple`**.
 
 **Simple** means:
 
  - The application is ready to be deployed to WebSphere Liberty
  - No changes to the source code are required
 
   
![screenshot](./images/media/image20.png)

 ***How is this application ready for deployment when it shows 4 issues?***
 
 In this case, the application has four **`Informational`** issues.
 
 Informational issues do not prevent the application from executing on the new runtime (WebSphere Liberty) but there may be small changes in the application’s behavior. If unexpected behavior is found during testing, then reviewing these **Informational** issues may help explain what is happening.


1.  Review the **'migration plan'** for this application. 

    a. Click on the **`Migration plan`** link at the end of the row of the **plantsbywebsphereee6.ear** application.

    ![screenshot](./images/media/image21.png)

    b.  The **Migration Plan** page opens.

    This page contains a summary of the application being migrated:

    - a preview of the files that will help during deployment (highlighted below)

    - a list of the application’s dependencies.

    All the files can be downloaded in a single convenient **migration plan**.

    ![](./images/media/image22.png)

2. At the bottom of the screen, there is an `Application dependencies` section. 
    
    This shows all the files, in addition to the application, that are required for a deployment. **Plantsbywebspheeee6.ear** has four dependencies.

    a. Expand the section it to see the details

    b. In this case, DB2 drivers called **db2jcc.jar** and **db2cc_licence.jar** are required for deployment (you might see additional libraries but those are not required in that case.)

    ![PBW depdencencies](./images/media/image23.png)

3.  Download the migration plan by clicking the **`Download plan`** button in the bottom right. 

    ![screenshot](./images/media/image24.png)

    If you get a security warning, accept the risk and continue.

    ![Firefox_Security_Alert1a.png](./images/media/Firefox_Security_Alert1a.png)
 
    ![Firefox_Security_Alert2a.png](./images/media/Firefox_Security_Alert2a.png)
    

    A zip file named “**plantsbywebsphereee6.ear_migrationPlan.zip**” will download to your **Download** folder.

<br>

In the next section, you will go through a step-by-step process of using the Application Modernization Accelerator migration plan to deploy **plantsbywebsphereee6.ear** as follows:

  1. Ensure the application can run locally on WebSphere Liberty

  2. Build an immutable container image running on WebSphere Liberty

  3. Deploy the image to OpenShift (and configure it) using a single command

  4. Redeploy and reconfigure this image for a new environment with a single command

<br>

## Troubleshooting
In the next section of the lab, you will need the migration plan.
If you run into any issues that hinder you from creating the AMA migration plan, use the following command to get it.

        cp ~/Student/ama/plantsbywebsphereee6.ear_migrationPlan.zip ~/Downloads/



# Part 2: Deploy PlantsByWebSphere app to a locally running WebSphere Liberty    

In this step, you will create a new Liberty server to run the PlantsByWebSphere application.

Then, you will review the migration plan to see what files you need to add and use the provided `server.xml` Liberty configuration file. This file is used to configure Liberty by providing values for ports, security, context routes and by providing application-specific configuration. 

### 2.1   Create a local Liberty Server

1.  From a **`Terminal`** window, download and install the Liberty archive.

        wget https://public.dhe.ibm.com/ibmdl/export/pub/software/websphere/wasdev/downloads/wlp/26.0.0.6/wlp-webProfile7-26.0.0.6.zip -P ~/Student
        cd ~/Student
        unzip wlp-webProfile7-26.0.0.6.zip


    

2.  From the **`Terminal`** window, create a new local WebSphere Liberty server named ‘**pbwserver**”

        ~/Student/wlp/bin/server create pbwserver

    **output:**

    ```
    Server pbwserver created.
    ```
3.  From the **Terminal** window, start the local WebSphere Liberty server.

        ~/Student/wlp/bin/server start pbwserver

    **Output:**

    ```
    Starting server pbwserver.

    Server pbwserver started with process ID #####
    ```

4.  Confirm the local WebSphere Liberty server is running. Open the `Firefox` web browser, open a new browser tab. Then go to: **http://localhost:9080**

    ![screenshot](./images/media/image25.png)


5.  From the **Terminal** window, stop the local WebSphere Liberty server.

        ~/Student/wlp/bin/server stop pbwserver

    **Output:**

    ```
    Stopping server pbwserver.
    Server pbwserver stopped.

    ```



### 2.1.3 Review the placeholder files in the migration plan

In this section, you will review the migration plan to see what files need to be added to the WebSphere Liberty configuration to run the application. 

1.  Let’s make note of the 5 `placeholder files` that are placed in the `migration plan`.

    The placeholder files are convenient references to let you know which files you will need to copy to the **Liberty server**.
 
    *Note: You will fully populate the migration plan in later steps*.

    a.  From the **Terminal window**, list the placeholder files in the migration plan.

        unzip -l ~/Downloads/plantsbywebsphereee6.ear_migrationPlan.zip | grep placeholder
 
    ![](./images/media/placeholder-files.png)
 
    - The **`target`** directory contains the placeholder file for the PlantsByWebSphere application EAR deployment file. This is a reminder that you need to copy the PlantsByWebSphere EAR file the Liberty server.

    - The **`src/main/liberty/lib`** directory contains the placeholder files for the DB2 database libraries required by the application. This is a reminder that you need to copy the DB2 libraries to the Liberty server.

    <br/>

### 2.1.4   Configure the Liberty server for PlantsByWebSphere

In this section, extract the migration plan that you downloaded. Then, copy the required dependency files and server configuration to the Liberty server.

  - Extract the **migration plan** to a new directory

  - Copy the **DB2 libraries** to the directory defined in the "DB2 driver configuration" in the server.xml file.

  - Copy the **PlantsByWebSphere EAR** file to the “apps” directory of the Liberty server

  - Copy the **server.xml** file from the migration plan to the Liberty server, replacing the default server.xml file


1. Extract the migration plan that you downloaded.

        mkdir ~/Student/migrationPlan
        cd ~/Student/migrationPlan
        cp ~/Downloads/plantsbywebsphereee6.ear_migrationPlan.zip .
        unzip plantsbywebsphereee6.ear_migrationPlan.zip


2. List the directory of the extracted migration plan.

        ls -l        

    The migration plan contains the files to build and deploy the PlantsByWebSphere application to Liberty, in containers, and on OpenShift.     

    ![](./images/media/migrationplan_content.png)
 

3.	Copy the PlantsByWebSphere application binary file into the `apps` location of the Liberty instance.

        cp ~/Student/plantsbywebsphereee6.ear ~/Student/wlp/usr/servers/pbwserver/apps

4. Create the `global shared` directory in Liberty that will be used to store the DB2 libraries. 

    This is where you will copy the DB2 libraries for Liberty

        mkdir -p ~/Student/wlp/usr/shared/config/lib/global
 
5.	Copy the DB2 driver files into the `global shared` location in Liberty.

    This will make it available to any application running in Liberty.

        cp ~/Student/db2_drivers/* ~/Student/wlp/usr/shared/config/lib/global

        ls -lrt ~/Student/wlp/usr/shared/config/lib/global



6.	Copy the `server.xml` file from the migration plan into the Liberty server to replace its existing configuration. Choose to overwrite the existing file if prompted.

        cp ~/Student/migrationPlan/src/main/liberty/config/server.xml ~/Student/wlp/usr/servers/pbwserver 

7.	Configure Liberty to use Java 8 instead of Java 21.

        echo "" >> ~/Student/wlp/usr/servers/pbwserver/server.env

        echo "JAVA_HOME=/usr/lib/jvm/ibm-semeru-open-8-jdk" >> ~/Student/wlp/usr/servers/pbwserver/server.env
 
8. Configure Liberty to use JPA 2.0 instead of JPA 2.1. 

        sed -i 's/jpa-2.1/jpa-2.0/g' /home/itzuser/Student/wlp/usr/servers/pbwserver/server.xml
    

9. Install the missing Liberty features required to run the application

        ~/Student/wlp/bin/featureUtility installServerFeatures pbwserver


    ___

    Now that the PlantsByWebSphere binary and its configuration are added to the Liberty server, the application is ready to be run from the web browser.     
    ___

9. Start the Liberty server

        ~/Student/wlp/bin/server start pbwserver --clean


10.  Run the PlantsByWebSphere application from the browser.

        http://localhost:9080/PlantsByWebSphere
 
        Now the application is running in Liberty, and the main page is displayed.
 
        ![A website page of a garden Description automatically generated](./images/media/image36.png)

11.  Attempt to click on any of the tabs: “**Flowers**”, **Fruits & Vegetables**”, or “**Trees**”.

12. **Note the exception.** This is an expected error. The issue is in the JPA persistence (Database access). A user is not defined, and therefore authentication to the database failed.
 
    ![A screen shot of a computer Description automatically generated](./images/media/image37.png)
 
    ___

    These pages should display a catalog of items in their respective category, which are retrieved from the application database. 

    **So, what happened?** 
    
    Application Modernization Accelerator does not collect any `sensitive data` for the application server.  This means the application-specific configuration information in the **server.xml** file has not been set. In this case, the `username` and `password` to access the database are missing.

    In the next section, you will review the server.xml file and add the sensitive data required to access the application database.  

    <br/>

### 2.1.5   Review the server.xml file

Now you will review the **`server.xml`** file and set the necessary configuration information. 

The **server.xml** file defines a set of **`features`** that the application requires. By importing only, the necessary **features** to support the application’s API needs, the footprint of the deployed application and Liberty server is kept as small as possible.

1. Review the server.xml file

    Using the “**`gedit`**” editor in a **`Terminal`** window, open the **server.xml** file located in the Liberty server

        gedit ~/Student/wlp/usr/servers/pbwserver/server.xml &

    - **Section 1** contains the `features` that the application requires, and these are discovered automatically during analysis performed by the Application Modernization Accelerator **Data Collector**.

      ![FeatureManager](./images/media/image38.png)

    - **Section 2:** Defines the `resources` required to access the database

      - The **`authdata`** defies the BD2 user and password that is used by the datasources. These refer to variables that are defined in the server.xml file.

      - The **`jdbc`** driver defines the required libraries. These are the libraries you copied into this location via the script

      ![database resources](./images/media/image39.png)

    - **Section 3:** The **`datasource`** contains all the information required to access the database: Database user, password, database name, host, and port number.

      ![datasource](./images/media/image40.png)


    - **Section 4** contains the variables for the **`non-sensitive configuration data`**. For example, the port to run the server on. The values of these variables are collected by Application Modernization Accelerator.

      ![non-sensitive config data](./images/media/image41.png)

    - **Section 5** contains the **`sensitive data variables`**. 
    
      You can see that the values for these variables are all **blank**, as this information is **never** collected by Application Modernization Accelerator.

      ![sensitive config data](./images/media/image42.png)


2.  The reason that the **PlantsByWebSphere** application returned an “Exception” is because the values for the **sensitive variables** have not been set - neither in the server.xml nor in the environment. 
  
    ![](./images/media/image43.png)

    Next, you will update the server.xml to include the credentials required to access the application database. 


### 2.1.6   Update the server.xml and retest the PlantsByWebSphere application


1. From a Terminal window, `stop` the Liberty server named ‘pbwserver”

        ~/Student/wlp/bin/server stop pbwserver

2. From the `gedit` editor that you have open, update the values in the **server.xml** file for the sensitive data as illustrated below:

    a. Scroll to the bottom of the `server.xml` file to view the sensitive-data variables. 

    b.  Set the default value for **Dmgr01Node_DB2INST1_password** to: **`db2password`**
    
    c. Set the default value for **Dmgr01Node_DB2INST1_user** to: **`db2inst1`**

     ![](./images/media/image44.png)
    
    d. **`Save`** and **`close`** the server.xml in the editor

    e. You might have detected that the database host is defined in the server.xml as **vm-1**. In your environment, the database is located on the host **desktop-2**.
    Use the following command to adjust the hostname.

        sed -i 's/vm-1/desktop-2/g' /home/itzuser/Student/wlp/usr/servers/pbwserver/server.xml

3. From a Terminal window, `start` the Liberty server named ‘pbwserver”

        ~/Student/wlp/bin/server start pbwserver


4. Reload and test PlantsByWebSphere application in the browser

    http://localhost:9080/PlantsByWebSphere

    If you would now attempt to click on any of the tabs: “Flowers”, Fruits & Vegetables”, or “Trees”, you would see that the authentication error has gone but you would see another error like in the screenshot below as the database has not been initialized yet.

    ![PBW Error not init](./images/media/pbw_dB_not_initialized.png)
    
    Therefore, before you can fully test the application, you need to initialize the database.

    a. Click on Help to get the help panel

    ![PBW Help](./images/media/pbw-help.png)
    
    b. Click on the **Reset database** button to reset the database.

    ![PBW Reset Database](./images/media/pbw-reset-database.png)

    c. Back on the main page, click the “**Flowers**” tab. The catalog of flowers should now be displayed.

    ![PBW Flowers](./images/media/image45.png)


3. From a **Terminal** window, `Stop` the Liberty server named ‘**pbwserver**”

        ~/Student/wlp/bin/server stop pbwserver


### Checkpoint

At this point in the lab, you have successfully demonstrated that the PlantsByWebSphere application runs successfully in WebSphere Liberty on a local VM.

In the next sections, you will deploy the PlantsByWebSphere application to containers and Red Hat OpenShift. 

<br>

# Part 3: Create and run the PlantsByWebSphere application as a container image    


## 3.1  Explore the Containerfile used to build PlantsByWebSphere as a container image

In the previous section, you used the **server.xml** to get the  application running on a `local Liberty instance`. This was to show you how the Application Modernization Accelerator **server.xml** file is used. If you are moving to Liberty in VMs as your new runtime, then you are done! 

However, if containers are to be your final destination, this section explores the Application Modernization Accelerator migration plan artifacts that accelerate application deployment to Liberty in containers. 

In the case of **`Simple`** applications, it is not necessary to carry out a separate step of deploying to a local Liberty instance at all. Instead, by using the Application Modernization Accelerator migration plan, you can deploy your application to **Liberty running in a container** all in one go. 

This is what you will do now.


1.	Add the PlantsByWebSphere binary file to your migration plan and remove the placeholder.

        cp ~/Student/plantsbywebsphereee6.ear ~/Student/migrationPlan/target

        rm ~/Student/migrationPlan/target/plantsbywebsphereee6.ear.placeholder


2.	Add the db2 library files to your migration plan and remove the placeholder.

        cp ~/Student/db2_drivers/* ~/Student/migrationPlan/src/main/liberty/lib  

        rm ~/Student/migrationPlan/src/main/liberty/lib/*.placeholder

3.	Update the server.xml file as before to adjust the database endpoint and JPA spec. 

        sed -i 's/vm-1/desktop-2/g' /home/itzuser/Student/migrationPlan/src/main/liberty/config/server.xml
        sed -i 's/jpa-2.1/jpa-2.0/g' /home/itzuser/Student/migrationPlan/src/main/liberty/config/server.xml


4.  Modify the "**Containerfile**" to specify a specific version of Liberty to deploy: in this case, **icr.io/appcafe/websphere-liberty:26.0.0.6-kernel-java8-openj9-ubi**

        sed -i 's/websphere-liberty:25.0.0.6-kernel-java8-openj9-ubi/websphere-liberty:26.0.0.6-kernel-java8-openj9-ubi/g' /home/itzuser/Student/migrationPlan/Containerfile


5. The `migration plan` is ready to be used to generate an image of your application running on WebSphere Liberty. To do this, you will use the **`Containerfile`** that comes with the migration plan.


    a. Explore the Containerfile in the migration plan

        gedit /home/itzuser/Student/migrationPlan/Containerfile
 
 
    - The **FROM** statements in the **Containerfile** pull in the following two images. 
     
      - **`IBM Semeru JDK 8`**
      - **`WebSphere Liberty Kernel image`**The 

      ![](./images/media/image50.png)

      ![](./images/media/image51.png)

        The Liberty kernel image is used to start with a minimual Liberty installation. The required features will 
       
    - The following commands in the Containerfile create the necessary folder structures and copies the **`binary files`** from the migration plan into the appropriate locations in the image.

      ![Containerfile-RUN](./images/media/image52.png)
      ![Containerfile-RUN](./images/media/image52a.png)

    - The **RUN features.sh** command will install the features required to run the application. As the initial installation was done using the Liberty kernel image, this will minimize the container image by installing only those features that are required for the application to run.

      ![Containerfile-RUN](./images/media/image52b.png)

    
    
    - There are several lines in the file that have been commented out. 
    
      By default, the **Containerfile** assumes that your application is available as a **binary** file. However, it can also be used to build your application from **source** code. The full details of how to do this can be found in **README.md** in the migration plan.

      ![](./images/media/image53.png)

    b. `Close` the "gedit" editor. **DO NOT SAVE ANY CHNAGES TO THE FILE!** 

    <br/>


### 3.2 Setup, build and run PlantsByWebSphere in a local container

In this section, you will build and run the PlantsByWebSphere application in a local container.

1.	Build the container image from the migration plan using podman

        cd ~/Student/migrationPlan

        podman build -f Containerfile --tag apps/pbw .

    - `-f` is the path to the `Containerfile` used to build the image
    - `--tag` allows you to define the name and version of the image to build
    - `.` is the "current directory" of the migration plan that includes the artifacts for building the container image. 

    The whole build process might take a couple of minutes.
    
2. Confirm the container image has been built

        podman images | grep apps/pbw


    ![](./images/media/pbw-image-listed.png)


3.	The next step is to run the image using the `podman run`command.

        podman run -d --rm -p 9080:9080 --name pbw-app  apps/pbw

    -  `-d` start the container in 'detached' mode so you get your command prompt back
    -  `--rm` remove the container when it is stopped
    -  `-p` expose the HTTP port
    -  `--name` is the name you are giving the container 


4.	Check that the container is up and running. 

        podman ps | grep pbw-app

    ![](./images/media/pbw-app-up.png)



5.  From the browser, access the PlantsByWebSphere application that is running in the local container:

    http://localhost:9080/PlantsByWebSphere
 
     ![PBW](./images/media/image61.png)
 
    
    Attempt to click on any of the tabs: “**Flowers**”, **Fruits & Vegetables**”, or “**Trees**”.

    ___

    **Note the exception.** This is an expected error. The issue is in the JPA persistence (Database access). A user is not defined, and therefore authentication to the database failed.
 
    ![](./images/media/pbw-container-db-error.png)
 
    The error is because you used the unchanged server.xml file that came with the migration plan. 
    
    As outlined already, Application Modernization Accelerator does not collect any sensitive data. Based on that, the application specific configuration information has not been set. 

6. To make sure the container as is works, let's inject the credentials via environment variables. Restart the container but with this time set user id and password as environment variables. (This approach is not recommended for production environments.)

       podman stop pbw-app

       podman run -d --rm -p 9080:9080 \
       -e Dmgr01Node_DB2INST1_password="db2password" \
       -e Dmgr01Node_DB2INST1_user="db2inst1" \
       --name pbw-app  apps/pbw 

7.  From the browser, access the PlantsByWebSphere application that is running in the local container:

    http://localhost:9080/PlantsByWebSphere
 
     ![PBW](./images/media/image61.png)
 
    
    Attempt to click on any of the tabs: “**Flowers**”, **Fruits & Vegetables**”, or “**Trees**”.

    This time it should work without any issue.

8. Finally stop the container

       podman stop pbw-app


    ___

**Considerations & recommendations:**

Another option here instead of setting environment variables is to update the **server.xml** file, add in the sensitive data values that Application Modernization Accelerator did not collect and rebuild the image. 
    
However, a big part of container image value is that they are **`immutable`**. No matter where you take and deploy the image the operating system, runtime, security patch level, etc. will be the same. 
    
This gives you great **reproducibility** and gets away from the classic ‘**but it works for me!**’ issue. You lose much of this value if you bake in the configuration with the image, as you will have to produce an image for each new configuration. 
    
Instead of using the exact same image in each of your development, staging and production environments, you would be using different images. 

___
   
**It is never best practice to hard code configuration into your image.** 


In the next section you will look at how the `migration plan` helps you manage this configuration easily across all your environments and how it will simplify deployment to your OpenShift cluster. The migration plan uses `Kustomize` to help achieve this.

___


## 3.2 Understand how Kustomize is used in the migration plan

**`kustomize`** is simple way to manage configuration across all your different deployments and environments without the need for templates.

By using **`overlays`**, it allows you to break out your basic configuration information (ports, names, hosts, etc.) from your sensitive data (usernames, password, etc.) that are likely to change in every deployment.

Every **kustomize** artifact is plain `YAML` and can be validated and processed in a standardized way. Plus, it makes them very human readable\! It is natively built into **kubectl** and the **OpenShift client**.

Take a look at the kustomize folder structure in the migration plan:

    tree ~/Student/migrationPlan/deploy/kustomize/

  > ![](./images/media/kustomize.png)
 

The structure of the kustomize folder is as follows.

  1.	`base` contains your expected standard deployment in a `config map` (because it is not sensitive data). It also contains the `application.cr-yaml` file that will execute the deployment and mount the necessary secrets that are created from the secret file.

  2.	`overlays` contains all your different deployment configurations. In this case, only a single deployment has been created for your `dev` systems. The `secret` file will contain all your application-specific **sensitive data** that will be created as secrets in OpenShift.

___

**Note:** In the next section you will use this kustomize structure to deploy your application image. 

Further information on **kustomize** can be found at <http://kustomize.io>
    
___


## 4 Deploy to OpenShift with config

You have now created a container image without any application specific configuration. In this section, you will **push** that image to an image repository and then **deploy** it into OpenShift, along with its configuration, with a single command.

### 4.1 Update the Kustomize structure to move the configmap configuration to the overlays folder

It is common to have different application configuration between deployments such as `dev` and `staging`. In this lab, you use only use one different DB2 application databases between the two configurations. For this reason , you want the configMap to be located in the Kustomize `overlays` folder, rather than the `base` where it is placed, by default, in the Application Modernization Accelerator migration plan. 

In the next step, you will update the file structure and the related kustomization.yaml configuration files. 

1. Move the configMap to the Kustomize `overlays` folder

       cd ~/Student/migrationPlan/deploy/kustomize
       mv base/plantsbywebsphereee6-configmap.yaml overlays/dev/plantsbywebsphereee6-configmap.yaml

2. Remove the reference to plantsbywebsphereee6-configmap.yaml from base/kustomization.yaml

    a. Edit the file and remove the reference

       cd ~/Student/migrationPlan/deploy/kustomize
       gedit base/kustomization.yaml

    > ![](./images/media/configmap_unchanged.png)

    b. Use the following command to review the final file:

        cat base/kustomization.yaml

    c. The content of the file should now look like this:

        # Generated by IBM Migration Artifacts Bundler
        # Thu Oct 22 17:56:54 UTC 2026
        commonLabels:
          app: plantsbywebsphereee6
        resources:
          - application-cr.yaml

3. Add the reference to plantsbywebsphereee6-configmap.yaml to overlays/dev/kustomization.yaml

    a. Edit the file:

        gedit overlays/dev/kustomization.yaml

    b. Add under ressources the following line:

       - plantsbywebsphereee6-configmap.yaml

    ![OCP-Kustomize-config](./images/media/OCP-Kustomize-config.png)
    
    c. Use the following command to review the final file:

        cat overlays/dev/kustomization.yaml

    d. The content of the file should now look like this:

        # Generated by IBM Migration Artifacts Bundler
        # Thu Oct 22 17:56:54 UTC 2026
        resources:
          - plantsbywebsphereee6-secret.yaml
          - plantsbywebsphereee6-configmap.yaml
        bases:
          - ../../base



### 4.2 Update the YAML files to add the application configuration for deployment

Recall, the container image does not have the application configuration baked in. 

You will make the following updates to the configuration YAML files in the migration plan for the PlantsByWebSphere application deployment to OpenShift. 

  -  Update the `base/application-cr.yaml` file
     - Add the reference to the container image
     - Accept the Liberty license
  
  -  Update the `overlays/dev/plantsbywebsphereee6-secret.yaml` file
     -  Add the sensitive data to the **secret** YAML file
  
  - Update the `overlays/dev/plantsbywebsphereee6-configmap.yaml` file
     -  Add the database host information to access the dev database


1.  Update the `application-cr.yaml` file
    
    The `application-cr.yaml` is the WebSphere Liberty custom resource used to deploy the PlantsByWebSphere application.
  

    a. Adjust the value of applicationImage from `IMAGE_REFERENCE` to something like
    `default-route-openshift-image-registry.apps.itz-e1vpbd.infra01-lb.dal14.techzone.ibm.com/apps-pbw/pbw-app:1.0`.

    The OpenShift base URL is stored in the variable **OCP_BASE_URL**. Check the content of the variable, then replace the imageReference string.

        echo $OCP_BASE_URL

        imageReference="default-route-openshift-image-registry.${OCP_BASE_URL}/apps-pbw\/pbw-app:1.0"
        echo $imageReference

        sed -i "s|IMAGE_REFERENCE|${imageReference}|g" base/application-cr.yaml

    b. Accept the license by replacing `accept: false` with `accept: true`

        sed -i 's/accept: false/accept: true/g' base/application-cr.yaml

    c. Review the updated **application-cr.yaml**
        
        cat ~/Student/migrationPlan/deploy/kustomize/base/application-cr.yaml

    The output should look like this:

    ![](./images/media/application-cr-yaml-pbw.png)

    Review also the rest of the file. 
    - **replicas** specifies the number of pods
    - **envFrom** specifies the configMap to be used
    - you could also specify the license information


    If you run into any trouble, you can copy the content from below (but need to adjust the applicationImage URL).

        # Generated by IBM Migration Artifacts Bundler
        # Thu Oct 22 17:56:54 UTC 2026
        kind: WebSphereLibertyApplication
        apiVersion: liberty.websphere.ibm.com/v1
        metadata:
            name: plantsbywebsphereee6
        spec:
            replicas: 1
            applicationImage: default-route-openshift-image-registry.apps.itz-e1vpbd.infra01-lb.dal14.techzone.ibm.com/apps-pbw/pbw-app:1.0
            expose: true
            volumeMounts:
            - mountPath: /config/variables
                name: plantsbywebsphereee6-secret
                readOnly: true
            volumes:
                - name: plantsbywebsphereee6-secret
                secret:
                    secretName: plantsbywebsphereee6-secret
            envFrom:
                - configMapRef:
                    name: plantsbywebsphereee6-config
            service:
                port: 9443
            manageTLS: true
            license:
                accept: true


    The updated `application-cr.yaml` in the **base** directory is now complete. The PlantsByWebSphere application configuration is finalized.  

    ___


2.	Update the **plantsbywebsphereee6-secret.yaml** file, which contains the sensitive-data for the application. 

    1. Adjust the line for the db2 user

    - from: `Dmgr01Node_DB2INST1_user: <REPLACE_WITH_BASE64_ENCODED_DATA>`
    - to: `Dmgr01Node_DB2INST1_user: ZGIyaW5zdDE=`

    Use the following command to do so:

        sed -i 's/Dmgr01Node_DB2INST1_user: <REPLACE_WITH_BASE64_ENCODED_DATA>/Dmgr01Node_DB2INST1_user: ZGIyaW5zdDE=/g' overlays/dev/plantsbywebsphereee6-secret.yaml

    2. Adjust the line for the db2 password

    - from: `Dmgr01Node_DB2INST1_password: <REPLACE_WITH_BASE64_ENCODED_DATA>`
    - to: `Dmgr01Node_DB2INST1_password: ZGIycGFzc3dvcmQ=`

    Use the following command to do so:

        sed -i 's/Dmgr01Node_DB2INST1_password: <REPLACE_WITH_BASE64_ENCODED_DATA>/Dmgr01Node_DB2INST1_password: ZGIycGFzc3dvcmQ=/g' overlays/dev/plantsbywebsphereee6-secret.yaml

    3. Adjust the line for the primary admin

    - from: `WIMUserRegistry_1_Default01Cell_userRegistry_primaryAdminId: <REPLACE_WITH_BASE64_ENCODED_DATA>`
    - to: `WIMUserRegistry_1_Default01Cell_userRegistry_primaryAdminId: d2FzYWRtaW4=`

    Use the following command to do so:

        sed -i 's/WIMUserRegistry_1_Default01Cell_userRegistry_primaryAdminId: <REPLACE_WITH_BASE64_ENCODED_DATA>/WIMUserRegistry_1_Default01Cell_userRegistry_primaryAdminId: d2FzYWRtaW4=/g' overlays/dev/plantsbywebsphereee6-secret.yaml

    4. Review the final file:

        cat overlays/dev/plantsbywebsphereee6-secret.yaml

        The output should look like this:

        ![](./images/media/secret-yaml-pbw.png)

    
    If you run into any trouble, you can copy the content from below.

        # Generated by IBM Migration Artifacts Bundler
        # Thu Oct 22 17:56:54 UTC 2026
        kind: Secret
        apiVersion: v1
        metadata:
            name: plantsbywebsphereee6-secret
        type: Opaque
        data:
            Dmgr01Node_DB2INST1_password: ZGIycGFzc3dvcmQ=
            Dmgr01Node_DB2INST1_user: ZGIyaW5zdDE=
            WIMUserRegistry_1_Default01Cell_userRegistry_primaryAdminId: d2FzYWRtaW4=
  
      The update to the `plantsbywebsphereee6-secret.yaml` in the **overlays/dev** directory is now complete. The sensitive data is encoded and added to the yaml file. 


    **HINT: How did You encode the credential values?**
    
    You updated the values in the YAML file with the sensitive data by  **Base64 encoding** the values as illustrated below.
 
    - You encoded the following values:
   
      ___

      - user: **db2inst1**

        echo db2inst1 | base64

      The encoded value is: **ZGIyaW5zdDE=**

      ___

      - password: **db2password**

        echo -n "db2password" | base64

      The encoded value is: **ZGIycGFzc3dvcmQ=**

      ___ 

      - user: **wasadmin**

        echo -n "wasadmin" | base64

      The encoded value is: **d2FzYWRtaW4=**




3.	Update the **plantsbywebsphereee6-configmap.yaml** file, which contains the database name and host for the `dev` database

    Adjust the database endpoint from vm-1 to desktop-2

    Use the following command to do so:

        cd ~/Student/migrationPlan/deploy/kustomize
        sed -i 's/vm-1/desktop-2/g' overlays/dev/plantsbywebsphereee6-configmap.yaml

    Review the final file:

        cat overlays/dev/plantsbywebsphereee6-configmap.yaml

    The output should look like this:

    ![](./images/media/pbw-configmap-yaml-update.png)

    
    The update to the `plantsbywebsphereee6-configmap.yaml` in the **overlays/dev** directory is now complete. The database host for the dev database has been added to the yaml file. 

Review the final configuration:

        ls -Rl ~/Student/migrationPlan/deploy/kustomize/

The output should look like this:

![](./images/media/updated-kustomize-structure.png)

Review the date and time of the yaml file to make sure you modified all of those files.

 ### 4.3 Deploy the application and its configuration to OpenShift

To deploy the PlantsByWebSphere application to OpenShift, you must perform the following basic steps.

  - Tag the container image, appropriately tagging for the OpenShift internal registry
 
  - Push the tagged container image to the OpenShift internal registry   

  - Use a single command to deploy and configure the application to OpenShift


1. Login to OpenShift using your IBM ID 

        oc login -w --server=$OCP_CONSOLE_URL:6443

    A browser window will open where you need to log in via IBM ID.

2. Verify that the WebSphere Liberty Operator has been installed

        oc get operators -n openshift-operators | grep websphere-liberty

    The output should look like this:

    ![](./images/media/wlp-operator-installed.png)

    If nothing is returned, the WebSphere Liberty Operator is not installed and you must do it manually. To do so, perform the steps described in the **Appendix** and then continue here.


3. Create a new project where you will deploy the application 

        oc new-project apps-pbw


4. Login to the OpenShift internal registry so you can push the image 

        oc registry login

    The command will return the registry public hostname which should be similar to (but maybe with a different base URL)

    `default-route-openshift-image-registry.apps.itz-e1vpbd.infra01-lb.dal14.techzone.ibm.com`

5. Tag the container image using the following podman command

        imageReference="default-route-openshift-image-registry.${OCP_BASE_URL}/apps-pbw/pbw-app:1.0"
        echo $imageReference

        podman tag apps/pbw ${imageReference}

6. Verify the image was tagged

        podman images | grep pbw-app

    ![](./images/media/pbw-apps-tagged.png)


7. Push the container image to OpenShift internal registry 

       imageReference="default-route-openshift-image-registry.${OCP_BASE_URL}/apps-pbw/pbw-app:1.0"
       echo $imageReference

       podman push ${imageReference}


In previous steps, you configured the sensitive data that is needed, accepted the license, pointed to the image that is needed to pull,  and logged into OpenShift. Now, with a single command, you can deploy to OpenShift and configure the image. 

You must specify which overlay will provide the configuration (dev in this case).

8. Deploy the PlantsByWebSphere application to OpenShift    

        cd ~/Student/migrationPlan/deploy/kustomize

        oc apply -n apps-pbw -k overlays/dev

    The output should show:
    
       configmap/plantsbywebsphereee6-config created
       secret/plantsbywebsphereee6-secret created
       webspherelibertyapplication.liberty.websphere.ibm.com/plantsbywebsphereee6 created


    Ignore warnings like
    - #Warning: 'bases' is deprecated. Please use 'resources' instead. Run 'kustomize edit fix' to update your Kustomization automatically.
    - #Warning: 'commonLabels' is deprecated. Please use 'labels' instead. Run 'kustomize edit fix' to update your Kustomization automatically.




9. Verify the deployment

        oc get deployment -n apps-pbw

    Wait for the `deployment` to be ready and available, as illustrated below:

        NAME                   READY   UP-TO-DATE   AVAILABLE  
        plantsbywebsphereee6   1/1     1            1    


10. Verify the application `pod` is ready.

    This may take a few moments. Just keep checking with command below

        oc get pods -n apps-pbw

    Wait for the `pod` to be ready and available, as illustrated below:

        NAME                                    READY   STATUS       
        plantsbywebsphereee6-7476449666-shnx8   1/1     Running   

11. List the route of the PlantsByWebSphere application:

        oc get route -o jsonpath='{range .items[*]}{.spec.host}{"\n"}{end}'

    The output should be similar to:

        plantsbywebsphereee6-apps-pbw.apps.itz-3035es.infra01-lb.dal14.techzone.ibm.com


12. Open in the browser the PlantsByWebSphere route for the `dev` deployment:

        /usr/bin/xdg-open "https://$(oc get route -o jsonpath='{range .items[*]}{.spec.host}{"\n"}{end}')/PlantsByWebSphere"


    The PlantsByWebSphere main page is displayed.

    ![A website page of a garden Description automatically generated](./images/media/image78a.png)

13. Click on the **`Trees`** category and view the trees that are loaded in the ‘dev’ database.

    This catalog of trees was retrieved from the DB2 database in the `dev` configuration.
 
    ![screenshot](./images/media/image79.png)



##  4.4 Explore the WebSphere Liberty Operator in OpenShift

In this section, you will take a look at the **WebSphere Liberty Operator** in the OpenShift console to see what has been deployed.

1.  Login to OpenShift console

    a.  On the desktop, click on the icon for the OpenShift Desktop.

    ![OCP_WebConsole_Desktop_Icon](./images/media/OCP_WebConsole_Desktop_Icon.png)
    
    Alternatively use the command:

        /usr/bin/xdg-open "$OCP_CONSOLE_URL"
    
    b.  Log in using your **IBMid**

    ![OCP Console Login](./images/media/OCP-Console-Login.png)
    

    c. After successful login, you should land on the panel **Welcome to the new OpenShift experience!**
    
    ![OCP_Tour](./images/media/OCP_Tour.png)

    Click on **Skip tour**.

    d. Finally you should land on the overview page

    ![OCP-Console_Overview](./images/media/OCP-Console_Overview.png)
    

    

2.  View the **IBM WebSphere Liberty** `Operator`

    a.  Click on **`Ecosystem > Installed Operators`** in the left-hand menu

    ![OCP-Console_Liberty-Operator.png](./images/media/OCP-Console_Liberty-Operator.png)

    b.  Click on **`IBM WebSphere Liberty`**

    ![OCP-Console_Liberty-Operator2.png](./images/media/OCP-Console_Liberty-Operator2.png)



3. View the **WebSphereLibertyApplication** `Deployment`

    a.  Click on the tab called **`WebSphereLibertyApplication`**

    ![OCP-Console_Liberty-Operator3.png](./images/media/OCP-Console_Liberty-Operator3.png)


    b. You will see the **plantsbywebsphereee6** application listed in the ‘dev’ namespace

    ![OCP-Console_Liberty-Application.png](./images/media/OCP-Console_Liberty-Application.png)

    
    b. In the **Name** column, click on the link called **`plantsbywebsphereee6`**.

    ![OCP-Console_Liberty-Application2.png](./images/media/OCP-Console_Liberty-Application2.png)

    c.  Select the **`Resources`** tab

    ![OCP-Console_Liberty-Application-PBW.png](./images/media/OCP-Console_Liberty-Application-PBW.png)

    d. Select the link for the kind **`Deployment`**

    ![OCP-Console_Liberty-Application-PBW2.png](./images/media/OCP-Console_Liberty-Application-PBW2.png)


    e. The PlantsByWebSphere deployment has one `pod` running

    ![OCP-Console_Liberty-Application-PBW3.png](./images/media/OCP-Console_Liberty-Application-PBW3.png)


4.  View the WebSphereLibertyApplication **`Route`**

    a.  Return to the **plantsbywebsphereee6 operator** page

    ![OCP-Console_Liberty-Application-PBW4.png](./images/media/OCP-Console_Liberty-Application-PBW4.png)


    b.  Click the **`Resources`** tab

    ![OCP-Console_Liberty-Application-PBW5.png](./images/media/OCP-Console_Liberty-Application-PBW5.png)


    c.  Select the link of **Kind: `route`**

    ![OCP-Console_Liberty-Application-PBW6.png](./images/media/OCP-Console_Liberty-Application-PBW6.png)


    d.  Click on the **`Location`** link for the route.

    ![OCP-Console_Liberty-Application-PBW7.png](./images/media/OCP-Console_Liberty-Application-PBW7.png)


    e.  The **Welcome to Liberty** page is displayed in a new Browser tab. 

    ![screenshot](./images/media/image95.png)

    f.  Append the context root ‘**PlantsByWebSphere’** to access the PlantsByWebSphere application, for example:

      https://plantsbywebsphereee6-apps-pbw.apps.itz-e1vpbd.infra01-lb.dal14.techzone.ibm.com/PlantsByWebSphere

    g. You should see the PlantsByWebSphere application. Feel free to test it again.

    ![pbw](./images/media/pbw_OCP.png)


## 4.5 Optional: Re-deploy app with different configuration to OpenShift  - To be done

You have deployed the PlantsByWebSphere application with its configuration to `dev`, using a single kustomize command. 

In this section, you will explain how to deploy the same application again with a new configuration for `staging`, again using a single kustomize command. 

1. Stop the `dev`database and delete the OCP deployment

        cd ~/Student/migrationPlan/deploy/kustomize/
        oc delete -n apps-pbw -k overlays/dev
        sudo podman stop plantsdb-db2



2. Create a `staging` database, exposed on port 50001.

        sudo podman run --privileged=true -d \
        --name plantsdb-db2-staging \
        -p 50001:50000 \
        -e LICENSE=accept \
        -e DB2INST1_PASSWORD=db2password \
        -e DBNAME=PLANTSDB \
        -v plantsdb-data2:/database \
        plantsdb-db2

3. Verify that the database is running

        sudo podman ps | grep plantsdb
        netstat -an | grep 50001

    You should see an output like this showing that a database container with the name **plantsdb-db2-staging** is running  and listeing on port 50001:

        08c592726a0b  localhost/plantsdb-db2:latest  10 seconds ago  Up 9 seconds  
        0.0.0.0:50001->50000/tcp, 22/tcp, 55000/tcp, 60006-60007/tcp  plantsdb-db2-staging

        tcp        0      0 0.0.0.0:50001           0.0.0.0:*               LISTEN     

4.	Create a new `overlay` directory called **staging** that will store the new staging configuration, coping the existing dev directory.

        cd ~/Student/migrationPlan/deploy/kustomize/overlays/

        cp -r dev staging


5.	Update the **plantsbywebsphereee6-configmap.yaml** file, which contains the database name and host for the **staging** configuration.

    You need to update the port from 50000 to 50001, the rest is the same.


        cd ~/Student/migrationPlan/deploy/kustomize
        sed -i 's/50000/50001/g' overlays/staging/plantsbywebsphereee6-configmap.yaml

        cat overlays/staging/plantsbywebsphereee6-configmap.yaml 

 
6. From the `kustomize` directory, apply the new `staging` overlay

        cd ~/Student/migrationPlan/deploy/kustomize

        oc apply -n apps-pbw -k overlays/staging

    The output should look like this:
    
       configmap/plantsbywebsphereee6-config created
       secret/plantsbywebsphereee6-secret created
       webspherelibertyapplication.liberty.websphere.ibm.com/plantsbywebsphereee6 created

7. Ensure the pod has been started

        oc get pods -n apps-pbw


8. Execute the following command to monitor the database log.

        sudo podman logs -f plantsdb-db2-staging


9. Open a new terminal and open in the browser the PlantsByWebSphere route for the `staging` deployment via the command:

        /usr/bin/xdg-open "https://$(oc get route -o jsonpath='{range .items[*]}{.spec.host}{"\n"}{end}')/PlantsByWebSphere"


    The PlantsByWebSphere main page is displayed.

    ![A website page of a garden Description automatically generated](./images/media/image78a.png)

    **Note:** If the application is not available, wait a moment and then reload the page.

10. Initialize the database.

    a. Click on Help to get the help panel

    ![PBW Help](./images/media/pbw-help.png)
    
    b. Click on the **Reset database** button to reset the database.

    ![PBW Reset Database](./images/media/pbw-reset-database.png)

11. Switch back to the terminal window where the `podman logs` command is running. You should see that the log has been updated. 
(Feel free to run the reset command again if you did not recognize any update)

12. Finally click on `Accessories` or any of the tabs “Flowers”, Fruits & Vegetables”, or “Trees” to verify that the application works fine.

    ![PBW_Accessories](./images/media/PBW_Accessories.png)
    

13. Clean up.

    a. Delete the OCP deployment

        cd ~/Student/migrationPlan/deploy/kustomize/
        oc delete -n apps-pbw -k overlays/staging


    b. Stop the `staging`database.

        sudo podman stop plantsdb-db2-staging

# Summary

___
**Congratulations!**

**You have successfully completed the core learning objectives in the lab**

___

In this lab, you learned how to deploy applications to WebSphere Liberty using the WebSphere Liberty Operator and the deployment artifacts produced by Application Modernization Accelerator in its migration plan.

You explored the options for deployment: 

  - locally running Liberty
  - Liberty as an image running in local container
  - Liberty as an image running in OpenShift.

You learned how to easily configure deployments to OpenShift to allow the same immutable image to be deployed for different configurations such as ‘dev’ and ‘staging’ environment deployments.

You learned about some of the practical ways you can secure your configuration data.

You explored the PlantsByWebSphere assessment details and reports in Application Modernization Accelerator.


___


# Appendix

## Troubleshooting:

### OpenShift related issues

#### oc login fails
<details>
<summary>Details around how to fix OpenShif login issues</summary>


If `oc login -w` fails with an error like

    error: dial tcp: lookup xxx no such host - verify you have provided the correct host and port and that the server is currently running.

this indicates that the OCP endpoint has not been set in the configuration. Try the following command to fix it:

    echo $OCP_CONSOLE_URL

If the command returns no URL, you need to set the URL via these commands:

    cat /usr/local/share/applications/OpenShiftConsole.desktop | grep http > OCP_URL.txt
    OCP_CONSOLE_URL=$(sed 's/Exec=\/usr\/bin\/xdg-open //g' OCP_URL.txt)
    sudo echo "export OCP_CONSOLE_URL=$OCP_CONSOLE_URL" >> ~/.bashrc
    OCP_BASE_URL="${OCP_CONSOLE_URL:34}"
    sudo echo "export OCP_BASE_URL=$OCP_BASE_URL" >> ~/.bashrc

Now you can log in via the command:

    oc login -w --server=$OCP_CONSOLE_URL:6443
    
</details>

#### WebSphere Liberty Operator not installed
<details>
<summary>Details around how to install the WebSphere Liberty Operator into OpenShift</summary>
The WebSphere Liberty Operator is installed by default.
But if not, here the  steps to install it.

<br>

1. Make sure that the ibm-operator-catalog is installed 

        oc get catalogsource -n openshift-marketplace

    The command output lists the installed operators for CatalogSources. Ensure the ibm-operator-catalog is listed.

    a. If the ibm-operator-catalog is not listed, perform the following steps:


    a. Install the ibm-operator-catalog
    
        cat <<EOF | oc apply -n openshift-marketplace -f -
        apiVersion: operators.coreos.com/v1alpha1
        kind: CatalogSource
        metadata:
            name: ibm-operator-catalog
            namespace: openshift-marketplace
        spec:
            displayName: IBM Operator Catalog
            publisher: IBM
            sourceType: grpc
            image: icr.io/cpopen/ibm-operator-catalog:latest
            updateStrategy:
                registryPoll:
                interval: 45m
        EOF

    b. Check that the pods for CatalogSources are running.


       oc get pods -n openshift-marketplace | grep ibm-operator-catalog

        This might take some time, Wait until the pod is running.


    Hint: You can find more details here:
        https://www.ibm.com/docs/en/was-liberty/base?topic=operator-preparing-installation

2. Install the liberty operator 

    1. Specify the namespace and trigger the installation

            WLO_NAMESPACE=openshift-operators
            cat <<EOF | oc apply -f -
            apiVersion: operators.coreos.com/v1alpha1
            kind: Subscription
            metadata:
                name: ibm-websphere-liberty
                namespace: $WLO_NAMESPACE
            spec:
                installPlanApproval: Automatic
                name: ibm-websphere-liberty
                source: ibm-operator-catalog
                sourceNamespace: openshift-marketplace
            EOF

    2. Check if pod wlo-controller-manager is available

            WLO_NAMESPACE=openshift-operators
            oc get pods -n $WLO_NAMESPACE

    3. Check the status of the WebSphere Liberty operator:

            WLO_NAMESPACE=openshift-operators
            oc get operators -n $WLO_NAMESPACE | grep websphere-liberty

    Hint: You can find more details here:
    https://www.ibm.com/docs/en/was-liberty/base?topic=operator-installing-red-hat-openshift-cli


</details>


## How to install and use the IBM License Service (ILS)

### Install the IBM License Service

1. Check if the IBM License Service is already installed:

    To verify the installation of License Service with CLI, run the following command:
	
       oc get deployment --all-namespaces | grep ibm-licensing-operator
    If a deployment is returned, ILS is already installed and you can skip the next steps

2. Install the ibm-licensing-catalog

    Execute the following comand to install the ILS catalog

        cat <<EOF | oc apply -f -
        apiVersion: operators.coreos.com/v1alpha1
        kind: CatalogSource
        metadata:
            name: ibm-licensing-catalog
            namespace: openshift-marketplace
        spec:
            displayName: IBM License Service Catalog
            publisher: IBM
            sourceType: grpc
            image: icr.io/cpopen/ibm-licensing-catalog
            updateStrategy:
                registryPoll:
                    interval: 45m
            grpcPodConfig:
                securityContextConfig: restricted
        EOF

    Output:

    `catalogsource.operators.coreos.com/ibm-licensing-catalog created`


3. Verify that the CatalogSource for your IBM License Service operator is created.

        oc get pods -n openshift-marketplace | grep licensing
        oc get catalogsource -n openshift-marketplace | grep licensing


4. Install the IBM License Service operator.

    1. Create a namespace for License Service.

            oc create namespace ibm-licensing

        Output: `namespace/ibm-licensing created`

    2. Create the operator group to deploy the OperatorGroup resource.

            cat <<EOF | oc apply -f -
            apiVersion: operators.coreos.com/v1
            kind: OperatorGroup
            metadata:
                name: licensing-og
                namespace: ibm-licensing
            spec:
                targetNamespaces:
                - ibm-licensing
            EOF

        Output: `operatorgroup.operators.coreos.com/licensing-og created`
 

    3. Create the subscription for License Service.

            export LS_CHANNEL=v4.2 
            cat <<EOF | oc apply -f -
            apiVersion: operators.coreos.com/v1alpha1
            kind: Subscription
            metadata:
                name: ibm-licensing-operator-app
                namespace: ibm-licensing
            spec:
            channel: $LS_CHANNEL
            installPlanApproval: Automatic
            name: ibm-licensing-operator-app
            source: ibm-licensing-catalog
            sourceNamespace: openshift-marketplace
            EOF

        Output: `subscription.operators.coreos.com/ibm-licensing-operator-app created`

    4. Verify that your License Service operator is installed:

            oc get pod -n $NAMESPACE
 
        The licensing-operator pod should be shown as "Running". Otherwise wait and try again (It might take up to 15 minutes for all the pods to show the Running status.

    5. Update the License Service instance that was created during installation to accept the license.

           echo -e "
            spec:	\n
                license:	\n
                    accept: true	\
            " > accept-license.yaml

           cat accept-license.yaml

           oc patch IBMLicensing instance --type merge --patch-file accept-license.yaml
 
    6. Get the route of the IBM License Service

            oc get route -n ibm-licensing -o jsonpath='{range .items[*]}{.spec.host}{"\n"}{end}'

5. Get the authentication token

        export LICENSING_URL=$(oc get route -n ibm-licensing -o jsonpath='{range .items[*]}{.spec.host}{"\n"}{end}')

        export TOKEN=$(kubectl get secret ibm-licensing-token -n ibm-licensing -o jsonpath='{.data.token}' |base64 -d)

6. Get the URL of the status page

    To display the URL of the ILS status page including token, use the command:

        echo https://${LICENSING_URL}/status?token=${TOKEN}

    Access the status page via the command:

        /usr/bin/xdg-open "https://${LICENSING_URL}/status?token=${TOKEN}"


7. You can retrieve the licensing report .zip file by running:

        curl -k "https://${LICENSING_URL}/snapshot?token=${TOKEN}" --output report.zip



Useful links how to install the IBM License Service: 
https://www.ibm.com/docs/en/cloud-paks/foundational-services/4.x?topic=service-installing-license)
https://www.ibm.com/docs/en/cloud-paks/foundational-services/4.x?topic=registry-installing-license-service-openshift-container-platform


### Use the IBM License Service 

Start from scratch without any deployment:

1. Delete the OCP deployment

        cd ~/Student/migrationPlan/deploy/kustomize/
        oc delete -n apps-pbw -k overlays/dev
        oc delete -n apps-pbw -k overlays/staging

2. Access the ILS Status Page:

        export LICENSING_URL=$(oc get route -n ibm-licensing -o jsonpath='{range .items[*]}{.spec.host}{"\n"}{end}')

        export TOKEN=$(kubectl get secret ibm-licensing-token -n ibm-licensing -o jsonpath='{.data.token}' |base64 -d)

        /usr/bin/xdg-open "https://${LICENSING_URL}/status?token=${TOKEN}"

    The ILS Status page should show no Liberty instance.

    ![ILS_Status-Page-noWLP.png](./images/media/ILS_Status-Page-noWLP.png)

3. Create an OCP deployment for Liberty dev

        cd ~/Student/migrationPlan/deploy/kustomize/
        oc apply -n apps-pbw -k overlays/dev

    Reload the ILS Status page and it should show a Liberty instance.

    ![ILS_Status-Page-1WLP-noLimit.png](./images/media/ILS_Status-Page-1WLP-noLimit.png)
    
4. Let's take a look at the information

    - The product is annotated for WebSphere Application Server which is based on PVUs.
    - The node has 8 cores
    - A CPU limit is not set, so the whole worker node will be licensed.
    - As each core is counted with 70 PVU, the metric value is 560.
    

5. Compare the findings with the deployment file.

        gedit ~/Student/migrationPlan/deploy/kustomize/base/application-cr.yaml

    ![ILS_application-cr1](./images/media/ILS_application-cr1.png)

    - No edition has been specified
    - No entitlement has been specified
    - No CPU limit has been specified

6. Adjust the deployment to add entitlement details.

    For easiness just remove the # from the lines 27 to 29
    
    ![ILS_application-cr2](./images/media/ILS_application-cr2.png)

    Save the changes but keep the editor open.

        
7. Apply the changes and see the results: 
   
        cd ~/Student/migrationPlan/deploy/kustomize/
        oc apply -n apps-pbw -k overlays/dev

    Reload the ILS Status page and it should show a Liberty instance.

    ![ILS_Status-Page-2WLP-noLimit.png](./images/media/ILS_Status-Page-2WLP-noLimit.png)
    
    - The product is annotated for WebSphere Application Server but now based on VPCs as WebSphere Hybrid Edition (WSHE) has the VPC metric.
    - The node has 8 cores
    - A CPU limit is not set, so the whole worker node will be licensed.
    - WAS has the ratio 4:1 within the WebSphere Hybrid Edition, which means 4 deployed cores require 1 VPC of WSHE. 
    - As the whole worker node must be licensed and the ratio 4:1 is applied, the metric value is 2.

8. Add a cpu limit to avoid that the whole worker node must be licensed.

       gedit ~/Student/migrationPlan/deploy/kustomize/base/application-cr.yaml

   Add the following 5 lines just the line after line 10:

       resources:
           requests:
               cpu: 500m
           limits:
               cpu: 1000m
               memory: 512Mi

    ![ILS_application-cr3](./images/media/ILS_application-cr3.png)

    Save the changes but keep the editor open.

9. Apply the changes and see the results: 
   
        cd ~/Student/migrationPlan/deploy/kustomize/
        oc apply -n apps-pbw -k overlays/dev

    Reload the ILS Status page and it should show a Liberty instance.

    ![ILS_Status-Page-2WLP-noLimit.png](./images/media/ILS_Status-Page-2WLP-noLimit.png)
    
    - The product is annotated for WebSphere Application Server but now based on VPCs as WebSphere Hybrid Edition (WSHE) has the VPC metric.
    - The node has 8 cores
    - A CPU limit is not set, so the whole worker node will be licensed.
    - WAS has the ratio 4:1 within the WebSphere Hybrid Edition, which means 4 deployed cores require 1 VPC of WSHE. 
    - As the whole worker node must be licensed and the ratio 4:1 is applied, the metric value is 2.
