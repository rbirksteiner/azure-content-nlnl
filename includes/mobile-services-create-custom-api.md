

1. 登入 Azure 管理入口網站，按一下 [行動服務]，然後選取您的行動服務。

2. 按一下 [API] 索引標籤，然後按一下 [建立]。這樣做會顯示 [Create a new custom API] 對話方塊。

3. 在 [API 名稱] 中輸入 completeall，然後按一下 [檢查] 按鈕以建立新的 API。

	> [AZURE.NOTE]有了預設權限，具備應用程式金鑰的任何人都可以呼叫自訂 API。不過，應用程式金鑰並不會被視為安全的認證，因為它不是以安全的方式散佈或儲存。請考慮限制只有經過驗證的使用者才能存取，以提供進階的安全性。

4. 按一下 API 資料表中的 [completeall]。

5. 按一下 [指令碼] 索引標籤，以下列程式碼取代現有的程式碼，然後按一下 [儲存]。此程式碼使用 [mssql 物件]直接存取 todoitem 資料表，以在所有項目上設定 `complete` 旗標。因為使用了 **exports.post** 函數，所以用戶端會傳送 POST 要求以執行此操作。系統會以整數值將變更的列數傳回給用戶端。


		exports.post = function(request, response) {
			var mssql = request.service.mssql;
			var sql = "UPDATE todoitem SET complete = 1 " +
                "WHERE complete = 0; SELECT @@ROWCOUNT as count";
			mssql.query(sql, {
				success: function(results) {
					if(results.length == 1)
						response.send(200, results[0]);
				}
			})
		};



> [AZURE.NOTE]<a href="http://go.microsoft.com/fwlink/p/?LinkId=309046" target="_blank">Express.js 程式庫</a>會實作提供給自訂 API 函數的 <a href="http://msdn.microsoft.com/library/windowsazure/jj554218.aspx" target="_blank">request</a> 和 <a href="http://msdn.microsoft.com/library/windowsazure/dn303373.aspx" target="_blank">response</a> 物件。如需詳細資訊，請參閱<a href="http://msdn.microsoft.com/library/windowsazure/dn280974.aspx" target="_blank">自訂 API</a>。

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[mssql 物件]: http://msdn.microsoft.com/library/windowsazure/jj554212.aspx

<!---HONumber=July15_HO2-->