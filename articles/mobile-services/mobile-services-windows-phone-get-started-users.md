<properties 
	pageTitle="開始使用驗證 (Windows Phone) | 行動開發人員中心" 
	description="了解如何使用行動服務透過眾多識別提供者驗證 Windows Phone 應用程式使用者，包括 Google、Facebook、Twitter 和 Microsoft。" 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/01/2015" 
	ms.author="glenga"/>

# 在您的行動服務應用程式中新增驗證

[AZURE.INCLUDE [mobile-services-selector-get-started-users-legacy](../../includes/mobile-services-selector-get-started-users-legacy.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>本主題顯示如何在 Azure 行動服務中從應用程式驗證使用者。在本教學課程中，您將使用行動服務支援的身分識別提供者，將驗證加入至快速入門專案。使用者 ID 值在成功經過行動服務的驗證和授權後，就會顯示出來。</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=298631" target="_blank" class="label">觀賞教學課程</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-get-started-authentication-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=298631" target="_blank" class="dev-onpage-video"><span class="icon">播放視訊</span></a> <span class="time">10:50</span></div>
</div>

本教學課程帶領您執行下列基本步驟，在您的應用程式中啟用驗證：

1. [註冊應用程式進行驗證，並設定行動服務]
2. [限制只有經驗證的使用者具有資料表的權限]
3. [將驗證新增至應用程式]

本教學課程會以行動服務快速入門為基礎。您還必須先完成[將行動服務新增至現有的應用程式]教學課程。

>[AZURE.NOTE]本教學課程示範行動服務如何管理不同的身分識別提供者的驗證流程。此方法設定起來很簡單，而且可支援多個提供者。若要改用可於用戶端管理驗證的 Live Connect，並在您的 Windows Phone 應用程式中提供單一登入體驗，請參閱[使用 Live Connect 在 Windows Phone 應用程式提供單一登入功能]主題。藉由使用用戶端管理的驗證，您的應用程式即可存取身分識別提供者保留的其他使用者資料。您可藉由呼叫伺服器指令碼中的 **user.getIdentities()** 函數，在行動服務中取得相同的使用者資料。如需詳細資訊，請參閱 [這篇文章](http://go.microsoft.com/fwlink/p/?LinkId=506605)。

##<a name="register"></a>註冊應用程式進行驗證，並設定行動服務


[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]


##<a name="permissions"></a>限制只有通過驗證的使用者具有權限


[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../../includes/mobile-services-restrict-permissions-javascript-backend.md)]


<ol start="3">
<li>在 Visual Studio 2012 Express for Windows Phone 中，開啟您在完成<strong>開始使用行動服務</strong>教學課程時所建立的專案。</li>

<li>按 F5 鍵執行此快速入門型應用程式；確認在應用程式啟動後，發生狀態代碼 401 (未經授權) 的未處理例外狀況。這是因為應用程式嘗試以未驗證的使用者身分來存取行動服務，但 <em>TodoItem</em> 資料表現在需要驗證。</li></ol>

接下來，您要將應用程式更新為在要求行動服務的資源之前必須驗證使用者。

##<a name="add-authentication"></a>將驗證新增至應用程式

[AZURE.INCLUDE [mobile-services-windows-phone-authenticate-app](../../includes/mobile-services-windows-phone-authenticate-app.md)]

##<a name="tokens"></a>將授權權杖儲存在用戶端上

[AZURE.INCLUDE [mobile-services-windows-phone-authenticate-app-with-token](../../includes/mobile-services-windows-phone-authenticate-app-with-token.md)]

## <a name="next-steps"> </a>後續步驟

在下一個[行動服務使用者的伺服器端授權](mobile-services-javascript-backend-service-side-authorization.md)教學課程中，您將使用由行動服務根據經驗證的使用者而提供的使用者識別碼值，來篩選行動服務傳回的資料。

<!-- Anchors. -->
[註冊應用程式進行驗證，並設定行動服務]: #register
[限制只有經驗證的使用者具有資料表的權限]: #permissions
[將驗證新增至應用程式]: #add-authentication
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/mobile-services-wp8-get-started-users/mobile-services-selection.png
[2]: ./media/mobile-services-wp8-get-started-users/mobile-service-uri.png
[3]: ./media/mobile-services-wp8-get-started-users/mobile-identity-tab.png
[4]: ./media/mobile-services-wp8-get-started-users/mobile-portal-data-tables.png
[5]: ./media/mobile-services-wp8-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[將行動服務新增至現有的應用程式]: mobile-services-windows-phone-get-started-data.md
[Authorize users with scripts]: ../mobile-services-windows-phone-authorize-users-in-scripts.md
[Azure Management Portal]: https://manage.windowsazure.com/
[使用 Live Connect 在 Windows Phone 應用程式提供單一登入功能]: mobile-services-windows-phone-single-sign-on.md
 

<!---HONumber=July15_HO2-->