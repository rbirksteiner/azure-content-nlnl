<properties 
	pageTitle="開始使用行動分析 | 行動開發人員中心" 
	description="開始使用行動分析。" 
	documentationCenter="ios" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="mahender"/>

# 開始使用行動分析 (Capptain)

<div class="dev-center-tutorial-selector sublanding">
<a href="/documentation/articles/mobile-services-ios-get-started-mobile-analytics" title="iOS" class="current">iOS</a>
</div>

在本教學課程中，您會將行動分析功能新增至使用 [Capptain] 的應用程式。行動分析可讓您判斷使用者如何與您的應用程式互動，以及哪些部分會引發大部分的活動。若要開始使用這些資料，您必須使用 Capptain SDK 設定您的應用程式。


>[AZURE.NOTE]Microsoft 擁有的 Capptain.com 可針對 Azure 行動服務標準層客戶，將行動應用程式的分析免費提供給高達 100,000 個每月有效使用者。若要使用這項服務，請在 mobileservices@microsoft.com 與我們連絡，以取得進一步的指示。本教學課程將彙總 Capptain.com 功能，並提供其使用方式的相關指示。


本教學課程將逐步引導您完成下列基本步驟：

1. [初始化 Capptain SDK]
2. [多載 UIViewController]

本教學課程需要下列各項：

* 一個 [Capptain] 帳戶
* 一個[行動服務標準層]應用程式

## <a name="initialize"></a>初始化 Capptain SDK

1. 導覽至您在 Capptain 中註冊之應用程式的 [應用程式詳細資料] 頁面。選取 [SDK] 索引標籤，並下載套件。

2. 在 XCode 中，以滑鼠右鍵按一下您的專案並選取 [將檔案新增至...]，以將 Capptain SDK 新增至您的專案。 選擇 CapptainSDK 資料夾。

3. 選取您的專案。在 [Build Phases] 索引標籤下選取 [Link Binary With Libraries]，然後新增下列架構：
    * AdSupport.framework - 將連結設為選用
    * SystemConfiguration.framework
    * CoreTelephony.framework
    * CFNetwork.framework
    * CoreLocation.framework
    * libxml2.dylib

    >[AZURE.NOTE]AdSupport 架構可以移除。Capptain 需要此架構來收集 IDFA。但您可以停用 IDFA 收集，以符合關於此識別碼的 Apple 原則。

4. 在「應用程式委派」實作檔案中，匯入 Capptain 代理程式。


        #import "CapptainAgent.h"


5. 在 `applicationDidFinishLaunching:` 或 `application:didFinishLaunchingWithOptions:` 內，使用您的應用程式識別碼或 SDK 金鑰傳入 `registerapp:identifiedby:`，以初始化 Capptain 代理程式。

         - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
          [...]
          [CapptainAgent registerApp:@"YOUR_APPID" identifiedBy:@"YOUR_SDK_KEY"];
          [...]
        }

## <a name="instrument"></a>多載 UIViewController

1. 在您的專案中找出 `UIViewController` 的每個子系，然後確定每個子系都改為繼承自 `CapptainViewController`。

        #import <UIKit/UIKit.h>
        #import "CapptainViewController.h"

        // formerly @interface Tab1ViewController : UIViewController<UITextFieldDelegate>
        @interface Tab1ViewController : CapptainViewController<UITextFieldDelegate> {
          UITextField* myTextField1;
          UITextField* myTextField2;
        }

        @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
        @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

2. 在您的專案中找出 `UITableViewController` 的每個子系，然後確定每個子系都改為繼承自 `CapptainTableViewController`。

    您的應用程式現在已設定成將分析資料傳送至 Capptain。

## 後續步驟
在 [http://www.capptain.com](http://www.capptain.com) 上了解 Capptain 還可為您的應用程式執行哪些工作

<!-- Anchors. -->
[初始化 Capptain SDK]: #initialize
[多載 UIViewController]: #instrument


<!-- URLs. -->
[Capptain]: http://www.capptain.com
[行動服務標準層]: /pricing/details/mobile-services/
 

<!---HONumber=July15_HO1-->