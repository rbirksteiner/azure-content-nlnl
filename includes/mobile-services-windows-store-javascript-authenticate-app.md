

1. 開啟專案檔案 default.js，然後在 **app.OnActivated** 方法多載中，以下列程式碼取代對 **refreshTodoItems** 方法最後的呼叫： 
	
        var userId = null;

        // Request authentication from Mobile Services using a Facebook login.
        var login = function () {
            return new WinJS.Promise(function (complete) {
                client.login("facebook").done(function (results) {
                    userId = results.userId;
                    refreshTodoItems();
                    var message = "You are now logged in as: " + userId;
                    var dialog = new Windows.UI.Popups.MessageDialog(message);
                    dialog.showAsync().done(complete);
                }, function (error) {
                    userId = null;
                    var dialog = new Windows.UI.Popups
                        .MessageDialog("An error occurred during login", "Login Required");
                    dialog.showAsync().done(complete);
                });
            });
        }            

        var authenticate = function () {
            login().then(function () {
                if (userId === null) {

                    // Authentication failed, try again.
                    authenticate();
                }
            });
        }

        authenticate();

    如此會建立一個成員變數來存放目前使用者，並建立一個方法來處理驗證程序。使用者透過 Facebook 登入來驗證。如果您使用的身分識別提供者不是 Facebook，請將傳給上述 <strong>login</strong> 方法的值變更為下列其中一個：_microsoftaccount_、_twitter_、_google_ 或 _windowsazureactivedirectory_。

    >[AZURE.NOTE]如果您已向行動服務註冊您的 Windows 市集應用程式套件資訊，則應該為 <em>useSingleSignOn</em> 參數提供 <strong>true</strong> 值來呼叫 <a href="http://go.microsoft.com/fwlink/p/?LinkId=322050" target="_blank">login</a> 方法。若您沒有這麼做，您的使用者將在每次呼叫登入方法時都看到登入提示。

2. 按 F5 鍵執行應用程式，並以您選擇的身分識別提供者登入應用程式。

   	成功登入後，應用程式應會正確無誤地執行，而且您應能夠查詢行動服務並更新資料。

<!---HONumber=July15_HO2-->