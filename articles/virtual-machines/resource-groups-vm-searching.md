<properties
   pageTitle="利用 PowerShell 和 Azure CLI 瀏覽和選取 Azure 虛擬機器映像"
   description="了解如何在使用資源管理員建立 Azure 虛擬機器時，判斷映象的發佈者、提供項目及 SKU。"
   services="virtual-machines"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="command-line-interface"
   ms.workload="infrastructure"
   ms.date="05/29/2015"
   ms.author="rasquill"/>

# 利用 PowerShell 和 Azure CLI 瀏覽和選取 Azure 虛擬機器映像

> [AZURE.NOTE]當您在本主題中搜尋 VM 映象時，您是搭配使用 [Azure 資源管理模式](../resource-group-overview.md) 與適用於 Mac、Linux 和 Windows 的 Azure 命令列介面或 Windows PowerShell 的最近安裝 。搭配 Azure CLI 時，請輸入 `azure config mode arm` 來進入該模式。搭配 PowerShell 時，則請輸入 `Switch-AzureMode AzureResourceManager`。請參閱[搭配使用 Azure CLI 與資源管理](xplat-cli-azure-resource-manager.md)和[搭配使用 PowerShell 與 Azure 資源管理](../powershell-azure-resource-manager.md)，以取得更多完整的更新與設定詳細資料。

## 常用映象資料表


| PublisherName | 提供項目 | SKU |
|:---------------------------------|:-------------------------------------------|:---------------------------------|:--------------------|
| OpenLogic | CentOS | 7 |
| OpenLogic | CentOS | 7.1 |
| CoreOS | CoreOS | Beta |
| CoreOS | CoreOS | Stable |
| MicrosoftDynamicsNAV | DynamicsNAV | 2015 |
| MicrosoftSharePoint | MicrosoftSharePointServer | 2013 |
| msopentech | Oracle-Database-12c-Weblogic-Server-12c | 標準 |
| msopentech | Oracle-Database-12c-Weblogic-Server-12c | Enterprise |
| MicrosoftSQLServer | SQL2014-WS2012R2 | Enterprise-Optimized-for-DW |
| MicrosoftSQLServer | SQL2014-WS2012R2 | Enterprise-Optimized-for-OLTP |
| Canonical | UbuntuServer | 12.04.5-LTS |
| Canonical | UbuntuServer | 14.04.2-LTS |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter |
| MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1 |
| MicrosoftWindowsServer | WindowsServer | Windows-Server-Technical-Preview |
| MicrosoftWindowsServerEssentials | WindowsServerEssentials | WindowsServerEssentials |
| MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | 2012R2 |


## Azure CLI

尋找要與 `azure vm quick-create` 搭配使用之映像或建立資源群組範本檔案的最簡單且最快速方式是呼叫 `azure vm image list` 命令，並傳遞位置、發佈者名稱 (不區分大小寫！) 和提供項目 (如果您知道提供項目)。例如，如果您知道 "Canonical" 是 "UbuntuServer" 提供項目的發佈者，則下列清單只是簡短範例 (許多清單相當長)。

    azure vm image list westus canonical ubuntuserver
    info:    Executing command vm image list
    warn:    The parameter --sku if specified will be ignored
    + Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
    data:    Publisher  Offer         Sku          Version          Location  Urn                                               
    data:    ---------  ------------  -----------  ---------------  --------  --------------------------------------------------
    data:    canonical  ubuntuserver  12.04-DAILY  12.04.201504201  westus    canonical:ubuntuserver:12.04-DAILY:12.04.201504201
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201302250  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201302250
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201303250  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201303250
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201304150  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201304150
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201305160  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201305160
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201305270  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201305270
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201306030  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201306030
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201306240  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201306240
    
**Urn** 資料行將是您傳遞給 `azure vm quick-create` 的表單。
    
不過，您通常還不知道什麼可用。在此情況下，您可以瀏覽映像，方法是先使用 `azure vm image list-publishers` 探索發佈者，並以您預期用於資源群組的資料中心位置來回應位置提示字元。例如，下列清單是美國西部位置的所有映像發佈者 (將位置設為小寫並移除標準位置中的空格，來傳遞 location 引數)。

    azure vm image list-publishers
    info:    Executing command vm image list-publishers
    Location: westus
    + Getting virtual machine image publishers (Location: "westus")                
    data:    Publisher                                       Location
    data:    ----------------------------------------------  --------
    data:    a10networks                                     westus  
    data:    aiscaler-cache-control-ddos-and-url-rewriting-  westus  
    data:    alertlogic                                      westus  
    data:    AlertLogic.Extension                            westus  

    
這些清單可能相當長，因此上述範例清單只是程式碼片段。假設我注意到 Canonical 事實上是美國西部位置的映像發佈者。您現在可以呼叫 azure vm image list-offers 來尋找其提供項目，並在提示字元中傳遞位置和發佈者 (例如下列範例)：

    azure vm image list-offers           
    info:    Executing command vm image list-offers
    Location: westus
    Publisher: canonical
    + Getting virtual machine image offers (Publisher: "canonical" Location:"westus")
    data:    Publisher  Offer         Location
    data:    ---------  ------------  --------
    data:    canonical  UbuntuServer  westus  
    info:    vm image list-offers command OK
    
現在，我們知道在美國西部區域中，Canonical 在 Azure 上發佈 **UbuntuServer** 提供項目。但是，是什麼 SKU？ 若要取得那些項目，請呼叫 `azure vm image list-skus`，並使用您探索到的位置、發佈者和提供項目來回應提示。

    azure vm image list-skus           
    info:    Executing command vm image list-skus
    Location: westus
    Publisher: canonical
    Offer: ubuntuserver
    + Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
    data:    Publisher  Offer         sku          Location
    data:    ---------  ------------  -----------  --------
    data:    canonical  ubuntuserver  12.04-DAILY  westus  
    data:    canonical  ubuntuserver  12.04.2-LTS  westus  
    data:    canonical  ubuntuserver  12.04.3-LTS  westus  
    data:    canonical  ubuntuserver  12.04.4-LTS  westus  
    data:    canonical  ubuntuserver  12.04.5-LTS  westus  
    data:    canonical  ubuntuserver  12.10        westus  
    data:    canonical  ubuntuserver  14.04-beta   westus  
    data:    canonical  ubuntuserver  14.04-DAILY  westus  
    data:    canonical  ubuntuserver  14.04.0-LTS  westus  
    data:    canonical  ubuntuserver  14.04.1-LTS  westus  
    data:    canonical  ubuntuserver  14.04.2-LTS  westus  
    data:    canonical  ubuntuserver  14.10        westus  
    data:    canonical  ubuntuserver  14.10-beta   westus  
    data:    canonical  ubuntuserver  14.10-DAILY  westus  
    data:    canonical  ubuntuserver  15.04        westus  
    data:    canonical  ubuntuserver  15.04-beta   westus  
    data:    canonical  ubuntuserver  15.04-DAILY  westus  
    info:    vm image list-skus command OK
    
現在，使用此資訊，在頂端呼叫原始呼叫，即可找到就是您要的映像。

    azure vm image list westus canonical ubuntuserver 14.04.2-LTS
    info:    Executing command vm image list
    + Getting virtual machine images (Publisher:"canonical" Offer:"ubuntuserver" Sku: "14.04.2-LTS" Location:"westus")
    data:    Publisher  Offer         Sku          Version          Location  Urn                                               
    data:    ---------  ------------  -----------  ---------------  --------  --------------------------------------------------
    data:    canonical  ubuntuserver  14.04.2-LTS  14.04.201503090  westus    canonical:ubuntuserver:14.04.2-LTS:14.04.201503090
    data:    canonical  ubuntuserver  14.04.2-LTS  14.04.20150422   westus    canonical:ubuntuserver:14.04.2-LTS:14.04.20150422 
    data:    canonical  ubuntuserver  14.04.2-LTS  14.04.201504270  westus    canonical:ubuntuserver:14.04.2-LTS:14.04.201504270
    info:    vm image list command OK
    
現在，您可以精確地選擇想要使用的映像。若要使用您剛找到的 URN 資訊快速地建立 VM，或使用具有該 URN 資訊的範本，請參閱[搭配使用適用於 Mac、Linux 和 Windows 的 Azure CLI 與 Azure 資源管理](xplat-cli-azure-resource-manager.md)。

## PowerShell

使用 Azure 資源管理員建立新的虛擬機器時，在某些情況下，您需要使用下列映像屬性組合來指定映像：

- 發佈者
- 提供項目
- SKU

例如，**Set-AzureVMSourceImage** PowerShell Cmdlet 或資源群組範本檔案需要這些值，而在此檔案中，您必須指定要建立的虛擬機器類型。

如果您需要決定這些值，則可以瀏覽映像以決定這些值，方法是：

1. 列出映像發佈者。
2. 針對指定的發佈者，列出其提供項目。
3. 針對指定的提供項目，列出其 SKU。

若要在 PowerShell 中這麼做，請先切換到 Azure PowerShell 的資源管理員模式。

	Switch-AzureMode AzureResourceManager 

在上面第一個步驟中，使用這些命令列出發佈者。

	$locName="<Azure location, such as West US>"
	Get-AzureVMImagePublisher -Location $locName | Select PublisherName

填寫所選擇的發佈者名稱，然後執行以下命令。

	$pubName="<publisher>"
	Get-AzureVMImageOffer -Location $locName -Publisher $pubName | Select Offer

填寫所選擇的提供項目名稱，然後執行以下命令。

	$offerName="<offer>"
	Get-AzureVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus

從 **Get-AzureVMImageSku** 命令的顯示中，您擁有指定新虛擬機器之映像所需的所有資訊。

範例如下。

	PS C:\> $locName="West US"
	PS C:\> Get-AzureVMImagePublisher -Location $locName | Select PublisherName
	
	PublisherName
	-------------
	a10networks
	aiscaler-cache-control-ddos-and-url-rewriting-
	alertlogic
	AlertLogic.Extension
	Barracuda.Azure.ConnectivityAgent
	barracudanetworks
	basho
	boxless
	bssw
	Canonical
	...

針對 "MicrosoftWindowsServer" 發佈者：

	PS C:\> $pubName="MicrosoftWindowsServer"
	PS C:\> Get-AzureVMImageOffer -Location $locName -Publisher $pubName | Select Offer
	
	Offer
	-----
	WindowsServer

針對 "WindowsServer" 提供項目：

	PS C:\> $offerName="WindowsServer"
	PS C:\> Get-AzureVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
	
	Skus
	----
	2008-R2-SP1
	2012-Datacenter
	2012-R2-Datacenter
	Windows-Server-Technical-Preview

從此清單中，複製所選擇的 SKU 名稱，而且您擁有 **Set-AzureVMSourceImage** PowerShell Cmdlet 或資源群組範本檔案的所有資訊，而資源群組範本檔案需要您指定映像的發佈者、提供項目和 SKU。


<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png
[8]: ./media/markdown-template-for-new-articles/copytemplate.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[gog]: http://google.com/
[yah]: http://search.yahoo.com/
[msn]: http://search.msn.com/
 

<!---HONumber=62-->