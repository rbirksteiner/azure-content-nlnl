
先前範例所示範的標準登入，在每次應用程式啟動時，皆需要用戶端連絡身分識別提供者和行動服務。這個方法不只效率不彰，而且如果同時有許多用戶試圖啟用您的應用程式時，還可能遇到使用量相關的問題。更好的方法就是快取行動服務傳回的驗證權杖，然後嘗試在使用提供者形式登入前先使用此方法。

>[AZURE.NOTE]無論您使用用戶端管理或服務管理驗證，皆可以快取行動服務發行的權杖。本教學課程使用服務管理驗證。


1. 在 Eclipse 中，開啟 ToDoActivity.java 檔案，然後加入下列 import 陳述式：

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.content.SharedPreferences.Editor;

2. 在 `ToDoActivity` 類別中新增下列成員。

    	public static final String SHAREDPREFFILE = "temp";	
	    public static final String USERIDPREF = "uid";	
    	public static final String TOKENPREF = "tkn";	


3. 在 ToDoActivity.java 檔案中，新增下列 `cacheUserToken` 方法的定義。
 
    	private void cacheUserToken(MobileServiceUser user)
	    {
    		SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
    	    Editor editor = prefs.edit();
	        editor.putString(USERIDPREF, user.getUserId());
    	    editor.putString(TOKENPREF, user.getAuthenticationToken());
	        editor.commit();
    	}	
  
    此方法將使用者識別碼和權杖儲存在標示為私人的慣用檔案中。這可以保護快取的存取，如此一來裝置上的其他應用程式將無法存取權杖，因為應用程式的喜好設定已沙箱化。不過，如果有人取得裝置的存取權，他們有可能也可以透過其他方法取得權杖快取的存取權。

    >[AZURE.NOTE]如果使用權杖存取的資料十分敏感，而且有人可能可以取得裝置存取權，那麼您可以使用加密來進一步保護權杖。不過，完整的安全解決方案已超過本教學課程範圍，而且也須視您的安全性需求而定。


4. 在 ToDoActivity.java 檔案中，新增下列 `loadUserTokenCache` 方法的定義。

    	private boolean loadUserTokenCache(MobileServiceClient client)
	    {
	        SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
    	    String userId = prefs.getString(USERIDPREF, "undefined"); 
	        if (userId == "undefined")
	            return false;
    	    String token = prefs.getString(TOKENPREF, "undefined"); 
    	    if (token == "undefined")
    	        return false;
        	    
    	    MobileServiceUser user = new MobileServiceUser(userId);
    	    user.setAuthenticationToken(token);
    	    client.setCurrentUser(user);
        	    
    	    return true;
	    }



5. 在 *ToDoActivity.java* 檔案中，將 `authenticate` 方法改為使用權杖快取的下列方法。如果您想使用 Microsoft 帳戶以外的帳戶，請變更登入提供者。

		private void authenticate() {
			// We first try to load a token cache if one exists.
		    if (loadUserTokenCache(mClient))
		    {
		        createTable();
		    }
		    // If we failed to load a token cache, login and create a token cache
		    else
		    {
			    // Login using the Google provider.    
				ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);
		
		    	Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
		    		@Override
		    		public void onFailure(Throwable exc) {
		    			createAndShowDialog("You must log in. Login Required", "Error");
		    		}   		
		    		@Override
		    		public void onSuccess(MobileServiceUser user) {
		    			createAndShowDialog(String.format(
		                        "You are now logged in - %1$2s",
		                        user.getUserId()), "Success");
		    			cacheUserToken(mClient.getCurrentUser());
		    			createTable();	
		    		}
		    	});
		    }
		}

6. 使用有效的帳戶建置應用程式，並且測試驗證。至少執行 2 次此動作。第一次執行時，您應該會收到登入並建立權杖快取的提示。之後每次執行時，系統會試圖載入用於驗證的權杖快取，而您無須再次登入。

<!---HONumber=July15_HO2-->