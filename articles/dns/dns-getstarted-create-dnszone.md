<properties
   pageTitle="開始使用 Azure DNS | Microsoft Azure"
   description="了解如何建立 Azure DNS 的 DNS 區域。這是建立第一個 DNS 區域以開始裝載 DNS 網域的逐步解說。"
   services="dns"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/01/2015"
   ms.author="joaoma"/>

# 開始使用 Azure DNS
網域 'contoso.com' 可能包含許多的 DNS 記錄，例如 'mail.contoso.com' (用於郵件伺服器) 和 'www.contoso.com' (用於網站)。DNS 區域用來裝載特定網域的 DNS 記錄。<BR><BR> 為了開始裝載您的網域，我們必須先建立 DNS 區域。針對特定網域建立的任何 DNS 記錄，都位於該網域的 DNS 區域內。<BR><BR> 這些指示使用 Microsoft Azure PowerShell。請務必更新至最新的 Azure PowerShell，才能使用 Azure DNS Cmdlet。相同的步驟也可以使用 Microsoft Azure 命令列介面、REST API 或 SDK 來執行。<BR><BR>

## 設定 Azure DNS PowerShell

必須先完成下列步驟，才能使用 Azure PowerShell 管理 Azure DNS。

### 步驟 1
 Azure DNS 使用 Azure 資源管理員 (ARM)。請確定您切換 PowerShell 模式來使用 ARM Cmdlet。如需詳細資訊，請參閱[將 Windows PowerShell 與資源管理員搭配使用](../powershell-azure-resource-manager)。<BR><BR>

		PS C:\> Switch-AzureMode -Name AzureResourceManager

### 步驟 2
 登入您的 Azure 帳戶。<BR><BR>

		PS C:\> Add-AzureAccount

系統會提示使用您的認證進行驗證。<BR>

### 步驟 3
選擇其中一個要使用的 Azure 訂用帳戶。<BR>


		PS C:\> Select-AzureSubscription -SubscriptionName "MySubscription"

若要查看可用的訂用帳戶清單，請使用 ‘Get-AzureSubscription’ Cmdlet。<BR>

### 步驟 4
建立資源群組 (若使用現有的資源群組，請略過此步驟)<BR>

		PS C:\> New-AzureResourceGroup -Name MyAzureResourceGroup -location "West US"


Azure 資源管理員需要所有的資源群組指定一個位置。這用來作為該資源群組中資源的預設位置。然而，因為所有 DNS 資源是全球性，而非區域性，資源群組位置的選擇不會對 Azure DNS 造成影響。<BR>

### 步驟 5

Azure DNS 服務由 Microsoft.Network 資源提供者管理。您的 Azure 訂用帳戶必須註冊為使用此資源提供者，您才能使用 Azure DNS。此為每個訂用帳戶的一次性作業。

	PS C:\> Register-AzureProvider -ProviderNamespace Microsoft.Network



## Etag 和標記
### Etag
假設有兩個人或兩個處理序同時嘗試修改 DNS 記錄。何者獲勝？ 獲勝者知道他剛才已覆寫另一人所做的變更嗎？<BR><BR> Azure DNS 使用 Etag 以安全地處理相同資源的並行變更。每個 DNS 資源 (區域或記錄集) 都有一個相關聯的 Etag。每當擷取資源時，也會擷取其 Etag。更新資源時，您可以選擇傳回 Etag，讓 Azure DNS 可以確認伺服器上的 Etag 相符。因為每次更新資源都會重新產生 Etag，Etag 不符就表示發生並行變更。建立新的資源時也會使用 Etag，以確保該資源尚不存在。

根據預設，Azure DNS PowerShell 會使用 Etag 來禁止對區域和記錄集進行並行變更。選擇性的 ‘-Overwrite’ 參數可以用來停用 Etag 檢查，在此情況下，將覆寫任何已發生的並行變更。<BR><BR> 在 Azure DNS REST API 層級上是使用 HTTP 標頭指定 Etag。下表提供它們的行為：

|標頭|行為|
|------|--------|
|None|PUT 一定成功 (沒有 Etag 檢查)|
|If-match <etag>|唯有當資源存在且 Etag 符合時，PUT 才會成功|
|If-match * |唯有當資源存在時，PUT 才會成功|
|If-none-match |* 唯有當資源不存在時，PUT 才會成功|

### 標記
標記與 Etag 不同。標記是名稱-值組的清單，由 Azure 資源管理員在計費或分群用途上用來標示資源。如需「標記」的詳細資訊，請參閱「使用標記來組織您的 Azure 資源」。Azure DNS PowerShell 在區域與記錄集上支援使用選項 ‘-Tag’ 參數來指定「標記」。下列範例示範如何使用兩個標記 ‘project = demo’ 和 ‘env = test’ 建立 DNS 區域：

	PS C:\> New-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGRoup -Tag @( @{ Name="project"; Value="demo" }, @{ Name="env"; Value="test" } )


## 建立 DNS 區域
使用 New-AzureDnsZone Cmdlet 建立 DNS 區域。在下列範例中，我們將在稱為 'MyResourceGroup' 的資源群組中建立稱為 'contoso.com' 的 DNS 區域：<BR>

		PS C:\> New-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGRoup

>[AZURE.NOTE]在 Azure DNS 中，指定的區域名稱結尾不能有 ‘.’。例如，指定為 'contoso.com'，而非 'contoso.com.'。<BR>


現在已在 Azure DNS 中建立 DNS 區域。建立 DNS 區域也會建立下列 DNS 記錄：<BR>



- 「起始點授權」(SOA) 記錄。這出現在每個 DNS 區域的根。
- 授權名稱伺服器 (NS) 記錄。這些顯示哪些名稱伺服器裝載該區域。Azure DNS 使用名稱伺服器集區，因此，不同的名稱伺服器可能會指派至 Azure DNS 中的不同區域。如需詳細資訊，請參閱[將網域委派給 Azure DNS](../dns-domain-delegation)。<BR>

若要檢視這些記錄，請使用 Get-AzureDnsRecordSet：

		PS C:\> Get-AzureDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

	Name              : @
	ZoneName          : contoso.com
	ResourceGroupName : MyResourceGroup
	Ttl               : 3600
	Etag              : 2b855de1-5c7e-4038-bfff-3a9e55b49caf
	RecordType        : SOA
	Records           : {[ns1-01.azure-dns.com,msnhst.microsoft.com,900,300,604800,300]}
	Tags              : {}

	Name              : @
	ZoneName          : contoso.com
	ResourceGroupName : MyResourceGroup
	Ttl               : 3600
	Etag              : 5fe92e48-cc76-4912-a78c-7652d362ca18
	RecordType        : NS
	Records           : {ns1-01.azure-dns.com, ns2-01.azure-dns.net, ns3-01.azure-dns.org,
                  ns4-01.azure-dns.info}
	Tags              : {}

>[AZURE.NOTE]位於 DNS 區域的根 (或「頂點」) 的記錄集使用 "@" 做為記錄集名稱。


建立第一個 DNS 區域之後，您可以使用 nslookup、dig 之類的 DNS 工具或 [Resolve-DnsName PowerShell Cmdlet](https://technet.microsoft.com/zh-tw/library/jj590781.aspx) 進行測試。<BR>

如果您還沒有將網域委派給 Azure DNS 中的新區域，您必須將 DNS 查詢直接導向您的區域的其中一個名稱伺服器。NS 記錄中提供您的區域的名稱伺服器，如上述 Get-AzureDnsRecordSet 所列 — 請務必在下列命令中換成您區域的正確值。<BR>

		C:\> nslookup
		> set type=SOA
		> server ns1-01.azure-dns.com
		> contoso.com

		Server: ns1-01.azure-dns.com
		Address:  208.76.47.1

		contoso.com
        		primary name server = ns1-01.azure-dns.com
        		responsible mail addr = msnhst.microsoft.com
        		serial  = 1
        		refresh = 900 (15 mins)
        		retry   = 300 (5 mins)
        		expire  = 604800 (7 days)
        		default TTL = 300 (5 mins)


## 後續步驟


[開始建立記錄集與記錄](dns-getstarted-create-recordset.md)<BR> [如何管理 DNS 區域](dns-operations-dnszones.md)<BR> [如何管理 DNS 記錄](dns-operations-recordsets.md)<BR> [使用 .NET SDK 自動化 Azure 作業](dns-sdk.md)<BR> [Azure DNS REST API 參考](https://msdn.microsoft.com/library/azure/mt163862.aspx)
 

<!---HONumber=62-->