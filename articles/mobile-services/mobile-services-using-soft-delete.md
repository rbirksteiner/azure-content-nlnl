<properties 
	pageTitle="使用行動服務 (Windows 市集) 中的虛刪除 | 行動開發人員中心" 
	description="了解如何在您的應用程式中使用 Azure 行動服務的虛刪除功能" 
	documentationCenter="" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/18/2015" 
	ms.author="wesmc"/>

# 使用行動服務中的虛刪除

##概觀

以 JavaScript 或 .NET 後端建立的資料表，可以選擇性地啟用虛刪除功能。使用虛刪除時，將會在資料庫中新增屬於 [SQL 位元類型] 的新資料行 *__deleted*。啟用虛刪除時，刪除作業將不會從資料庫中實際刪除資料列，而會將已刪除資料行的值設為 TRUE。

在對已啟用虛刪除的資料表查詢記錄時，依預設將不會在查詢中傳回刪除的資料列。若要要求這些資料列，您必須在 [REST 查詢作業](http://msdn.microsoft.com/library/azure/jj677199.aspx)中傳入查詢參數 *__includeDeleted=true*。在 .NET 用戶端 SDK 中，您也可以使用協助程式方法 `IMobileServiceTable.IncludeDeleted()`。

.NET 後端的虛刪除支援最初是隨 Microsoft Azure 行動服務 .NET 後端的 1.0.402 版而發行的。最新的 NuGet 套件可從 [Microsoft Azure 行動服務 .NET 後端](http://go.microsoft.com/fwlink/?LinkId=513165)取得。


使用虛刪除可能有下列好處：

* 在使用[行動服務的離線資料同步]功能時，用戶端 SDK 會自動查詢已刪除的記錄，並將其從本機資料庫中移除。在未啟用虛刪除的情況下，您必須在後端另行撰寫程式碼，讓用戶端 SDK 得知要從本機存放區中移除哪些記錄。否則，用戶端本機存放區和後端的這些已刪除的記錄將會不一致，而必須呼叫用戶端方法 `PurgeAsync()` 來清理本機存放區。
* 有些應用程式會有一律不實體刪除資料、或僅在稽核後刪除資料的商業需求。在此情況下，虛刪除功能就可派上用場。
* 虛刪除可用來實作「取消刪除」功能，讓意外刪除的資料可以復原。不過，虛刪除的記錄會佔用資料庫中的空間，因此您應考慮建立排程的作業來定期實刪除已虛刪除的記錄。如需相關範例，請參閱 [對 .NET 後端使用虛刪除] 和 [對 JavaScript 後端使用虛刪除]。您的用戶端程式碼也應定期呼叫 `PurgeAsync()`，使這些實刪除的記錄不會保留在裝置的本機資料存放區中。





##為 .NET 後端啟用虛刪除

.NET 後端的虛刪除支援最初是隨 Microsoft Azure 行動服務 .NET 後端的 1.0.402 版而發行的。最新的 NuGet 套件可從 [Microsoft Azure 行動服務 .NET 後端](http://go.microsoft.com/fwlink/?LinkId=513165)取得。

下列步驟將引導您為 .NET 後端行動服務啟用虛刪除。

1. 在 Visual Studio 中，開啟您的 .NET 後端行動服務專案。
2. 以滑鼠右鍵按一下.NET 後端專案，然後按一下 [管理 NuGet 套件]。 
3. 在套件管理員對話方塊中，按一下更新下方的 **Nuget.org**，然後安裝 [Microsoft Azure 行動服務 .NET 後端](http://go.microsoft.com/fwlink/?LinkId=513165) NuGet 套件的 1.0.402 版或更新版本。
3. 在 Visual Studio 的 [方案總管] 中，展開您 .NET 後端專案下方的 [控制器] 節點，然後開啟您的控制器來源。例如 *TodoItemController.cs*。
4. 在控制器的 `Initialize()` 方法中，將 `enableSoftDelete: true` 參數傳至 EntityDomainManager 建構函式。

        protected override void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);
            MobileService1Context context = new MobileService1Context();
            DomainManager = new EntityDomainManager<TodoItem>(context, Request, Services, enableSoftDelete: true);
        }


##為 JavaScript 後端啟用虛刪除

如果您要為行動服務建立新的資料表，您可以在資料表建立頁面上啟用虛刪除。

![][2]

若要在 JavaScript 後端中的現有資料表上啟用虛刪除：

1. 在[管理入口網站]中，按一下您的行動服務。然後按一下 [資料] 索引標籤。
2. 在資料頁面上，點選所需的資料表。然後，在命令列中按一下 [啟用虛刪除] 按鈕。如果資料表已啟用虛刪除，此按鈕將不會出現，但您可以按一下資料表的 [瀏覽] 或 [資料行] 索引標籤，以檢視 *__deleted* 資料行。

    ![][0]

    若要為資料表停用虛刪除，請按一下 [資料行] 索引標籤，然後按一下 *__deleted* 資料行和 [刪除] 按鈕。

    ![][1]

## <a name="using-with-dotnet"></a>對 .NET 後端使用虛刪除


下列排程工作會清除存留期超過一個月的虛刪除記錄：

    public class SampleJob : ScheduledJob
    {
        private MobileService1Context context;
     
        protected override void Initialize(ScheduledJobDescriptor scheduledJobDescriptor, 
            CancellationToken cancellationToken)
        {
            base.Initialize(scheduledJobDescriptor, cancellationToken);
            context = new MobileService1Context();
        }
     
        public override Task ExecuteAsync()
        {
            Services.Log.Info("Purging old records");
            var monthAgo = DateTimeOffset.UtcNow.AddDays(-30);
     
            var toDelete = context.TodoItems.Where(x => x.Deleted == true && x.UpdatedAt <= monthAgo).ToArray();
            context.TodoItems.RemoveRange(toDelete);
            context.SaveChanges();
     
            return Task.FromResult(true);
        }
    }

若要深入了解 .NET 後端行動服務的排程工作，請參閱：[在 JavaScript 後端行動服務中排程週期性工作](mobile-services-dotnet-backend-schedule-recurring-tasks.md)




##對 JavaScript 後端使用虛刪除

您可以使用資料表指令碼，為 JavaScript 後端行動服務的虛刪除功能新增邏輯。

若要偵測取消刪除要求，請在您的更新資料表指令碼中使用 "undelete" 屬性：
    
    function update(item, user, request) {
        if (request.undelete) { /* any undelete specific code */; }
    }
若要在指令碼的查詢結果中納入已刪除的記錄，請將 "includeDeleted" 參數設為 true：
    
    tables.getTable('softdelete_scenarios').read({
        includeDeleted: true,
        success: function (results) {
            request.respond(200, results)
        }
    });

若要透過 HTTP 要求擷取已刪除的記錄，請新增查詢參數 "__includedeleted=true"：

    http://youservice.azure-mobile.net/tables/todoitem?__includedeleted=true

### 對虛刪除的記錄進行清除的範例排程工作。

此範例排程工作會刪除在特定日期之前更新的記錄：

    function purgedeleted() {
         mssql.query('DELETE FROM softdelete WHERE __deleted=1', {
            success: function(results) {
                console.log(results);
            },
            error: function(err) {
                console.log("error is: " + err);
        }});
    }

若要深入了解 JavaScript 後端行動服務的排程工作，請參閱：[在 JavaScript 後端行動服務中排程週期性工作](mobile-services-schedule-recurring-tasks.md)。





<!-- Images -->
[0]: ./media/mobile-services-using-soft-delete/enable-soft-delete-button.png
[1]: ./media/mobile-services-using-soft-delete/disable-soft-delete.png
[2]: ./media/mobile-services-using-soft-delete/enable-soft-delete-new-table.png

<!-- URLs. -->
[SQL 位元類型]: http://msdn.microsoft.com/library/ms177603.aspx
[行動服務的離線資料同步]: mobile-services-windows-store-dotnet-get-started-offline-data.md
[管理入口網站]: https://manage.windowsazure.com/


 

<!---HONumber=July15_HO1-->