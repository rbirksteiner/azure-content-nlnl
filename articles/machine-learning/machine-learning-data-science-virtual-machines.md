<properties 
	pageTitle="在 Azure 中設定適用於資料科學的虛擬機器或 SQL Server 虛擬機器" 
	description="設定資料科學虛擬機器" 
	metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="msolhab" 
	manager="jacob.spoelstra" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/17/2015" 
	ms.author="mohabib;xibingao;bradsev" />

# 在 Azure 中設定適用於資料科學的虛擬機器或 SQL Server 虛擬機器

有數種類型的 Azure 虛擬機器可佈建並設定來做為雲端架構資料科學環境的一部分。決定要使用哪一個虛擬機器類別，取決於要使用機器學習服務進行模型化的資料類型和數量，以及該資料在雲端中的目標目的地。如需進行此決策時要考量的問題指導方針，請參閱[規劃您的 Azure Machine Learning 資料科學環境](machine-learning-data-science-plan-your-environment.md)。此處涵蓋的兩個案例都包含 CAS

所提供的指示會說明如何設定 Azure VM 和含有 SQL 服務的 Azure VM，以做為 IPython Notebook 伺服器。虛擬機器會在 Windows 上執行並使用支援工具加以設定，例如，Azure 儲存體總管和 AzCopy，以及其他對於資料科學專案非常實用的封裝。例如，Azure 儲存體總管和 AzCopy 會提供便利的方法，將資料從本機電腦上傳至 Azure 儲存體，或者從儲存體將資料下載到本機電腦。提供兩組指示：

* [設定適用於資料科學的 Azure 虛擬機器](machine-learning-data-science-setup-virtual-machine.md)示範如何針對可使用某種 SQL 以外的 Azure 儲存體來儲存資料的情況，使用 IPython Notebook 和其他用來進行資料科學的工具來佈建 Azure 虛擬機器。 

* [設定適用於資料科學的 Azure SQL Server 虛擬機器](machine-learning-data-science-setup-sql-server-virtual-machine.md)示範如何針對要在 SQL 資料庫中儲存資料的情況，使用 IPython Notebook 和其他用來進行資料科學的工具來佈建 Azure SQL Server 虛擬機器。

在進行佈建並設定之後，這些虛擬機器就已經準備好用來做為 IPython Notebook 伺服器，以用於進行資料探索和處理，以及其他可與 Azure Machine Learning 和雲端資料科學程序一起使用的工作。此程序可包含將資料移至 HDInsight，在那裡處理資料並進行取樣，以準備使用 Azure Machine Learning 從資料中進行學習的步驟。

* 如需如何從 Azure Blob 儲存體將資料移至 HDInsight 的相關資訊，請參閱[建立並將資料從 Azure Blob 儲存體載入 Hive 資料表](machine-learning-data-science-hive-tables.md)。
* 如需在 HDInsight 中使用 Hive 查詢處理資料的相關資訊，請參閱[在雲端資料科學程序中將 Hive 查詢提交至 HDInsight Hadoop 叢集](machine-learning-data-science-hive-queries.md)。
* 如需在 HDInsight 中取樣資料的相關資訊，請參閱[在 Azure HDInsight Hive 資料表中進行資料取樣](machine-learning-data-science-sample-data-hive.md)。


> [AZURE.NOTE] Azure 虛擬機器的定價策略是「**只針對您使用的項目進行付費**」。若要確定您在不使用虛擬機器時不會被計費，則當它閒置時，其在 [Azure 管理入口網站](http://manage.windowsazure.com/)中的狀態必須是 [**已停止 (已取消配置)**] 狀態。如需逐步指示或如何解除配置虛擬機器，請參閱[關閉並取消配置未使用的虛擬機器](machine-learning-data-science-setup-virtual-machine.md#shutdown) 




<!--HONumber=49--> 