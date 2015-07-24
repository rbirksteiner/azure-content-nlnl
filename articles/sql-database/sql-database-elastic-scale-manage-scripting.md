<properties 
	pageTitle="使用指令碼編寫 Elastic Scale" 
	description="使用 PowerShell 及 Azure Automation Service Runbook 記錄 Elastic Scale 工作的指令碼" 
	services="sql-database" 
	documentationCenter="" 
	manager="stuartozer" 
	authors="Joseidz" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/03/2015" 
	ms.author="Joseidz@microsoft.com"/>

# 使用指令碼管理 Elastic Scale


## Azure 自動化服務 

[Azure 自動化](http://azure.microsoft.com/documentation/services/automation/)為 Azure 平台提供一個功能強大、極有必要的 PowerShell 工作流程執行服務。現在，您可以將常見 Azure 入口網站體驗內難以執行的維護工作自動化。只要撰寫 PowerShell 工作流程 (在 Azure 自動化中稱為 **Runbook**)，將它上傳到雲端，然後排程您想要何時執行 Runbook。本文件針對一些分區彈性範例，提供 Azure 自動化的端對端設定。如需詳細資訊，請參閱[預覽公告](http://blogs.technet.com/b/in_the_cloud/archive/2014/04/15/announcing-the-microsoft-azure-automation-preview.aspx)。或註冊 Azure [訂用帳戶](https://account.windowsazure.com/PreviewFeatures?fid=automation)。

在此範例中，Azure 自動化做為排程和工作負載執行架構。將為 Azure 自動化視為[雲端中的 SQL 代理程式](http://azure.microsoft.com/blog/2014/06/26/azure-automation-your-sql-agent-in-the-cloud/)。

除了這份文件，這裡有其他資源：

* [開始使用 Azure 自動化](../automation-create-runbook-from-samples.md)
* [逐步解說：開始使用新的 Microsoft Azure 自動化預覽功能](http://blogs.technet.com/b/keithmayer/archive/2014/04/04/step-by-step-getting-started-with-windows-azure-automation.aspx) 
* [Microsoft Azure 自動化](http://blogs.technet.com/b/cbernier/archive/2014/04/08/microsoft-azure-automation.aspx) 
* 在[自動化論壇](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)詢問 Azure 自動化的特定問題。  


## 必要條件

[註冊](http://azure.microsoft.com/services/preview/)並[熟悉](../automation-create-runbook-from-samples.md) Microsoft Azure 自動化預覽服務。


## 分區彈性 PowerShell 檔案

下列 PowerShell 檔案集包含使用 Azure 自動化完成水平和垂直縮放案例的基本命令。

這些範例說明如何使用 PowerShell 範例模組來執行基本分區彈性工作。結合 Microsoft Azure 自動化服務和對應的 Azure 自動化 Runbook，您可以建立自動化和排程的工作，以佈建新的分區和 (或) 根據一組規則變更特定分區的效能層級。

**SetupShardedEnvironment.ps1**：此 PowerShell Runbook 會執行分區化環境的一次性設定，在環境中提供分區對應管理員和範圍分區對應。

**ProvisionByDate.ps1**：在某日的工作負載之前預先佈建新的資料庫。將會建立資料庫且根據日期戳記 (YYYYMMDD) 命名，並在分區對應管理員中註冊為範圍 [YYYYMMDD, YYYYMMDD + 1D)。

**ProvisionBySize.ps1**：當目前資料庫的容量不足時，佈建新的資料庫。

**ReduceServiceTier.ps1**：逐一查看提供的分區對應中的分區，並決定每一個個別分區是否應該降低效能層級。有兩個準則決定分區是否為適合的對象：1) 分區的目前服務層，2) 資料庫存在的時間。

**ShardManagement.psm1**：提供一組與分區對應管理員互動的方法。

**SqlDatabaseHelpers.psm1**：提供一組與 Azure SQL 資料庫互動的方法。

**ShardElasticity.psm1**：提供一組方法來執行水平縮放和垂直縮放。

**ShardElasticityModule.psd1**：提供一組與 Elastic Scale 和 Azure SQL DB 互動的方法。

## 成本

請注意，執行 PowerShell 範例指令碼將會導致建立資料庫，因而對訂閱擁有者引發實際成本。基礎的 Azure SQL DB 和其他 Azure SQL DB 資料庫採取相同的[費率](http://azure.microsoft.com/pricing/details/sql-database/)計費。從 11 月 1 日開始的成本是：

* SetupShardedEnvironment Runbook 會在 Basic 資料庫上建立分區對應管理員 ($0.0069/小時)，也會在 Basic 資料庫上佈建第一個分區 ($0.0069/小時)。 

* ProvisionBySize 和 ProvisionByDate Runbook 都會佈建 Standard S0 資料庫 ($0.0208/小時)。為了抵銷這些成本，如果搭配 ReduceServiceTier Runbook 執行，則新佈建資料庫的服務層在經過一天之後，將會從 Standard S0 ($0.0208/小時) 降低到 Basic ($0.0069/小時)。

最後，在所提供範例的範圍內，使用 [Azure 自動化](http://azure.microsoft.com/pricing/details/automation/)目前不會對訂用帳戶擁有者引發任何費用。如需詳細資訊，請參閱 [Azure 自動化定價頁面](http://azure.microsoft.com/pricing/details/automation/)。

## 載入 Runbook 

1. 下載 **ShardElasticity.zip** 檔案，並將內容解壓縮。
2. [使用 NuGet 加入 Elastic Scale 二進位檔的參考](sql-database-elastic-scale-add-references-visual-studio.md)
3. 尋找 Elastic Scale 用戶端二進位檔 (**Microsoft.Azure.SqlDatabase.ElasticScale.Client.dll**)。
4. 將 DLL 放入 ShardElasticityModule 資料夾，並壓縮資料夾。 
3. 在 Azure 自動化帳戶中，上傳 ShardElasticityModule.zip 檔案作為**資產**。 
4. 在 Azure 自動化中，建立一個稱為 *ElasticScaleCredential* 的**資產認證**，其中包含 Azure SQL Database 伺服器的使用者名稱和密碼。 
5. 建立一個稱為 *SqlServerName* 的**資產變數**，作為完整的 Azure SQL Database 伺服器名稱。 
5. 上傳 **SetupShardedEnvironment.ps1**、**ProvisionBySize.ps1**、**ProvisionByDate.ps1** 和 **ProvisionByDate.ps1** 作為 Runbook。 
6. 以一次性作業測試 **SetupShardedEnvironment.ps1** Runbook，以設定分區化環境。 
7. 發佈剩餘的一或多個 Runbook，並將 Runbook 連結至排程。 
8. 透過 [**工作**] 索引標籤觀察 Runbook 的輸出。 

如果「快速範例指示」未成功，請參閱下面的「詳細範例指示」。

## 使用 Runbook

1. 編寫與封裝 PowerShell 模組 
2. 建立 Microsoft Azure 自動化帳戶 
3. 將 PowerShell 模組上傳至 Azure 自動化作為資產 
4. 建立 Azure 自動化認證和變數資產 
5. 將 PowerShell Runbook 上傳至 Azure 自動化 
6. 設定分區化環境 
7. 測試自動化 Runbook 
8. 發佈 Runbook 
9. 排程 Runbook 


## 編寫與封裝 PowerShell 模組 

第一個步驟是建立 PowerShell 模組，以參考 Elastic Scale 組件並封裝這個模組，以便能夠將它上傳至 Azure 自動化服務作為資產。

1. 下載 “ShardElasticity.zip” 檔案。
2. 解壓縮所有的內容。

    ![全部擷取][1]
3. 取得 Elastic Scale 用戶端 DLL (亦即 Microsoft.Azure.SqlDatabase.ElasticScale.Client.dll)，並將下列檔案複製到您在步驟 1 中下載的本機 “ShardElasticityModule” 資料夾。這有兩種方式可以完成：1) 透過 Elastic Scale NuGet 封裝[連結] 下載 DLL，或 2) 從 Elastic Scale 入門套件專案 [連結] (必須先建置)，移至 \\bin\\Debug\\ 取得 DLL。

    ![取得 Dll][2]

4. 壓縮 ShardElasticityModule 資料夾。

    注意：Azure 自動化需要數個名稱慣例：以模組名稱 ShardElasticityModule.psm1 為例，zip 檔案名稱必須完全符合 (ShardElasticityModule.zip)。Zip 檔案包含 ShardElasticityModule 資料夾 (名稱符合模組的名稱)，而此資料夾又包含 psm1 檔案。如果未遵循此結構，Azure 自動化將無法解除封裝模組。

5.    在驗證壓縮資料夾的內容和結構符合需求之後，請繼續下一個步驟。它看起來應該如下：

    ![dll][3]


## 註冊 Azure 自動化預覽

1. 移至 [Azure 預覽功能](http://azure.microsoft.com/services/preview/)。
    
2. 按一下 [**試試看**]。

    ![按一下 [試試看]][8]

2. 移至 [ Microsoft Azure 入口網站](https://manage.windowsazure.com/microsoft.onmicrosoft.com)。
3. 按一下 [**自動化**]。

    ![自動化][4]
4. 在畫面底部按一下 [**建立**]。 
5. 在如下所示的提示中，請輸入有效的帳戶名稱，然後按一下方塊右下角的勾號。

    ![提示][5] 
5. 請繼續進行下一個步驟。成功時如下圖所示。

    ![成功][6]

## 將 PowerShell 模組上傳至 Azure 自動化作為資產 

將上述 PowerShell 模組上傳至您的 Azure 自動化帳戶。例如，模組會包含一組可從 Runbook 參考的分區彈性函數和 Elastic Scale DLL。

1. 按一下畫面頂端功能區中的 [**資產**]。
2. 按一下頁面底部的 [**匯入模組**]。 
3. 按一下 [**瀏覽檔案…**]，找出上方的 **ShardElasticityModule.zip** 檔案。 
4. 一旦選擇正確的檔案，按一下方塊右下角的勾號將它匯入。Azure 自動化服務會匯入模組。 
5. 請繼續進行下一個步驟。成功時如下圖所示。如果未成功匯入模組，請確定 zip 檔案符合上述的慣例。

    ![Assets][10]
      
## 建立 Azure 自動化認證和變數資產 

Azure 自動化可以分別建立認證和變數資產，供許多 Runbook 參考，您不需要將認證和常用的變數固定寫在 Runbook 中。例如，變更密碼，但只在一個地方發生。

1. 選取您剛才建立的新 Azure 自動化帳戶。
2. 在 **ShardElasticityExamples** 帳戶下，按一下功能區中的 [**資產**]。
3. 按一下畫面底部的 [**加入設定**]。  
4. 按一下 [**加入認證**]。 

    ![加入認證][9]
4. 選取 [**Windows PowerShell 認證**] 作為 [**認證類型**]，輸入 **ElasticScaleCredential** 作為 [名稱]。描述是選擇性的。  
5. 按一下方塊右下角的箭號。 

    注意：若要直接使用 Runbook 而不修改，請使用指示所提供的變數名稱，一字不差。Runbook 會參考變數名稱。 
5. 針對您要執行分區彈性範例的 Azure SQL DB 伺服器，插入使用者名稱和密碼 (兩次)。 
 
6. 若要建立變數資產，請按一下 [**加入設定**]，然後選取 [**加入變數**]。

    ![加入變數][7]
7. 針對此教學課程，請建立完整 Azure SQL DB 伺服器名稱 (將放置分區對應管理員和分區化資料庫) 的變數。選取 [**字串**] 作為 [**變數類型**]，並輸入 **SqlServerName**。按一下箭頭以繼續。 
8. 輸入完整的 Azure SQL DB 伺服器名稱做為 [值]，然後按一下勾號。 
9. 您現在已建立分區彈性 Runbook 中將使用的認證和變數資產。請繼續進行下一個步驟。成功時如下所示： 
    

## 將 PowerShell Runbook 上傳至 Azure 自動化 

上傳所提供的四個範例 PowerShell Runbook。

1. 若要上傳新的 Azure 自動化 Runbook，請按一下功能區中的 [**RUNBOOK**]。 
2. 在畫面底部按一下 [**匯入**]。 
3. 瀏覽至檔案所在的資料夾，選取 [**SetupShardedEnvironment.ps1**]，然後按一下核取記號。 
4. 對剩餘三個 PowerShell Runbook (**ProvisionByDate.ps1**、**ProvisionBySize.ps1** 和 **ReduceServiceTier.ps1**)，重複步驟 2 和 3。 
5. 請繼續進行下一個步驟。 

## 設定分區化環境 

下一步是執行 **SetupShardedEnvironment** Runbook，它會佈建分區化環境，在環境中提供分區對應管理員 DB、範圍分區對應和當天的分區。

1. 按一下名稱以選取 **SetupShardedEnvironment** Runbook。 
2. 按一下功能區上的 [**編寫**]。 
3. 在此畫面中，您會看到組成 Runbook 的程式碼 (想要的話，也能夠編輯)。若要設定分區化環境，請按一下畫面底部的 [**測試**] 按鈕。確認您要儲存並測試 Runbook。 
4. 您可以在輸出窗格中查看工作的狀態。當完成時，您所指定的 Azure SQL DB 伺服器中將會填入分區對應管理員資料庫和分區資料庫。**SetupShardedEnvironment** Runbook 只用來佈建分區化的環境，而不是依據重複排程來執行。請繼續進行下一個步驟。  

## 測試自動化 Runbook 

在發佈和排程 Runbook 之前測試每個 Runbook 成功執行。

1. 按一下頁面頂端功能區的 [**RUNBOOK**]。 
2. 按一下 [**ProvisionByDate**] Runbook。
3. 按一下頁面頂端功能區的 [**編寫**]。 
4. 按一下 [**儲存]，**再按一下 [**測試**]。5. 對 **ReduceServiceTier** 重複上述步驟。 

    請注意，因為 **ProvisionBySize** 和 **ProvisionByDate** 都會佈建新的分區 (使用不同的演算法)，所以目前不需要執行 **ProvisionByDate**。

## 發佈 Runbook 
下一步是發佈 Runbook，使它可以排程為定期執行。

1. 按一下頁面底部的 [**發佈**]。 
2. 按一下 [**已發佈**]。 
3. 請繼續進行下一個步驟。
 
## 排程 Runbook 

最後一個步驟是建立排程並連結至以上發佈的 Runbook。

1. 按一下頁面頂端的 [**排程**]。 
2. 按一下 [**連結至新排程**]。
3. 適當地為排程命名，然後按一下向右箭號按鈕。
4. 設定排程。
5. 當完成時，按一下方塊底部的勾號。
6. 一旦已根據先前建立的排程執行工作，請按一下頁面頂端功能區的 [**工作**] 選項，然後選取排定的工作。
## 結論 

您現在已成功撰寫和封裝 PowerShell 模組、將 PowerShell 模組上傳至 Azure 自動化作為資產，且已建立、測試、發佈並排程 Runbook。若要監視 Runbook 的健全狀況和狀態，請使用 [儀表板] 和 [工作] 索引標籤。

提供的範例只粗淺說明 Elastic Scale、Azure SQL DB 和 Azure 自動化結合之後可發揮的用途。請實驗並以這些範例為基礎，讓您的 Azure SQL DB Elastic Scale 應用程式能夠水平、垂直調整，或兩者同時進行。

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-manage-scripting/zip.png
[2]: ./media/sql-database-elastic-scale-manage-scripting/dll.png
[3]: ./media/sql-database-elastic-scale-manage-scripting/lookslikethis.png
[4]: ./media/sql-database-elastic-scale-manage-scripting/automation.png
[5]: ./media/sql-database-elastic-scale-manage-scripting/prompt.png
[6]: ./media/sql-database-elastic-scale-manage-scripting/success.png
[7]: ./media/sql-database-elastic-scale-manage-scripting/add-variable.png
[8]: ./media/sql-database-elastic-scale-manage-scripting/sign-up.png
[9]: ./media/sql-database-elastic-scale-manage-scripting/add-credential.png
[10]: ./media/sql-database-elastic-scale-manage-scripting/assets.png
 

<!---HONumber=July15_HO2-->