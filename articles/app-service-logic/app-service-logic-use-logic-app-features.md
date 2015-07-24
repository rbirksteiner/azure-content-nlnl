<properties 
	pageTitle="使用邏輯應用程式功能" 
	description="了解如何使用邏輯應用程式的進階功能。" 
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
	ms.topic="article"
	ms.date="05/23/2015"
	ms.author="stepsic"/>
	
# 使用邏輯應用程式功能

在[上一個主題][Create a new logic app]中，您已建立第一個邏輯應用程式。現在我們將說明如何使用應用程式服務邏輯應用程式，來建立更完整的程序。本主題將介紹下列新的邏輯應用程式概念：

- 條件式邏輯，只有在符合特定條件時，才會執行動作。
- 重複的動作。
- 用以編輯現有邏輯應用程式的程式碼檢視。
- 啟動工作流程的選項。

在完成本主題之前，您應先完成[建立新的邏輯應用程式]中的步驟。在 [Azure 入口網站] 中，瀏覽至您的邏輯應用程式，然後按一下摘要中的 [**觸發程序和動作**]，以編輯邏輯應用程式定義。

## 新增條件式邏輯和重複

雖然原始流程也可運作，但有某些區域是可以改善的。首先，動作只會將傳回的最高排名推文傳送給您。在邏輯上，您會想要收到所有含有關鍵字的推文。若要對項目清單 (例如傳回的推文) 重複執行動作，您必須使用 `repeat` 屬性。

### 重複
重複會取用項目清單，並對該清單中的每個項目執行動作。下列步驟會更新現有的動作以使用重複，使推文的清單更為合理。

1. 返回您所建立的工作流程，並按一下 [**Essentials**] 中的 [**定義**] 連結。 

2. 若要編輯 [**Dropbox 連接器**] 動作，請按一下鉛筆圖示。

3. 按一下齒輪圖示，然後選取 [**對清單重複執行**]。
 
2. 在 [**重複**] 方塊旁邊，按一下 `...`，然後選取 [**主體**]。這會輸入：

    	@body('twitterconnector')

	到文字方塊中。此函數會輸出推文清單。

3. 選取 [**內容**] 文字方塊中的所有文字，然後加以刪除。然後，按一下 `...`，並選取 [**推文文字**]。這會插入 **repeatItem()** 函數，傳回清單中的每個元素。

最後，請注意重複動作的輸出有其特殊性。如果您想要參考 Dropbox 作業的結果，例如，您可能*不*執行一般 `@actions('dropboxconnector').outputs.body`，而是執行：`@actions('dropboxconnector').outputs.repeatItems`。這會傳回每次執行作業的所有清單，以及每一次的輸出。例如，`@first(actions('dropboxconnector').outputs.repeatItems).outputs.body.FilePath` 將傳回第一個上傳之檔案的路徑。

### 條件式
此邏輯應用程式仍會產生很多上傳至 Dropbox 的檔案。下列步驟中會新增其他邏輯，以確定您只有在推文有特定數量的回推時，才會收到檔案。

1. 按一下動作頂端的齒輪圖示，然後選取 [**新增要符合的條件**]。

2. 在文字方塊中，輸入下列內容：

    	@greater(repeatItem().Retweet_Count , 5)
    
	函數 **greater** 會比較兩個值，且只有在第一個值大於第二個值時，才允許動作執行。您會存取由點 (.) 加上屬性名稱組成的給定屬性，例如上述的 `.Retweet_Count`。

3. 按一下核取符號，以儲存 Dropbox 動作。

## 使用程式碼檢視編輯邏輯應用程式

除了設計工具以外，您也可以直接編輯定義邏輯應用程式的程式碼，如下所示。

1. 按一下命令列中的 [**程式碼檢視**] 按鈕。 

	這會開啟一個完整的編輯器，顯示您剛建立的定義。

	![程式碼檢視](./media/app-service-logic-use-logic-app-features/codeview.png)

    藉由使用文字編輯器，您可以在相同的邏輯應用程式或邏輯應用程式之間複製並貼上任何數量的動作。您也可以輕鬆地在定義中新增或移除整個區段，以及與其他人共用定義。

2. 在程式碼檢視中進行變更之後，請直接按一下 [**儲存**]。

### 參數
邏輯應用程式的某些功能只能在程式碼檢視中使用。例如，參數就是其中之一。參數可讓您輕鬆地在整個邏輯應用程式中重複使用值。例如，如果您想要在數個動作中使用同一個電子郵件地址，您應將該地址定義為參數。

下列作業會更新您現有的邏輯應用程式，而將參數用於查詢詞彙。

1. 在程式碼檢視中找出 `parameters : {}` 物件，並插入下列主題物件：

	    "topic" : {
		    "type" : "string",
		    "defaultValue" : "MicrosoftAzure"
	    }
    
2. 捲動至 `twitterconnector` 動作、找出查詢值，並將其取代為 `#@{parameters('topic')}`。您也可以使用 **concat** 函式來結合兩個或多個字串，例如：`@concat('#',parameters('topic'))` 等同於上述程式碼。
 
3. 最後，請移至 `dropboxconnector` 動作，並新增主題參數，如下所示：

    	/tweets/@{parameters('topic')}/@{repeatItem().TweetID}.txt

參數很適合用來提取您很可能經常變更的值。當您需要在不同環境中覆寫參數時，參數特別有用。如需有關如何根據環境覆寫參數的詳細資訊，請參閱我們的 [REST API 文件](http://go.microsoft.com/fwlink/?LinkID=525617&clcid=0x409)。

現在，當您按一下 [**儲存**] 時，每小時都將有回推數超過 5 個的新推文傳遞到您 Dropbox 中名為 [**推文**] 的資料夾。

若要深入了解邏輯應用程式定義，請參閱[撰寫邏輯應用程式定義](app-service-logic-author-definitions.md)。

## 啟動邏輯應用程式工作流程
有數個不同的選項可用來啟動您的邏輯應用程式中定義的工作流程。工作流程一律可在 [Azure 入口網站]中隨選啟動。

### 循環觸發程序
循環觸發程序會依照您指定的間隔執行。當觸發程序具有條件式邏輯時，觸發程序會判斷工作流程是否需要執行。觸發程序透過傳回 `200` 狀態碼，表示應執行。如果不需要執行，則會傳回 `202` 狀態碼。

### 使用 REST API 回呼
服務可以呼叫邏輯應用程式端點以啟動工作流程。您可以您可以從邏輯應用程式中的 [**設定**] 命令列按鈕導覽至 [**屬性**] 分頁，以找出要存取的端點。

您可以從自訂應用程式內使用此回呼來叫用邏輯應用程式。您必須使用 [**基本**] 驗證。系統會為您建立 `default` 的使用者名稱，密碼則是 [**屬性**] 分頁上的 [**主要存取金鑰**] 欄位。例如：

        POST https://default:<<your primary access key>>@<< your endpoint>>/run?api-version=2015-02-01-preview
        Content-type: application/json
        {
            "name" : "nameOfTrigger",
            "outputs" : { "property" : "value" }
        }

您可以將輸出傳遞至工作流程，並在工作流程中加以參考。以前述觸發程序為例，如果您包含 `@triggers().outputs.property`，將會取得 `value`。

如需詳細資訊，請參閱 [REST 文件](http://go.microsoft.com/fwlink/?LinkID=525617&clcid=0x409)。

### 手動執行
您可以定義沒有觸發程序的邏輯應用程式。在此情況下，工作流程必須隨選啟動。這種邏輯應用程式最適用於只需要間歇性執行的程序。若要建立不含觸發程序的邏輯應用程式，請在設計工具的 [**啟動邏輯**] 方塊中勾選 [**手動執行此邏輯**]。

若要隨選啟動邏輯應用程式，請按一下命令列上的 [**立即執行**] 按鈕。

<!-- Shared links -->
[Create a new logic app]: app-service-logic-create-a-logic-app.md
[建立新的邏輯應用程式]: app-service-logic-create-a-logic-app.md
[Azure 入口網站]: https://portal.azure.com

<!---HONumber=62-->