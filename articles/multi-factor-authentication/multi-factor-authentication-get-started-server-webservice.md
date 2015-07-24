<properties 
	pageTitle="開始使用 MFA Server Mobile App Web 服務" 
	description="Azure Multi-Factor Authentication 應用程式提供額外的頻外驗證選項。它可以讓 MFA Server 將通知推播給使用者。" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="bryanla"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/02/2015" 
	ms.author="billmath"/>

# 開始使用 MFA Server Mobile App Web 服務

Azure Multi-Factor Authentication 應用程式提供額外的頻外驗證選項。Azure Multi-Factor Authentication 會將通知推送到使用者智慧型手機或平板電腦上的 Azure Multi-Factor Authentication 應用程式，取代在登入時撥打自動電話或傳送 SMS 給使用者。使用者只需要在應用程式中點選 [驗證] (或輸入 PIN 再點選 [驗證]) 即可登入。

若要使用 Azure Multi-Factor Authentication 應用程式，使用者必須滿足以下條件，應用程式才能與 Mobile App Web 服務成功通訊：

- 如需硬體和軟體需求，請參閱＜硬體和軟體需求＞
- 您必須使用 Azure Multi-Factor Authentication Server 6.0 或更高版本
- 必須將 Mobile App Web 服務安裝在執行 Microsoft® Internet Information Services (IIS) 6.x、IIS 7.x 的網際網路對向 Web 伺服器上。
- 在使用 IIS 6.x 時，請確定 ASP.NET v2.0.50727 已安裝、註冊並設定為 [已允許]
- 使用 IIS 7.x 時所需的角色服務包括 ASP.NET 和 IIS 6 Metabase 相容性
- Mobile App Web 服務必須可透過公用 URL 存取
- Mobile App Web 服務必須受到 SSL 憑證保護。
- 必須以 IIS 6.x、IIS 7.x 將 Azure Multi-Factor Authentication Web 服務 SDK 安裝在 Azure Multi-Factor Authentication Server 所安裝的伺服器上。
- Azure Multi-Factor Authentication Web 服務 SDK 必須受到 SSL 憑證保護。
- Mobile App Web 服務必須要能透過 SSL 連接 Azure Multi-Factor Authentication Web 服務 SDK。
- Mobile App Web 服務必須要能使用隸屬於 "Azure Multi-Factor Authentication Admins" 安全性群組之服務帳戶的認證驗證 Azure Multi-Factor Authentication Web 服務 SDK。如果 Azure Multi-Factor Authentication Server 在加入網域的伺服器上執行，此服務帳戶和群組也會存在於 Active Directory 中。如果伺服器未加入網域，此服務帳戶和群組會存在於 Azure Multi-Factor Authentication Server 伺服器本機。


若要將使用者入口網站安裝在 Azure Multi-Factor Authentication Server 以外的伺服器上，您必須進行以下三個步驟：

1. 安裝 Web 服務 SDK
2. 安裝 Mobile App Web 服務
3. 在 Azure Multi-Factor Authentication Server 中配置行動應用程式設定
4. 為使用者啟用 Azure Multi-Factor Authentication 應用程式

## 安裝 Web 服務 SDK

如果您尚未將 Azure Multi-Factor Authentication Web 服務 SDK 安裝在 Azure Multi-Factor Authentication Server 上，請移至該伺服器，然後開啟 Azure Multi-Factor Authentication Server。按一下 [Web 服務 SDK] 圖示，再按一下 [安裝 Web 服務 SDK...] 按鈕，然後遵循畫面呈現的指示。Web 服務 SDK 必須受到 SSL 憑證保護。自我簽署憑證適用於此目的，不過您必須將它匯入使用者入口網站 Web 伺服器上本機電腦帳戶的「信任的根憑證授權」存放區，這樣它才會在起始 SSL 連接時信任該憑證。

<center>![Setup](./media/multi-factor-authentication-get-started-server-webservice/sdk.png)</center>

## 安裝 Mobile App Web 服務
在安裝 Mobile App Web 服務之前，請注意下列各項：

- 如果您已將 Azure Multi-Factor Authentication 使用者入口網站安裝在網際網路對向伺服器上，可以從使用者入口網站的 web.config 檔案將使用者名稱、密碼及 URL 複製到 Web 服務 SDK。 
- 在網際網路對向 Web 伺服器上開啟網頁瀏覽器，並瀏覽至輸入 web.config 檔案中的 Web 服務 SDK URL，如此將有所幫助。如果瀏覽器可以順利連接 Web 服務，它應該會提示您輸入認證。輸入使用者名稱和密碼 (與輸入 web.config 檔案中的使用者名稱和密碼完全相同)。確定未出現任何憑證警告或錯誤。
- 如果反向 Proxy 或防火牆座落於 Mobile App Web 服務 Web 伺服器之前，而且正在執行 SSL 卸載，您可以編輯 Mobile App Web 服務的 web.config 檔案，並將下列機碼新增至 <appSettings> 區段，讓 Mobile App Web 服務可以使用 http，而不是 https。不過，從 Mobile App 到防火牆/反向 Proxy 仍然需要 SSL。 <add key="SSL_REQUIRED" value="false"/> 

### 安裝 Mobile App Web 服務

<ol>
<li>在 Azure Multi-Factor Authentication Server 伺服器上開啟 Windows 檔案總管，接著瀏覽至安裝 Azure Multi-Factor Authentication Server 的資料夾 (如 C:\Program Files\Azure Multi-Factor Authentication)。視需要針對要安裝 Mobile App Web 服務的伺服器選擇 32 位元或 64 位元版本的 Azure Multi-Factor AuthenticationPhoneAppWebServiceSetup 安裝檔案。將安裝檔案複製到網際網路對向伺服器。</li>

<li>在網際網路對向 Web 伺服器上，您必須以系統管理員權限執行安裝程式檔案。若要這樣做，最簡單的方式是以系統管理員身分開啟命令提示字元，再瀏覽至複製安裝檔案的位置。</li>

<li>執行 Multi-factor AuthenticationMobileAppWebServiceSetup 安裝檔案，視需要變更網站，再將虛擬目錄變更為簡短的名稱 (如 "PA")。在啟用期間，由於使用者必須將 Mobile App Web 服務 URL 輸入行動裝置，因此我們建議使用簡短的虛擬目錄名稱。</li>

<li>Azure Multi-Factor AuthenticationMobileAppWebServiceSetup 安裝完成之後，瀏覽至 C:\inetpub\wwwroot\PA (或根據虛擬目錄名稱瀏覽至適當目錄) 並編輯 web.config 檔案。</li>

<li>找出 WEB_SERVICE_SDK_AUTHENTICATION_USERNAME 和 WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD 機碼，並將值設定為隸屬於 PhoneFactor Admins 安全性群組之服務帳戶的使用者名稱和密碼 (請參閱前文的＜需求＞一節)。如果您之前已安裝 Azure Multi-Factor Authentication 使用者入口網站，這可能是當做該使用者入口網站之身分識別的帳戶。請務必將使用者名稱和密碼輸入行尾的引號之間 (value=””/>)。建議您使用合格的使用者名稱 (如 domain\username 或 machine\username)。</li>

<li>找出  pfMobile App Web Service_pfwssdk_PfWsSdk 設定，並將值從 “http://localhost:4898/PfWsSdk.asmx” 變更為在 Azure Multi-Factor Authentication Server 伺服器上執行之 Web 服務 SDK 的 URL (如 https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx)。由於此連接使用 SSL，所以您必須依據伺服器名稱參考 Web 服務 SDK，而不是 IP 位址，因為 SSL 憑證是針對伺服器名稱發行，所以使用的 URL 必須與憑證上的名稱相符。如果伺服器名稱無法解析為網際網路對向伺服器的 IP 位址，請在該伺服器上的主機檔案加入項目，讓 Azure Multi-Factor Authentication Server 伺服器的名稱能與其 IP 位址相對應。完成變更後，儲存 web.config 檔案。</li>

<li>如果安裝 Mobile App Web 服務的網站 (如預設網站) 尚未與公開簽署的憑證繫結，請在伺服器上安裝憑證 (如果尚未安裝)，開啟 IIS 管理員，然後將憑證繫結至網站。</li>

<li>從任何電腦開啟網頁瀏覽器，並瀏覽至安裝 Mobile App Web 服務的 URL (如 https://www.publicwebsite.com/PA)。確定未出現任何憑證警告或錯誤。</li>

### 在 Azure Multi-Factor Authentication Server 中配置行動應用程式設定
既然 Mobile App Web 服務已安裝完成，您需要設定 Azure Multi-Factor Authentication Server，使其與入口網站搭配運作。

#### 在 Azure Multi-Factor Authentication Server 中配置行動應用程式設定

1. 在 Azure Multi-Factor Authentication Server 中，按一下 [使用者入口網站] 圖示。如果您允許使用者控制其驗證方法，請在 [設定] 索引標籤之 [允許使用者選取方法] 下方勾選 [行動應用程式]。若未啟用這個功能，使用者就必須連絡技術支援人員來完成行動應用程式啟用。
2. 勾選 [允許使用者啟用行動應用程式] 方塊。
3. 勾選 [允許使用者註冊] 方塊。
4. 按一下 [行動應用程式] 圖示。
5. 輸入安裝 Azure Multi-Factor AuthenticationMobileAppWebServiceSetup 時建立之虛擬目錄搭配使用的 URL。您可以在提供的空間內輸入帳戶名稱。此處的公司名稱會顯示在行動應用程式中。如果保留空白，系統會改為顯示在 Azure 管理入口網站中建立的 Multi-Factor Auth Provider 名稱。 



<center>![Setup](./media/multi-factor-authentication-get-started-server-webservice/mobile.png)</center>

<!---HONumber=July15_HO2-->