
<properties
	pageTitle="Azure 備份 - 管理虛擬機器"
	description="了解如何管理 Azure 虛擬機器"
	services="backup"
	documentationCenter=""
	authors="jimpark"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/28/2015"
	ms.author="jimpark"/>

# 管理虛擬機器


## 管理受保護的虛擬機器

1. 若要檢視和管理虛擬機器的備份設定，按一下 [**受保護項目**] 索引標籤。

  - 按一下受保護項目的名稱以查看 [**備份詳細資料**] 索引標籤，上面會顯示上次備份的相關資訊。

        ![Virtual machine backup](./media/backup-azure-manage-vms/backup-vmdetails.png)

2. 若要檢視和管理虛擬機器的備份原則設定，按一下 [**原則**] 索引標籤。

  - [**備份原則**] 索引標籤將顯示現有的原則。您可以視需要修改。如果您需要建立新的原則，按一下 [**原則**] 頁面中的 [**建立**]。請注意，如果您想要移除一個原則，該原則就不能與任何虛擬機器相關聯。

        ![Virtual machine policy](./media/backup-azure-manage-vms/backup-vmpolicy.png)

3. 您可以在 [**工作**] 頁面取得更多虛擬機器動作或狀態的相關資訊。按一下清單中的工作以取得詳細資訊，或為特定虛擬機器篩選工作。

    ![作業](./media/backup-azure-manage-vms/backup-job.png)

## 虛擬機器的隨選備份
設定保護後，您可以執行虛擬機器的隨選備份。如果虛擬機器的初始備份已暫止，則隨選備份會在 Azure 備份保存庫中建立虛擬機器的完整複本。如果已完成第一個備份，隨選備份只會將先前備份的變更傳送到 Azure 備份保存庫。

若要進行虛擬機器的隨選備份：

1. 瀏瀏覽至 [**受保護項目** 頁面，並選取 [**Azure 虛擬機器**] 作為 [**類型**] (若尚未選取)，然後按一下 [**選取**] 按鈕。

    ![VM 類型](./media/backup-azure-manage-vms/vm-type.png)

2. 選取您要進行隨選備份的虛擬機器，然後按一下頁面底部的 [**立即備份**] 按鈕。

    ![立即備份](./media/backup-azure-manage-vms/backup-now.png)

    這會在所選的虛擬機器上建立備份作業。透過這項作業建立的復原點保留範圍，將會與在虛擬機器相關原則中指定的保留範圍相同。

    ![建立備份作業](./media/backup-azure-manage-vms/creating-job.png)

    >[AZURE.NOTE]若要檢視與虛擬機器相關聯的原則，請向下切入到 [**受保護項目**] 頁面中的虛擬機器，並移至 [備份原則] 索引標籤。

3. 建立作業之後，您可以按一下快顯通知列中的 [**檢視作業**]，以在 [作業] 頁面中查看對應的作業。

    ![建立的備份作業](./media/backup-azure-manage-vms/created-job.png)

4. 順利完成作業之後，將會建立可供您還原虛擬機器的復原點。這也會使 [**受保護項目**] 頁面中 的復原點資料行值遞增 1。

## 停止保護虛擬機器
您可以透過下列選項，選擇停止虛擬機器的未來備份：

- 保留 Azure 備份保存庫中與虛擬機器相關聯的備份資料
- 刪除與虛擬機器相關聯的備份資料

如果您已選取第一個選項，您可以使用備份資料來還原虛擬機器。如需這類虛擬機器的定價詳細資訊，請按一下[這裡](http://azure.microsoft.com/pricing/details/backup/)。

若要停止虛擬機器的保護：

1. 瀏瀏覽至 [**受保護項目** 頁面，並選取 [**Azure 虛擬機器**] 作為篩選類型 (若尚未選取)，然後按一下 [**選取**] 按鈕。

    ![VM 類型](./media/backup-azure-manage-vms/vm-type.png)

2. 選取虛擬機器，然後按一下頁面底部的 [**停止保護**]。

    ![停止保護](./media/backup-azure-manage-vms/stop-protection.png)

3. 根據預設，Azure 備份不會刪除與虛擬機器相關聯的備份資料。

    ![確認停止保護](./media/backup-azure-manage-vms/confirm-stop-protection.png)

    如果您要刪除備份資料，請選取此核取方塊。

    ![核取方塊](./media/backup-azure-manage-vms/checkbox.png)

    請選取停止備份的原因。雖然這是選擇性動作，但提供原因可幫助 Azure 備份處理意見反應，並設定客戶案例的優先順序。

4. 按一下 [**提交**] 按鈕以提交 [**停止保護**] 作業。按一下 **檢視作業**] 以在 [**作業**] 頁面中查看對應作業。

    ![停止保護](./media/backup-azure-manage-vms/stop-protect-success.png)

    如果您未在 [**停止保護**] 精靈中選取 [**刪除相關聯的備份資料**] 選項，然後在作業完成後，保護狀態會變更為 [**已停止保護**]。資料將會使用 Azure 備份保留，直到被明確刪除為止。您隨時都可藉由在 [受保護的項目] 頁面中選取虛擬機器，然後按一下 [刪除] 來刪除資料。

    ![已停止保護](./media/backup-azure-manage-vms/protection-stopped-status.png)

    如果您已選取 [**刪除相關聯的備份資料**] 選項，則虛擬機器將不會出現在 [**受保護項目**] 頁面中。

## 重新保護虛擬機器
如果您未選取 [**停止保護**] 中的 [**刪除相關聯的備份資料**] 選項，您可以遵循類似於備份已註冊虛擬機器的步驟，重新保護虛擬機器。一旦受保護，此虛擬機器會在停止保護之前保留備份資料，而在重新保護之後建立復原點。

重新保護之後，如果有 [**停止保護**] 之前的復原點，則虛擬機器的保護狀態會變更為 [**受保護**]。

  ![重新保護的 VM](./media/backup-azure-manage-vms/reprotected-status.png)

>[AZURE.NOTE]重新保護虛擬機器時，您可以選擇與最初用於保護虛擬機器不同的原則。

## 取消註冊虛擬機器

如果您想要從備份保存庫移除虛擬機器：

1. 按一下頁面底部的 [**取消註冊**] 按鈕。

    ![停用保護](./media/backup-azure-manage-vms/unregister-button.png)

    快顯通知會出現在畫面底部要求確認。按一下 [**是**] 以繼續。

    ![停用保護](./media/backup-azure-manage-vms/confirm-unregister.png)

## 刪除備份資料
您可以刪除與虛擬機器相關聯的備份資料：

- 在停止保護作業期間
- 在虛擬機器上完成停止保護作業之後

對於在成功完成 [**停止備份**] 作業後處於 [已停止保護] 狀態的虛擬機器，若要刪除其上的備份資料：

1. 瀏覽至 [**受保護項目** 頁面，並選取 [**Azure 虛擬機器**] 作為類型，然後按一下 [**選取**] 按鈕。

    ![VM 類型](./media/backup-azure-manage-vms/vm-type.png)

2. 選取虛擬機器。虛擬機器會處於 [**已停止保護**] 狀態。

    ![已停止保護](./media/backup-azure-manage-vms/protection-stopped-b.png)

3. 按一下頁面底部的 [**刪除**] 按鈕。

    ![刪除備份](./media/backup-azure-manage-vms/delete-backup.png)

4. 在 [**刪除備份資料**] 精靈中，選取刪除備份資料的原因 (強烈建議)，然後按一下 [**提交**]。

    ![刪除備份資料](./media/backup-azure-manage-vms/delete-backup-data.png)

5. 這會建立一項作業以刪除所選虛擬機器的備份資料。按一下 [**檢視作業**] 以在 [作業] 頁面中查看對應作業。

    ![成功刪除資料](./media/backup-azure-manage-vms/delete-data-success.png)

    完成此作業後，將從 [**受保護項目**] 頁面中移除虛擬機器的對應項目。


###儀表板

在 [**儀表板**] 頁面中，您可以檢閱有關 Azure 虛擬機器、其儲存體和過去 24 小時內相關聯作業的資訊。您可以檢視備份狀態和任何相關聯的備份錯誤。

  ![儀表板](./media/backup-azure-manage-vms/dashboard-protectedvms.png)
 

<!---HONumber=62-->