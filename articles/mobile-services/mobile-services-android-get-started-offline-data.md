<properties
	pageTitle="在 Android 行動服務應用程式中加入離線資料同步 | 行動開發人員中心"
	description="了解如何使用 Azure 行動服務快取和同步 Android 應用程式中的離線資料"
	documentationCenter="android"
	authors="RickSaling"
	manager="dwrede"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="06/30/2015"
	ms.author="ricksal"/>

# 在 Android 行動服務應用程式中加入離線資料同步

[AZURE.INCLUDE [mobile-services-selector-offline](../../includes/mobile-services-selector-offline.md)]

## 摘要

行動應用程式可能會在移至沒有服務的區域，或因為網路問題而失去網路連線。例如，用於遠端營造地點的營造產業應用程式可能需要輸入稍後會同步至 Azure 的排程資料。使用 Azure 行動服務離線同步，您可以在失去許多行動應用程式所需的網路連線時持續作業。透過離線同步，您可以使用 Azure SQL Server 資料表的本機副本，並定期重新同步處理兩個版本。

在本教學課程中，您將從[行動服務快速入門教學課程]更新應用程式，以啟用離線同步處理，然後離線加入資料、將這些項目同步處理到線上資料庫，並在 Azure 管理入口網站中驗證變更，藉此測試應用程式。

不論您是離線或連線狀態，對資料進行多項變更隨時可能會發生衝突。未來的教學課程將探索同步衝突處理，供您選擇要接受哪個版本的變更。在此教學課程中，我們假設沒有同步衝突，而您對現有資料進行的變更將直接套用到 Azure SQL Server。


## 若要開始，您需要：

[AZURE.INCLUDE [mobile-services-android-prerequisites](../../includes/mobile-services-android-prerequisites.md)]


## 更新應用程式以支援離線同步

利用離線同步讀取和寫入*同步資料表* (使用 *IMobileServiceSyncTable* 介面)，這是您裝置上 **SQL Light** 資料庫的一部分。

若要推送和提取裝置與 Azure 行動服務之間的變更，您可使用*同步處理內容* (*MobileServiceClient.SyncContext*)，這是您以用來在本機儲存資料的本機資料庫所初始化。

1. 在 *AndroidManifest.xml* 檔案中加入此程式碼，以新增檢查網路連線的權限：

	    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />


2. 將下列 **import** 陳述式加入至 *ToDoActivity.java*：

		import java.util.Map;
		
		import android.widget.Toast;
		
		import com.microsoft.windowsazure.mobileservices.table.query.Query; 
		import com.microsoft.windowsazure.mobileservices.table.sync.MobileServiceSyncContext; 
		import com.microsoft.windowsazure.mobileservices.table.sync.MobileServiceSyncTable; 
		import com.microsoft.windowsazure.mobileservices.table.sync.localstore.ColumnDataType; 
		import com.microsoft.windowsazure.mobileservices.table.sync.localstore.SQLiteLocalStore; 

3. 在 `ToDoActivity` 類別頂端附近，將 `mToDoTable` 變數的宣告從 `MobileServiceTable<ToDoItem>` 類別變更為 `MobileServiceSyncTable<ToDoItem>` 類別。

		 private MobileServiceSyncTable<ToDoItem> mToDoTable;

	這是您定義同步資料表的位置。

4. 若要處理本機存放區的初始化，請在建立 `MobileServiceClient` 執行個體之後，於 `onCreate` 方法中加入下列幾行：

			// Saves the query which will be used for pulling data
			mPullQuery = mClient.getTable(ToDoItem.class).where().field("complete").eq(false);

			SQLiteLocalStore localStore = new SQLiteLocalStore(mClient.getContext(), "ToDoItem", null, 1);
			SimpleSyncHandler handler = new SimpleSyncHandler();
			MobileServiceSyncContext syncContext = mClient.getSyncContext();

			Map<String, ColumnDataType> tableDefinition = new HashMap<String, ColumnDataType>();
			tableDefinition.put("id", ColumnDataType.String);
			tableDefinition.put("text", ColumnDataType.String);
			tableDefinition.put("complete", ColumnDataType.Boolean);
			tableDefinition.put("__version", ColumnDataType.String);

			localStore.defineTable("ToDoItem", tableDefinition);
			syncContext.initialize(localStore, handler).get();

			// Get the Mobile Service Table instance to use
			mToDoTable = mClient.getSyncTable(ToDoItem.class);

5. 在上述程式碼 (位於 `try` 區塊) 後面，於 `MalformedURLException` 區塊之後加入額外的 `catch` 區塊：

		} catch (Exception e) {
			Throwable t = e;
			while (t.getCause() != null) {
					t = t.getCause();
				}
			createAndShowDialog(new Exception("Unknown error: " + t.getMessage()), "Error");
		}

6. 加入這個方法，以驗證網路連線：

		private boolean isNetworkAvailable() {
			ConnectivityManager connectivityManager
					= (ConnectivityManager) getSystemService(Context.CONNECTIVITY_SERVICE);
			NetworkInfo activeNetworkInfo = connectivityManager.getActiveNetworkInfo();
			return activeNetworkInfo != null && activeNetworkInfo.isConnected();
		}


7. 加入這個方法，以同步處理本機 *SQL Light* 存放區與 Azure SQL Server：

		public void syncAsync(){
			if (isNetworkAvailable()) {
				new AsyncTask<Void, Void, Void>() {
	
					@Override
					protected Void doInBackground(Void... params) {
						try {
							mClient.getSyncContext().push().get();
							mToDoTable.pull(mPullQuery).get();

						} catch (Exception exception) {
							createAndShowDialog(exception, "Error");
						}
						return null;
					}
				}.execute();
			} else {
				Toast.makeText(this, "You are not online, re-sync later!" +
						"", Toast.LENGTH_LONG).show();
			}
		}


8. 在 `onCreate` 方法中，在 `refreshItemsFromTable` 呼叫的前面加入此程式碼成為倒數第二行：

			syncAsync();

	這會使裝置在啟動時與 Azure 資料表同步處理。否則，您會顯示本機存放區的上次離線內容。


 
9. 在 `refreshItemsFromTable` 方法中更新程式碼以使用此查詢 (`try` 區塊內的第一行程式碼)：

		final MobileServiceList<ToDoItem> result = mToDoTable.read(mPullQuery).get();

10. 在 `onOptionsItemSelected` 方法中，以此程式碼取代 `if` 區塊的內容：

			syncAsync();
			refreshItemsFromTable();

	此程式碼會在您按右上角的 [**重新整理**] 按鈕時執行。除了在啟動時同步處理以外，這是您將本機存放區同步至 Azure 的主要方式。這促使了同步變更的批次處理且具有效率 (有鑑於從 Azure 提取是相當耗成本的作業)。您也可以將 `syncAsync` 呼叫新增至 `addItem` 和 `checkItem` 方法，將此應用程式設計程在每次變更時同步。


## 測試應用程式

在本節中，您會在開啟 WiFi 的情況下測試行為，然後關閉 WiFi 以建立離線案例。

當您新增資料項目時，這些項目會存放在本機 SQL Light 存放區中，但直到您按 [**重新整理**] 按鈕時才會同步處理至行動服務。對於何時需要同步處理資料，其他應用程式可能會有不同的需求，但是為了示範目的，本教學課程讓使用者明確要求。

當您按下該按鈕時，新的背景工作會啟動，並使用同步處理內容先推送對本機存放區做的所有變更，然後將所有變更的資料從 Azure 提取至本機資料表。


### 線上測試

讓我們測試下列案例。

1. 在您的裝置上增加一些新項目； 
2. 確認項目不會顯示在入口網站中； 
3. 接下來按 [**重新整理**] 並確認這些項目接著顯示。
4. 在入口網站中變更或新增項目，然後按 [**重新整理**] 並確認變更顯示在您的裝置上。

### 離線測試

<!-- Now if you run the app and tap the refresh button, you should see all the items from the server. At that point you should be able to turn off the networking from the device by placing it in *Airplane Mode*, and continue making changes – the app will work just fine. When it’s time to sync the changes to the server, turn the network back on, and tap the **Refresh** button again.

One thing which is important to point out: if there are pending changes in the local store, a pull operation will first push those changes to the server (so that if there are changes in the same row, the push operation will fail and the application has an opportunity to handle the conflicts appropriately). That means that the push call in the code above isn’t necessarily required, but I think it’s always a good practice to be explicit about what the code is doing.
-->

1. 讓裝置或模擬器處於*飛航模式*。這會建立離線案例。

2. 新增 *ToDo* 項目，或將某些項目標示為完成。結束裝置或模擬器 (或強制關閉應用程式)，然後重新啟動。請確認您的變更已保存在裝置上，因為它們會保留在本機 SQL Light 存放區中。

3. 檢視 Azure *TodoItem* 資料表的內容。請確認新項目_尚未_ 同步處理到伺服器：

   - 若為 JavaScript 後端，移至管理入口網站，然後按一下 [資料] 索引標籤以檢視 `TodoItem` 資料表的內容。
   - 若為 .NET 後端，使用 SQL 工具 (如 *SQL Server Management Studio*) 或 REST 用戶端 (如 *Fiddler* 或 *Postman*) 檢視資料表內容。

4. 在裝置或模擬器中開啟 WiFi。接著，按 [**重新整理**] 按鈕。

5. 在 Azure 入口網站中，再次檢視 TodoItem 資料。新的和變更的 TodoItems 現在應該會出現。


## 後續步驟

* [使用行動服務中的虛刪除][Soft Delete]

## 其他資源

* [雲端報導：Azure 行動服務中的離線同步處理]

* [Azure Friday：Azure 行動服務中的離線應用程式] (附註︰示範適用於 Windows，但功能討論適用於所有平台)


<!-- URLs. -->

[Get the sample app]: #get-app
[Review the Core Data model]: #review-core-data
[Review the Mobile Services sync code]: #review-sync
[Change the sync behavior of the app]: #setup-sync
[Test the app]: #test-app


[Mobile Services sample repository on GitHub]: https://github.com/Azure/mobile-services-samples


[Get started with Mobile Services]: mobile-services-android-get-started.md
[Get started with data]: mobile-services-android-get-started-data.md
[Handling Conflicts with Offline Support for Mobile Services]: mobile-services-android-handling-conflicts-offline-data.md
[Soft Delete]: mobile-services-using-soft-delete.md

[雲端報導：Azure 行動服務中的離線同步處理]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday：Azure 行動服務中的離線應用程式]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

[行動服務快速入門教學課程]: mobile-services-android-get-started.md

<!---HONumber=July15_HO2-->