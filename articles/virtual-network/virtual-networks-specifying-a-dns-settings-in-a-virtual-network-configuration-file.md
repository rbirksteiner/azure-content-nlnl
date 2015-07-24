<properties 
   pageTitle="指定虛擬網路組態檔中的 DNS 設定"
   description="說明"
   services="virtual-network"
   documentationCenter="na"
   authors="joaoma"
   manager="jdial"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/28/2015"
   ms.author="joaoma" />

# 指定虛擬網路組態檔中的 DNS 設定

網路組態檔有兩種您可以用來指定網域名稱系統 (DNS) 設定的項目：**DnsServers** 和 **DnsServerRef**。您可以藉由指定其 IP 位址並參考 **DnsServers** 項目的名稱，新增 DNS 伺服器的清單。然後您可以使用 **DnsServerRef** 項目，來針對您虛擬網路內的不同網站，指定要使用 DnsServers 項目中哪些 DNS 伺服器項目。

>[AZURE.IMPORTANT]如需如何設定網路組態檔的相關資訊，請參閱[使用網路組態檔設定虛擬網路](https://msdn.microsoft.com/library/azure/jj156097.aspx)。如需網路組態檔中所包含之各個項目的相關資訊，請參閱 [Azure 虛擬網路組態結構描述](https://msdn.microsoft.com/library/azure/jj157100.aspx)。

網路組態檔可能包含下列項目。每個項目的標題會連結至提供項目值設定之其他相關資訊的網頁。

[Dns 項目](http://go.microsoft.com/fwlink/?LinkId=248093)

    <Dns>
      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>
    </Dns>

>[AZURE.WARNING]**DnsServer** 項目中的 **name** 屬性僅做為 **DnsServerRef** 項目的參考。它不代表 DNS 伺服器的主機名稱。每個 **DnsServer** 屬性值在整個 Microsoft Azure 訂用帳戶中必須是唯一的。

[Virtual Network Sites 項目](http://go.microsoft.com/fwlink/?LinkId=248093)

	<DnsServersRef>
	  <DnsServerRef name="ID1" />
	  <DnsServerRef name="ID2" />
	  <DnsServerRef name="ID3" />
	</DnsServersRef>

>[AZURE.NOTE]若要指定 Virtual Network Sites 項目的這項設定，先前在 DNS 項目中必須定義它。Virtual Network Sites 項目中的 DnsServerRef *name* 必須參考 DnsServer *name* 的 DNS 項目中指定的名稱值。

## 另請參閱

[使用網路組態檔設定虛擬網路](http://go.microsoft.com/fwlink/?LinkId=248094)

[Azure 虛擬網路組態結構描述](http://go.microsoft.com/fwlink/?LinkId=248093)

[Azure 服務組態結構描述](https://msdn.microsoft.com/library/windowsazure/ee758710)

<!---HONumber=July15_HO2-->