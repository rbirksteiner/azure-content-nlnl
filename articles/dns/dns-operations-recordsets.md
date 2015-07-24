<properties 
   pageTitle="在 Azure DNS 管理 DNS 記錄集和記錄 |Microsoft Azure" 
   description="將網域裝載於 Azure DNS 時，在 Azure DNS 管理 DNS 記錄集和記錄。對記錄集和記錄執行作業的所有 PowerShell 命令。" 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="Adinah" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="en"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="05/01/2015"
   ms.author="joaoma"/>

# 如何管理 DNS 記錄

本指南示範如何管理 DNS 區域的記錄集和記錄。

請務必了解 DNS 記錄集和個別 DNS 記錄之間的差別。記錄集是指一個區域中有相同名稱和相同類型的記錄集合。如需詳細資訊，請參閱[了解記錄集和記錄](../dns-getstarted-create-recordset#Understanding-record-sets-and-records)。

## 建立記錄集

記錄集是使用 New-AzureDnsRecordSet Cmdlet 來建立。您必須指定記錄集名稱、區域、存留時間 (TTL) 和記錄類型。

>[AZURE.NOTE]記錄集名稱必須是相對名稱，不含區域名稱。比方說，區域 'contoso.com' 中的記錄集名稱 'www' 會建立具有完整名稱 'www.contoso.com' 的記錄集。

>針對位於區域頂點的記錄集，請使用 "@"做為記錄集名稱 (包括引號)。記錄集的完整名稱就等於區域名稱，在此案例中為 "contoso.com"。

Azure DNS 支援下列記錄類型： A、AAAA、CNAME、MX、NS、SOA、SRV、TXT。每個區域會自動建立 SOA 類型的記錄集，無法另外建立。

	PS C:\> $rs = New-AzureDnsRecordSet -Name www -Zone $zone -RecordType A -Ttl 300 [-Tag $tags] [-Overwrite] [-Force]

如果記錄集已經存在，則命令會失敗，除非使用 -Overwrite 參數。‘-Overwrite’ 選項會觸發確認提示，但可使用 -Force 參數來隱藏提示。

在上述範例中，使用 Get-AzureDnsZone 或 New-AzureDnsZone 傳回的區域物件來指定區域。或者，您也可以使用區域名稱和資源群組名稱來指定區域：

	PS C:\> $rs = New-AzureDnsRecordSet -Name www –ZoneName contoso.com –ResourceGroupName MyAzureResourceGroup -RecordType A -Ttl 300 [-Tag $tags] [-Overwrite] [-Force]

New-AzureDnsRecordSet 傳回本機物件，代表 Azure DNS 中建立的記錄集。

>[AZURE.NOTE]CNAME 記錄集不能與其他具有相同名稱的記錄集共存。例如，您無法同時建立具有相對名稱 'www' 的 CNAME 和具有相對名稱 'www' 的 A 記錄。因為區域頂點 (名稱 = '@') 一定會包含建立區域時所建立的 NS 和 SOA 記錄集，這表示您無法在區域頂點建立 CNAME 記錄集。這些條件約束源自於 DNS 標準，並不是 Azure DNS 的限制。

## 取得記錄集
若要擷取現有的記錄集，請使用 ‘Get-AzureDnsRecordSet’，並指定記錄集相對名稱、記錄類型和區域：

	PS C:\> $rs = Get-AzureDnsRecordSet -Name www –RecordType A -Zone $zone

如同 New-AzureDnsRecordSet 一樣，記錄集名稱必須是相對名稱，亦即不含區域名稱。您可以使用區域物件 (如上所示) 或使用區域名稱和資源群組名稱來指定區域：

	PS C:\> $rs = Get-AzureDnsRecordSet –Name www –RecordType A -Zonename contoso.com -ResourceGroupName MyAzureResourceGroup

Get-AzureDnsRecordSet 傳回本機物件，代表 Azure DNS 中建立的記錄集。

## 列出記錄集
只要略過 -Name 及/或 –RecordType 參數，Get-AzureDnsRecordSet 也可用來列出記錄集：

### 選項 1 
列出所有記錄集。這會傳回所有記錄集，而不論名稱或記錄類型：

	PS C:\> $list = Get-AzureDnsRecordSet -Zone $zone
### 選項 2 

列出指定記錄類型的記錄集。這會傳回符合指定記錄類型 (此案例中為 A 記錄) 的所有記錄集：

	PS C:\> $list = Get-AzureDnsRecordSet –RecordType A -Zone $zone 

在以上兩個案例中，您可以使用區域物件 (如上所示) 或指定 –ZoneName 和 –ResourceGroupName 參數來指定區域。

## 將記錄加入至記錄集
記錄是使用 Add-AzureDnsRecordConfig Cmdlet 加入至記錄集。這是離線作業 — 只有代表本機物件的記錄集會變更。

將記錄加入至記錄集的參數，根據記錄集的類型而所有不同。比方說，使用 'A' 類型的記錄集時，您只能使用參數 'IPv4Address' 來指定記錄。

您可以額外呼叫 Add-AzureDnsRecordConfig，將更多記錄加入至每個記錄集。任何記錄集最多只能加入 100 筆記錄。不過，CNAME 類型的記錄集最多只能包含 1 筆記錄，一個記錄集不能包含兩筆相同的記錄。您可以建立空的記錄集 (沒有記錄)，但不會出現在 Azure DNS 名稱伺服器上。

一旦記錄集包含所要的一組記錄，就必須使用 Set-AzureDnsRecordSet Cmdlet 認可它，而這會以提供的記錄集取代 Azure DNS 中現有的記錄集。下列範例示範如何建立每一種記錄類型的記錄集 (包含單一記錄)。

### 建立含有單一記錄的 A 記錄集

	PS C:\> $rs = New-AzureDnsRecordSet -Name "test-a" -RecordType A -Zone $zone -Ttl 60
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

用來建立記錄的作業序列也可以「經由管道輸送」，亦即使用管道傳遞記錄集物件，而不是當做參數傳遞。例如：

	PS C:\> New-AzureDnsRecordSet -Name "test-a" -RecordType A -Zone $zone -Ttl 60 | Add-AzureDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureDnsRecordSet

### 建立含有單一記錄的 AAAA 記錄集

	PS C:\> $rs = New-AzureDnsRecordSet -Name "test-aaaa" -RecordType AAAA -Zone $zone -Ttl 60
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

### 建立含有單一記錄的 CNAME 記錄集

	PS C:\> $rs = New-AzureDnsRecordSet -Name "test-cname" -RecordType CNAME -Zone $zone -Ttl 60
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

### 建立含有單一記錄的 MX 記錄集
此範例會使用記錄集名稱 "@"，在區域頂點 (例如 "contoso.com") 建立 MX 記錄。對於 MX 記錄而言，這很常見。

	PS C:\> $rs = New-AzureDnsRecordSet -Name "@" -RecordType MX -Zone $zone -Ttl 60
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

### 建立含有單一記錄的 NS 記錄集

	PS C:\> $rs = New-AzureDnsRecordSet -Name "test-ns" -RecordType NS -Zone $zone -Ttl 60
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs
### 建立含有單一記錄的 SRV 記錄集

如果在區域的根部建立 SRV 記錄，只需要在記錄名稱中指定 _service 和 _protocol — 記錄名稱不需要同時包含 '.@'

	PS C:\> $rs = New-AzureDnsRecordSet -Name "_sip._tls" -RecordType SRV -Zone $zone -Ttl 60
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs –Priority 0 –Weight 5 –Port 8080 –Target "sip.contoso.com"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

### 建立含有單一記錄的 TXT 記錄集

	PS C:\> $rs = New-AzureDnsRecordSet -Name "test-txt" -RecordType TXT -Zone $zone -Ttl 60
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

## 修改現有的記錄集
修改現有記錄集所遵循的模式與建立記錄類似。作業序列如下：

1.	使用 Get-AzureDnsRecordSet 擷取現有的記錄集。
2.	修改記錄集，包括加入記錄、移除記錄、變更記錄參數或變更記錄集 TTL。這些變更是離線進行 — 只有代表記錄集的本機物件會變更。
3.	使用 Set-AzureDnsRecordSet Cmdlet 認可您所做的變更。這會以提供的記錄集取代 Azure DNS 中現有的記錄集。

下列範例示範作法：

### 更新現有記錄集的記錄
在此範例中，我們變更現有 A 記錄的 IP 位址：

	PS C:\> $rs = Get-AzureDnsRecordSet -name "test-a" -RecordType A -Zone $zone 
	PS C:\> $rs.Records[0].Ipv4Address = "134.170.185.46"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs 

Set-AzureDnsRecordSet Cmdlet 使用 ‘etag’ 檢查，以確保不會覆寫並行變更。使用 ‘-Overwrite’ 旗標來停用這些檢查。如需詳細資訊，請參閱「Etag 和標記」。

### 修改 SOA 記錄

>[AZURE.NOTE]您無法從區域頂點 (名稱 = '@') 自動建立的 SOA 記錄集加入或移除記錄，但是您可以修改 SOA 記錄內的參數和記錄集 TTL。

下列範例示範如何變更 SOA 記錄的 'Email' 屬性：

	PS C:\> $rs = Get-AzureDnsRecordSet -Name "@" -RecordType SOA -Zone $zone
	PS C:\> $rs.Records[0].Email = "admin.contoso.com"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs 

### 在區域頂點修改 NS 記錄

>[AZURE.NOTE]您無法在區域頂點 (名稱 = '@') 自動建立的 NS 記錄集加入、移除或修改記錄。修改記錄集 TTL 是唯一允許的變更。

下列範例示範如何變更 NS 記錄集的 TTL 屬性：

	PS C:\> $rs = Get-AzureDnsRecordSet -Name "@" -RecordType NS -Zone $zone
	PS C:\> $rs.Ttl = 300
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs 

### 將記錄加入至現有的記錄集
在此範例中，我們將兩個額外的 MX 記錄加入至現有的記錄集：

	PS C:\> $rs = Get-AzureDnsRecordSet -name "test-mx" -RecordType MX -Zone $zone
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Exchange "mail2.contoso.com" -Preference 10
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Exchange "mail3.contoso.com" -Preference 20
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs 

## 從現有的記錄集移除記錄

您可以使用 Remove-AzureDnsRecordConfig 從記錄集移除記錄。請注意，要移除的記錄必須完全符合現有的資料錄，包括所有參數。必須使用 Set-AzureDnsRecordSet 認可變更。

移除記錄集的最後一筆記錄不會刪除記錄集。如需詳細資訊，請參閱下方的[刪除記錄集](#delete-a-record-set)。


	PS C:\> $rs = Get-AzureDnsRecordSet -Name "test-a" -RecordType A –Zone $zone
	PS C:\> Remove-AzureDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

用來從記錄集移除記錄的作業序列也可以「經由管道輸送」，亦即使用管道傳遞記錄集物件，而不是當做參數傳遞。例如：

	PS C:\> Get-AzureDnsRecordSet -Name "test-a" -RecordType A -Zone $zone | Remove-AzureDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureDnsRecordSet
### 從記錄集移除 AAAA 記錄

	PS C:\> $rs = Get-AzureDnsRecordSet -Name "test-aaaa" -RecordType AAAA –Zone $zone
	PS C:\> Remove-AzureDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

### 從記錄集移除 CNAME 記錄

因為 CNAME 記錄集最多只能包含一筆記錄，移除該記錄會留下空的記錄集。

	PS C:\> $rs =  Get-AzureDnsRecordSet -name "test-cname" -RecordType CNAME –Zone $zone	
	PS C:\> Remove-AzureDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

### 從記錄集移除 MX 記錄

	PS C:\> $rs = Get-AzureDnsRecordSet -name "test-mx" -RecordType 'MX' –Zone $zone	
	PS C:\> Remove-AzureDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

### 從記錄集移除 NS 記錄
	
	PS C:\> $rs = Get-AzureDnsRecordSet -Name "test-ns" -RecordType NS -Zone $zone
	PS C:\> Remove-AzureDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

### 從記錄集移除 SRV 記錄

	PS C:\> $rs = Get-AzureDnsRecordSet -Name "_sip._tls" -RecordType SRV -Zone $zone
	PS C:\> Remove-AzureDnsRecordConfig -RecordSet $rs –Priority 0 –Weight 5 –Port 8080 –Target "sip.contoso.com"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

### 從記錄集移除 TXT 記錄

	PS C:\> $rs = Get-AzureDnsRecordSet -Name "test-txt" -RecordType TXT -Zone $zone
	PS C:\> Remove-AzureDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

## 刪除記錄集
您可以使用 Remove-AzureDnsRecordSet Cmdlet 刪除記錄集。

>[AZURE.NOTE]您無法在建立區域時所自動建立的區域頂點 (名稱 = ‘@’) 刪除 SOA 和 NS 記錄集。刪除區域時會自動刪除它們。

使用下列三種方式之一來移除記錄集：
### 選項 1
依名稱指定所有參數：

	PS C:\> Remove-AzureDnsRecordSet -Name "test-a" -RecordType A -Zonename "contoso.com" -ResourceGroupName MyAzureResourceGroup [-Force]
選擇性的 ’-Force’ 參數可用來隱藏確認提示。

### 選項 2
依名稱和類型指定記錄集，依物件指定區域：

	PS C:\> Remove-AzureDnsRecordSet -Name "test-a" -RecordType A -Zone $zone [-Force]

### 選項 3
依物件指定記錄集：

	PS C:\> Remove-AzureDnsRecordSet –RecordSet $rs [-Overwrite] [-Force]

使用物件指定記錄集可啟用 'etag' 檢查，以確保不會刪除並行的變更。選擇性的 ‘-Overwrite’ 旗標可停用這些檢查。如需詳細資訊，請參閱 [Etag 和標記](../dns-getstarted-create-dnszone#Etags-and-tags)。

記錄集物件也可以經由管道輸送，而不是當做參數傳遞：

	PS C:\> Get-AzureDnsRecordSet -Name "test-a" -RecordType A -Zone $zone | Remove-AzureDnsRecordSet [-Overwrite] [-Force]

##另請參閱

[開始建立記錄集和記錄](../dns-getstarted-create-recordset)<BR> [在 DNS 區域上執行作業](../dns-operations-dnszones)<BR> [使用 .NET SDK 將作業自動化](../dns-sdk)
 

<!---HONumber=62-->