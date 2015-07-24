

1. 在 Visual Studio 中，以滑鼠右鍵按一下 [Controllers] 資料夾，展開 [新增]，然後按一下 [新增 Scaffolded 項目]。這會顯示 [新增 Scaffold] 對話方塊。

2. 展開 [Azure 行動服務]，按一下 [Azure 行動服務自訂控制器]，再按一下 [新增]，並提供 `CompleteAllController` 的 [控制器名稱]，最後再按一下 [新增]。

	![Web API Add Scaffold dialog](./media/mobile-services-dotnet-backend-create-custom-api/add-custom-api-controller.png)

	這會建立名為 **CompleteAllController** 的新空白控制器類別。

	>[AZURE.NOTE]如果對話方塊沒有行動服務的特定 Scaffold，請改為建立新的 Web API 控制器 - 空白。在此新控制器類別中加入公用 **Services** 屬性，該屬性可傳回 **ApiServices** 類型。此屬性可用來存取控制器內部的伺服器專屬設定。

3. 在 **CompleteAllController.cs** 中，新增下列 **using** 陳述式。以行動服務專案的命名空間取代 `todolistService`，該名稱必須是附加 `Service` 的行動服務名稱。

		using System.Threading.Tasks;
		using todolistService.Models;

4. 在 **CompleteAllController.cs** 中，新增下列類別來包裝要傳送至用戶端的回應。

        // We use this class to keep parity with other Mobile Services
        // that use the JavaScript backend. This way the same client
        // code can call either type of Mobile Service backend.
        public class MarkAllResult
        {
            public int count;
        }

5. 將下列程式碼新增至新控制器：。以資料模型的 DbContext 名稱取代 `todolistContext`，該名稱必須是附加 `Context` 的行動服務名稱。同樣地，將 UPDATE 陳述式中的結構描述名稱取代為您的行動服務名稱。此程式碼使用 [Database 類別](http://msdn.microsoft.com/library/system.data.entity.database.aspx) (英文) 直接存取 **TodoItems** 資料表，以在所有項目上設定完成旗標。此方法支援 POST 要求，且系統會以整數值將變更的列數傳回給用戶端。


	    // POST api/completeall
        public async Task<MarkAllResult> Post()
        {
            using (todolistContext context = new todolistContext())
            {
                // Get the database from the context.
                var database = context.Database;

                // Create a SQL statement that sets all uncompleted items
                // to complete and execute the statement asynchronously.
                var sql = @"UPDATE todolist.TodoItems SET Complete = 1 " +
                            @"WHERE Complete = 0; SELECT @@ROWCOUNT as count";

                var result = new MarkAllResult();
                result.count = await database.ExecuteSqlCommandAsync(sql);

                // Log the result.
                Services.Log.Info(string.Format("{0} items set to 'complete'.",
                    result.count.ToString()));

                return result;
            }
        }

	> [AZURE.NOTE]有了預設權限，具備應用程式金鑰的任何人都可以呼叫自訂 API。不過，應用程式金鑰並不會被視為安全的認證，因為它不是以安全的方式散佈或儲存。請考慮限制只有經過驗證的使用者才能存取，以提供進階的安全性。

<!---HONumber=July15_HO2-->