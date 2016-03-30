<properties
    pageTitle="How to use the Azure slave plugin with Hudson Continuous Integration"
    description="Describes how to use Azure slave plugin with Hudson Continuous Integration."
    services="storage" 
    documentationCenter="java" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor="jimbe" />

<tags
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="11/19/2015" 
    ms.author="v-dedomi"/>

#How to use the Azure slave plugin with Hudson Continuous Integration

The Azure slave plugin for Hudson enables you to provision slave nodes on Azure when running distributed builds.

## Install the Azure Slave plugin
1. In the Hudson dashboard, click **Manage Hudson**.
2. In the **Manage Hudson** page, click on **Manage Plugins**.
3. Click the **Available** tab.
4. Click **Search** and type **Azure** to limit the list to relevant plugins.

    If you opt to scroll through the list of available plugins, you will find the Azure slave plugin under the **Cluster Management and Distributed Build** section in the **Others** tab.
     
5. Select the checkbox for **Azure Slave Plugin**.
6. Click **Install**.
7. Restart Hudson.

Now that the plugin is installed, the next steps would be to configure the plugin with your Azure subscription profile and to create a template that will be used in creating the VM for the slave node.


## Configure the Azure Slave plugin with your subscription profile

A subscription profile, also referred to as publish settings, is an XML file that contains secure credentials and some additional information you'll need to work with Azure in your development environment. To configure the Azure slave plugin, you need: 

* your subscription id
* a Management certificate for your subscription

These can be found in your subscription profile. If you don't have a copy of your subscription profile, you can download it from [here](https://manage.windowsazure.com/publishsettings/Index?SchemaVersion=2.0). Below is an example of a subscription profile.

    <?xml version="1.0" encoding="utf-8"?>
        <PublishData>
        <PublishProfile SchemaVersion="2.0" PublishMethod="AzureServiceManagementAPI">
        <Subscription
            ServiceManagementUrl="https://management.core.windows.net"
            Id="<Subscription ID>"
            Name="Pay-As-You-Go"
            ManagementCertificate="<Management certificate value>" />
        </PublishProfile>
    </PublisOnce you have your subscription profile, follow these steps to configure the Azure slave plugin. plugIn the Hudson dashboard, click **Manage Hudson**.udsoClick **Configure System**.ysteScroll down the page to find the **Cloud** section.sectClick **Add new cloud > Microsoft Azure**.Azure**.add new cloud new cloud](./media/azure-slave-plugin-for-hudson/hudson-setup-addcloud.This will show the fields where you need to enter your subscription details.n detailconfigure profilere profile](./media/azure-slave-plugin-for-hudson/hudson-setup-configureprofileCopy the subscription id and management certificate from your subscription profile and paste them in the appropriate fields.te fieWhen copying the subscription id and management certificate, **do not** include the quotes that enclose the values.he valuesClick on **Verify configuration**.uratWhen the configuration is verified successfully, click **Save**. **SaSet up a virtual machine template for the Azure Slave pluginavA virtual machine template defines the parameters the plugin will use to create a slave node on Azure. In the following steps we'll be creating template for an Ubuntu VM.UbuntuIn the Hudson dashboard, click **Manage Hudson**. HudClick on **Configure System**. SysScroll down the page to find the **Cloud** section.* seWithin the **Cloud** section, find **Add Azure Virtual Machine Template** and click the **Add** button.** buttoadd vm template vm template](./media/azure-slave-plugin-for-hudson/hudson-setup-addnewvmtemplaSpecify a cloud service name in the **Name** field.aIf the name you specify refers to an existing cloud service, the VM will be provisioned in that service.hOtherwise, Azure will create a new one.e a nIn the **Description** field, enter text that describes the template you are creating.rThis information is only for documentary purposes and is not used in provisioning a VM.ioniIn the **Labels** field, enter **linux**.rThis label is used to identify the template you are creating and is subsequently used to reference the template when creating a Hudson job. HudsSelect a region where the VM will be created. be Select the appropriate VM size.ate VSpecify a storage account where the VM will be created. Make sure that it is in the same region as the cloud service you'll be using.lIf you want new storage to be created, you can leave this field blank.fieldRetention time specifies the number of minutes before Hudson deletes an idle slave. Leave this at the default value of 60.valueIn **Usage**, select the appropriate condition when this slave node will be used.iFor now, select **Utilize this node as much as possible**. possiAt this point, your form would look somewhat similar to this:milar totemplate configemplate config](./media/azure-slave-plugin-for-hudson/hudson-setup-templateconfig1-withdIn **Image Family or Id** you have to specify what system image will be installed on your VM.eYou can either select from a list of image families or specify a custom image. custoIf you want to select from a list of image families, enter the first character (case-sensitive) of the image family name.eFor instance, typing **U** will bring up a list of Ubuntu Server families.rOnce you select from the list, Jenkins will use the latest version of that system image from that family when provisioning your VM.oning yoOS family listOS family list](./media/azure-slave-plugin-for-hudson/hudson-oslist.If you have a custom image that you want to use instead, enter the name of that custom image. Custom image names are not shown in a list so you have to ensure that the name is entered correctly.red correcFor this tutorial, type **U** to bring up a list of Ubuntu images and select **Ubuntu Server 14.04 LTS**.r 14.04For **Launch method**, select **SSH**.electCopy the script below and paste in the **Init script** field.cript** field.

        # Install Java
        sudo apt-get -y update
        sudo apt-get install -y openjdk-7-jdk
        sudo apt-get -y update --fix-missing
        sudo apt-get install -y openjdk-7-jdk

        # Install git
        sudo apt-get install -y git

        #Install ant
        sudo apt-get install -y ant
        sudo apt-get -y update --fix-missing
        sudo aptThe **Init script** will be executed after the VM is created. In this example, the script installs Java, git, and ant.talls JavaIn the **Username** and **Password** fields, enter your preferred values for the administrator account that will be created on your VM.ll beClick on **Verify Template** to check if the parameters you specified are valid. you Click on **Save**.d.
18.Create a Hudson job that runs on a slave node on Azure oIn this section, you'll be creating a Hudson task that will run on a slave node on Azure.on a sIn the Hudson dashboard, click **New Job**.boardEnter a name for the job you are creating.the For the job type, select **Build a free-style software job**.ree-Click **Ok**.e joIn the job configuration page, select **Restrict where this project can be run**.his Select **Node and label menu** and select **linux** (we specified this label when creating the virtual machine template in the previous section).in theIn the **Build** section, click **Add build step** and select **Execute shell**.seleEdit the following script, replacing **(your github account name)**, **(your project name)**, and **(your project directory)** with appropriate values, and paste the edited script in the text area that appears.e text area that appears.


        # Clone from git repo
        currentDir="$PWD"
        if [ -e (your project directory) ]; then
            cd (your project directory)
            git pull origin master
        else
            git clone https://github.com/(your github account name)/(your project name).git
        fi
        
        # change directory to project
        cd $currentDir/(your project directory)

        #Execute Click on **Save**.  antIn the Hudson dashboard, find the job you just created and click on the **Schedule a build** icon.ck Hudson will then create a slave node using the template created in the previous section and execute the script you specified in the build step for this task.ied in the build step for this task.






  

  
