<properties 
	pageTitle="如何為您的應用程式服務應用程式設定 Azure Active Directory 驗證" 
	description="了解如何為您的應用程式服務應用程式設定 Azure Active Directory 驗證。" 
	authors="mattchenderson" 
	services="app-service\mobile" 
	documentationCenter="" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="05/26/2015" 
	ms.author="mahender"/>

# 如何設定您的應用程式以使用 Azure Active Directory 登入

本主題說明如何設定 Azure 應用程式服務，以使用 Azure Active Directory 做為驗證提供者。

## <a name="register"> </a>向 Azure Active Directory 註冊您的應用程式

1. 登入 [Azure 管理入口網站預覽]，然後導覽至您的應用程式服務閘道。

2. 在 [**設定**] 下選擇 [ **身分識別**]，然後選取 [**Azure Active Directory**]。複製 [**APP URL**]。請確實使用 HTTPS 配置。

    ![][1]

3. 登入 [Azure 管理入口網站]，然後導覽至 [**Active Directory**]。

    ![][2]

4. 選取您的目錄，然後選取頂端的 [**應用程式**] 索引標籤。按一下底部的 [**新增**]，以建立新的應用程式註冊。

5. 按一下 [Add an application my organization is developing]。

6. 在 [新增應用程式精靈] 中，輸入應用程式的 [名稱]，然後按一下 [Web Application And/Or Web API] 類型。接著，按一下以繼續。

7. 在 [**登入 URL**] 方塊中，貼上您從閘道的 Active Directory 身分識別提供者設定中複製的應用程式識別碼。在 [應用程式識別碼 URI] 方塊中，輸入相同的唯一資源識別碼。接著，按一下以繼續。

8. 新增應用程式之後，按一下 [設定] 索引標籤。編輯 [**單一登入**] 下的 [**回覆 URL**]，使其成為附加路徑 _/signin-aad_ 的閘道 URL。例如：`https://contosogateway.azurewebsites.net/signin-aad`。請確實使用 HTTPS 配置。

    ![][3]

9. 按一下 [儲存]。然後，複製應用程式的 [**用戶端識別碼**]。

## <a name="secrets"> </a>將 Azure Active Directory 資訊新增至行動應用程式

10. 返回預覽管理入口網站，和閘道的 [**使用者驗證**] 分頁。貼入 Azure Active Directory 身分識別提供者的 [**用戶端識別碼**] 設定。
  
11. 在 [允許的租用戶] 清單中，需要新增您用來註冊應用程式之目錄的網域 (例如 contoso.onmicrosoft.com)。若要尋找您的預設網域名稱，可以按一下 Azure Active Directory 租用戶上的 [**網域**] 索引標籤。將您的網域名稱新增至[允許的租用戶] 清單，然後按一下 [儲存]。

現在，您已可在應用程式中使用 Azure Active Directory 進行驗證。

## <a name="related-content"> </a>相關內容

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

使用 Azure Active Directory 單一登入驗證行動應用程式的使用者：[iOS][ios-adal]

<!-- Images. -->

[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication-preview/app-service-aad-settings.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication-preview/app-service-navigate-aad.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication-preview/app-service-aad-app-configure.png

<!-- URLs. -->

[Azure 管理入口網站預覽]: https://portal.azure.com/
[Azure 管理入口網站]: https://manage.windowsazure.com/
[ios-adal]: ../app-service-mobile-dotnet-backend-xamarin-ios-aad-sso-preview.md
 

<!---HONumber=62-->