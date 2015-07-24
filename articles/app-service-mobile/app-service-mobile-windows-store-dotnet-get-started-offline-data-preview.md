<properties
	pageTitle="搭配 Azure 行動應用程式使用離線資料 (Windows Store) | 行動開發人員中心"
	description="了解如何使用 Azure 行動服務快取和同步 Windows 市集應用程式中的離線資料"
	documentationCenter="windows"
	authors="christopheranderson"
	manager="dwrede"
	editor=""
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/22/2015"
	ms.author="chrande"/>

# 使用 Azure 行動應用程式中的離線資料同步

[AZURE.INCLUDE [app-service-mobile-selector-offline-preview](../../includes/app-service-mobile-selector-offline-preview.md)]


本教學課程說明如何使用 Azure 行動應用程式後端在 Windows Universal Store 應用程式中新增離線支援。當您的應用程式處於離線狀態時，離線支援可讓您與本機資料庫互動。一旦您的應用程式與後端資料庫連線之後，您就可使用離線功能來同步處理本機變更。



在本教學課程中，您將會更新[建立 Windows 應用程式]教學課程中的通用應用程式，以支援 Azure 行動應用程式的離線功能。接著，您會在中斷連線的離線狀態下新增資料、將這些項目同步處理至線上資料庫，然後登入 Azure 管理入口網站，以檢視執行應用程式時對資料所做的變更。

本教學課程將逐步引導您完成下列基本步驟：

1. [更新應用程式以支援離線功能]
2. [更新應用程式的同步處理行為]
3. [更新應用程式以重新連接您的行動應用程式後端]

本教學課程需要下列各項：

* 執行於 Windows 8.1 的 Visual Studio 2013。
* 完成[建立 Windows 應用程式]。
* [Azure 行動服務 SDK 2.0.0 版 (或更新版本)][Azure Mobile App SDK Nuget]
* [Azure 行動服務 SQLite Store 1.0.2 版 (或更新版本)][SQLite store Nuget]
* [SQLite for Windows 8.1](www.sqlite.org/downloads)

>[AZURE.NOTE]若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Azure 免費試用</a>。

## <a name="enable-offline-app"></a>更新應用程式以支援離線功能

Azure 行動應用程式的離線功能可讓您在行動服務處於離線狀態時，仍可與本機資料庫互動。若要在您的應用程式中使用這些功能，您必須將 `MobileServiceClient.SyncContext` 初始化至本機存放區。接著，請透過 `IMobileServiceSyncTable` 介面參考您的資料表。在本教學課程中，我們會在本機存放區使用 SQLite。

1. 安裝適用於 Windows 8.1 和 Windows Phone 8.1 的 SQLite 執行階段。

    * **Windows 8.1 執行階段：**安裝 [SQLite for Windows 8.1]。
    * **Windows Phone 8.1：**安裝 [SQLite for Windows Phone 8.1]。

    >[AZURE.NOTE]如果您要使用 Internet Explorer，當您按一下安裝 SQLite 的連結時，系統會提示您下載 .zip 檔案格式的 .vsix。請以 .vsix 副檔名將此檔案儲存至您的硬碟，而不要使用 .zip。在 Windows 檔案總管中按兩下此 .vsix 檔案，以執行安裝。

2. 在 Visual Studio 中，開啟您在[建立 Windows 應用程式]教學課程中完成的專案。安裝適用於 Windows 8.1 執行階段和 Windows Phone 8.1 專案的 **WindowsAzure.MobileServices.SQLiteStore** NuGet 封裝。

    * **Windows 8.1：**在 [方案總管] 中，以滑鼠右鍵按一下 Windows 8.1 專案，然後按一下 [管理 Nuget 封裝]，以執行 NuGet 封裝管理員。搜尋 **SQLiteStore** 以安裝 `WindowsAzure.MobileServices.SQLiteStore` 封裝。
    * **Windows Phone 8.1：**以滑鼠右鍵按一下 Windows Phone 8.1 專案，然後按一下 [管理 NuGet 封裝]，以執行 NuGet 封裝管理員。搜尋 **SQLiteStore** 以安裝 `WindowsAzure.MobileServices.SQLiteStore` 封裝。

    >[AZURE.NOTE]如果安裝建立舊版 SQLite 的參考，您可以只刪除該重複的參考。

3. 在方案總管中，以滑鼠右鍵按一下 Windows 8.1 執行階段和 Windows Phone 8.1 平台專案的 [參考]，並確定有 SQLite 的參考位於 [擴充功能] 區段中。

    ![][1] </br>

    **Windows 8.1 執行階段**

    ![][11] </br>

    **Windows Phone 8.1**

4. SQLite Runtime 會要求您變更要建置到 **x86**、**x64** 或 **ARM** 之專案的處理器架構。[任何 CPU] 不受支援。在方案總管中，按一下上方的 [方案]，將處理器架構下拉式方塊變更為您想測試的其中一項支援的設定。

    ![][13]

5. 在方案總管中，開啟共用專案中的 MainPage.cs 檔案。在檔案頂端取消註解下列 using 陳述式：

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;  // offline sync
        using Microsoft.WindowsAzure.MobileServices.Sync;         // offline sync

6. 在 MainPage.cs 中，將 `todoTable` 的定義加入註解，並取消註解下一行中呼叫 `MobileServicesClient.GetSyncTable()` 的定義：

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>(); // offline sync

7. 在 MainPage.cs 中標示為 `Offline sync` 的區域內，取消註解 `InitLocalStoreAsync` 和 `SyncAsync` 方法。`InitLocalStoreAsync` 方法會初始化與 SQLite 存放區的用戶端同步處理內容。

        private async Task InitLocalStoreAsync()
        {
            if (!App.MobileService.SyncContext.IsInitialized)
            {
                var store = new MobileServiceSQLiteStore("localstore.db");
                store.DefineTable<TodoItem>();
                await App.MobileService.SyncContext.InitializeAsync(store);
            }

            await SyncAsync();
        }

        private async Task SyncAsync()
        {
            await App.MobileService.SyncContext.PushAsync();
            await todoTable.PullAsync("todoItems", todoTable.CreateQuery());
        }

8. 在 `OnNavigatedTo` 事件處理常式中，取消註解對 `InitLocalStoreAsync` 的呼叫：

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await InitLocalStoreAsync(); // offline sync
            await RefreshTodoItems();
        }

9. 取消註解 `InsertTodoItem`、`UpdateCheckedTodoItem` 和 `ButtonRefresh_Click` 方法中對 `SyncAsync` 的 3 個呼叫：

        private async Task InsertTodoItem(TodoItem todoItem)
        {
            await todoTable.InsertAsync(todoItem);
            items.Add(todoItem);

            await SyncAsync(); // offline sync
        }

        private async Task UpdateCheckedTodoItem(TodoItem item)
        {
            await todoTable.UpdateAsync(item);
            items.Remove(item);
            ListItems.Focus(Windows.UI.Xaml.FocusState.Unfocused);

            await SyncAsync(); // offline sync
        }

        private async void ButtonRefresh_Click(object sender, RoutedEventArgs e)
        {
            ButtonRefresh.IsEnabled = false;

            await SyncAsync(); // offline sync
            await RefreshTodoItems();

            ButtonRefresh.IsEnabled = true;
        }

10. 在 `SyncAsync` 方法中新增例外狀況處理常式：

        private async Task SyncAsync()
        {
            String errorString = null;

            try
            {
                await App.MobileService.SyncContext.PushAsync();
                await todoTable.PullAsync("todoItems", todoTable.CreateQuery()); // first param is query ID, used for incremental sync
            }

            catch (MobileServicePushFailedException ex)
            {
                errorString = "Push failed because of sync errors: " +
                  ex.PushResult.Errors.Count + " errors, message: " + ex.Message;
            }
            catch (Exception ex)
            {
                errorString = "Pull failed: " + ex.Message +
                  "\n\nIf you are still in an offline scenario, " +
                  "you can try your Pull again when connected with your Mobile Serice.";
            }

            if (errorString != null)
            {
                MessageDialog d = new MessageDialog(errorString);
                await d.ShowAsync();
            }
        }

    在此範例中，我們擷取遠端 `todoTable` 中的所有記錄，但也可以藉由傳遞查詢來篩選記錄。`PullAsync` 的第一個參數是用於增量同步處理的查詢識別碼，會使用 `UpdatedAt` 時間戳記取得自從上次同步後修改過的記錄。對您應用程式中的每個邏輯查詢而言，查詢識別碼應該是唯一的描述性字串。若選擇不要增量同步處理，請傳遞 `null` 做為查詢識別碼。這會擷取每個提取作業的所有記錄，而可能降低效能。

    請注意，推送和提取作業可能會發生 `MobileServicePushFailedException`。它可能發生於提取，因為提取作業會在內部執行推入，以確定所有資料表及任何關聯性都一致。

11. 在 Visual Studio 中按 **F5** 鍵，以重新建置並執行此應用程式。應用程式的行為會與離線同步變更之前相同，因為它對插入、更新和重新整理作業執行了同步處理作業。

## <a name="update-sync"></a>更新應用程式的同步處理行為

在本節中，您將修改應用程式，使其不會對插入和更新作業執行同步處理，但僅限於按下 [重新整理] 按鈕時。然後，您將中斷行動應用程式後端的應用程式連線，以模擬離線狀態。當您新增資料項目時，這些項目會存放在本機存放區中，但不會同步處理至行動應用程式後端。

1. 開啟共用專案中的 App.xaml.cs。編輯 `InsertTodoItem` 和 `UpdateCheckedTodoItem` 方法，以註解化對 `SyncAsync` 的呼叫。

2. 編輯共用專案中的 App.xaml.cs。註解化 **MobileServiceClient** 的初始化，並新增使用無效行動應用程式 URL 的以下幾行：

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://<your-mobile-service>-code.azurewebsites.fail",
            "https://<your-mobile-service>gateway.azurewebsites.fail",
            "AppKey"
        );

3. 在 `InitLocalStoreAsync()` 中註解化 `SyncAsync()` 的呼叫，使應用程式不會在啟動時執行同步處理。

4. 按 **F5** 以建置並執行應用程式。輸入一些新的 todo 項目，然後為每個項目按一下 [儲存]。新的 todo 項目在可推送至行動應用程式後端之前，都只會存留在本機存放區中。用戶端應用程式的行為，會如同它已連接到支援所有建立、讀取、更新、刪除 (CRUD) 作業的行動應用程式後端。

5. 關閉應用程式並重新加以開啟，以驗證您所建立的新項目持續存留於本機存放區中。

## <a name="update-online-app"></a>更新應用程式以重新連接您的行動應用程式後端

在本節中，您會將應用程式重新連接至行動應用程式後端。您將藉此模擬應用程式在行動應用程式後端中，從離線狀態恢復為線上狀態的情境。當您按一下 [重新整理] 按鈕時，資料會同步處理至您的行動應用程式後端。

1. 開啟共用專案中的 App.xaml.cs。取消註解您先前初始化的 `MobileServiceClient`，以重新新增正確的行動應用程式服務後端 URL、閘道 URL，及應用程式金鑰。

2. 按 **F5** 鍵，以重新建置與執行應用程式。請注意，雖然應用程式此時已連接到行動應用程式後端，但資料看起來仍會與離線狀態下相同。這是因為此應用程式一律會使用指向本機存放區的 `IMobileServiceSyncTable`。

3. 登入 Azure 入口網站，並檢視您的行動應用程式後端的資料庫。

    在 Visual Studio 中，移至 [**伺服器總管**] -> [**Azure**] -> [**SQL Database**]。在資料庫上按一下滑鼠右鍵，並選取 [在 SQL Server 物件總管中開啟]。

    請注意，資料並未與資料庫和本機存放區進行同步處理。

4. 在應用程式中，按 [重新整理] 按鈕。這會使應用程式呼叫 `PushAsync` 和 `PullAsync`。此推送作業會將本機存放區項目傳送至行動服務，然後從行動服務中擷取新的資料。

    推送作業會從 `MobileServiceClient.SyncContext` 執行 (而非 `IMobileServicesSyncTable`)，並推送該同步內容所有相關資料表的變更。這是為了解說資料表之間有所關聯的情況。

5. 在應用程式中，按一下幾個項目旁的核取方塊以在本機存放區中完成它們。

6. 再按一次 [重新整理] 按鈕，以呼叫 `SyncAsync`。`SyncAsync` 會同時呼叫推送和提取，但在此案例中，我們可能已移除 `PushAsync` 的呼叫。這是因為**提取一律會先執行推送**。這是為了確保本機存放區中的所有資料表和關聯性都保持一致。


##摘要

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

* 您的應用程式應該定期呼叫 `IMobileServiceSyncTable.PurgeAsync()` 以清除本機存放區。


<!-- Anchors. -->
[更新應用程式以支援離線功能]: #enable-offline-app
[更新應用程式的同步處理行為]: #update-sync
[更新應用程式以重新連接您的行動應用程式後端]: #update-online-app
[Next Steps]: #next-steps

<!-- Images -->
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data-preview/app-service-mobile-add-reference-sqlite-dialog.png
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data-preview/app-service-mobile-add-wp81-reference-sqlite-dialog.png
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data-preview/cpu-architecture.png


<!-- URLs. -->
[TodoList Offline Sample]: http://go.microsoft.com/fwlink/?LinkId=394777
[建立 Windows 應用程式]: /documentation/articles/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview/
[SQLite for Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
[SQLite for Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkId=397953

[Azure Mobile App SDK Nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices/2.0.0-beta
[SQLite store nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.2
 

<!---HONumber=62-->