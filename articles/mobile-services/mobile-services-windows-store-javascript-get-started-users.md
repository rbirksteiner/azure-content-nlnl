<properties 
	pageTitle="開始使用驗證 (JavaScript) | 行動開發人員中心" 
	description="了解如何使用行動服務透過眾多識別提供者驗證 Windows 市集 JavaScript 應用程式使用者，包括 Google、Facebook、Twitter 和 Microsoft。" 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="glenga"/>

# 在您的行動服務應用程式中新增驗證

[AZURE.INCLUDE [mobile-services-selector-get-started-users-legacy](../../includes/mobile-services-selector-get-started-users-legacy.md)]

本主題顯示如何在 Azure 行動服務中從應用程式驗證使用者。在本教學課程中，您將使用行動服務支援的身分識別提供者，將驗證加入至快速入門專案。由行動服務成功驗證並授權之後，就會顯示使用者識別碼值。

本教學課程帶領您執行下列基本步驟，在您的應用程式中啟用驗證：

1. [註冊應用程式進行驗證，並設定行動服務]
2. [限制只有經驗證的使用者具有資料表的權限]
3. [將驗證新增至應用程式]
4. [將驗證權杖儲存在用戶端]

本教學課程會以行動服務快速入門為基礎。您還必須先完成教學課程[開始使用行動服務]。

>[AZURE.NOTE]本教學課程示範行動服務如何管理不同的身分識別提供者的驗證流程。此方法設定起來很簡單，而且可支援多個提供者。若要改用 Live Connect 與用戶端管理的驗證，並在您的 Windows Phone 應用程式中提供單一登入體驗，請參閱[使用 Live Connect 在 Windows 市集應用程式提供單一登入功能]主題。透過用戶端管理的驗證，您的應用程式就能存取身分識別提供者所維護的其他使用者資料。您可藉由呼叫伺服器指令碼中的 **user.getIdentities()** 函數，在行動服務中取得相同的使用者資料。如需詳細資訊，請參閱 [這篇文章](http://go.microsoft.com/fwlink/p/?LinkId=506605)。

##<a name="register"></a>註冊應用程式進行驗證，並設定行動服務

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

<ol start="5">
<li><p>(選用) 完成<a href="/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/">註冊 Windows 市集應用程式套件以採用 Microsoft 驗證</a>中的步驟。</p>

    
	<p>請注意，此步驟僅適用於「Microsoft 帳戶」登入提供者，所以是選用步驟。向行動服務註冊 Windows 市集應用程式封裝資訊之後，用戶端就能夠重複使用 Microsoft 帳戶登入認證來享受單一登入的方便性。如果您沒有執行此動作，Microsoft 帳戶登入使用者會在每次呼叫登入方法時j都會看到登入提示。若您打算使用「Microsoft 帳戶」身分識別提供者，請完成這個步驟。</p>
</li>
</ol>
現在，行動服務和應用程式已設定為使用您選擇的驗證提供者。

##<a name="permissions"></a>限制只有通過驗證的使用者具有權限

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../../includes/mobile-services-restrict-permissions-javascript-backend.md)]

<ol start="3">
<li><p>在 Visual Studio 2012 Express for Windows 8 中，開啟您在完成<a href="/develop/mobile/tutorials/get-started/">開始使用行動服務</a>教學課程時所建立的專案。</p></li> 
<li><p>按 F5 鍵執行此快速入門型應用程式；確認在應用程式啟動後，發生狀態代碼 401 (未經授權) 的未處理例外狀況。</p>
   
   	<p>這是因為應用程式嘗試以未驗證的使用者身分來存取行動服務，但 <em>TodoItem</em> 資料表現在需要驗證。</p></li>
</ol>

接下來，您要將應用程式更新為在要求行動服務的資源之前必須驗證使用者。

##<a name="add-authentication"></a>將驗證新增至應用程式

[AZURE.INCLUDE [mobile-services-windows-store-javascript-authenticate-app](../../includes/mobile-services-windows-store-javascript-authenticate-app.md)]

##<a name="tokens"></a>將授權權杖儲存在用戶端上

[AZURE.INCLUDE [mobile-services-windows-store-javascript-authenticate-app-with-token](../../includes/mobile-services-windows-store-javascript-authenticate-app-with-token.md)]

## <a name="next-steps"> </a>後續步驟

在下一個[行動服務使用者的伺服器端授權][Authorize users with scripts]教學課程中，您將使用由行動服務根據經驗證的使用者而提供的使用者識別碼值，來篩選行動服務傳回的資料。


<!-- Anchors. -->
[註冊應用程式進行驗證，並設定行動服務]: #register
[限制只有經驗證的使用者具有資料表的權限]: #permissions
[將驗證新增至應用程式]: #add-authentication
[將驗證權杖儲存在用戶端]: #tokens
[Next Steps]: #next-steps


<!-- URLs. -->
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[使用 Live Connect 在 Windows 市集應用程式提供單一登入功能]: mobile-services-windows-store-javascript-single-sign-on.md
[開始使用行動服務]: ../mobile-services-windows-store-get-started.md
[Get started with data]: mobile-services-windows-store-javascript-get-started-data.md
[Get started with authentication]: mobile-services-windows-store-javascript-get-started-users.md
[Get started with push notifications]: mobile-services-javascript-backend-windows-store-javascript-get-started-push.md
[Authorize users with scripts]: ../mobile-services-windows-store-javascript-authorize-users-in-scripts.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Register your Windows Store app package for Microsoft authentication]: /develop/mobile/how-to-guides/register-windows-store-app-package
 

<!---HONumber=July15_HO1-->