<properties 
   pageTitle="DNS 區域上的作業 | Microsoft Azure" 
   description="您可以使用 Azure Powershell Cmdlet 管理 DNS 區域。如何在 Azure DNS 上更新、刪除及建立 DNS 區域" 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="Adinah" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="05/01/2015"
   ms.author="joaoma"/>

# 如何管理 DNS 區域

本指南說明如何管理 DNS 區域。有助於了解管理 DNS 區域所需執行的作業序列。

## 建立新的 DNS 區域

若要建立新的 DNS 區域來裝載您的網域，請使用 New-AzureDnsZone Cmdlet：

		PS C:\> $zone = New-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [–Tag $tags] 

此作業會在 Azure DNS 中建立新的 DNS 區域，並傳回對應至該區域的本機物件。您可以選擇性地指定一系列的 Azure 資源管理員標記，如需詳細資訊，請參閱 [Etag 和標記](../dns-getstarted-create-dnszone#Etags-and-tags)。

區域的名稱在資源群組內必須是唯一的，且區域必須尚未存在，否則作業會失敗。

不同的資源群組或不同的 Azure 訂用帳戶中，可重複使用相同的區域名稱。雖然多個區域共用相同的名稱，但每個執行個體會被指派不同的名稱伺服器位址，而且從父系網域只能委派一個執行個體。如需詳細資訊，請參閱[將網域委派給 Azure DNS](../dns-domain-delegation)。

## 取得 DNS 區域

若要擷取 DNS 區域，請使用 Get-AzureDnsZone Cmdlet：

		PS C:\> $zone = Get-AzureDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup

此作業會傳回對應至 Azure DNS 中現有區域的 DNS 區域物件。這個物件包含區域的相關資料 (例如記錄集的數目)，但不包含記錄集本身。

## 列出 DNS 區域
您可以從 Get-AzureDnsZone 中省略區域名稱，以列舉資源群組中的所有區域：

	PS C:\> $zoneList = Get-AzureDnsZone -ResourceGroupName MyAzureResourceGroup
此作業會傳回一系列的區域物件。

## 更新 DNS 區域
您可以使用 Set-AzureDnsZone 變更 DNS 區域資源。這不會更新區域內的任何 DNS 記錄集 (請參閱[如何管理 DNS 記錄](../dns-operations-recordsets))。它只用來更新區域資源本身的屬性。這在目前限於區域資源的 Azure 資源管理員「標記」。如需詳細資訊，請參閱 [Etag 和標記](../dns-getstarted-create-dnszone#Etags-and-tags)。

請使用下列兩種方法之一來更新 DNS 區域：

### 選項 1
 
使用區域名稱和資源群組來指定區域。

	PS C:\> Set-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Tag $tags]

### 選項 2
使用 Get-AzureDnsZone 傳回的 $zone 物件來指定區域：

	PS C:\> $zone = Get-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> <..modify $zone.Tags here...>
	PS C:\> Set-AzureDnsZone -Zone $zone [-Overwrite]

使用 Set-AzureDnsZone 搭配 $zone 物件時，將會使用 ‘Etag’ 檢查，以確保不會覆寫並行變更。您可以使用選擇性的 ‘-Overwrite’ 參數來停用這些檢查。如需詳細資訊，請參閱 [Etag 和標記](../dns-getstarted-create-dnszone#Etags-and-tags)。

## 刪除 DNS 區域

您可以使用 Remove-AzureDnsZone Cmdlet 刪除 DNS 區域。
 
在 Azure DNS 中刪除 DNS 區域之前，您必須刪除所有記錄集，但建立區域時在區域的根自動建立的 NS 和 SOA 記錄除外。

請使用下列兩種方法之一來移除 DNS 區域：

### 選項 1

使用區域名稱和資源群組名稱來指定區域：

	PS C:\> Remove-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Force] 

此作業具有選擇性的 '-Force' 參數，可不提示您確認您想要移除 DNS 區域。
### 選項 2

使用 Get-AzureDnsZone 傳回的 $zone 物件來指定區域：

	PS C:\> $zone = Get-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Remove-AzureDnsZone -Zone $zone [-Force] [-Overwrite]

'-Force' 參數與「選項 1」中相同。

如同 ‘Set-AzureDnsZone’ 一樣，使用 $zone 物件指定區域可啟用 'etag' 檢查，以確保不會刪除並行的變更。<BR> 選擇性的 ‘-Overwrite’ 旗標可停用這些檢查。如需詳細資訊，請參閱 [Etag 和標記](../dns-getstarted-create-dnszone#Etags-and-tags)。

區域物件也可以經由管道輸送，而不是當做參數傳遞：

	PS C:\> Get-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureDnsZone [-Force] [-Overwrite]

## 後續步驟


[管理 DNS 記錄](../dns-operations-recordsets)

[使用 .NET SDK 自動化作業](../dns-sdk)

<!---HONumber=62-->