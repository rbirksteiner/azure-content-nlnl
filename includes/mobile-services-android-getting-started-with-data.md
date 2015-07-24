您的行動服務已準備就緒，現在可以更新應用程式以便在行動服務 (而非本機收集) 中儲存項目。

1. 請確定 *build.gradle (模組應用程式)* 檔案中的 [相依性] 標記是否包含下列程式碼行 (如果沒有，請將它們加入)。這會將參考新增至行動服務 Android 用戶端 SDK。

		compile 'com.android.support:support-v4:21.0.3'
    	compile 'com.google.code.gson:gson:2.2.2'
	    compile 'com.google.guava:guava:18.0'
	    compile 'com.microsoft.azure:azure-mobile-services-android-sdk:2.0.2+'


2. 現在按一下 [同步處理專案與 Gradle 檔案] 以重新建置專案。

3. 開啟 AndroidManifest.xml 檔案並加入這一行，以便讓應用程式存取 Azure 中的行動服務。

		<uses-permission android:name="android.permission.INTERNET" />


4. 在 [專案總管] 中，開啟位於 **[GetStartedWithData] => [app] => [src] => [java]** 資料夾中的 TodoActivity.java 檔案，並取消註解下列程式碼行：



		import java.net.MalformedURLException;
		import android.os.AsyncTask;
		import com.google.common.util.concurrent.FutureCallback;
		import com.google.common.util.concurrent.Futures;
		import com.google.common.util.concurrent.ListenableFuture;
		import com.microsoft.windowsazure.mobileservices.MobileServiceClient;
		import com.microsoft.windowsazure.mobileservices.MobileServiceList;
		import com.microsoft.windowsazure.mobileservices.http.NextServiceFilterCallback;
		import com.microsoft.windowsazure.mobileservices.http.ServiceFilter;
		import com.microsoft.windowsazure.mobileservices.http.ServiceFilterRequest;
		import com.microsoft.windowsazure.mobileservices.http.ServiceFilterResponse;
		import com.microsoft.windowsazure.mobileservices.table.MobileServiceTable;

 
5. 註解化下列幾行：

		import java.util.ArrayList;
		import java.util.List;

6. 我們將會移除應用程式目前使用的記憶體中清單，所以可以將它換成行動服務。在 **ToDoActivity** 類別中，下列這一行程式碼定義現有的 **toDoItemList** 清單，請將它變成註解。

		public List<ToDoItem> toDoItemList = new ArrayList<ToDoItem>();

7. 儲存檔案，專案會指出建置錯誤。請搜尋其餘三個用到 `toDoItemList` 變數的位置，並將指出的區段變成註解。這會完全移除記憶體中的清單。

8. 現在開始加入我們的行動服務。將下列幾行程式碼取消註解：

		private MobileServiceClient mClient;
		private private MobileServiceTable<ToDoItem> mToDoTable;

9. 在檔案底部找出 *ProgressFilter* 類別並取消註解。此類別會在 *MobileServiceClient* 執行網路作業時顯示 'loading' 指示。


10. 在管理入口網站中，按一下 [行動服務]，然後按一下您剛剛建立的行動服務。

11. 按一下 [儀表板] 索引標籤並記下 [網站 URL]，然後按一下 [管理金鑰] 並記下 [應用程式金鑰]。

   	![](./media/download-android-sample-code/mobile-dashboard-tab.png)

  	從應用程式程式碼存取行動服務時，您將會用到這些值。

12. 在 **onCreate** 方法中，下列幾行程式碼定義 **MobileServiceClient** 變數，請將它們取消註解：

		try {
		// Create the Mobile Service Client instance, using the provided
		// Mobile Service URL and key
			mClient = new MobileServiceClient(
					"MobileServiceUrl",
					"AppKey", 
					this).withFilter(new ProgressFilter());

			// Get the Mobile Service Table instance to use
			mToDoTable = mClient.getTable(ToDoItem.class);
		} catch (MalformedURLException e) {
			createAndShowDialog(new Exception("There was an error creating the Mobile Service. Verify the URL"), "Error");
		}

  	這會建立用來存取行動服務的新 *MobileServiceClient* 執行個體。也會建立 *MobileServiceTable* 執行個體，用於代理行動服務中的資料儲存。

13. 在上述程式碼中，依序將 `MobileServiceUrl` 和 `AppKey` 換成您的行動服務中的 URL 和應用程式金鑰。



14. 將 **checkItem** 方法的這幾行取消註解：

	    new AsyncTask<Void, Void, Void>() {
	        @Override
	        protected Void doInBackground(Void... params) {
	            try {
	                mToDoTable.update(item).get();
	                runOnUiThread(new Runnable() {
	                    public void run() {
	                        if (item.isComplete()) {
	                            mAdapter.remove(item);
	                        }
	                        refreshItemsFromTable();
	                    }
	                });
	            } catch (Exception exception) {
	                createAndShowDialog(exception, "Error");
	            }
	            return null;
	        }
	    }.execute();

   	這會將項目更新傳送至行動服務，並從配接器中移除已核取的項目。
    
15. 將 **addItem** 方法的這幾行取消註解：
	
		// Insert the new item
		new AsyncTask<Void, Void, Void>() {
	        @Override
	        protected Void doInBackground(Void... params) {
	            try {
	                mToDoTable.insert(item).get();
	                if (!item.isComplete()) {
	                    runOnUiThread(new Runnable() {
	                        public void run() {
	                            mAdapter.add(item);
	                        }
	                    });
	                }
	            } catch (Exception exception) {
	                createAndShowDialog(exception, "Error");
	            }
	            return null;
	        }
	    }.execute();
		

  	此程式碼會建立新的項目，並將此項目插入至遠端行動服務中的資料表。

16. 將 **refreshItemsFromTable** 方法的這幾行取消註解：

		// Get the items that weren't marked as completed and add them in the adapter
	    new AsyncTask<Void, Void, Void>() {
	        @Override
	        protected Void doInBackground(Void... params) {
	            try {
	                final MobileServiceList<ToDoItem> result = mToDoTable.where().field("complete").eq(false).execute().get();
	                runOnUiThread(new Runnable() {
	                    @Override
	                    public void run() {
	                        mAdapter.clear();

	                        for (ToDoItem item : result) {
	                            mAdapter.add(item);
	                        }
	                    }
	                });
	            } catch (Exception exception) {
	                createAndShowDialog(exception, "Error");
	            }
	            return null;
	        }
	    }.execute();

	這會查詢行動服務，並傳回所有未標示為完成的項目。項目會加入至配接器來繫結。
		

<!-- URLs. -->
[Mobile Services Android SDK]: http://aka.ms/Iajk6q

<!---HONumber=July15_HO2-->