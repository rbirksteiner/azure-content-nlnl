<properties
	pageTitle="開始使用 Azure AD Cordova | Microsoft Azure"
	description="如何建置 Cordova 應用程式來與 Azure AD 整合進行登入，並使用 OAuth 呼叫受 Azure AD 保護的 API。"
	services="active-directory"
	documentationCenter=""
	authors="vibronet"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="07/17/2015"
	ms.author="vittorib"/>

# 整合 Azure AD 與 Apache Cordova 應用程式

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Apache Cordova 可讓您開發 HTML5/JavaScript 應用程式，然後在行動裝置上當做一個完備的原生應用程式執行。Azure AD 可讓您將企業級的驗證功能加入 Cordova 應用程式中。由於 iOS、 Android、 Windows 市集和 Windows Phone 上有一個 Cordova 外掛程式包裝 Azure AD 的原生 SDK，您可以增強您的應用程式來支援以使用者的 AD 帳戶登入、存取 Office 365 和 Azure API，甚至保護對您自己自訂的 Web API 的呼叫。

在本教學課程中，我們將使用 Active Directory 驗證程式庫 (ADAL) 的 Apache Cordova 外掛程式，提供下列功能來改善一個簡單的應用程式：

-	短短幾行程式碼，就可驗證 AD 使用者並取得權杖來呼叫 Azure AD Graph API。
-	使用該權杖叫用 Graph API 來查詢目錄，並顯示結果  
-	運用 ADAL 權杖快取，將使用者的驗證提示減到最少。

若要執行此作業，您需要執行下列動作：

2. 向 Azure AD 註冊應用程式
2. 在您的應用程式中加入程式碼來要求權杖
3. 加入程式碼以使用權杖來查詢 Graph API，並顯示結果。
4. 使用您想要做為目標的所有平台和 Cordova ADAL 外掛程式，建立 Cordova 部署專案，並在模擬器中測試解決方案。

## *0.必要條件*

若要完成本教學課程，您需要：

- Azure AD 租用戶，您在其中有一個帳戶具備應用程式開發權限
- 為了使用 Apache Cordova 而設定的開發環境  

如果兩者都已設定，請直接跳到步驟 1。

如果您沒有 Azure AD 租用戶，您可以[在這裡找到如何取得租用戶的指示](active-directory-howto-tenant.md)。

如果您的電腦上沒有安裝 Apache Cordova，請安裝下列項目：

- [Git](http://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
- [NodeJS](https://nodejs.org/download/)
- [Cordova CLI](https://cordova.apache.org/) (可以輕鬆地透過 NPM 封裝管理員來安裝：`npm install -g cordova`)

請注意，這些都應該可以在 PC 和 Mac 上執行。

每個目標平台各有不同的先決條件。

- 建置和執行 Windows Tablet/PC 或 Phone 應用程式版本
	- [Visual Studio 2013 for Windows with Update 2 或更新版本](http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-windows-8) (Express 或另一個版本)。
- 針對 iOS 建置和執行
	-   Xcode 5.x 或更新版本。請 http://developer.apple.com/downloads 或 [Mac App Store](http://itunes.apple.com/us/app/xcode/id497799835?mt=12) 下載
	-   [ios-sim](https://www.npmjs.org/package/ios-sim) – 可讓您到 iOS 模擬器從命令列啟動 iOS 應用程式 (可以輕鬆地透過終端機來安裝：`npm install -g ios-sim`)

- 針對 Android 建置和執行應用程式
	- 安裝 [Java Development Kit (JDK) 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) 或更新版本。請確定 `JAVA_HOME` (環境變數) 已根據 JDK 安裝路徑 (例如 C:\\Program Files\\Java\\jdk1.7.0\_75) 正確設定。
	- 安裝 [Android SDK](http://developer.android.com/sdk/installing/index.html?pkg=tools)，並將 `<android-sdk-location>\tools` 位置 (例如，C:\\tools\\Android\\android-sdk\\tools) 加入至 `PATH` 環境變數。
	- 開啟 Android SDK Manager (例如，透過終端機：`android`) 並安裝
    - *Android 5.0.1 (API 21)* 平台 SDK
    - *Android SDK Build-tools* 19.1.0 版或更新版本
    - *Android Support Repository* (Extras)

  Android sdk 並不提供任何預設模擬器執行個體。如果您想要在模擬器上執行 Android 應用程式，請從終端機執行 `android avd`，然後選取 [*Create...*]，以建立新的模擬器執行個體。建議使用 *API 層級* 19 或更高版本，請參閱 [AVD Manager](http://developer.android.com/tools/help/avd-manager.html)，取得 Android 模擬器和建立選項的相關資訊。


## *1.向 Azure AD 註冊應用程式*

注意：此為選用步驟。本教學課程提供預先佈建的值，您完全不需要在自己的租用戶中佈建，就能看到可運作的範例。不過，建議您執行這個步驟，並熟悉此程序，因為當您建立自己的應用程式時，就需要這樣做。

Azure AD 只會發出權杖給已知的應用程式。您必須先在租用戶中建立應用程式的項目，才能從應用程式使用 Azure AD。若要在您的租用戶中註冊新的應用程式，請執行下列動作：

- 登入 Azure 管理入口網站
- 在左側導覽中按一下 Active Directory
- 選取您要在其中註冊應用程式的租用戶
- 按一下 [應用程式] 索引標籤，然後按一下最下面抽屜的 [新增]。
- 遵照提示進行，並建立新的「原生用戶端應用程式」。
    - 應用程式的名稱將對使用者說明您的應用程式
    -	[重新導向 URI] 是用來將權杖傳回至您的應用程式的 URI。輸入 `http://MyDirectorySearcherApp`。

完成註冊後，AAD 會為您的應用程式指派一個唯一用戶端識別碼。您在後續章節中將會用到這個值：您可以在新建立的應用程式的 [設定] 索引標籤中找到此值。

## *2.複製本教學課程所需的儲存機制*

從 Shell 或命令列，輸入下列命令：

    git clone https://github.com/AzureAD/azure-activedirectory-library-for-cordova.git
    git clone -b skeleton https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova.git

## *3.建立 Cordova 應用程式*

有多種方式可以建立 Cordova 應用程式。在本教學課程中，我們將使用 Cordova 命令列介面 (CLI)。從 Shell 或命令列，輸入下列命令：


     cordova create DirSearchClient --copy-from="NativeClient-MultiTarget-Cordova/DirSearchClient"

這會建立 Cordova 專案的資料夾結構和架構，並將入門專案的內容複製到 www 子資料夾中。移至新的 DirSearchClient 資料夾。

    cd .\DirSearchClient

加入叫用 Graph API 所需的白名單外掛程式。

     cordova plugin add https://github.com/apache/cordova-plugin-whitelist.git

接著，加入所有您想要支援的平台。您至少必須執行下列其中一個命令，才能發揮範例的實用性。請注意，您無法在 Windows 模擬 iOS，或在 Mac 上模擬 Windows/Windows Phone。

    cordova platform add android@97718a0a25ec50fedf7b023ae63bfcffbcfafb4b
    cordova platform add ios
    cordova platform add windows

最後，您可以將 ADAL for Cordova 外掛程式加入您的專案。

    cordova plugin add ../azure-activedirectory-library-for-cordova

## *3.加入程式碼來驗證使用者，並從 AAD 取得權杖*

您在本教學課程中開發應用程式會提供簡易的目錄搜尋功能，使用者可以輸入目錄中任何使用者的別名，就會呈現一些基本的屬性。入門專案包含應用程式基本使用者介面的定義 (在 www/index.html 中)，也包含串連基本應用程式事件系列的架構、使用者介面繫結及結果顯示邏輯 (在 www/js/index.js 中)。您剩下的工作只是加入實作識別工作的邏輯。

您所要做的第一件事是在程式碼中引入通訊協定值，供 AAD 用於識別您的應用程式和您的目標資源。稍後會使用這些值來建構權杖要求。在 Index.js 檔案最上方插入下面程式碼片段。

```javascript
    var authority = "https://login.windows.net/common",
    redirectUri = "http://MyDirectorySearcherApp",
    resourceUri = "https://graph.windows.net",
    clientId = "a5d92493-ae5a-4a9f-bcbf-9f1d354067d3",
    graphApiVersion = "2013-11-08";
```

`redirectUri` 和 `clientId` 值應該符合 AAD 中用於描述您的應用程式的值。您可以從 Azure 入口網站的 [設定] 索引標籤中找到這些值，如稍早在本教學課程的步驟 1 所述。注意：如果您選擇不在您自己的租用戶中註冊新的應用程式，您可以直接貼上上述預先設定的值，這可讓看到範例在執行，但對於打算實際執行的應用程式，您總應該建立自己的項目。

接下來，我們必須加入實際的權杖要求程式碼。在 `search ` 和 `renderdata ` 定義之間，插入下列程式碼片段。

```javascript
    // Shows user authentication dialog if required.
    authenticate: function (authCompletedCallback) {

        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
            // Attempt to authorize user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user cridentials so triggers authentication dialog
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
        });

    },
```
讓我們將該函式分成兩個主要部分來檢查一下。此範例設計成可搭配任何租用戶，而不受限於特定的租用戶。它使用 "/common" 端點，可讓使用者在驗證時輸入任何帳戶，而且會將要求導向其所屬的租用戶。此方法的第一個部分會檢查 ADAL 快取，查看是否有已儲存的權杖。如果有的話，則使用它的來源租用戶來重新初始化 ADAL。這是為了避免額外提示而必須執行的動作，因為使用 "/common" 永遠會導致要求使用者輸入新的帳戶。```javascript
        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
``` 此方法的第二個部分會執行適當的權杖要求。 `acquireTokenSilentAsync` 方法會要求 ADAL 傳回指定資源的權杖，而不會顯示任何 UX。如果快取中已儲存適當的存取權杖，或者如果有重新整理權杖可用來取得新的存取權杖，而不會顯示任何提示，就會發生此情況。如果該嘗試失敗，我們就退回到 `acquireTokenAsync`，將會明顯提示使用者進行驗證。```javascript
            // Attempt to authorize user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user cridentials so triggers authentication dialog
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
``` 既然已取得權杖，我們終於可以叫用 Graph API，並執行我們想要的搜尋查詢。在 `authenticate` 定義下方，插入下列程式碼片段。

```javascript
// Makes Api call to receive user list.
    requestData: function (authResult, searchText) {
        var req = new XMLHttpRequest();
        var url = resourceUri + "/" + authResult.tenantId + "/users?api-version=" + graphApiVersion;
        url = searchText ? url + "&$filter=mailNickname eq '" + searchText + "'" : url + "&$top=10";

        req.open("GET", url, true);
        req.setRequestHeader('Authorization', 'Bearer ' + authResult.accessToken);

        req.onload = function(e) {
            if (e.target.status >= 200 && e.target.status < 300) {
                app.renderData(JSON.parse(e.target.response));
                return;
            }
            app.error('Data request failed: ' + e.target.response);
        };
        req.onerror = function(e) {
            app.error('Data request failed: ' + e.error);
        }

        req.send();
    },

```
起始點檔案提供簡易型 UX，可在文字方塊中輸入使用者的別名。這個方法會使用該值來建構查詢、將它與存取權杖結合、將它傳送到 Graph，然後剖析結果。起始點檔案中已存在的 renderData 方法會負責呈現結果。

## *4.執行*
終於可以開始執行您的應用程式了！ 操作很簡單：應用程式啟動後，在文字方塊中輸入您要查閱的使用者的別名，然後按一下按鈕。系統會提示您進行驗證。在成功驗證和成功搜尋之後，將會顯示搜尋到的使用者的屬性。後續執行時只會執行搜尋，而不會顯示任何提示，因為先前取得的權杖已存在於快取中。執行應用程式的具體步驟隨著平台而不同。


##### 建置和執行 Windows Tablet/PC 應用程式版本

   `cordova run windows`

   __注意__：第一次執行期間可能會要求您登入，以取得開發人員授權。如需詳細資訊，請參閱[開發人員授權](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx)。


##### 在 Windows Phone 8.1 建置和執行應用程式

   在已連接的裝置上執行：`cordova run windows --device -- --phone`

   在預設模擬器上執行：`cordova emulate windows -- --phone`

   使用 `cordova run windows --list -- --phone` 查看所有可用的目標，使用 `cordova run windows --target=<target_name> -- --phone` 在特定裝置或模擬器上執行應用程式 (例如，`cordova run windows --target="Emulator 8.1 720P 4.7 inch" -- --phone`)。
##### 在 Android 裝置上建置和執行

   在已連接的裝置上執行：`cordova run android --device`

   在預設模擬器上執行：`cordova emulate android`

   __注意__：請確定您已使用 *AVD Manager* 建立模擬器執行個體，如*必要條件*一節所示。

   使用 `cordova run android --list` 查看所有可用的目標，使用 `cordova run android --target=<target_name>` 在特定裝置或模擬器上執行應用程式 (例如，`cordova run android --target="Nexus4_emulator"`)。

##### 在 iOS 裝置上建置和執行

   在已連接的裝置上執行：`cordova run ios --device`

   在預設模擬器上執行：`cordova emulate ios`

   __注意__：請確定您已安裝要在模擬器上執行的 `ios-sim` 封裝。請參閱*必要條件*一節，以取得詳細資訊。

    Use `cordova run ios --list` to see all available targets and `cordova run ios --target=<target_name>` to run application on specific device or emulator (for example,  `cordova run android --target="iPhone-6"`).

使用 `cordova run --help` 查看其他建置和執行選項。

這裡提供完成的範例供您參考 (不含您的設定值)。您現在可以進入更進階 (應該說更有趣) 的案例。您可以嘗試：

[使用 Azure AD 保護 Node.js Web API >>](active-directory-devquickstarts-webapi-nodejs.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
 

<!---HONumber=August15_HO6-->