

根據預設，所有對行動服務資源的要求都會限定於提供應用程式金鑰的用戶端 (此金鑰並不會嚴格保護對資源的存取)。若要保護您的資源，您必須限定只有經過驗證的用戶端可進行存取。

1. 在 Visual Studio 中開啟行動專案，展開 Controllers 資料夾，然後開啟 **TodoItemController.cs**。**TodoItemController** 類別會實作 TodoItem 資料表的資料存取。加入下列 `using` 陳述式：

		using Microsoft.WindowsAzure.Mobile.Service.Security;

2. 將下列 _AuthorizeLevel_ 屬性套用至 **TodoItemController** 類別：

		[AuthorizeLevel(AuthorizationLevel.User)]

	如此可確保對 _TodoItem_ 資料表的所有操作都必須由經過驗證的使用者進行。您也可以套用方法層級的 *AuthorizeLevel* 屬性。

3. (選擇性) 如果您想要在本機偵測驗證錯誤，請展開 `App_Start` 資料夾，開啟 **WebApiConfig.cs**，然後將下列程式碼新增至 **Register** 方法。

		config.SetIsHosted(true);

	這會指示本機行動服務專案依循在 Azure 中受託管的型態執行，包括接受 *AuthorizeLevel* 設定。如果沒有此設定，則無論是否有 *AuthorizeLevel* 設定，都會在未經驗證的情況下允許所有對 localhost 的 HTTP 要求。當您啟用自我託管模式時，您也需要設定本機應用程式金鑰的值。

4. (選擇性) 在 web.config 專案檔案中，設定 `MS_ApplicationKey` 應用程式設定的字串值。

	當您在本機執行服務時，您可以使用這個密碼 (不需要使用者名稱) 來測試 API 說明頁面。Azure 中的作用中網站不會使用這個字串值，您也不需要使用實際的應用程式金鑰，任何有效的字串值都適用。
 
4. 重新發佈您的專案。

<!---HONumber=July15_HO2-->