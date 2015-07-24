<properties 	
	pageTitle="Azure Data Factory - 範例" 
	description="提供 Azure Data Factory 服務隨附範例的詳細資料。" 
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
	ms.date="04/25/2015" 
	ms.author="spelluru"/>

# Azure Data Factory - 範例

## Azure 入口網站中的範例
您可以在 Azure 入口網站中使用 [範例管線] 刀鋒視窗，來快速部署、檢閱及測試 Azure Data Factory 範例。

1. 建立新的 Data Factory，或開啟現有的 Data Factory。如需建立 Data Factory 的步驟，請參閱 [開始使用 Azure Data Factory][data-factory-get-started]。
2. 在 Data Factory 的 [DATA FACTORY] 刀鋒視窗中，按一下 [範例管線] 磚。

	![範例管線磚](./media/data-factory-samples/SamplePipelinesTile.png)

2. 在 [範例管線] 刀鋒視窗中，按一下您想要部署的範例。
	
	![範例管線刀鋒視窗](./media/data-factory-samples/SampleTile.png)

3. 指定範例的組態設定。例如，您的 Azure 儲存體帳戶名稱和帳戶金鑰、Azure SQL Server 名稱、資料庫、使用者 ID 和密碼等。

	![範例刀鋒視窗](./media/data-factory-samples/SampleBlade.png)

4. 完成指定組態設定之後，請按一下 [建立]，以建立/部署範例管線，以及管線所使用的連結服務/資料表。
5. 您會在之前於 [範例管線] 刀鋒視窗中按下的範例磚上，看到部署的狀態。

	![部署狀態](./media/data-factory-samples/DeploymentStatus.png)

6. 當您看到範例磚出現 [部署成功] 訊息時，請關閉 [範例管線] 刀鋒視窗。
5. 在 [DATA FACTORY] 刀鋒視窗中，您會看到連結服務、資料集和管線已加入至您的 Data Factory。  

	![Data Factory 刀鋒視窗](./media/data-factory-samples/DataFactoryBladeAfter.png)
   

下表提供 [範例管線] 磚中可用範例的簡短描述。

範例名稱 | 說明
----------- | -----------
遊戲客戶分析 | Contoso 是一間打造多平台遊戲的遊戲公司：包括遊戲主機、手持裝置與個人電腦 (PC)。每個遊戲都會產生大量記錄檔。Contoso 的目標是要收集和分析這些遊戲所產生的記錄檔，以取得使用量資訊、發掘向上銷售與交叉銷售機會、開發新的強大功能等，藉此改善業務並為客戶提供更好的體驗。此範例收集範例記錄檔、處理並以參考資料加以充實，然後轉換資料以評估 Contoso 最近展開的行銷活動的成效。
 
## GitHub 上的範例
[GitHub Azure-DataFactory 儲存機制](https://github.com/azure/azure-datafactory)包含數個範例，可協助您快速運用 Azure Data Factory 服務，或修改指令碼並用在您自己的應用程式中。Samples\JSON 資料夾包含常見案例的 JSON 程式碼片段。

[data-factory-get-started]: data-factory-get-started.md#CreateDataFactory

<!---HONumber=62-->