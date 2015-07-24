<properties 
	pageTitle="開始使用 Azure AD Connect" 
	description="了解如何下載、安裝和執行 Azure AD Connect 的安裝精靈。" 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# 開始使用 Azure AD Connect

<div class="dev-center-tutorial-selector sublanding">
<a href="../active-directory-aadconnect/" title="它是什麼">它是什麼</a> <a href="../active-directory-aadconnect-how-it-works/" title="運作方式">運作方式</a> <a href="../active-directory-aadconnect-get-started/" title="開始使用" class="current">開始使用</a> <a href="../active-directory-aadconnect-whats-next/" title="後續步驟">後續步驟</a> <a href="../active-directory-aadconnect-learn-more/" title="深入了解">深入了解</a>
</div>


下列文件將協助您開始使用 Azure Active Directory Connect。本文件說明使用 Azure AD Connect 的快速安裝。如需自訂安裝的詳細資訊，請參閱 [Azure AD Connect 的自訂安裝](active-directory-aadconnect-get-started-custom.md)。如需從 DirSync 升級至 Azure AD Connect 的詳細資訊，請參閱[升級 DirSync 至 Azure Active Directory Connect](active-directory-aadconnect-dirsync-upgrade-get-started.md)。

## 下載 Azure AD Connect



若要開始使用 Azure AD Connect，您可以使用下列連結下載最新版本：[下載 Azure AD Connect](http://go.microsoft.com/fwlink/?LinkID=615771)

## 安裝 Azure AD Connect 之前
在您快速設定安裝 Azure AD Connect 之前，您將需要一些項目。


 
- Azure 訂用帳戶或 [Azure 試用訂用帳戶](http://azure.microsoft.com/pricing/free-trial/) - 這僅需要用來存取 Azure 入口網站，而不會用於 Azure AD Connect。如果您正在使用 PowerShell 或 Office 365，則您不需要 Azure 訂用帳戶來使用 Azure AD Connect。
- 想要與其整合之 Azure AD 租用戶的 Azure AD 全域管理員帳戶
- AD 網域控制站或成員伺服器 (含 Windows Server 2008 或更新版本)
- 本機 Active Directory 的企業系統管理員帳戶
- 選用：測試使用者帳戶來驗證同步處理。 


對於多個樹系或同盟登入等「自訂選項」，請在[這裡](active-directory-aadconnect-get-started-custom.md)了解其他需求。


## 快速安裝 Azure AD Connect
選取 [快速設定] 是預設選項，而且是其中一個最常見的案例。這樣做時，Azure AD Connect 會使用密碼雜湊同步處理選項部署同步處理。這僅適用於單一樹系，而且可讓您的使用者使用其內部部署密碼來登入雲端。使用 [快速安裝] 會在完成安裝之後自動開始同步處理 (不過您可以選擇不開啟)。使用此選項，只要簡短地按幾下即可將內部部署目錄擴充至雲端。

<center>![歡迎使用 Azure AD Connect](./media/active-directory-aadconnect-get-started/welcome.png)</center>

### 使用快速設定安裝 Azure AD Connect
--------------------------------------------------------------------------------------------

1. 以企業系統管理員身分登入您想要安裝 Azure AD Connect 的伺服器。這應該是您想要做為同步處理伺服器的伺服器。
2. 瀏覽並按兩下 AzureADConnect.msi
3. 在 [歡迎] 畫面上，選取同意授權條款的方塊，然後按一下 [繼續]。
4. 在 [快速設定] 畫面上，按一下 [使用快速設定]。
<center>![歡迎使用 Azure AD Connect](./media/active-directory-aadconnect-get-started/express.png)</center>
6. 在 [連接到 Azure AD] 畫面上，輸入您的 Azure AD 的 Azure 全域系統管理員使用者名稱和密碼。按 [下一步]。
8. 在 [連接到 AD DS] 畫面上輸入企業系統管理員帳戶的使用者名稱和密碼。按 [下一步]。
<center>![歡迎使用 Azure AD Connect](./media/active-directory-aadconnect-get-started/install4.png)</center>
9. 在 [準備好設定] 畫面中，按一下 [安裝]。
	- 在 [準備好設定] 頁面上，您可以選擇取消核取 [設定一完成，即開始同步處理程序] 核取方塊。如果這麼做，精靈會設定同步處理但會停用該工作而不執行，直到您在 [工作排程器] 將它重新啟用。一旦啟用工作，每隔三小時就會執行同步處理。
	- 此外，您也可以選擇核取對應 [Exchange 混合式部署] 的核取方塊，以設定同步處理服務。如果您不打算在雲端和內部部署設定 Exchange 信箱，則不需要此設定。

<center>![歡迎使用 Azure AD Connect](./media/active-directory-aadconnect-get-started/readyinstall.png)</center>
8. 當安裝完成時，按一下 [結束]。


<br> <br>

如需使用快速安裝的影片，請參閱下列內容：

<center>[AZURE.VIDEO azure-active-directory-connect-express-settings]</center>



## 驗證安裝

成功安裝 Azure AD Connect 之後，您可以登入 Azure 入口網站並檢查上次同步處理時間，以確認正在進行同步處理。

1.  登入 Azure 入口網站。
2.  選取左邊的 [Active Directory]。
3.  按兩下您剛才用來設定 Azure AD Connect 的目錄。
4.  在頂端，選取 [目錄整合]。注意上次同步處理時間。

<center>![快速安裝](./media/active-directory-aadconnect-get-started/verify.png)</center>

## 後續步驟
您已經安裝 Azure AD Connect，現在可以使用[這裡](active-directory-aadconnect-whats-next.md)的連結繼續安裝後的工作，例如指派您的使用者 Azure AD Premium 或 Enterprise Mobility 授權或設定其他選項。

 

<!---HONumber=62-->