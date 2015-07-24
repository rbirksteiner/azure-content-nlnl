<properties 
	pageTitle="在 Azure 中安裝複本網域控制站" 
	description="此教學課程說明如何從 Azure 的虛擬機器上的內部部署 Active Directory 樹系中安裝網域控制站。" 
	services="virtual-network" 
	documentationCenter="" 
	authors="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"
	tags="azure-classic-portal"/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="Justinha"/>


# 在 Azure 虛擬網路中安裝複本 Active Directory 網域控制台

此主題說明如何在 Azure 虛擬網路中的 Azure 虛擬機器 (VM) 上為內部部署 Active Directory 網域安裝其他網域控制站 (亦稱為複本網域控制站) 。

您也可能對以下相關主題有興趣：

- 您可以在 Azure 虛擬網路上選擇性安裝新的 Active Directory 樹系。如需相關步驟，請參閱[在 Azure 虛擬網路上安裝新的 Active Directory 樹系](../active-directory-new-forest-virtual-machine.md)。
-  如需在 Azure 虛擬網路上安裝 Active Directory 網域服務 (AD DS) 的概念指引，請參閱[在 Azure 虛擬機器上部署 Windows Server Active Directory 的方針](https://msdn.microsoft.com/library/azure/jj156090.aspx) (英文)。


## 案例圖表

在此案例中，外部使用者需要存取在加入網域的伺服器上執行的應用程式。執行應用程式伺服器和複本 DC 的 VM 安裝在 Azure 虛擬網路中。虛擬網路可透過[站對站 VPN](https://msdn.microsoft.com/library/azure/dn133795.aspx) 連線方式來連線到內部部署網路，如下圖所示，或者您可以使用 [ExpressRoute](../../services/expressroute/) 進行更快速的連線。

應用程式伺服器和網域控制站會部署在個別[雲端服務](../cloud-services-what-is.md)內分散運算處理，以及在[可用性設定組](../virtual-machines/virtual-machines-manage-availability.md)內改良容錯功能。網域控制站會使用 Active Directory 複寫功能，在彼此之間以及與內部部署網域控制站互相複寫。不需要任何同步處理工具。

![][1]

## 建立 Azure 虛擬網路的 Active Directory 站台

在 Active Directory 中建立一個站台來代表對應虛擬網路的網路區域是個不錯的主意。這樣有助於最佳化驗證、複寫及其他 DC 位置的作業。下列步驟說明如何建立站台，如需詳細背景，請參閱[加入新的站台](https://technet.microsoft.com/library/cc781496.aspx)。

1. 開啟 Active Directory 站台及服務：[伺服器管理員] > [工具] > [Active Directory 站台及服務]。
2. 建立站台來代表您建立 Azure 虛擬網路的區域：按一下 [站台] > [動作] > [新增站台] > 輸入新站台的名稱 (例如「Azure 美國西部」) > 選取站台連結 > [確定]。
3. 建立子網路，並與新站台關聯：按兩下 [站台] > 以滑鼠右鍵按一下 [子網路] > [新增子網路] > 輸入虛擬網路的 IP 位址範圍 (例如案例圖表中的 10.1.0.0/16) > 選取新的 Azure 站台 > [確定]。

## 建立 Azure 虛擬網路

1. 在 Azure 傳統入口網站中，依序按一下 [新增] > [網路服務] > [虛擬網路] > [自訂建立]，並使用下列值來完成精靈。

    在此精靈頁面上… | 指定這些值
	------------- | -------------
	**虛擬網路詳細資料** | <p>名稱：輸入虛擬網路的名稱，例如 WestUSVNet。</p><p>區域：選擇最近的區域。</p>
	**DNS 和 VPN 連線能力** | <p>DNS 伺服器：指定一或多個內部部署 DNS 伺服器的名稱與 IP 位址。</p><p>連線能力：選取 [設定站對站 VPN]。</p><p>區域網路：指定新的區域網路。</p><p>如果您使用 ExpressRoute 而不是 VPN，請參閱 [透過 Exchange 提供者設定 ExpressRoute 連線](https://msdn.microsoft.com/library/azure/dn606306.aspx)。</p>
	**站對站連線能力** | <p>名稱：輸入內部部署網路的名稱。</p><p>VPN 裝置 IP 位址：指定將連線到虛擬網路的裝置公用 IP 位址。VPN 裝置不能位於 NAT 後方。</p><p>位址：指定內部部署網路的位址範圍 (例如案例圖表中的 192.168.0.0/16)。</p>
	**虛擬網路位址空間** | <p>位址空間：指定您想要在 Azure 虛擬網路中執行的 VM IP 位址範圍 (例如案例圖表中的 10.1.0.0/16)。此位址範圍不得與內部部署網路的位址範圍重疊。</p><p>子網路：指定應用程式伺服器的子網路名稱和位址 (例如前端 10.1.1.0/24) 以及網域控制站的子網路名稱和位址 (例如後端 10.1.2.0/24)。</p><p>按一下 [加入閘道子網路]。</p>

2. 接著，您要設定虛擬網路閘道來建立安全的站台對站台 VPN 連線。如需指示，請參閱[設定虛擬網路閘道](https://msdn.microsoft.com/library/azure/jj156210.aspx)。
3. 在新的虛擬網路與內部部署 VPN 裝置之間建立站台對站台 VPN 連線。如需指示，請參閱[設定虛擬網路閘道](https://msdn.microsoft.com/library/azure/jj156210.aspx)。



## 建立 DC 角色的 Azure VM

重複下列步驟，視需要建立裝載 DC 角色的 VM。您應該至少部署兩部虛擬網域控制站以提供容錯和冗餘。如果 Azure 虛擬網路包含至少兩個類似設定的 DC (亦即，它們都是 GC、執行 DNS 伺服器，並且未持有任何 FSMO 角色等等)，那麼可將執行這些 DC 的 VM 放在可用性集合以獲得改善的容錯。若要使用 Windows PowerShell 而非 UI 建立 VM，請參閱[使用 Azure PowerShell 建立和預先設定以 Windows 為基礎的虛擬機器](../virtual-machines/virtual-machines-ps-create-preconfigure-windows-vms.md)。

1. 在 Azure 傳統入口網站中，按一下 [新增] > [計算] > [虛擬機器] > [從組件庫]。使用下列值來完成精靈。除非建議或需要另一個值，否則請接受設定的預設值。

    在此精靈頁面上… | 指定這些值
	------------- | -------------
	**選擇映像** | Windows Server 2012 R2 Datacenter
	**虛擬機器組態** | <p>虛擬機器名稱：輸入單一標籤名稱 (例如 AzureDC1)。</p><p>新的使用者名稱：輸入使用者的名稱。此使用者將會是 VM 上本機 Administrators 群組的成員。第一次登入 VM 時，您將需要此名稱。內建的系統管理員帳戶會無法運作。</p><p>新密碼/確認：輸入密碼</p>
	**虛擬機器組態** | <p>雲端服務：針對第一個 VM 選擇<b>建立新的雲端服務</b>，然後在建立更多將主控 DC 角色的 VM 時選取該相同雲端服務名稱。</p><p>雲端服務 DNS 名稱：指定全域唯一的名稱</p><p>區域/同質群組/虛擬網路：指定虛擬網路名稱 (例如 WestUSVNet)。</p><p>儲存體帳戶：針對第一個 VM 選擇<b>使用自動產生的儲存體帳戶</b>，然後在建立更多將主控 DC 角色的 VM 時選取該相同儲存體帳戶名稱。</p><p>可用性設定組：選擇<b>建立可用性設定組</b>。</p><p>可用性設定組名稱：在建立第一個 VM 時輸入可用性設定組的名稱，然後在建立更多 VM 時輸入該相同名稱。</p>
	**虛擬機器組態** | <p>選取<b>安裝 VM 代理程式</b>以及您所需的任何其他延伸模組。</p>
2. 將磁碟連接至將執行 DC 伺服器角色的每個 VM。需要額外的磁碟來儲存 AD 資料庫、記錄檔和 SYSVOL。指定磁碟的大小 (例如 10 GB) 並保留 [**主機快取喜好設定**] 設為 [**無**]。如需相關步驟，請參閱[如何將資料磁碟連接至 Windows 虛擬機器](../virtual-machines/storage-windows-attach-disk.md)。
3. 在您第一次登入 VM 之後，請開啟 [**伺服器管理員**] > [**檔案和儲存體服務**]，以在磁碟上使用 NTFS 建立磁碟區。
4. 為將執行 DC 角色的 VM 保留靜態 IP 位址。若要保留靜態 IP 位址，請下載 Microsoft Web Platform Installer，[安裝 Azure PowerShell](../powershell-install-configure.md) 並執行 Set-AzureStaticVNetIP Cmdlet。例如：

    'Get-AzureVM -ServiceName AzureDC1 -Name AzureDC1 | Set-AzureStaticVNetIP -IPAddress 10.0.0.4 | Update-AzureVM

如需如何設定靜態 IP 位址的詳細資訊，請參閱[設定 VM 的靜態內部 IP 位址](https://msdn.microsoft.com/library/azure/dn630228.aspx)。

## 在 Azure VM 上安裝 AD DS

登入 VM，並確認您具備整個站台對站台 VPN 的連線能力，或是對內部部署網路上的資源具備 ExpressRoute 連線功能。然後在 Azure VM 上安裝 AD DS。您可以使用您用來在內部部署網路上安裝其他網域控制站的相同程序 (UI、Windows PowerShell 或回應檔案)。當您安裝 AD DS 時，請務必指定新磁碟區的 AD 資料庫、記錄檔和 SYSVOL 的位置。如果您需要 AD DS 安裝上的重新整理程式，請參閱[安裝 Active Directory 網域服務 (等級 100)](https://technet.microsoft.com/library/hh472162.aspx) 或[在現有網域中安裝複本 Windows Server 2012 網域控制站 (等級 200)](https://technet.microsoft.com/library/jj574134.aspx)。

## 重新設定虛擬網路的 DNS 伺服器

1. 在 Azure 的傳統網站中，按一下虛擬網路的名稱，然後按一下 [設定] 索引標籤[重新設定虛擬網路的 DNS 伺服器 IP 位址](https://msdn.microsoft.com/library/azure/dn275925.aspx)，以使用指派給複本網域控制站的靜態 IP 位址，而不是內部部署 DNS 伺服器的 IP 位址。
 
2. 若要確保虛擬網路上的所有複本 DC VM 都已設定使用虛擬網路上的 DNS 伺服器，請按一下 [虛擬機器]，按一下每個 VM 的狀態欄，然後按一下 [重新啟動]。請等到 VM 顯示 [執行中] 狀態，再嘗試登入。

## 建立應用程式伺服器的 VM

1. 重複執行下列步驟來建立做為應用程式伺服器執行的 VM。除非建議或需要另一個值，否則請接受設定的預設值。


	在此精靈頁面上… | 指定這些值
	------------- | -------------
	**選擇映像** | Windows Server 2012 R2 Datacenter
	**虛擬機器組態** | <p>虛擬機器名稱：輸入單一標籤名稱 (例如 AppServer1)。</p><p>新的使用者名稱：輸入使用者的名稱。此使用者將會是 VM 上本機 Administrators 群組的成員。第一次登入 VM 時，您將需要此名稱。內建的系統管理員帳戶會無法運作。</p><p>新密碼/確認：輸入密碼</p>
	**虛擬機器組態** | <p>雲端服務：針對第一個 VM 選擇 **建立新的雲端服務**，然後在建立更多將會裝載應用程式的 VM 時選取該相同雲端服務名稱。</p><p>雲端服務 DNS 名稱：指定全域唯一的名稱</p><p>區域/同質群組/虛擬網路：指定虛擬網路名稱 (例如 WestUSVNet)。</p><p>儲存體帳戶：針對第一個 VM 選擇 **使用自動產生的儲存體帳戶**，然後在建立更多將會裝載應用程式的 VM 時選取該相同儲存體帳戶名稱。</p><p>可用性設定組：選擇 **建立可用性設定組**。</p><p>可用性設定組名稱：當您建立第一個 VM 時輸入可用性設定組的名稱，然後在建立更多的 VM 時選取該相同名稱。</p>
	**虛擬機器組態** | <p>選取<b>安裝 VM 代理程式</b>以及您所需的任何其他延伸模組。</p>

2. 佈建每個 VM 之後，登入並將 VM 加入網域。在 [**伺服器管理員**] 中，依序按一下 [**本機伺服器**] > [**WORKGROUP**] > [**變更...**]，然後選取 [**網域**] 並輸入您的內部部署網域名稱。提供網域使用者的認證，然後重新啟動 VM 以完成網域加入。

若要使用 Windows PowerShell 而非 UI 建立 VM，請參閱[使用 Azure PowerShell 建立和預先設定以 Windows 為基礎的虛擬機器](../virtual-machines/virtual-machines-ps-create-preconfigure-windows-vms.md)。

如需有關使用 Windows PowerShell 的詳細資訊，請參閱[開始使用 Azure Cmdlet](https://msdn.microsoft.com/library/azure/jj554332.aspx) 和 [Azure Cmdlet 參考](https://msdn.microsoft.com/library/azure/jj554330.aspx)。

## 其他資源

-  [在 Azure 虛擬機器上部署 Windows Server Active Directory 的指導方針](https://msdn.microsoft.com/library/azure/jj156090.aspx)
-  [如何使用 Azure PowerShell 將現有的內部部署 Hyper-V 網域控制站上傳到 Azure](http://support.microsoft.com/kb/2904015)
-  [在 Azure 虛擬網路上安裝新的 Active Directory 樹系](../active-directory-new-forest-virtual-machine.md)
-  [Azure 虛擬網路](https://msdn.microsoft.com/library/azure/jj156007.aspx)
-  [Microsoft Azure IT Pro IaaS：(01) 虛擬機器基本概念](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
-  [Microsoft Azure IT Pro IaaS：(05) 建立虛擬網路和跨單位連線](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
-  [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)
-  [Azure 管理 Cmdlet](https://msdn.microsoft.com/library/azure/jj152841)

<!--Image references-->
[1]: ./media/virtual-networks-install-replica-active-directory-domain-controller/ReplicaDCsOnAzureVNet.png
 

<!---HONumber=July15_HO2-->