
<properties 
    pageTitle="RemoteApp 雲端集合疑難排解 - 建立"
    description="了解如何疑難排解 RemoteApp 雲端集合建立失敗" 
    services="remoteapp" 
    solutions="" documentationCenter="" 
    authors="vkbucha" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/28/2015" 
    ms.author="vikbucha" />



# 建立 RemoteApp 雲端集合疑難排解

Azure 管理入口網站中的常見錯誤：

	DNS server could not be reached
	ProvisioningTimeout

雲端集合通常會因為您在建立期間使用自訂映像而失敗。如果您看到上述其中一個錯誤，而且您正在使用自訂映像來建立集合，請檢查下列事項：

- 確定上傳的自訂映像符合映像需求。 
- 最常見的問題是映像的 Sysprep 處理不正確。  
- 確認映像可以在 HYPER-V 內開機，或嘗試直接在 Azure 訂閱中使用映像建立 IAAS VM。如果 VM 無法開機且未啟動，則通常表示未正確準備自訂映像。請遵循＜如何為 RemoteApp 建立自訂範本映像＞來確認已建立自訂映像

如果您使用訂閱隨附的其中一個 Microsoft 映像，請嘗試再次建立集合。如果問題仍存在，請連絡 Microsoft 支援。

	PlatformImageTrialModeOnly

如果看到這個錯誤，通常表示您已經升級至付費帳戶，但您正在嘗試使用 Microsoft 提供的映像，而該映像只在服務的試用模式期間有效。在此情況下，請嘗試再次建立您的雲端集合，但務必指定正確的映像。
 

<!---HONumber=July15_HO1-->