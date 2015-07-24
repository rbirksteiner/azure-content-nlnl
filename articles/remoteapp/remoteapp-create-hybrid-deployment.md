<properties 
	pageTitle="如何建立 Azure RemoteApp 的混合式收藏" 
	description="了解如何建立連接內部網路的 RemoteApp 部署。" 
	services="remoteapp" 
	documentationCenter="" 
	authors="lizap" 
	manager="mbaldwin" 
	editor=""/>

<tags 
	ms.service="remoteapp" 
	ms.workload="compute" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="elizapo"/>

# 如何建立 Azure RemoteApp 的混合式收藏

RemoteApp 收藏分成兩種：

- 雲端：完全在 Azure 之中，可使用 Azure 管理入口網站中的 [快速建立] 選項建立。  
- 混合式：包含內部部署存取所需的虛擬網路，需使用管理入口網站中的 [使用 VNET 建立] 選項建立。

本教學課程將逐步引導您完成建立混合式收藏的程序。有八個步驟：

1.	決定要對您的收藏使用哪個[映像](remoteapp-imageoptions.md)。您可以建立自訂映像，或使用您的訂用帳戶隨附的其中一個 Microsoft 映像。
2. 設定虛擬網路。
2.	建立 RemoteApp 收藏。
2.	將您的收藏連結到虛擬網路。
3.	將範本映像新增到您的收藏。
4.	設定目錄同步處理。RemoteApp 要求用下列方式與 Azure Active Directory 整合：1) 設定具有 [密碼同步] 選項的 Azure Active Directory 同步作業，或 2) 設定不具 [密碼同步] 選項的 Azure Active Directory 同步作業，但使用同盟至 AD FS 的網域。查看 [Active Directory 搭配 RemoteApp 的組態資訊](remoteapp-ad.md)。
5.	發佈 RemoteApp 應用程式。
6.	設定使用者存取。

**開始之前**

在建立收藏之前，您必須執行下列作業：

- [註冊](http://azure.microsoft.com/services/remoteapp/) RemoteApp。 
- 在 Active Directory 中建立使用者帳戶，以做為 RemoteApp 服務帳戶。限制此帳戶的權限，使其只能將機器加入網域中。
- 收集內部部署網路的相關資訊：IP 位址資訊和 VPN 裝置詳細資料。
- 安裝 [Azure PowerShell](../install-configure-powershell.md) 模組。
- 收集您想授與存取權之使用者的相關資訊。您將需要每個使用者的 Azure Active Directory 使用者主體名稱 (例如，name@contoso.com)。
- 選擇範本映像。RemoteApp 範本映像包含您要為使用者發佈的應用程式與程式。如需詳細資訊，請參閱 [RemoteApp 映像選項](remoteapp-imageoptions.md)。 
- [設定 RemoteApp 的 Azure Active Directory](remoteapp-ad.md)。



## 步驟 1：設定虛擬網路
您可以部署混合式 RemoteApp 收藏，使用現有的 Azure 虛擬網路，或者可以建立新的虛擬網路。虛擬網路可讓您的使用者透過 RemoteApp 遠端資源存取您本機網路上的資料。使用 Azure 虛擬網路可以讓您的收藏直接從網路存取其他 Azure 服務和部署到該虛擬網路的虛擬機器。

### 建立 Azure VNET 並將它加入您的 Active Directory 部署

首先，建立[虛擬網路](https://msdn.microsoft.com/library/azure/dn631643.aspx)。您可以在 Azure 管理入口網站的 [網路] 索引標籤上進行。您必須將虛擬網路連線到同步到您 Azure Active Directory 租用戶的 Active Directory 部署。

如需詳細資訊，請參閱[關於管理入口網站中的虛擬網路設定](https://msdn.microsoft.com/library/azure/jj156074.aspx)。

### 確定您的虛擬網路已為 RemoteApp 準備就緒
在建立 RemoteApp 收藏之前，請先確定您的虛擬網路已準備就緒。您可以執行下列動作來驗證：

1. 在您剛才為 RemoteApp 建立的虛擬網路子網路內建立 Azure 虛擬機器。
2. 使用遠端桌面連線到虛擬機器。(按一下 [連線])。
3. 將虛擬機器加入您要用於 RemoteApp 的同一個 Active Directory 部署。

有作用嗎？ 您的虛擬網路和子網路已為 RemoteApp 準備就緒！

您可以在[這裡](https://msdn.microsoft.com/library/azure/jj156003.aspx)找到有關建立 Azure 虛擬機器並將它們連線到 RemoteApp 的詳細資訊。

## 步驟 2：建立 RemoteApp 收藏 ##



1. 在 [Azure 管理入口網站](http://manage.windowsazure.com)中，移至 RemoteApp 頁面。
2. 按一下 [新增] > [使用 VNET 建立]。
3. 輸入收藏的名稱。
4. 選擇您要使用的方案 - 標準或基本。
5. 按一下 [建立 RemoteApp 收藏]。

在建立 RemoteApp 收藏後，請移至 RemoteApp [快速入門] 頁面以繼續設定步驟。

## 步驟 3：將您的收藏連結到虛擬網路 ##

 
1. 在 [快速入門] 頁面上，按一下 [連結虛擬網路]。
2. 從下拉式清單選擇您要使用的虛擬網路。
3. 選擇您要使用的區域，然後確定欄位中顯示正確的訂用帳戶。 
5. 回到 [快速入門] 頁面上，按一下 [加入本機網域]。將 RemoteApp 服務帳戶新增至您的本機 Active Directory 網域。您將需要網域名稱、組織單位、服務帳戶的使用者名稱和密碼。 

	這是您按照[設定 Azure RemoteApp 的 Active Directory](remoteapp-ad.md) 中的步驟所收集到的資訊。


## 步驟 4：連結到 RemoteApp 映像 ##

RemoteApp 範本映像包含您要與使用者共用的程式。您可以建立新的[範本映像](remoteapp-imageoptions.md)，或連結到現有的映像 (已匯入或上傳至 Azure RemoteApp 的映像)。您也可以連結到包含 Office 365 或 Office 2013 (供試用) 程式的其中一個 RemoteApp [範本映像](remoteapp-images.md)。

如果您要上傳新映像，您必須輸入名稱，並選擇映像的位置。在精靈的下一頁，您會看見一組 PowerShell Cmdlet - 從提高權限的 Windows PowerShell 提示複製並執行這些 Cmdlet，可上傳指定的映像。

如果您要連結至現有的範本映像，請直接指定映像名稱、位置和相關聯的 Azure 訂閱。



## 步驟 5：設定 Active Directory 目錄同步處理 ##

RemoteApp 要求用下列方式與 Azure Active Directory 整合：1) 設定具有 [密碼同步] 選項的 Azure Active Directory 同步作業，或 2) 設定不具 [密碼同步] 選項的 Azure Active Directory 同步作業，但使用同盟至 AD FS 的網域。如需規劃資訊和詳細步驟，請參閱[目錄同步處理藍圖](http://msdn.microsoft.com//library/azure/hh967642.aspx)。

## 步驟 6：發佈 RemoteApp 應用程式 ##

RemoteApp 應用程式是您提供給使用者的應用程式或程式。此程式位於您為收藏上傳的範本映像中。當使用者存取應用程式時，應用程式看似會在其本機環境中執行，但實際上是在 Azure 中執行。

您必須先將應用程式發佈至使用者摘要，您的使用者才能存取 RemoteApp 應用程式；使用者摘要是您的使用者可透過遠端桌面用戶端存取的可用應用程式清單。
 
您可以將多個應用程式發佈至 RemoteApp 收藏。在 RemoteApp 發佈頁面中按一下 [發佈] 以新增應用程式。您可以從範本映像的 [開始] 功能表發佈，或藉由為應用程式指定範本映像的路徑來發佈。如果您選擇從 [開始] 功能表新增，請選擇要發佈的應用程式。如果您選擇提供應用程式的路徑，請提供應用程式的名稱，以及應用程式在範本映像上的安裝路徑。

## 步驟 7：設定使用者存取 ##

在您建立 RemoteApp 收藏之後，現在您必須新增可使用遠端資源的使用者。您要為其提供存取權的使用者，必須存在於與您用來建立此 RemoteApp 收藏的訂用帳戶相關聯的 Active Directory 租用戶中。

1.	在 [快速入門] 頁面上，按一下 [Configure user access]。 
2.	輸入工作帳戶 (來自於 Active Directory)，或是您要為其授與存取權的 Microsoft 帳戶。

	**注意：**

	確定您使用的是 “user@domain.com” 格式。

	如果您的收藏中使用 Office 365 ProPlus，您必須為使用者使用 Active Directory 身分識別。這有助於驗證授權。


3.	在驗證使用者之後，按一下 [儲存]。


## 後續步驟 ##
至此，您已成功建立並部署 RemoteApp 混合式收藏。下一個步驟是要讓使用者下載並安裝遠端桌面用戶端。您可以在 RemoteApp 的 [快速入門] 頁面上找到用戶端的 URL。接著，請讓使用者登入用戶端，並存取您所發佈的應用程式。


 

<!---HONumber=July15_HO2-->