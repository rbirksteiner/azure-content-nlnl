<properties
	pageTitle="OneDrive 連接器"
	description="開始使用 OneDrive 連接器"
	authors="anuragdalmia"
	manager="dwrede"
	editor=""
	services="app-service\logic"
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/09/2015"
	ms.author="andalmia"/>

# 在邏輯應用程式中使用 OneDrive 連接器 #

邏輯應用程式可以根據各種資料來源觸發，並提供連接器以取得及處理屬於流程一部分的資料。

OneDrive 連接器可讓您從 OneDrive 帳戶上傳\下載\刪除檔案。

## 建立邏輯應用程式的 OneDrive 連接器 ##
若要使用 OneDrive 連接器，您必須先建立 OneDrive 連接器 API 應用程式的執行個體。這可以直接從邏輯應用程式設計工具中進行，或在工具外進行。在設計工具外建立執行個體的作法如下：

1.	從 Azure 入口網站的首頁開啟 Azure Marketplace。
2.	Under "Everything", search for “OneDrive connector”.
3.	設定 OneDrive 連接器，如下所示：

	![][1] 
	- **名稱** - 提供 OneDrive 連接器的名稱 
	- **App Service 方案** - 選取或建立 App Service 方案 
	- **定價層** - 選擇用於連接器的定價層 
	- **資源群組** - 選取或建立連接器所在的資源群組 
	- **訂用帳戶** - 選擇您要在其中建立此連接器的訂用帳戶 
	- **位置** - 選擇您要部署連接器的地理位置

4. 按一下 [建立]。將建立新的 OneDrive 連接器。
5. 建立 API 應用程式執行個體後，您可以在相同的資源群組中建立邏輯應用程式，以便使用 OneDrive 連接器。

## 在邏輯應用程式中使用 Dropbox 連接器 # ##
建立 API 應用程式之後，您現在可以使用 OneDrive 連接器做為邏輯應用程式的動作。若要這樣做，您需要：

1.	建立新的邏輯應用程式，並選擇具有 OneDrive 連接器的相同資源群組。請依照指示[建立新的邏輯應用程式]。

2.	在建立的邏輯應用程式中開啟 [觸發程序和動作] 以開啟邏輯應用程式設計工具，並設定您的流程。

3.	OneDrive 接器就會出現在右側程式庫中的 [此資源群組中的 API 應用程式] 區段。

	![][2]
4.	您可以在 [OneDrive 連接器] 上按一下來將 OneDrive 連接器 API 應用程式置入編輯器。按一下 [授權] 按鈕。提供您的 Microsoft 認證 (若未自動登入)。按一 [是] 來允許存取。

	![][3]
	![][4]

5.	您現在便可以在流程中使用 OneDrive 連接器。OneDrive 連接器中目前沒有觸發程序可使用。可使用的動作有 - 「取得檔案」、「上傳檔案」、「刪除檔案」和「列出檔案」。

	![][5]

6.	讓我們逐步進行「上傳檔案」體驗。您可以使用 OneDrive 動作「上傳檔案」來上傳檔案到您的 OneDrive 帳戶。

	![][6]

	設定 [上傳檔案] 的輸入屬性，如下所示：

 - **檔案路徑** - 指定要上傳之檔案的檔案路徑
 - **內容** - 指定要上傳之檔案的內容
 - **內容轉移編碼** - 指定 [無] 或 [base64]
 - **覆寫** - 指定 [true] 會覆寫已存在的檔案。這是進階屬性

7. 設定好這些之後，「上傳檔案」動作便已設定並可在流程中使用。同樣地，您也可以設定其他動作。

8. 若要在邏輯應用程式外使用連接器，可以利用連接器公開的 REST API。您可以使用 [瀏覽]->[API 應用程式]->[OneDrive 連接器] 檢視這個「API 定義」。在 [摘要] 區段下的 [API 定義] 透鏡上按一下，來檢視此連接器公開的 API。

	![][7]

9. 如需 API 的詳細資訊，請參閱 [OneDrive API 定義]。

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-onedrive/img1.PNG
[2]: ./media/app-service-logic-connector-onedrive/img2.PNG
[3]: ./media/app-service-logic-connector-onedrive/img3.PNG
[4]: ./media/app-service-logic-connector-onedrive/img4.PNG
[5]: ./media/app-service-logic-connector-onedrive/img5.PNG
[6]: ./media/app-service-logic-connector-onedrive/img6.PNG
[7]: ./media/app-service-logic-connector-onedrive/img7.PNG

<!-- Links -->
[建立新的邏輯應用程式]: app-service-logic-create-a-logic-app.md
[OneDrive API 定義]: https://msdn.microsoft.com/zh-tw/library/dn974227.aspx

<!----HONumber=62-->