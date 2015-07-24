<properties
    pageTitle="啟用行動應用程式的離線同步處理 (Xamarin iOS)"
    description="了解如何在 Xamarin iOS 應用程式中使用應用程式服務行動應用程式快取和同步離線資料"
    documentationCenter="xamarin"
    authors="lindydonna"
    manager="dwrede"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="03/26/2015"
    ms.author="donnam"/>

# 啟用 Xamarin iOS 行動應用程式的離線同步處理

[AZURE.INCLUDE [app-service-mobile-selector-offline-preview](../../includes/app-service-mobile-selector-offline-preview.md)]

本教學課程說明 iOS 之行動應用程式的離線同步處理功能。離線同步處理可讓使用者與行動應用程式進行互動--檢視、新增或修改資料--即使沒有網路連線進也可行。變更會儲存在本機資料庫中︰裝置上線後，這些變更就會與遠端服務進行同步處理。

離線同步具有幾種潛在用途：

* 在裝置上本機快取伺服器資料，以改善應用程式回應性
* 讓應用程式能夠在網路連線中斷後恢復
* 讓使用者即使在沒有網路存取的情況下仍能建立及修改資料，而支援連線微弱或無連線的情況
* 同步多個裝置之間的資料，並在兩個裝置修改相同的記錄時偵測衝突

如果這是您第一次接觸行動應用程式，先完成[建立 Xamarin iOS 應用程式]教學課程。

本教學課程需要下列各項：

* Visual Studio 2013
* Visual Studio [Xamarin 延伸] **或** [Xamarin Studio] (在 OS X 上)

## 檢閱行動應用程式同步處理程式碼

行動應用程式離線同步處理可讓使用者在無法存取網路時，仍可與本機資料庫互動。若要在您的應用程式中使用這些功能，您必須將 `MobileServiceClient.SyncContext` 初始化至本機存放區。接著，請透過 `IMobileServiceSyncTable` 介面參考您的資料表。本節將逐步解說 `QSTodoService.cs` 中的離線同步處理相關程式碼。

1. 在 Visual Studio 中，開啟您在 [開始使用行動應用程式教學課程中完成的專案]。開啟檔案 `QSTodoService.cs`。

2. 請注意，`todoTable` 成員的類型為 `IMobileServiceSyncTable`。離線同步處理會使用此同步處理資料表介面，而不是 `IMobileServiceTable`。使用同步處理資料表時，所有作業都會移至本機存放區，而且只與具有明確推送和提取作業的遠端服務同步處理。

    若要取得同步處理資料表的參考，應使用 `GetSyncTable()` 方法。若要移除離線同步處理功能，您應改用 `GetTable()`。

3. 必須先初始化本機存放區，才可以執行資料表作業。此動作可用 `InitializeStoreAsync` 方法來完成：

        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }

    這會使用 `MobileServiceSQLiteStore` 類別 (在行動服務 SDK 中提供) 建立本機存放區。您也可以實作 `IMobileServiceLocalStore`，以提供不同的本機存放區實作。

    `DefineTable` 方法會在本機存放區中建立與給定類型中的欄位相符的資料表，在此案例中為 `ToDoItem`。此類型不一定要包含遠端資料庫中的所有資料行 -- 可以只儲存資料行的子集。

<!--     This overload of `InitializeAsync` uses the default conflict handler, which fails whenever there is a conflict. To provide a custom conflict handler, see the tutorial [Handling conflicts with offline support for Mobile Services].
 -->
4. `SyncAsync` 方法會觸發實際的同步處理作業：

        public async Task SyncAsync()
        {
            try
            {
                await client.SyncContext.PushAsync();
                await todoTable.PullAsync("allTodoItems", todoTable.CreateQuery()); // query ID is used for incremental sync
            }

            catch (MobileServiceInvalidOperationException e)
            {
                Console.Error.WriteLine(@"Sync Failed: {0}", e.Message);
            }
        }

    首先會呼叫 `IMobileServiceSyncContext.PushAsync()`。此方法是 `IMobileServicesSyncContext` 的成員之一 (而不是同步資料表)，因為它會在所有資料表之間推送變更。只有以某種方式在本機上修改過的記錄 (透過 CUD 作業)，才會傳送至伺服器。

    接下來，此方法會呼叫 `IMobileServiceSyncTable.PullAsync()`，以將資料從伺服器上的資料表提取至應用程式。請注意，如果同步處理內容中有任何擱置的變更，提取一律會先發出推送動作。這是為了確保本機存放區中的所有資料表與關係都能維持一致。在此情況下，我們明確呼叫了推送動作。

    在此範例中，我們擷取遠端 `TodoItem` 資料表中的所有記錄，但也可以藉由傳遞查詢來篩選記錄。`PullAsync()` 的第一個參數是用於增量同步處理的查詢識別碼，會使用 `UpdatedAt` 時間戳記取得自從上次同步後修改過的記錄。對您應用程式中的每個邏輯查詢而言，查詢識別碼應該是唯一的描述性字串。若選擇不要增量同步處理，請傳遞 `null` 做為查詢識別碼。這會擷取每個提取作業的所有記錄，而可能降低效能。

<!--     >[AZURE.NOTE] To remove records from the device local store when they have been deleted in your mobile service database, you should enable [Soft Delete]. Otherwise, your app should periodically call `IMobileServiceSyncTable.PurgeAsync()` to purge the local store.

    Note that the `MobileServicePushFailedException` can occur for both a push and a pull operation. The next tutorial, [Handling conflicts with offline support for Mobile Services], shows how to handle these sync related exceptions.
-->

5. 在 `QSTodoService` 類別中，`SyncAsync()` 方法會在修改資料的作業 (`InsertTodoItemAsync()` 和 `CompleteItemAsync`) 之後呼叫。此方法也會從 `RefreshDataAsync()` 呼叫，以便使用者每次執行重新整理動作時都能取得最新的資料。應用程式也會在啟動時執行同步處理，因為 `QSTodoListViewController.ViewDidLoad()` 會呼叫 `RefreshDataAsync()`。

    因為每次修改資料時都會呼叫 `SyncAsync()`，所以此應用程式假設使用者每次在編輯資料時都處於線上狀態。在下一節中我們將更新應用程式，讓使用者即使在離線時也可以編輯。

## 更新應用程式的同步處理行為

在本節中，您將修改應用程式，使應用程式不會在啟動或執行插入和更新作業時同步處理，而只有在執行重新整理動作時才會同步處理。然後，您將中斷行動後端的應用程式連線，以模擬離線狀態。當您新增資料項目時，這些項目會存放在本機存放區中，但不會立即同步處理至行動後端資料存放區。

1. 開啟 `QSTodoService.cs`。在下列方法中將 `SyncAsync()` 的呼叫註解化：

    - `InsertTodoItemAsync`
    - `CompleteItemAsync`
    - `RefreshAsync`

    現在，`RefreshAsync()` 將只會從本機存放區中載入資料，但不會連接到應用程式後端。

2. 在 `QSTodoService.cs` 中變更 `applicationURL` 的定義，以指向無效的行動應用程式 URI：

        const string applicationURL = @"https://your-service.azurewebsites.xxx/"; // invalid URI

3. 若要確保在執行重新整理筆勢時會同步處理資料，請編輯 `QSTodoListViewController.RefreshAsync()` 方法。在呼叫 `RefreshDataAsync()` 之前，新增 `SyncAsync()` 的呼叫：

        private async Task RefreshAsync ()
        {
            RefreshControl.BeginRefreshing ();

            await todoService.SyncAsync();
            await todoService.RefreshDataAsync (); // add this line

            RefreshControl.EndRefreshing ();

            TableView.ReloadData ();
        }

4. 建置並執行應用程式。新增一些新的 todo 項目。這些新的項目在可推送至行動後端之前，都只會存留在本機存放區中。用戶端應用程式的行為，會如同它已連接到支援所有建立、讀取、更新、刪除 (CRUD) 作業的後端。

5. 關閉應用程式並重新加以開啟，以驗證您所建立的新項目持續存留於本機存放區中。

## 更新應用程式以重新連接您的行動後端

在本節中，您會將應用程式重新連接到行動後端，而模擬回到線上狀態的應用程式。當您執行重新整理動作時，資料將會同步處理至您的行動後端中。

1. 開啟 `QSTodoService.cs`。移除無效的行動應用程式 URL，並重新新增正確的 URL 和應用程式金鑰。

2. 重新建置並執行應用程式。請注意，雖然應用程式現在已連接到行動後端，但資料並未變更。這是因為此應用程式一律會使用指向本機存放區的 `IMobileServiceSyncTable`。

3. 連接到後端 SQL 資料庫，以檢視已儲存的資料。在 Visual Studio 中，移至 [**伺服器總管**] -> [**Azure**] -> [**SQL 資料庫**]。在資料庫上按一下滑鼠右鍵，並選取 [**在 SQL Server 物件總管中開啟**]。

    請注意，資料並*未* 與資料庫和本機存放區進行同步處理。

4. 在應用程式中拉下項目清單，以執行重新整理動作。這會導致應用程式呼叫 `RefreshDataAsync()`，接著呼叫 `SyncAsync()`。這將執行推送和提取作業，先將本機存放區項目傳送至行動後端中，再從後端中擷取新的資料。

5. 重新整理資料庫檢視，並確認變更已同步處理。

<!-- ##Summary

[AZURE.INCLUDE [mobile-services-offline-summary-csharp](../../includes/mobile-services-offline-summary-csharp.md)]

## Next steps

* [Handling conflicts with offline support for Mobile Services]

* [How to use the Xamarin Component client for Azure Mobile Services]
 -->

<!-- Images -->

<!-- URLs. -->
[建立 Xamarin iOS 應用程式]: ../app-service-mobile-dotnet-backend-xamarin-ios-get-started.md

[How to use the Xamarin Component client for Azure Mobile Services]: ../partner-xamarin-mobile-services-how-to-use-client-library.md

[Xamarin Studio]: http://xamarin.com/download
[Xamarin 延伸]: http://xamarin.com/visual-studio
 

<!---HONumber=62-->