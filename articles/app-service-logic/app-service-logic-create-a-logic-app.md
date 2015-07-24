<properties
	pageTitle="建立邏輯應用程式"
	description="開始建立基本的邏輯應用程式"
	authors="stepsic-microsoft-com"
	manager="dwrede"
	editor=""
	services="app-service\logic"
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="05/23/2015"
	ms.author="stepsic"/>

#建立新的邏輯應用程式
本主題只花幾分鐘就能示範如何開始使用 [App Services Logic Apps](app-service-logic-what-are-logic-apps.md)。我們將逐步解說可將一組您感興趣的推文傳送到 Dropbox 資料夾的工作流程。

若要使用此案例，您需要：

- Azure 訂用帳戶
- Twitter 帳戶
- Dropbox 帳戶

<!--- TODO: Add try it now information here -->

##取得連接器

首先，您必須建立兩個您將用到的連接器：[**Dropbox 連接器**](app-service-logic-connector-dropbox.md)和 [**Twitter 連接器**](app-service-logic-connector-twitter.md)。若要建立這些項目：

0. 登入 Azure 入口網站。

1. 按一下主畫面上的 [[**Marketplace**](https://portal.azure.com/#blade/HubsExtension/GalleryFeaturedMenuItemBlade/selectedMenuItemId/apiapps)]，並搜尋 **Twitter** (或[按一下這裡](https://portal.azure.com/#create/microsoft_com.TwitterConnector.0.2.2))。

2. 選取 Twitter 連接器，然後按一下 [建立] 按鈕。您會看到一個包含所有設定的檢視。您可以保留 **Twitter 連接器**名稱。

3. 在 [**建立新的應用程式服務方案**] 中輸入方案名稱。

	>[AZURE.NOTE]本節中的步驟會假設您打算建立新的應用程式服務方案。如果您打算使用現有的應用程式服務方案，請按一下 [**選取現有項目**] 來選取現有的方案，然後跳至本節的最後一個步驟。您需要一個方案來主控您的所有應用程式。

4.  選取新方案的 [**定價層**]。

	>[AZURE.NOTE]根據預設，系統只會顯示建議邏輯應用程式使用的方案。按一下 [**檢視全部**] 來查看所有可用方案。當您在免費層次中執行邏輯應用程式時，您只能每小時執行，並每個月最多使用 1000 個動作。

5. 建立流程的 [**資源群組**]。

	資源群組會當做您應用程式的容器使用。所有的應用程式資源會存在於相同的資源群組。

6. 如果您有多個 Azure 訂閱，請選擇您即將使用的訂閱。

7. 選擇要執行邏輯應用程式的 [**位置**]。

	![建立 API 應用程式檢視](./media/app-service-logic-create-a-logic-app/gallery.png)

8. 按一下 [建立]。佈建步驟可能需要一兩分鐘的時間。

9. 現在對 [Dropbox](https://portal.azure.com/#create/microsoft_com.DropboxConnector.0.2.2) 重複執行此程序。

##啟動邏輯應用程式

現在，您需要建立新的邏輯應用程式：

1. 按一下畫面左下角的 [**+ 新增**] 按鈕，展開 [**Web + 行動**]，然後按一下 [**邏輯應用程式**]。

 	這會顯示 [建立邏輯應用程式] 檢視，您可以在這裡提供一些基本設定當做開始。

	![建立邏輯應用程式檢視](./media/app-service-logic-create-a-logic-app/createlogicapp.png)

2. 在 [**名稱**] 中，為您的應用程式邏輯輸入有意義的名稱。

3. 選擇您在建立連接器時使用的 [**應用程式服務方案**]。這應該會為您自動選擇位置、訂閱和資源群組。

這會完成基本設定，但這時先別按 [**建立**]。接下來，您會將觸發程序和動作新增至您的工作流程。

## 新增觸發程序

觸發程序是可讓您的邏輯應用程式執行的項目。接下來，您將新增一個週期性觸發程序，它會在預先定義的排程上啟動您的工作流程。

1. 仍在 [**建立邏輯應用程式**] 檢視中，按一下 [**觸發程序和動作**]。

	這會顯示可顯示流程的全螢幕設計工具。右手邊是可能包含觸發程序的所有服務清單。

2. 在頂端區段中，按一下 [**週期性**]。

	這會新增一個您可以在其中指定週期性設定的方塊。

	![週期性](./media/app-service-logic-create-a-logic-app/recurrence.png)


4.  選擇週期性 [**頻率**] 和 [**間隔**] (例如每小時一次)，然後按一下綠色核取符號。

現在，您會將動作新增至資料流程。

## 新增 Twitter 動作

動作是工作流程的執行項目。您可以擁有任意數目的動作，而且您可以將它們分組，以便將資訊從一個動作傳遞到下一個動作。

1. 在右側窗格中，尋找 [**Twitter 連接器**]，然後按一下它。


2. 將它載入後，請按一下 [**授權**] 按鈕，登入您的 Twitter 帳戶，然後按一下 [**授權應用程式**]。

	這會授與連接器存取您 Twitter 帳戶的權限。Twitter 連接器所提供的可能作業清單便會隨即顯示。

	![動作](./media/app-service-logic-create-a-logic-app/actions.png)

3. 按一下 [**搜尋推文**]，接著在 [**指定查詢**] 中輸入類似 `#MicrosoftAzure` 的內容，然後按一下綠色核取符號。

	![Twitter 搜尋](./media/app-service-logic-create-a-logic-app/twittersearch.png)

Twitter 連接器現在便是工作流程的一部分。

## 新增 Dropbox 動作並建立應用程式

最後一個步驟是加入將推文上傳至 Dropbox 檔案的動作。

1. 在右側窗格中，按一下 [**Dropbox 連接器**]。

2. 佈建完成後，請按一下 [**授權**] 按鈕，登入您的 Dropbox 帳戶，然後按一下 [**允許**]。

	![授權 Dropbox 連接器](./media/app-service-logic-create-a-logic-app/authorize.png)

	這會授與連接器存取您 Dropbox 帳戶的權限。Dropbox 連接器所提供的可能作業清單便會隨即顯示。

4. 按一下 [**上傳檔案**]。

	這會顯示 Dropbox 連接器設定，您必須有了這些設定後才能將資料從 Twitter 搜尋傳遞至 Dropbox。

	![Dropbox](./media/app-service-logic-create-a-logic-app/dropbox.png)

3. 在 [**檔案路徑**] 欄位中，輸入 `/tweet.txt`

4. 在 [**內容**] 欄位中，按一下 [`...`] 按鈕，然後再按一下 [**推文文字**] 選項。

	這會將值 `@first(body('twitterconnector')).TweetText` 輸入文字方塊中。這個產生的值包含下列部分：

	內容部分 | 說明
	------------------------------------------ | ------------
	 `@` | 指出您正在輸入函數，而不是實際值。
	`actions('twitterconnector').outputs.body` | 取得 Twitter 連接器查詢所傳回的推文。
	`first()` | 搜尋推文動作會傳回一份清單，但您只想要上傳一個檔案
	`.TweetText` | 選取推文訊息屬性。

5. 按一下綠色核取符號來儲存連接器設定。

5. 現在設計已完成，請按一下設計工具左上角的 [**程式碼檢視**]，並注意這是 JSON 程式碼，可用來定義剛剛在設計工具中建立的工作流程。我們將在[下一個主題][Use logic app features]中詳加討論這段程式碼。

6. 按一下畫面底部的 [**確定**] 按鈕，然後按一下 [**建立**] 按鈕。

	如此即會建立新的邏輯應用程式。

## 管理已建立的邏輯應用程式

現在，您的邏輯應用程式已啟動並執行。每次當排定的工作流程執行時，它會檢查推文是否包含特定的雜湊標記。當它找到符合的推文時，它就會將該推文放入 Dropbox。最後，您將看到如何停用應用程式，或看到它的執行狀況。

1. 按一下畫面左側的 [**瀏覽**]，然後選取 [**邏輯應用程式**]。

2. 按一下您剛剛建立的新邏輯應用程式，以查看目前狀態和一般資訊。

3. 若要編輯新的邏輯應用程式，請按一下 [**觸發程序和動作**]。

5. 若要關閉應用程式，請按一下命令列中的 [**停用**]。

不到 5 分鐘的時間，您便能夠設定在雲端中執行的簡單邏輯應用程式。若要深入了解如何使用邏輯應用程式功能，請參閱[使用邏輯應用程式功能]。若要深入了解邏輯應用程式定義本身，請參閱[撰寫邏輯應用程式定義](app-service-logic-author-definitions.md)。

<!-- Shared links -->
[Azure portal]: https://portal.azure.com
[Use logic app features]: app-service-logic-use-logic-app-features.md
[使用邏輯應用程式功能]: app-service-logic-use-logic-app-features.md
 

<!---HONumber=62-->