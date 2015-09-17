<properties
	pageTitle="開始使用 Azure AD AngularJS | Microsoft Azure"
	description="如何建置 Angular JS 單一頁面應用程式來與 Azure AD 整合進行登入，並使用 OAuth 呼叫受 Azure AD 保護的 API。"
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="07/17/2015"
	ms.author="dastrock"/>


# 使用 Azure AD 保護 AngularJS 單一頁面應用程式

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure AD 可讓您簡單又直截了當加入登入、登出，並保護對單一頁面應用程式的 OAuth API 呼叫。它也可讓您的應用程式以 Active Directory 帳戶來驗證使用者，並取用受 Azure AD 保護的任何 Web API，例如 Office 365 API 或 Azure API。

對於在瀏覽器中執行的 javascript 應用程式，Azure AD 提供 Active Directory 驗證程式庫 (又稱為 adal.js)。Adal.js 存在的唯一目的是讓您的應用程式輕鬆取得存取權杖。為了示範究竟多麼簡單，我們將建置一個執行下列動作的 AngularJS 待辦事項清單應用程式：

- 使用 Azure AD 做為身分識別提供者，將使用者登入應用程式。
- 顯示使用者的一些相關資訊。
- 使用 AAD 簽發的持有者權杖，安全地呼叫應用程式的待辦事項清單 API。
- 將使用者登出應用程式。

若要建立可完整運作的應用程式，您必須：

2. 向 Azure AD 註冊您的應用程式。
3. 安裝 ADAL 並設定 SPA。
5. 使用 ADAL 來保護 SPA 中的頁面。

若要開始使用，請[下載應用程式基本架構](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/skeleton.zip)或[下載完整的範例](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip)。您還需要一個可以建立使用者並註冊應用程式的 Azure AD 租用戶。如果您還沒有租用戶，[了解如何取得租用戶](active-directory-howto-tenant.md)。

## *1.註冊 DirectorySearcher 應用程式*
若要讓應用程式能夠驗證使用者並取得權杖，您必須先在 Azure AD 租用戶中註冊這個應用程式。

-	登入 [Azure 管理入口網站](https://manage.windowsazure.com)
-	在左側導覽中按一下 **Active Directory**
-	選取要在其中註冊應用程式的租用戶。
-	按一下 [**應用程式**] 索引標籤，然後按一下最下面抽屜的 [**新增**]。
-	遵照提示進行，並建立新的 **Web 應用程式和/或 WebAPI**。
    -	應用程式的 [**名稱**] 將對使用者說明您的應用程式。
    -	[**重新導向 URI**] 是供 AAD 傳回權杖的位置。此範例的預設位置是 `https://localhost:44326/`
-	完成註冊後，AAD 會為您的應用程式指派一個唯一 [**用戶端識別碼**]。您在後續章節中將會用到這個值，所以請從 [**設定**] 索引標籤中複製此值。
- Adal.js 會使用 OAuth 隱含流程來與 Azure AD 通訊。您必須為您的應用程式啟用隱含流程，請執行下列動作：
    - 按一下 [**管理資訊清單**]，下載應用程式資訊清單。
    - 開啟資訊清單，並找出 `oauth2AllowImplicitFlow` 屬性。將值設為 `true`。
    - 再按一次 [**管理資訊清單**]，以儲存並上傳應用程式資訊清單。

## *2.安裝 ADAL 並設定 SPA*
既然您在 Azure AD 中已有應用程式，您可以安裝 adal.js，並撰寫身分識別相關的程式碼。

-	首先，使用 Package Manager Console，將 adal.js 加入至 TodoSPA 專案：
  - 下載 [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal.js) 並加入至 `App/Scripts/` 專案目錄。
  - 下載 [adal-angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal-angular.js) 並加入至 `App/Scripts/` 專案目錄。
  - 在 `index.html` 中的 `</body>` 結尾之前，載入每個指令碼：

```js
...
<script src="App/Scripts/adal.js"></script>
<script src="App/Scripts/adal-angular.js"></script>
...
```

-	為了讓 SPA 的後端待辦事項清單應用程式從瀏覽器接受權杖，後端需要應用程式註冊的組態資訊。在 TodoSPA 專案中，開啟 `web.config`。取代 `<appSettings>` 區段中的元素值，以反映您在 Azure 入口網站中所輸入的值。每當使用 ADAL 時，您的程式碼便會參考這些值。
    -	`ida:Tenant` 是指您的 Azure AD 租用戶網域，例如 contoso.onmicrosoft.com
    -	`ida:Audience` 必須是您從入口網站複製的應用程式 [**用戶端識別碼**]。

## *3.使用 ADAL 來保護 SPA 中的頁面*
Adal.js 已建置為與 AngularJS 路由和 http 提供者整合，可讓您保護 SPA 中的個別檢視。

- 在 `App/Scripts/app.js` 中，載入 adal.js 模組：

```js
angular.module('todoApp', ['ngRoute','AdalAngular'])
.config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
 function ($routeProvider, $httpProvider, adalProvider) {
...
```
- 您現在可以使用應用程式註冊的組態值來初始化 `adalProvider` (也在 `App/Scripts/app.js` 中)：

```js
adalProvider.init(
  {
      instance: 'https://login.microsoftonline.com/',
      tenant: 'Enter your tenant name here e.g. contoso.onmicrosoft.com',
      clientId: 'Enter your client ID here e.g. e9a5a8b6-8af7-4719-9821-0deef255f68e',
      extraQueryParameter: 'nux=1',
      //cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
  },
  $httpProvider
);
```
- 現在只需要一行程式碼 - `requireADLogin`，就能保護應用程式中的 `TodoList` 檢視。

```js
...
}).when("/TodoList", {
        controller: "todoListCtrl",
        templateUrl: "/App/Views/TodoList.html",
        requireADLogin: true,
...
```

您現在有一個安全的單一頁面應用程式，能夠將使用者登入，並發出以持有人權杖保護的要求至其後端 API。當使用者按一下 `TodoList` 連結時，需要的話，adal.js 會自動重新導向至 Azure AD 進行登入。此外，adal.js 將會自動將 access\_token 附加至任何傳送至應用程式後端的 ajax 要求。以上是以 adal.js 建置 SPA 時最起碼的條件，但 SPA 中還有其他許多有用的功能：

- 若要明確發出登入和登出要求，您可以在控制器中定義函式來叫用 adal.js。在 `App/Scripts/homeCtrl.js` 中：

```js
...
$scope.login = function () {
    adalService.login();
};
$scope.logout = function () {
    adalService.logOut();
};
...
```
- 您也可以在應用程式的 UI 中顯示使用者資訊。Adal 服務已經加入至 `userDataCtrl` 控制器，您現在可以在相關聯檢視 `App/Scripts/UserData.html` 中存取 `userInfo` 物件：

```js
<p>{{userInfo.userName}}</p>
<p>aud:{{userInfo.profile.aud}}</p>
<p>iss:{{userInfo.profile.iss}}</p>
...
```

- 在許多情況下，您也可能想知道使用者是否已登入。您也可以使用 `userInfo` 物件來收集此資訊。例如，在 `index.html` 中，您可以根據驗證狀態顯示 [登入] 或 [登出] 按鈕：

```js
<li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
<li><a class="btn btn-link" ng-hide=" userInfo.isAuthenticated" ng-click="login()">Login</a></li>
```

恭喜！ 您的 Azure AD 整合式單一頁面應用程式現在已完成。它可以驗證使用者、使用 OAuth 2.0 安全地呼叫其後端，以及取得使用者的基本資訊。如果您還沒有這麼做，現在是將一些使用者植入租用戶的時候。執行待辦事項清單 SPA，並使用其中一個使用者登入。將工作加入至使用者待辦事項清單、登出，再重新登入。

Aaal.js 可讓您輕鬆地將這些常見的身分識別功能全部納入您的應用程式。它會為您處理一切麻煩的事，包括快取管理、OAuth 通訊協定支援、向使用者顯示登入 UI、重新整理過期權杖等等。

[這裡](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip)提供完成的範例供您參考 (不含您的設定值)。您現在可以繼續探索其他案例。您可以嘗試：

[從 SPA 呼叫 CORS Web API >>](https://github.com/AzureAdSamples/SinglePageApp-WebAPI-AngularJS-DotNet)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=August15_HO9-->