<properties 
   pageTitle="Azure 儲存體 Blob 連接器" 
   description="開始使用 Azure 儲存體 Blob 連接器" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="anuragdalmia" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="05/11/2015"
   ms.author="rajram"/>
   
#Azure 儲存體 Blob 連接器

##概觀
Azure 儲存體 Blob 連接器可讓您從 Blob 容器上傳、下載及刪除 Blob。

##建立新的 Azure 儲存體 Blob 連接器
若要建立新的 Azure 儲存體連接器，請遵循下面所述的步驟。<ul> <li>啟動 Azure 入口網站<li>使用 [+ 新增] (位於頁面底部) -> [Web + 行動] --> [Azure Marketplace] 開啟 Azure Marketplace </ul>

![啟動 Azure Marketplace][1]<br> <ul> <li>按一下 [API 應用程式] <li>搜尋 <i>Blob</i>，然後選取 Azure 儲存體 Blob 連接器 </ul>

![選取 Azure 儲存體 Blob 連接器][2] <br> <ul> <li>按一下 [建立] <li>在開啟的 Azure 儲存體 Blob 連接器分頁中，提供下列資料。</ul>

![建立 Azure 儲存體 Blob 連接器][3]

- **位置** - 選擇您要部署連接器的地理位置
- **訂閱** - 選擇您要建立此連接器的訂閱
- **資源群組** - 選取或建立連接器所在的資源群組
- **Web 裝載方**案 - 選取或建立 Web 裝載方案
- **定價層** - 選擇連接器的定價層
- **名稱** - 提供 Blob 儲存體連接器的名稱
- **套裝設定** 
	- **容器/SAS URI** - 指定 Blob 容器的 URI。URI 也可能包括 SAS 權杖。範例 http://storageaccountname.blob.core.windows.net/containername 或 http://storageaccountname.blob.core.windows.net/containername?sr=c&si=mypolicy&sig=signatureblah
	- **存取金鑰** - 指定有效的主要/次要儲存體帳戶存取金鑰。如果您使用 SAS 權杖進行驗證，請將此欄位空白。
- 按一下 [建立]。將建立新的 Azure 儲存體 Blob 連接器。

##在邏輯應用程式中使用 Azure 儲存體 Blob 連接器
一旦建立 Azure 儲存體 Blob 連接器，即可從流程中取用。

透過 [+ 新增] -> [Web + 行動] -> [LogicApp] 建立新流程。提供流程的中繼資料 (包括資源群組)

![建立邏輯應用程式][4]

按一下 [*觸發程序和動作*]。流程設計工具隨即開啟

![邏輯應用程式空白流程設計工具][5]

Azure 儲存體 Blob 連接器可做為動作。

###動作
按一下右窗格中的 Azure 儲存體 Blob 連接器。此連接器會列出支援的動作。

![Azure 儲存體 Blob 動作的清單][10]

Azure 儲存體 Blob 連接器支援六個動作。包括

- **取得 Blob** - 從容器取得特定的 Blob
- **上傳 Blob** - 上傳新的 Blob 或更新現有的 Blob
- **刪除 Blob** - 從容器中刪除特定的 Blob
- **列出 Blob** - 列出目錄中的所有 Blob
- **建立 Blob 快照** - 建立特定 Blob 的唯讀快照
- **複製 Blob** - 從另一個 Blob 複製以建立新的 Blob。來源 Blob 可能位於相同的帳戶或另一個帳戶中。

舉例來說 - 上傳 Blob。按一下 [上傳 Blob]

![上傳 Blob 動作的輸入][11]


- **Blob 路徑** - 指定要上傳之 Blob 的路徑。此路徑相對於已設定的容器路徑。
- **Blob 寫入內容** - 指定要上傳之 Blob 的內容和屬性。
- **內容轉移編碼** - 指定 [無] 或 base64
- **覆寫** - 如果設定為 true，將會覆寫現有的 Blob。如果 Blob 已存在於相同的路徑，則會傳回錯誤。

提供輸入，然後按一下刻度標記以完成輸入設定。


請注意，已設定的 [Azure 儲存體 Blob 上傳 Blob] 動作會顯示輸入參數以及輸出參數。

####使用先前動作的輸出做為 Azure 儲存體 Blob 動作的輸入
請注意，在已設定的螢幕擷取畫面中，[內容] 值會設定為運算式。

	@triggers().outputs.body.Content


您可以將它設定為任何您想要的值。這只是一個範例。運算式會採用邏輯應用程式觸發程序的輸出，並將它做為要上傳的檔案內容。假設您要使用前一個動作的輸出，像是轉換。在此情況下，運算式會是

	@actions('transformservice').outputs.body.OutputXML


<!-- Image reference -->
[1]: ./media/app-service-logic-connector-azurestorageblob/LaunchAzureMarketplace.PNG
[2]: ./media/app-service-logic-connector-azurestorageblob/SelectAzureStorageBlobConnector.PNG
[3]: ./media/app-service-logic-connector-azurestorageblob/CreateAzureStorageBlobConnector.PNG
[4]: ./media/app-service-logic-connector-azurestorageblob/CreateLogicApp.PNG
[5]: ./media/app-service-logic-connector-azurestorageblob/LogicAppEmptyFlowDesigner.PNG
[6]: ./media/app-service-logic-connector-azurestorageblob/ChooseBlobAvailableTrigger.PNG
[7]: ./media/app-service-logic-connector-azurestorageblob/BasicInputsBlobAvailableTrigger.PNG
[8]: ./media/app-service-logic-connector-azurestorageblob/AdvancedInputsBlobAvailableTrigger.PNG
[9]: ./media/app-service-logic-connector-azurestorageblob/ConfiguredBlobAvailableTrigger.PNG
[10]: ./media/app-service-logic-connector-azurestorageblob/ListOfAzureStorageBlobActions.PNG
[11]: ./media/app-service-logic-connector-azurestorageblob/BasicInputsUploadBlob.PNG
 

<!---HONumber=62-->