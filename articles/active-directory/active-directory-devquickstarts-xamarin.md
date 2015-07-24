<properties
	pageTitle="開始使用 Azure AD Xamarin | Microsoft Azure"
	description="如何建置 Xamarin 應用程式來與 Azure AD 整合進行登入，並使用 OAuth 呼叫受 Azure AD 保護的 API。"
	services="active-directory"
	documentationCenter="xamarin"
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-xamarin"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="04/28/2015"
	ms.author="dastrock"/>


# 將 Azure AD 整合至 Xamarin 應用程式

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Xamarin 允許您以 C# 撰寫可在數個不同平台 (包括行動裝置和電腦之類) 上執行的應用程式。如果您正在建置使用 Xamarin 的應用程式，Azure AD 可讓您簡單又直截了當以 Active Directory 帳戶驗證您的使用者。它也可讓您的應用程式安全地使用任何受 Azure AD 保護的 Web API，例如 Office 365 API 或 Azure API。

對於需要存取受保護資源的 Xamarin 應用程式，Azure AD 提供 Active Directory 驗證程式庫 (ADAL)。ADAL 存在的唯一目的是為了讓您的應用程式輕鬆取得存取權杖。為了示範究竟有多麼簡單，我們將建置一個可執行下列動作的「目錄搜尋程式」應用程式：

-	可在 iOS、Android、Windows 桌面、Windows Phone 和 Windows 市集上執行。
- 使用單一可攜式類別庫 (PCL) 來驗證使用者並取得 Azure AD Graph API 的權杖
-	搜尋目錄以尋找具有指定 UPN 的使用者。

若要建立完整可用的應用程式，您必須：

2. 設定您的 Xamarin 開發環境
2. 向 Azure AD 註冊您的應用程式。
3. 安裝及設定 ADAL。
5. 使用 ADAL 來取得 Azure AD 的權杖。

若要開始使用，請[下載基本架構專案](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/skeleton.zip)或[下載完整的範例](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip)。每一個都是 Visual Studio 2013 解決方案。您還需要一個可以建立使用者並註冊應用程式的 Azure AD 租用戶。如果您還沒有租用戶，[了解如何取得租用戶](active-directory-howto-tenant.md)。

## *0.設定您的 Xamarin 開發環境*
設定 Xamarin 有幾種不同的方式，視您想要鎖定的特定平台而定。由於本教學課程包括適用於 iOS、Android 和 Windows 的專案，我們選擇使用 Visual Studio 2013 和 [Xamarin.iOS 組建主機](http://developer.xamarin.com/guides/ios/getting_started/installation/windows/)，這將需要： - 一部可執行 Visual Studio 和 Windows 應用程式的 Windows 電腦 - 一部 OSX 電腦 (如果您希望能夠執行 iOS 應用程式) - Xamarin 企業訂用帳戶 ([免費試用](http://developer.xamarin.com/guides/cross-platform/getting_started/beginning_a_xamarin_trial/)就已足夠) - [Xamarin for Windows](https://xamarin.com/download)，其中包括 Xamarin.iOS、Xamarin.Android，以及 Visual Studio 整合 (建議在此範例中使用)。 - [Xamarin for OS X](https://xamarin.com/download)，其中包括 Xamarin.iOS (和 Xamarin.iOS 組建主機)、Xamarin.Android、Xamarin.Mac 和 Xamarin Studio。

建議您從 [Xamarin 下載頁面](https://xamarin.com/download)開始，在您的 Mac 和 PC 上安裝 Xamarin。如果您沒有兩部可以使用的 Mac 和 PC 電腦，您仍然可以執行此範例，但可能必須略過某些專案。若是 iOS 和 Android，請依照[詳細安裝指南](http://developer.xamarin.com/guides/cross-platform/getting_started/installation/)進行，如果您想要深入了解開發可用選項的詳細資訊，請查看[建置跨平台應用程式](http://developer.xamarin.com/guides/cross-platform/application_fundamentals/building_cross_platform_applications/part_1_-_understanding_the_xamarin_mobile_platform/)指南。您不需要在此階段中設定開發裝置，您也不需要 Apple 開發人員計劃訂用帳戶 (當然，除非您想要在裝置上執行 iOS 應用程式)。

完成必要設定之後，您可以在 Visual Studio 中開啟解決方案並開始使用。您會看到六個專案：五個平台特定專案和一個可跨所有平台共用的可攜式類別庫，`DirectorySearcher.cs`


## *1.註冊目錄搜尋應用程式*
若要啟用您的應用程式以取得權杖，您必須先在 Azure AD 租用戶中進行註冊，並對其授與存取 Azure AD Graph API 的權限：

-	登入 [Azure 管理入口網站](https://manage.windowsazure.com)
-	在左側導覽中按一下 **Active Directory**
-	選取要在其中註冊應用程式的租用戶。
-	按一下 [**應用程式**] 索引標籤，然後按一下最下面抽屜的 [**新增**]。
-	遵照提示進行，並建立新的**原生用戶端應用程式**。
    -	應用程式的 [**名稱**] 將對使用者說明您的應用程式
    -	「**重新導向 Uri**」是配置和字串的組合，Azure AD 可用它來傳回權杖回應。輸入值，例如 `http://DirectorySearcher`。
-	完成註冊後，AAD 會為您的應用程式指派一個唯一用戶端識別碼。您在後續章節中將會用到這個值，所以請從 [**設定**] 索引標籤中複製此值。
- 此外，在 [**設定**] 索引標籤上找到 [其他應用程式的權限] 區段。在 [**委派權限**] 下，為 [Azure Active Directory] 應用程式新增 [**存取您的組織目錄**] 權限。這樣做可讓您的應用程式查詢 Graph API 的使用者。

## *2.安裝及設定 ADAL*
既然您在 Azure AD 中已經擁有應用程式，您可以安裝 ADAL，並撰寫身分識別相關程式碼。為了讓 ADAL 能夠與 Azure AD 進行通訊，您必須提供一些應用程式註冊相關資訊。從使用封裝管理員主控台將 ADAL 加入解決方案中的每個專案開始。

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirectorySearcherLib -IncludePrerelease
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Android -IncludePrerelease
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Desktop -IncludePrerelease
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-iOS -IncludePrerelease
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Universal.Windows -IncludePrerelease
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Universal.WindowsPhone -IncludePrerelease
`

- 您應該會注意到每個專案會新增兩個程式庫參考：ADAL 的 PCL 部份和平台特定部分。

-	在 DirectorySearcherLib 專案中，開啟 `DirectorySearcher.cs`。變更類別成員值，以反映您在 Azure 入口網站中所輸入的值。每當使用 ADAL 時，您的程式碼便會參考這些值。
    -	`tenant` 是指您的 Azure AD 租用戶網域，例如 contoso.onmicrosoft.com
    -	`clientId` 是指您從入口網站複製的應用程式 clientId。
    - `returnUri` 是指您在入口網站中輸入的 redirectUri，例如 `http://DirectorySearcher`。

## *3.使用 ADAL 來取得 AAD 的權杖*
*幾乎*所有應用程式的驗證邏輯採用 `DirectorySearcher.SearchByAlias(...)`。平台特定專案所需的只是將內容參數傳遞至 `DirectorySearcher` PCL。

- 首先，開啟 `DirectorySearcher.cs` 並在 `SearchByAlias(...)` 方法中加入新的參數。`IPlatformParameters` 是個內容參數，其中包含 ADAL 必須執行驗證的平台特定物件。

```C#
public static async Task<List<User>> SearchByAlias(string alias, IPlatformParameters parent)
{
```

-	接下來，初始化 `AuthenticationContext` - ADAL 的主要類別。這裡是您將與 Azure AD 通訊所需座標傳遞給 ADAL 的位置。然後呼叫 `AcquireTokenAsync(...)`，它可接受 `IPlatformParameters` 物件，並叫用將權杖傳回應用程式所需的驗證流程。

```C#
...
AuthenticationResult authResult = null;

try
{
    AuthenticationContext authContext = new AuthenticationContext(authority);
    authResult = await authContext.AcquireTokenAsync(graphResourceUri, clientId, returnUri, parent);
}
...
```
- `AcquireTokenAsync(...)` 會先嘗試為要求的資源 (在此範例中會是 Graph API) 傳回權杖，而不提示使用者輸入其認證 (透過快取或重新整理舊的權杖)。只有在必要時，它才會在取得要求的權杖之前，對使用者顯示 Azure AD 登入頁面。


- 您可以接著將存取權杖附加至 Graph API 要求的授權標頭中：

```C#
...
request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
...
```

`DirectorySearcher` PCL 和應用程式的身分識別相關程式碼到此告一段落。剩下的工作只是在每個平台檢視中呼叫 `SearchByAlias(...)` 方法，並在必要的地方加入程式碼以便正確地處理 UI 生命週期。

####Android：
- 在 `MainActivity.cs` 中，在按鈕點擊處理常式中新增 `SearchByAlias(...)` 的呼叫：

```C#
List<User> results = await DirectorySearcher.SearchByAlias(searchTermText.Text, new PlatformParameters(this));
```
- 您還必須覆寫 `OnActivityResult` 生命週期方法，將任何驗證重新導向轉送回到適當的方法。ADAL 針對 Android 中的此項作業提供協助程式方法：

```C#
...
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
}
...
```

####Windows 桌面：
- 在 `MainWindow.xaml.cs` 中，只需進行 `SearchByAlias(...)` 呼叫，即可在桌面的 `PlatformParameters` 物件中傳遞 `WindowInteropHelper`：

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

####iOS：
- 在 `DirSearchClient_iOSViewController.cs` 中，iOS `PlatformParameters` 物件只需參考檢視控制器：

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

####Windows 市集
- 在 Windows 市集中，開啟 `MainPage.xaml.cs` 並實作 `Search` 方法，這會視需要在共用專案中使用協助程式方法來更新 UI。

```C#
await UnivDirectoryHelper.Search(
  sender, e,
  SearchResults,
  SearchTermText,
  StatusResult,
  new PlatformParameters(PromptBehavior.Auto, false));
```

####Windows Phone
- 在 Windows Phone 中，開啟 `MainPage.xaml.cs` 並實作 `Search` 方法，這會在共用專案中使用相同的協助程式方法來更新 UI。

```C#
await UnivDirectoryHelper.Search(
  sender, e,
  SearchResults,
  SearchTermText,
  StatusResult,
  new PlatformParameters());
```

恭喜！ 您現在擁有一個能夠在五個不同平台之間驗證使用者並使用 OAuth 2.0 安全地呼叫 Web API 的運作中 Xamarin 應用程式。如果您還沒有這麼做，現在正是將一些使用者植入租用戶的好時機。執行 DirectorySearcher 應用程式，並使用其中一個使用者登入。根據 UPN 搜尋其他使用者。

ADAL 可讓您輕鬆地將常見的身分識別功能納入您的應用程式。它會為您處理一切麻煩的事，包括快取管理、OAuth 通訊協定支援、向使用者顯示登入 UI、重新整理過期權杖等等。您唯一需要知道的就是單一 API 呼叫，`authContext.AcquireToken*(…)`。

[這裡](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip)提供完成的範例供您參考 (不含您的設定值)。您現在可以繼續其他身分識別案例。您可以嘗試：

[使用 Azure AD 保護 .NET Web API >>](active-directory-devquickstarts-webapi-dotnet.md)

如需其他資源，請參閱：- [GitHub 上的 AzureADSamples >>](https://github.com/AzureAdSamples) - [CloudIdentity.com >>](https://cloudidentity.com) - [Azure.com 上的 Azure AD 文件 >>](http://azure.microsoft.com/documentation/services/active-directory/)
 

<!---HONumber=62-->