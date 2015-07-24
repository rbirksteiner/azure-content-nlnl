<properties 
	pageTitle="監視邏輯應用程式" 
	description="若要查看邏輯應用程式的完成項目。" 
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
	ms.date="03/10/2015"
	ms.author="stepsic"/>

#監視邏輯應用程式

在遵循[建立邏輯應用程式](app-service-logic-create-a-logic-app.md)所述步驟建立了邏輯應用程式之後，您可以看到其在 Azure 入口網站執行的完整記錄。若要檢視記錄，請按一下入口網站畫面左側的 [**瀏覽**]，然後選取 [**邏輯應用程式**]。您訂閱中的邏輯應用程式清單便會隨即顯示。邏輯應用程式可以是 [**已啟用**] 或 [**已停用**]。**已啟用**邏輯應用程式代表觸發程序會執行邏輯應用程式以回應觸發程序事件，**已停用**邏輯應用程式代表將不會執行邏輯應用程式以回應事件。

![概觀](./media/app-service-logic-monitor-your-logic-apps/overview.png)

出現邏輯應用程式分頁時，有兩個區段相當有用：

- [**摘要**] 會告訴您最新狀態，而且還是編輯邏輯應用程式的進入點。
- [**所有執行**] 會顯示此邏輯應用程式已執行的清單。

##執行

![所有規則](./media/app-service-logic-monitor-your-logic-apps/allruns.png)

這份執行清單會顯示特定執行的**開始時間**、**執行識別碼** (呼叫 REST API 時，您可以使用此識別碼) 和**持續時間**。按一下任何資料列以查看該執行的詳細資料。

[詳細資料] 分頁會顯示包含執行中所有動作之執行時間和順序的圖表。圖表下面會是所有已執行動作的完整清單。

![執行和動作](./media/app-service-logic-monitor-your-logic-apps/runandaction.png)

最後，在某個特定動作上，您可以取得所有傳遞至該動作的資料，以及從 [**輸入**] 和 [**輸出**] 區段中接收的資料。

另外一個重要資訊是**追蹤識別碼**。這個識別碼會在所有動作呼叫的標頭中進行傳遞。如果在您自己的服務內已有記錄，我們建議您記錄此追蹤識別碼，您便可以使用此識別碼來交互參照自己的記錄檔。

##觸發程序記錄和版本控制

目前有兩個額外功能尚未在 UI 中提供使用 (即將推出)，但已可透過 [REST API](http://go.microsoft.com/fwlink/?LinkID=525617&clcid=0x409) 使用。

1. **觸發程序記錄** - 輪詢觸發程序會在某些時間間隔上檢查 API，但不一定會開始執行，這會視回應而定 (例如 `200` 代表要執行，而 `202` 代表不要執行)。觸發程序記錄可讓您查看所有發生但未執行邏輯應用程式的呼叫 (`202` 回應)。

2. **舊版** - 更新邏輯應用程式的定義時，系統會儲存舊版的定義。這是因為如果您已經有進行中的執行，則該執行將會參考執行啟動時已存在的邏輯應用程式版本。執行正在進行時，您無法變更執行的定義。版本記錄 REST API 可讓您存取這項資訊。
 

<!---HONumber=62-->