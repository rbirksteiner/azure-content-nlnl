<properties 
   pageTitle="建立磁碟區"
   description="說明如何在 StorSimple 裝置上新增磁碟區。"
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="04/28/15"
   ms.author="v-sharos" />

#### 建立磁碟區

1. 在裝置的 [快速入門] 頁面上，按一下 [新增磁碟區]。這樣會啟動 [新增磁碟區] 精靈。

2. 在 [新增磁碟區] 精靈的 [基本設定] 下方：
   1. 輸入磁碟區的 [名稱]。
   2. 為磁碟區指定 [佈建的容量]。**磁碟區容量必須介於 1 GB 到 64 TB 之間。**
   3. 在下拉式清單中，為磁碟區選取 [使用類型] 。針對存取頻率較低的封存資料，選取 [封存磁碟區]。對於所有其他資料類型，選取 [主要磁碟區]。
   4. 按一下箭頭圖示 ![arrow-icon](./media/storsimple-create-volume/HCS_ArrowIcon-include.png) 以移至下一頁。

     ![新增磁碟區](./media/storsimple-create-volume/HCS_AddVolume1M-include.png)

3. 在 [其他設定] 對話方塊中，加入新的存取控制記錄 (ACR)：
   1. 提供 ACR 的 [名稱]。
   2. 在 [iSCSI 啟動器名稱] 下方，提供 Windows 主機的 iSCSI 完整格式名稱 (IQN)。如果沒有 IQN，請移至 [取得 Windows Server 主機的 IQN][](#get-the-iqn-of-a-windows-server-host)。
   3. 在 [此磁碟區的預設備份嗎？] 下方，選取 [啟用] 核取方塊。預設備份將會建立原則，在每天的 22:30 (裝置時間) 執行，並建立此磁碟區的雲端快照。

     >[AZURE.NOTE]在此處啟用備份之後，就無法加以還原。您必須編輯磁碟區，才能修改此設定。

     ![新增磁碟區](./media/storsimple-create-volume/HCs_AddVolume2M-include.png)

4. 按一下核取圖示 ![核取圖示](./media/storsimple-create-volume/HCS_CheckIcon-include.png)。使用指定的設定來建立磁碟區。

<!---HONumber=July15_HO2-->