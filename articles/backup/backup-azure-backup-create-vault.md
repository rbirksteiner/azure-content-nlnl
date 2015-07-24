<properties
   pageTitle="Azure 備份 - 建立備份保存庫和指定儲存體備援性"
   description="了解如何在 Azure 備份中建立備份保存庫以及指定儲存體備援選項"
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
	 ms.date="04/08/2015"
	 ms.author="prvijay"/>

# 建立備份保存庫
若要將 Windows Server 或 System Center Data Protection Manager (SCDPM) 的檔案和資料備份至 Azure，或將 IaaS VM 備份至 Azure，您必須在要儲存資料的地區區域中建立一個備份保存庫。

本教學課程將引導您建立用來儲存備份的保存庫。

1. 登入[管理入口網站](https://manage.windowsazure.com/)
2. 按一下 [**新增**] -> [**資料服務**] -> [**復原服務**] -> [**備份保存庫**] -> 並選擇 [**快速建立**] <br/> ![建立保存庫][1]

3. 針對 [**名稱**] 參數，輸入易記名稱來識別備份保存庫。每個訂用帳戶皆需為唯一名稱。

4. 針對 [**區域**] 參數，選取備份保存庫的地區區域。您的選擇會決定備份資料所要傳送的地區。透過選擇靠近您位置的地區，您可以在備份至 Azure 時減少網路延遲。

5. 按一下 [**建立保存庫**] 以完成工作流程。要等備份保存庫建立好，可能需要一些時間。若要檢查狀態，您可以監視位於入口網站底部的通知。<br/> ![正在建立保存庫][2]

6. 建立備份保存庫之後，將會有一則訊息告訴您已成功建立保存庫，並且該保存庫會在「復原服務」的資源中列為 [**使用中**] 狀態。<br/> ![正在建立保存庫狀態][3]


## Azure 備份 - 儲存體備援選項

識別儲存體備援選項的最佳時機，在於保存庫建立之後，以及任何電腦註冊至保存庫之前。一旦項目已註冊至保存庫，儲存體備援選項即遭到鎖定且無法修改。

您的業務需求會決定 Azure 備份後端儲存體的儲存體備援性。如果您使用 Azure 作為主要的備份儲存體端點 (例如您正從 Windows Server 備份至 Azure)，您應該考慮挑選 (預設值) 異地備援儲存體選項。您會在備份保存庫的 [**設定**] 選項下方看到此選項。<br/> ![GRS][4]

### 異地備援儲存體 (GRS)
GRS 可維護六個資料複本。有了 GRS，您的資料會在主要區域內複寫三次，並在與主要區域相距甚遠的次要區域內複寫三次，提供最高等級的持久性。將資料儲存在 GRS 時，若主要區域發生故障，則 Azure 備份可確保您的資料持續儲存在兩個不同區域中。

### 本機備援儲存體 (LRS)
本機備援儲存體 (LRS) 可維護三個資料複本。LRS 會在單一區域的單一設備內複寫三次。LRS 可保護您的資料免於一般硬體故障，但無法避免整體 Azure 設備故障。

如果您使用 Azure 作為第三個備份儲存體端點 (例如您使用 SCDPM 讓本機備份複製內部部署，並使用 Azure 以符合長期保留需要)，您應該考慮從備份保存庫的 [**設定**] 選項選擇本機備援儲存體。這將減少在 Azure 中儲存資料的成本，同時針對您可能會接受第三個複本的資料提供較低層級的持久性。<br/> ![LRS][5]




## 注意

+ 從 2015 年 3 月開始，客戶不需要以程式設計方式 (例如：PowerShell) 來建立備份保存庫。

+ 儲存體備援必須於保存庫建立之後，以及任何電腦註冊至保存庫之前進行選取。一旦項目已註冊至保存庫，儲存體備援選項即遭到鎖定且無法修改。

<!--Image references-->
[1]: ./media/backup-azure-backup-create-vault/createvault1.png
[2]: ./media/backup-azure-backup-create-vault/creatingvault1.png
[3]: ./media/backup-azure-backup-create-vault/backupvaultstatus1.png
[4]: ./media/backup-azure-backup-create-vault/grs.png
[5]: ./media/backup-azure-backup-create-vault/lrs.png
 

<!---HONumber=62-->