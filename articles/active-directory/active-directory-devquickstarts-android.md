<properties
	pageTitle="開始使用 Azure AD Android | Microsoft Azure"
	description="如何建置 Android 應用程式來與 Azure AD 整合進行登入，並使用 OAuth 呼叫受 Azure AD 保護的 API。"
	services="active-directory"
	documentationCenter="android"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="04/28/2015"
	ms.author="brandwe"/>

# 將 Azure AD 整合至 Android 應用程式

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

如果您正在開發桌面應用程式，Azure AD 讓使用 Active Directory 帳戶驗證您的使用者變得簡單明瞭。它也可讓您的應用程式安全地使用任何受 Azure AD 保護的 Web API，例如 Office 365 API 或 Azure API。

對於需要存取受保護資源的 Android 用戶端，Azure AD 提供 Active Directory 驗證程式庫 (ADAL)。ADAL 存在的唯一目的是為了讓您的應用程式輕鬆取得存取權杖。為了示範究竟多麼簡單，我們將建置一個執行下列動作的 Android 待辦事項清單應用程式：

-	取得存取權杖來使用 [OAuth 2.0 驗證通訊協定](https://msdn.microsoft.com/library/azure/dn645545.aspx)呼叫待辦事項清單 API。
-	取得使用者的待辦事項清單
-	將使用者登出。

首先，您需要 Azure AD 租用戶，供您建立使用者並登錄應用程式。如果您還沒有租用戶，[了解如何取得租用戶](active-directory-howto-tenant.md)。

## 步驟 1：下載並執行 Node.js REST API TODO 範例伺服器

我們特別撰寫此範例，以有別於為 Microsoft Azure Active Directory 建置單一租用戶 To-Do REST API 的現有範例。這是快速入門的必要條件。

如需有關如何設定的資訊，請造訪我們現有的範例：

* [適用於 Node.js 的 Microsoft Azure Active Directory 範例 REST API 服務](active-directory-devquickstarts-webapi-nodejs.md)

## 步驟 2：向 Microsoft Azure AD 租用戶註冊 Web API

**我要做什麼？**

*Microsoft Active Directory 支援加入兩種類型的應用程式。Web API 提供服務給可存取這些 Web API 的使用者和應用程式 (無論是在 Web 或在裝置上執行的應用程式)。在此步驟中，您要註冊在本機執行且用來測試此範例的 Web API。這個 Web API 通常是 REST 服務，提供您想要應用程式存取的功能。Microsoft Azure Active Directory 可以保護任何端點！*

*在本範例中，我們假設您正要註冊上述的 TODO REST API，但這適用於任何您想要 Azure Active Directory 保護的 Web API。*

向 Microsoft Azure AD 註冊 Web API 的步驟

1. 登入 [Azure 管理入口網站](https://manage.windowsazure.com)。
2. 在左側導覽中按一下 Active Directory。
3. 按一下您要註冊範例應用程式的目錄租用戶。
4. 按一下 [應用程式] 索引標籤。
5. 在下拉式清單中，按一下 [新增]。
6. 按一下 [加入我的組織正在開發的應用程式]。
7. 輸入應用程式的易記名稱，例如 "TodoListService"，選取 [Web 應用程式和/或 Web API]，然後按 [下一步]。
8. 在 [登入 URL] 中，輸入範例的基礎 URL，預設為 `https://localhost:8080`。
9. 在 [應用程式識別碼 URI] 中，輸入 `https://<your_tenant_name>/TodoListService`，將 `<your_tenant_name>` 換成您的 Azure AD 租用戶名稱。按一下 [確定] 以完成註冊。
10. 在 Azure 入口網站中時，按一下您的應用程式的 [設定] 索引標籤。
11. **找出 [用戶端識別碼] 值，將它複製到別處**，稍後設定應用程式時需要此值。

## 步驟 3：註冊範例 Android 原生用戶端應用程式

第一個步驟是註冊您的 Web 應用程式。接下來，您也需要讓 Azure Active Directory 知道您的應用程式。這可讓您的應用程式與剛註冊的 Web API 通訊

**我要做什麼？**

*如上所述，Microsoft Active Directory 支援加入兩種類型的應用程式。Web API 提供服務給可存取這些 Web API 的使用者和應用程式 (無論是在 Web 或在裝置上執行的應用程式)。在此步驟中，您要註冊此範例中的應用程式。您必須執行此動作，此應用程式才能要求存取您剛剛註冊的 Web API。除非註冊應用程式，否則 Azure Active Directory 甚至會拒絕讓您的應用程式要求登入！ 這是模型安全性的一部分。*

*在本範例中，我們假設您要註冊上述的範例應用程式，但這適用於任何您想要開發的應用程式。*

**為什麼我要將應用程式和 Web API 都放在一個租用戶？**

*您可能已經猜到，您可以建置應用程式來存取在另一個租用戶的 Azure Active Directory 中註冊的外部 API。如果您這麼做，系統會提示您的客戶同意您可以使用應用程式中的 API。幸好，Active Directory Authentication Library for iOS 會替您處理此同意舉動！ 隨著我們深入更進階的功能，您將了解這是從 Azure 和 Office 及任何其他服務提供者存取 Microsoft API 套件時所需的一件重要工作。現在，因為您在相同租用戶下註冊您的 Web API 和應用程式，因此您不會看到任何要求同意的提示。如果您開發的應用程式僅供自己公司使用，通常會是這種情況。*

1. 登入 [Azure 管理入口網站](https://manage.windowsazure.com)。
2. 在左側導覽中按一下 Active Directory。
3. 按一下您要註冊範例應用程式的目錄租用戶。
4. 按一下 [應用程式] 索引標籤。
5. 在下拉式清單中，按一下 [新增]。
6. 按一下 [加入我的組織正在開發的應用程式]。
7. 輸入應用程式的易記名稱，例如 "TodoListClient-Android"，選取 [原生用戶端應用程式]，然後按 [下一步]。
8. 在 [重新導向 URI] 中，輸入 `http://TodoListClient`。按一下 [完成]。
9. 按一下應用程式的 [設定] 索引標籤。
10. 找出 [用戶端識別碼] 值，將它複製到別處，稍後設定應用程式時需要此值。
11. 在 [其他應用程式的權限] 中，按一下 [新增應用程式]。 在 [顯示] 下拉式清單中選取 [其他]，然後按一下上方的核取記號。找到並按一下 TodoListService，然後按一下底部的核取記號，以新增應用程式。從 [委派的權限] 下拉式清單中選取 [存取 TodoListService]，並儲存組態。



若要使用 Maven 來建置，您可以使用最上層的 pom.xml

  * 將此儲存機制複製到您選擇的目錄：

  `$ git clone git@github.com:AzureADSamples/NativeClient-Android.git`

  * 請依照 [Prerequests 一節的步驟，為 Android 設定您的 Maven](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android)
  * 安裝模擬器與 SDK 19
  * 移至您已複製儲存機制的根資料夾
  * 執行命令：mvn clean install
  * 將目錄切換至快速入門範例：cd samples\hello
  * 執行命令：mvn android:deploy android:run
  * 您應該會看到應用程式正在啟動
  * 輸入測試使用者認證來測試一下！

除了 aar 封裝，也會提交 jar 封裝。

### 步驟 4：下載 Android ADAL，並將它加入您的 XCode 工作區

我們提供多個選項，讓您輕鬆地在 Android 專案中使用此程式庫：

* 您可以使用原始程式碼將此程式庫匯入到 Eclipse，並連結至您的應用程式。
* 如果是使用 Android Studio，您可以使用 *aar* 封裝格式，並參考二進位檔。

####選項 1：原始檔 Zip

若要下載原始程式碼的複本，請按一下頁面右側的 [下載 ZIP]，或按一下[這裡](https://github.com/AzureAD/azure-activedirectory-library-for-android/archive/v1.0.9.tar.gz)。

####選項 2：透過 Git 取得原始檔

若要透過 git 取得 SDK 的原始程式碼，只要輸入：

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src

####選項 3：透過 Gradle 取得二進位檔

您可以從 Maven 中央儲存機制取得二進位檔。AAR 封裝可以在 AndroidStudio 中加入您的專案中，如下所示：

```gradle
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath.m2\repository"
    }
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
}
```

####選項 4：透過 Maven 取得 aar

如果您在 Eclipse 中使用 m2e 外掛程式，您可以在 pom.xml 檔案中指定相依性：

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>1.1.1</version>
    <type>aar</type>
</dependency>
```


####選項 5：libs 資料夾內的 jar 封裝
您可以從 maven 儲存機制取得 jar 檔案，並放入專案的 *libs* 資料夾中。您也需要將必要的資源複製到專案，因為 jar 封裝不包含它們。


### 步驟 5：將 Android ADAL 參考加入至您的專案


2. 在專案中加入參考，並指定為 Android 程式庫。如果您不確定怎麼做，[按一下這裡取得詳細資訊](http://developer.android.com/tools/projects/projects-eclipse.html)

3. 加入專案相依性以偵錯您的專案設定

4. 更新專案的 AndroidManifest.xml 檔案來包含：

    ```Java
      <uses-permission android:name="android.permission.INTERNET" />
      <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
      <application
            android:allowBackup="true"
            android:debuggable="true"
            android:icon="@drawable/ic_launcher"
            android:label="@string/app_name"
            android:theme="@style/AppTheme" >

            <activity
                android:name="com.microsoft.aad.adal.AuthenticationActivity"
                android:label="@string/title_login_hello_app" >
            </activity>
      ....
      <application/>
    ```

7. 在您的主要活動建立 AuthenticationContext 的執行個體。此呼叫的詳細資料超出此讀我檔案的範圍，但您可以查看 [Android 原生用戶端範例](https://github.com/AzureADSamples/NativeClient-Android)，由此開始也很不錯。以下是範例：

    ```Java
    // Authority is in the form of https://login.windows.net/yourtenant.onmicrosoft.com
    mContext = new AuthenticationContext(MainActivity.this, authority, true); // This will use SharedPreferences as            default cache
    ```
  * 注意：mContext 是您的活動中的欄位

8. 複製此程式碼區塊，以便於使用者輸入認證並收到授權碼之後處理 AuthenticationActivity 的結束工作：

    ```Java
     @Override
     protected void onActivityResult(int requestCode, int resultCode, Intent data) {
         super.onActivityResult(requestCode, resultCode, data);
         if (mContext != null) {
             mContext.onActivityResult(requestCode, resultCode, data);
         }
     }
    ```

9. 為了要求權杖，您定義回呼

    ```Java
    private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {

            @Override
            public void onError(Exception exc) {
                if (exc instanceof AuthenticationException) {
                    textViewStatus.setText("Cancelled");
                    Log.d(TAG, "Cancelled");
                } else {
                    textViewStatus.setText("Authentication error:" + exc.getMessage());
                    Log.d(TAG, "Authentication error:" + exc.getMessage());
                }
            }

            @Override
            public void onSuccess(AuthenticationResult result) {
                mResult = result;

                if (result == null || result.getAccessToken() == null
                        || result.getAccessToken().isEmpty()) {
                    textViewStatus.setText("Token is empty");
                    Log.d(TAG, "Token is empty");
                } else {
                    // request is successful
                    Log.d(TAG, "Status:" + result.getStatus() + " Expired:"
                            + result.getExpiresOn().toString());
                    textViewStatus.setText(PASSED);
                }
            }
        };
    ```
10. 最後，使用該回呼來要求權杖：

    ```Java
     mContext.acquireToken(MainActivity.this, resource, clientId, redirect, user_loginhint, PromptBehavior.Auto, "",
                    callback);
    ```

參數的說明：

  * Resource 是必要的，是您嘗試存取的資源。
  * Clientid 是必要的，來自 AzureAD 入口網站。
  * 您可以將 redirectUri 設為您的 packagename。這在 acquireToken 呼叫中不需要提供。
  * PromptBehavior 有助於要求認證時略過快取和 Cookie。
  * 授權碼兌換成權杖之後，就會呼叫 Callback。

  Callback 會有一個提供 accesstoken、過期日期和 idtoken 資訊的 AuthenticationResult 物件。

選擇性：**acquireTokenSilent**

您可以呼叫 **acquireTokenSilent** 來處理快取和權杖重新整理。它也提供同步處理版本。它接受 userid 做為參數。

    ```java
     mContext.acquireTokenSilent(resource, clientid, userId, callback );
    ```

11. **Broker**：Microsoft Intune 公司入口網站應用程式將提供 Broker 元件。如果有一個使用者帳戶在這個驗證器上建立，且開發人員選擇不要略過它，Adal 會使用 Broker 帳戶。開發人員可以使用下列方法來略過 Broker 使用者：

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```

 開發人員必須註冊特殊 redirectUri 供 Broker 使用。RedirectUri 的格式為 msauth://packagename/Base64UrlencodedSignature。您可以使用指令碼 "brokerRedirectPrint.ps1" 或使用 API call mContext.getBrokerRedirectUri，以取得應用程式的 redirecturi。簽章與您的簽署憑證有關。

 目前的 Broker 模型僅針對一位使用者。AuthenticationContext 提供 API 方法來取得 Broker 使用者。

 ```java
 String brokerAccount =  mContext.getBrokerUser();
 ``` 如果帳戶有效，則會傳回 Broker 使用者。

 您的應用程式資訊清單應該有使用 AccountManager 帳戶的權限：http://developer.android.com/reference/android/accounts/AccountManager.html

 * GET_ACCOUNTS
 * USE_CREDENTIALS
 * MANAGE_ACCOUNTS


經過這個逐步解說，您應該已擁有成功與 Azure Active Directory 整合所需的項目。如需此工作的更多範例，請瀏覽 GitHub 上的AzureADSamples / 儲存機制。

## 重要資訊

### 自訂

您的應用程式資源可能覆寫程式庫專案資源。建置您的應用程式時會發生這種情況。基於這個理由，您可以依想要的方式自訂驗證活動配置。您必須確定保留 ADAL 使用的控制項 (Web 檢視) 的 id。

### Broker

Broker 元件隨附於 Microsoft Intune 的公司入口網站應用程式。帳戶於帳戶管理員中建立。帳戶類型為 "com.microsoft.workaccount"。它只允許單一的 SSO 帳戶。完成應用程式的其中一個裝置挑戰之後，就會建立此使用者的 SSO Cookie。

### 授權單位 Url 和 ADFS

ADFS 不視為正式的 STS，因此您需要開啟執行個體探索，並在 AuthenticationContext 建構函式上傳遞 false。

授權單位 url 需要 STS 執行個體和租用戶名稱：https://login.windows.net/yourtenant.onmicrosoft.com

### 查詢快取項目

ADAL 在 SharedPrefrecens 中提供預設快取與一些簡單的快取查詢函式。您可以使用 ```Java
 ITokenCacheStore cache = mContext.getCache();
``` 從 AuthenticationContext 取得目前的快取。如果想要自訂，您也可以提供您的快取實作。```Java
mContext = new AuthenticationContext(MainActivity.this, authority, true, yourCache);
```

### PromptBehavior

ADAL 提供選項來指定提示行為。如果重新整理權杖無效，而且需要使用者認證，PromptBehavior.Auto 會快顯 UI。PromptBehavior.Always 會略過快取使用，並永遠顯示 UI。

### 從快取無訊息地要求權杖並重新整理

這個方法不會使用 UI 快顯，也不需要活動。它會從快取傳回權杖 (若有的話)。如果權杖已過期，則嘗試重新整理它。如果重新整理權杖已過期或失效，則傳回 AuthenticationException。

    ```Java
    Future<AuthenticationResult> result = mContext.acquireTokenSilent(resource, clientid, userId, callback );
    ```

您也可以使用此方法進行同步呼叫。您可以將 callback 設為 null，或使用 acquireTokenSilentSync。

### 診斷

診斷問題的主要資訊來源如下：

+ 例外狀況
+ 記錄檔
+ 網路追蹤

此外，請注意相互關聯識別碼在程式庫中是診斷的核心。如果您想要將 ADAL 要求與程式碼中其他的作業相互關聯，您可以依每一個要求來設定相互關聯識別碼。如果您沒有設定相互關聯識別碼，則 ADAL 會產生隨機相互關聯識別碼，所有記錄訊息和網路呼叫會標示相互關聯識別碼。自行產生的識別碼隨每個要求而不同。

#### 例外狀況

這顯然是第一個診斷。我們試著提供有用的錯誤訊息。如果您發現沒有幫助的錯誤訊息，請提出問題來告訴我們。請同時提供裝置資訊，例如機型和 SDK#。

#### 記錄檔

您可以設定程式庫來產生記錄訊息，用以協助診斷問題。設定記錄時，請執行下列呼叫來設定回呼，供 ADAL 用來移交每一個產生的記錄訊息。


 ```Java
 Logger.getInstance().setExternalLogger(new ILogger() {
     @Override
     public void Log(String tag, String message, String additionalMessage, LogLevel level, ADALError errorCode) {
      ...
      // You can write this to logfile depending on level or errorcode.
      writeToLogFile(getApplicationContext(), tag +":" + message + "-" + additionalMessage);
     }
 }
 ``` 訊息可以寫入自訂記錄檔，如下所示。不幸的是，從裝置取得記錄檔沒有標準方法。有一些服務可協助您處理這部份。您可以也自創方法，例如將檔案傳送到伺服器。

```Java
private syncronized void writeToLogFile(Context ctx, String msg) {
       File directory = ctx.getDir(ctx.getPackageName(), Context.MODE_PRIVATE);
       File logFile = new File(directory, "logfile");
       FileOutputStream outputStream = new FileOutputStream(logFile, true);
       OutputStreamWriter osw = new OutputStreamWriter(outputStream);
       osw.write(msg);
       osw.flush();
       osw.close();
}
```

##### 記錄層級

+ Error(例外狀況)
+ Warn(警告)
+ Info(參考)
+ Verbose(詳細資料)

設定記錄層級的方法如下：```Java
Logger.getInstance().setLogLevel(Logger.LogLevel.Verbose);
 ```

 所有記錄訊息除了傳送至任何自訂記錄檔回呼，也會傳送至 logcat。您可以將記錄傳送至檔案形式 logcat，如下所示：

 ```
  adb logcat > "C:\logmsg\logfile.txt"
 ``` adb 命令的其他範例：https://developer.android.com/tools/debugging/debugging-log.html#startingLogcat

#### 網路追蹤

您可以使用各種工具來擷取 ADAL 產生的 HTTP 流量。如果您熟悉 OAuth 通訊協定，或如果您需要提供診斷資訊給 Microsoft 或其他支援管道，這是最有用。

Fiddler 是最簡單的 HTTP 追蹤工具。請使用下列連結來設定它，以正確記錄 ADAL 網路流量。必須設定 fiddler 或任何其他工具 (例如 Charles) 來記錄未加密的 SSL 流量，才會更實用。注意：這種方式產生的追蹤可能包含極機密的資訊，例如存取權杖、使用者名稱和密碼。如果您使用實際執行帳戶，請勿將這些追蹤洩漏給第三方。如果您需要提供追蹤給某人以取得支援，請以您不介意共用使用者名稱和密碼的暫時帳戶來重現問題。

+ [設定適用於 Android 的 Fiddler](http://docs.telerik.com/fiddler/configure-fiddler/tasks/ConfigureForAndroid)
+ [設定適用於 ADAL 的 Fiddler 規則](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/How-to-listen-to-httpUrlConnection-in-Android-app-from-Fiddler)


### 對話方塊模式
沒有活動的 acquireToken 方法支援對話方塊提示。

### 加密

根據預設，ADAL 會加密權杖並儲存在 SharedPreferences 中。您可以在 StorageHelper 類別查看詳細資料。Android 引進 AndroidKeyStore 4.3(API18) 安全儲存體來存放私密金鑰。ADAL 對 API18 的和更新版本使用此機制。如果您想要使用較低 SDK 版本的 ADAL，您需要在 AuthenticationSettings.INSTANCE.setSecretKey 提供秘密金鑰

### Oauth2 持有者挑戰

AuthenticationParameters 類別提供從 Oauth2 持有者挑戰取得 authorization_uri 的功能。

### Web 檢視中的工作階段 Cookie

在應用程式關閉後，Android Web 檢視不會清除工作階段 Cookie。您可以使用以下範例程式碼來處理這部分：```java
CookieSyncManager.createInstance(getApplicationContext());
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.removeSessionCookie();
CookieSyncManager.getInstance().sync();
``` 深入了解 Cookie：http://developer.android.com/reference/android/webkit/CookieSyncManager.html

### 資源覆寫

ADAL 程式庫包含下列兩個 ProgressDialog 訊息英文字串。

如果需要當地語系化字串，您的應用程式應該覆寫這些英文字串。

```Java
<string name="app_loading">Loading...</string>
<string name="broker_processing">Broker is processing</string>
<string name="http_auth_dialog_username">Username</string>
<string name="http_auth_dialog_password">Password</string>
<string name="http_auth_dialog_title">Sign In</string>
<string name="http_auth_dialog_login">Login</string>
<string name="http_auth_dialog_cancel">Cancel</string>
```

=======

### NTLM 對話方塊
Adal 1.1.0 版支援 NTLM 對話方塊，此對話方塊是透過 WebViewClient 的 onReceivedHttpAuthRequest 事件來處理。您可以自訂對話方塊版面配置和字串。### 步驟 5：下載 iOS 原生用戶端範例程式碼
 

<!---HONumber=62-->