<properties
   pageTitle="下載、安裝和註冊 Azure 備份代理程式"
   description="了解下載 Azure 備份代理程式的方式和位置、安裝步驟，以及如何使用保存庫認證來註冊 Azure 備份代理程式"
   services="backup"
   documentationCenter=""
   authors="prvijay"
   manager="shreeshd"
   editor=""/>
<tags
   ms.service="backup"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage-backup-recovery"
   ms.date="04/08/2015"
   ms.author="prvijay"/>

# 下載、安裝和註冊 Azure 備份代理程式

建立 Azure 備份保存庫之後，您的每個內部部署伺服器 (Windows Server、Windows 用戶端或 System Center Data Protection Manager 伺服器) 上應該安裝代理程式，以便讓您將資料和應用程式備份至 Azure。本文章涵蓋在 Windows Server 或 Windows 用戶端電腦上設定 Azure 備份代理程式所需的步驟。

1. 登入[管理入口網站](https://manage.windowsazure.com/)

2. 按一下 [復原服務]，然後選取您要向伺服器註冊的備份保存庫。備份保存庫的 [快速入門] 頁面隨即出現。<br/> ![快速入門][1]

3. 在 [快速入門] 頁面上，按一下 [**下載代理程式**] 選項下方的 [ **適用於 Windows Server 或 System Center Data Protection Manager 或 Windows 用戶端**]。按一下 [儲存] 將其複製至本機電腦上。<br/> ![儲存代理程式][2]

4. 代理程式安裝之後，按兩下 MARSAgentInstaller.exe 來啟動 Azure 備份代理程式的安裝。選擇安裝資料夾和代理程式所需要的暫存資料夾。指定的快取位置必須至少包含備份資料大小 5%的可用空間。

5.	若您使用 Proxy 伺服器連接至網際網路，請在 [**Proxy 組態**] 畫面上，輸入 Proxy 伺服器詳細資料。若您使用已驗證的 Proxy，請在此畫面中輸入使用者名稱和密碼的詳細資料。

6.	Azure 備份代理程式會安裝 .NET Framework 4.5 和 Windows PowerShell (若尚未安裝) 來完成安裝。

7.	安裝代理程式之後，按一下 [**前往註冊**] 按鈕以繼續與工作流程。<br/> ![註冊][3]

8. 在保存庫認證畫面中，瀏覽並選取先前已下載的保存庫認證檔。<br/> ![保存庫認證][4] <br/> <br/>
> [AZURE.NOTE]保存庫認證檔僅於 48 小時內有效 (從入口網站下載後起算)。如果您在此畫面中遇到任何錯誤 (例如「提供的保存庫認證檔已過期」)，請登入 Azure 入口網站，並再次下載保存庫認證檔。
>
> 請確定保存庫認證檔位於可透過設定應用程式存取的位置。如果您遇到存取權相關的錯誤，請將保存庫認證檔複製至此電腦中的暫存位置並重試作業。
>
> 如果您遇到保存庫認證無效的錯誤 (例如「提供的保存庫認證」無效。檔案可能損毀或沒有與復原服務相關聯的最新認證」，請從入口網站下載新的保存庫認證檔，之後再重試作業。若使用者在 Azure 入口網站中連續快速地按下 [下載保存庫認證] 選項，則通常會看見此錯誤。在此情況下，僅第二個保存庫認證檔為有效。

9. 在 [**加密設定**] 畫面中，您可以產生複雜密碼或提供複雜密碼 (最少 16 個字元)，並請務必將複雜密碼儲存在安全的位置。<br/> ![加密][5] <br/> <br/>
> [AZURE.WARNING]如果遺失或忘記複雜密碼，Microsoft 將無法協助您復原備份資料。加密複雜密碼為使用者所有，Microsoft 將不會看到使用者所使用的複雜密碼。請將檔案儲存在安全的位置，如同復原作業期間所需。

10. 一旦您按一下 [**完成**] 按鈕，電腦即成功註冊至保存庫，且現在您已準備好開始備份至 Microsoft Azure。您可透過按一下 Azure 備份 MMC 嵌入式管理單元中 [**變更屬性**] 選項，來修改註冊工作流程期間所指定的設定。<br/> ![變更屬性][6]

## 後續步驟
+ 若您有關於工作流程的任何問題，請參閱安裝和設定 Azure 備份的[常見問題集](backup-azure-backup-faq.md)。


<!--Image references-->
[1]: ./media/backup-azure-backup-download-register/quickstart.png
[2]: ./media/backup-azure-backup-download-register/agent.png
[3]: ./media/backup-azure-backup-download-register/register.png
[4]: ./media/backup-azure-backup-download-register/vc.png
[5]: ./media/backup-azure-backup-download-register/encryption.png
[6]: ./media/backup-azure-backup-download-register/change.png
 

<!---HONumber=62-->