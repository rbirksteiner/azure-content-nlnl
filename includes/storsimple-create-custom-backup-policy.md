<properties 
   pageTitle="建立自訂的 StorSimple 備份原則"
   description="說明如何使用 StorSimple Manager 服務建立自訂備份原則。"
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
   ms.date="05/14/2015"
   ms.author="v-sharos" />

#### 若要建立自訂備份原則

1. 在 [**裝置**] 頁面上，按一下 [**備份原則**]，然後按一下 [**新增**]。

2. 在 [**新增備份原則**] 對話方塊中，[**定義備份原則**] 之下：

    1. 指定備份原則名稱。

    2. 選取要加入至這個原則的磁碟區。您可以從下拉式清單選擇磁碟區來加入多個磁碟區。

    3. 按一下核取圖示 ![核取圖示](./media/storsimple-add-backup-policy/HCS_CheckIcon-include.png)。

     在成功建立原則之後，系統將會通知您。備份原則頁面會同時更新，以顯示新建立的原則。

4. 按一下原則名稱 (第一個資料行) 以深入了解您剛剛建立之原則的詳細資料。

5. 按一下 [**管理排程**]。

6. 在 [**管理排程**] 對話方塊中：

    1. 選取 [**新建**] 以新增其他排程。

    2. 從下拉式清單中。選擇備份類型為 [**本機**] 或 [**雲端**] 快照集。

    3. 指定備份頻率 (以分鐘、小時、天或週為單位)。

    4. 選取保留期。保留選項會根據備份頻率而定。例如，針對每日原則，可以指定保留幾週，而針對每月原則，則可指定保留幾個月。
 
    5. 選取原則的開始時間和日期。

    6. 選取核取方塊以啟用原則。

7. 按一下核取圖示 ![核取圖示](./media/storsimple-add-backup-policy/HCS_CheckIcon-include.png) 以完成。

8. 您將返回原則詳細資料。按一下 [**儲存**] 儲存您對此原則所做的變更。當原則已儲存時將會通知您。

9. 瀏覽回 [**備份原則**] 頁面。備份原則的表格式清單將會更新，以顯示已修改的原則。

    ![自訂備份原則](./media/storsimple-create-custom-backup-policy/HCS_CustomBackupPolicyM-include.png)。

<!---HONumber=July15_HO2-->