<properties 
	pageTitle="建立並上傳 Windows Server VHD 到 Azure" 
	description="了解在已安裝 Windows Server 作業系統的 Azure 中建立並上傳虛擬硬碟 (VHD)。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="kathydav"/>


# 建立並上傳 Windows Server VHD 到 Azure#

本文說明如何使用作業系統上傳虛擬硬碟 (VHD)，以便用它做為映像，根據該映像建立虛擬機器。如需 Azure 中的磁碟和映像的詳細資訊，請參閱[關於 Azure 中的磁碟和映像](http://msdn.microsoft.com/library/azure/jj672979.aspx)。

> [AZURE.NOTE]當您根據映像建立虛擬機器時，您可以視需要為您要在虛擬機器上執行的應用程式自訂作業系統設定。這項設定只會針對該虛擬機器進行儲存，而不會影響映像。

## 必要條件##

本文假設您具有下列項目：

1. **Azure 訂閱** - 如果還沒有，您可以[免費申請 Azure 帳戶](/pricing/free-trial/?WT.mc_id=A261C142F) - 您將取得可試用付費 Azure 服務的額度，且即使在額度用完後，您仍可保留帳戶，並使用免費的 Azure 服務，例如「網站」。除非您明確變更您的設定且同意付費，否則我們將不會從您的信用卡收取任何費用。您也可以[啟用 MSDN 訂戶權益](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) - 您的 MSDN 訂閱每月會提供您額度，您可以用在 Azure 付費服務。   

2. **Microsoft Azure PowerShell** - 您已安裝 Microsoft Azure PowerShell 模組，並設定成使用您的訂用帳戶。若要下載此模組，請參閱 [Microsoft Azure 下載](http://azure.microsoft.com/downloads/)。[這裡](../powershell-install-configure.md)有安裝和設定模組的教學課程。您將使用 [Add-AzureVHD](http://msdn.microsoft.com/library/azure/dn495173.aspx) Cmdlet 上傳 VHD。

3. **支援的 Windows 作業系統並儲存在 .vhd 檔案中** - 您已將支援的 Windows Server 作業系統安裝在虛擬硬碟中。有多項工具可用來建立 .vhd 檔案。您可以使用虛擬化解決方案 (例如 Hyper-V) 建立虛擬機器，並安裝作業系統。如需指示，請參閱[安裝 Hyper-V 角色及設定虛擬機器](http://technet.microsoft.com/library/hh846766.aspx)。

> [AZURE.NOTE]Microsoft Azure 不支援 VHDX 格式。您可以使用 Hyper-V 管理員或 [Convert-VHD Cmdlet](http://technet.microsoft.com/library/hh848454.aspx)，將磁碟轉換為 VHD 格式。教學課程提供於[這裡](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx)。
 
 支援下列 Windows Server 版本<P> <TABLE BORDER="1" WIDTH="600"> <TR BGCOLOR="#E9E7E7"> <TH>OS</TH> <TH>SKU</TH> <TH>Service Pack</TH> <TH>Architecture</TH> </TR> <TR> <TD>Windows Server 2012 R2</TD> <TD>所有版本</TD> <TD>N/A</TD> <TD>x64</TD> </TR> <TR> <TD>Windows Server 2012</TD> <TD>所有版本</TD> <TD>N/A</TD> <TD>x64</TD> </TR> <TR> <TD>Windows Server 2008 R2</TD> <TD>所有版本</TD> <TD>SP1</TD> <TD>x64</TD> </TR> </TABLE> </P>


此工作包含下列步驟：

- [步驟 1：準備要上傳的映像][]
- [步驟 2：在 Azure 中建立儲存體帳戶][]
- [步驟 3：準備 Azure 的連線][]
- [步驟 4：上傳 .vhd 檔案][]

## 步驟 1：準備要上傳的映像 ##

在您可以將映像上傳至 Azure 之前，必須使用 Sysprep 命令將它一般化。如需有關 Sysprep 的詳細資訊，請參閱[如何使用 Sysprep：簡介](http://technet.microsoft.com/library/bb457073.aspx)。

從安裝作業系統的虛擬機器，完成下列程序：

1. 登入作業系統。

2. 以系統管理員身分開啟 [命令提示字元] 視窗。切換至 **%windir%\system32\sysprep** 目錄，然後執行 `sysprep.exe`。

	![開啟 [命令提示字元] 視窗](./media/virtual-machines-create-upload-vhd-windows-server/sysprep_commandprompt.png)

3.	[系統準備工具] 對話方塊隨即出現。

	![啟動 Sysprep](./media/virtual-machines-create-upload-vhd-windows-server/sysprepgeneral.png)

4.  在 [系統準備工具] 中選取 [Enter System Out-of-Box Experience (OOBE)]，並確認 [一般化] 已勾選。

5.  在 [關機選項]中選取 [關機]。

6.  按一下 [確定]。


## 步驟 2：在 Azure 中建立儲存體帳戶 ##

必須要有 Azure 中的儲存體帳戶才能上傳 .vhd 檔案，以在 Azure 中用來建立虛擬機器。您可以使用 Azure 管理入口網站來建立儲存體帳戶。

1. 登入 Azure 管理入口網站。

2. 按一下命令列上的 [新增]。

3. 依序按一下 [資料服務] > [儲存體] > [快速建立]。

	![快速建立儲存體帳戶](./media/virtual-machines-create-upload-vhd-windows-server/Storage-quick-create.png)

4. 依照下列方式填入欄位：
	
	- 在 **URL** 下，為儲存體帳戶輸入要在 URL 中使用的子網域名稱。此項目可以包含 3 至 24 個小寫字母與數字。此名稱會成為 URL 內用來為訂閱的 Blob、「佇列」或「資料表」資源定址的主機名稱。
			
	- 選擇儲存體帳戶的**位置或同質群組**。同質群組可讓您將雲端服務和儲存體放在相同的資料中心。
		 
	- 決定儲存體帳戶是否要使用**地理區域複寫**。依預設會開啟地理區域複寫。此選項可讓您免費將資料複寫至次要位置，使您在主要位置發生重大錯誤時，可將儲存體容錯移轉至該位置。次要位置會自動指派，且無法變更。如果您因為法律規定或組織原則而需要更充分掌控您以雲端為基礎的儲存體所在的位置，您可以關閉地理複寫。但請注意，如果您後續又開啟地理區域複寫，在您將現有的資料複寫至次要位置時，將會產生一次性的資料傳輸費用。不含地理區域複寫的儲存服務會有相對的折扣。如需深入了解如何管理儲存體帳戶的地理區域複寫，請參閱：[建立、管理或刪除儲存體帳戶](../storage-create-storage-account/#replication-options)。

	![輸入儲存體帳戶詳細資料](./media/virtual-machines-create-upload-vhd-windows-server/Storage-create-account.png)


5. 按一下 [建立儲存體帳戶]。帳戶現在會出現在 [儲存體] 下方。

	![已成功建立儲存體帳戶](./media/virtual-machines-create-upload-vhd-windows-server/Storagenewaccount.png)

6. 接下來，為您上傳的 VHD 建立容器。按一下儲存體帳戶名稱，然後按一下 [容器]。

	![儲存體帳戶詳細資訊](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_detail.png)

7. 按一下 [建立容器]。

	![儲存體帳戶詳細資訊](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_container.png)

8. 輸入容器的 [名稱]，然後選取 [存取] 原則。

	![容器名稱](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_containervalues.png)

	> [AZURE.NOTE]容器預設為私人，且只能由帳戶擁有者存取。若要允許對容器中 Blob 的公開讀取存取，但不允許存取容器屬性和中繼資料，請使用 [公用 Blob] 選項。若要允許對容器及 Blob 的完整公開讀取存取，請使用 [公開容器] 選項。

## 步驟 3：準備 Microsoft Azure 的連線 ##

您必須先在電腦與 Azure 中的訂閱之間建立安全連線，才能上傳 .vhd 檔案。您可以使用 Microsoft Azure Active Directory 方法或憑證方法來達到此目的。

> [AZURE.TIP]若要開始使用 Azure PowerShell，請參閱[如何安裝及設定 Microsoft Azure PowerShell](../install-configure-powershell.md)。如需一般資訊，請參閱[開始使用 Microsoft Azure Cmdlet](https://msdn.microsoft.com/library/azure/jj554332.aspx)。

### 使用 Microsoft Azure AD 方法

1. 開啟 Azure PowerShell 主控台。

2. 輸入：`Add-AzureAccount`
	
	這個命令會開啟登入視窗，您可以用您的工作或學校帳戶登入。

	![PowerShell Window](./media/virtual-machines-create-upload-vhd-windows-server/add_azureaccount.png)

3. Azure 會驗證並儲存認證資訊，然後關閉視窗。

### 使用憑證方法 

1. 開啟 Azure PowerShell 主控台。 

2.	輸入：`Get-AzurePublishSettingsFile`。

3. 隨即開啟瀏覽器視窗，並提示您下載 .publishsettings 檔案。它包含您 Microsoft Azure 訂閱的資訊和憑證。

	![瀏覽器下載頁面](./media/virtual-machines-create-upload-vhd-windows-server/Browser_download_GetPublishSettingsFile.png)

3. 儲存 .publishsettings 檔案。

4. 輸入：`Import-AzurePublishSettingsFile <PathToFile>`

	其中 `<PathToFile>` 是 .publishsettings 檔案的完整路徑。

## 步驟 4：上傳 .vhd 檔案

在上傳 .vhd 檔案時，您可以將 .vhd 檔案放在 Blob 儲存體中的任一處。在下列命令範例中，**BlobStorageURL** 是您在步驟 2 中建立之儲存體帳戶的 URL，**YourImagesFolder** 則是您要用來儲存映像之 Blob 儲存體中的容器。**VHDName** 是管理入口網站中用來識別虛擬硬碟的顯示標籤。**PathToVHDFile** 是 .vhd 檔案的完整路徑和名稱。


1. 從您在上一個步驟使用的 Azure PowerShell 視窗中，輸入：

	`Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>`
	
	![PowerShell Add-AzureVHD](./media/virtual-machines-create-upload-vhd-windows-server/powershell_upload_vhd.png)

	如需 Add-AzureVhd Cmdlet 的詳細資訊，請參閱 [Add-AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx)。

## 步驟 5：將影像加入至您的自訂映像清單 ##

上傳 .vhd 之後，您可以將其新增為與訂閱相關之自訂映像清單中的映像。

1. 在管理入口網站的 [All Items] 下方按一下 [虛擬機器]。

2. 在 [虛擬機器] 下，按一下 [映像]。

3. 然後按一下 [建立映像]。

	![PowerShell Add-AzureVHD](./media/virtual-machines-create-upload-vhd-windows-server/Create_Image.png)

4. 在 [從 VHD 建立映像] 中，執行下列動作：
 	

	- 指定 [名稱]
	- 指定 [說明]
	- 若要指定 **VHD 的 URL**，請按一下資料夾按鈕以開啟下列視窗：
 
	![選取 VHD](./media/virtual-machines-create-upload-vhd-windows-server/Select_VHD.png)

	- 選取您的 VHD 所在的儲存體帳戶，然後按一下 [開啟]。這會讓您回到 [從 VHD 建立映像] 視窗。
	- 回到 [從 VHD 建立映像] 視窗後，選取 [作業系統系列]。
	- 勾選 [I have run Sysprep on the virtual machine associated with this VHD] 並確認您在步驟 1 將作業系統一般化，然後按一下 [確定]。 

	![新增映像](./media/virtual-machines-create-upload-vhd-windows-server/Create_Image_From_VHD.png)

5. **選擇性：**您可以使用 Add-AzureVMImage Cmdlet 將您的 VHD 新增為映像，而不使用管理入口網站。在 Azure PowerShell 主控台中，輸入：

	`Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>`
	
	![PowerShell Add-AzureVMImage](./media/virtual-machines-create-upload-vhd-windows-server/add_azureimage_powershell.png)

6. 完成前面的步驟之後，當您選擇 [映像] 索引標籤時會列出新的映像。


	![自訂映像](./media/virtual-machines-create-upload-vhd-windows-server/vm_custom_image.png)

	這個新的映像現在會在您建立虛擬機器時出現於 [我的映像] 下。如需指示，請參閱[如何建立執行 Windows 的自訂虛擬機器](virtual-machines-windows-create-custom.md)。

	![從自訂映像建立 VM](./media/virtual-machines-create-upload-vhd-windows-server/create_vm_custom_image.png)

	> [AZURE.TIP]如果在您嘗試建立 VM 時發生錯誤，並出現錯誤訊息「VHD https://XXXXX... 具有不受支援的虛擬大小 YYYY 位元組。大小必須是整數 (以 MB 為單位)」，這表示您的 VHD 不是整數 MB，且必須是固定大小的 VHD。請嘗試使用 Add-AzureVMImage PowerShell Cmdlet 來新增映像，而不要使用管理入口網站 (請參閱上面的步驟 5)。Azure Cmdlet 可確保 VHD 會符合 Azure 需求。
	
## 後續步驟 ##
 

建立虛擬機器之後，嘗試建立 SQL Server 虛擬機器。如需指示，請參閱[在 Microsoft Azure 上佈建 SQL Server 虛擬機器](virtual-machines-provision-sql-server.md) (英文)。

[步驟 1：準備要上傳的映像]: #prepimage
[步驟 2：在 Azure 中建立儲存體帳戶]: #createstorage
[步驟 3：準備 Azure 的連線]: #prepAzure
[步驟 4：上傳 .vhd 檔案]: #upload
 

<!---HONumber=July15_HO1-->