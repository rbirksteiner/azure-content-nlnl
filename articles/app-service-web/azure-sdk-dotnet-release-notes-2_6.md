<properties 
   pageTitle="Azure SDK for .NET 2.6 版本資訊" 
   description="Azure SDK for .NET 2.6 版本資訊" 
   services="app-service/web" 
   documentationCenter=".net" 
   authors="Juliako" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="04/29/2015"
   ms.author="juliako"/>


# Azure SDK for .NET 2.6 版本資訊

本文件包含 Azure SDK for .NET 2.6 版的版本資訊。

您可以使用 Azure SDK 2.6 開發以 .NET 4.5.2 或.NET 4.6 為目標的雲端服務應用程式 (PaaS)，前提是您必須在雲端服務角色上手動安裝目標 .NET Framework。請參閱[在雲端服務角色上安裝 .NET](http://go.microsoft.com/fwlink/?LinkID=309796)。


##服務匯流排更新

- 事件中心： 

	- 現在藉由公開事件中心的其他發行者端點，可在傳送事件時提供目標存取控制。
	- 其他加入至事件中心功能的穩定性和改良。
	- 在訊息和事件中心內，加入 WebSocket 上的 Amqp 通訊協定支援。

##HDInsight Tools for Visual Studio 更新

- **IntelliSense 的增強功能**：遠端中繼資料建議

	HDInsight Tools for Visual Studio 現在支援在編輯 Hive 指令碼時取得遠端中繼資料。例如，您可以輸入 **SELECT * FROM**，即可顯示所有的資料表名稱。此外，資料行名稱會在指定資料表之後顯示。

- **HDInsight 模擬器支援**

	HDInsight Tools for Visual Studio 現在支援連接到 HDInsight 模擬器，方便您在不產生任何費用的情況下本機開發 Hive 指令碼，然後對您的 HDInsight 叢集執行這些指令碼。

	如需詳細資訊，請參閱[本手冊](http://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409)。

- **HDInsight Tools for Visual Studio 支援一般的 Hadoop 叢集** (預覽)

	HDInsight Tools for Visual Studio 現在支援一般的 Hadoop 叢集，因此您可以使用 HDInsight Tools for Visual Studio 來執行下列動作：

	- 連接到您的叢集、 
	- 使用增強式 IntelliSense/自動完成支援來撰寫 Hive 查詢、 
	- 使用直覺式 UI 來檢視叢集中的所有工作。 

	如需詳細資訊，請參閱[本手冊](http://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409)。

##角色中快取更新

- **角色中快取**已更新為使用 **Microsoft Azure 儲存體 SDK** 4.3 版本。過去，**角色中快取**一直是使用 Azure 儲存體 SDK 1.7 版本。

	使用 Azure SDK 2.5 或更低版本的客戶應該更新到 Azure SDK 2.6，並移至新的 Azure 儲存體 SDK 版本。請注意，我們將於 2015 年 12 月 9 日移除 Azure 儲存體 2011-08-18 版本。如需詳細資訊，請閱讀[本公告](http://azure.microsoft.com/blog/2014/08/04/microsoft-azure-storage-service-version-removal/)。

	如需詳細資訊，請參閱 [Azure 快取的角色中快取](https://msdn.microsoft.com/library/azure/dn386103.aspx)。

##Azure 應用程式服務工具

Azure SDK 2.6 版中已更新下列項目。

- 已增強 Azure 發行的功能，以將 Azure API Apps 納為部署目標。
- API Apps 佈建功能提供使用者 API 應用程式的建立和佈建功能。
- 將 [伺服器總管] 使用按資源群組分組的 Web、行動及 API 應用程式加以變更，以反映新的應用程式服務節點。
- 加入已新增至大部分 C# 專案的 Azure API 應用程式用戶端手勢，將可自動產生啟用 Swagger 功能並可在使用者的 Azure 訂用帳戶中執行的 API Apps。
- [伺服器總管] 中的 API Apps 工具和應用程式服務節點僅可以在 Visual Studio 2013 中使用。 

##Azure 資源管理員工具更新

Azure 資源管理員工具已經更新，以納入虛擬機器、網路和儲存體的範本。JSON 編輯體驗已經更新，以納入新的範本大綱檢視以及使用 JSON 程式碼片段編輯範本的能力。從 Visual Studio 部署的範本會使用專案所提供的 PowerShell 指令碼，以便 Visual Studio 使用針對指令碼所做的任何變更。

##雲端服務的診斷改良功能

Azure SDK 2.6 重新提供針對收集 Azure 計算模擬器中的診斷記錄檔，並將它們傳送到開發儲存體的支援。在模擬器中執行應用程式時所產生的任何診斷記錄檔 (包括應用程式追蹤記錄檔、Windows 事件追蹤 (ETW) 記錄檔、效能計數器、基礎結構記錄檔和 Windows 事件記錄檔) 會被傳輸到開發儲存體，以確認診斷記錄功能可在本機電腦上運作。

您現在可以在服務組態 (.cscfg) 檔中指定診斷儲存體帳戶，讓您輕而易舉便可在不同的環境中使用不同的診斷儲存體帳戶。連接字串在 Azure SDK 2.4 和 Azure SDK 2.6 中的運作方式有一些顯著的差異。如需有關如何使用診斷儲存體連接字串，以及它會如何影響您的專案等詳細資訊，請參閱[設定 Azure 雲端服務的診斷](http://go.microsoft.com/fwlink/?LinkID=532784)。

##重大變更

###Azure 資源管理員工具 

- Azure SDK 2.5 所提供的 [**雲端部署專案**] 專案類型已重新命名為 [**Azure 資源群組**]。
- 在 Azure SDK 2.5 中建立的 [**雲端部署專案**] 專案類型仍然可以在 2.6 中使用，但從 Visual Studio 部署範本將會失敗。不過，使用 PowerShell 指令碼進行部署仍可像先前一樣運作。如需如何在 2.6 中使用**雲端部署專案**的相關資訊，請閱讀[本文](http://go.microsoft.com/fwlink/?LinkID=534086)。
 
##已知問題

- 收集模擬器中的診斷記錄檔需要 64 位元的作業系統。在 32 位元的作業系統上執行時，將無法收集診斷記錄檔。這並不會影響任何其他模擬器功能。 

- 2015 年 4 月 29 日發行的 Azure SDK 2.6 有兩個問題：

	- 電腦上安裝有 Azure SDK 2.6 時無法在 Visual Studio 2015 載入通用應用程式。
	- 在 Visual Studio 2013 和 Visual Studio 2015 偵錯雲端服務專案將會失敗，此時 Visual Studio 會變得沒有回應並當機，同時還會顯示內有「正在設定模擬器的診斷」訊息的對話方塊。
	
	2015 年 5 月 18 日發行了 Azure SDK 2.6 的更新。更新的版本是 2.6.30508.1601；內含上述兩個問題的修正程式。您可以從 [控制台] -> [程式和功能]-> [Microsoft Azure Tools for Microsoft Visual Studio 2013 – v 2.6] 識別 SDK 的組建。[版本] 欄中會顯示組建編號。

	如果您仍會遇到上述問題，請針對 [VS 2012](http://go.microsoft.com/fwlink/p/?linkid=323511&clcid=0x409)、[VS 2013](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) 或 [VS 2015](http://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409) 安裝最新版的 Azure 2.6 SDK。
 

<!---HONumber=62-->