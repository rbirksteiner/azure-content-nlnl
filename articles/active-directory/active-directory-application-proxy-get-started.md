<properties
	pageTitle="如何為內部部署應用程式提供安全的遠端存取"
	description="涵蓋如何使用 Azure AD 應用程式 Proxy 為您的內部部署應用程式提供安全的遠端存取。"
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="terrylan"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/07/2015"
	ms.author="rkarlin"/>

# 如何為內部部署應用程式提供安全的遠端存取

您想要為擁有各種裝置 (受管理、未受管理；平板電腦、智慧型手機和膝上型電腦) 的遠端使用者提供存取。而且，為大量的資源提供安全的存取可能會很複雜。近年來，反向 Proxy 是提供安全的遠端存取的一種常見方式，但它們必須在難以保護，且難以提供高度可用性的防火牆後面。

## 雲端的安全遠端存取
在現代的雲端環境中，我們用 Microsoft Azure Active Directory 中的應用程式 Proxy，將遠端存取帶到下一個層級。應用程式 Proxy 是 Azure AD 當做服務提供的一個功能，也就是說，部署與使用都非常容易。它整合了 Office 365 所使用的相同身分識別平台，也就是 Azure Active Directory。

## 什麼是 Azure Active Directory 應用程式 Proxy？
應用程式 Proxy 針對 Web 應用程式託管的內部部署，提供單一登入及安全的遠端存取，例如 SharePoint 網站和 Outlook Web Access。您的內部部署 Web 應用程式的存取方式現在與 Azure Active Directory 中的 SaaS 應用程式存取方式相同，不需要 VPN，也不需要變更網路基礎結構。應用程式 Proxy 可讓您發佈應用程式。員工可以從家裡使用自己的裝置登入您的應用程式，並透過這個雲端 Proxy 進行驗證。

## 運作方式
### 啟用存取
應用程式 Proxy 的運作方式是透過在網路內部安裝一個稱為連接器的精簡型 Windows Server 服務。連接器不一定需要開放任何輸入連接埠，而且您不需要在 DMZ 中放置任何內容。如果您的應用程式有大量的流量，您可以新增更多連接器，而且該服務將會負責負載平衡。連接器是無狀態的，而且必要時，會從雲端提取所有內容。當使用者從遠端存取任何裝置上的應用程式時，他會經過 Azure Active Directory 的驗證，並取得應用程式的存取權。

### 單一登入
Azure AD 應用程式 Proxy 為使用 IWA 的應用程式或宣告感知應用程式提供單一登入 (SSO) 功能。如果您的應用程式使用整合式 Windows 驗證，應用程式 Proxy 會模擬使用 Kerberos 限制委派的使用者來提供 SSO。單一登入 (SSO) 宣告感知應用程式是可行的，因為使用者已經過 Azure Active Directory 的驗證。

## 如何開始使用
請確定您有 Azure AD 基本或進階的訂用帳戶，以及您是全域管理員的 Azure AD 目錄。您也需要 Azure AD 基本或進階的授權，目錄系統管理員和使用者才能存取應用程式。如需詳細資訊，請查看這裡。

### 開始為內部部署應用程式啟用遠端存取
設定應用程式 Proxy 是以兩個步驟完成：

1. [啟用應用程式 Proxy 並設定連接器](active-directory-application-proxy-enable.md)<br>
2. [發佈應用程式](active-directory-application-proxy-publish.md)：使用快速且簡單的精靈發佈內部部署應用程式並提供遠端存取。

## 後續步驟
應用程式 Proxy 還有其他更多用途：


- [使用您自己的網域名稱發佈應用程式](https://msdn.microsoft.com/library/azure/mt210927.aspx)
- [啟用單一登入](https://msdn.microsoft.com/library/azure/dn879065.aspx)
- [使用宣告感知應用程式](https://msdn.microsoft.com/library/azure/mt210926.aspx)
- [啟用條件式存取](https://msdn.microsoft.com/library/azure/dn931796.aspx)


### 深入了解應用程式 Proxy
- [看看我們在這裡的線上說明](https://msdn.microsoft.com/library/azure/dn768219.aspx)
- [查閱應用程式 Proxy 部落格](http://blogs.technet.com/b/applicationproxyblog/)
- [觀看我們在 Channel 9 上的影片！](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## 其他資源
* [以組織方式註冊 Azure](../sign-up-organization.md)
* [Azure 身分識別](../fundamentals-identity.md)

<!---HONumber=August15_HO6-->