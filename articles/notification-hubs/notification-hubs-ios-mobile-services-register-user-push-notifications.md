<properties 
	pageTitle="使用行動服務註冊目前使用者以取得推播通知 - 通知中樞" 
	description="了解如何在 Azure 行動服務執行註冊時，在 iOS 應用程式中向 Azure 通知中心要求推播通知註冊。" 
	services="notification-hubs" 
	documentationCenter="ios" 
	authors="ysxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="04/24/2015" 
	ms.author="yuaxu"/>

# 使用行動服務註冊目前使用者以取得推播通知

<div class="dev-center-tutorial-selector sublanding">
    <a href="/documentation/articles/notification-hubs-windows-store-mobile-services-register-user-push-notifications/" title="Windows 市集 C#">Windows 市集 C#</a><a href="/documentation/articles/notification-hubs-ios-mobile-services-register-user-push-notifications/" title="iiOS" class="current">iOS</a>
</div>

本主題將說明以 Azure 行動服務執行註冊時，應如何向 Azure 通知中心要求推播通知註冊。這是[使用通知中心來通知使用者]教學課程的延伸主題。您必須已完成該教學課程中的必要步驟，才能建立已驗證的行動服務。如需通知使用者案例的詳細資訊，請參閱[使用通知中心來通知使用者]。

1. 在 Xcode 中，在您完成必要的教學課程[開始使用驗證]時所建立的專案中開啟 QSTodoService.h 檔案，然後新增下列 **deviceToken** 屬性：

		@property (nonatomic) NSData* deviceToken;

 	此屬性會儲存裝置權杖。

2. 在 QSTodoService.m 檔案中，新增下列 **getDeviceTokenInHex** 方法：

			- (NSString*)getDeviceTokenInHex {
			    const unsigned *tokenBytes = [[self deviceToken] bytes];
			    NSString *hexToken = [NSString stringWithFormat:@"%08X%08X%08X%08X%08X%08X%08X%08X",
			                          ntohl(tokenBytes[0]), ntohl(tokenBytes[1]), ntohl(tokenBytes[2]),
			                          ntohl(tokenBytes[3]), ntohl(tokenBytes[4]), ntohl(tokenBytes[5]),
			                          ntohl(tokenBytes[6]), ntohl(tokenBytes[7])];
			    return hexToken;
			}

	此方法會將裝置權杖轉換為十六進位字串值。

3. 在 QSAppDelegate.m 檔案中，將下列程式碼行新增至 **didFinishLaunchingWithOptions** 方法中：

			[[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];

	這會在您的應用程式中啟用推播通知。

4. 	在 QSAppDelegate.m 檔案中新增下列方法：

			- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {
			    [QSTodoService defaultService].deviceToken = deviceToken;
			}

	這會更新 **deviceToken** 屬性。

	> [AZURE.NOTE]此時，此方法中不應有任何其他程式碼。如果您已呼叫您在完成[開始使用通知中樞](/manage/services/notification-hubs/get-started-notification-hubs-ios/"%20target="_blank")教學課程時所新增的 **registerNativeWithDeviceToken** 方法，您必須註解化或移除該呼叫。

5.  (選用) 在 QSAppDelegate.m 檔案中，新增下列處理常式方法：

			- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo {
			    NSLog(@"%@", userInfo);
			    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
			                          [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
			                          @"OK" otherButtonTitles:nil, nil];
			    [alert show];
			}

 	此方法會在您執行中的應用程式接收到通知時，在 UI 中顯示警示。

6. 在 QSTodoListViewController.m 檔案中，新增 **registerForNotificationsWithBackEnd** 方法：

			- (void)registerForNotificationsWithBackEnd {
			    NSString* json = [NSString  stringWithFormat:@"{"platform":"ios", "deviceToken":"%@"}", [self.todoService getDeviceTokenInHex] ];

			    [self.todoService.client invokeAPI:@"register_notifications" data:[json dataUsingEncoding:NSUTF8StringEncoding] HTTPMethod:@"POST" parameters:nil headers:nil completion:^(id result, NSHTTPURLResponse *response, NSError *error) {
			        if (error != nil) {
			            NSLog(@"Registration failed: %@", error);
			        } else {
			            // display UIAlert with successful login
			            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Back-end registration" message:@"Registration successful" delegate:nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];
			            [alert show];
			        }
			    }];
			}

	此方法會建構包含裝置權杖的 json 裝載。接著，它會呼叫您行動服務中的自訂 API，以進行通知的註冊。此方法會建立推播通知的裝置權杖，並將其連同裝置類型傳送至會在通知中心建立註冊的自訂 API 方法。此自訂 API 定義於[使用通知中心來通知使用者]中。

7.	最後，在使用者成功驗證後，在 **viewDidAppear** 方法中將此 API 的呼叫新增至新的 **registerForNotificationsWithBackEnd** 方法中，如下列範例所示：

			- (void)viewDidAppear:(BOOL)animated
			{
			    MSClient *client = self.todoService.client;

			    if (client.currentUser != nil) {
			        return;
			    }

			    [client loginWithProvider:@"microsoftaccount" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
			        [self refresh];
			        [self registerForNotificationsWithBackEnd];
			    }];
			}

	> [AZURE.NOTE]這會確使在每次載入頁面時都會要求註冊。在您的應用程式中，您可能只想定期進行此註冊，以確保註冊是最新的。
	
現在，用戶端應用程式已更新，請回到[使用通知中心來通知使用者]，並更新行動服務，以使用通知中心傳送通知。

<!-- Anchors. -->

<!-- Images. -->


<!-- URLs. -->
[使用通知中心來通知使用者]: /manage/services/notification-hubs/notify-users
[開始使用驗證]: /develop/mobile/tutorials/get-started-with-users-ios/

[Azure Management Portal]: https://manage.windowsazure.com/
[Get Started with Notification Hubs]: /manage/services/notification-hubs/get-started-notification-hubs-ios/
 

<!---HONumber=July15_HO2-->