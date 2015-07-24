<properties
   pageTitle="如何使用 Azure PowerShell 命令來建立空白的雲端服務容器"
   description="本文說明如何使用 PowerShell 指令碼建立雲端服務容器，以及與執行雲端服務相關的管理作業"
   services="cloud-services"
   documentationCenter=".net"
   authors="cawaMS"
   manager="bscholl" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="na"
   ms.date="06/19/2015"
   ms.author="cawa"/>

# 如何使用 Azure PowerShell 命令來建立空白的雲端服務容器
1. [下載 Azure PowerShell](http://go.microsoft.com/?linkid=9811175&clcid=0x409)，以安裝 Microsoft Azure PowerShell Cmdlet。如需安裝 Azure PowerShell Cmdlet 及連接至您的 Azure 訂用帳戶的進一步指示，請參閱「[如何安裝及設定 Azure PowerShell](../powershell-install-configure.md)」。

2. **New-AzureService** 是用來建立空白雲端服務容器的 Cmdlet。

    ```
    New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
    New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
```

   以下是叫用 Cmdlet 的範例：```
New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"
```

3. 如需有關建立 Azure 雲端服務的詳細資訊，請執行：```
Get-help New-AzureService
```

4. 後續步驟：

   - 若要管理雲端服務部署，請參閱「[Get-AzureService](https://msdn.microsoft.com/library/azure/dn495131.aspx)」、「[Remove-AzureService](https://msdn.microsoft.com/library/azure/dn495120.aspx)」及「[Set-AzureService](https://msdn.microsoft.com/library/azure/dn495242.aspx)」命令。另請參閱「[如何設定雲端服務](cloud-services-how-to-configure.md)」。

    - 若要將雲端服務專案發佈至 Azure，請參閱「[在Azure 中持續提供雲端服務](cloud-services-dotnet-continuous-delivery.md)」中的 **PublishCloudService.ps1** 程式碼範例
 

<!---HONumber=62-->