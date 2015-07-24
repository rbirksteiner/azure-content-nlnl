<properties 
	pageTitle="BizTalk 服務中的簽發者名稱和簽發者金鑰 | Azure" 
	description="了解如何在 BizTalk 服務中擷取服務匯流排或存取控制 (ACS) 的簽發者名稱和簽發者金鑰。MABS，WABS" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/07/2015" 
	ms.author="mandia"/>




# BizTalk 服務：簽發者名稱和簽發者金鑰

Azure BizTalk 服務使用服務匯流排簽發者名稱和簽發者金鑰，以及存取控制簽發者名稱和簽發者金鑰。具體而言：

工作 | 什麼簽發者名稱和簽發者金鑰
--- | ---
從 Visual Studio 部署應用程式 | 存取控制簽發者名稱和簽發者金鑰
設定 Azure BizTalk 服務入口網站 | 存取控制簽發者名稱和簽發者金鑰
在 Visual Studio 中使用 BizTalk 配接器服務建立 LOB 轉送 | 服務匯流排簽發者名稱和簽發者金鑰

本主題列出擷取簽發者名稱和簽發者金鑰的步驟。

## 存取控制簽發者名稱和簽發者金鑰
下列項目會使用存取控制簽發者名稱和簽發者金鑰：

- 在 Visual Studio 中建立的 Azure BizTalk 服務應用程式：若要在 Visual Studio 中成功將您的 BizTalk 服務應用程式部署至 Azure，請輸入存取控制簽發者名稱和簽發者金鑰。 
- Azure BizTalk 服務入口網站：在您建立 BizTalk 服務且開啟 BizTalk 服務入口網站時，系統會使用相同的存取控制值，自動為您的部署註冊您的存取控制簽發者名稱和簽發者金鑰。

### 複製與貼上存取控制簽發者名稱和簽發者金鑰

1. 登入 [Azure 管理入口網站](http://go.microsoft.com/fwlink/p/?LinkID=213885)。
2. 在左導覽窗格中，選取 [**BizTalk 服務**]。
3. 選取 BizTalk 服務。 
4. 在工作列選取 [**連線資訊**]。這時會列出存取控制命名空間、預設簽發者 (簽發者名稱) 和預設金鑰 (簽發者金鑰)，供您複製和貼上。<br/><br/> 總結：<br/>簽發者名稱 = 預設簽發者<br/>簽發者金鑰 = 預設金鑰


您也可選取 [**開啟 ACS 管理入口網站]** 來擷取存取控制值：

1. 登入 [Azure 管理入口網站](http://go.microsoft.com/fwlink/p/?LinkID=213885)。
2. 在左導覽窗格中，選取 [**BizTalk 服務**]。
3. 選取 BizTalk 服務。
4. 選取 [連接資訊] 按鈕，然後選取 [**開啟 ACS 管理入口網站**]。
5. 在入口網站的 [**服務設定**]下，選取 [**服務身分識別**]。這會顯示您的服務身分識別，也就是您的存取控制簽發者名稱值。選取 [服務身分識別] 連結來查看密碼，也就是您的簽發者金鑰值。您可以複製這些值。<br/><br/> 例如，在 [**服務身分識別**] 中，您看到 「owner」。「owner」 就是您的存取控制簽發者名稱。按一下 "owner" 連結時，您會看到 [密碼]。按一下 [密碼] 連結就可以看到值。此密碼值就是您的存取控制簽發者金鑰。<br/><br/>總結：<br/>簽發者名稱 = 服務身分識別名稱<br/>簽發者金鑰 = 密碼值

在左導覽窗格中，您也可以選取 [**Active Directory**]來擷取存取控制值。

> [AZURE.IMPORTANT]使用 <strong>Active Directory</strong> 建立存取控制命名空間時，**不會**自動建立服務身分識別。佈建 BizTalk 服務時，會自動建立存取控制命名空間、服務身分識別 「owner」(簽發者名稱)、密碼 (簽發者金鑰) 和對稱金鑰。<br /> [做法：使用 ACS 管理服務來設定服務身分識別](http://go.microsoft.com/fwlink/p/?LinkID=303942)提供存取控制服務身分識別的詳細資訊。


## 服務匯流排簽發者名稱和簽發者金鑰
BizTalk 配接器服務會使用服務匯流排簽發者名稱和簽發者金鑰。在 Visual Studio 中，您在 BizTalk 服務專案中使用 BizTalk 配接器服務來連線至內部部署企業營運 (LOB) 系統。若要連線，您需要建立 LOB 轉送並輸入 LOB 系統詳細資料。如果這樣做，則您也需要輸入服務匯流排簽發者名稱和簽發者金鑰。

### 擷取服務匯流排簽發者名稱和簽發者金鑰

1. 登入 [Azure 管理入口網站](http://go.microsoft.com/fwlink/p/?LinkID=213885)。
2. 在左導覽窗格中，選取 [**服務匯流排**]。
3. 選取您的命名空間。在工作列中，選取 [**連線資訊**]。這會顯示 [**預設簽發者**] (簽發者名稱) 和 [**預設金鑰**] (簽發者金鑰)。您可以複製這些值。<br/><br/> 總結：<br/>簽發者名稱 = 預設簽發者<br/>簽發者金鑰 = 預設金鑰

## 下一步
其他 Azure BizTalk 服務主題：

-  [安裝 Azure BizTalk 服務 SDK](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
-  [教學課程：Azure BizTalk 服務](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
-  [如何開始使用 Azure BizTalk 服務 SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
-  [Azure BizTalk 服務](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>


## 另請參閱
-  [做法：使用 ACS 管理服務來設定服務身分識別](http://go.microsoft.com/fwlink/p/?LinkID=303942)<br/>
- [BizTalk 服務：開發人員、基本、標準和高級版本圖表](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [BizTalk 服務：使用 Azure 管理入口網站進行佈建](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [BizTalk 服務：佈建狀態圖](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [BizTalk 服務：儀表板、監視和調整索引標籤](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [BizTalk 服務：備份與還原](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [BizTalk 服務：節流](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
 

<!---HONumber=62-->