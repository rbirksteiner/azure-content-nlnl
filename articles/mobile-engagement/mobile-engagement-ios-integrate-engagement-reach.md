<properties 
	pageTitle="Azure Mobile Engagement iOS SDK Reach 整合" 
	description="Azure Mobile Engagement iOS SDK 的最新更新與程序"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

#如何在 iOS 上整合 Engagement Reach

> [AZURE.IMPORTANT]在遵循此指南之前，您必須先遵循＜如何在 iOS 上整合 Engagement＞文件中所說明的整合程序。

##整合步驟

### 將 Engagement Reach SDK 嵌入您的 iOS 專案

-   在您的 Xcode 專案中加入 Reach SDK。在 Xcode 中，移至 [專案] > [加入至專案]，然後選擇 `EngagementReach` 資料夾。

### 修改您的應用程式委派

-   在您的實作檔案頂端，匯入 Engagement Reach 模組：

			[...]
			#import "AEReachModule.h"

-   在 `applicationDidFinishLaunching:` 或 `application:didFinishLaunchingWithOptions:` 方法內建立觸達模組，並將它傳遞到您現有的 Engagement 初始化行：

			- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
			  AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
			  [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
			  [...]
			
			  return YES;
			}

-   使用您想做為通知圖示的影像名稱修改 **'icon.png'** 字串。
-   如果您想在觸達活動中使用 [更新徽章值] 選項，或想使用原生推送 </SaaS/Reach API/Campaign format/Native Push> 活動，必須讓觸達模組自行管理徽章圖示 (它會自動清除應用程式徽章，也會重設每一次應用程式啟動或於前景執行時，由 Engagement 所儲存的值)。做法是在觸達模組初始化之後加入以下這一行：

			[reach setAutoBadgeEnabled:YES];

-   如果您想要處理觸達資料推送，必須讓您的應用程式委派符合 `AEReachDataPushDelegate` 通訊協定。觸達模組初始化之後請加入以下這一行：

			[reach setDataPushDelegate:self];

-   然後您就可以在應用程式委派中實作 `onDataPushStringReceived:` 與 `onDataPushBase64ReceivedWithDecodedBody:andEncodedBody:` 方法：

			-(BOOL)didReceiveStringDataPushWithCategory:(NSString*)category body:(NSString*)body
			{
			   NSLog(@"String data push message with category <%@> received: %@", category, body);
			   return YES;
			}
			
			-(BOOL)didReceiveBase64DataPushWithCategory:(NSString*)category decodedBody:(NSData *)decodedBody encodedBody:(NSString *)encodedBody
			{
			   NSLog(@"Base64 data push message with category <%@> received: %@", category, encodedBody);
			   // Do something useful with decodedBody like updating an image view
			   return YES;
			}

### 類別

當您建立「資料推送」活動時，類別參數是選用的，且可讓您篩選資料推送。如果您想要推送不同種類的 `Base64` 資料，且想要在剖析這些資料之前識別其類型，這會很有用。

您的應用程式現在已準備好接收及顯示觸達內容！

##如何隨時接收宣告和輪詢

透過使用 Apple Push Notification Service，Engagement 就可以隨時傳送觸達通知給您的使用者。

若要啟用這項功能，必須準備好您的應用程式以使用 Apple 推送通知，並修改您的應用程式委派。

### 準備好您的應用程式以使用 Apple 推送通知

請依照本指南進行：《如何準備您的應用程式以使用 Apple 推送通知》。

### 加入必要的用戶端程式碼

目前您的應用程式應該在 Engagement 前端具備已註冊的 Apple 推送憑證。

如果尚未這麼做，必須註冊應用程式以接收推送通知。啟動您的應用程式時，請新增以下這一行 (通常在 `application:didFinishLaunchingWithOptions:` 中)：

			if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
			  [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
			  [application registerForRemoteNotifications];
			}
			else {
			
			  [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
			}

然後，您需要將 Apple 伺服器傳回的裝置權杖提供給 Engagement。這會在您應用程式委派的 `application:didRegisterForRemoteNotificationsWithDeviceToken:` 方法中完成：

			- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
			{
			    [[EngagementAgent shared] registerDeviceToken:deviceToken];
			}

最後，當您的應用程式接收到遠端通知時，必須告知 Engagement SDK。若要這麼做，只要呼叫您應用程式委派中的 `applicationDidReceiveRemoteNotification:` 方法即可：

			- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo
			{
			    [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo];
			}

### 最後一個步驟

一旦完成所有的這些步驟，您的應用程式就已經準備好隨時接收 Engagement 推送訊息。

但是，在您的應用程式已經啟動以回應送達的 Engagement 推送訊息時，您可能想要對使用者顯示一些內容。針對這一點，我們在 `AEPushDelegate` 通訊協定中多提供了三個委派方法：

			-(void)willRetrieveLaunchMessage;
			-(void)didFailToRetrieveLaunchMessage;
			-(void)didReceiveLaunchMessage:(AEPushMessage*)launchMessage;

`willRetrieveLaunchMessage`

> 當使用者啟動應用程式以回應送達的推送訊息時，會在應用程式啟動後馬上呼叫此方法，以指出正在擷取完整的訊息。這是向使用者顯示載入訊息的一個好機會。

`didFailToRetrieveLaunchMessage`

> 當訊息擷取失敗時，會呼叫此方法。此時，您應該會向使用者顯示一個指示項目失敗的訊息。

`didReceiveLaunchMessage:`

> 當接受到啟動應用程式的推送訊息時，會呼叫此方法。利用這個機會隱藏任何載入訊息，並向使用者顯示適當的內容。

一旦初始化 Engagement，您就必須設定推送委派：

			[[EngagementAgent shared] setPushDelegate:self];

### 完整範例

以下是整合的完整範例：

			#pragma mark -
			#pragma mark Application lifecycle
			
			- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
			{
			  /* Reach module */
			  AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
			  [reach setAutoBadgeEnabled:YES];
			
			  /* Engagement initialization */
			  [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
			  [[EngagementAgent shared] setPushDelegate:self];
			
			  /* Views */
			  [window addSubview:[tabBarController view]];
			  [window makeKeyAndVisible];
			
			  [application registerForRemoteNotificationTypes:UIRemoteNotificationTypeAlert|UIRemoteNotificationTypeBadge|UIRemoteNotificationTypeSound];
			  return YES;
			}
			
			- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
			{
			  [[EngagementAgent shared] registerDeviceToken:deviceToken];
			}
			
			- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo
			{
			  [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo];
			}
			
			
			#pragma mark -
			#pragma mark Engagement push delegate
			
			-(void)willRetrieveLaunchMessage
			{
			  [UIApplication sharedApplication].networkActivityIndicatorVisible = YES;
			}
			
			-(void)didReceiveLaunchMessage:(AEPushMessage *)launchMessage
			{
			  /* Hide network activity indicator */
			  [UIApplication sharedApplication].networkActivityIndicatorVisible = NO;
			}
			
			-(void)didFailToRetrieveLaunchMessage
			{
			  /* Hide network activity indicator */
			  [UIApplication sharedApplication].networkActivityIndicatorVisible = NO;
			
			  /* Display an error alert */
			  UIAlertView* alert = [[UIAlertView alloc] initWithTitle:NSLocalizedString(@"Sorry", nil)
			                         message:NSLocalizedString(@"Could not retrieve message.", nil)
			                         delegate:nil
			                         cancelButtonTitle:NSLocalizedString(@"Close", nil)
			                         otherButtonTitles:nil];
			  [alert show];
			  [alert release];
			}

##如何自訂活動

### 通知

有兩種通知類型： 系統通知和應用程式內通知。

系統通知都是由 iOS 處理，且無法自訂。

應用程式內通知，是由以動態方式加入到目前應用程式視窗中的檢視所組成。這稱為通知重疊。通知重疊非常適合用於快速整合，因為它們不需要您修改應用程式中的任何檢視。

#### 版面配置

若要修改應用程式內通知的外觀，您可以依需求直接修改 `AENotificationView.xib` 檔案，只要保留標記值與現有子檢視的類型即可。

根據預設，應用程式內通知會出現在畫面底部。如果您偏好將通知顯示在畫面頂端，請編輯所提供的 `AENotificationView.xib` 並變更主要檢視的 `AutoSizing` 屬性，讓通知可以保留在其 superview 的頂端。

#### 類別

當您修改提供的版面配置時，會修改您所有通知的外觀。類別可讓您定義通知的各種目標外觀 (也可能是行為)。當您建立觸達活動時可以指定類別。請記住，類別也可讓您自訂宣告與輪詢，本文件稍後將會說明。

若要為您的通知註冊類別處理常式，必須在觸達模組完成初始化後加入呼叫。

			AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
			[reach registerNotifier:myNotifier forCategory:@"my_category"];
			...

`myNotifier` 必須是符合 `AENotifier` 通訊協定之物件的執行個體。

您可以自己實作通訊協定方法，或選擇重新實作已經執行大部分工作的現有 `AEDefaultNotifier` 類別。

例如，如果您想要重新定義特定類別的通知檢視，可以遵循此範例：

			#import "AEDefaultNotifier.h"
			#import "AENotificationView.h"
			@interface MyNotifier : AEDefaultNotifier
			@end
			
			@implementation MyNotifier
			
			-(NSString*)nibNameForCategory:(NSString*)category
			{
			  return "MyNotificationView";
			}
			
			@end

這個簡單的類別範例假設您已在主要的應用程式套件組合中有一個名為 `MyNotificationView.xib` 的檔案。如果方法無法找到相對應的 `.xib`，將不會顯示通知且 Engagement 會在主控台中輸出一則訊息。

提供的 nib 檔案應該遵守以下規則：

-   它應該只包含一個檢視。
-   子檢視的類型應該與提供的 nib 檔案 (名稱為 `AENotificationView.xib`) 內的類型相同
-   子檢視應該具有與提供的 nib 檔案 (名稱為 `AENotificationView.xib`) 內標記相同的標記

> [AZURE.TIP]複製提供的 nib 檔案 (名稱為 `AENotificationView.xib`)，並從該處開始工作。但請小心，此 nib 檔案內的檢視與 `AENotificationView` 類別相關聯。這個類別重新定義了 `layoutSubViews` 方法，藉此根據內容來移動並重新調整其子檢視的大小。您可以用 `UIView` 取代它，或是自訂檢視類別。

如果您需要更深入地自訂通知 (如果要讓執行個體直接從程式碼載入檢視)，建議您查看所提供之 `Protocol ReferencesDefaultNotifier` 與 `AENotifier` 的原始程式碼與類別文件。

請注意，您可以針對多個類別使用相同的通知程式。

您也可以重新定義預設通知程式，如下所示：

			AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
			[reach registerNotifier:myNotifier forCategory:kAEReachDefaultCategory];

##### 通知處理

使用預設類別時，會在 `AEReachContent` 物件上呼叫部分生命週期方法，來報告統計資料並更新活動狀態：

-   在應用程式中顯示通知時，如果 `handleNotification:` 傳回 `YES`，則 `AEReachModule` 會呼叫 `displayNotification` 方法 (它會報告統計資料)。
-   如果關閉通知，則會呼叫 `exitNotification` 方法、報告統計資料，且可以立即處理下一個活動。
-   如果按一下通知，則會呼叫 `actionNotification`、報告統計資料，並執行相關聯的動作。

如果 `AENotifier` 的實作略過預設行為，您必須自行呼叫這些生命週期方法。以下範例說明一些會略過預設行為的情況：

-   您不延伸 `AEDefaultNotifier`，例如從頭開始實作類別處理。
-   您已覆寫 `prepareNotificationView:forContent:`，請務必至少將 `onNotificationActioned` 或 `onNotificationExited` 對應到其中一個 U.I 控制項。

> [AZURE.WARNING]如果 `handleNotification:` 擲回例外狀況，則會刪除內容且會呼叫 `drop`，這會在統計資料中報告，且可以立即處理下一個活動。

#### 將通知併入現有的檢視

重疊最適合用於快速整合，但有些時候不太方便，或是造成不想要的副作用。

如果您不滿意您部分檢視中的重疊系統，可以對這些檢視加以自訂。

您可以決定在現有的檢視中包含我們的通知版面配置。若要這樣做，有兩種實作樣式可用：

1.  使用介面產生器加入通知檢視

	-   開啟 [介面產生器]
	-   放置您想要在其中顯示通知的 `UIView` (大小為 320x60，如果在 iPad 上則為 768x60)
	-   將此檢視的標記值設定為：**36822491**

2.  以程式設計方式新增通知檢視。在您已經將檢視初始化時加入以下程式碼：

			UIView* notificationView = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 320, 60)]; //Replace x and y coordinate values to your needs.
			notificationView.tag = NOTIFICATION_AREA_VIEW_TAG;
			[self.view addSubview:notificationView];

您可以在 `AEDefaultNotifier.h` 中找到 `NOTIFICATION_AREA_VIEW_TAG` 巨集。

> [AZURE.NOTE]預設通知程式會自動偵測此檢視中是否已包含通知版面配置，而且不會為它新增重疊。

### 宣告和輪詢

#### 版面配置

您可以修改 `AEDefaultAnnouncementView.xib` 與 `AEDefaultPollView.xib` 檔案，只要保留標記值與現有子檢視的類型即可。

#### 類別

##### 替代版面配置

類似通知，活動類別可以用來做為宣告和輪詢的替代版片配置。

若要建立宣告的類別，您必須在初始化觸達模組後延伸 **AEAnnouncementViewController** 並將它註冊：

			AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
			[reach registerAnnouncementController:[MyCustomAnnouncementViewController class] forCategory:@"my_category"];

> [AZURE.NOTE]每次使用者按一下通知以取得 "my\\_category" 類別的宣告時，會將您已經註冊的檢視控制器 (在此情況下為 `MyCustomAnnouncementViewController`) 透過呼叫 `initWithAnnouncement:` 方法來初始化，而且會將檢視加入到目前的應用程式視窗。

在 `AEAnnouncementViewController` 類別的實作中，您必須讀取 `announcement` 屬性來初始化您的子檢視。請考量以下範例，其中的兩個標籤會使用屬於 `AEReachAnnouncement` 類別的 `title` 和 `body` 屬性來初始化：

			-(void)loadView
			{
			    [super loadView];
			
			    UILabel* titleLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 20, 300, 60)];
			    titleLabel.font = [UIFont systemFontOfSize:32.0];
			    titleLabel.text = self.announcement.title;
			
			    UILabel* bodyLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 20, 300, 60)];
			    bodyLabel.font = [UIFont systemFontOfSize:24.0];
			    bodyLabel.text = self.announcement.body;
			
			    [self.view addSubview:titleLabel];
			    [self.view addSubview:bodyLabel];
			}

如果您不想要自行載入您的檢視，但是想要重複使用預設的宣告檢視配置，可以讓自訂檢視控制器延伸提供的 `AEDefaultAnnouncementViewController` 類別。在此情況下，請複製 nib 檔案 `AEDefaultAnnouncementView.xib` 並將它重新命名，讓您的自訂檢視控制器可以載入它 (若是名稱為 `CustomAnnouncementViewController` 的控制器，您應該呼叫您的 nib 檔案 `CustomAnnouncementView.xib`) 。

若要取代預設的宣告類別，只要針對 `kAEReachDefaultCategory` 中定義的類別註冊您的自訂檢視控制器即可：

			[reach registerAnnouncementController:[MyCustomAnnouncementViewController class] forCategory:kAEReachDefaultCategory];

可利用相同的方式自訂輪詢：

			AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
			[reach registerPollController:[MyCustomPollViewController class] forCategory:@"my_category"];

這一次，提供的 `MyCustomPollViewController` 必須延伸 `AEPollViewController`。或者您可以選擇從預設控制器 `AEDefaultPollViewController` 延伸。

> [AZURE.IMPORTANT]請記得在關閉檢視控制器之前呼叫 `action` (若是自訂輪詢檢視控制器，則為 `submitAnswers:`)，或是呼叫 `exit` 方法。否則，將不會傳送統計資料 (亦即無法分析活動)，更重要的是將不會通知下一個活動，直到應用程式處理程序重新啟動為止。

##### 實作範例

在此實作中，是從外部 xib 檔案載入自訂宣告檢視。

就像進階通知的自訂一樣，也建議您查看標準實作的原始程式碼。

`CustomAnnouncementViewController.h`

			//Interface
			@interface CustomAnnouncementViewController : AEAnnouncementViewController {
			  UILabel* titleLabel;
			  UITextView* descTextView;
			  UIWebView* htmlWebView;
			  UIButton* okButton;
			  UIButton* cancelButton;
			}
			
			@property (nonatomic, retain) IBOutlet UILabel* titleLabel;
			@property (nonatomic, retain) IBOutlet UITextView* descTextView;
			@property (nonatomic, retain) IBOutlet UIWebView* htmlWebView;
			@property (nonatomic, retain) IBOutlet UIButton* okButton;
			@property (nonatomic, retain) IBOutlet UIButton* cancelButton;
			
			-(IBAction)okButtonClicked:(id)sender;
			-(IBAction)cancelButtonClicked:(id)sender;

`CustomAnnouncementViewController.m`

			//Implementation
			@implementation CustomAnnouncementViewController
			@synthesize titleLabel;
			@synthesize descTextView;
			@synthesize htmlWebView;
			@synthesize okButton;
			@synthesize cancelButton;
			
			-(id)initWithAnnouncement:(AEReachAnnouncement*)anAnnouncement
			{
			  self = [super initWithNibName:@"CustomAnnouncementViewController" bundle:nil];
			  if (self != nil) {
			    self.announcement = anAnnouncement;
			  }
			  return self;
			}
			
			- (void) dealloc
			{
			  [titleLabel release];
			  [descTextView release];
			  [htmlWebView release];
			  [okButton release];
			  [cancelButton release];
			  [super dealloc];
			}
			
			- (void)viewDidLoad {
			  [super viewDidLoad];
			
			  /* Init announcement title */
			  titleLabel.text = self.announcement.title;
			
			  /* Init announcement body */
			  if(self.announcement.type == AEAnnouncementTypeHtml)
			  {
			    titleLabel.hidden = YES;
			    htmlWebView.hidden = NO;
			    [htmlWebView loadHTMLString:self.announcement.body baseURL:[NSURL URLWithString:@"http://localhost/"]];
			  }
			  else
			  {
			    titleLabel.hidden = NO;
			    htmlWebView.hidden = YES;
			    descTextView.text = self.announcement.body;
			  }
			
			  /* Set action button label */
			  if([self.announcement.actionLabel length] > 0)
			    [okButton setTitle:self.announcement.actionLabel forState:UIControlStateNormal];
			
			  /* Set exit button label */
			  if([self.announcement.exitLabel length] > 0)
			    [cancelButton setTitle:self.announcement.exitLabel forState:UIControlStateNormal];
			}
			
			#pragma mark Actions
			
			-(IBAction)okButtonClicked:(id)sender
			{
			    [self action];
			}
			
			-(IBAction)cancelButtonClicked:(id)sender
			{
			    [self exit];
			}
			
			@end
 

<!---HONumber=July15_HO2-->