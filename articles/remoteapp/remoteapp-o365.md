
<properties 
    pageTitle="透過 Azure RemoteApp 使用 Office 365" 
    description="了解 Office 365 和 Azure RemoteApp 如何共同運作" 
    services="remoteapp" 
    solutions="" 
	documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/28/2015" 
    ms.author="elizapo" />

# 透過 Azure RemoteApp 使用 Office 365

在 RemoteApp 中裝載 Office 應用程式有兩個選項：Office 365 ProPlus 或 Office 2013 Professional Plus 試用版。

## Office 365 ProPlus 
您可以使用 Office 365 ProPlus 範本映像建立 RemoteApp 收藏。此選項可讓您將 Office 365 服務延伸至 RemoteApp。您必須有現有的訂閱方案，而且您的使用者必須有 Office 365 ProPlus 服務的授權 (獨立授權或透過 Office 365 服務方案授權)。

RemoteApp 支援 Office 365 共用電腦啟用。當您啟用「共用電腦啟用」時，以及使用 [Office 部署工具](http://www.microsoft.com/download/details.aspx?id=36778)進行安裝時，Office 365 ProPlus 會進行安裝但不會啟用。當使用者登入包含 Office 365 的收藏時，Office 會檢查是否已為 Office 365 ProPlus 佈建使用者。如果是，則 Office 會暫時啟用 Office 365 ProPlus - 這個啟用只會持續到該使用者登出服務為止。

若要使用 Office 365 共用電腦啟用，您必須依照[這些指示](https://technet.microsoft.com/library/dn782858.aspx)建立[自訂範本](remoteapp-create-custom-image.md)並在該處安裝 Office 365 ProPlus。

您可以在 [Office 365 管理入口網站](https://portal.office365.com/)中管理使用者的 Office 365 授權。閱讀更多關於 [Office 365 服務方案](http://technet.microsoft.com/library/office-365-plan-options.aspx)的資訊。


## Office 2013 Professional Plus 試用版 
在 RemoteApp 30 天的試用版期間，您可以使用 Office 2013 Professional Plus (試用版) 範本映像來建立 RemoteApp 收藏。您可以透過使用者的 Azure Active Directory 工作帳戶或 Microsoft 帳戶，將他們指派至這個試用版收藏。不需再另外訂閱。

這是能親自在 RemoteApp 中體驗和感受 Office 的絕佳選項。不過，此選項僅適用於評估和測試。使用 Office 2013 Professional Plus (試用版) 範本映像建立的 RemoteApp 收藏無法轉換成實際執行模式，在試用期結束後就會停用。

## 從試用版切換到實際執行
開始 30 天的免費試用後，入口網站中 RemoteApp 區段的備註會告知您距離轉換為付費帳戶前所剩餘的試用天數。您可以使用這個備註中的連結來啟用您的帳戶並切換成實際執行模式。

啟用帳戶後，將會影響您帳戶中的所有 RemoteApp 收藏。

- 透過 Windows Server 2012 R2 或 Office 365 ProPlus 範本映像執行的收藏將順利地轉換成實際執行。所有使用者資料與設定 (包括進行中的工作階段) 都將維持不變。
- 如果您已上傳自訂範本映像，使用這些映像的收藏也將順利轉換。
- Office 2013 Professional Plus (試用版) 範本映像僅適用於評估。透過此範本映像執行的收藏無法轉換成實際執行。它們會成為「停用」狀態。


如果您在試用期過後還未轉換成實際執行模式，將會停用您的 RemoteApp 收藏。別擔心，您的設定和使用者資料會持續儲存 90 天，因此，您仍可以啟用您的服務和切換到實際執行模式，不會遺失任何資料。
 

<!---HONumber=62-->