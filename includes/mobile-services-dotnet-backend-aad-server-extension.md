### (選用) 設定 Azure Active Directory 的 .NET 行動服務

>[AZURE.NOTE]這些都是選用步驟，因為它們只適用於 Azure Active Directory 登入提供者。

1. 安裝 [WindowsAzure.MobileServices.Backend.Security NuGet 封裝](https://www.nuget.org/packages/WindowsAzure.MobileServices.Backend.Security)。

2. 在 Visual Studio 中，展開 App_Start，然後開啟 WebApiConfig.cs。在頂端新增下列 `using` 陳述式：

        using Microsoft.WindowsAzure.Mobile.Service.Security.Providers;

3. 另外，在 WebApiConfig.cs 中，將 `options` 具現化之後，立即將下列程式碼新增至 `Register` 方法：

        options.LoginProviders.Remove(typeof(AzureActiveDirectoryLoginProvider));
        options.LoginProviders.Add(typeof(AzureActiveDirectoryExtendedLoginProvider));

<!---HONumber=July15_HO2-->