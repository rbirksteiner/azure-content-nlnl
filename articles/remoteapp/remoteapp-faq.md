<properties 
	pageTitle="Azure RemoteApp 常見問題集" 
	description="關於 Azure RemoteApp 的常見問題。" 
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

# Azure RemoteApp 常見問題集
我們已聽見下列關於 Azure RemoteApp 的問題。還有其他問題嗎？ 請瀏覽 [RemoteApp 論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureRemoteApp)，讓我們知道您需要知道些什麼。

## 什麼是 Azure RemoteApp？ ##


- **什麼是 Azure RemoteApp？** RemoteApp 是一種 Azure 服務，可從 Azure 提供遠端桌面服務所支援的內部部署 Microsoft RemoteApp 功能。RemoteApp 可協助您提供安全的遠端存取方式，讓許多不同的使用者裝置可以存取應用程式。深入了解 [Azure RemoteApp](remoteapp-whatis.md)。
- **有哪兩種部署選項？** 有兩種 RemoteApp 部署 (或收藏)：雲端和混合式。找出哪一種[部署選項](remoteapp-whatis.md)最適合您的組織。

## 支援的組態 ##

- **服務限制有哪些？** 我們剛加入新的主題，涵蓋所有[服務限制及預設值](remoteapp-servicelimits.md)。讓我們知道您是否有更多的問題。
- **我必須有多少個使用者？** 至少 20 個使用者。容我再次重複 - 至少 20 個。您將為 20 個使用者付費。請在這裡查看其他[使用者限制及預設值](remoteapp-servicelimits.md)。
- **是否支援自訂的企業營運 (LOB) 應用程式？** 是。若要在 Azure RemoteApp 中使用自訂應用程式，請建立[自訂範本映像](remoteapp-create-custom-image.md)，然後將其上傳至 RemoteApp 收藏。
- **我自訂的 LOB 應用程式能在 Azure RemoteApp 中運作嗎？** 最好的方式就是進行測試。檢閱[應用程式相容性需求](http://www.microsoft.com/download/details.aspx?id=18704)並查看 [RD 相容性中心](http://www.rdcompatibility.com/compatibility/default.aspx)。
- **哪一種部署方式 (雲端或混合式) 最適合我的組織？** 如果您想要與單一登入 (SSO) 完整整合，而且有安全的內部部署網路連線，混合式收藏能提供最完整的體驗。雲端收藏使用多種驗證方法，提供靈活簡單的方式隔離您的部署。深入了解[部署選項](remoteapp-whatis.md)。
- **混合式收藏需要 VNET。我們可以使用現有的 VNET 嗎？** 可以，如果現有的 VNET 為 Azure VNET 的話。如需詳細資訊，請參閱[混合式收藏指示](remoteapp-create-hybrid-deployment.md)中的「步驟 1：設定虛擬網路」。
- **我可以使用雲端或現有的虛擬機器作為 RemoteApp 收藏的範本嗎？** 可以！ 您可以根據 Azure VM 建立映像、使用訂用帳戶中包含的其中一個映像，或者建立自訂映像。查看 [RemoteApp 映像選項](remoteapp-imageoptions.md)。
- **我們在內部部署或 Azure 中有 SQL 或其他資料庫。我們應使用何種部署類型？** 這取決於您的 SQL 或後端資料庫的所在位置。如果資料庫在私人網路中，請使用混合式收藏。如果資料庫公開於網際網路上並可供用戶端連線，您可以使用雲端收藏。
- **磁碟機對應、USB 和序列埠、剪貼簿共用，以及印表機重新導向呢？** Azure RemoteApp 中都支援上述所有功能。預設會啟用剪貼簿共用和印表機重新導向。您可以在[這裡](remoteapp-redirection.md)深入了解重新導向。 


- **驗證呢？ 支援何種方法？** 雲端收藏支援 Microsoft 帳戶與 Azure Active Directory 帳戶，而其同時也是 Office 365 帳戶。混合式收藏僅支援已從 Windows Server Active Directory 部署同步 (使用類似 [Azure Active Directory 同步作業](http://blogs.technet.com/b/ad/archive/2014/09/16/azure-active-directory-sync-is-now-ga.aspx)的工具) 的 Azure Active Directory 帳戶；更具體而言，就是同步時使用 [密碼同步] 選項，或是同步時已設定 Active Directory Federation Services (AD FS) 同盟。您也可以設定[多重要素驗證 (MFA)](../../services/multi-factor-authentication/)。

	**注意：**Azure Active Directory 使用者必須來自於與您的訂用帳戶相關聯的租用戶。(您可以在入口網站的 [設定] 索引標籤上檢視和修改您的訂用帳戶。如需詳細資訊，請參閱[變更 RemoteApp 所使用的 Azure Active Directory 租用戶](remoteapp-changetenant.md)。)

- **為什麼我不能授與我的 Azure Active Directory 帳戶存取權？** Azure Active Directory 使用者必須來自於與您的訂用帳戶相關聯的目錄。您可以在入口網站的 [設定] 索引標籤上檢視或修改該目錄。如需詳細資訊，請參閱[變更 RemoteApp 所使用的 Azure Active Directory 租用戶](remoteapp-changetenant.md)。
- **用戶端應用程式支援哪些裝置和作業系統？** Windows 8.1、Windows 8、Windows 7 Service Pack 1、iOS、Mac OS X、Windows RT、Android 裝置和 Windows Phone 都可以使用用戶端應用程式。我們也支援 Windows 10 預覽版。
 
	立即[下載](https://www.remoteapp.windowsazure.com/ClientDownload/AllClients.aspx) RemoteApp 用戶端。
- **Azure RemoteApp 是否支援精簡型用戶端？** 是，支援下列 Windows Embedded 精簡型用戶端：
	- Windows Embedded Standard 7 with Service Pack 1
	- Windows Embedded POSReady7 
	- Windows Embedded Thin PC 
	- Windows Embedded 8.1 Industry

- **遠端桌面工作階段主機 (RDSH) 支援哪一版的 Windows Server？** Windows Server 2012 R2。

##支援與意見反應

- **我能免費試用這項服務嗎？** 是。免費試用期有 30 天。試用結束之後，您可以轉換到付費帳戶 (用於生產環境中) 或停止使用服務。請前往 [manage.windowsazure.com](http://manage.windowsazure.com) 開始您的免費試用- 建立新的 RemoteApp 執行個體。使用免費試用版，可以建立 2 個 RemoteApp 執行個體，每個執行個體 10 個使用者。請記住這個試用期只有 30 天。
- **RemoteApp 的支援計畫為何？** 計費及訂用帳戶管理支援均為免費提供。透過 [Azure 服務方案](../../../support/plans/)可以使用技術支援。您也可以透過我們的 [Azure 討論區論壇](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=AzureRemoteApp)獲得免費的社群支援。 
- **RemoteApp 的價格為何？** 請查看 [Azure RemoteApp 價格詳細資料](../../../pricing/details/remoteapp/)。
- **如何提交意見反應？** 請瀏覽[意見反應論壇](http://feedback.azure.com/forums/247748-azure-remoteapp)。
- **我可以和誰討論更多 Azure RemoteApp 的資訊？** 除了我們的[討論區論壇](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=AzureRemoteApp) (這是張貼問題的最佳去處) 外，您還可加入每週的[專家請益網路研討會](https://azureinfo.microsoft.com/US-Azure-WBNR-FY15-11Nov-AzureRemoteAppAskTheExperts-Registration-Page.html)，在這裡我們會討論 RemoteApp 的一切。
- **RemoteApp 文件呢？** 很高興您這麼問。除了入口網站說明庫的說明內容外 (只要按下入口網站任一頁面上的 **?**)，還有下列關於 RemoteApp 的文件：
	- **開始使用：**
		- [什麼是 RemoteApp？](remoteapp-whatis.md)
		- [RemoteApp 範本映像中有什麼內容？](remoteapp-images.md)
		- [授權如何運作？](remoteapp-licensing.md)
		- [RemoteApp 與 Office 如何共同運作？](remoteapp-o365.md)
		- [重新導向如何在 RemoteApp 中運作？](remoteapp-redirection.md)
	- **部署：**
		- [建立自訂範本映像](remoteapp-create-custom-image.md)
		- [建立混合式收藏](remoteapp-create-hybrid-deployment.md)
		- [建立雲端收藏](remoteapp-create-cloud-deployment.md)
		- [設定 RemoteApp 的 Azure Active Directory](remoteapp-ad.md)
		- [在 RemoteApp 中發佈應用程式](remoteapp-publish.md)
	- **管理：**
		- [新增使用者](remoteapp-user.md)
		- [設定和使用 RemoteApp 的最佳作法](remoteapp-bestpractices.md)	

	影片！ 我們也有一些關於 RemoteApp 的影片。有些提供簡介 ([Azure RemoteApp 簡介](http://azure.microsoft.com/documentation/videos/cloud-cover-ep-150-azure-remote-app-with-thomas-willingham-and-nihar-namjoshi/))，有些則是引導您完成部署 ([雲端部署](https://www.youtube.com/watch?v=3NAv2iwZtGc&feature=youtu.be)和[混合式部署](https://www.youtube.com/watch?v=GCIMxPUvg0c&feature=youtu.be))。請觀賞影片！

 

<!---HONumber=July15_HO2-->