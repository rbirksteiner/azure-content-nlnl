<properties
	pageTitle="FTP 連接器"
	description="開始使用 FTP 連接器"
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
	ms.date="03/05/2015"
	ms.author="rajeshramabathiran"/>

#FTP 連接器

##概觀
FTP 連接器可讓您從 FTP 伺服器移入/移出資料。FTP 連接器的主要功能如下：

- 依照需求從 FTP 伺服器提取檔案
- 根據可設定的排程執行輪詢
- 根據 FTP 伺服器中的新文件輪詢 FTP 伺服器並觸發邏輯流程
- 指定 FTP 伺服器做為 IP 位址、連接埠、密碼和主機名稱
- 依照需求執行傳送的能力
- 依照需求在 FTP 伺服器上刪除檔案的能力

##建立新的 FTP 連接器
若要建新的 FTP 連接器，請遵循下面提到的步驟。
- 啟動 Azure 入口網站 
- 使用 [+ 新增] (位於頁面底部) -> [Web + 行動] --> [Azure Marketplace] 開啟 Azure Marketplace

	![Launch Azure Marketplace][1]

- 按一下 API 應用程式
- 搜尋 FTP，然後選取 FTP 連接器

	![選取 FTP 連接器][2]

- 按一下 [建立]
- 在開啟的 FTP 連接器刀鋒視窗中，提供下列資料。

	![建立 FTP 連接器][3]

- **位置** - 選擇您要部署連接器的地理位置
- **訂閱** - 選擇您要建立此連接器的訂閱
- **資源群組** - 選取或建立連接器所在的資源群組
- **Web 裝載方**案 - 選取或建立 Web 裝載方案
- **定價層** - 選擇連接器的定價層
- **名稱** - 提供 FTP 連接器的名稱
- **套裝設定**
	- **伺服器位址** - 指定 FTP 伺服器名稱或 IP 位址
	- **使用者名稱** - 指定要連接到 FTP 伺服器的使用者名稱
	- **密碼** - 指定要連接到 FTP 伺服器的密碼
	- **根資料夾** - 指定根資料夾路徑
	- **使用二進位** - 指定 true 以使用二進位傳輸模式，指定 false 則使用 ASCII
	- **使用 SSL** - 指定 true 以透過 SSL/TLS 安全通道使用 FTP
	- **伺服器連接埠** - 指定 FTP 伺服器連接埠號碼
- 按一下 [建立]。將建立新的 FTP 連接器。

##在邏輯應用程式中使用 FTP 連接器
一旦建立 FTP 連接器，即可從流程中取用。

透過 [+ 新增] -> [Web + 行動] -> [LogicApp] 建立新流程。提供流程的中繼資料 (包括資源群組)

![建立邏輯應用程式][4]

按一下 [*觸發程序和動作*]。流程設計工具隨即開啟

![邏輯應用程式空白流程設計工具][5]

可以使用 FTP 連接器做為觸發程序和動作。

###觸發程序
在空的流程設計工具中，按一下右邊組件庫窗格中的 FTP 連接器。

![選擇 FTP 觸發程序][6]

FTP 連接器具有一個觸發程序 -「檔案可用 (讀取然後刪除)」。此觸發程序

- 輪詢新檔案的資料夾路徑
- 具現化每個新檔案的邏輯流程
- 在具現化邏輯流程之後，從資料夾路徑中刪除檔案

按一下 [檔案可用 (讀取然後刪除)] 觸發程序。

![基本輸入 FTP 觸發程序][7]

輸入可協助您設定依排定的頻率輪詢特定資料夾路徑。基本輸入為 
- 頻率 
- 指定 FTP 輪詢的頻率 
- 間隔 
- 指定已排定頻率的間隔
- 資料夾路徑 
- 指定 FTP 伺服器中的資料夾路徑 
- 檔案類型 
- 指定檔案類型是文字還是二進位

按一下省略符號 [...] 可顯示進階輸入。

![基本輸入 FTP 觸發程序][8]

進階輸入為 
- 檔案遮罩 
- 指定輪詢時的檔案遮罩 
- 排除檔案遮罩 
- 指定輪詢時排除的檔案遮罩

提供輸入，然後按一下核取記號以完成輸入設定。

![基本輸入 FTP 觸發程序][9]

請注意，設定的 FTP 觸發程序會顯示已設定的輸入參數及輸出。

####在後續動作中使用 FTP 觸發程序的輸出
FTP 觸發程序的輸出可做為流程中其他某些動作的輸入。

您可以按一下動作的輸入對話方塊中的 '...'，然後從下拉式方塊中直接選取 FTP 的輸出。

您也可以直接在動作的輸入方塊中撰寫運算式。要參考 FTP 觸發程序輸出的流程運算式如下所示

	@triggers('ftpconnector').outputs.body.Content

###動作
按一下右窗格中的 FTP 連接器。FTP 連接器會列出支援的動作。

![FTP 動作清單][10]

FTP 連接器支援四個動作。包括

- **取得檔案** - 取得特定檔案的內容
- **上傳檔案** - 將檔案上傳至 FTP 資料夾路徑
- **刪除檔案** - 從 FTP 資料夾路徑刪除檔案
- **列出檔案** - 列出 FTP 資料夾路徑中的所有檔案

舉例來說 - 上傳檔案。按一下 [上傳檔案]

會先顯示基本輸入。

![上傳檔案動作的基本輸入][11]


- **內容** - 指定要上傳之檔案的內容
- **內容轉移編碼** - 指定 [無] 或 [base64]
- **檔案路徑** - 指定要上傳檔案的檔案路徑

按一下[...] 可顯示進階輸入。

![上傳檔案動作的基本輸入][12]


- **存在時附加** - True 或 False「存在時附加」。啟用時，資料會附加至檔案 (如果存在的話)。停用時，則會覆寫檔案 (如果存在的話)。
- **暫存資料夾** - 選擇性。如果有提供，配接器會將檔案上傳到 [暫存資料夾路徑]，上傳完成之後檔案會移至 [資料夾路徑]。[暫存資料夾路徑] 應和 [資料夾路徑] 在相同的實體磁碟上，以確保移動作業自動進行。只在啟用 [存在時附加] 屬性，才能使用暫存資料夾。

提供輸入，然後按一下核取記號以完成輸入設定。

![已設定的上傳檔案動作][13]

「檔案路徑」參數設定為

	@concat('/Output/',triggers().outputs.body.FileName)

請注意，已設定的 [FTP 上傳檔案] 動作會顯示輸入參數，以及輸出參數。

####使用先前動作的輸出做為 FTP 動作的輸入
請注意，在已設定的螢幕擷取畫面中，[內容] 值會設定為運算式。

	@triggers().outputs.body.Content


您可以將它設定為任何您想要的值。這只是一個範例。運算式會採用邏輯應用程式觸發程序的輸出，並將它做為要上傳的檔案內容。假設您要使用前一個動作的輸出，像是轉換。在此情況下，運算式會是

	@actions('transformservice').outputs.body.OutputXML


<!-- Image reference -->
[1]: ./media/app-service-logic-connector-ftp/LaunchAzureMarketplace.PNG
[2]: ./media/app-service-logic-connector-ftp/SelectFTPConnector.PNG
[3]: ./media/app-service-logic-connector-ftp/CreateFTPConnector.PNG
[4]: ./media/app-service-logic-connector-ftp/CreateLogicApp.PNG
[5]: ./media/app-service-logic-connector-ftp/LogicAppEmptyFlowDesigner.PNG
[6]: ./media/app-service-logic-connector-ftp/ChooseFTPTrigger.PNG
[7]: ./media/app-service-logic-connector-ftp/BasicInputsFTPTrigger.PNG
[8]: ./media/app-service-logic-connector-ftp/AdvancedInputsFTPTrigger.PNG
[9]: ./media/app-service-logic-connector-ftp/ConfiguredFTPTrigger.PNG
[10]: ./media/app-service-logic-connector-ftp/ListOfFTPActions.PNG
[11]: ./media/app-service-logic-connector-ftp/BasicInputsUploadFile.PNG
[12]: ./media/app-service-logic-connector-ftp/AdvancedInputsUploadFile.PNG
[13]: ./media/app-service-logic-connector-ftp/ConfiguredUploadFile.PNG
 

<!---HONumber=62-->