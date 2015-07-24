

1. 開啟專案檔案 default.js，然後在 **app.OnActivated** 方法多載中，以下列程式碼取代對 **refreshTodoItems** 方法最後的呼叫： 

        // Define a member variable for storing the signed-in user.
        var userId = null;

        // Request authentication from Mobile Services using a Facebook login.
        var authenticate = function () {
            return new WinJS.Promise(function (complete) {
                // Change 'client' to the name of your MobileServiceClient instance.
                // Sign-in using Facebook authentication.
                client.login("facebook").done(function (results) {
                    userId = results.userId;
                    refreshTodoItems();
                    buttonLogin.disabled = true;
                    var message = "You are now signed in as: " + userId;
                    var dialog = new Windows.UI.Popups.MessageDialog(message);
                    dialog.showAsync().done(complete);
                }, function (error) {
                    userId = null;
                    var dialog = new Windows.UI.Popups
                    .MessageDialog(error);
                        //.MessageDialog("An error occurred during login", "Login Required");
                    dialog.showAsync().done(complete);
                });
            });
        };

        // Handle the sign in button click event.
        buttonLogin.addEventListener("click", function () {
            authenticate();
        });

	如果您使用的身分識別提供者不是 Facebook，請將傳給上述 <strong>login</strong> 方法的值變更為下列其中一個：_microsoftaccount_、_twitter_、_google_ 或 _windowsazureactivedirectory_。

    >[AZURE.NOTE]如果您已向行動服務註冊您的 Windows 市集應用程式套件資訊，則應該為 <em>useSingleSignOn</em> 參數提供 <strong>true</strong> 值來呼叫 <a href="http://go.microsoft.com/fwlink/p/?LinkId=322050" target="_blank">login</a> 方法。若您沒有這麼做，您的使用者將在每次呼叫登入方法時都看到登入提示。

2. 在 Windows 市集應用程式專案中，開啟 default.html 專案檔案，並將以下 **button** 元素新增至定義 **save** 按鈕的元素前方：

      	<button id="buttonLogin" style="margin-left: 5px">Sign in</button>

3. 按 F5 鍵執行應用程式，並以您選擇的身分識別提供者登入應用程式。

   	成功登入後，應用程式應會正確無誤地執行，而且您應能夠查詢行動服務並更新資料。

<!---HONumber=July15_HO2-->