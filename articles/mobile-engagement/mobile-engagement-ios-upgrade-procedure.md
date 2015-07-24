<properties 
	pageTitle="Azure Mobile Engagement iOS SDK 升級程序" 
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

#升級程序

如果您已經整合舊版 Engagement 到您的應用程式，在升級 SDK 時您必須考慮以下幾點。

針對每個新版 SDK，您必須先取代 (在 xcode 中移除並重新匯入) EngagementSDK 與 EngagementReach 資料夾。

##從 1.16.0 到 2.0.0
以下說明如何將 SDK 整合從 Capptain SAS 提供的 Capptain 服務，移轉到由 Azure Mobile Engagement 提供的應用程式內。如果您是從較早版本移轉，請參閱 Capptain 網站，先移轉到 1.16 後再套用以下程序。

>[Azure.IMPORTANT]Capptain 和 Mobile Engagement 是不同的服務，而以下程序只適用於移轉用戶端應用程式。移轉應用程式中的 SDK「不會」將您的資料從 Capptain 伺服器移轉到 Mobile Engagement 伺服器

### 代理程式

`registerApp:` 方法已被新方法 `init:` 取代。您的應用程式委派必須隨之更新，並使用連接字串：

			- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
			{
			  [...]
			  [EngagementAgent init:@"YOUR_CONNECTION_STRING"];
			  [...]
			}

SmartAd 追蹤已從 SDK 移除，因此您必須移除 `AETrackModule` 類別的所有執行個體

### 類別名稱變更

執行品牌再造時，有幾個類別/檔案名稱需要變更。

所有首碼為 "CP" 的類別，都使用 "AE" 首碼重新命名。

範例：

-   `CPModule.h` 已重新命名為 `AEModule.h`。

所有首碼為 "Capptain" 的類別，都已使用 "Engagement" 首碼重新命名。

範例：

-   `CapptainAgent` 類別已重新命名為 `EngagementAgent`。
-   `CapptainTableViewController` 類別已重新命名為 `EngagementTableViewController`。
-   `CapptainUtils` 類別已重新命名為 `EngagementUtils`。
-   `CapptainViewController` 類別已重新命名為 `EngagementViewController`。
 

<!---HONumber=July15_HO2-->