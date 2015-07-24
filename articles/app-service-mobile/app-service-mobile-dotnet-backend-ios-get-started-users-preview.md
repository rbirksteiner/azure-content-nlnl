<properties
	pageTitle="開始在 iOS 中使用行動應用程式的驗證"
	description="了解如何使用行動應用程式透過眾多識別提供者驗證 iOS 應用程式使用者，包括 Google、Facebook、Twitter 和 Microsoft。"
	services="app-service\mobile"
	documentationCenter="ios"
	authors="mattchenderson" 
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/16/2015"
	ms.author="mahender"/>

# 將驗證新增至您的 iOS 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

本主題說明如何從用戶端應用程式驗證應用程式服務行動應用程式的使用者。在本教學課程中，您將使用應用程式服務支援的身分識別提供者，將驗證新增至快速入門專案。由行動應用程式成功驗證並授權之後，就會顯示使用者識別碼值。

本教學課程以行動應用程式快速入門為基礎。您也必須先完成[建立 iOS 應用程式]教學課程。

##<a name="register"></a>註冊應用程式進行驗證，並設定應用程式服務

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>限制只有通過驗證的使用者具有權限

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

<ol start="7">
<li><p>在 Xcode 中，開啟專案。按 [執行]<b></b> 按鈕，以啟動應用程式。確認在應用程式啟動後，發生狀態代碼 401 (未經授權) 的例外狀況。</p>

   	<p>這是因為應用程式嘗試以未驗證的使用者身分存取您的行動應用程式程式碼，但 <em>TodoItem</em> 資料表現在需要驗證。</p></li>
</ol>

接下來，您要將應用程式更新為在要求應用程式服務的資源之前必須驗證使用者。

##<a name="add-authentication"></a>將驗證新增至應用程式

[AZURE.INCLUDE [app-service-mobile-ios-authenticate-app](../../includes/app-service-mobile-ios-authenticate-app.md)]

##<a name="store-authentication"></a>將驗證權杖儲存在應用程式中

[AZURE.INCLUDE [app-service-mobile-ios-authenticate-app-with-token](../../includes/app-service-mobile-ios-authenticate-app-with-token.md)]


<!-- URLs. -->

[建立 iOS 應用程式]: app-service-mobile-dotnet-backend-ios-get-started-preview.md

[Azure Management Portal]: https://portal.azure.com
 

<!---HONumber=62-->