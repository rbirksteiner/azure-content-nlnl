<properties
	pageTitle="如何為您的應用程式服務應用程式設定 Facebook 驗證"
	description="了解如何為您的應用程式服務應用程式設定 Facebook 驗證。"
	services="app-service\mobile"
	documentationCenter=""
	authors="mattchenderson" 
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

# 如何設定應用程式以使用 Facebook 登入

本主題說明如何設定 Azure 應用程式服務，以使用 Facebook 做為驗證提供者。

若要完成本主題的程序，您必須具有已通過電子郵件地址與手機號碼驗證的 Facebook 帳戶。若要建立新的 Facebook 帳戶，請前往 [facebook.com]。

## <a name="register"> </a>向 Facebook 註冊您的應用程式

1. 瀏覽至 [Facebook 開發人員]網站，並以您的 Facebook 帳戶認證登入。

2. (選用) 按一下 [應用程式]，然後按一下 [Register as a Developer]，接受政策並遵循註冊步驟 (若您尚未註冊)。

3. 按一下 [應用程式]，然後按一下 [製作新應用程式]。

4. 選取 [**網站**] 做為您的平台。選擇應用程式的唯一名稱，然後按一下 [**建立新的 Facebook 應用程式識別碼**]。

5. 從下拉式清單中選取應用程式的類別。然後按一下 [**建立應用程式識別碼**]。

6. 在下一個頁面上，選取右上角的 [**略過快速入門**]。這會將您帶到應用程式的開發人員儀表板。

7. 在 [**應用程式密鑰**] 欄位中，按一下 [**顯示**]、在系統要求時提供您的密碼，然後記下 [**應用程式識別碼**] 和 [**應用程式密鑰**] 的值。

	> [AZURE.NOTE]**安全性注意事項** 用戶端密碼是重要的安全性認證。請勿與任何人共用此密碼，或在用戶端應用程式中加以散發。

8. 在左側的導覽列上，按一下 [**設定**]。在 [**應用程式網域**] 中輸入閘道的 URL，並輸入 [**連絡人電子郵件**]。

    ![][0]

9. 如果您未看見下方的網站區段，請按一下 [**新增平台**]，然後選取 [**網站**]。在 [**網站 URL**] 中輸入您應用程式服務閘道的 URL，然後按一下 [**儲存變更**]。

10. 按一下 [**進階**] 索引標籤，然後將您的重新導向 URI 新增至 [**有效的 OAuth 重新導向 URI**]。然後按一下 [**儲存變更**]。您的重新導向 URI 是附加路徑 _/signin-facebook_ 的閘道 URL。例如：`https://contosogateway.azurewebsites.net/signin-facebook`。請確實使用 HTTPS 配置。

11. 用來註冊應用程式的 Facebook 帳戶是應用程式的系統管理員。此時，只有系統管理員可以登入此應用程式。若要驗證其他 Facebook 帳戶，請按一下左側導覽列中的 [**狀態和檢閱**]。然後按一下 [是]，以啟用一般公用存取權。


## <a name="secrets"> </a>將 Facebook 資訊新增至行動應用程式


12. 登入 [[Azure 管理入口網站]]，然後導覽至您的應用程式服務閘道。

13. 在 [**設定**] 下選擇 [**身分識別**]，然後選取 [**Facebook**]。貼入您先前取得的應用程式識別碼和應用程式密鑰值。然後按一下 [儲存]。

    ![][1]

現在，您已可在應用程式中使用 Facebook 進行驗證。

## <a name="related-content"> </a>相關內容

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication-preview/app-service-facebook-dashboard.png
[1]: ./media/app-service-mobile-how-to-configure-facebook-authentication-preview/app-service-facebook-settings.png

<!-- URLs. -->
[Facebook 開發人員]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: http://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /zh-tw/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure 管理入口網站]: https://portal.azure.com/
 

<!---HONumber=62-->