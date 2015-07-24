
先前範例所示範的標準登入，在每次應用程式啟動時，皆需要用戶端連絡身分識別提供者和行動服務。這個方法不只效率不彰，而且如果同時有許多用戶試圖啟用您的應用程式時，還可能遇到使用量相關的問題。更好的方法就是快取行動服務傳回的驗證權杖，然後嘗試在使用提供者形式登入前先使用此方法。

>[AZURE.NOTE]無論您使用用戶端管理或服務管理驗證，皆可以快取行動服務發行的權杖。本教學課程使用服務管理驗證。

1. 在 default.js 專案檔中，使用下列程式碼來取代現有的 **Login** 函數：

        var credential = null;
        var vault = new Windows.Security.Credentials.PasswordVault();

        // Request authentication from Mobile Services using a Facebook login.
        var login = function () {
            return new WinJS.Promise(function (complete) {
                client.login("facebook").done(function (results) {
                    // Create a credential for the returned user.
                    credential = new Windows.Security.Credentials
                        .PasswordCredential("Facebook", results.userId,
                        results.mobileServiceAuthenticationToken);
                    vault.add(credential);
                    userId = results.userId;

                    refreshTodoItems();
                    var message = "You are now logged in as: " + userId;
                    var dialog = new Windows.UI.Popups.MessageDialog(message);
                    dialog.showAsync().done(complete);
                }, function (error) {
                    var dialog = new Windows.UI.Popups
                        .MessageDialog("An error occurred during login", "Login Required");
                    dialog.showAsync().done(complete);
                });
            });
        }

2. 使用下列程式碼來取代現有的 **authenticate** 函數：

        var authenticate = function () {
            // Try to get a stored credential from the PasswordVault.                
            try{
                credential = vault.findAllByResource("Facebook").getAt(0);
            }
            catch (error) {
                // This is expected when there's no stored credential.
            }
            
            if (credential) {
                // Set the user from the returned credential.   
                credential.retrievePassword();
                client.currentUser = {
                    "userId": credential.userName,
                    "mobileServiceAuthenticationToken": credential.password
                };

                // Try to return an item now to determine if the cached credential has expired.
                todoTable.take(1).read()
                            .done(function () {
                                refreshTodoItems();
                            }, function (error) {
                                if (error.request.status === 401) {
                                    login(credential, vault).then(function () {
                                        if (!credential) {

                                            // Authentication failed, try again.
                                            authenticate();
                                        }
                                    });
                                }                                   
                            });
            } else {

                login().then(function () {
                    if (!credential) {
                        // Authentication failed, try again.
                        authenticate();
                    }
                });
            }
        }

	在這個版本的 **authenticate** 中，應用程式會嘗試使用已儲存於 **PasswordVault** 中的認證來存取行動服務。系統會傳送簡單的查詢，以確認儲存的權杖尚未到期。傳回 401 時，即會嘗試以提供者為主的一般登入。如果沒有儲存任何認證，也會執行一般登入。

3. 重新啟動應用程式兩次。

	請注意，第一次啟動時，需要再次使用該提供者登入。不過，在第二次重新啟動時，可以使用快取的認證，並略過登入。

<!---HONumber=July15_HO2-->