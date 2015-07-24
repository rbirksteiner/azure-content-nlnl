<properties 
	pageTitle="在混合式雲端中設定 Office 365 Directory 同步處理 (DirSync) 進行測試" 
	description="了解如何在混合式雲端中設定 Office 365 Directory 同步處理 (DirSync) 伺服器進行 IT 專業或開發測試。" 
	services="virtual-network" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-service-management"/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2015" 
	ms.author="josephd"/>

# 在混合式雲端中設定 Office 365 Directory 同步處理 (DirSync) 進行測試

本主題將逐步引導您使用 Microsoft Azure 代管的密碼同步處理建立混合式雲端環境來測試 Office 365 Directory 同步處理 (DirSync)。以下是產生的組態。

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_3.png)
 
這個組態會從您在網際網路上的位置模擬 Azure 生產環境中的 DirSync 伺服器。其中包括：

- 簡化的內部部署網路 (公司網路子網路)。
- Azure (TestVNET) 代管的跨單位部署虛擬網路。
- 站對站 VPN 連線。
- Office 365 FastTrack 試用訂閱。
- DirSync 伺服器以及 TestVNET 虛擬網路中的第二個網域控制站。

這個組態一般可供您開始：

- 對於透過密碼同步處理進行內部部署 Active Directory 網域同步處理的 Office 365 開發和測試應用程式。
- 執行此雲端型 IT 工作負載的測試。

設定此混合式雲端測試環境分為三個主要階段：

1.	設定混合式雲端環境進行測試。
2.	設定 Office 365 FastTrack 試用。
3.	設定 DirSync 伺服器 (DS1)。

如果您還沒有 Azure 訂用帳戶，則可以在[試用 Azure](http://azure.microsoft.com/pricing/free-trial/) 註冊免費試用版。如果您有 MSDN 訂閱，請參閱 [MSDN 訂閱者的 Azure 權益](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)。

## 第 1 階段：設定混合式雲端環境

使用[設定用於測試的混合式雲端環境](virtual-networks-setup-hybrid-cloud-environment-testing.md)主題中的指示。由於這個測試環境不需要公司網路子網路上的 APP1 伺服器，因此可以暫時將它關閉。

這是您目前的組態。

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_1.png)

> [AZURE.NOTE]針對第 1 階段，您也可以設定模擬混合式雲端測試環境。如需指示，請參閱[設定用於測試的模擬混合式雲端環境](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md)。

## 第 2 階段：設定 Office 365 FastTrack 試用

若要開始 Office 365 FastTrack 試用，您需要虛構的公司名稱和 Microsoft 帳戶。建議您使用類似 Contoso 的公司名稱作為公司名稱，這是 Microsoft 範例內容中使用的虛構公司名稱，但是不強制要求這麼做。

接著，註冊新的 Microsoft 帳戶。移至 **http://outlook.com** 並使用電子郵件地址 (例如 user123@outlook.com) 建立一個帳戶。您將使用這個帳戶註冊 Office 365 FastTrack 試用。

接著，註冊新的 Office 365 FastTrack 試用。

1.	以 CORP\\User1 帳戶認證登入 CLIENT1。
2.	開啟 Internet Explorer 並移至 **http://fasttrack.office.com**。3.	按一下 [開始使用 FastTrack]。
4.	在 [開始使用 FastTrack] 頁面的 [首先，註冊 Office 365 試用] 下，按一下 [企業在此註冊]。
5.	在 [步驟 1] 頁面上，填寫頁面，在 [公司電子郵件地址] 中指定新的 Microsoft 帳戶，然後按 [下一步]。
6.	在 [步驟 2] 頁面上，輸入初始 Office 365 帳戶的名稱 (在第一個欄位中)、虛構的公司名稱和密碼。在安全的位置記錄產生的電子郵件地址 (例如 user123@contoso123.onmicrosoft.com) 以及密碼。您將需要這些資訊，才能在階段 3 完成 Active Directory 同步處理工具組態精靈。按 [下一步]。
7.	在 [步驟 3] 頁面上，輸入可收發簡訊的手機或智慧型手機本身的電話號碼，然後按一下 [傳簡訊給我]。
8.	手機收到簡訊後，輸入驗證碼，然後按一下 [建立我的帳戶]。 
9.	Office 365 建立您的帳戶完畢時，按一下 [您準備繼續]。
10.	您現在應該會看見 Office 365 入口網站主頁面。在上方功能區中，按一下 [系統管理]，然後按一下 [Office 365]。Office 365 系統管理中心頁面隨即出現。讓 CLIENT1 保持這個頁面開啟。

這是您目前的組態。

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_2.png)

## 第 3 階段：設定 DirSync 伺服器 (DS1)

首先，在本機電腦的 Azure PowerShell 命令提示字元下，使用下列命令建立 DS1 的 Azure 虛擬機器。在執行這些命令之前，先填入變數值並移除 < and > 字元。

	$ServiceName="<The cloud service name for your TestVNET virtual network>"
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for DS1."
	$cred2=Get-Credential –UserName "CORP\User1" –Message "Now type the password for the CORP\User1 account."
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name DS1 -InstanceSize Medium -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	New-AzureVM –ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

接著，連接到 DS1 虛擬機器。

1.	在 Azure 管理入口網站的 [虛擬機器] 頁面上，按一下 DS1 虛擬機器 [狀態] 欄中的 [執行]。
2.	在工作列上，按一下 [連接]。 
3.	提示開啟 DS1.rdp 時，按一下 [開啟]。
4.	顯示 [遠端桌面連線] 訊息方塊後，按一下 [連接]。
5.	出現輸入認證的提示時，使用這些：
	- 名稱：**CORP\\User1**
	- 密碼：[User1 帳戶密碼]
6.	顯示憑證相關的 [遠端桌面連線] 訊息方塊提示時，按一下 [是]。

接著，設定 Windows 防火牆規則，允許基本連線測試的流量。從 DS1 的系統管理員層級 Windows PowerShell 命令提示字元下，執行這些命令。

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

Ping 命令應該會收到來自 IP 位址 10.0.0.1 的 4 次成功回覆。

接下來，在 Windows PowerShell 命令提示字元下使用此命令，在 DS1 上安裝 .NET 3.5。

	Add-WindowsFeature NET-Framework-Core

接著，在 DS1 上安裝 Directory 同步處理。

1.	執行 Internet Explorer，在網址列中輸入 **http://go.microsoft.com/fwlink/?LinkID=278924**，然後按 ENTER。提示執行 dirsync.exe，按一下 [儲存] 旁邊的箭號，並按一下 [另存新檔]，然後按一下 [儲存] 將檔案儲存於 [下載] 資料夾。如需安裝工具的詳細資訊，請參閱[安裝或升級 Directory 同步處理工具](http://technet.microsoft.com/library/jj151800)。2.	開啟 [下載] 資料夾，並且以滑鼠右鍵按一下 **dirsync** 檔，然後按一下 [以系統管理員身分執行]。
3.	在 Active Directory 同步處理安裝精靈的 [歡迎使用] 頁面上，按 [下一步]。 
4.	在 [授權條款] 頁面上，按一下 [我接受]，然後按 [下一步]。
5.	在 [選取資料夾安裝] 頁面上，按 [下一步]。這可能需要幾分鐘才能完成安裝。
6.	在 [完成] 頁面上，清除 [立即啟動組態精靈]，然後按一下 [完成]。
7.	從 [開始] 畫面中，按一下 [user1]，然後按一下 [登出]。

接著，對於 Office 365 FastTrack 試用啟用 Directory 同步處理。

1.	在 CLIENT1 上，於 [Office 365 系統管理中心] 頁面的左窗格中，按一下 [使用者]，然後按一下 [作用中使用者]。
2.	對於 [Active Directory 同步處理]，按一下 [設定]。
3.	在 [設定和管理 Active Directory 同步處理] 頁面的步驟 3 中，按一下 [啟動]。
4.	出現 [您想要啟動 Active Directory 同步處理？] 的提示時，按一下 [啟動]。啟動後，步驟 3 將顯示 [Active Directory 同步處理已啟動]。
5.	讓 CLIENT1 保持 [設定和管理 Active Directory 同步處理] 頁面開啟。

接著，以 CORP\\User1 帳戶登入 DC1，並開啟系統管理員層級 Windows PowerShell 命令提示字元。逐一執行這些命令，以建立名為 contoso_users 的新組織單位以及 Marci Kaufman 和 Lynda Meyer 兩個新的使用者帳戶。

	New-ADOrganizationalUnit -Name contoso_users -Path "DC=corp,DC=contoso,DC=com"
	New-ADUser -SamAccountName marcik -AccountPassword (Read-Host "Set user password" -AsSecureString) -name "Marci Kaufman" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Path "OU=contoso_users,DC=corp,DC=contoso,DC=com"
	New-ADUser -SamAccountName lyndam -AccountPassword (Read-Host "Set user password" -AsSecureString) -name "Lynda Meyer" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Path "OU=contoso_users,DC=corp,DC=contoso,DC=com"

您執行各個 Windows PowerShell 命令時，會提示您輸入新使用者的密碼。記錄這些密碼並儲存於安全之處。稍後您將需要這些資訊。

接著，在 DS1 上設定 Directory 同步處理。

1.	以 CORP\\User1 帳戶登入 DS1。
2.	在 [開始] 畫面上，輸入「Directory 同步處理」。
3.	以滑鼠右鍵按一下 [Directory 同步處理組態]，然後按一下 [以系統管理員身分執行]。這將啟動組態精靈。
4.	在 [歡迎] 頁面中按 [下一步]。
5.	在 [Microsoft Azure Active Directory 認證] 頁面上，輸入您在階段 2 中設定 Office 365 FastTrack 試用時所建立之初始帳戶的電子郵件地址和密碼。按 [下一步]。 
6.	在 [Active Directory 認證] 頁面中，於 [使用者名稱] 中輸入 **CORP\\User1**，並且於 [密碼] 中輸入 User1 帳戶密碼。按 [下一步]。
7.	在 [混合式部署] 頁面上，選取 [啟用混合式部署]，然後按 [下一步]。
8.	在 [密碼同步處理] 頁面上，選取 [啟用密碼同步處理]，然後按 [下一步]。
9.	[組態] 頁面會顯示。組態完成時，按 [下一步]。
10.	在 [完成] 頁面上，按一下 [完成]。出現提示時，按一下 **[確定]**。

接著，確認 CORP 網域中的使用者帳戶已同步處理至 Office 365。請注意，可能需要經過數小時才會進行同步處理。

在 CLIENT1 的 [設定和管理 Active Directory 同步處理] 頁面上，按一下此頁面步驟 6 中的 [使用者] 連結。如果 Directory 同步處理順利進行，您應該會看見如下所示的內容。

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_4.png)

[狀態] 欄將指出已透過 Active Directory 網域的同步處理取得帳戶。

接著，示範 Lynda Myer Active Directory 帳戶的 Office 365 密碼同步。

1.	在 CLIENT1 [作用中使用者] 頁面上，選取 **Lynda Meyer** 帳戶。
2.	在 Lynda Meyer 帳戶內容的 [指派的授權] 下，按一下 [編輯]。
3.	在 [指派授權] 索引標籤上，選取 [設定使用者位置] 中的位置 (例如美國)。
4.	選取 [Microsoft Office 365 計劃 E3]，然後按一下 [儲存]。
5.	關閉 Internet Explorer。
6.	執行 Internet Explorer 並移至 **http://portal.microsoftonline.com**。7.	使用 Lynda Meyer 的 Office 365 認證登入。她的使用者名稱將是 lyndam@<*您的虛構名稱*>.onmicrosoft.com。密碼是 Lynda Meyer Active Directory 使用者帳戶密碼。
8.	成功登入後，您將看見 Office 365 入口網站主首頁顯示**讓今天有所不同**。

這是您目前的組態。

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_3.png)
 
這個環境到此準備就緒，可供您測試運用 Office 365 DirSync 功能的 Office 365 應用程式，或測試 DS1 的 DirSync 功能和效能。

## 其他資源

[在 Microsoft Azure 中部署 Office 365 目錄同步作業 (DirSync)](http://technet.microsoft.com/library/dn635310.aspx)

[使用 Office 伺服器和雲端的解決方案](http://technet.microsoft.com/library/dn262744.aspx)

[設定用於測試的混合式雲端環境](virtual-networks-setup-hybrid-cloud-environment-testing.md)

[在混合式雲端中設定用於測試的 SharePoint 內部網路伺服器陣列](virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

[在混合式雲端中設定 Web 型 LOB 應用程式進行測試](virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[設定用於測試的模擬混合式雲端環境](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md)

[Azure 混合式雲端測試環境](../virtual-machines/virtual-machines-hybrid-cloud-test-environments.md)

[Azure 基礎結構服務實作指導方針](../virtual-machines/virtual-machines-infrastructure-services-implementation-guidelines.md)


 

<!---HONumber=July15_HO2-->