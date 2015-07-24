<properties
   pageTitle="建立 DNS 區域的記錄集和記錄 | Microsoft Azure"
   description="如何建立 Azure DNS 的主機記錄。使用 PowerShell 設定記錄集和記錄"
   services="dns"
   documentationCenter="na"
   authors="joaoma"
   manager="Adinah"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/01/2015"
   ms.author="joaoma"/>


# 建立 DNS 記錄

建立 DNS 區域之後，您必須加入網域的 DNS 記錄。若要這樣做，您必須先了解 DNS 記錄和記錄集。


## 了解記錄集和記錄
每一筆 DNS 記錄都有名稱和類型。

_完整_名稱包含區域名稱，而_相對_名稱不含區域名稱。比方說，區域 'contoso.com' 中的相對記錄名稱 'www' 就給出完整記錄名稱 'www.contoso.com'。

>[AZURE.NOTE]在 Azure DNS 中，記錄是使用相對名稱來指定。

記錄根據所包含的資料而有各種類型。最常見的類型為 'A' 記錄，可將名稱對應到 IPv4 位址。另一個類型為 'MX' 記錄，可將名稱對應到郵件伺服器。

Azure DNS 支援所有常見的 DNS 記錄類型：A、AAAA、CNAME、MX、NS、SOA、SRV 和 TXT。

有時候，您需要以指定的名稱和類型建立多筆 DNS 記錄。例如，假設 www.contoso.com 網站裝載於兩個不同的 IP 位址。這需要兩筆不同的 A 記錄，每個 IP 位址各一筆：

	www.contoso.com.		3600	IN	A	134.170.185.46
	www.contoso.com.		3600	IN	A	134.170.188.221

這是記錄集的範例。記錄集是指一個區域中有相同名稱和相同類型的 DNS 記錄集合。大部分的記錄集只包含單一記錄，但像以上的記錄集包含多筆記錄的例子也屢見不鮮。(SOA 和 CNAME 類型的記錄集是例外，DNS 標準不允許這些類型有多筆相同名稱的記錄。)

存留時間 (TTL) 指定每一筆記錄由用戶端快取多久，之後才會重新查詢。在上述範例中，TTL 是 3600 秒 (1 小時)。TTL 是針對記錄集而指定，而非針對每一筆記錄，因此相同的值套用至該記錄集內的所有記錄。

>[AZURE.NOTE]Azure DNS 使用記錄集來管理 DNS 記錄。



## 建立記錄集和記錄

下列範例將示範如何建立記錄集與記錄。我們將使用 DNS 'A' 記錄類型，如需其他記錄類型，請參閱[如何管理 DNS 記錄](dns-operations-recordsets.md)


### 步驟 1

建立記錄集，並指派給變數 $rs：

	PS C:\>$rs = New-AzureDnsRecordSet -Name "www" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 60

記錄集在 DNS 區域 'contoso.com' 中有相對記錄名稱 'www'，因此記錄的完整名稱為 'www.contoso.com'。記錄類型為 'A'，TTL 為 60 秒。

>[AZURE.NOTE]若要在區域頂點 (在此案例中為 'contoso.com') 建立記錄集，請使用記錄名稱 "@" (包括引號)。這是常見的 DNS 慣例。

這個記錄集是空的，而我們必須新增記錄，才能使用新建立的 "www" 記錄集。<BR>

### 步驟 2

使用在步驟 1 建立記錄集時所指派的 $rs 變數，將 IPv4 A 記錄加入至 "www" 記錄集：

	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.185.46
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.188.221

使用 Add-AzureDnsRecordConfig 將記錄加入至記錄集是離線作業。只有本機變數 $rs 會更新。

### 步驟 3
認可對記錄集所做的變更。使用 Set-AzureDnsRecordSet 將記錄集的變更上傳到 Azure DNS：


	Set-AzureDnsRecordSet -RecordSet $rs

變更已完成。您可以使用 Get-AzureDnsRecordSet，從 Azure DNS 擷取記錄集。


	PS C:\> Get-AzureDnsRecordSet –Name www –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup


	Name              : www
	ZoneName          : contoso.com
	ResourceGroupName : MyAzureResourceGroup
	Ttl               : 3600
	Etag              : 68e78da2-4d74-413e-8c3d-331ca48246d9
	RecordType        : A
	Records           : {134.170.185.46, 134.170.188.221}
	Tags              : {}



您也可以使用 nslookup 或其他 DNS 工具來查詢新的記錄集。

>[AZURE.NOTE]如同建立區域時一樣，如果您還沒有將網域委派給 Azure DNS 名稱伺服器，則必須明確指定區域的名稱伺服器位址。


	C:\> nslookup www.contoso.com ns1-01.azure-dns.com

	Server: ns1-01.azure-dns.com
	Address:  208.76.47.1

	Name:    www.contoso.com
	Addresses:  134.170.185.46
    	        134.170.188.221



## 後續步驟
[如何管理 DNS 區域](dns-operations-dnszones.md)

[如何管理 DNS 記錄](dns-operations-recordsets.md)<BR>

[使用 .NET SDK 自動化 Azure 作業](dns-sdk.md)
 

<!---HONumber=62-->