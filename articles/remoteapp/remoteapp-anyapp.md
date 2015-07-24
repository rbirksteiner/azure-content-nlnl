<properties
   pageTitle="使用 RemoteApp 在任何裝置上執行任何應用程式"
   description="了解如何使用 RemoteApp 與使用者共用任何應用程式。"
   services="remoteapp"
   documentationCenter=""
   authors="lizap"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="05/28/2015"
   ms.author="elizapo"/>

# 使用 RemoteApp 在任何裝置上執行任何應用程式

本教學課程顯示如何在任何裝置的任何位置立即執行 Windows 應用程式。不論是 Internet Explorer 6、10 年前撰寫的自訂應用程式還是 Office 應用程式，您的使用者不再需要繫結至數個應用程式的特定作業系統 (如 Windows XP)。

使用 RemoteApp，您的使用者也可以使用專屬 Android 或 Apple 裝置，以及取得與 Windows (或 Windows Phone) 上的相同經驗。完成方式是在 Azure 的 Windows 虛擬機器集合中裝載 Windows 應用程式，而您的使用者可以從具有網際網路連線的任何地方進行存取。讓我們開始吧！

在本教學課程中，我們會與所有使用者共用 Access。因為 Access 是一個資料庫，而且我們想要使用該資料庫，所以我們會進行一些額外的步驟，讓使用者存取 Access 資料共用。

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]


## 在 RemoteApp 中建立集合

由建立集合開始。集合是做為您應用程式和使用者的容器。每個集合都是根據映像，而您可以建立專屬映像或使用訂用帳戶所提供的映像。在本教學課程中，我們使用 Office 2013 試用版映像，而此映像包含我們想要共用的應用程式。

1. 在 Azure 管理入口網站中，在左導覽中向下捲動，直到看到 Azure RemoteApp。開啟該頁面。
2. 按一下 [**建立 RemoteApp 集合**]。
3. 按一下 [**快速建立**]，然後輸入集合的名稱。
4. 選取您想要用來建立集合的區域。最佳的體驗是選取地理上最接近您使用者存取應用程式之位置的區域。例如，在本教學課程中，使用者將位在華盛頓州的雷德蒙德。最接近的 Azure 區域是**美國西部**。
5. 選取您想要使用的計費方案。基本計費方案是大型 Azure VM 上有 16 位使用者，而標準計費方案是大型 Azure VM 上有 10 位使用者。基本方案一般最適用於資料項目類型工作流程。在生產力應用程式 (如 Office) 中，您想要使用標準方案。
6. 最後，選取 Office 2013 Professional 映像。此映像包含 Office 2013 應用程式。  
7. 現在，按一下 [**建立 RemoteApp 集合**]。

![在 RemoteApp 中建立雲端集合](./media/remoteapp-anyapp/ra-anyappcreatecollection.png)

這會開始建立您的集合，但是它可能需要一個小時的時間。

現在，您可以開始新增使用者。

## 與使用者共用應用程式

成功建立集合之後，即可開始將 Access 發佈給使用者，並新增應該具有其存取權的使用者。

如果您在建立集合時離開 Azure RemoteApp 節點，請從 Azure 首頁回到該節點開始。

1. 按一下左導覽中的 [**RemoteApp**]。
2. 按一下您先前建立的集合來存取其他選項和設定集合。![新的 RemoteApp 雲端集合](./media/remoteapp-anyapp/ra-anyappcollection.png)
3. 在 [**發佈**] 索引標籤上，按一下畫面底部的 [**發佈**]，然後按一下 [**發佈開始功能表程式**]。![發佈 RemoteApp 程式](./media/remoteapp-anyapp/ra-anyapppublish.png)
4. 從清單中，選取您想要發佈的應用程式。基於我們的目的，我們已選擇 [Access]。按一下 [完成]。等候應用程式完成發佈。![在 RemoteApp 中發佈 Access](./media/remoteapp-anyapp/ra-anyapppublishaccess.png)


1. 應用程式完成發佈之後，請前往 [**使用者存取**] 索引標籤，新增所有需要存取您應用程式的使用者。輸入您使用者的使用者名稱 (電子郵件地址)，然後按一下 [**儲存**]。![將使用者新增至 RemoteApp](./media/remoteapp-anyapp/ra-anyappaddusers.png)


1. 現在，就可以開始告訴您的使用者有關這些新應用程式的存取方式。若要這樣做，請將使用者指向遠端桌面用戶端下載 URL 的電子郵件傳送給您的使用者。![RemoteApp 的用戶端下載 URL](./media/remoteapp-anyapp/ra-anyappurl.png)

## 設定 Access 的存取權

部分應用程式在透過 RemoteApp 部署之後，需要進行額外的設定。尤其，針對 Access，我們要在 Azure 上建立任何使用者都能存取的檔案共用 (如果您不想這麼做，您可以建立[混合式集合](remoteapp-create-hybrid-deployment.md) [而不是我們的雲端集合]，可讓您的使用者存取本機網路上的檔案和資訊。) 然後，我們需要告訴使用者將其電腦上的本機磁碟機對應至 Azure 檔案系統。

第一部分是您以系統管理員身分執行的作業。然後，是您的使用者執行的一些步驟。

1. 請從發佈命令列介面 (cmd.exe) 開始。在 [**發佈**] 索引標籤中，選取 [**cmd**]，然後按一下 [**發佈 > 使用路徑發佈程式**]。
2. 輸入應用程式和路徑的名稱。基於我們的目的，使用 "File Explorer" 做為名稱並使用 "%SYSTEMDRIVE%\\windows\\explorer.exe" 做為路徑。![發佈 cmd.exe 檔案。](./media/remoteapp-anyapp/ra-publishcmd.png)
3. 現在，您需要建立 Azure [儲存體帳戶](../storage-create-storage-account.md)。我們命名為 "accessstorage"，因此請選擇對您有意義的名稱 (只能有一個 "accessstorage")。![Azure 儲存體帳戶](./media/remoteapp-anyapp/ra-anyappazurestorage.png)
4. 現在，回到您的儀表板，以取得您儲存體的路徑 (端點位置)。您將立即使用此項，因此請確定在某個位置複製它。

![儲存體帳戶路徑](./media/remoteapp-anyapp/ra-anyappstoragelocation.png) 5.接著，建立儲存體帳戶之後，您需要有主要存取金鑰。按一下 [**管理存取金鑰**]，然後複製主要存取金鑰。6.現在，設定儲存體帳戶的內容，並建立 Access 的新檔案共用。在提升權限的 Windows PowerShell 視窗中，執行下列 Cmdlet：

        $ctx=New-AzureStorageContext <account name> <account key>
    	$s = New-AzureStorageShare <share name> -Context $ctx

	So for our share, these are the cmdlets we run:

	    $ctx=New-AzureStorageContext accessstorage <key>
    	$s = New-AzureStorageShare <share name> -Context $ctx


現在是使用者的部分。首先，讓您的使用者安裝 [RemoteApp 用戶端](remoteapp-clients.md)。接下來，使用者必須將磁碟機從其帳戶對應至您建立的 Azure 檔案共用，並新增其 Access 檔案。這是他們執行的作業：

1. 在 RemoteApp 用戶端中，存取已發佈的應用程式。啟動 cmd.exe 程式。
2. 執行下列命令，以將電腦中的磁碟機對應至檔案共用：

		net use z: \<accountname>.file.core.windows.net<share name> /u:<user name> <account key>

	如果您將 **/persistent** 參數設定為 yes，對應的磁碟機將在工作階段之間持續保存。
1. 現在，從 RemoteApp 啟動 File Explorer 應用程式。將您要在共用的應用程式中使用的任何 Access 檔案複製至檔案共用。![將 Access 檔案放在 Azure 共用中](./media/remoteapp-anyapp/ra-anyappuseraccess.png)
1. 最後，開啟 Access，然後開啟您剛共用的資料庫。您應該會看到從雲端執行的 Access 中的資料。![從雲端執行的實際 Access 資料庫](./media/remoteapp-anyapp/ra-anyapprunningaccess.png)

現在，您可以在任何裝置上使用 Access - 只要確定您安裝 RemoteApp 用戶端。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟

現在，您已經熟悉如何建立集合，請嘗試建立[使用 Office 365 的集合](remoteapp-tutorial-o365anywhere.md)。或者，您可以建立可存取區域網路的[混合式集合](remoteapp-create-hybrid-deployment.md)。

<!--Image references-->

<!---HONumber=July15_HO2-->