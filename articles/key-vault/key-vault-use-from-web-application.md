<properties pageTitle="從 Web 應用程式使用 Azure 金鑰保存庫 | 概觀" description="使用此教學課程來幫助您了解如何從 Web 應用程式使用 Azure 金鑰保存庫。" services="key-vault" documentationCenter="" authors="adamhurwitz" manager="" tags="azure-resource-manager"//>

<tags 
	ms.service="key-vault" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/24/2015" 
	ms.author="adhurwit"/>

# 從 Web 應用程式使用 Azure 金鑰保存庫 #

## 簡介  
使用此教學課程來幫助您了解如何從 Azure 中的 Web 應用程式使用 Azure 金鑰保存庫。它會引導您完成從 Azure 金鑰保存庫存取密碼的程序，以便在 Web 應用程式中使用該密碼。

**預估完成時間：**15 分鐘。


如需 Azure 金鑰保存庫的概觀資訊，請參閱[什麼是 Azure 金鑰保存庫？](key-vault-whatis.md)

## 必要條件 

若要完成本教學課程，您必須具備下列項目：

- Azure 金鑰保存庫中密碼的 URI
- 已在 Azure Active Directory 註冊且有權存取您金鑰保存庫之 Web 應用程式的用戶端識別碼和用戶端密碼
- Web 應用程式。我們將會說明 ASP.NET MVC 應用程式在 Azure 中做為 Web 應用程式部署的步驟。 

> [AZURE.NOTE]在本教學課程中，完成在[開始使用 Azure 金鑰保存庫](key-vault-get-started.md)中所列步驟是很重要的，這樣您才會有 Web 應用程式的密碼 URI 和用戶端識別碼和用戶端密碼。

已在 Azure Active Directory 註冊、且已獲權存取金鑰保存庫的 Web 應用程式將會被用來存取金鑰保存庫。如果情況不是這樣，請回到開始使用教學課程中的註冊應用程式，並重複列出的步驟。

本教學課程是針對 Web 開發人員所設計，這些開發人員必須了解在 Azure 上建立 Web 應用程式的基本概念。如需有關 Azure Web Apps 的詳細資訊，請參閱 [Web Apps 概觀](../app-service-web-overview.md)。



## <a id="packages"></a>新增 Nuget 封裝 ##
有三個 Web 應用程式必須已安裝的封裝。

- Active Directory 驗證程式庫 - 包含與 Azure Active Directory 互動及管理使用者身分識別的方法
- Azure 金鑰保存庫資源庫 - 包含與 Azure 金鑰保存庫互動的方法


您可以使用 Package Manager Console 的 Install-Package 命令安裝這三個封裝。

	// this is currently the latest stable version of ADAL
	Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

	//this is a preview version of the Key Vault Library
	Install-Package Microsoft.Azure.KeyVault -Pre


## <a id="webconfig"></a>修改 Web.Config ##
有三個必須加入至 web.config 檔案的應用程式設定，如下所示。

	<!-- ClientId and ClientSecret refer to the web application registration with Azure Active Directory -->
    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />

	<!-- SecretUri is the URI for the secret in Azure Key Vault -->
    <add key="SecretUri" value="secreturi" />


如果您不打算將您的應用程式做為 Azure Web 應用程式裝載，則您應該將實際的 ClientId、用戶端密碼和密碼 URI 值加入 web.config。因為我們將在 Azure 入口網站中新增實際值以取得額外的安全性層級，不然，您可以保留這些虛擬值。


## <a id="gettoken"></a>新增方法以取得存取權杖 ##
為了能夠使用金鑰保存庫 API，您需要存取權杖。金鑰保存庫用戶端會處理金鑰保存庫 API 的呼叫，但是您必須提供具有取得存取權杖的函式。

以下是從 Azure Active Directory 取得存取權杖的程式碼。此程式碼可以放置在應用程式的任何位置。我想要新增 Utils 或 EncryptionHelper 類別。

	//add these using statements
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using System.Web.Configuration;
	
	//this is an optional property to hold the secret after it is retrieved
	public static string EncryptSecret { get; set; }

	//the method that will be provided to the KeyVaultClient
	public async static Task<string> GetToken(string authority, string resource, string scope)
    {
	    var authContext = new AuthenticationContext(authority);
	    ClientCredential clientCred = new ClientCredential(WebConfigurationManager.AppSettings["ClientId"],
                    WebConfigurationManager.AppSettings["ClientSecret"]);
	    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);
	    
	    if (result == null)
	    	throw new InvalidOperationException("Failed to obtain the JWT token");
	    
	    return result.AccessToken;
    }

## <a id="appstart"></a>在應用程式啟動時擷取密碼 ##
現在我們需要呼叫金鑰保存庫 API，並擷取密碼的程式碼。下列程式碼可以放置在任何位置，只要在您需要使用它之前呼叫即可。我已將程式碼放入 Global.asax 的應用程式啟動事件中，因此它在啟動時會執行一次，並讓密碼可在應用程式中使用。

	//add these using statements
	using Microsoft.Azure.KeyVault;
	using System.Web.Configuration;

	// I put my GetToken method in a Utils class. Change for wherever you placed your method. 
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

	var sec = kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]).Result.Value;
	
	//I put a variable in a Utils class to hold the secret for general  application use. 
    Utils.EncryptSecret = sec;



## <a id="portalsettings"></a>(選擇性) 在 Azure 入口網站中新增應用程式設定 ##
如果您已有 Azure Web Apps，您現在可以在 Azure 入口網站中為 AppSettings 新增實際值。如此一來，實際值將不會存在於 web.config 中，但會透過您有個別存取控制功能的入口網站受到保護。這些值會被您在 web.config 中輸入的值取代。請確定名稱都相同。

![Azure 入口網站中顯示的應用程式設定][1]



## <a id="next"></a>接續步驟 ##


如需程式設計參考，請參閱 [Azure 金鑰保存庫 C# 用戶端 API 參考](https://msdn.microsoft.com/library/azure/dn903628.aspx)。


<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
 

<!---HONumber=58_postMigration-->