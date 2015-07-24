### 擷取連接字串
您可以使用 **CloudStorageAccount** 類型來代表儲存體帳戶資訊。如果您使用 Azure 專案範本且 (或) 具有 Microsoft.WindowsAzure.CloudConfigurationManager 命名空間參照，可以使用 **CloudConfigurationManager** 類型，擷取 Azure 服務設定中的儲存體連接字串與儲存體帳戶資訊：

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

如果您是建立不含 Microsoft.WindowsAzure.CloudConfigurationManager 參照的應用程式，且連接字串位於 `web.config` 或 `app.config` (如上所示)，則可使用 **ConfigurationManager** 來擷取連接字串。您需要將 System.Configuration.dll 參照新增至專案，並為其新增其他命名空間宣告：

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"]);

<!---HONumber=July15_HO2-->