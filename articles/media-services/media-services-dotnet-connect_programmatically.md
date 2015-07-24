<properties 
	pageTitle="使用 .NET 連線到媒體服務帳戶" 
	description="本主題示範如何使用 .NET 連線到媒體服務。" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/24/2015" 
	ms.author="juliako"/>


# 使用 Media Services SDK for .NET 連線到媒體服務帳戶

這篇文章是[媒體服務點播視訊工作流程](media-services-video-on-demand-workflow.md)和[媒體服務即時資料流工作流程](media-services-live-streaming-workflow.md)系列的一部分。

本主題描述使用 Media Services SDK for .NET 進行程式設計時，如何取得與 Microsoft Azure 媒體服務的程式設計連線。


## 連線到媒體服務

若要以程式設計方式連線到媒體服務，您必須先前已設定 Azure 帳戶，並在該帳戶上設定媒體服務，然後設定 Visual Studio 專案，才能使用 Media Services SDK for .NET 進行開發。如需詳細資訊，請參閱＜使用 Media Services SDK for .NET　進行開發的設定＞。

媒體服務帳戶設定程序結束時，您會取得下列必要連線值。使用這些值，即可進行與媒體服務的程式設計連線。

- 媒體服務帳戶名稱。

- 媒體服務帳戶金鑰。

若要尋找這些值，請移至 Azure 管理入口網站，並選取您的媒體服務帳戶，然後按一下入口網站視窗底部的 [**管理金鑰**] 圖示。按一下每個文字方塊旁邊的圖示，會將值複製到系統剪貼簿。


## 建立 CloudMediaContext 執行個體

若要開始針對媒體服務進行程式設計，您需要建立可呈現伺服器內容的 **CloudMediaContext** 執行個體。**CloudMediaContext** 包含重要集合的參考，包括工作、資產、檔案、存取原則和定位器。

>[AZURE.NOTE]**CloudMediaContext** 類別不具備執行緒安全。您應該為每個執行緒或每組作業建立新的 CloudMediaContext。


CloudMediaContext 有五個建構函式多載。建議使用採用 **MediaServicesCredentials** 做為參數的建構函式。如需詳細資訊，請參閱下面的**重複使用存取控制服務權杖**。

下列範例會使用公用 CloudMediaContext(MediaServicesCredentials 認證) 建構函式：

	// _cachedCredentials and _context are class member variables. 
	_cachedCredentials = new MediaServicesCredentials(
	                _mediaServicesAccountName,
	                _mediaServicesAccountKey);
	
	_context = new CloudMediaContext(_cachedCredentials);


## 重複使用存取控制服務權杖

本節顯示如何使用採用 MediaServicesCredentials 做為參數的 CloudMediaContext 建構函式，來重複使用存取控制服務權杖。


[Azure Active Directory 存取控制](https://msdn.microsoft.com/library/hh147631.aspx) (也稱為存取控制服務或 ACS) 是一種雲端型服務，可提供簡單的方式來驗證和授權使用者存取其 Web 應用程式。Microsoft Azure 媒體服務會透過需要 ACS 權杖的 OAuth 通訊協定來控制其服務的存取。媒體服務會收到來自授權伺服器的 ACS 權杖。

使用 Media Services SDK 進行開發時，您可以選擇不處理權杖，因為 SDK 程式碼會進行管理。不過，讓 SDK 全權管理 ACS 權杖會導致不必要的權杖要求。要求權杖十分耗時，並且會耗用用戶端和伺服器資源。此外，如果速率太高，則 ACS 伺服器會節流處理要求。限制是每秒 30 個要求，如需詳細資料，請參閱 [ACS 服務限制](https://msdn.microsoft.com/library/gg185909.aspx)。

從 Media Services SDK 3.0.0.0 版開始，您可以重複使用 ACS 權杖。採用 **MediaServicesCredentials** 做為參數的 **CloudMediaContext** 建構函式會啟用多個內容之間的 ACS 權杖共用。MediaServicesCredentials 類別會封裝媒體服務認證。如果有 ACS 權杖可用，並且知道其到期時間，則可以使用權杖建立新的 MediaServicesCredentials 執行個體，並將它傳遞給 CloudMediaContext 的建構函式。請注意，Media Services SDK 會在權杖到期時自動重新整理權杖。如下列範例所示，有兩種方式可以重複使用 ACS 權杖。

- 您可以快取記憶體中的 **MediaServicesCredentials** 物件 (例如，在靜態類別變數中)。然後，將快取的物件傳遞至 CloudMediaContext 建構函式。MediaServicesCredentials 物件包含可重複使用的 ACS 權杖 (如果仍然有效)。如果權杖無效，則 Media Services SDK 會使用提供給 MediaServicesCredentials 建構函式的認證來重新整理權杖。

	請注意，呼叫 RefreshToken 之後，**MediaServicesCredentials** 物件會取得有效的權杖。**CloudMediaContext** 會呼叫建構函式中的 **RefreshToken** 方法。如果您想要將權杖值儲存到外接式儲存裝置，請一定要先檢查 TokenExpiration 值是否有效，再儲存權杖資料。如果無效，則請在快取前呼叫 RefreshToken。

		// Create and cache the Media Services credentials in a static class variable.
		_cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey);

		
		// Use the cached credentials to create a new CloudMediaContext object.
		if(_cachedCredentials == null)
		{
		    _cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey);
		}
		
		CloudMediaContext context = new CloudMediaContext(_cachedCredentials);

- 您也可以快取 AccessToken 字串和 TokenExpiration 值。稍後可以使用這些值，來建立具有快取權杖資料的新 MediaServicesCredentials 物件。這特別適用於權杖可以在多個處理程序或電腦之間安全共用的情況。

	下列程式碼片段會呼叫此範例中未定義的 SaveTokenDataToExternalStorage、GetTokenDataFromExternalStorage 和 UpdateTokenDataInExternalStorageIfNeeded 方法。您可以定義這些方法來儲存、擷取和更新外接式儲存裝置中的權杖資料。

		CloudMediaContext context1 = new CloudMediaContext(_mediaServicesAccountName, _mediaServicesAccountKey);
		
		// Get token values from the context.
		var accessToken = context1.Credentials.AccessToken;
		var tokenExpiration = context1.Credentials.TokenExpiration;
		
		// Save token values for later use. 
		// The SaveTokenDataToExternalStorage method should check 
		// whether the TokenExpiration value is valid before saving the token data. 
		// If it is not valid, call MediaServicesCredentials’s RefreshToken before caching.
		SaveTokenDataToExternalStorage(accessToken, tokenExpiration);
		
	使用儲存的權杖值來建立 MediaServicesCredentials。


		var accessToken = "";
		var tokenExpiration = DateTime.UtcNow;
		
		// Retrieve saved token values.
		GetTokenDataFromExternalStorage(out accessToken, out tokenExpiration);
		
		// Create a new MediaServicesCredentials object using saved token values.
		MediaServicesCredentials credentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey)
		{
		    AccessToken = accessToken,
		    TokenExpiration = tokenExpiration
		};
		
		CloudMediaContext context2 = new CloudMediaContext(credentials);

	更新權杖副本 (以免媒體服務 SDK 已更新權杖)。
	
		if(tokenExpiration != context2.Credentials.TokenExpiration)
		{
		    UpdateTokenDataInExternalStorageIfNeeded(accessToken, context2.Credentials.TokenExpiration);
		}
		

- 如果您有多個媒體服務帳戶 (例如，針對負載共用目的或地理分散)，則可以使用 System.Collections.Concurrent.ConcurrentDictionary 集合來快取 MediaServicesCredentials 物件 (ConcurrentDictionary 集合代表多個執行緒可以並行存取之金鑰/值組的執行緒安全集合)。您之後就可以使用 GetOrAdd 方法來取得快取的認證。

		// Declare a static class variable of the ConcurrentDictionary type in which the Media Services credentials will be cached.  
		private static readonly ConcurrentDictionary<string, MediaServicesCredentials> mediaServicesCredentialsCache = 
		    new ConcurrentDictionary<string, MediaServicesCredentials>();
		

		// Cache (or get already cached) Media Services credentials. Use these credentials to create a new CloudMediaContext object.
		static public CloudMediaContext CreateMediaServicesContext(string accountName, string accountKey)
		{
		    CloudMediaContext cloudMediaContext;
		    MediaServicesCredentials mediaServicesCredentials;
		
		    mediaServicesCredentials = mediaServicesCredentialsCache.GetOrAdd(
		        accountName,
		        valueFactory => new MediaServicesCredentials(accountName, accountKey));
		
		    cloudMediaContext = new CloudMediaContext(mediaServicesCredentials);
		
		    return cloudMediaContext;
		}
		
## 連線到位於中國北部地區的媒體服務帳戶

如果您的帳戶位於中國北部地區，請使用下列建構函式：

	public CloudMediaContext(Uri apiServer, string accountName, string accountKey, string scope, string acsBaseAddress)

例如：


	_context = new CloudMediaContext(
	    new Uri("https://wamsbjbclus001rest-hs.chinacloudapp.cn/API/"),
	    _mediaServicesAccountName,
	    _mediaServicesAccountKey,
	    "urn:WindowsAzureMediaServices",
	    "https://wamsprodglobal001acs.accesscontrol.chinacloudapi.cn");


## 在組態中儲存連線值

強烈建議使用這種作法將連線值儲存在組態中，尤其是敏感的值 (例如您的帳戶名稱與密碼)。另外，也建議將敏感的組態資料加密。您可以使用 Windows 加密檔案系統 (EFS) 將整個組態檔加密。若要在檔案上啟用 EFS，請在檔案上按一下滑鼠右鍵，並選取 [**屬性**]，然後在 [**進階設定**] 索引標籤中啟用加密。或者，可以使用受保護的組態，建立將選擇之組態檔案內的部分加密的自訂解決方案。請參閱[使用受保護組態將組態資訊加密](https://msdn.microsoft.com/library/53tyfkaw.aspx)。

下列 App.config 檔案包含必要的連線值。<appSettings> 元素中的值是您在媒體服務帳戶設定程序中取得的必要值。


<pre><code>
&lt;configuration>
    &lt;appSettings>
	&lt;add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
    	&lt;add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
    &lt;/appSettings>
&lt;/configuration>
</code></pre>

若要從組態擷取連線值，可以使用 **ConfigurationManager** 類別，然後將值指派至程式碼中的欄位：
	
	private static readonly string _accountName = ConfigurationManager.AppSettings["MediaServicesAccountName"];
	private static readonly string _accountKey = ConfigurationManager.AppSettings["MediaServicesAccountKey"];


<!-- Anchors. -->


<!-- URLs. -->

<!---HONumber=July15_HO1-->