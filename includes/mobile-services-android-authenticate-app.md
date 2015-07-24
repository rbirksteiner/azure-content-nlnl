
1. 在 Android Studio 的 [專案總管] 中，開啟 ToDoActivity.java 檔案，並新增下列 import 陳述式。

		import java.util.concurrent.ExecutionException;
		import java.util.concurrent.atomic.AtomicBoolean;

		import android.content.Context;
		import android.content.SharedPreferences;
		import android.content.SharedPreferences.Editor;

		import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
		import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;

2. 將下列方法加入至 **ToDoActivity** 類別：
	
		private void authenticate() {
		    // Login using the Google provider.
		    
			ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);
	
	    	Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
	    		@Override
	    		public void onFailure(Throwable exc) {
	    			createAndShowDialog((Exception) exc, "Error");
	    		}   		
	    		@Override
	    		public void onSuccess(MobileServiceUser user) {
	    			createAndShowDialog(String.format(
	                        "You are now logged in - %1$2s",
	                        user.getUserId()), "Success");
	    			createTable();	
	    		}
	    	});   	
		}


	這會建立新的方法來處理驗證程序。使用者透過 Google 登入來驗證。將出現對話方塊來顯示已驗證的使用者的識別碼。必須通過驗證才能繼續。

    > [AZURE.NOTE]如果您使用的身分識別提供者不是 Google，請將傳給上述 **login** 方法的值變更為下列其中一個：_MicrosoftAccount_、_Facebook_、_Twitter_ 或 _windowsazureactivedirectory_。

3. 在 **onCreate** 方法中，在具現化 `MobileServiceClient` 物件的程式碼後面新增下列這一行程式碼。

		authenticate();

	此呼叫會啟動驗證程序。

4. 將 **onCreate** 方法中 `authenticate();` 後面的其餘程式碼移至新的 **createTable** 方法，如下所示：

		private void createTable() {
	
			// Get the Mobile Service Table instance to use
			mToDoTable = mClient.getTable(ToDoItem.class);
	
			mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);
	
			// Create an adapter to bind the items with the view
			mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
			ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
			listViewToDo.setAdapter(mAdapter);
	
			// Load the items from the Mobile Service
			refreshItemsFromTable();
		}

9. 在 [執行] 功能表中，按一下 [執行應用程式] 來啟動應用程式，並以您選擇的身分識別提供者登入。

   	成功登入後，應用程式應會正確無誤地執行，而且您應能夠查詢行動服務並更新資料。

<!---HONumber=July15_HO2-->