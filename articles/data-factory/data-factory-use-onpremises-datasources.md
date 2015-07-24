<properties 
	pageTitle="啟用管線以使用內部部署資料 | Azure Data Factory" 
	description="了解如何使用 Azure Data Factory 註冊內部部署資料來源，並將資料複製到資料來源/複製資料來源中的資料。" 
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
	ms.date="06/04/2015" 
	ms.author="spelluru"/>

# 啟用管線以使用內部部署資料

若要啟用 Azure Data Factory 中的管線以使用內部部署資料來源，您需要運用 Azure 管理入口網站或 Azure PowerShell 將內部部署資料來源新增為 Data Factory 的連結服務。
 
若要將內部部署資料來源新增為 Data Factory 的連結服務，您必須先在內部部署電腦上下載並安裝「Microsoft 資料管理閘道」，然後設定內部部署資料來源的連結服務使用閘道。


## <a href="DMG"></a> 資料管理閘道

「資料管理閘道」是一套透過安全且可管理的方式，將內部部署資料來源連結至雲端服務的軟體。您可以使用「資料管理閘道」來進行下列作業：

- 「連接至內部部署資料以支援混合式資料存取」– 您可以將內部部署資料連結至雲端服務，以享受雲端服務的功能，同時保持企業使用內部部署資料持續運作。
- 「定義安全的資料 Proxy」– 您可以定義要對「資料管理閘道」公開哪些內部部署資料來源，閘道會依此驗證來自雲端服務的資料要求，並保護內部部署資料來源。
- 「管理您的閘道以達到完整控管」– 您將可完整地監視與記錄「資料管理閘道」內的所有活動，以便您加以管控。
- 「有效率地移動資料」– 資料會以平行方式傳輸，且系統會採用自動重試邏輯，修復間歇性網路問題。


「資料管理閘道」提供全方位內部部署資料連線功能，包括下列：

- 「不會干擾公司防火牆運作」– 資料管理閘道即裝即用，無需開啟防火牆連線或變更您公司網路基礎結構的配置。 
- 「透過您的憑證加密認證」– 用來連線到資料來源的認證會以完全由使用者所擁有的憑證加密。若沒有憑證，任何人 (包括 Microsoft 在內) 皆無法將認證解密成純文字。

### 使用資料管理閘道的考量
1.	「資料管理閘道」的單一執行個體可用於多個內部部署資料來源，但是請記住，**閘道器會繫結至一個 Azure Data Factory**，不能與另一個 Data Factory 共用。
2.	電腦上「只能安裝一個資料管理閘道的執行個體」。假設您有兩個需要存取內部部署資料來源的 Data Factory，您需要將繫結至不同 Data Factory 的閘道個別安裝在兩部內部部署電腦上。
3.	雖然閘道不需要和資料來源位在相同的電腦上，但越接近資料來源可縮短閘道連線到資料來源的時間。建議您安裝閘道器的電腦不同於裝載內部部署資料來源的電腦，如此閘道器才不會與資料來源爭奪資源。
4.	您可以有「多個閘道器在不同電腦上，但連接至相同的內部部署資料來源」。例如，您可能有兩個閘道器用於服務兩個 Data Factory，但相同的內部部署資料來源都向這兩個 Data Factory 註冊。 
5.	若您已在電腦上安裝用於 **Power BI** 案例的閘道器，請於另一台電腦上安裝另一個用於 Azure Data Factory 的閘道器。

### 連接埠和安全性考量 
- 「資料管理閘道」安裝程式會開啟閘道器電腦上的 **8050** 和 **8051** 連接埠。這些連接埠由「認證管理員」(ClickOnce 應用程式) 使用，可讓您設定內部部署連結服務的認證，以及測試資料來源的連接。網際網路無法連接至這些連接埠，您不需要在公司防火牆中開啟這些連接埠。
- 在內部部署 SQL Server 資料庫和 Azure SQL Database 之間往返複製資料時，請確定下列各項：
 
	- 閘道器電腦上的防火牆允許從 **TCP** 連接埠 **1433** 連出的 TCP 通訊
	- 設定 [SQL Azure 防火牆設定](https://msdn.microsoft.com/library/azure/jj553530.aspx)，將閘道器電腦的 IP 位址加入至允許的 IP 位址。

- 當複製資料至/從內部部署 SQL Server 至任何目的地，而且閘道器和 SQL Server 電腦不同時，請執行下列動作：在 SQL Server 電腦上[設定 Windows 防火牆](https://msdn.microsoft.com/library/ms175043.aspx)，讓閘道器可以透過 SQL Server 執行個體所接聽的連接埠存取資料庫。在預設執行個體中，這是連接埠 1433。

- 您必須在能夠連接至「資料管理閘道」的電腦上啟動「認證管理員」應用程式，才能設定資料來源的認證，以及測試資料來源的連接。

### 閘道器安裝 - 先決條件 

1.	支援的作業系統版本包括 Windows 7、Windows 8/8.1、Windows Server 2008 R2、Windows Server 2012。
2.	建議閘道器電腦的「組態」至少為 2 GHz、4 核心、8 GB RAM 和 80 GB 磁碟。
3.	如果主機電腦休眠，閘道器即無法回應資料要求。因此，安裝閘道器之前，請先在電腦上設定適當的「電源計劃」。如果電腦已設定為休眠，安裝閘道器時會提示訊息。  


 

## 逐步介紹

在本逐步解說中，您可以使用將資料從內部部署 SQL Server 資料庫移至 Azure Blob 的管線，來建立一個 Data Factory。

## 步驟 1：建立 Azure Data Factory
在此步驟中，您可以使用 Azure 管理入口網站來建立名為 **ADFTutorialOnPremDF** 的 Azure Data Factory 執行個體。您也可以使用 Azure Data Factory Cmdlet 來建立 Data Factory。

1.	登入 [Azure Preview 入口網站][azure-preview-portal]之後，從左下角按一下 [**新增**]、在 [**建立**] 刀鋒視窗選取 [**資料分析**]，然後在 [**資料分析**] 刀鋒視窗上按一下 **Data Factory**。

	![新增->DataFactory][image-data-factory-new-datafactory-menu]
  
6. 在 [**新增 Data Factory**] 刀鋒視窗中：
	1. 輸入 **ADFTutorialOnPremDF** 做為名稱。
	2. 按一下 [資源群組名稱]，然後選取 **ADFTutorialResourceGroup** (如果您已經完成[開始使用 Azure Data Factory][adfgetstarted]教學課程)。您可以選取現有的資源群組，或建立一個新的群組。若要建立新的資源群組：
		1. 按一下 [建立新的資源群組]。
		2. 在 [**建立資源群組**] 刀鋒視窗中，輸入資源群組的 [**名稱**]，然後按一下 [**確定**]。

7. 請注意，[**新增 Data Factory**] 刀鋒視窗上已核取 [**新增至開始面板**]。

	![新增至儀表板][image-data-factory-add-to-startboard]

8. 在 [**新增 Data Factory**] 刀鋒視窗中，按一下 [**建立**]。

	Azure Data Factory 的名稱在全域必須是唯一的。如果您收到錯誤：**Data Factory 名稱 “ADFTutorialOnPremDF” 無法使用**，請變更 Data Factory 名稱 (例如 yournameADFTutorialOnPremDF)，然後嘗試重新建立。執行此教學課程中的其餘步驟時，請使用此名稱來取代 ADFTutorialOnPremDF。

9. 尋找左側 [**通知**] 中樞中來自建立程序的通知。按一下 **X** 關閉 [**通知**] 刀鋒視窗 (若已開啟)。

	![通知中樞][image-data-factory-notifications-hub]

11. 建立完成之後，您會看到 **Data Factory** 刀鋒視窗，如下所示：

	![Data Factory 首頁][image-data-factory-datafactory-homepage]

## 步驟 2：建立資料管理閘道
5.	在 **ADFTutorialOnPremDF** 的 **Data Factory** 刀鋒視窗中，按一下 [**連結服務**]。 

	![Data Factory 首頁][image-data-factory-home-age]

2.	在 [**連結服務**] 刀鋒視窗中，按一下 [**+ 資料閘道**]。

	![連結的服務 - [新增閘道器] 按鈕][image-data-factory-linkedservices-add-gateway-button]

2. 在 [**建立**] 刀鋒視窗上，輸入 **adftutorialgateway** 做為 [**名稱**]，然後按一下 [**確定**]。

	![[建立閘道器] 刀鋒視窗][image-data-factory-create-gateway-blade]

3. 在 [**設定**] 分頁中，按一下 [**直接安裝在此電腦上**]。這會下載閘道的安裝套件、在電腦上安裝、設定和註冊閘道。

	> [AZURE.NOTE]請使用 Internet Explorer 或 Microsoft ClickOnce 相容的 Web 瀏覽器。

	![閘道器 - [設定] 刀鋒視窗][image-data-factory-gateway-configure-blade]

	這是最簡單的方式 (一鍵)，透過單一步驟即可下載、安裝、設定和註冊閘道。您可以看到「Microsoft 資料管理閘道組態管理員」應用程式已安裝在電腦上。您也可以在此資料夾中找到執行檔 **ConfigManager.exe**：**C:\Program Files\Microsoft Data Management Gateway\1.0\Shared**。

	您也可以使用此刀鋒視窗中的連結手動下載與安裝閘道器，並使用 [**註冊金鑰**] 文字方塊中顯示的金鑰來加以註冊。
	
	如需閘道器的詳細資訊 (包括最佳作法和重要考量)，請參閱[資料管理閘道](#DMG)一節。

	>[AZURE.NOTE]您必須是本機電腦上的系統管理員，才能成功安裝和設定「資料管理閘道」。您可以將其他使用者加入至資料管理閘道使用者本機 Windows 群組。此群組的成員可以使用「資料管理閘道組態管理員」工具來設定閘道器。

4. 按一下左側的 [通知] 中樞。等待 [**通知**] 刀鋒視窗中出現 [**'adftutorialgateway' 的快速安裝成功**] 訊息。

	![快速安裝成功][express-setup-succeeded]
5. 依序在 [**建立**] 刀鋒視窗及 [**新增 Data Factory**] 刀鋒視窗中，按一下 [**確定**]。
6. 關閉 [**連結服務**] 刀鋒視窗 (按右上角的 **X** 按鈕)，重新開啟 [**連結服務**] 刀鋒視窗以查看最新的閘道器狀態。 
7. 確認閘道器的 [狀態] 為 [線上]。 

	![閘道器狀態][gateway-status]
5. 在電腦上啟動「Microsoft 資料管理閘道組態管理員」應用程式。

	![閘道器組態管理員][image-data-factory-gateway-configuration-manager]

6. 請稍候，待這些值設定完成，如下所示：
	1. 如果服務的 [狀態] 未設定為 [已啟動]，請按一下 [啟動服務] 來啟動服務，並稍等一下，待其他欄位重新整理完成。
	2. [閘道器名稱] 設定為 **adftutorialgateway**。
	3. [執行個體名稱] 設定為 **adftutorialgateway**。
	4. [閘道器金鑰狀態] 設定為 [已註冊]。
	5. 底部的狀態列會顯示 [已連接到資料管理閘道雲端服務] 和一個綠色的核取記號。
	
7. 在 [**連結服務**] 刀鋒視窗上，確認閘道器的 [**狀態**] 為 [**良好**]。
8. 關閉所有刀鋒視窗，直到返回 **Data Factory** 首頁。 

## 步驟 2：建立連結服務 
在此步驟中，您將建立兩個連結服務：**StorageLinkedService** 和 **SqlServerLinkedService****SqlServerLinkedService** 連結內部部署 SQL Server 資料庫，而 **StorageLinkedService** 連結服務會將 Azure Blob 存放區連結至 **ADFTutorialDataFactory**。稍後在本逐步解說中，您將建立可將內部部署 SQL Server 資料庫的資料複製到 Azure Blob 存放區的管線。

### 在內部部署 SQL Server 資料庫中新增連結服務
1.	在 **DATA FACTORY** 刀鋒視窗中，按一下 [**製作和部署**] 磚來啟動 Data Factory 的**編輯器**。

	![[製作和部署] 磚][image-author-deploy-tile]

	如需 Data Factory 編輯器的詳細概觀，請參閱 [Data Factory 編輯器][data-factory-editor]主題。

2.	在 [**編輯器**] 中，按一下工具列上的 [**新增資料存放區**] 按鈕，然後從下拉式功能中表選取 [**內部部署 SQL Server 資料庫**]。

	![編輯器 [新增資料存放區] 按鈕][image-editor-newdatastore-onpremsql-button]
    
3.	在右窗格中，您應該會看到用來建立內部部署 SQL Server 連結服務的 JSON 範本。![內部部署 SQL 連結服務 - 設定][image-editor-newdatastore-onpremsql-settings]

4.	在 JSON 窗格中，執行下列動作：
	1.	在 **gatewayName** 屬性中，輸入 **adftutorialgateway** 取代雙引號內的所有文字。  
	2.	如果您使用 **SQL 驗證**： 
		1.	在 **connectionString** 屬性中，將 **<servername>**、**<databasename>**、**<username>** 和 **<password>** 分別取代為您的內部部署 SQL Server 名稱、資料庫名稱、使用者帳戶和密碼。	
		2.	從 JSON 檔案中移除最後兩個屬性 (**username** 和 **password**)，並從剩餘的 JSON 指令碼中移除最後一行結尾的「逗號 (,)」 字元。
		
				{
	    			"name": "SqlServerLinkedService",
	    			"properties": {
		        		"type": "OnPremisesSqlLinkedService",
		        		"ConnectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
		        		"gatewayName": "adftutorialgateway"
	    			}
				}
	3.	如果您使用「Windows 驗證」：
		1. 在 **connectionString** 屬性中，將 **<servername>** 和 **<databasename>** 分別取代為您的內部部署 SQL Server 名稱和資料庫名稱。將 **Integrated Security** 設定為 **True**。從連接字串中移除 **ID** 和 **Password**。
			
				{
    				"name": "SqlServerLinkedService",
    				"properties": {
        				"type": "OnPremisesSqlLinkedService",
        				"ConnectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;",
		   				"gatewayName": "adftutorialgateway",
				        "username": "<Specify user name if you are using Windows Authentication>",
				        "password": "<Specify password for the user account>"
    				}
				}		
		
6. 按一下工具列上的 [**部署**]，部署 SqlServerLinkedService。確認您在標題列看到 [**已成功建立連結服務**] 訊息。在左邊的樹狀檢視中，您也應該會看到 **SqlServerLinkedService**。
		   
	![SqlServerLinkedService 部署成功][image-editor-sql-linked-service-successful]
	
  
您也可以按一下 [**連結服務**] 刀鋒視窗上的 [**新增資料存放區**] 工具列按鈕，以建立 SQL Server 連結服務。如果要這樣做，您需要使用在電腦上執行的「認證管理員」ClickOnce 應用程式存取入口網站，設定資料來源的認證。如果您從閘道器電腦以外的另一台電腦存取入口網站，您必須確定「認證管理員」應用程式可以連接到閘道器電腦。如果應用程式無法連接閘道器電腦，它將不允許您設定資料來源的認證，以及測試資料來源的連接。

#### 新增 Azure 儲存體帳戶的連結服務
 
1. 在 [**編輯器**] 中，按一下工具列上的 [**新增資料存放區**] 按鈕，然後從下拉式功能表中選取 [**Azure 儲存體**]。在右窗格中，您應該會看到用來建立 Azure 儲存體連結服務的 JSON 範本。 

	![編輯器 [新增資料存放區] 按鈕][image-editor-newdatastore-button]
    
6. 將 **<accountname>** 和 **<accountkey>** 取代為您的 Azure 儲存體帳戶的帳戶名稱和帳戶金鑰值。

	![編輯器 Blob 儲存體 JSON][image-editor-blob-storage-json]
	
	如需 JSON 屬性的詳細資料，請參閱 [JSON 指令碼參考](http://go.microsoft.com/fwlink/?LinkId=516971)。

6. 按一下工具列上的 [**部署**]，部署 StorageLinkedService。確認您在標題列看到 [已成功建立連結服務] 訊息。

	![編輯器 Blob 儲存體部署][image-editor-blob-storage-deploy]

 
## 步驟 3：建立輸入和輸出資料集
在此步驟中，您會建立代表複製作業的輸入和輸出資料的輸入和輸出資料集 (內部部署 SQL Server 資料庫 => Azure Blob 儲存體)。在建立資料集或資料表 (矩形資料集) 之前，您必須執行下列動作 (清單後列有詳細步驟)：

- 在您新增為 Data Factory 連結服務的 SQL Server 資料庫中，建立名為 **emp** 的資料表，並在資料表中插入幾個範例項目。
- - 如果您尚未完成[開始使用 Azure Data Factory][adfgetstarted] 一文的教學課程，請在您新增為 Data Factory 連結服務的 Azure blob 儲存體帳戶中，建立名為 **adftutorial** 的 Blob 容器。。

### 準備用於教學課程的內部部署 SQL Server

1. 在您指定用於內部部署 SQL Server 連結服務 (**SqlServerLinkedService**) 的資料庫中，使用下列 SQL 指令碼在資料庫中建立 **emp** 資料表。


        CREATE TABLE dbo.emp
		(
			ID int IDENTITY(1,1) NOT NULL, 
			FirstName varchar(50),
			LastName varchar(50),
    		CONSTRAINT PK_emp PRIMARY KEY (ID)
		)
		GO
 

2. 在資料表中插入一些範例：


        INSERT INTO emp VALUES ('John', 'Doe')
		INSERT INTO emp VALUES ('Jane', 'Doe')



### 建立輸入資料表

1.	在 [Data Factory 編輯器] 中，按一下命令列的 [新增資料集]，然後按一下 [內部部署 SQL]。 
2.	使用下列文字取代右窗格中的 JSON：    

        {
    		"name": "EmpOnPremSQLTable",
    		"properties":
    		{
        		"location":
        		{
            		"type": "OnPremisesSqlServerTableLocation",
            		"tableName": "emp",
            		"linkedServiceName": "SqlServerLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "Hour",
            		"interval": 1,       
	    			"waitOnExternal":
	    			{
        				"retryInterval": "00:01:00",
	        			"retryTimeout": "00:10:00",
	        			"maximumRetry": 3
	    			}
		  
        		}
    		}
		}

	請注意：
	
	- location **type** 設定為 **OnPremisesSqlServerTableLocation**。
	- **tableName** 設定為 **emp**。
	- **linkedServiceName** 設定為 **OnPremSqlLinkedService** (您已在步驟 2 中建立此連結服務)。
	- 針對不是由 Azure Data Factory 中的另一個管線產生的輸入資料表，您必須在 JSON 中指定 **waitOnExternal** 區段。它代表輸入資料產生於 Azure Data Factory 服務外部。   

	如需 JSON 屬性的詳細資料，請參閱 [JSON 指令碼參考][json-script-reference]。

2. 按一下命令列的 [部署]，以部署資料集 (資料表是矩形的資料集)。確認您在標題列看到 [已成功部署資料表] 訊息。


### 建立輸出資料表

1.	在 [Data Factory 編輯器] 中，按一下命令列的 [新增資料集]，然後按一下 [Azure Blob 儲存體]。
2.	使用下列文字取代右窗格中的 JSON： 

		{
    		"name": "OutputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
            		"folderPath": "adftutorial/outfromonpremdf",
            		"format":
            		{
                		"type": "TextFormat",
                		"columnDelimiter": ","
            		},
            		"linkedServiceName": "StorageLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "Hour",
            		"interval": 1
        		}
    		}
		}
  
	請注意：
	
	- location **type** 設為 **AzureBlobLocation**。
	- **linkedServiceName** 設定為 **StorageLinkedService** (您已在步驟 2 中建立此連結服務)。
	- **folderPath** 設定為 **adftutorial/outfromonpremdf**，其中 outfromonpremdf 是 adftutorial 容器中的資料夾。您只需要建立 **adftutorial** 容器。
	- **availability** 設定為**每小時**，且 (**frequency** 設定為**小時**，**interval** 設定為 **1**)。Data Factory 服務會每小時在 Azure SQL Database 的 **emp** 資料表中產生輸出資料配量。 

	如果您沒有指定**輸入資料表**的 **fileName**，則輸入資料夾 (**folderPath**) 中的所有檔案/Blob 都會視為輸入。如果您在 JSON 中指定 fileName，則只有指定的檔案/Blob 會被視為輸入。如需範例，請參閱[教學課程][adf-tutorial]中的範例檔案。
 
	如果您未指定**輸出資料表**的 **fileName**，**folderPath** 中產生的檔案會依照下列格式命名：<Guid>.txt (例如：Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.)。

	若要根據 **SliceStart** 時間動態地設定 **folderPath** 和 **fileName**，請使用 partitionedBy 屬性。在下列範例中，folderPath 使用 SliceStart (處理配量的開始時間) 中的年、月和日，fileName 使用 SliceStart 中的小時。例如，如果配量產生於 2014-10-20T08:00:00，folderName 設定為 wikidatagateway/wikisampledataout/2014/10/20，而 fileName 設定為 08.csv。

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

 

	如需 JSON 屬性的詳細資料，請參閱 [JSON 指令碼參考][json-script-reference]。

2.	按一下命令列的 [部署]，以部署資料集 (資料表是矩形的資料集)。確認您在標題列看到 [已成功部署資料表] 訊息。
  

## 步驟 4：建立和執行管線
在此步驟中，您會建立**管線**，其中包含一個使用 **EmpOnPremSQLTable** 做為輸入和 **OutputBlobTable** 做為輸出的**複製活動**。

1.	按一下命令列的 [新增管線]。如果沒看到此按鈕，請按一下 [...] (省略符號) 展開命令列。
2.	使用下列文字取代右窗格中的 JSON：   


        {
			"name": "ADFTutorialPipelineOnPrem",
    		"properties":
    		{
        		"description" : "This pipeline has one Copy activity that copies data from an on-prem SQL to Azure blob",
	       		 "activities":
	        	[
			    	{
						"name": "CopyFromSQLtoBlob",
						"description": "Copy data from on-prem SQL server to blob",		
						"type": "CopyActivity",
						"inputs": [ {"name": "EmpOnPremSQLTable"} ],
						"outputs": [ {"name": "OutputBlobTable"} ],
						"transformation":
						{
							"source":
							{                               
								"type": "SqlSource",
								"sqlReaderQuery": "select * from emp"
							},
							"sink":
							{
								"type": "BlobSink"
							}	
						},
						"Policy":
						{
							"concurrency": 1,
							"executionPriorityOrder": "NewestFirst",
							"style": "StartOfInterval",
							"retry": 0,
							"timeout": "01:00:00"
						}		

				     }
	        	],
				"start": "2015-02-13T00:00:00Z",
        		"end": "2015-02-14T00:00:00Z",
        		"isPaused": false
			}
		}

	請注意：
 
	- 在 activities 區段中，只會有一個活動的 **type** 設為 **CopyActivity**。
	- 活動的**輸入**設定為 **EmpOnPremSQLTable**，活動的**輸出**則設定為 **OutputBlobTable**。
	- 在 **transformation** 區段中，「來源類型」指定為 **SqlSource**，「接收類型」指定為 **BlobSink**。 - **SqlSource** 的 **sqlReaderQuery** 屬性指定 SQL 查詢 **select * from emp**。

	將 **start** 屬性的值取代為目前日期，並將 **end** 值取代為隔天的日期。開始和結束日期時間都必須是 [ISO 格式](http://en.wikipedia.org/wiki/ISO_8601)。例如：2014-10-14T16:32:41Z。**end** 時間為選擇性項目，但在本教學課程中會用到。
	
	如果您未指定 **end** 屬性的值，則會以「**start + 48 小時**」計算。若要無限期地執行管線，請指定 **9/9/9999** 做為 **end** 屬性的值。
	
	您定義將根據為每個 Azure Data Factory 資料表定義的 **Availability** 屬性來處理資料配量的持續時間。
	
	在上述範例中，由於每小時即產生一個資料配量，共會有 24 個資料配量。
	
2. 按一下命令列的 [部署]，以部署資料集 (資料表是矩形的資料集)。確認您在標題列看到 [已成功部署管線] 訊息。
5. 現在，按一下 **X** 關閉 [**編輯器**] 刀鋒視窗。再按一次 **X**，關閉內含工具列和樹狀檢視的 [ADFTutorialDataFactory] 刀鋒視窗。如果您看到 [將捨棄未儲存的編輯] 訊息，請按一下 [確定]。
6. 您應該會回到 **ADFTutorialOnPremDF** 的 **DATA FACTORY** 刀鋒視窗。

**恭喜！** 您已成功建立 Azure Data Factory、連結服務、資料表和管線，以及排定的管線。

### 在圖表檢視中檢視 Data Factory 
1. 在「Azure Preview 入口網站」中，在 **ADFTutorialOnPremDF** Data Factory 的首頁上按一下 [圖表] 磚：

	![圖表連結][image-data-factory-diagram-link]

2. 您應該會看到如下所示的圖表：

	![圖表檢視][image-data-factory-diagram-view]

	您可以將管線和資料表放大、縮小、放大到 100%、縮放至適當比例和自動定位，以及顯示歷程資訊 (反白顯示所選取項目的上游和下游項目)。您可以按兩下物件 (輸入/輸出資料表或管線) 查看其屬性。

## 步驟 5：監視資料集和管線
在此步驟中，您將使用 Azure 入口網站來監視 Azure Data Factory 的運作情形。您也可以使用 PowerShell Cmdlet 來監視資料集和管線。如需關於使用 Cmdlet 進行監視的詳細資訊，請參閱[使用 PowerShell 監視和管理 Azure Data Factory][monitor-manage-powershell]。

1. 瀏覽至「Azure Preview 入口網站」(如果您已關閉它)
2. 如果 [**ADFTutorialOnPremDF**] 刀鋒視窗尚未開啟，請按一下**開始面板**上的 [**ADFTutorialOnPremDF**] 以開啟此刀鋒視窗。
3. 您應該會看到您在此刀鋒視窗上建立之資料表和管線的**計數**和**名稱**。

	![Data Factory 首頁][image-data-factory-homepage-2]
4. 現在，按一下 [資料集] 磚。
5. 在 [**資料集**] 刀鋒視窗中，按一下 [**EmpOnPremSQLTable**]。

	![EmpOnPremSQLTable 配量][image-data-factory-onprem-sqltable-slices]

6. 請注意，到目前為止已經產生資料配量，且它們的狀態為 [**就緒**]。這是因為您已將資料插入 SQL Server 資料庫中，而資料一直留存於其中。確認下方的 [問題配量] 區段中沒有顯示任何配量。


	[最近更新的配量] 和 [最近失敗的配量] 清單都依照 [上次更新時間] 排序。在下列情況中，配量的更新時間會變更。
    

	-  您以手動方式更新配量的狀態，例如，使用 **Set-AzureDataFactorySliceStatus** (或) 在配量的 [配量] 刀鋒視窗上按一下 [執行]。
	-  配量因為執行而變更狀態 (例如，開始執行、執行結束但失敗、執行結束且成功等)。
 
	按一下清單的標題或 **...** (省略符號)，查看更長的配量清單。按一下工具列上的 [篩選] 以篩選配量。
	
	若要改成依配量開始/結束時間來檢視資料配量，請按一下 [資料配量 (依配量時間)] 磚。

7. 現在，在 [**資料集**] 刀鋒視窗中，按一下 [**OutputBlobTable**]。

	![OputputBlobTable 配量][image-data-factory-output-blobtable-slices]
8. 確認到目前為止的配量已產生，且狀態為 [就緒]。等待到目前為止的配量的狀態設為 [就緒]。
9. 按一下清單中的任何資料配量，您應該會看到 [**資料配量**] 刀鋒視窗。

	![資料配量刀鋒視窗][image-data-factory-dataslice-blade]

	如果配量不是 [就緒] 狀態，您可以在 [未就緒的上游配量] 清單中看到未就緒且阻礙目前配量執行的上游配量。

10. 按一下底部清單中的 [活動執行]，可查看 [活動執行詳細資料]。

	![[活動執行詳細資料] 刀鋒視窗][image-data-factory-activity-run-details]

11. 按一下 **X** 關閉所有刀鋒視窗，直到您回到 **ADFTutorialOnPremDF** 的起始刀鋒視窗。
14. (選用) 依序按一下 [管線] 及 [ADFTutorialOnPremDF]，然後逐步深入輸入資料表 (**已使用**) 或輸出資料表 (**已產生**)。
15. 使用「Azure 儲存體總管」之類的工具來驗證輸出。

	![Azure 儲存體總管][image-data-factory-stroage-explorer]


## 使用 Azure PowerShell 建立和註冊閘道器 
本節說明如何使用 Azure PowerShell Cmdlet 建立和註冊閘道。

1. 在系統管理員模式下啟動 **Azure PowerShell**。 
2. **AzureResourceManager** 模式中可以使用 Azure Data Factory Cmdlet。執行下列命令以切換至 **AzureResourceManager** 模式。     

        switch-azuremode AzureResourceManager


2. 使用 **New-AzureDataFactoryGateway** Cmdlet 來建立邏輯閘道器，如下所示：

		New-AzureDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>

	**範例命令和輸出**：


		PS C:\> New-AzureDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description “gateway for walkthrough”

		Name              : MyGateway
		Description       : gateway for walkthrough
		Version           :
		Status            : NeedRegistration
		VersionStatus     : None
		CreateTime        : 9/28/2014 10:58:22
		RegisterTime      :
		LastConnectTime   :
		ExpiryTime        :
		ProvisioningState : Succeeded


3. 使用 **New-AzureDataFactoryGatewayKey** Cmdlet 來為新建立的閘道器產生註冊金鑰，並將金鑰儲存在本機變數 **$Key** 中：

		New-AzureDataFactoryGatewayKey -GatewayName <gatewayname> -ResourceGroupName ADF -DataFactoryName <dataFactoryName>

	
	**範例命令的輸出：**


		PS C:\> $Key = New-AzureDataFactoryGatewayKey -GatewayName MyGateway -ResourceGroupName ADF -DataFactoryName $df 

	
4. 在 Azure PowerShell 中，切換到資料夾：**C:\Program Files\Microsoft Data Management Gateway\1.0\PowerShellScript**，然後執行與本機變數 **$Key** 相關聯的 **RegisterGateway.ps1** 指令碼 (如以下命令所示)，將您電腦上安裝的用戶端代理程式，註冊到您稍早建立的邏輯閘道器。

		PS C:\> .\RegisterGateway.ps1 $Key.GatewayKey
		
		Agent registration is successful!

5. 您可以使用 **Get-AzureDataFactoryGateway** Cmdlet 取得 Data Factory 中的閘道器器清單。當 [狀態] 顯示為 [線上] 時，表示您的閘道器已就緒可供使用。

		Get-AzureDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF

您可以使用 **Remove-AzureDataFactoryGateway** Cmdlet 移除閘道器，並使用 **Set-AzureDataFactoryGateway** Cmdlet 更新閘道器的說明。如需這些 Cmdlet 的語法及其他詳細資訊，請參閱 Data Factory Cmdlet 參考文件。



[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[adf-getstarted]: data-factory-get-started.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[data-factory-editor]: data-factory-editor.md

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[64bit-download-link]: http://go.microsoft.com/fwlink/?LinkId=517623
[32bit-download-link]: http://go.microsoft.com/fwlink/?LinkId=517624

[azure-preview-portal]: http://portal.azure.com
[adfgetstarted]: data-factory-get-started.md
[monitor-manage-powershell]: data-factory-monitor-manage-using-powershell.md





[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456



[azure-powershell-install]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/

[image-author-deploy-tile]: ./media/data-factory-use-onpremises-datasources/author-deploy-tile.png
[image-editor-newdatastore-button]: ./media/data-factory-use-onpremises-datasources/editor-newdatastore-button.png
[image-editor-blob-storage-json]: ./media/data-factory-use-onpremises-datasources/editor-blob-storage-settings.png
[image-editor-blob-storage-deploy]: ./media/data-factory-use-onpremises-datasources/editor-deploy-blob-linked-service.png
[image-editor-newdatastore-onpremsql-button]: ./media/data-factory-use-onpremises-datasources/editor-newdatastore-onpremsql-button.png
[image-editor-newdatastore-onpremsql-settings]: ./media/data-factory-use-onpremises-datasources/editor-onprem-sql-settings.png
[image-editor-sql-linked-service-successful]: ./media/data-factory-use-onpremises-datasources/editor-sql-linked-service-successful.png

[gateway-status]: ./media/data-factory-use-onpremises-datasources/gateway-status.png
[express-setup-succeeded]: ./media/data-factory-use-onpremises-datasources/express-setup-succeeded.png
[image-data-factory-onprem-new-everything]: ./media/data-factory-use-onpremises-datasources/OnPremNewEverything.png

[image-data-factory-onprem-datastorage-cache-backup]: ./media/data-factory-use-onpremises-datasources/OnPremDataStorageCacheBackup.png

[image-data-factory-onprem-datastorage-see-all]: ./media/data-factory-use-onpremises-datasources/OnPremDataStorageSeeAll.png

[image-data-factory-onprem-dataservices-blade]: ./media/data-factory-use-onpremises-datasources/OnPremDataServicesBlade.png

[image-data-factory-onprem-datafactory-preview-blade]: ./media/data-factory-use-onpremises-datasources/DataFactoryPreviewBlade.png

[image-data-factory-onprem-create-resource-group]: ./media/data-factory-use-onpremises-datasources/OnPremCreateResourceGroup.png

[image-data-factory-add-to-startboard]: ./media/data-factory-use-onpremises-datasources/OnPremNewDataFactoryAddToStartboard.png

[image-data-factory-notifications-hub]: ./media/data-factory-use-onpremises-datasources/OnPremNotificationsHub.png

[image-data-factory-datafactory-homepage]: ./media/data-factory-use-onpremises-datasources/OnPremDataFactoryHomePage.png

[image-data-factory-startboard]: ./media/data-factory-use-onpremises-datasources/OnPremStartboard.png

[image-data-factory-linkedservices-blade]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesBlade.png

[image-data-factory-linkedservices-add-datastore-button]: ./media/data-factory-use-onpremises-datasources/ONPremLinkedServicesAddDataStoreButton.png

[image-data-factory-new-datastore-blade]: ./media/data-factory-use-onpremises-datasources/OnPremNewDataStoreBlade.png

[image-data-factory-get-storage-key]: ./media/data-factory-use-onpremises-datasources/OnPremGetStorageKey.png

[image-data-factory-linkedservices-add-gateway-button]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesAddGaewayButton.png

[image-data-factory-create-gateway-blade]: ./media/data-factory-use-onpremises-datasources/OnPremCreateGatewayBlade.png

[image-data-factory-gateway-configure-blade]: ./media/data-factory-use-onpremises-datasources/OnPremGatewayConfigureBlade.png

[image-data-factory-gateway-configuration-manager]: ./media/data-factory-use-onpremises-datasources/OnPremDMGConfigurationManager.png

[image-data-factory-oneclick-install]: ./media/data-factory-use-onpremises-datasources/OnPremOneClickInstall.png

[image-data-factory-diagram-link]: ./media/data-factory-use-onpremises-datasources/OnPremDiagramLink.png

[image-data-factory-diagram-view]: ./media/data-factory-use-onpremises-datasources/OnPremDiagramView.png

[image-data-factory-homepage-2]: ./media/data-factory-use-onpremises-datasources/OnPremDataFactoryHomePage2.png

[image-data-factory-stroage-explorer]: ./media/data-factory-use-onpremises-datasources/OnPremAzureStorageExplorer.png

[image-data-factory-home-age]: ./media/data-factory-use-onpremises-datasources/DataFactoryHomePage.png

[image-data-factory-onprem-sqltable-slices]: ./media/data-factory-use-onpremises-datasources/OnPremSQLTableSlicesBlade.png

[image-data-factory-output-blobtable-slices]: ./media/data-factory-use-onpremises-datasources/OutputBlobTableSlicesBlade.png

[image-data-factory-dataslice-blade]: ./media/data-factory-use-onpremises-datasources/DataSlice.png

[image-data-factory-activity-run-details]: ./media/data-factory-use-onpremises-datasources/ActivityRunDetailsBlade.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-use-onpremises-datasources/NewDataFactoryMenu.png

[image-data-factory-preview-portal-storage-key]: ./media/data-factory-get-started/PreviewPortalStorageKey.png

<!---HONumber=62-->