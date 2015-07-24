<properties 
   pageTitle="選用：為服務設定新的儲存體帳戶"
   description="說明如何針對執行 Update 1 的 StorSimple Manager 服務設定儲存體帳戶。"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/18/2015"
   ms.author="alkohli" />

#### 在 StorSimple 8000 系列 Update 1.0 中新增儲存體帳戶

1. 在 StorSimple Manager 服務登陸頁面上選取您的服務，然後按兩下該服務。這會將您帶領到 [快速入門] 頁面。選取 [設定] 頁面。

2. 按一下 [新增/編輯儲存體帳戶]。

3. 在 [**新增/編輯儲存體帳戶**] 對話方塊中，按一下 [**新增**]：

4. 在 [**提供者**] 欄位中，選取適當的雲端服務提供者。支援的提供者為 Azure、Amazon S3、具有 RRS 的 Amazon S3、HP 及 OpenStack。指定與您的雲端服務提供者之儲存體帳戶相關聯的認證和位置。針對認證顯示的欄位將會不同，取決於您指定的雲端服務提供者。
  - 如果您已選取 Azure 做為您的雲端服務提供者，請為您的 Microsoft Azure 儲存體帳戶提供 [**名稱**] 和主要 [**存取金鑰**]。如果是 Azure 帳戶，會自動填入位置。

        ![Add Azure storage account](./media/storsimple-configure-new-storage-account-u1/AddAzureStorageaccount-include.png)

 - 如果您選取 Amazon S3 或具有 RRS 的 Amazon S3 ，請提供易記的 [**儲存體帳戶名稱**]、[**存取金鑰**] 及 [**祕密金鑰**]。如果是 Amazon S3 和具有 RRS 的 Amazon S3，支援下列位置：

		- US Standard
		- US West (Oregon)
		- US West (Northern California)
		- EU (Ireland)
		- Asia Pacific (Singapore)
		- Asia Pacific (Sydney)
		- Asia Pacific (Tokyo)
		- South America (Sao Paulo)

        ![Add Amazon storage account](./media/storsimple-configure-new-storage-account-u1/AddAmazonStorageaccount-include.png)
	  		
 - 如果您選取 HP 做為您的雲端服務提供者，請提供易記的 [**儲存體帳戶名稱**]、[**租用戶識別碼**]、[**使用者名稱**] 及[**密碼**]。如果是 HP，支援下列位置：

		- US East
		- US West
	  
        ![Add HP storage account](./media/storsimple-configure-new-storage-account-u1/AddHPStorageaccount-include.png)
	  		
 - 如果您選取 **Openstack** 做為您的雲端服務提供者，請提供 [**主機名稱**]、[**存取金鑰**] 及 [**秘密金鑰**]。

        > [AZURE.NOTE] For all the cloud service providers, excluding Azure, a friendly name is allowed. You can use different friendly names and create more than one storage account with the same set of credentials.

        ![Add Openstack storage account](./media/storsimple-configure-new-storage-account-u1/AddOpenstackStorageaccount-include.png)

5. 選取 [啟用 SSL 模式] 來建立裝置與雲端之間網路通訊的安全通道。只有當您在私人雲端內進行操作時，才能清除 [啟用 SSL 模式] 核取方塊。

      >[AZURE.NOTE]如果您使用 HP 做為您的提供者，將一律啟用 SSL。
  		
6. 按一下核取圖示 ![核取圖示](./media/storsimple-configure-new-storage-account/HCS_CheckIcon-include.png)。成功建立儲存體帳戶之後，系統將會通知您。

7. 新建立的儲存體帳戶將顯示於 [設定] 頁面上的 [儲存體帳戶] 下方。按一下 [**儲存**] 以儲存新的儲存體帳戶。系統提示您進行確認時，按一下 [確定]。

<!---HONumber=July15_HO2-->