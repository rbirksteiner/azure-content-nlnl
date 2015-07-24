<properties pageTitle="透過備份組還原 StorSimple 磁碟區" | Microsoft Azure description="說明如何利用備份目錄透過備份組還原 StorSimple 磁碟區" services="storsimple" documentationCenter="NA" authors="SharS" manager="carolz" editor="" /> <tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="06/25/2015"
   ms.author="v-sharos" />

# 從備份組還原 StorSimple 磁碟區

## 概觀

[備份類別目錄] 頁面會顯示在產生手動或自動備份時建立的所有備份組。您可以使用此頁面來列出備份原則或磁碟區的所有備份、選取或刪除備份，或是使用備份來還原或複製磁碟區。

 ![備份類別目錄頁面](./media/storsimple-restore-from-backup-set/HCS_BackupCatalog.png)

本教學課程說明如何使用 [備份類別目錄] 頁面，從備份組還原您的裝置。

## 如何使用備份類別目錄 

[備份類別目錄] 頁面提供查詢，可協助您縮小備份組選取範圍。您可以篩選根據下列參數擷取的備份組：

- **裝置** - 建立備份組所在的裝置。
- **備份原則** 或 **磁碟區** - 與此備份組相關聯的備份原則或磁碟區。
- **從** 和 **至** - 建立備份組的日期和時間範圍。

接著會根據下列屬性，將篩選的備份組列表顯示：

- **名稱** - 與備份組相關聯的備份原則或磁碟區的名稱。
- **大小** - 備份組的實際大小。
- **建立日期** - 建立備份的日期和時間。 
- **類型** - 備份組可以是本機快照集或雲端快照集。本機快照是本機儲存於裝置上的所有磁碟區資料備份，而雲端快照是指位於雲端的磁碟區資料備份。本機快照可提供更快速的存取，而雲端快照是選擇來進行資料復原。
- **起始者** - 備份可根據排程自動初始，或由使用者手動初始。(您可以使用備份原則來排程備份。或者，可以使用 [取得備份] 選項來取得互動式備份。)

## 如何從備份還原您的裝置

您可以使用 [備份類別目錄] 頁面，從特定的備份還原裝置。不過，請記住，還原磁碟區會將磁碟區的狀態還原為其在取得備份時的狀態。在備份作業之後新增的所有資料都將遺失。

> [AZURE.WARNING]從備份還原將從備份取代現有的磁碟區。這可能會造成在取得備份之後寫入的所有資料遺失。


### 從備份組還原

1. 在 StorSimple Manager 服務頁面上，按一下 [備份類別目錄] 索引標籤。

    ![備份類別目錄](./media/storsimple-restore-from-backup-set/HCS_Restore.png)

2. 選取備份組，如下所示：
  1. 選取適當的裝置。
  2. 在下拉式清單中，針對要選取的備份選擇磁碟區或備份原則。
  3. 指定時間範圍。
  4. 按一下核取圖示 ![核取圖示](./media/storsimple-restore-from-backup-set/HCS_CheckIcon.png) 以執行此查詢。
 
    與選取的磁碟區或備份原則相關聯的備份應該會出現在備份組清單中。

3. 展開備份組以檢視相關聯的磁碟區。您必須先在主機和裝置上將這些磁碟區離線，才能還原它們。存取 [磁碟區容器] 頁面上的磁碟區，然後依照 [讓磁碟區離線][](storsimple-manage-volumes.md#take-a-volume-offline) 中的指示來讓它們離線。

    >  [AZURE.IMPORTANT]確定您已先讓主機上的磁碟區離線，然後再讓裝置上的磁碟區離線。如果您未讓主機上的磁碟區離線，則 StorSimple Manager 服務會自動在主機上讓它們離線。這可能會導致資料損毀。

4. 瀏覽回到 [備份類別目錄] 索引標籤，並選取備份組。

5. 按一下頁面底部的 [還原]。

6. 系統將提示您進行確認。

    ![確認電子郵件](./media/storsimple-restore-from-backup-set/HCS_ConfirmRestore.png)

7. 檢閱還原資訊，然後按一下核取圖示 ![核取圖示](./media/storsimple-restore-from-backup-set/HCS_CheckIcon.png)。這將會初始還原工作，而您可以藉由存取 [工作] 頁面來進行檢視。

8. 還原完成之後，您可以確認磁碟區的內容已由備份的磁碟區所取代。

## 後續步驟

了解如何[管理 storsimple 磁碟區](storsimple-manage-volumes.md)

<!---HONumber=July15_HO2-->