<properties 
	pageTitle="在行動服務中使用離線資料 (Windows Phone) | 行動開發人員中心" 
	description="了解如何在 Windows Phone 應用程式中對於同步離線資料使用 Azure 行動服務。" 
	documentationCenter="mobile-services" 
	authors="lindydonna" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/16/2015" 
	ms.author="wesmc;donnam"/>

# 在行動服務中使用離線資料同步

[AZURE.INCLUDE [mobile-services-selector-offline](../../includes/mobile-services-selector-offline.md)]


本主題將說明如何使用 Azure 行動服務的離線功能。Azure 行動服務的離線功能可讓您在行動服務處於離線狀態時，仍可與本機資料庫互動。離線功能可讓您在重新上線時同步處理本機變更與行動服務。

在本教學課程中，您將更新[開始使用資料]教學課程中的應用程式，以支援 Azure 行動服務的離線功能。接著，您會在中斷連線的離線狀態下新增資料、將這些項目同步處理至線上資料庫，然後登入 Azure 管理入口網站，以檢視執行應用程式時對資料所做的變更。


>[AZURE.NOTE]本教學課程旨在協助您深入了解如何透過行動服務，來使用 Azure 儲存並擷取 Windows Phone 應用程式中的資料。如果這是您第一次接觸行動服務，請考慮先完成[開始使用行動服務]和[開始使用資料]教學課程。

本教學課程將逐步引導您完成下列基本步驟：

1. [更新應用程式以支援離線功能]
2. [在離線狀態下測試應用程式] 
3. [更新應用程式以重新連接您的行動服務]
4. [測試連接到行動服務的應用程式]

本教學課程需要下列各項：

* Visual Studio 2012
* [Windows Phone 8 SDK]
* 完成[開始使用資料]教學課程。
* [Azure 行動服務 SDK 1.3.0 版 (或更新版本)][Mobile Services SDK Nuget]
* [Azure 行動服務 SQLite Store 1.0.0 版 (或更新版本)][SQLite store nuget]
* [SQLite for Windows Phone 8]

>[AZURE.NOTE]若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Azure 免費試用</a>。

## <a name="enable-offline-app"></a>更新應用程式以支援離線功能

Azure 行動服務的離線功能可讓您在行動服務處於離線狀態時，仍可與本機資料庫互動。若要在您的應用程式中使用這些功能，您必須將 `MobileServiceClient.SyncContext` 初始化至本機存放區。接著，請透過 `IMobileServiceSyncTable` 介面參考您的資料表。

本節將以 SQLite 做為離線功能的本機存放區。

>[AZURE.NOTE]您可以略過本節，直接下載某個已具有離線支援的「開始使用」專案。若要下載具有離線支援的專案，請參閱[開始使用 Windows Phone 的離線範例]。


1. 安裝 SQLite for Windows Phone 8 專案。您可以從連結 [SQLite for Windows Phone 8] 加以安裝。

    >[AZURE.NOTE]如果您要使用 Internet Explorer，當您按一下安裝 SQLite 的連結時，系統會提示您下載 .zip 檔案格式的 .vsix。請以 .vsix 副檔名將此檔案儲存至您的硬碟，而不要使用 .zip。在 Windows 檔案總管中按兩下此 .vsix 檔案，以執行安裝。

2. 在 Visual Studio 中，開啟您在[開始使用行動服務]或[開始使用資料]教學課程中完成的專案。在 [方案總管] 中，以滑鼠右鍵按一下專案下的 [參考]，然後在 [Windows Phone]>[擴充功能] 下新增 [SQLite for Windows Phone] 的參考。

    ![][1]

3. SQLite Runtime 會要求您變更要建置到 **x86**、**x64** 或 **ARM** 之專案的處理器架構。[任何 CPU] 不受支援。將處理器架構變更為您要測試的其中一項支援設定。

    ![][11]

4. 在 Visual Studio 的 [方案總管] 中，以滑鼠右鍵按一下您的用戶端應用程式專案，然後按一下 [管理 NuGet 封裝]，以執行 NuGet 封裝管理員。搜尋 **SQLiteStore**，以安裝 **WindowsAzure.MobileServices.SQLiteStore** 封裝。

    ![][2]

5. 在 Visual Studio 的 [方案總管] 中，開啟 MainPage.xaml.cs 檔案。在檔案頂端新增下列 using 陳述式。

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Newtonsoft.Json.Linq;

6. 在 Mainpage.xaml.cs 中，將 `todoTable` 的宣告以屬於 `IMobileServicesSyncTable` 類型、藉由呼叫 `MobileServicesClient.GetSyncTable()` 而初始化的宣告來取代。

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>();

7. 在 MainPage.xaml.cs 中更新 `TodoItem` 類別，使該類別包含 **Version** 系統屬性，如下所示。

        public class TodoItem
        {
          public string Id { get; set; }
          [JsonProperty(PropertyName = "text")]
          public string Text { get; set; }
          [JsonProperty(PropertyName = "complete")]
          public bool Complete { get; set; }
          [Version]
          public string Version { get; set; }
        }


8. 在 MainPage.xaml.cs 中更新 `OnNavigatedTo` 事件處理常式，使其成為 `async` 方法，並對 SQLite 存放區初始化用戶端同步內容。此 SQLite 存放區可使用與行動服務資料表的結構描述相符的資料表建立，但其中必須包含在上一個步驟中新增的 **Version** 系統屬性。

        protected async override void OnNavigatedTo(NavigationEventArgs e)
        {
            if (!App.MobileService.SyncContext.IsInitialized)
            {
                var store = new MobileServiceSQLiteStore("localsync12.db");
                store.DefineTable<TodoItem>();
                await App.MobileService.SyncContext.InitializeAsync(store, new MobileServiceSyncHandler());
            }
            RefreshTodoItems();
        }

9. 在 Visual Studio 的 [方案總管] 中，開啟 MainPage.xaml 檔案。尋找 [重新整理] 按鈕的按鈕定義。將其取代為下列堆疊面板定義。

    此程式碼會為 [推送] 和 [提取] 作業新增兩個具有 click 事件處理常式的按鈕控制項。這些按鈕會在重新整理按鈕的所在之處水平排列。儲存檔案。

        <StackPanel  Orientation="Horizontal" Grid.Row="3" Grid.ColumnSpan="2" HorizontalAlignment="Center">
          <Button Name="ButtonRefresh" Click="ButtonRefresh_Click" Width="160">Refresh</Button>
          <Button Name="ButtonPush" Click="ButtonPush_Click" Width="160">Push</Button>
          <Button Name="ButtonPull" Click="ButtonPull_Click" Width="160">Pull</Button>
        </StackPanel>

    同樣地，依照下列螢幕擷取畫面變更文字區塊的文字。

    ![][12]
        


10. 在 MainPage.xaml.cs 中，為 [推送] 和 [提取] 按鈕新增按鈕 click 事件處理常式，並儲存檔案。

        private async void ButtonPull_Click(object sender, RoutedEventArgs e)
        {
            Exception pullException = null;
            try
            {
                await todoTable.PullAsync("todoItems", todoTable.CreateQuery()); // first param is query ID, used for incremental sync
                RefreshTodoItems();
            }
            catch (Exception ex)
            {
                pullException = ex;
            }
            if (pullException != null) {
                MessageBox.Show("Pull failed: " + pullException.Message +
                  "\n\nIf you are in an offline scenario, " + 
                  "try your Pull again when connected with your Mobile Serice.");
            }
        }
        private async void ButtonPush_Click(object sender, RoutedEventArgs e)
        {
            string errorString = null;
            try
            {
                await App.MobileService.SyncContext.PushAsync();
                RefreshTodoItems();
            }
            catch (MobileServicePushFailedException ex)
            {
                errorString = "Push failed because of sync errors: " + 
                  ex.PushResult.Errors.Count() + ", message: " + ex.Message;
            }
            catch (Exception ex)
            {
                errorString = "Push failed: " + ex.Message;
            }
            if (errorString != null) {
                MessageBox.Show(errorString + 
                  "\n\nIf you are in an offline scenario, " + 
                  "try your Push again when connected with your Mobile Serice.");
            }
        }

11. 目前還不要執行應用程式。請按 **F7** 鍵，以重新建置專案。驗證未發生任何建置錯誤。

## <a name="test-offline-app"></a>在離線狀態下測試應用程式

在本節中，您將中斷行動服務的應用程式連線，以模擬離線狀態。接著，您會新增某些將保存在本機存放區中的資料項目。

請注意，在本節中，應用程式不應連接到任何行動服務。因此，如果您測試 [推送] 和 [提取] 按鈕，將會擲出例外狀況。在下一節中，您會將此用戶端應用程式重新連接到行動服務，以測試將存放區與行動服務資料庫同步處理的 [推送] 和 [提取] 作業。


1. 在 Visual Studio 的 [方案總管] 中，開啟 App.xaml.cs。將您 URL 的 "**azure-mobile.net**" 取代為 "**azure-mobile.xxx**"，以將 **MobileServiceClient** 的初始化變更為無效位址。接著，請儲存檔案。

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.xxx/",
            "AppKey"
        );

2. 在 Visual Studio 中按 **F5**，以建置並執行此應用程式。輸入新的 todo 項目，然後按一下 [儲存]。新的 todo 項目在可推送至行動服務之前，都只會存留在本機存放區中。用戶端應用程式的行為，會如同它已連接到支援所有建立、讀取、更新、刪除 (CRUD) 作業的行動服務。

    ![][4]

3. 關閉應用程式並重新加以開啟，以驗證您所建立的新項目持續存留於本機存放區中。

## <a name="update-online-app"></a>更新應用程式以重新連接您的行動服務

在本節中，您會將應用程式重新連接至行動服務。您將藉此模擬應用程式在行動服務中從離線狀態恢復為線上狀態的情境。


1. 在 Visual Studio 的 [方案總管] 中，開啟 App.xaml.cs。將您 URL 的 "**azure-mobile.xxx**" 取代為 "**azure-mobile.net**"，以將 **MobileServiceClient** 的初始化重新變更為正確的位址。接著，請儲存檔案。

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.net/",
            "Your AppKey"
        );


## <a name="test-online-app"></a>測試連接到行動服務的應用程式


在本節中，您會測試將本機存放區與行動服務資料庫同步處理的推送和提取作業。

1. 在 Visual Studio 中按 **F5** 鍵，以重新建置並執行此應用程式。請注意，雖然應用程式此時已連接到行動服務，但資料看起來仍會與離線狀態下相同。這是因為此應用程式一律會使用指向本機存放區的 `IMobileServiceSyncTable`。

    ![][4]

2.  登入 Microsoft Azure 管理入口網站，並檢視您的行動服務的資料庫。如果您的行動服務使用 JavaScript 後端，則您可以從行動服務的 [資料] 索引標籤瀏覽資料。

    如果您將 .NET 後端用於行動服務，請在 Visual Studio 中移至 [伺服器總管] -> [Azure] -> [SQL 資料庫]。在資料庫上按一下滑鼠右鍵，並選取 [在 SQL Server 物件總管中開啟]。

    請注意，資料並未與資料庫和本機存放區進行同步處理。

    ![][6]

3. 在應用程式中，按 [推送] 按鈕。這會使應用程式呼叫 `MobileServiceClient.SyncContext.PushAsync`，然後呼叫 `RefreshTodoItems` 以使用本機存放區中的項目重新整理應用程式。此推送作業會使行動服務資料庫接收來自該存放區的資料。但本機存放區並不會接收行動服務資料庫中的項目。

    推送作業會從 `MobileServiceClient.SyncContext` 執行 (而非 `IMobileServicesSyncTable`)，並推送該同步內容所有相關資料表的變更。這是為了解說資料表之間有所關聯的情況。

    ![][7]

4. 在應用程式中，新增若干新項目至本機存放區。

    ![][8]

5. 這次請在應用程式中按 [提取] 按鈕。應用程式只會呼叫 `IMobileServiceSyncTable.PullAsync()` 和 `RefreshTodoItems`。請注意，行動服務資料庫中的所有資料都會提取至本機存放區，並顯示在應用程式中。但也請注意，本機存放區中的所有資料仍會推送至行動服務資料庫。這是因為**提取一律會先執行推送**。
 
    在此範例中，我們擷取遠端 `todoTable` 中的所有記錄，但也可以藉由傳遞查詢來篩選記錄。`PullAsync` 的第一個參數是用於增量同步處理的查詢識別碼，會使用 `UpdatedAt` 時間戳記取得自從上次同步後修改過的記錄。對您應用程式中的每個邏輯查詢而言，查詢識別碼應該是唯一的描述性字串。若選擇不要增量同步處理，請傳遞 `null` 做為查詢識別碼。這會擷取每個提取作業的所有記錄，而可能降低效能。

    >[AZURE.NOTE]若要使用離線資料同步支援刪除記錄的同步處理，您應啟用[虛刪除]。否則，您必須呼叫 `IMobileServiceSyncTable.PurgeAsync()`，以清除本機存放區。

 
    ![][9]

    ![][10]
  

##摘要

##摘要

[AZURE.INCLUDE [mobile-services-offline-summary-csharp](../../includes/mobile-services-offline-summary-csharp.md)]

## 後續步驟

* [處理行動服務的離線支援衝突]

* [使用行動服務中的虛刪除][Soft Delete]

<!-- Anchors. -->
[更新應用程式以支援離線功能]: #enable-offline-app
[在離線狀態下測試應用程式]: #test-offline-app
[更新應用程式以重新連接您的行動服務]: #update-online-app
[測試連接到行動服務的應用程式]: #test-online-app
[Next Steps]: #next-steps

<!-- Images -->
[0]: ./media/mobile-services-windows-phone-get-started-data-vs2013/mobile-todoitem-data-browse.png
[1]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-services-add-reference-sqlite-dialog.png
[2]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-services-sqlitestore-nuget.png
[3]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-services-sqlitepcl-nuget.png
[4]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-services-offline-app-run1.png
[5]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-services-online-app-run1.png
[6]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-data-browse.png
[7]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-data-browse2.png
[8]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-services-online-app-run2.png
[9]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-services-online-app-run3.png
[10]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-data-browse3.png
[11]: ./media/mobile-services-windows-phone-get-started-offline-data/vs-select-processor-architecture.png
[12]: ./media/mobile-services-windows-phone-get-started-offline-data/ui-screenshot.png

<!-- URLs. -->
[處理行動服務的離線支援衝突]: mobile-services-windows-phone-handling-conflicts-offline-data.md
[開始使用 Windows Phone 的離線範例]: http://go.microsoft.com/fwlink/?LinkId=397952
[開始使用行動服務]: ../mobile-services-windows-phone-get-started.md
[開始使用資料]: mobile-services-windows-phone-get-started-data.md
[SQLite for Windows Phone 8]: http://go.microsoft.com/fwlink/?LinkId=397953
[Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?linkid=268374
[Soft Delete]: mobile-services-using-soft-delete.md
[虛刪除]: mobile-services-using-soft-delete.md

[Mobile Services SDK Nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0
[SQLite store nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0
 

<!---HONumber=62-->