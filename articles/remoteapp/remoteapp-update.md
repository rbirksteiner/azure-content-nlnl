<properties
   pageTitle="更新 Azure RemoteApp 收藏"
   description="了解如何更新 Azure RemoteApp 收藏"
   services="remoteapp"
   documentationCenter=""
   authors="lizap"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="compute"
   ms.date="06/09/2015"
   ms.author="elizapo"/>

# 更新 Azure RemoteApp 中的收藏

無可避免地，總會有一次，您需要更新 Azure RemoteApp 收藏中的應用程式或映像。如果您是使用 Azure RemoteApp 訂用帳戶隨附的其中一個映像，則在雲端或混合式收藏中，任何和所有的更新都是由 Azure RemoteApp 本身處理，因此您可以放手不管。

不過，如果您是使用自訂映像 (從頭建立的映像，或是修改其中一個我們的映像所建立的映像)，則您需負責維護映像和應用程式。

因此，您該如何更新您的收藏？ 相當簡單：

1. 更新您在收藏中使用的映像。套用所需的任何修補程式或更新，然後將它儲存成新的名稱。
2. [上傳](remoteapp-uploadimage.md)或[匯入](remoteapp-image-on-azurevm)該映像至 RemoteApp。
3. 現在，在 [收藏] 頁面上，按一下 [**更新**]。
4. 從 [**範本映像**] 清單中選擇新的映像。
4. 以下是麻煩的部分 - 您必須決定如何處理任何目前正在使用收藏中應用程式的使用者。您有下列選擇：
	- **更新後給與使用者 60 分鐘**。一完成更新，Azure RemoteApp 就會顯示訊息給任何作用中使用者，告訴他們儲存工作並登出，然後重新登入。60 分鐘之後，任何未登出的作用中使用者將自動登出。使用者可以立即重新登入。 
	- **立即登出使用者**。一完成更新，就會自動登出所有使用者，而且沒有任何警告。如果您選擇此選項，使用者可能會遺失資料。不過，它們可以立即重新連接至應用程式。

1. 按一下核取記號以啟動更新。

 

<!---HONumber=July15_HO2-->