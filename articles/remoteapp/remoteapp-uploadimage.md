
<properties 
    pageTitle="上傳 Azure RemoteApp 的自訂映像"
    description="了解如何上傳 Azure RemoteApp 的自訂映像" 
    services="remoteapp" 
    solutions="" documentationCenter="" 
    authors="ericorman" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/28/2015" 
    ms.author="ericor" />



# 上傳 Azure RemoteApp 的自訂映像

現在，您已建立自訂範本映像或已更新變更，您必須將該映像上傳至您的 Azure RemoteApp 映像庫。使用這些步驟。


## 開始之前

1.      請確認您的自訂映像符合[映像需求](remoteapp-imagereqs.md)和[應用程式需求](remoteapp-appreqs.md)。
2.      安裝 [Azure PowerShell 模組](../install-configure-powershell.md)。

## 逐步解說如何上傳自訂映像

1.      開啟 Azure 管理入口網站，並瀏覽至 [RemoteApp] 頁面。
2.      在 [範本映像] 索引標籤上，按一下頁面底部的 [上傳]。
4.      為您的映像輸入一個易記名稱，並指定儲存體帳戶位置。請確定此位置會是與 RemoteApp 集合相同的位置或是您要建立 RemoteApp 集合的位置。 
5.      當系統出現提示時，請將指令碼下載到本機電腦。
6.      將文字方塊中的命令參數複製到您的剪貼簿。
7.      開啟提升權限的 Windows PowerShell 視窗  
8.      從提升權限的 Windows PowerShell 視窗，瀏覽至您下載指令碼的相同目錄。
9.      貼上複製的命令，並按 **Enter** 鍵。

	系統便會開始上傳程序，持續時間會取決於許多因素，其中包括您的網路速度和影像的大小

11.    如果因為網路中斷或類似原因導致上傳失敗，您可以繼續您已開始的上傳程序。若要繼續上傳，您可以再次使用相同的命令列來執行指令碼。

> [AZURE.WARNING]切勿修改上傳指令碼。已實作可確保映像符合映像需求和應用程式需求的特定檢查。

## 常見問題

- 請確定使用的是 Windows PowerShell，而不是 Azure PowerShell。您必須安裝 Azure PowerShell 模組，因為上傳過程中會需要某些特定模組。 
- 切勿更改指令碼，該指令碼已提供驗證，方便您使用。
- 如果 vhd 檔案在上傳期間遭到鎖定，請將檔案複製或移動到新位置，並再次嘗試上傳。有些 Windows 程序可能會阻止上傳。  
 

<!---HONumber=July15_HO1-->