<properties 
	pageTitle="什麼是 RemoteApp？" 
	description="了解 Azure RemoteApp。" 
	services="remoteapp" 
	documentationCenter="" 
	authors="lizap" 
	manager="mbaldwin" 
	editor=""/>

<tags 
	ms.service="remoteapp" 
	ms.workload="compute" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/30/2015" 
	ms.author="elizapo"/>

# 什麼是 Azure RemoteApp？

Azure RemoteApp 可將遠端桌面服務所支援的內部部署 Microsoft RemoteApp 程式功能提供給 Azure。Azure RemoteApp 可協助您提供安全的遠端存取方式，讓許多不同的使用者裝置可以存取應用程式。

當您將 RemoteApp 移至 Azure 時，就可以利用 Azure 的儲存體、延展性和全球市場，而不必擔心複雜的內部部署組態。Microsoft 提供 Azure 的維護，以確保其可靠性，讓您得以專注於處理更重要的問題，例如建立最佳應用程式以供您的企業使用。Azure RemoteApp 的另一個優點是可存取性 - 您的使用者可以從 Windows、iOS、Mac OS X 和 Android 裝置存取 RemoteApp 程式。他們可以在自己偏好的環境中使用您的應用程式，而您會使用 Azure 管理入口網站來管理這些應用程式。

請閱讀更多有關 RemoteApp 的詳細資訊，或者如果我們已經說服您，請[立即試用](http://azure.microsoft.com/services/remoteapp/)。

有關於 Azure RemoteApp 的問題嗎？ 請查看我們的[常見問題集](remoteapp-faq.md)。

Azure RemoteApp 屬於 [Microsoft 虛擬桌面基礎結構](http://www.microsoft.com/server-cloud/products/virtual-desktop-infrastructure/explore.aspx)。

**新功能！** 想要深入了解 Azure RemoteApp 嗎？ 或準備要驗證 RemoteApp？ 請加入我們每週的[專家請益網路研討會](https://azureinfo.microsoft.com/AzureRemoteAppAskTheExperts-Registration-Page.html?ls=Website)。

## RemoteApp 收藏
RemoteApp 收藏分成兩種：


- **雲端收藏**由 Azure 雲端代管，並將所有程式資料儲存在此雲端。使用者可以使用其 Microsoft 帳戶或與 Azure Active Directory 同步處理或同盟的公司認證進行登入，以存取應用程式。
- **混合式收藏**由 Azure 雲端代管，並在其中儲存資料，但也會讓使用者存取儲存在您區域網路上的資料和資源。使用者可以使用與 Azure Active Directory 同步處理或同盟的公司認證進行登入，以存取應用程式。

### 雲端收藏

[雲端 RemoteApp 收藏](remoteapp-create-cloud-deployment.md)提供在雲端中代管應用程式的獨立方法。雲端收藏只存在 Azure 雲端中，而不是連接到您的區域網路。

在 RemoteApp 試用版中，我們提供預先安裝的 Office 365 ProPlus 或 Office 2013 應用程式，而且您可以和您的使用者共用。如果您選擇利用可用的軟體，即可快速佈建您的服務。

使用含有 Office 應用程式的雲端收藏有另一項好處，您建置服務所在的應用程式和作業系統永遠可透過定期更新保持最新狀態，Microsoft 反惡意程式碼端點保護可提供不間斷的防護。您的使用者可使用其 Microsoft 帳戶或公司認證來存取應用程式。身為系統管理員的您只需擔心如何找出誰應該存取哪些應用程式。

您也可以為使用者建立雲端收藏來共用某個自訂應用程式或一組應用程式。若要這樣做，您需要[建立自訂映像](remoteapp-imageoptions.md) (這是我們將應用程式發佈到 RemoteApp 的方法)，而且只要在建立收藏時選擇該映像 (而非 Office 2013 映像) 即可。

#### 選擇雲端收藏的時機

當您想要共用的應用程式不需要連接至貴公司私人網路上的任何資源時 (例如，透過 VPN 裝置)，請選擇雲端收藏。如果應用程式使用網際網路、OneDrive 或 Azure 上的資源，則雲端收藏將適用於您。它也是最快建立的。


### 混合式收藏
[混合式 RemoteApp 收藏](remoteapp-create-hybrid-deployment.md)可讓您提供自訂的一組應用程式給使用者，並且提供您區域網路中資料和資源的存取權。不同於雲端收藏使用的自訂映像，您為混合式收藏建立的映像是在加入網域的環境中執行，因此會授與您區域網路和資料的完整存取權。

藉由整合 Active Directory 與 Azure Active Directory (使用 DirSync)，您的使用者可以使用其公司認證來存取應用程式和資料。當您在 Active Directory 中使用工作帳戶時，您可以將您的公司政策帶入雲端，以控制您透過 RemoteApp 提供的應用程式。

只要您使用 RD 工作階段主機角色服務在 Windows Server 2012 R2 上建置您的範本映像，您可以為使用者發佈的應用程式會有一些限制。如果應用程式在該範本映像環境中運作正常，使用者即可透過 RemoteApp 加以存取。

#### 選擇混合式的時機

如果您需要連接至貴公司私人網路上的資源，請選擇混合式收藏。比方說，如果應用程式需要存取下列其中一項：

- 位於內部網路上的檔案伺服器
- Quicken
- 防火牆後面的資料庫

這通常對私人網路上有大量資源無法移至雲端的大型公司很有用。

### 升級收藏
混合式與雲端收藏之間的一個主要差異，在於軟體更新的處理方式。對於使用預先安裝之 Office 365 ProPlus 或 Office 2013 映像的雲端收藏，您不需擔心任何更新。此服務會自行維護並持續推出應用程式和作業系統的更新。

對於混合式收藏以及使用自訂範本映像的雲端收藏，您必須負責維護映像和應用程式。對於已加入網域的映像，您可以使用 Windows Update、群組原則或 System Center 等工具控制更新。

升級自訂的範本映像後，必須將新映像上傳至 Azure 雲端，然後更新收藏以使用新映像。(您可以從 RemoteApp [快速入門] 頁面或 [儀表板] 執行此作業。)

如需詳細資訊，請參閱 [更新您的收藏](remoteapp-update.md)。

## 支援的 RemoteApp 用戶端
Windows 和 Windows RT 的 RemoteApp 用戶端應用程式以及 Mac、iOS 和 Android 適用的 Microsoft 遠端桌面應用程式均可支援 Azure RemoteApp。您的使用者可以在其行動或運算裝置上使用這些應用程式，以存取新的 RemoteApp 程式。

如需用戶端的詳細資訊，請參閱[在 Azure RemoteApp 存取您的應用程式](remoteapp-clients.md)。

## 後續步驟
快！ 立即試用！ 這些文章可幫助您開始使用 RemoteApp：

- [建立 RemoteApp 映像](remoteapp-imageoptions.md)
- [如何建立 RemoteApp 的雲端收藏](remoteapp-create-cloud-deployment.md)
- [如何建立 RemoteApp 的混合式收藏](remoteapp-create-hybrid-deployment.md)
- [RemoteApp 中的授權如何運作？](remoteapp-licensing.md)
- [使用 Azure RemoteApp 的最佳作法](remoteapp-bestpractices.md)
- [Azure RemoteApp 常見問題集](remoteapp-faq.md)
 

<!---HONumber=July15_HO2-->