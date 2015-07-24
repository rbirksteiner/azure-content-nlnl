<properties 
   pageTitle="客體作業系統系列 1 淘汰通知 | Azure" 
   description="提供客體作業系統系列 1 淘汰時間及如何自行判斷是否受影響等相關資訊" 
   services="cloud-services" 
   documentationCenter="na" 
   authors="Thraka" 
   manager="timlt" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd" 
   ms.date="06/16/2015"
   ms.author="adegeo"/>



# 客體作業系統系列 1 淘汰通知

我們早在 2013 年 6 月 1 日宣佈客體作業系統系列 1 的淘汰資訊。

**2014 年 9 月 2 日** 以 Windows Server 2008 作業系統為基礎的 Azure 客體作業系統 (客體 OS) 系列 1.x 已正式淘汰。所有使用系列 1 部署新服務或升級現有服務的嘗試均會失敗並且會出現錯誤訊息，通知您客體作業系統系列 1 已遭到淘汰。

**2014 年 11 月 3 日** 客體 OS 系列 1 的延伸支援已結束，並且已完全淘汰。所有仍以系列 1 為基礎的服務都將受到影響。我們可能會隨時停止這些服務。除非您以手動方式自行升級服務，否則我們不保證您的服務能繼續運作。

如果您有其他問題，請造訪[雲端服務論壇](http://social.msdn.microsoft.com/Forums/home?forum=windowsazuredevelopment&filter=alltypes&sort=lastpostdesc)或[連絡 Azure 支援](http://azure.microsoft.com/support/options/)。




## 您受到影響了嗎？

如果以下任一情況成立，表示您的雲端服務已受到影響：

1. 雲端服務之 ServiceConfiguration.cscfg 檔案明確地指定 "osFamily = "1" 值。 
2. 雲端服務之 ServiceConfiguration.cscfg 檔案未明確指定 osFamily 的值。在本案例中，系統目前使用預設值 "1" 。
3. Azure 管理入口網站列出您的客體作業系統系列值為 "Windows Server 2008"。若要找出此值，請參閱[本文章](https://msdn.microsoft.com/library/azure/gg456325.aspx)。

若要找出每個雲端服務執行的 OS 系列，您可以在 Azure PowerShell 中執行以下程式碼，但請先[設定 Azure PowerShell](../install-configure-powershell.md)。如需指令碼的其他詳細資料，請參閱「[Azure 客體 OS 系列 1 生命週期結束：2014 年 6 月](http://blogs.msdn.com/b/ryberry/archive/2014/04/02/azure-guest-os-family-1-end-of-life-june-2014.aspx)」(英文)

```Powershell
foreach($subscription in Get-AzureSubscription) {
    Select-AzureSubscription -SubscriptionName $subscription.SubscriptionName 
    
    $deployments=get-azureService | get-azureDeployment -ErrorAction Ignore | where {$_.SdkVersion -NE ""} 

    $deployments | ft @{Name="SubscriptionName";Expression={$subscription.SubscriptionName}}, ServiceName, SdkVersion, Slot, @{Name="osFamily";Expression={(select-xml -content $_.configuration -xpath "/ns:ServiceConfiguration/@osFamily" -namespace $namespace).node.value }}, osVersion, Status, URL
}
```

如果指令碼輸出中 osFamily 資料行為空白或含有 "1"，代表您的雲端服務將受到作業系統系列 1 淘汰所影響。

## 受影響時的建議

我們建議您將雲端服務角色移轉到任一個受支援的客體作業系統系列：

**客體 OS 系列 4.x** - Windows Server 2012 R2 *(建議選項)*

1. 請確認您的應用程式使用 SDK 2.1 或更新版本，搭配 .NET Framework 4.0、4.5 或 4.5.1。
2. 在 ServiceConfiguration.cscfg 檔案中將 osFamily 屬性設定為 "4" 的，然後重新部署雲端服務。


**客體 OS 系列 3.x** - Windows Server 2012

1. 請確認您的應用程式使用 SDK 1.8 或更新版本，搭配 .NET Framework 4.0 或 4.5。 
2. 在 ServiceConfiguration.cscfg 檔案中將 osFamily 屬性設定為「3」，然後重新部署雲端服務。


**客體 OS 系列 2.x** - Windows Server 2008 R2

1. 請確認您的應用程式使用 SDK 1.3 和更新版本，搭配 .NET Framework 3.5 或 4.0。 
2. 在 ServiceConfiguration.cscfg 檔案中將 osFamily 屬性設定為 "2" 的，然後重新部署雲端服務。


## 客體作業系統系列 1 的延長支援已在 2014 年 11 月 3 日結束。
以客體作業系統系列 1 為基礎的雲端服務將不再受到支援。請儘早移轉系列 1 以避免服務中斷。

<!---HONumber=62-->