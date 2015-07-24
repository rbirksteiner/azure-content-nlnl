<properties
	pageTitle="使用 DocumentDB Document Explorer 檢視、編輯、建立和上傳 JSON 文件 | Azure"
	description="深入了解 DocumentDB Document Explorer 是一項 Azure 預覽入口網站工具，可用來檢視、編輯、建立和上傳使用 DocumentDB 的 JSON 文件。"
	services="documentdb"
	authors="stephbaron"
	manager="johnmac"
	editor="monicar"
	documentationCenter=""/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="06/10/2015"
	ms.author="stbaro"/>

# 使用 DocumentDB Document Explorer 檢視、編輯、建立和上傳 JSON 文件 #

本篇文章提供了 [Microsoft Azure DocumentDB](http://azure.microsoft.com/services/documentdb/) Document Explorer 的概觀，它是一項 Azure Preview 入口網站工具，可讓您檢視、編輯、建立和上傳使用 DocumentDB 的 JSON 文件。

完成本教學課程後，您將能夠回答下列問題：

-	如何透過網頁瀏覽器輕鬆地建立、檢視、編輯和刪除個別 DocumentDB 文件？
-	如何透過網頁瀏覽器輕鬆地檢視 DocumentDB 文件的系統屬性？
-	如何透過網頁瀏覽器輕鬆地在 DocumentDB 中執行文件的大量擷取？

##<a id="Launch"></a>啟動並瀏覽 Document Explorer##

您可以從任何 DocumentDB 帳戶、資料庫和集合分頁啟動 Document Explorer。

1. 只要在 DocumentDB 帳戶或資料庫刀鋒視窗頂端按一下 [Document Explorer] 命令即可。

	![[Document Explorer] 命令的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/documentexplorercommand.png)
 
2. 您也可以在每個刀鋒視窗底部附近看到 [開發人員工具] 透鏡，其中包含 [Document Explorer] 部件。

	![[Document Explorer] 部件的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/documentexplorerpart.png)

2. 您只需按一下磚便可啟動 Document Explorer。

	<p>[資料庫] 和 [集合] 下拉式清單方塊會根據您在其中啟動 Document Explorer 的內容預先填入。例如，如果您要從資料庫刀鋒視窗啟動，則系統會預先填入目前資料庫。如果您要從集合刀鋒視窗啟動，則系統會預先填入目前集合。

	![[Document Explorer] 的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/documentexplorerinitial.png)

3. 根據預設，Document Explorer 會在選取的集合中，依照建立日期 (最早到最晚) 載入前 100 個文件。您可以選取 [Document Explorer] 刀鋒視窗底部的 [載入更多] 選項，載入其他的文件 (每批 100 個文件)。按一下 [Document Explorer] 刀鋒視窗頂端的 [設定] 命令，即可修改預設行為。

	![[Document Explorer 設定] 刀鋒視窗的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/documentexplorersettings.png)


4. 您可以在 [設定] 刀鋒視窗中，調整項目數量來傳回每一頁，並在 [Document Explorer] 方格中提供 WHERE 子句以載入比對文件。閱讀更多 DocumentDB SQL 文法資訊，請按一下[這裡](documentdb-sql-query.md)。

	![[Document Explorer 設定] 刀鋒視窗的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/documentexplorersettings2.png)

	> [AZURE.NOTE]修改 Document Explorer 設定後，您必須按一下 [重新整理] 命令才能套用新設定。設定只會維持在目前的瀏覽器工作階段。
	
5. [資料庫] 和 [集合] 下拉式清單方塊可用來輕鬆地變更目前檢視文件的集合，而無需關閉並重新啟動 Document Explorer。

5. Document Explorer 還支援根據 id 屬性篩選目前載入的文件集。您只需在篩選方塊中輸入即可。

	![反白顯示篩選條件的 [Document Explorer] 螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/documentexplorerfilter.png)

	Document Explorer 清單中的結果便會根據您所提供的準則進行篩選。

	![含篩選結果的 [Document Explorer] 螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/documentexplorerfilterresults.png)


	> [AZURE.IMPORTANT]Document Explorer 篩選功能只會從**目前**載入的文件集進行篩選，而且不會針對目前選取的集合執行查詢。

6. 若要重新整理 Document Explorer 所載入的文件清單，您只需要按一下刀鋒視窗頂端的 [重新整理] 命令即可。

	![[Document Explorer] 重新整理命令的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/documentexplorerrefresh.png)


##<a id="Create"></a>使用 Document Explorer 檢視、建立和編輯文件##

Document Explorer 可讓您輕鬆地建立、編輯和刪除文件。

- 若要建立文件，您只需要按一下 [建立文件] 命令，並提供少量的 JSON 程式碼片段即可。

	![[Document Explorer] 建立文件體驗的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/createdocument.png)

- 只要輸入或貼上您想要建立之文件的 JSON 內容，並按一下 [儲存] 命令以認可您的文件。

	![[Document Explorer] 儲存命令的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/savedocument1.png)

	> [AZURE.NOTE]如果您未提供 "id" 屬性，則 Document Explorer 會自動新增 id 屬性，並產生一個 GUID 做為識別碼值。

- 如果您已經有來自 JSON 檔案、MongoDB、SQL Server、CSV 檔案、Azure 資料表儲存體，或其他 DocumentDB 集合的資料，即可使用 DocumentDB 的[資料移轉工具](documentdb-import-data.md)快速匯入資料。

- 若要編輯現有文件，您只需要在 Document Explorer 中選取該文件，視需要編輯文件，然後按一下 [儲存] 命令即可。

	![[Document Explorer] 編輯文件功能的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/editdocument.png)

- 如果您正在編輯文件，並決定捨棄目前這組編輯，您只需要按一下 [捨棄] 命令，確認捨棄動作，然後重新載入文件的先前狀態即可。

	![[Document Explorer] 捨棄命令的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/discardedit.png)

- 您可以刪除文件，方法是選取該文件，按一下 [刪除] 命令，然後確認刪除。確認後，系統會立即將文件從 Document Explorer 清單中移除：

	![[Document Explorer] 刪除命令的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/deletedocument.png)

- 請注意，Document Explorer 會驗證任何新的或已編輯文件是否包含有效 JSON。您甚至可以移至不正確的區段，以取得有關驗證錯誤的詳細資訊。

	![[Document Explorer] 無效 JSON 反白顯示的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/invalidjson1.png)

- 此外，Document Explorer 會防止您儲存包含無效 JSON 內容的文件。

	![[Document Explorer] 無效 JSON 儲存錯誤的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/invalidjson2.png)

- 最後，Document Explorer 可讓您輕鬆地檢視目前載入之文件的系統屬性，您只需按一下 [屬性] 命令即可。

	![[Document Explorer] 文件屬性檢視的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/documentproperties.png)

	> [AZURE.NOTE]時間戳記 (_ts) 屬性在內部會以 Epoch 時間表示，但是 Document Explorer 會以一般人可判讀的 GMT 格式顯示此值。

##<a id="BulkAdd"></a>使用 Document Explorer 大量新增文件##

Document Explorer 支援大量擷取一或多個現有 JSON 文件。

1. 若要開始上傳程序，請按一下 [新增文件] 命令。

	![[Document Explorer] 大量擷取功能的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/adddocument1.png)

2. 此時會開啟新的分頁。按一下 [瀏覽] 按鈕以開啟 [檔案總管] 視窗，並選取要上傳的一或多個 JSON 文件。

	![[Document Explorer] 大量擷取程序的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/adddocument2.png)

	> [AZURE.NOTE]Document Explorer 的每個個別上傳作業目前支援多達 100 個 JSON 文件。

3. 對您的選取項目感到滿意後，請按一下 [上傳] 按鈕。文件會自動新增至 Document Explorer 方格，且上傳結果會顯示為作業進度。系統會針對個別檔案回報匯入失敗。

	![[Document Explorer] 大量擷取結果的螢幕擷取畫面](./media/documentdb-view-JSON-document-explorer/adddocument3.png)

4. 作業完成後，您可以另外選取多達 100 個文件進行上傳。

##<a name="NextSteps"></a>接續步驟

若要深入了解 DocumentDB，請按一下[這裡](http://azure.com/docdb)。
 

<!---HONumber=July15_HO1-->