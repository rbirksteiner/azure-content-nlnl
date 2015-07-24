<properties 
	pageTitle="使用 [查詢總管]，針對 DocumentDB 集合建立、編輯和執行 SQL 查詢 | Azure" 
	description="深入了解 DocumentDB 查詢總管是一項 Azure 預覽入口網站工具，可用來針對 DocumentDB 集合建立、編輯和執行 SQL 查詢。" 
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

# 使用 [查詢總管]，針對 DocumentDB 集合建立、編輯和執行 SQL 查詢 #

本篇文章提供了 [Microsoft Azure DocumentDB](http://azure.microsoft.com/services/documentdb/) 查詢總管的概觀，它是一項 Azure Preview 入口網站工具，可讓您針對 DocumentDB 集合建立、編輯和執行查詢。

完成本教學課程後，您將能夠回答下列問題：

-	如何透過網頁瀏覽器輕鬆地針對 DocumentDB 集合建立、編輯和執行查詢？
-	如何透過網頁瀏覽器輕鬆地瀏覽 DocumentDB 查詢結果頁面？
-	如何疑難排解 DocumentDB 查詢的語法錯誤？ 

##<a id="Launch"></a>啟動與瀏覽 [查詢總管]##

您可以從任何 DocumentDB 帳戶、資料庫和集合分頁啟動 [查詢總管] 。
  
1. 只要在 DocumentDB 帳戶或資料庫刀鋒視窗頂端按一下 [查詢總管] 命令即可。

	![[查詢總管] 命令的螢幕擷取畫面](./media/documentdb-query-collections-query-explorer/queryexplorercommand.png)

2. 您也會在每個刀鋒視窗底部附近看到 [開發人員工具] 透鏡，其中包含 [查詢總管] 磚。
	
	![[查詢總管] 部件的螢幕擷取畫面](./media/documentdb-query-collections-query-explorer/queryexplorerpart.png)

2. 您只需按一下磚便可啟動 [查詢總管]。

	[資料庫] 和 [集合] 下拉式清單方塊會根據您在其中啟動 [查詢總管] 的內容預先填入。例如，如果您要從資料庫刀鋒視窗啟動，則系統會預先填入目前資料庫。如果您要從集合刀鋒視窗啟動，則系統會預先填入目前集合。

	![[查詢總管] 的螢幕擷取畫面](./media/documentdb-query-collections-query-explorer/queryexplorerinitial.png)

##<a id="Create"></a>使用查詢總管建立、編輯和執行查詢##

[查詢總管] 可讓您輕鬆地針對 DocumentDB 集合建立、編輯和執行查詢，並包含醒目提示的基本關鍵字和值以改善查詢撰寫經驗。

- 初次開啟 [查詢總管] 時，系統會提供 SELECT * FROM 的預設查詢。您可以接受預設查詢或建構自己的查詢，然後按一下 [執行查詢] 按鈕以檢視結果。[查詢總管] 支援 DocumentDB SQL 查詢語言，如[查詢 DocumentDB](documentdb-sql-query.md) 中所述。

	![[查詢總管] 查詢結果的螢幕擷取畫面](./media/documentdb-query-collections-query-explorer/queryresults1.png)

- 您也可以輸入多個查詢、反白一個想要執行的查詢，然後按一下 [執行查詢] 按鈕以檢視結果。

	![[查詢總管] 反白與執行的螢幕擷取畫面](./media/documentdb-query-collections-query-explorer/queryexplorerhighlightandrun.png)

- 您可以使用 [載入檔案] 命令載入現有檔案的內容。

	![[查詢總管] 載入檔案的螢幕擷取畫面](./media/documentdb-query-collections-query-explorer/loadqueryfile.png)

- 根據預設，[查詢總管] 會傳回結果，以 100 為一組。如果您的查詢產生的結果超過 100 個，您只需使用 [下一頁] 和 [上一頁] 命令即可瀏覽結果集。

	![[查詢總管] 分頁支援的螢幕擷取畫面](./media/documentdb-query-collections-query-explorer/queryresultspagination.png)

- 成功的查詢可提供如要求費用、目前顯示的結果集，以及是否有更多結果等資訊，您可以透過先前所述的 [下一頁] 命令進行存取。

	![[查詢總管] 查詢資訊的螢幕擷取畫面](./media/documentdb-query-collections-query-explorer/queryinformation.png)

- 同樣地，如果查詢完成但發生錯誤，則 [查詢總管] 會顯示可協助疑難排解工作的錯誤清單。

	![[查詢總管] 查詢錯誤的螢幕擷取畫面](./media/documentdb-query-collections-query-explorer/queryerror.png)

##<a name="NextSteps"></a>接續步驟

- 若要深入了解 DocumentDB，請按一下[這裡](http://azure.com/docdb)。
- 若要深入了解 [查詢總管] 中支援的 DocumentDB SQL 文法，請按一下[這裡](documentdb-sql-query.md)。
 

<!---HONumber=July15_HO1-->