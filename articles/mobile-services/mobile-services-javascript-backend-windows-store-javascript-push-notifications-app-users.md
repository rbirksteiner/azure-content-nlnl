<properties 
	pageTitle="傳送推播通知給已驗證的使用者" 
	description="了解如何將推播通知傳送給特定" 
	services="mobile-services,notification-hubs" 
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
	ms.date="06/04/2015" 
	ms.author="glenga"/>

# 傳送推播通知給已驗證的使用者

[AZURE.INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

本主題說明如何將推播通知傳送給任何已註冊之 iOS 裝置上的驗證使用者。不同於先前的[將推播通知新增至您的應用程式]教學課程，本教學課程會變更您的行動服務，要求使用者必須先完成驗證，用戶端才能向通知中心註冊推播通知。此外也會修改註冊，以根據指派的使用者識別碼新增標記。最後會更新伺服器指令碼，而僅將通知傳送給已驗證的使用者，而不是所有的註冊。
 
本教學課程支援 Windows 市集和 Windows Phone 市集應用程式。

##必要條件 

在開始本教學課程之前，您必須已完成下列行動服務教學課程：

+ [將驗證新增至您的應用程式]<br/>將登入需求新增至 TodoList 範例應用程式。

+ [將推播通知新增至您的應用程式]<br/>使用通知中心，設定 TodoList 範例應用程式的推播通知。

在您完成這兩個教學課程後，您即可防止未經授權的使用者從您的行動服務註冊推播通知。

##<a name="register"></a>更新服務以要求註冊所需的驗證

[AZURE.INCLUDE [mobile-services-javascript-backend-push-notifications-app-users](../../includes/mobile-services-javascript-backend-push-notifications-app-users.md)]

<ol start="5"><li><p>使用下列程式碼來取代 insert 函數，然後按一下 [儲存]<strong></strong>：</p>
<pre><code> function insert(item, user, request) {
    // Define a payload for the Windows Store toast notification.
    var payload = '&lt;?xml version="1.0" encoding="utf-8"?>&lt;toast>&lt;visual>' +    
    '&lt;binding template="ToastText01">&lt;text id="1">' +
    item.text + '&lt;/text>&lt;/binding>&lt;/visual>&lt;/toast>';

    // Get the ID of the logged-in user.
    var userId = user.userId;		

    request.execute({
        success: function() {
            // If the insert succeeds, send a notification to all devices 
	    	// registered to the logged-in user as a tag.
            	push.wns.send(userId, payload, 'wns/toast', {
                success: function(pushResponse) {
                    console.log("Sent push:", pushResponse);
	    			request.respond();
                    },             
                    error: function (pushResponse) {
                            console.log("Error Sending push:", pushResponse);
	    				request.respond(500, { error: pushResponse });
                        }
                    });
                }
            });
}</code></pre>

<p>此 insert 指令碼會使用「使用者識別碼標記」傳送推播通知 (包含所插入項目的文字) 給所有 Windows 市集應用程式註冊。</p></li></ol>

##<a name="update-app"></a>更新應用程式以在註冊前先登入

[AZURE.INCLUDE [mobile-services-windows-store-javascript-push-notifications-app-users](../../includes/mobile-services-windows-store-javascript-push-notifications-app-users.md)]

##<a name="test"></a>測試應用程式

[AZURE.INCLUDE [mobile-services-windows-test-push-users](../../includes/mobile-services-windows-test-push-users.md)]

<!-- Anchors. -->
[Updating the service to require authentication for registration]: #register
[Updating the app to log in before registration]: #update-app
[Testing the app]: #test
[Next Steps]: #next-steps


<!-- URLs. -->
[將驗證新增至您的應用程式]: mobile-services-windows-store-javascript-get-started-users.md
[將推播通知新增至您的應用程式]: mobile-services-javascript-backend-windows-store-javascript-get-started-push.md

[Azure Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=July15_HO1-->