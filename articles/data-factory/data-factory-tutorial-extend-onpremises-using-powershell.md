<properties 
	pageTitle="請將教學課程中的輸出資料複製到內部部署 SQL Server 資料庫。" 
	description="本教學課程中的逐步解說延伸 Data Factory 教學課程，將行銷活動有效性資料複製到內部部署 SQL Server 資料庫。"
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/14/2015" 
	ms.author="spelluru"/>


# 逐步解說：將行銷活動有效性資料複製到內部部署 SQL Server 資料庫
在此逐步解說中，您將學習如何設定環境以啟用管線，來處理您的內部部署資料。
 
在記錄檔處理案例中的最後一個步驟，從第一個逐步解說與分割 -> 充實 -> 分析工作流程，行銷活動有效性輸出已複製到 Azure SQL 資料庫。您也可以在組織內，將此資料移動到內部部署 SQL Server 以進行分析。
 
為了從 Azure Blob 將行銷活動有效性資料複製到內部部署 SQL Server，您需要使用與在第一個逐步解說中所使用的一組相同 Cmdlet 建立額外的內部部署連結的服務、資料表和管線。

## 必要條件

在執行本文的逐步解說之前，您**必須**先執行[教學課程：使用 Data Factory 移動和處理記錄檔][datafactorytutorial]中的逐步解說。

**(建議)** 檢閱和練習[讓您的管線處理內部部署資料][useonpremisesdatasources]文章中的逐步解說，以逐步演練建立管線，將資料從內部部署 SQL Server 移至 Azure Blob 存放區。


在本逐步解說中，您將執行下列步驟：

1. [步驟 1：建立資料管理閘道](#OnPremStep1)。資料管理閘道是可讓您從雲端存取組織中內部部署資料來源的用戶端代理程式。閘道可以讓您在內部部署 SQL Server 和 Azure 的資料存放區之間傳送資料。	

	您必須在公司環境中至少安裝一個閘道，以及將它向其註冊 Azure Data Factory 註冊，之後才將內部部署 SQL Server 資料庫新增為 Azure Data Factory 的連結的服務。

2. [步驟 2：為內部部署 SQL Server 建立連結的服務](#OnPremStep2)。在此步驟中，您會先在內部部署 SQL Server 電腦上建立資料庫和資料表，然後建立連結的服務：**OnPremSqlLinkedService**。
3. [步驟 3：建立資料表和管線](#OnPremStep3)。在此步驟中，您將建立資料表 **MarketingCampaignEffectivenessOnPremSQLTable** 和管線 **EgressDataToOnPremPipeline**。 

4. [步驟 4：監視管線和檢視結果](#OnPremStep4)。在此步驟中，您將使用 Azure 入口網站來監視管線、資料表和資料配量。


## <a name="OnPremStep1"></a>步驟 1：建立資料管理閘道

資料管理閘道是可讓您從雲端存取組織中內部部署資料來源的用戶端代理程式。閘道可以讓您在內部部署 SQL Server 和 Azure 的資料存放區之間傳送資料。
  
您必須在公司環境中至少安裝一個閘道，以及將它向其註冊 Azure Data Factory 註冊，之後才將內部部署 SQL Server 資料庫新增為 Azure Data Factory 的連結的服務。

如果您有可以使用的現有資料閘道，請略過此步驟。

1.	建立邏輯資料閘道。在 **Azure Preview 入口網站**中，按一下 **DATA FACTROY** 刀鋒視窗上的 [**連結服務**]。
2.	按一下命令列上的 [**新增 (+) 資料閘道**]。  
3.	在 [**新增資料閘道**] 分頁中，按一下 [**建立**]。
4.	在 [**建立**] 分頁中，輸入 **MyGateway** 做為資料閘道的 [**名稱**]。
5.	按一下 [**選擇區域**]，並視需要予以變更。 
6.	在 [**建立**] 分頁中按一下 [**確定**]。 
7.	您應該會看到 [**設定**] 分頁。 
8.	在 [**設定**] 分頁中，按一下 [**直接安裝在此電腦上**]。這會在您的電腦上下載、安裝及設定閘道，並向服務註冊閘道。如果在您想要連結至這個邏輯閘道入口網站的電腦上已安裝現有的閘道，使用此分頁中的金鑰，使用資料管理閘道組態管理員 (預覽) 工具重新註冊您的閘道。

	![資料管理閘道組態管理員][image-data-factory-datamanagementgateway-configuration-manager]

9. 按一下 [**確定**] 以關閉 [**設定**] 分頁，再按一下 [**確定**] 以關閉 [**建立**] 分頁。在 [**連結的服務**] 分頁中，等候 **MyGateway** 的狀態變更為**良好**。您也可以啟動**資料管理閘道組態管理員 (預覽)** 工具來確認閘道的名稱符合入口網站中的名稱，以及 [**狀態**] 為 [**已註冊**]。您可能必須關閉再重新開啟 [連結的服務] 分頁，以查看最新狀態。可能需要幾分鐘的時間，畫面才會重新整理為最新狀態。

## <a name="OnPremStep2"></a>步驟 2：為內部部署 SQL Server 建立連結的服務

在此步驟中，您可以先在內部部署 SQL Server 電腦上建立所需的資料庫和資料表，然後再建立連結的服務。

### 準備內部部署資料庫和資料表

若要開始，您需要建立 SQL Server 資料庫、資料表、使用者定義型別和預存程序。這些將用於從 Azure Blob 將 **MarketingCampaignEffectiveness** 結果移至 SQL Server 資料庫。

1.	在 **Windows 檔案總管**中，導覽至 **C:\ADFWalkthrough** (或您解壓縮範例的位置) 中的 **OnPremises** 子資料夾。
2.	在您喜好的編輯器中開啟 **prepareOnPremDatabase&Table.ps1**，將反白顯示部分取代為您的 SQL Server 資訊，並儲存檔案 (請提供 **SQL 驗證**詳細資訊)。針對教學課程的目的，為您的資料庫啟用 SQL 驗證。 
			
		$dbServerName = "<servername>"
		$dbUserName = "<username>"
		$dbPassword = "<password>"

3. 在 **Azure PowerShell** 中，導覽至 **C:\ADFWalkthrough\OnPremises** 資料夾。
4.	執行 **prepareOnPremDatabase&Table.ps1** **(以雙引號括住 &，或如下所示)**。
			
		& '.\prepareOnPremDatabase&Table.ps1'

5. 一旦指令碼執行成功，您會看到下列項目：
			
		PS E:\ADF\ADFWalkthrough\OnPremises> & '.\prepareOnPremDatabase&Table.ps1'
		6/10/2014 10:12:33 PM Script to create sample on-premises SQL Server Database and Table
		6/10/2014 10:12:33 PM Creating the database [MarketingCampaigns], table and stored procedure on [.]...
		6/10/2014 10:12:33 PM Connecting as user [sa]
		6/10/2014 10:12:33 PM Summary:
		6/10/2014 10:12:33 PM 1. Database 'MarketingCampaigns' created.
		6/10/2014 10:12:33 PM 2. 'MarketingCampaignEffectiveness' table and stored procedure 


### 建立連結的服務

1.	在 **Azure Preview 入口網站**中，按一下 **LogProcessingFactory** 的 **DATA FACTORY** 刀鋒視窗上的 [**連結服務**] 磚。
2.	在 [**連結服務**] 刀鋒視窗中，按一下 [**新增 (+) 資料存放區**]。
3.	在 [新增資料存放區] 刀鋒視窗中，針對 [名稱] 輸入 **OnPremSqlLinkedService**。 
4.	按一下 [**類型 (需要設定)**]，然後選取 [**SQL Server**]。您現在應該會在 [**新增資料存放區**] 刀鋒視窗中看到 [**資料閘道**]、[**伺服器**]、[**資料庫**]，以及 [**認證**] 設定。 
5.	按一下 [**資料閘道 (設定必要的設定)**]，然後選取您先前建立的 [**MyGateway**]。 
6.	輸入裝載 **MarketingCampaigns** 資料庫的資料庫伺服器**名稱**。 
7.	針對 [資料庫] 輸入 **MarketingCampaigns**。 
8.	按一下 [**認證**]。 
9.	在 [**認證**] 刀鋒視窗中，按一下 [**按一下這裡以安全地設定認證**]。
10.	第一次時會安裝一鍵式應用程式，並啟動 [**設定認證]**對話方塊。11.	在 [**設定認證**] 對話方塊中，輸入 [**使用者名稱**] 和 [**密碼**]，然後按一下 [**確定**]。請稍候，待對話方塊關閉。 
12.	按一下 [**新增資料存放區**] 刀鋒視窗中的 [**確定**]。 
13.	在 [**連結服務**] 刀鋒視窗中，確認 **OnPremSqlLinkedService** 顯示在清單中，且連結服務的 [**狀態**] 為 [**良好**]。

## <a name="OnPremStep3"></a>步驟 3：建立資料表和管線

### 建立內部部署邏輯資料表

1.	在 **Azure PowerShell** 中，切換至 **C:\ADFWalkthrough\OnPremises** 資料夾。 
2.	使用 Cmdlet **New-AzureDataFactoryTable** 來建立 **MarketingCampaignEffectivenessOnPremSQLTable.json** 的資料表，如下所示。

			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\MarketingCampaignEffectivenessOnPremSQLTable.json
	 
#### 建立將資料從 Azure Blob 複製到 SQL Server 的管線

1.	使用 Cmdlet **New-AzureDataFactoryPipeline** 來建立 **EgressDataToOnPremPipeline.json** 的管線，如下所示。

			
		New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\EgressDataToOnPremPipeline.json
	 
2. 使用 Cmdlet **Set-AzureDataFactoryPipelineActivePeriod** 來指定 **EgressDataToOnPremPipeline** 的作用期間。

			
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z -EndDateTime 2014-05-05Z –Name EgressDataToOnPremPipeline

	請按 **'Y'** 繼續進行。
	
## <a name="OnPremStep4"></a>步驟 4：監視管線和檢視結果

您現在可以使用[步驟 6：監視資料表和管線](#MainStep6)中介紹的相同步驟，監視新的內部部署 ADF 資料表的新管線和資料配量。
 
當您看到資料表 **MarketingCampaignEffectivenessOnPremSQLTable** 的配量狀態變成「就緒」時，這表示管線已完成配量的執行。若要檢視結果，請查詢 **MarketingCampaignEffectiveness** 資料表 (位於 SQL Server 的 **MarketingCampaigns** 資料庫中)。
 
恭喜！ 您已成功完成使用內部部署資料來源的逐步解說。
 

[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[datafactorytutorial]: data-factory-tutorial-using-powershell.md
[adfgetstarted]: data-factory-get-started.md
[adfintroduction]: data-factory-introduction.md
[useonpremisesdatasources]: data-factory-use-onpremises-datasources.md
[usepigandhive]: data-factory-pig-hive-activities.md

[azure-preview-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/download/details.aspx?id=35580
[azure-sql-firewall]: http://msdn.microsoft.com/library/azure/jj553530.aspx

[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[adfwalkthrough-download]: http://go.microsoft.com/fwlink/?LinkId=517495
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[image-data-factory-datamanagementgateway-configuration-manager]: ./media/data-factory-tutorial-extend-onpremises-using-powershell/DataManagementGatewayConfigurationManager.png

 

<!---HONumber=62-->