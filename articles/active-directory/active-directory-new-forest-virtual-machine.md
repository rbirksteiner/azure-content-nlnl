<properties 
	pageTitle="在 Azure 虛擬網路上安裝 Active Directory 樹系"
	description="說明如何在 Azure 虛擬網路之虛擬機器 (VM) 上建立新 Active Directory 樹系的教學課程。"
	services="active-directory, virtual-network"
	documentationCenter=""
	authors="markusvi"
	manager="TerryLan"
	editor="LisaToft"
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="07/23/2015"
	ms.author="markusvi"/>


# 在 Azure 虛擬網路上安裝新的 Active Directory 樹系

本主題說明如何在 [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)上，於 Azure 虛擬網路的虛擬機器 (VM) 上建立新的 Windows Server Active Directory 環境。在此案例中，Azure 虛擬網路並未連線至內部部署網路。

您也可能對以下相關主題有興趣：

- 如需顯示相關步驟的影片，請參閱[如何在 Azure 虛擬網路上安裝新的 Active Directory 樹系](http://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/How-to-install-a-new-Active-Directory-forest-on-an-Azure-virtual-network)
- 您可以選擇性地[設定站台對站台 VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md)，然後安裝新樹系，或將內部部署樹系延伸至 Azure 虛擬網路。如需相關步驟，請參閱[在 Azure 虛擬網路中安裝複本 Active Directory 網域控制台](../virtual-networks-install-replica-active-directory-domain-controller.md) (英文)。
-  如需在 Azure 虛擬網路上安裝 Active Directory 網域服務 (AD DS) 的概念指引，請參閱[在 Azure 虛擬機器上部署 Windows Server Active Directory 的方針](https://msdn.microsoft.com/library/azure/jj156090.aspx) (英文)。

## 案例圖表

在此案例中，外部使用者需要存取在加入網域的伺服器上執行的應用程式。執行應用程式伺服器的 VM 和執行網域控制站的 VM 會安裝在 Azure 虛擬網路內自己的雲端服務中。它們也會包含在可用性設定檔內，以改進容錯能力。

![][1] 7
## 這與內部部署有何不同？

在 Azure 或在內部部署上安裝網域控制台，差異並不會很多。下表列出主要差異。

若要設定... | 內部部署 | Azure 虛擬網路	
------------- | -------------  | ------------
**網域控制站的 IP 位址** | 在網路介面卡內容上指派靜態 IP 位址 | 執行 Set-AzureStaticVNetIP Cmdlet 以指派靜態 IP 位址
**DNS 用戶端解析程式** | 在網域成員的網路介面卡內容上設定慣用與替代的 DNS 伺服器位址 | 在虛擬網路內容上設定 DNS 伺服器位址
**Active Directory 資料庫儲存體** | 選擇性地將預設儲存位置變更為不是 C:\\ | 您需要將預設儲存位置變更為不是 C:\\



## 建立 Azure 虛擬網路

1. 登入 Azure 傳統入口網站。
2. 建立虛擬網路。按一下 [網路] > [建立虛擬網路]。使用下表中的值來完成精靈。 

	在此精靈頁面上… | 指定這些值
	------------- | -------------
	**虛擬網路詳細資料** | <p>名稱：輸入虛擬網路的名稱</p><p>區域：選擇最近的區域</p>
	**DNS 與 VPN** | <p>DNS 伺服器保留空白</p><p>不選取任何 VPN 選項</p>
	**虛擬網路位址空間** | <p>子網路名稱：輸入子網路的名稱</p><p>起始 IP： <b>10.0.0.0</b></p><p>CIDR：<b>/24 (256)</b></p>



## 建立 VM 來執行網域控制台與 DNS 伺服器角色
 
重複下列步驟，視需要建立裝載 DC 角色的 VM。您應該至少部署兩部虛擬網域控制站以提供容錯和冗餘。如果 Azure 虛擬網路包含至少兩個類似設定的 DC (亦即，它們都是 GC、執行 DNS 伺服器，並且未持有任何 FSMO 角色等等)，那麼可將執行這些 DC 的 VM 放在可用性集合以獲得改善的容錯。

若要使用 Windows PowerShell 而非 UI 建立 VM，請參閱[使用 Azure PowerShell 建立和預先設定以 Windows 為基礎的虛擬機器](../virtual-machines-ps-create-preconfigure-windows-vms.md)。

1. 在傳統入口網站中，按一下 [**新增**] > [**計算**] > [**虛擬機器**] > [**從組件庫**]。使用下列值來完成精靈。除非建議或需要另一個值，否則請接受設定的預設值。

    在此精靈頁面上… | 指定這些值
	------------- | -------------
	**選擇映像** | Windows Server 2012 R2 Datacenter
	**虛擬機器組態** | <p>虛擬機器名稱：輸入單一標籤名稱 (例如 AzureDC1)。</p><p>新的使用者名稱：輸入使用者的名稱。此使用者將會是 VM 上本機 Administrators 群組的成員。第一次登入 VM 時，您將需要此名稱。內建的系統管理員帳戶會無法運作。</p><p>新密碼/確認：輸入密碼</p>
	**虛擬機器組態** | <p>雲端服務：針對第一個 VM 選擇<b>建立新的雲端服務</b>，然後在建立更多將主控 DC 角色的 VM 時選取該相同雲端服務名稱。</p><p>雲端服務 DNS 名稱：指定全域唯一的名稱</p><p>區域/同質群組/虛擬網路：指定虛擬網路名稱 (例如 WestUSVNet)。</p><p>儲存體帳戶：針對第一個 VM 選擇<b>使用自動產生的儲存體帳戶</b>，然後在建立更多將主控 DC 角色的 VM 時選取該相同儲存體帳戶名稱。</p><p>可用性設定組：選擇<b>建立可用性設定組</b>。</p><p>可用性設定組名稱：在建立第一個 VM 時輸入可用性設定組的名稱，然後在建立更多 VM 時輸入該相同名稱。</p>
	**虛擬機器組態** | <p>選取<b>安裝 VM 代理程式</b>以及您所需的任何其他延伸模組。</p>
2. 將磁碟連接至將執行 DC 伺服器角色的每個 VM。需要額外的磁碟來儲存 AD 資料庫、記錄檔和 SYSVOL。指定磁碟的大小 (例如 10 GB) 並保留 [**主機快取喜好設定**] 設為 [**無**]。如需相關步驟，請參閱[如何將資料磁碟連接至 Windows 虛擬機器](../storage-windows-attach-disk.md)。
3. 在您第一次登入 VM 之後，請開啟 [**伺服器管理員**] > [**檔案和儲存體服務**]，以在磁碟上使用 NTFS 建立磁碟區。
4. 為將執行 DC 角色的 VM 保留靜態 IP 位址。若要保留靜態 IP 位址，請下載 Microsoft Web Platform Installer，[安裝 Azure PowerShell](../powershell-install-configure.md) 並執行 Set-AzureStaticVNetIP Cmdlet。例如：

    'Get-AzureVM -ServiceName AzureDC1 -Name AzureDC1 | Set-AzureStaticVNetIP -IPAddress 10.0.0.4 | Update-AzureVM

如需如何設定靜態 IP 位址的詳細資訊，請參閱[設定 VM 的靜態內部 IP 位址](../virtual-network/virtual-networks-reserved-private-ip.md)。

## 安裝 Windows Server Active Directory

使用相同常式，[安裝 AD DS](https://technet.microsoft.com/library/jj574166.aspx) 供內部部署使用 (也就是您可以使用 UI、回應檔案或 Windows PowerShell)。您需要提供系統管理員認證，才能安裝新樹系。若要指定 Active Directory 資料庫、記錄與 SYSVOL 的位置，請將預設儲存位置從作業系統磁碟機變更為您連接至 VM 的額外資料磁碟。

在 DC 安裝完成之後，請重新連線至 VM 並登入 DC。記得要指定網域認證。

## 重設 Azure 虛擬網路的 DNS 伺服器

1. 重設新 DC/DNS 伺服器的 DNS 轉寄站設定。 
  1. 在 [伺服器管理員] 中，按一下 [**工具**] > [**DNS**]。 
  2. 在 [**DNS 管理員**]，以滑鼠右鍵按一下 DNS 伺服器的名稱，然後按一下 [**屬性**]。 
  3. 在 [**轉寄站**] 索引標籤上，按一下轉寄站的 IP 位址，按一下 [**編輯**]。選取的 IP 位址，然後按一下 [**刪除**]。 
  4. 按一下 [**確定**] 以關閉編輯器，再按一下 [**確定**] 以關閉 DNS 伺服器屬性。 
2. 更新虛擬網路的 DNS 伺服器設定。 
  1. 按一下 [**虛擬網路**] > 按兩下您建立的虛擬網路 > [**設定**] > [**DNS 伺服器**]，輸入執行 DC/DNS 伺服器角色的 VM 的名稱和其中一個 IP，然後按一下 [**儲存**]。 
  2. 選取 VM 並按一下 [重新啟動]，以觸發 VM 利用新 DNS 伺服器的 IP 位址來設定 DNS 解析程式設定。 


## 建立網域成員的 VM

1. 重複執行下列步驟來建立做為應用程式伺服器執行的 VM。除非建議或需要另一個值，否則請接受設定的預設值。

	在此精靈頁面上… | 指定這些值
	------------- | -------------
	**選擇映像** | Windows Server 2012 R2 Datacenter
	**虛擬機器組態** | <p>虛擬機器名稱：輸入單一標籤名稱 (例如 AppServer1)。</p><p>新的使用者名稱：輸入使用者的名稱。此使用者將會是 VM 上本機 Administrators 群組的成員。第一次登入 VM 時，您將需要此名稱。內建的系統管理員帳戶會無法運作。</p><p>新密碼/確認：輸入密碼</p>
	**虛擬機器組態** | <p>雲端服務：針對第一個 VM 選擇 **建立新的雲端服務**，然後在建立更多將會裝載應用程式的 VM 時選取該相同雲端服務名稱。</p><p>雲端服務 DNS 名稱：指定全域唯一的名稱</p><p>區域/同質群組/虛擬網路：指定虛擬網路名稱 (例如 WestUSVNet)。</p><p>儲存體帳戶：針對第一個 VM 選擇 **使用自動產生的儲存體帳戶**，然後在建立更多將會裝載應用程式的 VM 時選取該相同儲存體帳戶名稱。</p><p>可用性設定組：選擇 **建立可用性設定組**。</p><p>可用性設定組名稱：當您建立第一個 VM 時輸入可用性設定組的名稱，然後在建立更多的 VM 時選取該相同名稱。</p>
	**虛擬機器組態** | <p>選取<b>安裝 VM 代理程式</b>以及您所需的任何其他延伸模組。</p>
2. 佈建每個 VM 之後，登入並將 VM 加入網域。在 [**伺服器管理員**] 中，依序按一下 [**本機伺服器**] > [**WORKGROUP**] > [**變更...**]，然後選取 [**網域**] 並輸入您的內部部署網域名稱。提供網域使用者的認證，然後重新啟動 VM 以完成網域加入。

若要使用 Windows PowerShell 而非 UI 建立 VM，請參閱[使用 Azure PowerShell 建立和預先設定以 Windows 為基礎的虛擬機器](../virtual-machines-ps-create-preconfigure-windows-vms.md)。

如需有關使用 Windows PowerShell 的詳細資訊，請參閱[開始使用 Azure Cmdlet](https://msdn.microsoft.com/library/azure/jj554332.aspx) 和 [Azure Cmdlet 參考](https://msdn.microsoft.com/library/azure/jj554330.aspx)。


## 另請參閱

-  [如何在 Azure 虛擬網路上安裝新的 Active Directory 樹系](http://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/How-to-install-a-new-Active-Directory-forest-on-an-Azure-virtual-network)
-  [在 Azure 虛擬機器上部署 Windows Server Active Directory 的方針](https://msdn.microsoft.com/library/azure/jj156090.aspx)
-  [設定純雲端虛擬網路](../virtual-network/virtual-networks-create-vnet.md)
-  [設定站台對站台 VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md)
-  [在 Azure 虛擬網路中安裝複本 Active Directory 網域控制台](../virtual-networks-install-replica-active-directory-domain-controller.md)
-  [Microsoft Azure IT Pro IaaS：(01) 虛擬機器基本概念](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
-  [Microsoft Azure IT Pro IaaS：(05) 建立虛擬網路和跨單位連線](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
-  [虛擬網路概觀](../virtual-network/virtual-networks-overview.md)
-  [如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)
-  [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)
-  [Azure Cmdlet 參考](https://msdn.microsoft.com/library/azure/jj554330.aspx)
-  [設定 Azure VM 靜態 IP 位址](http://windowsitpro.com/windows-azure/set-azure-vm-static-ip-address)
-  [如何將靜態 IP 指派給 Azure VM](http://www.bhargavs.com/index.php/2014/03/13/how-to-assign-static-ip-to-azure-vm/)
-  [安裝新的 Active Directory 樹系](https://technet.microsoft.com/library/jj574166.aspx)
-  [Active Directory 網域服務 (AD DS) 虛擬化 (等級 100) 簡介](https://technet.microsoft.com/library/hh831734.aspx)

<!--Image references-->
[1]: ./media/active-directory-new-forest-virtual-machine/AD_Forest.png

 

<!---HONumber=August15_HO9-->