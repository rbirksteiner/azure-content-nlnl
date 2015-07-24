<properties 
	pageTitle="Dropbox 連接器"
	description="開始使用 Dropbox 連接器"
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
	ms.date="03/31/2015"
	ms.author="adgoda"/>

# 在邏輯應用程式中使用 Dropbox 連接器 #

邏輯應用程式可以根據各種資料來源觸發，並提供連接器以取得及處理屬於流程一部分的資料。

Dropbox 連接器可讓您從 Dropbox 帳戶上傳或下載檔案。

## 建立邏輯應用程式的 Dropbox 連接器 ##
若要使用 Dropbox 連接器，您必須先建立 Dropbox 連接器 API 應用程式的執行個體。以下步驟可以達到此目的：

1.	使用 Azure 入口網站左下方的 [+新增] 選項開啟 Azure Marketplace。
2.	瀏覽至 [Web 與行動] > [API Marketplace]，並搜尋「Dropbox 連接器」。
3.	設定 Dropbox 連接器，如下所示：
 
	![][1] 
	- **位置** - 選擇您要部署連接器的地理位置 
	- **訂用帳戶** - 選擇您要在其中建立此連接器的訂用帳戶 
	- **資源群組** - 選取或建立連接器所在的資源群組 
	- **App Service 方案** - 選取或建立 Web 主控方案 
	- **定價層** - 選擇用於連接器的定價層
	- **位置** - 選擇您要部署連接器的地理位置

4. 按一下 [建立]。將建立新的 Dropbox 連接器。
5. 建立 API 應用程式執行個體後，您可以在相同的資源群組中建立邏輯應用程式，以便使用 Dropbox 連接器。

## 在邏輯應用程式中使用 Dropbox 連接器 ##
建立 API 應用程式之後，您現在可以使用 Dropbox 連接器做為邏輯應用程式的動作。若要這樣做，您需要：

1.	建立新的邏輯應用程式，並選擇具有 Dropbox 連接器的相同資源群組。
 	
	![][2]
2.	開啟 [觸發程序和動作] 以開啟邏輯應用程式設計工具，並設定您的流程。 
 	
	![][3]
3.	Dropbox 接器就會出現在右側程式庫中的 [此資源群組中的 API 應用程式] 區段。
 
	![][4]
4.	您可以在 [Dropbox 連接器] 上按一下來將 Dropbox 連接器 API 應用程式置入編輯器。按一下 [授權] 按鈕。提供您的 Dropbox 認證。按一下 [允許]
 
	![][5]
	![][6]
	![][7]
6.	您現在便可以在流程中使用 Dropbox 連接器。您可以使用 Dropbox 動作 [上傳檔案] 將檔案上傳到您的 Dropbox 帳戶。
 
	![][8]
	![][9]

設定 [上傳檔案] 的輸入屬性，如下所示：

- **檔案路徑** - 指定要上傳之檔案的目的地 Dropbox 檔案路徑。範例：Photos/image.png
- **內容** - 指定要上傳之檔案的內容這通常來自邏輯應用程式的上一個步驟。
- **內容轉移編碼** - 指定 none 或 base64
- **覆寫** - 指定 [true] 會覆寫已存在的檔案。


<!-- Image reference -->
[1]: ./media/app-service-logic-connector-dropbox/img1.PNG
[2]: ./media/app-service-logic-connector-dropbox/img2.PNG
[3]: ./media/app-service-logic-connector-dropbox/img3.png
[4]: ./media/app-service-logic-connector-dropbox/img4.png
[5]: ./media/app-service-logic-connector-dropbox/img5.PNG
[6]: ./media/app-service-logic-connector-dropbox/img6.PNG
[7]: ./media/app-service-logic-connector-dropbox/img7.PNG
[8]: ./media/app-service-logic-connector-dropbox/img8.PNG
[9]: ./media/app-service-logic-connector-dropbox/img9.PNG
 

<!----HONumber=62-->