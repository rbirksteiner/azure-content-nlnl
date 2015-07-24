<properties
  pageTitle="將 Azure 雲端服務角色連接到裝載於 Azure 中的自訂 AD 網域控制站"
  description="了解如何使用 Powershell 和 AD 網域延伸將 Web/背景工作角色連接到自訂 AD 網域"
  services="cloud-services"
  documentationCenter=""
  authors="VMak"
  manager="MadhanA"
  editor=""/>

  <tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/05/2015"
    ms.author="vmaker"/>

# 將 Azure 雲端服務角色連接到裝載於 Azure 中的自訂 AD 網域控制站

## 將 Azure Web/背景工作角色連接到裝載於 Azure 之自訂網域控制站的逐步指南

我們會先在 Azure 中設定虛擬網路 (VNET)。接著再將 Active Directory 網域控制站 (裝載於 Azure 虛擬機器上) 加入 VNET。下一步是將現有雲端服務角色加入預先建立的 VNET，然後再將它們連接到網域控制站。

在開始之前，請特別注意以下幾件事：1.本教學課程使用 Powershell，因此請確認您已安裝 Azure Powershell 且已準備就緒。如需設定 Azure Powershell 的說明，請參閱「[如何安裝及設定 Azure PowerShell](../install-configure-powershell.md)」。2.AD 網域控制站和 Web/背景工作角色執行個體必須位在 VNET 中。

請遵循以下逐步指南，如果您遇到任何問題，請在下方留言，我們將會回覆您 (沒錯，我們真的會閱讀留言)。

## 建立虛擬網路

您可以使用 Azure 入口網站或 Powershell 在 Azure 中建立虛擬網路。在本教學課程中，我們將使用 Powershell。若要使用 Azure 入口網站建立虛擬網路，請參閱「[建立虛擬網路](../create-virtual-network.md)」。

    #Create Virtual Network

    $vnetStr =
    @"<?xml version="1.0" encoding="utf-8"?>
    <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="[your-vnet-name]" Location="West US">
            <AddressSpace>
              <AddressPrefix>[your-address-prefix]</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="[your-subnet-name]">
                <AddressPrefix>[your-subnet-range]</AddressPrefix>
              </Subnet>
            </Subnets>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>
    "@;

    $vnetConfigPath = "<path-to-vnet-config>"
    Set-AzureVNetConfig -ConfigurationPath $vnetConfigPath;

## 建立虛擬機器

完成虛擬網路的設定後，您需要建立 AD 網域控制站。在本教學課程中，我們會在 Azure 虛擬機器上設定 AD 網域控制站。

若要這樣做，請使用以下命令透過 Powershell 建立虛擬機器。

    #Initialize variables

    $vnetname = '<your-vnet-name>'
    $subnetname = '<your-subnet-name>'
    $vmsvc1 = ‘<your-hosted-service>’
    $vm1 = ‘<your-vm-name>’
    $username = ‘<your-username>’
    $password = ‘<your-password>’
    $ affgrp = ‘<your- affgrp>’

    #Create a VM and add it to the Virtual Network

    New-AzureQuickVM -Windows -ServiceName $vmsvc1 -name $vm1 -ImageName $imgname -AdminUsername $username -Password $password -AffinityGroup $affgrp -SubnetNames $subnetname -VNetName $vnetname


## 將虛擬機器提升為網域控制站
若要將虛擬機器設定為 AD 網域控制站，您需要登入 VM 並進行設定。

若要登入 VM，您可以透過 Powershell 取得 RDP 檔案；請使用下列命令。

    #Get RDP file

    Get-AzureRemoteDesktopFile -ServiceName $vmsvc1 -Name $vm1 -LocalPath <rdp-file-path>

登入 VM 後，請依照「[如何設定客戶的 AD 網域控制站](http://social.technet.microsoft.com/wiki/contents/articles/12370.windows-server-2012-set-up-your-first-domain-controller-step-by-step.aspx)」中的逐步指導將虛擬機器設為 AD 網域控制站。

## 將雲端服務部署加入虛擬網路

接下來，您需要將雲端服務部署加入剛才建立的 VNET。若要這樣做，請使用 Visual Studio 或選擇的編輯器將相關區段加入 cscfg，藉此修改雲端服務 cscfg。

    <ServiceConfiguration serviceName="[hosted-service-name]" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="[os-family]" osVersion="*">
        <Role name="[role-name]">
        <Instances count="[number-of-instances]" />
      </Role>
      <NetworkConfiguration>

        <!--optional-->
        <Dns>
          <DnsServers><DnsServer name="[dns-server-name]" IPAddress="[ip-address]" /></DnsServers>
        </Dns>
        <!--optional-->

        <!--VNET settings-->
        <VirtualNetworkSite name="[virtual-network-name]" />
        <AddressAssignments>
          <InstanceAddress roleName="[role-name]">
            <Subnets>
              <Subnet name="[subnet-name]" />
            </Subnets>
          </InstanceAddress>
        </AddressAssignments>
        <!--VNET settings-->

      </NetworkConfiguration>
    </ServiceConfiguration>

接下來，請建置雲端服務專案並將它部署到 Azure。如需將雲端服務封裝部署到 Azure 的說明，請參閱「[如何建立和部署雲端服務](cloud-services-how-to-create-deploy.md#deploy)」

## 使用 AD 網域延伸將 Web/背景工作角色連接到自訂網域

在 Azure 上部署雲端服務專案後，請使用 AD 網域延伸將角色執行個體連接到自訂 AD　網域。若要將 AD 網域延伸加入現有雲端服務部署及加入自訂網域，請在 Powershell 中執行下列命令：

    #Initialize domain variables

    $domain = ‘<your-domain-name>’;
    $dmuser = ‘$domain<your-username>’;
    $dmpswd = '<your-domain-password>';
    $dmspwd = ConvertTo-SecureString $dmpswd -AsPlainText -Force;
    $dmcred = New-Object System.Management.Automation.PSCredential ($dmuser, $dmspwd);

    #Add AD Domain Extension to the cloud service roles

    Set-AzureServiceADDomainExtension -Service <your-cloud-service-hosted-service-name> -Role <your-role-name> -Slot <staging-or-production> -DomainName $domain -Credential $dmcred -JoinOption 35;

這樣就大功告成了。

雲端服務現在應該已加入自訂網域控制站。如果您想要深入了解設定 AD 網域延伸時可用的其他選項，請依下圖所示的方法使用 PS 說明。

    help Set-AzureServiceADDomainExtension;
    help New-AzureServiceADDomainExtensionConfig;

關於取得將虛擬機器提升為網域控制站的延伸，我們也想知道您對這個做法實用與否的意見反應。如果您認為這個做法很實用，請在意見區段中留言，讓我們知道。

希望您覺得這個做法很實用！
 

<!---HONumber=62-->