<properties 
    pageTitle="建立以 Azure VM 為基礎的 Azure RemoteApp 映像"
    description="了解如何從 Azure 虛擬機器建立 RemoteApp 映像。" 
    services="remoteapp" 
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



# 建立以 Azure 虛擬機器為基礎的 Azure RemoteApp 映像

您可以從 Azure 虛擬機器建立 RemoteApp 映像 (其中保存您在收藏中共用的應用程式)。我們也在符合所有 RemoteApp 映像需求的 Azure VM 資源庫中加入了虛擬機器映像，如果您要的話，您可以將該 VM 映像做為您自己 VM 的起點使用。只需在映像庫中尋找「Windows Server 遠端桌面工作階段主機」。

共有兩個步驟：建立映像，然後將它從 Azure VM 映像庫上傳至 Azure RemoteApp。

## 建立以 Azure VM 為基礎的自訂映像

使用這些步驟來建立以 Azure VM 為基礎的映像。

1. 建立 Azure 虛擬機器。您可以使用 Azure 虛擬機器資源庫中的「Windows Server 遠端桌面工作階段主機」映像。此映像符合所有的 Azure RemoteApp 範本映像需求。 

	如需詳細資訊，請參閱[建立執行 Windows 的 VM](virtual-machines-windows-tutorial.md)。

2. 連接至 VM，並安裝和設定您想要透過 RemoteApp 共用的應用程式。請務必執行您應用程式所需的任何其他 Windows 設定。

	如需詳細資訊，請參閱[如何登入執行 Windows Server 的虛擬機器](virtual-machines-log-on-windows-server.md)。

3. 如果您打算使用 Windows Server 遠端桌面工作階段主機映像，它包含了可確保您的 VM 符合 RemoteApp 必要需求的驗證指令碼。若要執行指令碼，請按兩下桌面上的 **ValidateRemoteAppImage**。在繼續進行下一個步驟之前，請確定已修正指令碼所報告的所有錯誤。

4. SYSPREP 一般化和擷取映像。如需相關指示，請參閱[如何擷取 Windows 虛擬機器作為範本使用](../virtual-machines-capture-image-windows-server.md)。

 

## 將映像匯入 RemoteApp 映像庫

使用下列步驟將新的映像匯入 RemoteApp：

1. 在 [**範本映像**] 索引標籤中：
	- 如果您沒有現有的映像，請按一下 [**上傳或匯入範本映像**]。 
	- 如果您已經有一個以上的映像，請按一下 [**+**] 以新增映像。

2. 選取 [**從您的虛擬機器映像庫匯入映像**]，然後按 [**下一步**]。

3. 在下一個頁面上，從清單中選取自訂映像，並確認您已遵循建立映像時的所列步驟進行。按 [下一步]。
4. 輸入新 RemoteApp 映像的名稱，並挑選一個位置，然後按一下核取記號以開始匯入程序。

> [AZURE.NOTE]您可以將映像從 Azure 虛擬機器支援的任何 Azure 位置，匯入到 Azure RemoteApp 支援的任何 Azure 位置。視位置而定，匯入可能需要多達 25 分鐘的時間。

現在您已經準備好開始建立新的收藏 ([雲端](remoteapp-create-cloud-deployment.md)收藏或[混合式](remoteapp-create-hybrid-deployment.md))，視您的需求而定。
 

<!---HONumber=July15_HO2-->