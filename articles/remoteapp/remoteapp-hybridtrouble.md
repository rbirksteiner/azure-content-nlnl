
<properties 
    pageTitle="建立 RemoteApp 混合式集合疑難排解"
    description="了解如何疑難排解 RemoteApp 混合式集合建立失敗" 
    services="remoteapp" 
    solutions="" documentationCenter="" 
    authors="vkbucha" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="06/30/2015" 
    ms.author="elizapo" />



# 建立 Azure RemoteApp 混合式集合疑難排解

「混合式收藏」裝載於 Azure 雲端中，並在其中儲存資料，但也會讓使用者存取儲存在您區域網路上的資料和資源。使用者可以使用與 Azure Active Directory 同步處理或同盟的公司認證進行登入，以存取應用程式。您可以部署使用現有 Azure 虛擬網路的混合式集合，或者可以建立新的虛擬網路。建議您建立或使用虛擬網路子網路，而其預期 Azure RemoteApp 未來成長的 CIDR 範圍夠大。

是否尚未建立集合？ 如需步驟，請參閱[建立混合式集合](remoteapp-create-hybrid-deployment.md)。

如果在建立集合時發生問題，或集合未如您預期地運作，請參閱下列資訊。

## VNET 是否使用強制通道？ ##
RemoteApp 目前不支援使用已啟用強制通道的 VNET。如果您需要這個功能，請連絡 RemoteApp 小組以要求支援。

核准您的要求之後，請確定在您為 Azure RemoteApp 選擇的子網路以及該子網路的 VM 上開啟下列連接埠。子網路中的 VM 也應該可以存取有關網路安全性群組之小節中提及的 URL。

輸出：TCP：443、TCP：10101-10175

## VNET 是否已定義網路安全性群組？ ##
如果您已在正用於您集合的子網路上定義網路安全性群組，請確定可以在子網路內存取下列 URL：

	https://management.remoteapp.windowsazure.com  
	https://opsapi.mohoro.com  
	https://telemetry.remoteapp.windowsazure.com  
	https://*.remoteapp.windowsazure.com  
	https://login.windows.net (if you have Active Directory)  
	https://login.microsoftonline.com  
	Azure storage *.remoteapp.windowsazure.com  
	*.core.windows.net  
	https://www.remoteapp.windowsazure.com  
	https://www.remoteapp.windowsazure.com  

在虛擬網路子網路上，開啟下列連接埠：

輸入 - TCP：3030、TCP：443 輸出 - TCP：443

您可以將額外網路安全性群組新增至子網路中您所部署的 VM，以進行更嚴格的控制。

## 是否正在使用專屬 DNS 伺服器？ 是否可以從 VNET 子網路存取它們？ ##
對於混合式集合，您使用專屬 DNS 伺服器。您可以在網路組態結構描述中或在建立虛擬網路時透過管理入口網站來指定它們。DNS 伺服器的使用順序就是針對進行容錯移轉所指定的順序 (而非循環配置資源)。

請確定可以從針對這個集合所指定的 VNET 子網路中存取和使用您集合的 DNS 伺服器。

例如：

	<VirtualNetworkConfiguration>
    <Dns>
      <DnsServers>
        <DnsServer name="" IPAddress=""/>
      </DnsServers>
    </Dns>
	</VirtualNetworkConfiguration>

![定義 DNS](./media/remoteapp-hybridtrouble/dnsvpn.png)

如需詳細資訊，請參閱[使用專屬 DNS 伺服器的名稱解析](https://msdn.microsoft.com/library/azure/jj156088.aspx#bkmk_BYODNS)。

## 是否在您的集合中使用 Active Directory 網域控制站？ ##
目前只有一個 Active Directory 網域才能與 Azure RemoteApp 相關聯。混合式集合僅支援已使用 Windows Server Active Directory 部署之 DirSync 工具同步的 Azure Active Directory 帳戶；更具體而言，就是已同步「密碼同步化」選項，或已同步設定 Active Directory Federation Services (AD FS) 同盟。您必須建立一個自訂網域，以符合內部部署之網域的 UPN 網域尾碼，並設定目錄整合。

如需詳細資訊，請參閱[設定 Azure RemoteApp 的 Active Directory](remoteapp-ad.md)。

請確定提供的網域詳細資料有效，而且可以從用於 Azure 遠端應用程式的子網路中所建立的 VM 連線網域控制站。也請確定提供的服務帳戶認證具有將電腦新增至所提供網域的權限，而且可以從 VNET 中所提供的 DNS 解析提供的 AD 名稱。

## 在您建立集合時指定哪個網域名稱？ ##

建立或新增的網域名稱必須是內部網域名稱 (非 Azure AD 網域名稱)，而且必須是可解析的 DNS 格式 (contoso.local)。例如，您有 Active Directory 內部名稱 (contoso.local) 和 Active Directory UPN (contoso.com) - 您必須在建立集合時使用內部名稱。

<!---HONumber=July15_HO1-->