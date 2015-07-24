<properties 
	pageTitle="Windows 通用 SDK 概觀" 
	description="適用於 Azure Mobile Engagement 的 Windows 通用 SDK 概觀。" 									
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/02/2015" 
	ms.author="piyushjo" />

#適用於 Azure Mobile Engagement 的 Windows 通用 SDK 概觀

從這裡開始了解所有在 Windows 通用 app 中整合 Azure Mobile Engagement 的細節。如果您想要先試用一下，請務必先完成我們的 [15 分鐘教學課程](mobile-engagement-windows-store-dotnet-get-started.md)。

按一下以查看 [SDK 內容](mobile-engagement-windows-store-sdk-content.md)

##整合程序

1. 從這裡開始：[如何在 Windows 通用 app 中整合 Mobile Engagement](mobile-engagement-windows-store-integrate-engagement.md)

2. 通知：[如何在 Windows 通用 app 中整合 Reach (通知)](mobile-engagement-windows-store-integrate-engagement-reach.md)

3. 標記計劃實作：[如何在 Windows 通用 app 中使用進階的 Mobile Engagement 標記 API](mobile-engagement-windows-store-use-engagement-api.md)。

##版本資訊

###3.1.0 (05/21/2015)

-   Mobile Engagement 裝置識別碼現在是根據在安裝時產生的 GUID。

如需較早版本，請參閱[完整版本資訊](mobile-engagement-windows-store-release-notes.md)

##升級程序

如果您已經整合舊版 Engagement 到您的應用程式，在升級 SDK 時您必須考慮以下幾點。

如果您錯過數個版本的 SDK，可能必須遵循幾個程序步驟，請參閱完整的[升級程序](mobile-engagement-windows-store-upgrade-procedure.md)。例如，如果您要從 0.10.1 移轉到 0.11.0，您必須先遵循「從 0.9.0 到 0.10.1」的程序，然後「從 0.10.1 到 0.11.0」的程序。

###從 2.0.0 到 3.0.0

#### 資源
此步驟僅涉及自訂資源。如果您已自訂透過 SDK (html、影像、重疊) 提供的資源，則您必須先備份這些資源，然後在已升級的資源上升級和重新套用您的自訂。

### 從舊版升級

請參閱[升級程序](mobile-engagement-windows-store-upgrade-procedure/)

<!---HONumber=July15_HO2-->