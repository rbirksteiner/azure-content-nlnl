以下步驟能新建將推播通知傳送給應用程式的自訂 [ApiController](http://go.microsoft.com/fwlink/p/?LinkId=512673)。您可以在 [TableController](http://msdn.microsoft.com/library/azure/dn643359.aspx) (英文) 中或後端服務的其他任何位置實作此相同程式碼。

1. 在 Visual Studio [方案總管] 中，於行動服務專案的 Controllers 資料夾上按一下滑鼠右鍵，展開 [新增]，然後按 [New Scaffolded Item]。

	這會顯示 [新增 Scaffold] 對話方塊。

2. 展開 [**Azure 行動服務**] 並依序按一下 [**Azure 行動服務自訂控制器**] 及 [**新增**]，提供 `NotifyAllUsersController` 的 [**控制器名稱**]，然後再按 [**新增**]。

	![Web API Add Scaffold dialog](./media/mobile-services-dotnet-backend-update-server-push-vs2013/add-custom-api-controller.png)

	這會新建名為 **NotifyAllUsersController** 的空白控制器類別。

3. 在新的 NotifyAllUsersController.cs 專案檔案中，新增下列 **using** 陳述式：

        using Newtonsoft.Json.Linq;
        using System.Threading.Tasks;

4. 在控制器上新增以下實作 POST 方法的程式碼：

        public async Task<bool> Post(JObject data)
        {
            try
            {
                // Define the XML paylod for a WNS native toast notification 
				// that contains the value supplied in the POST request.
                string wnsToast = 
                    string.Format("<?xml version="1.0" encoding="utf-8"?>" +
                    "<toast><visual><binding template="ToastText01">" + 
                    "<text id="1">{0}</text></binding></visual></toast>", 
                    data.GetValue("toast").Value<string>());

                // Define the WNS native toast with the payload string.
                WindowsPushMessage message = new WindowsPushMessage();
                message.XmlPayload = wnsToast;

                // Send the toast notification.
                await Services.Push.SendAsync(message);
                return true;
            }
            catch (Exception e)
            {
                Services.Log.Error(e.ToString());
            }
            return false;
        }

	>[AZURE.NOTE]任何具有應用程式金鑰的用戶端均可呼叫此 POST 方法，因此這並不安全。為了保護端點，請將 `[AuthorizeLevel(AuthorizationLevel.User)]` 屬性套用至方法或類別，以要求驗證。

<!---HONumber=July15_HO2-->