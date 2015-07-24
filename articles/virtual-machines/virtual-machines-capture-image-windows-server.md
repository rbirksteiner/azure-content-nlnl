<properties 
	pageTitle="對執行 Windows Server 的虛擬機器擷取映像" 
	description="了解如何對執行 Windows Server 2008 R2 的 Azure 虛擬機器 (VM) 擷取映像。" 
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
	ms.date="03/13/2015" 
	ms.author="kathydav"/>

#如何擷取 Windows 虛擬機器以做為範本#

本文說明如何擷取執行 Windows 的 Azure 虛擬機器，以便用它做為範本建立其他虛擬機器。此範本包括 OS 磁碟和任何連接虛擬機器的資料磁碟。它不包含網路組態，因此您將需要在建立使用該範本的其他虛擬機器時加以設定。

Azure 會將此範本視為映像，並將其儲存在 [我的映像] 下方。這也是您已上傳的任何映像儲存所在之處。如需有關映像的詳細資訊，請參閱[關於 Azure 中的虛擬機器映像][]。

##開始之前##

這些步驟假設您已建立 Azure 虛擬機器且設定好作業系統，包括連接任何資料磁碟。如果您還沒這麼做，請參閱下列指示：

- [如何建立自訂虛擬機器][]
- [如何將資料磁碟連接至虛擬機器][]

##擷取虛擬機器##

1. 按一下命令列上的 [連線]，連線到虛擬機器。如需詳細資訊，請參閱[如何登入執行 Windows Server 的虛擬機器][]。

2.	以系統管理員身分開啟 [命令提示字元] 視窗。


3.	切換至 `%windir%\system32\sysprep` 目錄，然後執行 sysprep.exe。


4. 	[系統準備工具] 對話方塊隨即出現。執行下列動作：


	- 在 [系統清理動作] 中選取 [Enter System Out-of-Box Experience (OOBE)]，並確認 [一般化] 已勾選。如需 Sysprep 的詳細用法，請參閱[如何使用 Sysprep：簡介][]。

	- 在 [關機選項]中選取 [關機]。

	- 按一下 [確定]。

	![執行 Sysprep](./media/virtual-machines-capture-image-windows-server/SysprepGeneral.png)

7.	Sysprep 會將虛擬機器關機，這會在[管理入口網站](http://manage.windowsazure.com) 中，將機器的狀態變更為 [已停止]。


8.	按一下 [虛擬機器]，然後選取要擷取的虛擬機器。

9.	按一下命令列上的 [擷取]。

	![擷取虛擬機器](./media/virtual-machines-capture-image-windows-server/CaptureVM.png)

	[擷取虛擬機器] 對話方塊隨即出現。

10.	在 [映像名稱] 中輸入新映像的名稱。

11.	將 Windows Server 映像新增到自訂映像組合之前，必須先如前述步驗所指示，執行 Sysprep 將它一般化。按一下 [I have run Sysprep on the virtual machine]，表示您已這麼做。

12.	按一下打勾記號以擷取映像。

  **注意：當您擷取一般化虛擬機器的映像時，會刪除該虛擬機器。**

 新映像現在會出現在 [映像] 下。![Image capture successful](./media/virtual-machines-capture-image-windows-server/VMCapturedImageAvailable.png)

##後續步驟##
映像已可用來做為範本，建立虛擬機器。若要這麼做，您將需要使用 [從組件庫] 方法並選取您剛建立的映像，建立自訂的虛擬機器。如需指示，請參閱[如何建立自訂虛擬機器][]。

	
[關於 Azure 中的虛擬機器映像]: http://msdn.microsoft.com/library/azure/dn790290.aspx
[如何建立自訂虛擬機器]: virtual-machines-create-custom.md
[如何將資料磁碟連接至虛擬機器]: storage-windows-attach-disk.md
[如何登入執行 Windows Server 的虛擬機器]: http://www.windowsazure.com/manage/windows/how-to-guides/log-on-a-windows-vm/
[如何使用 Sysprep：簡介]: http://technet.microsoft.com/library/bb457073.aspx
[Run Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Enter Sysprep.exe options]: ./media/virtual-machines-capture-image-windows-server/SysprepGeneral.png
[The virtual machine is stopped]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Capture an image of the virtual machine]: ./media/virtual-machines-capture-image-windows-server/CaptureVM.png
[Enter the image name]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Image capture successful]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Use the captured image]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png
 

<!---HONumber=July15_HO1-->