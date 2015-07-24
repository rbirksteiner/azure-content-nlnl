
### 建立備份

1. 在裝置的 [快速入門] 頁面上，按一下 [新增備份原則]。這將會啟動 [新增備份原則] 精靈。 

2. 在 [定義備份原則] 頁面上：
  1. 為備份原則提供包含 3 到 150 個字元的名稱。
  2. 選取要備份的磁碟區。如果您選取一個以上的磁碟區，這些磁碟區將會群組在一起，以建立可在當機時保持一致的備份。
  3. 按一下箭頭圖示 ![arrow-icon](./media/storsimple-take-backup/HCS_ArrowIcon-include.png)。 
  
    ![Add-backup-policy](./media/storsimple-take-backup/HCS_AddBackupPolicyWizard1M-include.png)

3. 在 [定義排程] 頁面上：
  1. 從下拉式清單中選取備份的類型。針對更快速的還原，選取 [本機快照]。針對資料恢復功能，選取 [雲端快照]。
  2. 指定備份頻率 (以分鐘、小時、天或週為單位)。
  3. 選取保留時間。保留選項會根據備份頻率而定。例如，針對每日原則，可以指定保留幾週，而針對每月原則，則可指定保留幾個月。
  4. 選取備份原則的開始時間和日期。
  5. 選取 [啟用] 核取方塊以啟用備份原則。 
  6. 按一下核取圖示 ![核取圖示](./media/storsimple-take-backup/HCS_CheckIcon-include.png) 以儲存原則。

    ![Add-backup-policy](./media/storsimple-take-backup/HCS_AddBackupPolicyWizard2M-include.png)
 
     您現在擁有的備份原則將會建立磁碟區資料的排程備份。

您已經完成裝置設定。

<!---HONumber=July15_HO2-->