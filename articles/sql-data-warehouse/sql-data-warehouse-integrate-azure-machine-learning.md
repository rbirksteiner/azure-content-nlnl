<properties
   pageTitle="搭配使用 Azure 機器學習服務與 SQL 資料倉儲 | Microsoft Azure"
   description="搭配使用 Azure 機器學習服務與 SQL 資料倉儲以便開發解決方案的秘訣。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/23/2015"
   ms.author="sahajs"/>

# 搭配使用 Azure 機器學習服務與 SQL 資料倉儲

Azure 機器學習服務是一項完全受管理的預測性分析服務，您可用來在 SQL 資料倉儲中針對您的資料建立預測模型，並將這些模型發佈為可供取用 Web 服務。閱讀[Azure 上的機器學習服務簡介][]，即可了解預測性分析和機器學習的基本概念。然後，您可以使用[建立實驗教學課程][]，了解如何建立、定型、評分和測試機器學習模型。

在本文中，您將學習如何使用 [Azure Machine Learning Studio][] 執行下列作業：從您的資料庫讀取資料，以建立、定型和評分預測模型 - 將資料寫入至您的資料庫


## 從 SQL 資料倉儲讀取資料

我們將從 AdventureWorksDW 資料庫中的 Product 資料表讀取資料。

### 步驟 1
按一下 Machine Learning Studio 視窗底部的 [+新增]，選取 [實驗]，然後選取 [空白實驗] 以開始新的實驗。選取畫布頂端的預設實驗名稱，然後將它重新命名為有意義的名稱，例如，單車價格預測。

### 步驟 2
在實驗畫布左邊的資料集和模組選擇區中尋找 [讀取器] 模組。將此模組拖曳到實驗畫布。![][drag_reader]

### 步驟 3
選取 [讀取器] 模組並填寫屬性窗格。1.選取 Azure SQL Database 做為資料來源。2.資料庫伺服器名稱：輸入伺服器名稱。您可以使用 [Azure 入口網站][]進行搜尋。![][server_name] 3.資料庫名稱：輸入您剛指定的伺服器上的資料庫名稱。4.伺服器使用者帳戶名稱：輸入具有資料庫存取權限的帳戶的使用者名稱。5.伺服器使用者帳戶名稱：提供指定之使用者帳戶的密碼。6.接受任何伺服器憑證：如果您想要在讀取資料前跳過檢閱網站憑證，請使用這個選項 (較不安全)。7.資料庫查詢：輸入 SQL 陳述式，描述您要讀取的資料。在此情況下，我們將使用下列查詢從 Product 資料表讀取資料。


```
SELECT ProductKey, EnglishProductName, StandardCost,
        ListPrice, Size, Weight, DaysToManufacture,
        Class, Style, Color
FROM dbo.DimProduct;
```

![][reader_properties]

### 步驟 4
1. 按一下實驗畫布下方的 [執行]，以執行實驗。
2. 實驗完成時，[讀取器] 模組都會呈現綠色核取標記，表示該模組已順利完成。同時也請留意位於右上角的 [執行完成] 狀態。![][run]
3. 若要查看匯入的資料，請按一下汽車資料集底部的輸出連接埠，然後選取 [視覺化]。

## 建立、定型和評分模型
您現在可以使用此資料集：- 建立模型：處理資料和定義功能 - 定型模型：選擇並套用學習演算法 - 評分和測試模型：預測新的單車價格
	
![][model]

若要深入了解如何建立、定型、評分和測試機器學習模型，請使用[建立實驗教學課程][]。

## 將資料寫入至 Azure SQL 資料倉儲

我們會將結果集寫入至 AdventureWorksDW 資料庫中的 ProductPriceForecast 資料表。

### 步驟 1
在實驗畫布左邊的資料集和模組選擇區中尋找 [寫入器] 模組。將此模組拖曳到實驗畫布。![][drag_writer]

### 步驟 2
選取 [寫入器] 模組並填寫屬性窗格。1.選取 Azure SQL Database 做為資料目的地。2.資料庫伺服器名稱：輸入伺服器名稱。您可以使用 [Azure 入口網站][]進行搜尋。3.資料庫名稱：輸入您剛指定的伺服器上的資料庫名稱。4.伺服器使用者帳戶名稱：輸入具有資料庫寫入權限的帳戶的使用者名稱。5.伺服器使用者帳戶名稱：提供指定之使用者帳戶的密碼。6.接受任何伺服器憑證 (不安全)：如果您不想檢視憑證，請選取此選項。7.要儲存之資料行的逗號分隔清單：提供您要輸出的資料集或結果資料行清單。8.資料表名稱：指定資料表的名稱。9.以逗號分隔的資料表資料行清單：指定要用於新資料表中的資料行名稱。資料行名稱可與來源資料集中的資料行名稱不同，但您必須在此列出您為輸出資料表定義的相同數目的資料行。10.每個 SQL Azure 作業寫入的資料列數目：您可以設定在單一作業中寫入至 SQL 資料庫的資料列數目。

![][writer_properties]

### 步驟 3
1. 按一下實驗畫布下方的 [執行]，以執行實驗。
2. 實驗完成時，所有模組都會呈現綠色核取標記，表示它們已順利完成。 



## 後續步驟
如需整合概觀，請參閱 [SQL 資料倉儲整合概觀][]。如需更多開發秘訣，請參閱 [SQL 資料倉儲開發概觀][]。

<!--Image references-->
[drag_reader]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-drag-reader.png
[server_name]: ./media/sql-data-warehouse-integrate-azure-machine-learning/dw-server-name.png
[reader_properties]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-reader-properties.png
[run]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-finished-running.png
[model]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-create-train-score-model.png
[drag_writer]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-drag-writer.png
[writer_properties]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-writer-properties.png


<!--Article references-->

[SQL 資料倉儲開發概觀]: ./sql-data-warehouse-overview-develop/
[SQL 資料倉儲整合概觀]: ./sql-data-warehouse-overview-integration/
[建立實驗教學課程]: ./machine-learning-create-experiment/
[Azure 上的機器學習服務簡介]: ./machine-learning-what-is-machine-learning/
[Azure Machine Learning Studio]: https://studio.azureml.net/Home
[Azure 入口網站]: https://portal.azure.com/

<!--MSDN references-->

<!--Other Web references-->
[Azure Machine Learning documentation]: http://azure.microsoft.com/documentation/services/machine-learning/

<!---HONumber=July15_HO1-->