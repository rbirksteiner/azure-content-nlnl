<properties
   pageTitle="Azure 備份 - 從 Windows Server 或 Windows 用戶端進行備份和還原"
   description="了解如何從 Windows Server 或 Windows 用戶端進行備份和還原。本文章也涵蓋替代伺服器復原"
   services="backup"
   documentationCenter=""
   authors="prvijay"
   manager="shreeshd"
   editor=""/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="article"
	 ms.date="04/02/2015"
	 ms.author="prvijay"/>

# 從 Windows Server 或 Windows 用戶端電腦進行備份和還原
本文涵蓋從 Windows Server 或 Windows 用戶端電腦進行備份所需的步驟。也包含在相同電腦上還原備份檔案，以及在任何其他電腦上還原備份檔案所需的步驟。

## 備份檔案
1. 電腦已註冊後，請開啟 Microsoft Azure 備份 MMC 嵌入式管理單元。<br/> ![搜尋結果][1]

2. 按一下 [**排程備份**] <br/> ![排程備份][2]

3. 選取您想要備份的項目。Windows Server/Windows 用戶端 (例如不包含 System Center Data Protection Manager) 上的 Azure 備份可讓您保護檔案和資料夾。<br/> ![要備份的項目][3]

4. 依照下列[文章](backup-azure-backup-cloud-as-tape.md)中的詳細說明指定備份排程和保留原則

5. 選擇傳送初始備份的方法。您選擇的完成初始植入效率取決於您想要備份的資料量，以及網際網路的上傳連結速度。如果您打算在高延遲、低頻寬連接的情況下備份高達 GB/TB 的資料，建議您將磁碟送至最接近的 Azure 資料中心以完成初始備份。這稱為「離線備份」，在此[文章](https://msdn.microsoft.com/library/azure/dn894419.aspx)中涵蓋詳細說明。如果您有足夠的頻寬連接，建議您透過網路完成初始備份。<br/> ![初始備份][4]

6. 當工作流程完成時，請返回至 MMC 嵌入式管理單元，然後按一下 [**立即備份**] 以透過網路完成初始植入。<br/> ![立即備份][5]

7. 完成初始植入後，Azure 備份主控台中的 [**工作**] 檢視會表示出狀態。<br/> ![IR 已完成][6]

## 在相同電腦上復原資料
如果您不小心刪除檔案，而且您想要在相同電腦上還原檔案/磁碟區 (備份進行處)，下列步驟可協助您復原資料。

1. 開啟**Microsoft Azure 備份**嵌入式管理單元。

2. 按一下 [**復原資料**] 來初始化工作流程。<br/> ![復原資料][7]

3. 選取 **這台伺服器 (* yourmachinename *)** 選項，若您打算還原備份在同一部電腦上的檔案。<br/> ![相同電腦][8]

4. 您可以選擇 [**瀏覽檔案**] 或 [**搜尋檔案**]。如果您打算還原路徑已知的一個或多個檔案，請保留預設選項。如果您不確定資料夾結構，但想要搜尋檔案，請挑選 [**搜尋檔案**] 選項。為了達成本章節的目的，我們將使用預設選項繼續進行。<br/> ![瀏覽檔案][9]

5. 在下一個畫面中，請選取您要還原檔案所在的磁碟區。該畫面可讓您從任何時間點進行還原。日曆控制項中以**粗體**顯示的日期會指出還原點的可用性。選取日期之後，您可以根據您的備份排程 (以及成功的備份作業)，從 [**時間**] 下拉式清單選取時間點。<br/> ![磁碟區和日期][10]

6. 在下一個畫面中，請選取您想要復原的項目。您可以複選想要還原的資料夾/檔案。<br/> ![選取檔案][11]

7. 在下一個畫面中，請指定復原參數。<br/> ![修復選項][12]
  + 您可以選擇還原至原始位置 (其中的檔案/資料夾可能會遭到覆寫) 或相同電腦中的其他位置。

  + 如果您想要還原的檔案/資料夾存在於目標位置，您可以選擇建立複本 (相同檔案的兩個版本)，或覆寫目標位置中的檔案，或略過存在於目標的檔案修復。

  + 強烈建議您針對正在復原檔案上的 ACL 保留預設還原選項。

8. 一旦提供這些輸入，復原工作流程即會啟動，將檔案還原至此電腦。

## 還原至其他電腦
若您遺失整個伺服器，您仍可將檔案/磁碟區回復至不同的電腦。下列步驟說明工作流程。

步驟中使用的術語表如下：+ *來源電腦* – 用來進行備份且目前無法使用的的原始電腦。

  + *目標電腦* – 用來擷取資料的電腦。

  + *範例保存庫* – *來源電腦*和*目標電腦*註冊的備份保存庫。<br/>

> [AZURE.NOTE]從電腦進行的備份無法在執行舊版作業系統的電腦上進行還原。例如，如果從 Windows 7 電腦進行備份，則可以在 Windows 8 或更新版電腦上進行還原。不過若情況相反，便無法進行還原。

1. 在*目標電腦*開啟 **Microsoft Azure 備份**嵌入式管理單元。

2. 確定*目標電腦*和*來源電腦*將還原至相同的備份保存庫 (如同此文章*範例保存庫*所述)。

3. 按一下 [**復原資料**] 來初始化工作流程。<br/> ![復原資料][7]

4. 選取 [**其他伺服器**] <br/> ![其他伺服器][13]

5. 提供對應至*範例保存庫*的保存庫認證檔。如果保存庫認證檔是無效的 (或已過期)，請從 Azure 入口網站中的*範例保存庫*下載新的保存庫認證檔。一旦提供保存庫認證檔，即會顯示保存庫認證檔的備份保存庫。

6. 在顯示電腦的清單中選取 [*來源電腦*]。<br/> ![電腦清單][14]

7. 如同先前操作，選取 [**搜尋檔案**] 或 [**瀏覽檔案**] 選項。為了達成本章節的目的，我們將使用 [**搜尋檔案**] 選項。<br/> ![Search][15]

8. 在下一個畫面中選取磁碟區和日期。搜尋您想要還原的資料夾/檔案名稱。<br/> ![搜尋項目][16]

9. 選取需要進行還原的檔案所在位置。<br/> ![還原位置][17]

10. 提供*來源電腦*註冊至*範例保存庫*期間所提供的加密複雜密碼。<br/> ![加密][18]

一旦提供輸入，則按一下 [**復原**] 按鈕將會觸發在所提供目的地還原備份檔案。

<!--Image references-->
[1]: ./media/backup-azure-backup-and-recover/result.png
[2]: ./media/backup-azure-backup-and-recover/schedulebackup.png
[3]: ./media/backup-azure-backup-and-recover/items.png
[4]: ./media/backup-azure-backup-and-recover/initialbackup.png
[5]: ./media/backup-azure-backup-and-recover/backupnow.png
[6]: ./media/backup-azure-backup-and-recover/ircomplete.png

[7]: ./media/backup-azure-backup-and-recover/recover.png
[8]: ./media/backup-azure-backup-and-recover/samemachine.png
[9]: ./media/backup-azure-backup-and-recover/browseandsearch.png
[10]: ./media/backup-azure-backup-and-recover/volanddate.png
[11]: ./media/backup-azure-backup-and-recover/selectfiles.png
[12]: ./media/backup-azure-backup-and-recover/recoveroptions.png

[13]: ./media/backup-azure-backup-and-recover/anotherserver.png
[14]: ./media/backup-azure-backup-and-recover/machinelist.png
[15]: ./media/backup-azure-backup-and-recover/search.png
[16]: ./media/backup-azure-backup-and-recover/searchitems.png
[17]: ./media/backup-azure-backup-and-recover/restorelocation.png
[18]: ./media/backup-azure-backup-and-recover/encryption.png
 

<!---HONumber=62-->