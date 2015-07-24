<properties 
	pageTitle="處理行動服務中的離線資料衝突 (Windows Phone) | 行動開發人員中心" 
	description="了解您在 Windows Phone 應用程式中同步離線資料時應如何使用 Azure 行動服務處理衝突" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/15/2015" 
	ms.author="wesmc"/>


# 處理行動服務中的離線資料同步衝突

[AZURE.INCLUDE [mobile-services-selector-offline-conflicts](../../includes/mobile-services-selector-offline-conflicts.md)]

##概觀

本主題將說明在使用 Azure 行動服務的離線功能時，應如何同步處理資料及處理衝突。在本教學課程中，您將下載同時支援離線和線上資料的應用程式、將行動服務與該應用程式整合，然後登入 Azure 管理入口網站，以檢視執行應用程式時所做的資料庫更新。

本教學課程會以先前的教學課程[開始使用資料]中的步驟和範例應用程式為基礎。在開始本教學課程之前，您必須先完成[開始使用離線資料]。


##必要條件

本教學課程需要 Visual Studio 2012 和 [Windows Phone 8 SDK]。


##下載範例專案



本教學課程以[處理衝突程式碼範例] (這是 Visual Studio 2012 中的 Windows Phone 8 專案之一) 做為建置基礎。此應用程式的 UI 與[開始使用離線資料]教學課程中的應用程式相類似，差別在於前者的每個 TodoItem 都有新的日期資料行。

![][0]


1. 下載 Windows Phone 版本的[處理衝突程式碼範例]。 

2. 安裝 [SQLite for Windows Phone 8] (如果尚未安裝)。

3. 在 Visual Studio 2012 中，開啟下載的專案。在 [Windows Phone] > [延伸] 下，新增 [SQLite for Windows Phone] 的參考。

4. 在 Visual Studio 2012 中按 **F5** 鍵，以在偵錯程式中建置並執行應用程式。
 
5. 在應用程式中，為某些新的 todo 項目輸入文字，然後按一下 [儲存] 加以儲存。您也可以修改已新增之 todo 項目的到期日。


請注意，應用程式尚未連接到任何行動服務，因此 [推送] 和 [提取] 按鈕將會擲出例外狀況。


##將資料行新增至資料模型

在本節中，您將會更新行動服務的資料庫，以納入具有到期日資料行的 TodoItem 資料表。應用程式允許您在執行階段變更項目的到期日，因此您可以在本教學課程的後續小節中產生同步衝突。

範例中的 `TodoItem` 類別定義於 MainPage.xaml.cs 中。請注意，此類別具有下列會將該資料表做為同步作業目標的屬性。

        [DataTable("TodoWithDate")]

更新您的資料庫以納入此資料表。

###<a name="dotnet-backend"></a>更新 .NET 後端行動服務的資料庫 

如果您在行動服務中使用 .NET 後端，請依照下列步驟更新資料庫的結構描述。

1. 在 Visual Studio 中，開啟您的 .NET 後端行動服務專案。
2. 在 Visual Studio 的 [方案總管] 中，在您的服務專案中展開 **Models** 資料夾，並開啟 ToDoItem.cs。新增 `DueDate` 屬性，如下所示。

          public class TodoItem : EntityData
          {
            public string Text { get; set; }
            public bool Complete { get; set; }
            public System.DateTime DueDate { get; set; }
          }


3. 在 Visual Studio 的 [方案總管] 中展開 **App_Start** 資料夾，然後開啟 WebApiConfig.cs 檔案。

    在 WebApiConfig.cs 檔案中，注意您的預設資料庫初始設定式類別是衍生自 `DropCreateDatabaseIfModelChanges` 類別。這意味對模型的任何變更都會導致資料表捨棄，並重新建立以容納新模型。因此資料表的資料將流失，並且將重新植入資料表。請修改資料庫初始設定式的 Seed 方法，使 `Seed()` 初始化函數如下所示，以初始化新的 DueDate 資料行。儲存 WebApiConfig.cs 檔案。

    >[AZURE.NOTE]使用預設資料庫初始設定式時，每當 Entity Framework 在 Code First 模型定義中偵測到資料模型變更，就會捨棄並重新建立資料庫。若要進行此資料模型變更，並保有資料庫的現有資料，必須使用 Code First Migrations。如需詳細資訊，請參閱[如何使用 Code First Migrations 更新資料模型](mobile-services-dotnet-backend-how-to-use-code-first-migrations.md) (英文)。


        new TodoItem { Id = "1", Text = "First item", Complete = false, DueDate = DateTime.Today },
        new TodoItem { Id = "2", Text = "Second item", Complete = false, DueDate = DateTime.Today },

          

4. 在 Visual Studio 的 [方案總管] 中展開 **Controllers** 資料夾，然後開啟 ToDoItemController.cs。將 `TodoItemController` 類別重新命名為 `TodoWithDateController`。這將會變更資料表作業的 REST 端點。

        public class TodoWithDateController : TableController<TodoItem>
    

5. 在 Visual Studio 的 [方案總管] 中，以滑鼠右鍵按一下您的 .NET 後端行動服務專案，然後按一下 [發佈] 以發佈您的變更。


### <a name="javascript-backend"></a>更新 JavaScript 後端行動服務的資料庫

對於 JavaScript 後端行動服務，您將會新增名為 **TodoWithDate** 的新資料表。若要為 JavaScript 後端行動服務新增 **TodoWithDate** 資料表，請遵循下列步驟。

  1. 登入 [Azure 管理入口網站]。 

  2. 導覽至行動服務的 [資料] 索引標籤。

  3. 按一下頁面底部的 [建立]，然後建立名為 **TodoWithDate** 的新資料表。


##對行動服務進行應用程式測試

現在，我們將對行動服務進行應用程式測試。

1. 在 Azure 管理入口網站中，按一下 [儀表板] 索引標籤之命令列上的 [管理金鑰]，以找出您行動服務的應用程式金鑰。複製 [應用程式金鑰]。

2. 在 Visual Studio 的 [方案總管] 中，開啟 JavaScript 用戶端範例專案中的 App.xaml.cs 檔案。變更 **MobileServiceClient** 的初始化，使其使用您的行動服務 URL 和應用程式金鑰：

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.net/",
            "Your AppKey"
        );

3. 在 Visual Studio 中按 **F5** 鍵，以建置並執行此應用程式。

4. 和之前一樣，在文字方塊中輸入文字，然後按一下 [儲存] 以儲存一些新的 todo 項目。這會將資料儲存至本機同步資料表，但不會儲存至伺服器。

    ![][0]

5. 若要檢視資料庫的現行狀態，請登入 [Azure 管理入口網站]，按一下 [行動服務]，然後按一下您的行動服務。

  * 如果您在行動服務中使用 JavaScript 後端，請按一下 [資料] 索引標籤，然後按一下 [TodoWithDate] 資料表。按一下 [瀏覽] 以確認資料表仍是空白的，因為我們尚未將變更從應用程式推送至伺服器。

        ![][1]

  *  如果您在行動服務中使用 .NET 後端，請按一下 [設定] 索引標籤，然後按一下您的 SQL Database。按一下畫面底部的 [管理]，然後登入 SQL Azure 管理入口網站，執行如下的 SQL 查詢以檢視您的資料庫。
    
            SELECT * FROM todolist.todowithdate

        ![][2]

   	 

7. 返回應用程式，並按一下 [推送]。

8. 在管理入口網站中，按一下 [TodoItem] 資料表上的 [重新整理]。此時您應會看見您在應用程式中輸入的資料。

   	![][3]

9. 在下一節中將 [Emulator WVGA 512MB] 保持為啟動並執行中的狀態，屆時您將會在兩個模擬器中執行應用程式，以產生衝突。

##更新後端中的資料以產生衝突

在實際情況中，如果在一個應用程式推送資料庫中某個記錄的更新後，又有另一個應用程式使用該記錄的過期版本欄位嘗試推送相同記錄的變更，就會發生同步衝突。如果一個應用程式執行個體在未提取更新記錄的情況下嘗試更新相同記錄，就會發生衝突，並且在應用程式中產生 `MobileServicePreconditionFailedException`。

在本節中，您將同時執行兩個應用程式執行個體，以產生衝突。


1. 如果 [Emulator WVGA 512MB] 尚未啟動並執行，請按 **Ctrl+F5** 加以重新啟動。

2. 在 Visual Studio 中，將輸出裝置變更為 [Emulator WVGA]，然後按 **F5** 在新的模擬器中執行第二個應用程式執行個體。
 
    ![][5]
 
   
3. 在第二個應用程式執行個體中按一下 [提取]，使本機存放區與行動服務資料庫同步。兩個應用程式執行個體應含有相同的資料。
 
    ![][6]

4. 在第二個應用程式執行個體中按一下核取方塊以完成其中一個項目，然後按一下 [推送] 將變更推送至遠端資料庫。在下列螢幕擷取畫面中，[Pick up James] 已完成，表示 James 已被挑選。第一個應用程式執行個體現在含有過期的記錄。

    ![][9]

5. 在第一個應用程式執行個體中，嘗試變更過期記錄的日期，然後按一下 [推送]，嘗試以過期的記錄更新遠端資料庫。在下方的螢幕擷取畫面中，我們嘗試排定在 **5/10/2014** 挑選 James。

    ![][7]

6. 當您按一下 [推送] 按鈕以認可日期變更時，您會看見對話方塊指出已偵測到衝突。對話方塊會詢問您要如何解決衝突。請選擇其中一個選項，以解決衝突。

    在下方顯示的案例中，James 已被挑選。因此已無需排定在 **5/10/2014** 挑選他。此時會選取 [使用伺服器版本] 選項，因此第一個應用程式執行個體將會以來自伺服器的記錄更新該記錄。

    ![][8]

##檢閱處理同步衝突的程式碼

若要設定用以偵測衝突的離線功能，您必須同時在本機資料庫與資料傳輸物件中加入版本資料行。`TodoItem` 類別具有下列成員：

        [Version]
        public string Version { get; set; }

資料行 `__version` 也會在設定於 `OnNavigatedTo()` 方法中的本機資料庫中指定。

若要使用程式碼處理離線同步衝突，您必須建立類別以實作 `IMobileServiceSyncHandler`。請將此類型的物件傳至 `InitializeAsync` 的呼叫中：

     await App.MobileService.SyncContext.InitializeAsync(store, new SyncHandler(App.MobileService));

**MainPage.xaml.cs** 中的 `SyncHandler` 類別會實作 `IMobileServiceSyncHandler`。每個推送作業傳送至伺服器時，都會呼叫 `ExecuteTableOperationAsync` 方法。如果擲出了 `MobileServicePreconditionFailedException` 類型的例外狀況，表示項目的本機與遠端版本之間具有衝突。

若要以有利於本機項目的方式解決衝突，您應直接重試作業。衝突發生後，本機項目版本會進行更新以符合伺服器版本，因此重新執行作業時，將會以本機變更覆寫伺服器變更：

    await operation.ExecuteAsync(); 

若要以有利於伺服器項目的方式解決衝突，請直接從 `ExecuteTableOperationAsync` 回復。物件的本機版本將會被捨棄，並取代為伺服器中的值。

若要停止推送作業 (但保留佇列上的變更)，請使用方法 `AbortPush()`：

    operation.AbortPush();

如果從 `ExecuteTableOperationAsync` 呼叫了 `AbortPush`，將會停止目前的推送作業，但仍將持續執行所有擱置變更，包括目前的作業。下次呼叫 `PushAsync()` 時，這些變更將會傳送至伺服器。

取消推送時，`PushAsync` 會擲出 `MobileServicePushFailedException`，且例外狀況屬性 `PushResult.Status` 的值將是 `MobileServicePushStatus.CancelledByOperation`。




<!-- Images -->
[0]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run1.png
[1]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/mobile-services-todowithdate-empty.png
[2]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/mobile-services-todowithdate-empty-sql.png
[3]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/mobile-services-todowithdate-push1.png
[5]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/vs-emulator-wvga.png
[6]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/two-emulators-synced.png
[7]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/two-emulators-date-change.png
[8]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/two-emulators-conflict-detected.png
[9]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/two-emulators-item-completed.png



<!-- URLs -->
[處理衝突程式碼範例]: http://go.microsoft.com/fwlink/?LinkId=398257
[Get started with Mobile Services]: ../mobile-services-windows-phone-get-started.md
[開始使用資料]: mobile-services-windows-phone-get-started-offline-data.md
[開始使用離線資料]: mobile-services-windows-phone-get-started-offline-data.md
[Azure 管理入口網站]: https://manage.windowsazure.com/
[Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?linkid=268374
[SQLite for Windows Phone 8]: http://go.microsoft.com/fwlink/?LinkId=397953
[Get started with data]: mobile-services-windows-phone-get-started-data.md
 

<!---HONumber=July15_HO1-->