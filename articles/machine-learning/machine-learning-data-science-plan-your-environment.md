<properties 
	pageTitle="規劃雲端資料科學環境 |Azure" 
	description="規劃雲端資料科學環境" 
	metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="msolhab"
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="mohabib;bradsev" /> 


# 規劃 Azure Machine Learning 資料科學環境

當您設定 Azure Machine Learning 資料科學環境時，需要進行某些決策。您進行決策的依據是資料的類型、大小和來源位置，以及此資料在雲端中的目標目的地。雲端資料科學程序是一系列端對端的工作，帶領您從部分來源的原始資料開始，透過建立並發佈模型做為 Azure Web 服務，在應用程式中加以使用。

雲端資料科學程序工作流程顯示於**[資料科學程序圖](machine-learning-data-science-how-to-create-machine-learning-service.md)**中。如需深入了解資料科學程序中的個別步驟，請按一下[地圖](machine-learning-data-science-how-to-create-machine-learning-service.md)中的相關項目。

本文將討論在設定雲端資料科學環境時應考量的問題、列舉此程序中非常實用的資源和工具，以及提供如何使用雲端資料科學程序圖的指引。

## 要考量的問題

在您開始建立雲端資料科學環境之前，請考慮下列問題。

1. **您的資料位於何處？** (這個位置是指***資料來源***)。例如：
	- 資料可在 HTTP 位址上公開使用。
	- 資料位於本機或網路檔案位置中。
	- 資料是在 SQL Server 資料庫中。
	- 資料儲存於 Azure 儲存體容器中。
2. **如何將資料格式化？**例如：
    - 以逗號分隔或定位鍵分隔且未壓縮的檔案。
    - 以逗號分隔或定位鍵分隔且已壓縮的檔案。
	- 已壓縮或未壓縮的 Azure Blob。
	- SQL Server 資料表。
3. **您的資料大小為何？**
    - 小型：小於 2GB
    - 中型：大於 2GB 且小於 10GB
	- 大型：大於 10GB
	- 非常大型：數百個 GB
4. **您對資料的熟悉程度為何？**
    - 您需要瀏覽資料來探索它的結構描述、變數分佈、遺漏值等項目嗎？ 
	- 資料是否需要預先處理，或在轉換至表格表示法之前加以清除？ 
5. **您是否規劃 (或者您能夠) 將所有資料移至 Azure？**
    - 是，正在規劃將整個資料集複製到雲端以進行處理。
	- 否，只會將資料子集複製到 Azure。
6. **您慣用的 Azure 雲端目的地為何？**例如：
	- 將資料移至 Azure 儲存體 Blob。
	- 將資料儲存在可掛接的 Azure 虛擬硬碟中。
	- 將資料載入 Azure 虛擬機器上的 SQL Server 資料庫。
	- 將資料對應至 Azure HDInsight Hive 資料表。

## Azure 中的雲端資料科學資源

根據您的案例而定，您可能也需要下列各項：

1.  Azure 工具：[Azure PowerShell SDK](../install-configure-powershell.md)、[Azure 儲存體總管](http://azurestorageexplorer.codeplex.com/)、[AzCopy](../storage-use-azcopy.md) 及其他工具
2.  執行 SQL Server 的 Azure 虛擬機器
3.  Azure HDInsight (Hadoop)
4.  用以內部部署至 Azure 檔案共用的 Azure 虛擬網路
5.  適用於已排定之資料移動的 Azure Data Factory


## 如何使用雲端資料科學程序圖

[雲端資料科學程序圖](machine-learning-data-science-how-to-create-machine-learning-service.md)呈現各種不同的資料科學練習。此圖會顯示一般資料科學工作流程中涉及的核心步驟。並非所有步驟在每個資料科學練習中都是必要的。此外，此程序本質上是反覆的程序，而指定練習中的步驟順序可能會有所不同。您對於上述問題的回答可協助決定哪些步驟若程序中需要它們時會與您的案例相關，以及在哪些條件下需要反覆進行這些步驟。

如需根據原始資料大小、資料來源位置及 Azure 中目標儲存機制的範例案例，請參閱 [Azure Machine Learning 中的雲端資料科學程序](../hmachine-learning-data-science-plan-sample-scenarios.md)。



<!--HONumber=49--> 