<properties 
   pageTitle="建立 Web 應用程式的自訂 DNS 記錄 | Microsoft Azure" 
   description="如何使用 Azure DNS 來建立 Web 應用程式的自訂網域 DNS 記錄。使用 CNAME 或 A 記錄，逐步確認網域擁有權" 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="carolz" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="05/20/2015"
   ms.author="joaoma"/>

# 在自訂網域中建立 Web 應用程式的 DNS 記錄

您可以使用 Azure DNS 來裝載 Web 應用程式的自訂網域。比方說，假設您正在建立 Azure Web 應用程式，而且想要使用者透過使用 contoso.com 或 www.contoso.com 做為 FQDN 進行存取。在此案例中，您必須建立兩筆記錄：一筆指向 contoso.com 的根 A 記錄，和一筆 www 名稱、指向 A 記錄的 CNAME 記錄。

> [AZURE.NOTE]請記住，如果您在 Azure 中建立 Web 應用程式的 A 記錄，如果 Web 應用程式的基礎 IP 位址變更，則您必須手動更新 A 記錄。

為您的自訂網域建立記錄之前，您必須在 Azure DNS 中建立 DNS 區域，並將註冊機構中的區域委派給 Azure DNS。若要建立 DNS 區域，請依照[開始使用 Azure DNS](../dns-getstarted-create-dnszone/#Create-a-DNS-zone) 中的步驟進行。若要將 DNS 委派給 Azure DNS，請依照[將網域委派給 Azure DNS](../dns-domain-delegation) 中的步驟進行。
 
## 建立自訂網域的 A 記錄

A 記錄可用來將名稱對應到其 IP 位址。在下列範例中，我們會將 @ 做為 A 記錄指派給 IPv4 位址：

### 步驟 1
 
建立 A 記錄，並指派給變數 $rs
	
	PS C:\>$rs=New-AzureDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 600 

### 步驟 2

使用指派的 $rs 變數，將 IPv4 值新增至先前建立的記錄集 "@"。指派的 IPv4 值將是您 Web 應用程式的 IP 位址。

> [AZURE.NOTE]若要尋找 Web 應用程式的 IP 位址，請依照[在 Azure App Service 中設定自訂網域名稱](../web-sites-custom-domain-name/#Find-the-virtual-IP-address)中的步驟進行

	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Ipv4Address <your web app IP address>

### 步驟 3

認可對記錄集所做的變更。使用 Set-AzureDnsRecordSet 將記錄集的變更上傳到 Azure DNS：

	Set-AzureDnsRecordSet -RecordSet $rs

## 新增自訂網域的 CNAME 記錄

假設您的網域已受 Azure DNS 管理 (請參閱 [DNS 網域委派](../dns-domain-delegation))，您可以使用下列範例，建立 contoso.azurewebsites.net 的 CNAME 記錄：

### 步驟 1

開啟 powershell 並建立新的 CNAME 記錄集，然後指派給變數 $rs：

	PS C:\> $rs = New-AzureDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "www" -RecordType "CNAME" -Ttl 600
 
	Name              : www
	ZoneName          : contoso.com
	ResourceGroupName : myresourcegroup
	Ttl               : 600
	Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
	RecordType        : CNAME
	Records           : {}
	Tags              : {}

這會在名為 "contoso.com" 的DNS 區域中建立一個 [存留時間] 為 600 秒的記錄集類型 CNAME。

### 步驟 2

一旦建立 CNAME 記錄集，您需要建立將指向 Web 應用程式的別名值。

使用先前指派的變數 "$rs"，您可以使用下列 PowerShell 命令來建立 Web 應用程式 contoso.azurewebsites.net 的別名。

	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Cname "contoso.azurewebsites.net"
 
	Name              : www
	ZoneName          : contoso.com
	ResourceGroupName : myresourcegroup
	Ttl               : 600
	Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
	RecordType        : CNAME
	Records           : {contoso.azurewebsites.net}
	Tags              : {}

### 步驟 3

使用 Set-AzureDnsRecordSet Cmdlet 確認所做的變更：

	PS C:\>Set-AzureDnsRecordSet -RecordSet $rs

您可以使用 nslookup 來驗證由查詢 "www.contoso.com" 正確建立的記錄，如下所示：

	PS C:\> nslookup
	Default Server:  Default
	Address:  192.168.0.1
 
	> www.contoso.com
	Server:  default server
	Address:  192.168.0.1
	 
	Non-authoritative answer:
	Name:    <instance of web app service>.cloudapp.net
	Address:  <ip of web app service>
	Aliases:  www.contoso.com
    contoso.azurewebsites.net
    <instance of web app service>.vip.azurewebsites.windows.net

## 建立 Web Apps 的 awverify 記錄 (僅限 A 記錄)

如果您決定使用 Web 應用程式的 A 記錄，您必須通過驗證程序，才能允許 Azure 確保您擁有自訂網域。此驗證步驟可透過建立名為 "awverify" 的特殊 CNAME 記錄來完成。

在下列範例中將會建立 contoso.com 的 "awverify" 記錄，以驗證自訂網域的擁有權：

### 步驟 1

	PS C:\> $rs = New-AzureDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "awverify" -RecordType "CNAME" -Ttl 600
 
	Name              : awverify
	ZoneName          : contoso.com
	ResourceGroupName : myresourcegroup
	Ttl               : 600
	Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
	RecordType        : CNAME
	Records           : {}
	Tags              : {}


### 步驟 2

建立記錄集 awverify 之後，您必須對 awverify.contoso.azurewebsites.net 指派 CNAME 記錄集別名，如下列命令所示：

	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Cname "awverify.contoso.azurewebsites.net"
 
	Name              : awverify
	ZoneName          : contoso.com
	ResourceGroupName : myresourcegroup
	Ttl               : 600
	Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
	RecordType        : CNAME
	Records           : {awverify.contoso.azurewebsites.net}
	Tags              : {}

### 步驟 3

使用 Set-AzureDnsRecordSet Cmdlet 確認所做的變更，如下列命令所示：

	PS C:\>Set-AzureDnsRecordSet -RecordSet $rs

現在您可以繼續依照[設定 App Service 的自訂網域名稱](../web-sites-custom-domain-name)中的步驟進行，以設定 Web 應用程式使用自訂網域。

## 另請參閱

[管理 DNS 區域](../dns-operations-dnszones)

[管理 DNS 記錄](../dns-operations-recordsets)

[流量管理員概觀](../traffic-manager-overview)

[使用 .NET SDK 自動化 Azure 作業](../dns-sdk)


 

<!---HONumber=62-->