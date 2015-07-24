<properties 
	pageTitle="Azure App Service API Apps 和行動應用程式中的驗證" 
	description="了解如何在 Azure 應用程式服務中針對 API 應用程式和行動應用程式設定和使用驗證" 
	services="app-service" 
	documentationCenter="" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/29/2015" 
	ms.author="tdykstra"/>

# 在 Azure 應用程式服務中驗證 API 應用程式和行動應用程式

## 概觀

本文說明 [API 應用程式](../app-service-api/app-service-api-apps-why-best-platform.md)和[行動應用程式](../app-service-mobile/app-service-mobile-value-prop-preview.md)的內建驗證功能。

本文結尾處的 [[後續步驟](#next-steps)] 一節提供相關作法文件的連結。

## Azure 應用程式服務閘道

Azure 應用程式服務提供內建的驗證服務，可實作 [OAuth 2.0](#oauth) 和 [OpenID Connect](#oauth) 並使用多個*身分識別提供者*。身分識別提供者是受到 Azure 應用程式服務信任的外部服務，可驗證您的應用程式使用者。應用程式服務支援最熱門的身分識別提供者：

* Azure Active Directory
* Microsoft 帳戶
* Google
* Twitter
* Facebook

### 閘道架構

含有 API 應用程式或行動應用程式的 Azure 資源群組都會包含*閘道*。閘道是在 Web 應用程式中執行的 Azure 資源，可處理各項系統管理工作，包括在資源群組中驗證 API 應用程式和行動應用程式。

閘道可處理登入程序、管理權杖，並防止未驗證的呼叫到達[設定為需要驗證存取](../app-service-api/app-service-api-dotnet-add-authentication.md#protect-the-api-app)的 API 應用程式。閘道可以控制 API 應用程式的存取，因為資源群組中目的地為 API 應用程式的所有內送 HTTP 要求都會透過閘道來傳送。

下圖說明這些閘道功能。

![](./media/app-service-authentication-overview/gateway.png)

### 閘道功能

閘道驗證服務提供多項勝過執行自有 OAuth 2.0 實作的好處：

* Microsoft 提供 SDK 讓您使用簡化的語法執行驗證和授權工作。

* 因為應用程式服務會處理更多驗證工作，因此所需的開發和測試時間最少，並可避免實作 OAuth 提供者時所進行之變更的大部分或所有影響。

* 如果您需要保護多個應用程式因此將它們放在同一個資源群組中，您只需要一個用戶端識別碼和用戶端密碼即可用於每個驗證提供者，因為閘道只有一個重新導向 URL。

* 監視和疑難排解作業變得更加容易，因為您可以藉由監視閘道來監視整個資源群組的驗證相關流量。

* 偵錯變得更加容易，因為您可以設定程式在本機以偵錯模式執行時使用閘道，而不需要在身分識別提供者帳戶中變更重新導向 URL。

## 伺服器流程和用戶端流程

應用程式服務閘道提供兩種方式來驗證用戶端：*用戶端流程*和*伺服器流程*。在這兩種流程中，用戶端應用程式會將使用者認證 (一般來說是使用者名稱和密碼) 直接傳送給身分識別提供者。不管是哪一種流程，閘道和應用程式都不會收到使用者認證。

### 用戶端流程

用戶端流程表示用戶端應用程式會直接與身分識別提供者通訊來取得提供者的存取權杖。用戶端應用程式將提供者的存取權杖傳送至閘道。閘道建立使用者內容權杖並傳送給用戶端。這個使用者內容權杖亦稱為 Zumo 權杖，此名稱的由來是 [Azure 行動服務](/documentation/services/mobile-services/)的原始程式碼名稱 (API 應用程式和行動應用程式的驗證服務是建置在最初針對行動服務所開發的同一個架構上)。

下圖說明此流程。

![](./media/app-service-authentication-overview/clientflow.png)

用戶端接著會在呼叫受保護的 API 應用程式或行動應用程式時在 HTTP 要求中提供 Zumo 權杖。閘道會儲存提供者權杖，並記錄 Zumo 權杖所關聯的提供者權杖。

### 伺服器流程

伺服器流程表示用戶端應用程式依賴閘道來與起始登入的身分識別提供者通訊。用戶端瀏覽器會前往閘道 URL，閘道則會將要求重新導向至身分識別提供者的登入頁面。使用者登入之後，閘道會取得身分識別提供者的權杖、建立 Zumo 權杖，並將 Zumo 權杖傳送給用戶端。

![](./media/app-service-authentication-overview/serverflow.png)

用戶端應用程式和受保護的 API 應用程式或行動應用程式之間的後續互動，其處理方式與用戶端流程相同：用戶端在 HTTP 要求中提供 Zumo 權杖。

### 該選擇用戶端流程還是伺服器流程

如果您想要使用的身分識別提供者具有您想要支援之用戶端平台的 SDK，通常最好選擇用戶端流程。用戶端流程能提供最好的使用者體驗，因為它可以減少使用者必須輸入認證的次數。例如，如果您的使用者有 android 裝置，他們可能會有與裝置相關聯的 Google 帳戶，因此如果可以使用該帳戶，而不必重新輸入使用者名稱和密碼，就能提升使用者體驗。Android、iOS 或 Windows Phone 裝置中的 Facebook 帳戶或 Windows 桌面或 Windows Phone 上的 Microsoft 帳戶也是如此。

在其他情況下，伺服器流程就可能是較好的選擇：

- 您想要支援的身分識別提供者和用戶端平台沒有原生用戶端 SDK。

- 您想要讓某項服務快速進入實際執行環境，之後再於時間允許時改善使用者體驗。伺服器流程可讓 Azure 應用程式服務執行更多驗證工作，將所需的開發和測試量降至最低。

## 代表他人撥出電話至 SaaS 平台

您可以撰寫程式碼，代表已登入的使用者撥出電話至軟體即服務 (SaaS) 平台，或者您可以使用[連接器 API 應用程式](../app-service-mobile/app-service-logic-what-are-biztalk-api-apps.md)。例如，若要從使用者的 Twitter 帳戶發表推文，您可以使用 [Twitter SDK](https://dev.twitter.com/overview/api/twitter-libraries)，或在 Azure 訂用帳戶中佈建 [Twitter 連接器](../app-service-mobile/app-service-logic-connector-twitter.md)並加以呼叫。本節內容是關於從 API 應用程式或行動應用程式中執行的程式碼存取 SaaS 平台。

### 使用身分識別提供者權杖 

閘道會維護*權杖存放區*，在其中將 Zumo 權杖關聯到一或多個身分識別提供者存取權杖並重新整理權杖。當收到具有有效 Zumo 權杖的 HTTP 要求時，閘道會知道屬於該使用者的身分識別提供者權杖。
  
當 API 應用程式或行動應用程式中執行的程式碼需要代表已登入的使用者呼叫受保護的資源時，即可從閘道的權杖存放區擷取並使用身分識別提供者的權杖，如下圖所示。

![](./media/app-service-authentication-overview/idprovidertoken.png)

例如，假設身分識別提供者是 Azure Active Directory (AAD)，而且您的 API 應用程式想要使用 AAD 存取權杖呼叫 AAD 圖形 API，或要求存取使用者擁有權限的 SharePoint 網站。您可以將要求傳送至閘道以擷取 AAD 權杖，然後使用 AAD 權杖呼叫圖形 API，或取得 SharePoint 網站的存取權杖。

### 取得使用者同意以存取其他資源

閘道也有內建功能可用來取得使用者同意，以便您可以存取並非原始身分識別提供者的提供者所保護的資源。比方說，針對使用 Azure Active Directory 登入的使用者，您可能想要存取使用者的 Dropbox 帳戶中的檔案。

應用程式服務閘道包含內建支援，可用來取得使用者同意，以接受來自下列提供者的這類存取：

* Box
* DropBox
* Facebook
* Google
* Office365
* OneDrive
* QuickBooks
* Salesforce
* SharePointOnline
* Twitter
* Yammer

對於這些提供者，閘道會維護存取權杖，並將這些權杖關聯到 Zumo 權杖，就和它針對身分識別提供者存取權杖所執行的動作一樣。取得使用者同意和呼叫 SaaS 平台的程序如下圖所示。

![](./media/app-service-authentication-overview/saastoken.png)

應用程式服務執行階段支援和 SDK 讓撰寫程式碼來存取這些提供者之一所保護的資源變得相對容易。圖中有一個初步步驟並未顯示出來：您必須對提供者設定帳戶，並在 Azure 應用程式服務中設定提供者的用戶端識別碼和用戶端密碼設定。

針對上述和其他許多提供者，您也可以使用預先封裝的[連接器 API 應用程式](../app-service-mobile/app-service-logic-what-are-biztalk-api-apps.md)存取受保護的資源。

## SDK 可用性

針對 API 應用程式，適用於 .NET 的 SDK 可提供驗證功能：

* [Microsoft.Azure.AppService](http://www.nuget.org/packages/Microsoft.Azure.AppService) - 可供用於 API 應用程式用戶端。  
* [Microsoft.Azure.AppService.ApiApps.Service](http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service/) - 可供用於 API 應用程式中執行的 Web API 專案。
 
此外，Visual Studio 可以自動產生程式碼來使用適用於 .NET 的 SDK，進一步簡化您撰寫來呼叫 API 應用程式的程式碼。如需詳細資訊，請參閱[從 .NET 用戶端使用 Azure 應用程式服務中的 API 應用程式](../app-service-api/app-service-api-dotnet-consume.md)。

針對行動應用程式，SDK 適用於下列平台：

- [.NET 伺服器](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) 
- [iOS](../app-service-mobile/app-service-mobile-dotnet-backend-ios-get-started-preview.md)
- [Xamarin iOS](../app-service-mobile/app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview.md)
- [Xamarin Android](../app-service-mobile/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview.md)
- [Windows](../app-service-mobile/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview.md)
- JavaScript (開發過程中輔助之用)

## 替代的驗證方法

如果閘道驗證服務不符合您應用程式的需求，您可以自行處理驗證，或使用 Azure API 管理服務。

### <a id="doityourself"></a>自行驗證

您可以在 Azure 上執行驗證架構，例如 [ASP.NET 身分識別](http://www.asp.net/identity)或 [Thinktecture](http://www.thinktecture.com/identityAndAccessControl)。這麼做可讓您控制所有元件的運作方式，但是您也必須花更多時間開發和測試驗證功能。此外，如果您有多個應用程式需要使用多個重新導向 URL 來進行保護，您必須針對協力廠商驗證提供者 (例如 Facebook、Google 和 Twitter) 設定多個用戶端識別碼和用戶端密碼。

目前應用程式服務並不支援在閘道驗證使用自行驗證解決方案，但在[行動服務](mobile-services-dotnet-backend-get-started-custom-authentication.md)則可以支援。

### <a id="apim"></a>Azure API 管理

如果您有想要使用驗證來提供保護的現有 API，您可以在 Azure API 管理服務中這麼做。如需對 API 應用程式使用 API 管理的相關資訊，請參閱 Panos Kefalidis 的這篇部落格文章：[利用 API 應用程式的 API 管理](http://www.kefalidis.me/2015/06/taking-advantage-of-api-management-for-api-apps/)

## 後續步驟

本文說明了 Azure 應用程式服務針對 API 應用程式和行動應用程式所提供的驗證服務。以下是用來了解基礎驗證通訊協定之資源以及如何使用應用程式服務驗證功能之相關文件的一些連結。

* [OAuth 2.0、OpenID Connect 和 JSON Web 權杖 (JWT)](#oauth)
* API 應用程式資源
	* [API 應用程式用戶端流程](#apiaclient)
	* [API 應用程式伺服器流程](#apiaserver)
	* [API 應用程式代表呼叫](#apiaobo)
* 行動應用程式資源
	* [行動應用程式用戶端流程](#maclient)
	* [行動應用程式伺服器流程](#maserver)
	* [行動應用程式代表呼叫](#maobo)

### <a id="oauth"></a>OAuth 2.0、OpenID Connect 和 JSON Web 權杖 (JWT)

* [開始使用 OAuth 2.0](http://shop.oreilly.com/product/0636920021810.do "開始使用 OAuth 2.0") 
* [OAuth2、OpenID Connect 和 JSON Web 權杖 (JWT) 簡介 - PluralSight 課程](http://www.pluralsight.com/courses/oauth2-json-web-tokens-openid-connect-introduction) 
* [在 ASP.NET 中建置和保護多個用戶端的 RESTful API - PluralSight 課程](http://www.pluralsight.com/courses/building-securing-restful-api-aspdotnet)

### <a id="apiaclient"></a>API 應用程式用戶端流程

* [保護 API 應用程式](../app-service-api/app-service-api-dotnet-add-authentication.md) - API 應用程式設定組件適用於用戶端和伺服器流程，但 test-in-browser 組件說明伺服器流程。

### <a id="apiaserver"></a>API 應用程式伺服器流程

* [保護 API 應用程式](../app-service-api/app-service-api-dotnet-add-authentication.md) - API 應用程式設定組件適用於用戶端和伺服器流程，但 test-in-browser 組件說明伺服器流程。
* [從 .NET 用戶端使用 Azure 應用程式服務中的 API 應用程式](../app-service-api/app-service-api-dotnet-consume.md) - 已驗證呼叫的範例程式碼說明伺服器流程。 

### <a id="apiaobo"></a>API 應用程式代表呼叫受保護的資源

* [在 Azure 應用程式服務中部署和設定 SaaS 連接器 API 應用程式](../app-service-api/app-service-api-connnect-your-app-to-saas-connector.md) - 說明如何佈建預先封裝的連接器 API 應用程式、加以設定，並使用瀏覽器工具進行呼叫。
* 示範如何撰寫自有連接器 (也就是佈建和設定自訂 API 應用程式來代表呼叫受保護的資源) 的教學課程仍在開發中。

### <a id="maclient"></a>行動應用程式用戶端流程

* [將 Azure Active Directory 單一登入新增至您的 iOS 應用程式](../app-service-mobile/app-service-mobile-dotnet-backend-ios-aad-sso-preview.md)

### <a id="maserver"></a>行動應用程式伺服器流程

* [將驗證新增至您的 iOS 應用程式](../app-service-mobile/app-service-mobile-dotnet-backend-ios-get-started-users-preview.md)
* [將驗證新增至 Xamarin.iOS 應用程式](../app-service-mobile/app-service-mobile-dotnet-backend-xamarin-ios-get-started-users-preview.md)
* [將驗證新增至 Xamarin.Android 應用程式](../app-service-mobile/app-service-mobile-dotnet-backend-xamarin-android-get-started-users-preview.md)
* [將驗證新增至您的 Windows 應用程式](../app-service-mobile/app-service-mobile-dotnet-backend-windows-store-get-started-users-preview.md)

### <a id="maobo"></a>行動應用程式代表呼叫受保護的資源

* [在行動應用程式中取得存取權杖和呼叫 SharePoint API](../app-service-mobile/app-service-mobile-dotnet-backend-get-started-connect-to-enterprise.md#obtain-token)

<!---HONumber=62-->