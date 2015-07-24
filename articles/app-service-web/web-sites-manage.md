<properties 
	pageTitle="使用 Azure 入口網站管理 Web 應用程式" 
	description="在 Microsoft Azure 中使用 Azure 入口網站來執行 Web 應用程式管理工作的概觀。" 
	services="app-service\web" 
	documentationCenter="" 
	authors="MikeWasson" 
	writer="mwasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="mwasson"/>

# 使用 Azure 入口網站管理 Web 應用程式

在 [Azure 入口網站](https://portal.azure.com)中，您可以透過一系列的*分頁* (亦即適用於不同元件的容器) 來管理 Web 應用程式(如需 Azure 入口網站的概觀，請參閱[入口網站](http://go.microsoft.com/fwlink/?LinkId=529715)變更指南)。

若要檢視 Web 應用程式的分頁，依序按一下 [首頁] 和應用程式名稱，或者，若要查看您的所有 Azure 資源，可按一下 [瀏覽]。

![](./media/web-sites-manage/manage01.png)

分頁的最上層組件具有適用於一些常見動作的控制項：

- [設定]，可查看所有管理設定的清單。

- [瀏覽]，在瀏覽器視窗中開啟 Web 應用程式。

- 其他按鈕可啟動或停止應用程式、交換部署位置、刪除應用程式，以及進行其他操作。

[基本功能] 會列出有關應用程式的基本資訊，包括 URL、位置、[應用程式服務](http://go.microsoft.com/fwlink/?LinkId=529714)方案，以及目前狀態 (執行中、已停止等)。

位於 [基本功能] 下方的是可用來監視、管理和部署應用程式的各個組件。若要自訂分頁的版面配置，可使用滑鼠右鍵按一下並選取 [自訂] 或 [新增組件]。本主題的其餘部分將說明預設版面配置中的組件。

## 監視

位於 [監視] 下方的是顯示 Web 應用程式度量的圖形。若要設定該圖形，按一下 [編輯]。您可以選取時間範圍，以及要顯示哪些度量。可用的度量包含要求數目、平均回應時間、伺服器錯誤及 CPU 時間。

![](./media/web-sites-manage/manage02.png)

若要新增警示規則，可按一下圖形，然後按一下 [新增警示]。當特定的度量達到某個閾值時，警示規則會通知您。例如，當伺服器錯誤數目在 5 分鐘內超過某個值時，您可以收到警示。

[監視] 也包含設定分析、設定應用程式監視，以及建立可用性測試的組件。如需詳細資訊，請參閱[在 Azure App Service 中監視 Web 應用程式的基本概念](web-sites-monitor.md)。

## 使用量

![](./media/web-sites-manage/manage03.png)

- [檔案系統儲存體] 會顯示您的應用程式正在使用多少個檔案儲存體。
- [配額] 會顯示您的應用程式如何使用資源使用量配額。按一下此組件，以查看詳細資料。
- [調整規模] 可讓您調整執行個體計數及設定自動調整。請參閱[在 Azure App Service 中調整 Web 應用程式規模](../how-to-scale-websites.md)。  
- [預估成本] 會顯示該應用程式的預估成本。
- [定價層] 讓您能夠變更定價層。

## 作業

![](./media/web-sites-manage/manage04.png)

- [事件]。按一下以檢視事件記錄。  
- [警示規則]。按一下以檢視警示規則，並加入新的警示。
- [串流記錄] (A)。按一下以檢視應用程式記錄。若要啟用記錄，請移至 [設定] 並開啟 [診斷記錄] 分頁。 
- [主控台]。按一下以開啟在入口網站內部執行的命令列。您可以使用此命令列來執行類似 `mkdir` 和 `dir` 的 command 命令。  
- [處理序總管] (B)。按一下以檢視在應用程式中執行的處理序，包括工作集和執行緒計數。

## Deployment
 
![](./media/web-sites-manage/manage05.png)

- 設定連續部署。請參閱[在 Azure App Service 中使用 Git 來部署 Web 應用程式](web-sites-publish-source-control.md)
- 部署位置。請參閱[將 Azure App Service 中的 Web 應用程式部署至預備環境](web-sites-staged-publishing.md)
- 設定適用於 Git 或 FTP 的部署認證。 

## 網路

- 將應用程式連接到虛擬網路
- 新增混合式連線

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期入門 Web 應用程式。不需要信用卡；沒有承諾。

## 後續步驟

- [在 Azure App Service 中調整 Web 應用程式規模](../how-to-scale-websites.md)
- [使用 WebJob 執行背景工作](web-sites-create-web-jobs.md)
- [Azure Web Apps 備份](web-sites-backup.md)和[還原](web-sites-restore.md)
- [在 Azure App Service 中監視 Web 應用程式的基本概念](web-sites-monitor.md)

## 變更的項目
* 如需從網站變更為 App Service 的指南，請參閱：[Azure App Service 及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 如需從舊的入口網站變更為新入口網站的指南，請參閱：[巡覽預覽入口網站的參考](http://go.microsoft.com/fwlink/?LinkId=529715)
 

<!---HONumber=62-->