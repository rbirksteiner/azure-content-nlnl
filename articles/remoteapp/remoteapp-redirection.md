<properties 
    pageTitle="在 Azure RemoteApp 中使用重新導向" 
    description="了解如何在 RemoteApp 中設定和使用重新導向" 
    services="remoteapp" 
    solutions="" 
	documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/21/2015" 
    ms.author="elizapo" />

# 在 Azure RemoteApp 中使用重新導向

裝置重新導向可讓您的使用者使用與其本機電腦、手機或平板電腦連接的裝置，來與遠端應用程式互動。l比方說，如果您透過 RemoteApp 提供 Skype，您的使用者就必須在電腦安裝相機才能使用 Skype。印表機、麥克風、監視器以及各種不同以 USB 連接的周邊裝置也是如此。

RemoteApp 會利用遠端桌面通訊協定 (RDP) 與 RemoteFX 來提供重新導向功能。

## 依預設會何種重新導向功能？
當您使用 RemoteApp 時，預設會啟用下列重新導向功能。括號中的資訊顯示 RDP 設定。

- 在本機電腦上播放聲音 (**在這部電腦上播放**)。(audiomode:i:0)
- 從本機電腦擷取音訊並傳送到遠端電腦 (**從這部電腦錄製**)。(audiocapturemode:i:1)
- 列印到本機印表機 (redirectprinters:i:1)
- COM 連接埠 (redirectcomports:i:1)
- 智慧卡裝置 (redirectsmartcards:i:1)
- 剪貼簿 (能夠複製和貼上) (redirectclipboard:i:1)
- 清除字型平滑處理 (允許字型平滑處理:i:1)
- 重新導向所有支援的隨插即用裝置。(devicestoredirect:s:*)

## 還有其他哪些重新導向功能？
預設會停用兩個重新導向選項：

- 磁碟機重新導向 (磁碟機對應)：您本機電腦的磁碟機會與遠端作業階段中的磁碟機對應。這可讓您在遠端工作階段工作時，儲存或開啟本機磁碟機中的檔案。 
- USB 重新導向：您可以在遠端工作階段期間使用與本機電腦連接的 USB 裝置。

## 在 RemoteApp 中變更重新導向設定
您可以使用 Microsoft Azure PowerShell 搭配 SDK，變更集合的裝置重新導向設定。在您安裝新的 PowerShell 與 SDK 之後，請先依照[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)中所述進行設定以管理您的訂閱。

接著使用和下面類似的命令來設定自訂 RDP 屬性：

	Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:*"
    
(請注意，*`n* 是當成個別屬性之間的分隔符號使用)。

若要取得已設定的自訂 RDP 屬性清單，請執行下列 Cmdlet。請注意，只會將自訂屬性顯示為輸出，而預設屬性則否：

    Get-AzureRemoteAppCollection -CollectionName <collection name> 
 
當您設定自訂屬性時，每次都要指定所有自訂屬性，不然就會將設定恢復成停用。

### 常見範例
使用下列 Cmdlet 可啟用磁碟機重新導向：

	Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*”

使用這個 Cmdlet 可同時啟用 USB 與磁碟機重新導向：

	Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:*"

使用這個 Cmdlet 可停用剪貼簿共用：

	Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "redirectclipboard:i:0”

在您測試變更之前，請務必將集合中的所有使用者完全登出 (而非僅中斷連線)。為確保將使用者完全登出，請在 Azure 入口網站移至該集合中的 [工作階段] 索引標籤，然後將已中斷連線或已登入的任何使用者登出。有時候可能需要花費數秒的時間，磁碟機才能顯示在工作階段內的檔案總管中。

## 變更 Windows 用戶端的 USB 重新導向設定

如果您想要在與 RemoteApp 連線的電腦上使用 USB 重新導向，必須進行 2 個動作。1 - 您的系統管理員必須使用 Azure PowerShell 在集合層級啟用 USB 重新導向。2 - 在您想要使用 USB 重新導向的每個裝置上，都必須啟用允許重新導向的群組原則。每個想要使用 USB 重新導向的使用者都必須完成這個步驟。
   
> [AZURE.NOTE]只支援 Windows 電腦使用 Azure RemoteApp 提供 USB 重新導向。

### 為 RemoteApp 集合啟用 USB 重新導向
使用下列 Cmdlet 可在集合層級啟用 USB 重新導向：
    Set-AzureRemoteAppCollection -CollectionName <collection_name> -CustomRdpProperty "nusbdevicestoredirect:s:*"

### 為用戶端電腦啟用 USB 重新導向

若要在電腦設定 USB 重新導向設定：

1. 開啟本機群組原則編輯器 (GPEDIT.MSC)。(從命令提示字元中執行 gpedit.msc)。
2. 開啟 [電腦設定]\\[原則]\\[系統管理範本]\\[Windows 元件]\\[遠端桌面服務]\\[遠端桌面連線用戶端]\\[RemoteFX USB 裝置重新導向]。
3. 按兩下 [允許 RDP 重新導向這部電腦中其他支援的 RemoteFX USB 裝置]。
4. 選取 [已啟用]，然後在 [RemoteFX USB 重新導向存取權限] 中選取系統管理員與使用者。
5. 以系統管理權限開啟命令提示字元，然後執行下列命令： 

    gpupdate /force
6. 重新啟動電腦。

您也可以使用群組原則管理工具，為網域中的所有電腦建立和套用 USB 重新導向原則：

1. 以網域管理員的身分登入網域控制站。
2. 開啟 [群組原則管理主控台]。(按一下 [開始] > [系統管理工具] > [群組員則管理]。)
3. 瀏覽到您想要建立原則的網域或組織單位。
4. 右键单击 **默认域策略**, ，然后单击 **编辑**。
5. 開啟 [電腦設定]\\[原則]\\[系統管理範本]\\[Windows 元件]\\[遠端桌面服務]\\[遠端桌面連線用戶端]\\[RemoteFX USB 裝置重新導向]。
6. 按兩下 [允許 RDP 重新導向這部電腦中其他支援的 RemoteFX USB 裝置]。
7. 選取 [已啟用]，然後在 [RemoteFX USB 重新導向存取權限] 中選取系統管理員與使用者。
8. 按一下 [確定]。 

<!--HONumber=54--> 