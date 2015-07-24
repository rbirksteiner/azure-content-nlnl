<properties 
	pageTitle="在 Azure App Service 中還原 Web 應用程式" 
	description="了解如何從備份還原您的 Web 應用程式。" 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	writer="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="cephalin"/>

# 在 Azure App Service 中還原 Web 應用程式

本文說明如何使用 [App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) 備份功能，來還原您先前備份的 Web 應用程式。如需詳細資訊，請參閱 [App Service Web Apps 備份](web-sites-backup.md)。

Web Apps 還原功能可讓您依需求將 Web 應用程式還原為先前的狀態，或是根據原始 Web 應用程式的其中一個備份建立新的 Web 應用程式。建立以平行方式執行最新版本的新 Web 應用程式，對於 A/B 測試將有所幫助。

Web Apps 還原功能可在 [Azure 入口網站](http://go.microsoft.com/fwlink/?LinkId=529715)的 [備份] 刀鋒視窗上取得，僅適用於標準和高階模式。如需使用標準或高階模式調整應用程式規模的相關資訊，請參閱在 [Azure App Service 中調整 Web 應用程式規模](web-sites-scale.md)。請注意，高階模式能夠透過標準模式執行更多每日備份。

<a name="PreviousBackup"></a>
## 從先前製作的備份還原 Web 應用程式

1. 在 Azure 入口網站中 Web 應用程式的 [設定] 刀鋒視窗上，按一下 [備份] 選項來顯示 [備份] 刀鋒視窗。在此刀鋒視窗中捲動，並根據 [備份時間] 和 [狀態]，從備份清單中選取其中一個備份項目。
	
	![Choose backup source][ChooseBackupSource]
	
2. 選取 [備份] 刀鋒視窗頂端的 [立即還原]。

	![選擇立即還原][ChooseRestoreNow]

3. 在 [還原] 刀鋒視窗中，若要還原現有的 Web 應用程式，請確認所有顯示的詳細資料，然後按一下 [確定]。
	
您也可以從 [還原] 刀鋒視窗選取 [WEB 應用程式] 組件，然後選取 [建立新的 Web 應用程式] 組件，將您的 Web 應用程式還原為新的 Web 應用程式。
	
<a name="StorageAccount"></a>
## 從儲存體帳戶下載或刪除備份
	
1. 從 Azure 入口網站的主要 [瀏覽] 刀鋒視窗中，選取 [儲存體帳戶]。
	
	隨即會出現您現有的儲存體帳戶清單。
	
2. 選取包含您想要下載或刪除之備份的儲存體帳戶。
	
	[儲存體] 刀鋒視窗隨即顯示。

3. 選取 [儲存體] 刀鋒視窗中的 [容器] 組件，以顯示 [容器] 刀鋒視窗。
	
	隨即會顯示容器清單。這份清單也會顯示 URL 以及上次修改此容器的日期。
	
	![檢視容器][ViewContainers]

4. 在清單中，選取容器以顯示刀鋒視窗，其中會顯示檔案名稱清單，以及每個檔案的大小。
	
5. 選取檔案之後，您可以選擇 [下載] 或 [刪除] 檔案。請注意，有兩種主要的檔案類型：.zip 檔案和.xml 檔案。

<a name="OperationLogs"></a>
## 檢視稽核記錄
	
1. 若要查看有關 Web 應用程式還原作業成功或失敗的詳細資訊，請選取主要 [瀏覽] 刀鋒視窗的 [稽核記錄] 組件。 
	
	[音訊記錄] 刀鋒視窗會顯示您的所有作業，以及層級、狀態、資源和時間詳細資料。
	
2. 捲動刀鋒視窗來尋找與您的 Web 應用程式相關的作業。
3. 若要檢視有關作業的其他詳細資訊，請在清單中選取該作業。
	
[詳細資料] 刀鋒視窗將會顯示與該作業有關的可用資訊。
	
>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期入門 Web 應用程式。不需要信用卡；沒有承諾。
	
## 變更的項目
* 如需從網站變更為 App Service 的指南，請參閱：[Azure App Service 及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 如需從舊的入口網站變更為新入口網站的指南，請參閱：[巡覽預覽入口網站的參考](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- IMAGES -->
[ChooseBackupSource]: ./media/web-sites-restore/01ChooseBackupSource.png
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
[StorageAccountFile]: ./media/web-sites-restore/02StorageAccountFile.png
[BrowseCloudStorage]: ./media/web-sites-restore/03BrowseCloudStorage.png
[StorageAccountFileSelected]: ./media/web-sites-restore/04StorageAccountFileSelected.png
[ChooseRestoreSettings]: ./media/web-sites-restore/05ChooseRestoreSettings.png
[ChooseDBServer]: ./media/web-sites-restore/06ChooseDBServer.png
[RestoreToNewSQLDB]: ./media/web-sites-restore/07RestoreToNewSQLDB.png
[NewSQLDBConfig]: ./media/web-sites-restore/08NewSQLDBConfig.png
[RestoredContosoWebSite]: ./media/web-sites-restore/09RestoredContosoWebSite.png
[DashboardOperationLogsLink]: ./media/web-sites-restore/10DashboardOperationLogsLink.png
[ManagementServicesOperationLogsList]: ./media/web-sites-restore/11ManagementServicesOperationLogsList.png
[DetailsButton]: ./media/web-sites-restore/12DetailsButton.png
[OperationDetails]: ./media/web-sites-restore/13OperationDetails.png
 

<!---HONumber=62-->