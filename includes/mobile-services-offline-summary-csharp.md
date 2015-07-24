為了支援行動服務的離線功能，我們使用了 `IMobileServiceSyncTable` 介面，並對本機存放區初始化 `MobileServiceClient.SyncContext`。在此案例中，我們以本機存放區做為 SQLite 資料庫。

正常情況下，在行動服務的 CRUD 作業執行時，應用程式會如同仍處於連接狀態，但所有的作業都會對本機存放區執行。

當我們要同步處理本機存放區與伺服器時，我們使用 `IMobileServiceSyncTable.PullAsync` 和 `MobileServiceClient.SyncContext.PushAsync` 方法。

*  為了將變更推送至伺服器，我們呼叫了 `IMobileServiceSyncContext.PushAsync()`。此方法是 `IMobileServicesSyncContext` 的成員之一 (而不是同步資料表)，因為它會在所有資料表之間推送變更。

    只有以某種方式在本機上修改過的記錄 (透過 CUD 作業)，才會傳送至伺服器。
   
* 為了將資料從伺服器上的資料表提取至應用程式，我們呼叫了 `IMobileServiceSyncTable.PullAsync`。

    提取一律會先發出推送動作。這是為了確保本機存放區中的所有資料表和關聯性都保持一致。

    此外還有 `PullAsync()` 的多載，可指定查詢以篩選儲存在用戶端上的資料。如果未傳遞查詢，`PullAsync()` 將會在對應的資料表 (或查詢) 中提取所有資料列。您可以傳遞查詢，只篩選應用程式需要同步處理的變更。

* 若要啟用增量同步處理，請將查詢識別碼傳遞至 `PullAsync()`。查詢識別碼用來儲存上次提取作業所產生的上次更新時間戳記。對您應用程式中的每個邏輯查詢而言，查詢識別碼應該是唯一的描述性字串。如果查詢有一個參數，則相同的參數值必須屬於查詢識別碼的一部分。

    例如，如果您要針對使用者識別碼篩選，它必須是查詢識別碼的一部分：

        await PullAsync("todoItems" + userid, syncTable.Where(u => u.UserId = userid));

    如果您想選擇不要增量同步處理，則傳遞 `null` 做為查詢識別碼。在此情況下，每次呼叫 `PullAsync` 時都會擷取所有的記錄可能會沒有效率。

* 若要從裝置本機存放區中移除已在您行動服務資料庫中刪除的記錄，請啟用 [虛刪除]。否則，您的應用程式應定期呼叫 `IMobileServiceSyncTable.PurgeAsync()` 才能清除本機存放區。

<!---HONumber=July15_HO2-->