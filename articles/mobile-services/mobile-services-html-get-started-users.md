<properties 
	pageTitle="開始使用驗證 (HTML 5) | 行動開發人員中心" 
	description="了解如何使用行動服務透過眾多識別提供者驗證 HTML 應用程式使用者，包括 Google、Facebook、Twitter 和 Microsoft。" 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="glenga"/>

# 在您的行動服務應用程式中新增驗證 

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

本主題顯示如何從 HTML 應用程式 (包含 PhoneGap 或 Cordova 應用程式) 驗證 Azure 行動服務中的使用者。在本教學課程中，您將使用行動服務支援的身分識別提供者，將驗證加入至快速入門專案。由行動服務成功驗證並授權之後，就會顯示使用者識別碼值。

本教學課程會以行動服務快速入門為基礎。您還必須先完成教學課程[開始使用行動服務]。

##<a name="register"></a>註冊應用程式進行驗證，並設定行動服務

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

##<a name="permissions"></a>限制只有通過驗證的使用者具有權限

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../../includes/mobile-services-restrict-permissions-javascript-backend.md)]


3. 在應用程式目錄中，從 **server** 子資料夾啟動下列其中一個命令檔。

	+ **launch-windows** (Windows 電腦) 
	+ **launch-mac.command** (Mac OS X 電腦)
	+ **launch-linux.sh** (Linux 電腦)

	>[AZURE.NOTE]在 Windows 電腦上，PowerShell 要求您確認是否要執行指令碼時，請輸入 `R`。因為指令碼是從網際網路中下載，所以您的網頁瀏覽器可能會警告您不要執行指令碼。發生此情況時，您必須要求瀏覽器繼續載入指令碼。

	如此會在本機電腦上啟動網頁伺服器來裝載新的應用程式。

2. 在網頁瀏覽器中開啟 URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> 以啟動應用程式。

	資料載入失敗。這是因為應用程式嘗試以未驗證的使用者身分來存取行動服務，但 _TodoItem_ 資料表現在需要驗證。

3. (選用) 開啟網頁瀏覽器的指令碼偵錯工具，重新載入頁面。確認發生拒絕存取錯誤。

接下來，您要將應用程式更新為在要求行動服務的資源之前允許驗證。

##<a name="add-authentication"></a>將驗證新增至應用程式

>[AZURE.NOTE]因為是在快顯視窗中執行登入，所以您應該從按鈕的 click 事件中叫用 <strong>login</strong> 方法。否則，許多瀏覽器會阻擋登入視窗。

1. 開啟專案檔案 index.html，找出 H1 元素，在它下面加入下列程式碼片段：

	    <div id="logged-in">
            You are logged in as <span id="login-name"></span>.
            <button id="log-out">Log out</button>
        </div>
        <div id="logged-out">
            You are not logged in.
            <button>Log in</button>
        </div>

	這樣可讓您從網頁登入行動服務。

2. 在 app.js 檔案中，找出檔案最尾端呼叫 refreshTodoItems 函數的那一行程式碼，換成下列程式碼：
	
		function refreshAuthDisplay() {
			var isLoggedIn = client.currentUser !== null;
			$("#logged-in").toggle(isLoggedIn);
			$("#logged-out").toggle(!isLoggedIn);

			if (isLoggedIn) {
				$("#login-name").text(client.currentUser.userId);
				refreshTodoItems();
			}
		}

		function logIn() {
			client.login("facebook").then(refreshAuthDisplay, function(error){
				alert(error);
			});
		}

		function logOut() {
			client.logout();
			refreshAuthDisplay();
			$('#summary').html('<strong>You must login to access data.</strong>');
		}

		// On page init, fetch the data and set up event handlers
		$(function () {
			refreshAuthDisplay();
			$('#summary').html('<strong>You must login to access data.</strong>');		    
			$("#logged-out button").click(logIn);
			$("#logged-in button").click(logOut);
		});

    這會建立一組函數來處理驗證程序。使用者會透過 Facebook 登入受到驗證。如果您使用的識別提供者不是 Facebook，請將傳給上述 **login** 方法的值變更為以下其中一個：*microsoftaccount*、*facebook*、*twitter*、*google* 或 *aad*。

	>[AZURE.IMPORTANT]在 PhoneGap 應用程式中，您必須也將以下外掛程式新增至專案：<ul><li><code>phonegap plugin add https://git-wip-us.apache.org/repos/asf/cordova-plugin-device.git</code></li> <li><code>phonegap plugin add https://git-wip-us.apache.org/repos/asf/cordova-plugin-inappbrowser.git</code></li></ul>

9. 返回正在執行應用程式的瀏覽器，重新整理頁面。

	   成功登入後，應用程式應會正確無誤地執行，而且您應能夠查詢行動服務並更新資料。

	>[AZURE.NOTE]若是使用 Internet Explorer，登入之後可能會出現錯誤：<code>Cannot reach window opener.It may be on a different Internet Explorer zone</code>。這是因為快顯是在不同於 localhost (內部網路) 的安全性區域 (網際網路) 中執行。這只會在開發期間使用 localhost 時影響到應用程式。解決方法是開啟 [網際網路選項]<strong></strong> 的 [安全性]<strong></strong> 索引標籤，依序按一下 [近端內部網路]<strong></strong>、[網站]<strong></strong>，然後停用 [自動偵測內部網路]<strong></strong>。完成測試時，請記得恢復此設定。

## <a name="next-steps"> </a>後續步驟

在下一堂教學課程[使用指令碼授權使用者]中，您將使用行動服務根據通過驗證使用者所提供的使用者 ID 值，並用它來篩選行動服務所傳回的資料。深入了解如何透過 HTML/JavaScript 來使用行動服務，請參閱[行動服務 HTML/JavaScript 作法概念性參考]

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]: #next-steps

<!-- Images. -->

[4]: ./media/mobile-services-html-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-html-get-started-users/mobile-service-uri.png
[13]: ./media/mobile-services-html-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-html-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-html-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. -->
[開始使用行動服務]: mobile-services-html-get-started.md
[Get started with data]: mobile-services-html-get-started-data.md
[使用指令碼授權使用者]: mobile-services-javascript-backend-service-side-authorization.md

[Azure Management Portal]: https://manage.windowsazure.com/
[行動服務 HTML/JavaScript 作法概念性參考]: mobile-services-html-how-to-use-client-library.md
 

<!---HONumber=July15_HO1-->