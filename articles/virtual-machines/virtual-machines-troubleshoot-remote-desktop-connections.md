<properties 
	pageTitle="疑難排解以 Windows 為基礎之 Azure 虛擬機器的遠端桌面連線" 
	description="如果您無法連線以 Windows 為基礎的 Azure 虛擬機器，請使用下列診斷與步驟來隔離問題的來源。"
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/07/2015" 
	ms.author="josephd"/>

# 疑難排解以 Windows 為基礎之 Azure 虛擬機器的遠端桌面連線

如果您無法連線到以 Windows 為基礎的 Azure 虛擬機器，本文將說明如何以有條理的方式修正遠端桌面連線並判定根本原因。

## 步驟 1：執行 Azure IaaS 診斷封裝。

為解決建立遠端桌面連線時的許多常見問題，Microsoft 已建立了 [Azure IaaS (Windows) 診斷封裝](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)。

1.	按一下此頁面上的 **Microsoft Azure IaaS (Windows) 診斷封裝**，即可建立新的診斷工作階段。
2.	在**您的 Azure VM 遇到下列哪些問題？**頁面上，選取 **RDP 連線至 Azure VM (需要重新開機)** 問題。 

如需詳細資訊，請參閱 [Microsoft Azure IaaS (Windows) 診斷封裝知識庫文件](http://support.microsoft.com/kb/2976864)。

如果執行 Azure IaaS 診斷封裝未能修正此問題，或您無法執行診斷封裝，您可能會需要下列步驟所描述之更詳細的疑難排解。

> [AZURE.NOTE]必須從執行 Windows 8、Windows 8.1、Windows Server 2012 或 Windows Server 2012 R2 的電腦上執行 Azure IaaS (Windows) 診斷封裝。

## 步驟 2：判斷來自遠端桌面用戶端的錯誤訊息。

根據您在嘗試連線時所看到的錯誤訊息來使用下列各節。

### 遠端桌面連線訊息視窗：遠端工作階段已中斷連線，因為沒有可用的遠端桌面授權伺服器來提供授權。

原因：遠端桌面角色的 120 天授權寬限期已過期，您需要安裝授權。
 
做為暫時的因應措施，請從 Azure 管理入口網站儲存 RDP 檔案的本機複本，然後在 Windows PowerShell 命令提示字元執行該命令來連接。

	mstsc <File name>.RDP /admin

這只會停用該連接的授權。

如果您並非真的需要多於兩個對虛擬機器的同步遠端桌面連線，則您可以使用伺服器管理員來移除遠端桌面伺服器角色。

另請參閱 [Azure VM 失敗並出現「沒有可用的遠端桌面授權伺服器」](http://blogs.msdn.com/b/wats/archive/2014/01/21/rdp-to-azure-vm-fails-with-quot-no-remote-desktop-license-servers-available-quot.aspx)部落格文章。

### 遠端桌面連線訊息視窗：遠端桌面無法找到電腦「名稱」。

原因：您電腦上的遠端桌面用戶端無法解析此 RDP 檔案設定中的電腦名稱。

以下是由 Azure 管理入口網站產生的 RDP 檔案範例：

	full address:s:tailspin-azdatatier.cloudapp.net:55919
	prompt for credentials:i:1

在此範例中，位址部分包含雲端服務的完整網域名稱，這包含虛擬機器 (在本範例中為 tailspin-azdatatier.cloudapp.net) 以及遠端桌面流量端點的外部 TCP 連接埠 (55919)。

此問題的可能解決方案：

- 如果您位於組織內部網路，請確保您的電腦能存取並傳送 HTTPS 流量給 Proxy 伺服器。
- 如果您正使用本機儲存的 RDP 檔案，請嘗試使用 Azure 管理入口網站所產生的 RDP 檔案，確保虛擬機器或雲端服務的 DNS 名稱以及虛擬機器的端點連接埠都正確。

### 遠端桌面連線訊息視窗：發生驗證錯誤。無法連絡本機安全性授權。

原因：您連線的虛擬機器找不到您認證的使用者名稱部分所表示的安全性授權。

若您的使用者名稱格式為 *SecurityAuthority*\\*UserName* (範例：CORP\\User1)，則 *SecurityAuthority* 部分可以是虛擬機器的電腦名稱 (做為本機安全性授權) 或 Active Directory 網域名稱。

此問題的可能解決方案：

- 如果該使用者帳戶只能在虛擬機器中使用，請確認虛擬機器名稱的拼字正確。
- 如果該使用者帳戶是 Active Directory 網域帳戶，請確認網域名稱的拼字正確。
- 如果該使用者帳戶是 Active Directory 網域帳戶且網域名稱的拼字正確，請確認可以使用 Active Directory 網域的網域控制站。網域控制站電腦並未啟動，在包含網域控制站的 Azure 虛擬網路中是常見的問題。暫時的解決辦法是使用本機系統管理員帳戶，而不要使用網域帳戶。

### Windows 安全性訊息視窗：您的認證無法運作。

原因：您提交的帳戶名稱和密碼無法由目前連接的虛擬機器所驗證。

以 Windows 為基礎的電腦可以驗證本機帳戶或以網域為基礎帳戶之認證。

- 如果是本機帳戶，請使用 *ComputerName*\\*UserName* 語法 (範例：SQL1\\Admin4798)。 
- 如果是網域帳戶，請使用 *DomainName*\\*UserName* 語法 (範例：CONTOSO\\johndoe)。

對於您在新的 AD 樹系提升為網域控制器的電腦，您執行此提升時所登入的本機系統管理員帳戶會轉換為對等的帳戶，密碼與在新樹系和網域中的相同。上一個本機系統管理員帳戶已刪除。例如，如果您以本機系統管理員帳戶 DC1\\DCAdmin 登入，並提升此虛擬機器為 corp.contoso.com 網域中新樹系的網域控制器，則會刪除 DC1\\DCAdmin 本機帳戶，並且以相同密碼建立一個新的網域帳戶 CORP\\DCAdmin。

請再次檢查帳戶名稱，確保虛擬機器可驗證此名稱為有效帳戶。請再次確認密碼，確保密碼為正確。

如果您需要變更本機系統管理員帳戶的密碼，請參閱[如何重設 Windows 虛擬機器的密碼或遠端桌面服務](virtual-machines-windows-reset-password.md)。

### 遠端桌面連線訊息視窗：這部電腦無法連線到遠端電腦。

原因：您正用來連接的帳戶並沒有遠端桌面的登入權限。

每部 Windows 電腦都有遠端桌面使用者本機群組，其中包含有權限以遠端桌面連線登入的帳戶和群組。本機系統管理員群組成員也有權限，即使這些帳戶未列在遠端桌面使用者本機群組的成員中。對於加入網域的機器，本機系統管理員群組也包含此網域的網域系統管理員。

請確保您正用於啟動連接的帳戶具有遠端桌面登入權限。請使用網域系統管理員或本機系統管理員帳戶做為暫時的因應措施，來建立遠端桌面連線，並使用電腦管理嵌入式管理單元加入想要的帳戶到遠端桌面使用者本機群組 (**系統工具 > 本機使用者和群組 > 群組 > 遠端桌面使用者**)。

### 遠端桌面連線訊息視窗：遠端桌面因其中一項原因而無法連線到遠端電腦...

原因：遠端桌面用戶端無法在虛擬機器上與遠端桌面服務連線。此問題可能由許多根本原因所導致。

下列為一組相關的元件。

![](./media/virtual-machines-troubleshoot-remote-desktop-connections/tshootrdp_0.png)
 
在深入了解逐步的疑難排解程序之前，先在心中檢閱您在可以成功建立遠端桌面連線之後曾進行過的變更，再使用這些變更當成修正問題的基礎會相當有幫助。例如：

- 如果您可以建立遠端桌面連線，且您變更了虛擬機器或包含虛擬機器的雲端服務之公用 IP 位址 (又稱為虛擬 IP 位址 [VIP])，則您的 DNS 用戶端快取可能會有 DNS 名稱和「舊的 IP 位址」項目。請清除 DNS 用戶端快取並重試一次。或者，請嘗試使用新的 VIP 建立連線。
- 如果您從使用 Azure 管理入口網站或 Azure Preview 入口網站變更為使用應用程式來管理遠端桌面連線，則請確保應用程式組態包含用於遠端桌面流量之隨機決定的 TCP 連接埠。 

下節將逐步地隔離和判定此問題的各種根本原因，並提供解決方案和因應措施。

## 步驟 3：詳細疑難排解前的初步步驟

請執行以下步驟：

- 請檢查在 Azure 管理入口網站或 Azure Preview 入口網站中的虛擬機器狀態。
- [重新啟動虛擬機器](https://msdn.microsoft.com/library/azure/dn763934.aspx)
- [調整虛擬機器的大小](https://msdn.microsoft.com/library/dn168976.aspx)

在這些步驟之後，請再試一次遠端桌面連線。

## 步驟 4：詳細的疑難排解 

您的遠端桌面用戶端若不能連接 Azure 虛擬機器上的遠端桌面服務，可為下列問題或錯誤設定的來源所造成：

- 遠端桌面用戶端電腦
- 組織內部網路的邊緣裝置
- 雲端服務端點和存取控制清單 (ACL)
- 網路安全性群組
- 以 Windows 為基礎的 Azure 虛擬機器

### 來源 1：遠端桌面用戶端電腦

若要排除您的電腦為問題或錯誤設定來源之可能性，請確認您的電腦可以建立遠端桌面連線到另一部內部部署且以 Windows 為基礎的電腦。

![](./media/virtual-machines-troubleshoot-remote-desktop-connections/tshootrdp_1.png)
 
如果不能，請在您的電腦上檢查下列項目：

- 正封鎖遠端桌面流量的本機防火牆設定。
- 正阻止遠端桌面連線的本機安裝用戶端 Proxy 軟體。
- 正阻止遠端桌面連線的本機安裝網路監視軟體。
- 正阻止遠端桌面連線的其他類型安全性軟體，這會監視流量或允許/不允許特定類型的流量。

在所有這些情況下，請嘗試暫時停用此軟體，然後再嘗試遠端桌面連線到內部部署的電腦，以判斷根本原因。然後請和網路管理員合作，修正軟體設定以允許遠端桌面連線。

### 來源 2：組織內部網路的邊緣裝置

若要排除您組織內部網路的邊緣裝置為問題或錯誤設定來源之可能性，請確認直接連接到網際網路的電腦能遠端連線到您的 Azure 虛擬機器。

![](./media/virtual-machines-troubleshoot-remote-desktop-connections/tshootrdp_2.png)
 
如果您沒有直接連線到網際網路的電腦，則您只要將新的 Azure 虛擬機器建立於專屬的資源群組或雲端服務中並加以使用即可。如需詳細資訊，請參閱[在 Azure 中建立執行 Windows 的虛擬機器](virtual-machines-windows-tutorial.md)。當您完成測試後，請刪除此資源群組或虛擬機器及雲端服務。

如果您可以對直接連線到網際網路的電腦建立遠端桌面連線，請檢查組織內部網路的邊緣裝置之下列項目：

- 正封鎖網際網路 HTTPS 連接的內部防火牆。
- 正阻止遠端桌面連線的 Proxy 伺服器。
- 正阻止遠端桌面連線且在邊緣網路裝置上執行的入侵偵測或網路監視軟體。

請和網路管理員合作，修正您組織內部網路的邊緣裝置設定，允許以 HTTPS 為基礎的網際網路遠端桌面連線。

### 來源 3：雲端服務端點和 ACL

若要為「服務管理」建立的虛擬機器排除雲端服務端點和 ACL 為問題或錯誤設定來源之可能性，請確認另一部位於相同雲端服務或虛擬網路的 Azure 虛擬機器能使用遠端桌面連線到您的 Azure 虛擬機器。

![](./media/virtual-machines-troubleshoot-remote-desktop-connections/tshootrdp_3.png)
 
> [AZURE.NOTE]如果是在資源管理員中建立的虛擬機器，請跳到[來源 4：網路安全性群組](#nsgs)。

如果在同一個雲端服務或虛擬網路中沒有另一部虛擬機器，可以建立一部新的。如需詳細資訊，請參閱[在 Azure 中建立執行 Windows 的虛擬機器](virtual-machines-windows-tutorial.md)。當您完成測試後，請刪除額外的虛擬機器。

如果您可以建立遠端桌面連線到相同雲端服務或虛擬網路中的虛擬機器，則請檢查下列項目：

- 目標虛擬機器上的遠端桌面流量端點組態。此端點的私用 TCP 連接埠必須符合虛擬機器上遠端桌面服務正在接聽的 TCP 連接埠，根據預設為 3389。 
- 目標虛擬機器上的遠端桌面流量端點 ACL。ACL 讓您可指定要根據來源 IP 位址允許或拒絕來自網際網路的連入流量。設定錯誤的 ACL 會阻止送至端點的連入遠端桌面流量。檢查您的 ACL，以確保允許來自您的 Proxy 或其他邊緣伺服器的公用 IP 位址之連入流量。如需詳細資訊，請參閱[關於網路存取控制清單 (ACL)](https://msdn.microsoft.com/library/azure/dn376541.aspx)。

若要排除端點為問題或錯誤設定來源之可能性，請移除目前的端點，再選擇外部連接埠號碼介於 49152 到 65535 的隨機連接埠來建立新的端點。如需詳細資訊，請參閱[在 Azure 中設定虛擬機器的端點](virtual-machines-set-up-endpoints.md)。

### <a id="nsgs"></a>來源 4：網路安全性群組

網路安全性群組讓您更精確地控制受允許的輸入和輸出流量。您可以在 Azure 虛擬網路中建立跨越子網路和雲端服務的規則。請檢查您的網路安全性群組規則，以確保允許來自網際網路的遠端桌面流量。

如需詳細資訊，請參閱[關於網路安全性群組](../virtual-network/virtual-networks-nsg.md)。

### 來源 5：以 Windows 為基礎的 Azure 虛擬機器

最後一組可能的問題就是 Azure 虛擬機器本身。

![](./media/virtual-machines-troubleshoot-remote-desktop-connections/tshootrdp_5.png)
 
首先，如果您無法執行關於 **RDP 連線至 Azure VM (需要重新開機)** 問題的 [Azure IaaS (Windows) 診斷封裝](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)，請遵循在[如何重設 Windows 虛擬機器的密碼或遠端桌面服務](virtual-machines-windows-reset-password.md)中的指示，來重設虛擬機器上的遠端桌面服務。這將會：

- 啟用「遠端桌面」 Windows 防火牆預設規則 (TCP 連接埠 3389)。
- 藉由將 HKLM\\System\\CurrentControlSet\\Control\\Terminal Server\\fDenyTSConnections 登錄值設為 0 ，啟用遠端桌面連線。

再次嘗試來自您電腦的連線。如果無法成功，下列為某些可能的原因：

- 遠端桌面服務未在目標虛擬機器上執行。
- 遠端桌面服務未在 TCP 連接埠 3389 上接聽。
- Windows 防火牆或其他本機防火牆有阻止遠端桌面流量的輸出規則。
- 在 Azure 虛擬機器上執行的入侵偵測或網路監視軟體正在阻止遠端桌面連線。

若要為在「服務管理」中建立的虛擬機器修正這些可能的問題，您可以使用遠端 PowerShell 工作階段連線到 Azure 虛擬機器。首先，您必須安裝虛擬機器之代管雲端服務的憑證。移至[設定安全遠端 PowerShell 對 Azure 虛擬機器的存取權](http://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe)，以及下載 **InstallWinRMCertAzureVM.ps1** 指令碼檔案到您本機電腦的資料夾。

接下來，如果尚未安裝 Azure PowerShell，則請先安裝。請參閱[如何安裝和設定 Azure PowerShell](../install-configure-powershell.md)。

接下來，開啟 Azure PowerShell 命令提示字元，然後將目前資料夾變更為 **InstallWinRMCertAzureVM.ps1** 指令碼檔案的位置。若要執行 PowerShell 指令碼，您必須設定正確的執行原則。請執行 **Get-ExecutionPolicy** 命令來判斷您目前的原則層級。如需設定適當層級的相關資訊，請參閱 [Set-ExecutionPolicy](https://technet.microsoft.com/library/hh849812.aspx)。

接下來，請填入您的 Azure 訂用帳戶名稱、雲端服務名稱以及虛擬機器名稱 (移除 < and > 字元)，然後再執行這些命令。

	$subscr="<Name of your Azure subscription>"
	$serviceName="<Name of the cloud service that contains the target virtual machine>"
	$vmName="<Name of the target virtual machine>"
	.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName

您可以從 **Get-AzureSubscription** 命令顯示畫面中的 SubscriptionName 屬性取得正確的訂用帳戶名稱。您可以從 **Get-AzureVM** 命令顯示畫面中的 ServiceName 資料行，取得虛擬機器的雲端服務名稱。

如需證明您有新的憑證，請開啟針對目前使用者的憑證嵌入式管理單元，然後在 **Trusted Root Certification Authorities\\Certificates** 資料夾中查看。您應該在 Issued To 資料行中查看具有您的雲端服務之 DNS 名稱的憑證 (範例：cloudservice4testing.cloudapp.net)。

接下來，以這些命令啟動遠端 PowerShell 工作階段。

	$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
	$creds = Get-Credential
	Enter-PSSession -ConnectionUri $uri -Credential $creds

輸入有效的系統管理員認證後，您應該會看到像是 Azure PowerShell 提示字元的結果。

	[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>

提示字元的第一部分表示您現在發出 PowerShell 命令給包含目標虛擬機器的雲端服務。您的雲端服務名稱會與 "cloudservice4testing.cloudapp.net" 不同。

您現在可以發出 PowerShell 命令，調查其他前面所提到的問題，並修正組態。

### 手動修正遠端桌面服務接聽的 TCP 連接埠

如果您不能在遠端 PowerShell 工作階段提示字元執行關於 **RDP 連線至 Azure VM (需要重新開機)** 問題的 [Azure IaaS (Windows) 診斷封裝](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)，則請執行這個命令。

	Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

PortNumber 屬性會顯示目前的連接埠號碼。如有需要，請以此命令來變更遠端桌面連接埠號碼回到其預設值 (3389)。

	Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389

請確認已使用此命令變更連接埠為 3389。

	Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

請以此命令結束遠端 PowerShell 工作階段。

	Exit-PSSession

請確認 Azure 虛擬機器的遠端桌面端點也正在使用 TCP 連接埠 3398 做為其內部連接埠。然後，重新啟動 Azure 虛擬機器，再試一次遠端桌面連線。

## 步驟 5：提交您的問題到 Azure 支援論壇。

若要取得來自世界各地 Azure 專家的協助，您可以提交問題到 MSDN Azure 或 Stack Overflow 論壇。如需詳細資訊，請參閱 [Microsoft Azure 論壇](http://azure.microsoft.com/support/forums/)。

## 步驟 6：提出 Azure 支援事件。

如果您已執行關於 **RDP 連線至 Azure VM (需要重新開機)** 問題的 [Azure IaaS (Windows) 診斷封裝](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)，或是已完成本文的步驟 2 到步驟 5，但仍然無法建立遠端桌面連線，則您是否可以重新建立虛擬機器為一個要考量的替代方法。

如果無法重新建立虛擬機器，則可能是時候該提出 Azure 支援事件了。

若要提出事件，請前往 [Azure 支援網站](http://azure.microsoft.com/support/options/)，然後按一下**取得支援**。

如需使用 Azure 支援的相關資訊，請參閱 [Microsoft Azure 支援常見問題集](http://azure.microsoft.com/support/faq/)。

## 其他資源

[Azure IaaS (Windows) 診斷封裝](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[如何重設 Windows 虛擬機器的密碼或遠端桌面服務](virtual-machines-windows-reset-password.md)

[如何安裝和設定 Azure PowerShell](../install-configure-powershell.md)

[疑難排解以 Linux 為基礎之 Azure 虛擬機器的安全殼層 (SSH) 連線](virtual-machines-troubleshoot-ssh-connections.md)

[疑難排解在 Azure 虛擬機器上執行的應用程式存取](virtual-machines-troubleshoot-access-application.md)

<!---HONumber=July15_HO2-->