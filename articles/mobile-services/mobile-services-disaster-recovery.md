<properties
	pageTitle="發生災害時回復行動服務 - Azure 行動服務"
	description="了解如何在發生災害時回復行動服務。"
	services="mobile-services"
	documentationCenter=""
	authors="christopheranderson"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="04/24/2015"
	ms.author="christopheranderson"/>

# 發生災害時回復行動服務

使用 Azure 行動服務來部署應用程式時，您可以利用內建的功能，在發生可用性問題的情況下確保業務續航力，例如伺服器故障、網路中斷、資料遺失和大規模的設備損失，如果您是部署傳統的內部部署解決方案，則必須設計、實作和管理許多容錯和基礎結構功能，但使用 Azure 行動服務來部署應用程式，就能直接享受這些功能。Azure 可緩和絕大部分可能的失敗後果，且成本低廉。

## <a name="prepare"></a>對可能的災害預做準備

為了在發生可用性問題的情況下輕鬆回復，您可以事先預做準備：

+ **備份 Azure 行動服務 SQL Database 中的資料** 行動服務應用程式的資料會儲存在 Azure SQL Database 中。建議您依照＜[Windows Azure SQL Database 的業務續航力]＞中的指示來備份資料。
+ **備份行動服務指令碼** 建議您將行動服務指令碼儲存在原始檔控制系統中，例如 [Team Foundation Service] 或 [GitHub]，而不要只依賴行動服務本身的複本。您可以透過 Azure 入口網站，使用行動服務[原始檔控制功能]，或[使用 Azure CLI] 來下載指令碼。請注意入口網站中標示為 [預覽] 的功能，因為不保證可回復這些指令碼，可能需要從您自己的原始檔控制來源中回復指令碼。
+ **保留次要行動服務** 如果行動服務發生可用性問題，您可能必須將其重新部署到替代的 Azure 區域。為了確保容量可用 (例如在遺失整個區域的罕見情況下)，建議您在替代區域中建立次要行動服務，並將模式設定為與主要服務的模式相同或更高(如果主要服務處於基本模式，您可以將次要服務設為基本或標準。但是如果主要服務為標準，則次要服務也必須是標準)。

## <a name="watch"></a>監看問題的徵兆

這些情況指出可能需要進行回復作業的問題：

+ 連線至行動服務的應用程式已經很久沒有與服務進行通訊。
+ 行動服務狀態在 **Azure 入口網站**中會顯示為 [狀況不良][]。
+ 在 Azure 入口網站中，行動服務的每個索引標籤頂端會顯示 [狀況不良] 旗幟，且管理作業會產生錯誤訊息。
+ [Azure 服務儀表板]指出發生可用性問題。

## <a name="recover"></a>從災害中回復

發生問題時，請利用 [服務儀表板] 來取得指引和最新消息。

如果服務儀表板出現提示，請執行下列步驟，在替代 Azure 區域中將行動服務還原為執行中狀態。如果您已事先建立次要服務，則其容量將用來還原主要服務。因為在回復之後，主要服務的 URL 和應用程式金鑰不變，您並不需要更新任何參考此服務的應用程式。

在中斷之後回復行動服務：

1. 在 Azure 入口網站中，請確定所報告的服務狀態為 [狀況不良]。

2. 如果您已保留次要行動服務，則可以略過此步驟。

   如果您尚未保留次要行動服務，請立即在其他 Azure 區域中建立。將其調整模式設定為與主要服務相同的模式

3. 依照＜[使用 Azure CLI 來自動化行動服務]＞中的說明，設定 Azure 命令列介面 (Azure CLI) 來處理您的訂閱。

4. 現在您可以使用次要服務來回復主要服務。

	> [AZURE.IMPORTANT]除了移轉檔案，移轉命令也會更新主要服務的主機名稱以指向次要服務，用戶端應用程式便不需要進行更新。不過，主機名稱需要 30 分鐘的時間才能解析成新的服務。基於這個理由，建議只有在嚴重損壞修復案例中使用移轉命令。

	> [AZURE.IMPORTANT]執行此步驟中的命令時，將會刪除次要服務，所以其容量可用來回復主要服務。執行命令之前，建議您先備份指令碼和設定 (若想要保留的話)。

	準備好時，請執行此命令：

		azure mobile migrate PrimaryService SecondaryService
		info:    Executing command mobile migrate
		Warning: this action will use the capacity from the mobile service 'SecondaryService' and delete it and the host name for 'PrimaryService' may not resolve for up to 30 minutes. Do you want to migrate the mobile service 'PrimaryService'? [y/n]: y
		+ Performing migration
		+ Migration with id '0123456789abcdef0123456789abcdef' started. The migration may take several minutes to complete.
		+ Cleaning up
		info:    Migration complete. It may take 30 minutes for DNS to resolve to the migrated site.
		info:    mobile migrate command OK

    > [AZURE.NOTE]指令完成後可能需要經過一些時間，才能在入口網站中看到變更。

5. 與原始檔控制中的原始版本比較，以確認所有指令碼都已正確回復。在大部分情況下，指令碼會自動回復而不會遺失資料，但如果您發現有不一致之處，則可以手動回復該指令碼。

6. 請確定回復後的服務開始與 Azure SQL Database 進行通訊。回復命令會回復行動服務，但會保留原始資料庫的連線。如果主要 Azure 區域中的問題也影響到資料庫，則已回復的服務可能仍然無法正確執行。您可以利用 Azure 服務儀表板來檢查特定區域的資料庫狀態。如果原始資料庫未執行時，您可以回復它：
	+ 依照＜[Windows Azure SQL Database 的業務續航力]＞中的說明，將 Azure SQL Database 回復到您剛回復行動服務的 Azure 區域。
	+ 在 Azure 入口網站中，在行動服務的 [設定] 索引標籤上選擇 [變更資料庫]，然後選取剛回復的資料庫。

7. 您的行動服務現已裝載於不同的實體位置。您將需要更新您的發佈和/或 git 認證，以允許更新您執行中的網站。
	+ 如果您使用 **.NET 後端**，請如[發行行動服務](mobile-services-dotnet-backend-windows-store-dotnet-get-started/#publish-your-mobile-service)所述，重新設定您的發行設定檔。這會更新您的發行詳細資訊，以指向新的服務位置。
	+ 如果您使用 **Javascript 後端**並透過入口網站來管理您的服務，則不需要採取任何額外的動作。
	+ 如果您使用 **Javascript 後端**並透過節點來管理您的服務，請更新 git 遠端以指向新的儲存機制。若要這麼做，請從您的 git 遠端移除 .git 檔案路徑：

		1. 尋找您目前的原點遠端：git remote -v origin https://myservice.scm.azure-mobile.net/myservice.git (fetch) origin https://myservice.scm.azure-mobile.net/myservice.git (push)
		3. 使用相同的 url 更新遠端，但不包含最終 .git 檔案路徑：git remote set-url origin https://myservice.scm.azure-mobile.net
		4. 從原始端點提取以確認其運作正常。

現在的狀態應該是行動服務已回復到新的 Azure 區域，且現在正使用其原始 URL 接收來自您的市集應用程式的流量。

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Windows Azure SQL Database 的業務續航力]: http://msdn.microsoft.com/library/windowsazure/hh852669.aspx
[Team Foundation Service]: http://tfs.visualstudio.com/

[原始檔控制功能]: http://www.windowsazure.com/develop/mobile/tutorials/store-scripts-in-source-control/
[使用 Azure CLI]: http://www.windowsazure.com/develop/mobile/tutorials/command-line-administration/
[]: http://manage.windowsazure.com/
[Azure 服務儀表板]: http://www.windowsazure.com/support/service-dashboard/
[使用 Azure CLI 來自動化行動服務]: http://www.windowsazure.com/develop/mobile/tutorials/command-line-administration/
 

<!---HONumber=July15_HO2-->