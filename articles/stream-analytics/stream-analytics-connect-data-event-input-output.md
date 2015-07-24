<properties 
	pageTitle="連接輸入和輸出 | Microsoft Azure" 
	description="了解如何連接以及設定輸入來源和輸出目標，以制定 Stream Analytics 解決方案。" 
	documentationCenter="" 
	services="stream-analytics"
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="04/24/2015" 
	ms.author="jeffstok"/>

#連接輸入和輸出
本文件會教您各種輸入來源和輸出目標的設定方法，以制定不同的 Stream Analytics 解決方案。

##使用 SQL 做為輸出

如果資料的本質上具備關聯性，或者應用程式所需的內容需由資料庫提供時，適合使用 Azure SQL 資料庫來提供資料。

[http://azure.microsoft.com/services/sql-database/](http://azure.microsoft.com/services/sql-database/)

要開始使用 Azure SQL 資料庫，您應該具備以下的資料庫相關資訊：

1. 伺服器名稱。
2. 資料庫名稱。
3. 有效的使用者名稱/密碼組合。
4. 輸出資料表名稱。

![graphic1][graphic1]

請移至工作的輸出，然後依序按一下 [新增輸出] 命令和 [下一步]。

![graphic2][graphic2]


選擇 [SQL 資料庫] 做為您的輸出。

![graphic3][graphic3]

在下一個頁面上輸入資料庫資訊。輸出別名在查詢中的用途就是要將查詢輸出指向這個資料庫。如果您有一個輸出，預設別名就是 "output"。

![graphic4][graphic4]

如果使用的資料庫就是相同訂閱中使用的資料庫，您可以選取 [從目前的訂閱使用 SQL 資料庫]，然後從下拉式清單中選取用資料庫。

![graphic5][graphic5]

要新增此輸出，請按 [下一步]。您應該會看到系統進行兩項操作，第一個是新增輸出。

![graphic6][graphic6]

第二個操作是測試連線。Azure Stream Analytics 會嘗試連接至 SQL 資料庫，然後檢查輸入的認證是否正確、是否可以調閱資料表。完成後，您應該會看到兩則訊息其中之一。

![graphic7][graphic7]

![graphic8][graphic8]



如果這是後者，按一下 [詳細資料] ，就可以看到完整的錯誤詳細資料。

![graphic9][graphic9]

在這個範例中，提供的認證不正確。您可以更正認證，然後按一下 [測試連線] 按鈕，重新測試。

![graphic10][graphic10]

##使用事件中樞

###概觀
 
事件中樞可以擷取大量的事件，是最常見的 Stream Analytics 資料入口。它們可以收集各種裝置和服務的事件資料流。事件中樞搭配 Stream Analytics 之後，可以為客戶提供一種端點對端點的方案來分析即時資料 -- 用戶可以透過事件中樞即時將事件傳送到 Azure，然後 Stream Analytics 工作就會立即處理事件無時差。例如，用戶可以將網頁點選次數、感應器數據、線上記錄事件發佈到事件中樞，然後建立各種 Azure Stream Analytics 工作，讓這些工作將事件中樞當做輸入資料流來進行資料的即時篩選、彙總以及聯給。事件中樞也可以當做資料出口。要將 EH 做為輸出，最常見的例子就是 Stream Analytics 工作的輸出是另一個資料流工作的輸入。

###用戶群組
每一個 Azure Stream Analytics 工作都應該設定自己的事件中樞用戶群組。當工作包含自我聯結或多個輸出時，某些輸出就有可能供一個以上的讀取器使用，這樣會造成單個用戶群組中的讀取器總數，超過事件中樞規定的每一個用戶群組最多 5 個讀取器。在這種情況下，就需要將查詢細分成多個查詢並透過其他事件中樞，傳遞中繼結果。請注意，每一個事件中樞另外還有 20 個用戶群組的限制。如需詳細資訊，請參閱事件中心開發人員指南。

 
###參數
 
用戶設定事件中樞資料流時，還需要設定幾個參數。除非另有其他規定，否則這些參數會套用至事件中樞的輸入和輸出資料流。

1. 服務匯流排命名空間：事件中樞的服務匯流排命名空間。服務匯流排命名空間是一個容器，包含一組訊息實體。建立新的事件中樞時，也會建立服務匯流排命名空間。 
2. 事件中樞名稱：事件中樞的名稱。在您建立新事件中樞時所指定的名稱。 
3. 事件中樞原則名稱：共用存取原則 (用於存取事件中樞) 的名稱。您可以在 [設定] 索引標籤上，設定事件中樞的共用存取原則。每一個共用存取原則會有名稱、權限 (由您設定) 和存取金鑰。
4. 事件中樞原則索引鍵：共用存取原則 (用於存取事件中樞) 的主要或次要索引鍵。  
5. 事件中樞用戶群組：事件中樞輸入參數 (選擇性)。負責從事件中樞擷取資料的用戶群組。如果未指定，Stream Analytics 工作使用預設用戶群組，然後從事件中樞擷取資料。我們建議您每一個 Stream Analytics 工作使用不同的用戶群組。

資料分割索引鍵資料行： 事件中樞的輸出參數 (選擇性)。資料屬性資料行，是事件中樞輸出的資料分割索引鍵。

##使用 Azure 資料表輸出

使用者可以利用 Azure 資料表來處理結構描述較少限制的結構化資料。使用 Azure 資料表儲存資料時，資料可長期儲存而且調閱方便。如需詳細資訊，請參閱 [Azure 儲存體簡介](http://azure.microsoft.com/storage-introduction/)。
 
開始使用 Azure Table Storage 前，請準備好以下的資料表相關資訊：

1. 儲存體帳戶名稱 (如果這個儲存體與串流工作中的訂閱不同)。
2. 儲存體帳戶索引鍵 (如果這個儲存體與串流工作中的訂閱不同)。
3. 輸出資料表名稱 (如果沒有，我們會建立這個名稱)。
4. 資料分割索引鍵 (必填)。
5.   資料列索引鍵 (根據用戶意見反應，目前是必填的，我們打算將其變更為選擇性)

為了設計更好的資料分割索引鍵和資料列索引鍵，請參閱下列文章[為 Azure Table Storage 設計可擴充的資料分割策略](https://msdn.microsoft.com/library/azure/hh508997.aspx)。


![graphic11][graphic11]


請移至工作的輸出，然後依序按一下 [新增輸出] 命令和 [下一步]。


![graphic12][graphic12]


選擇「資料表儲存體」做為輸出。


![graphic13][graphic13]


在下一個頁面上輸入 Azure 資料表資訊。輸出別名在查詢中的用途就是要將查詢輸出指向這個資料表。


![graphic14][graphic14]
![graphic15][graphic15]

「批次大小」是指批次作業的記錄個數，如果不熟悉，請保留預設值或者參考 [https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx) 以取得詳細資訊。


如果您要使用相同訂閱中的正在使用的 Azure 儲存體，您可以選取 [從目前的訂閱使用儲存體帳戶]，然後從下拉式清單中選取儲存體帳戶。

要新增此輸出，請按 [下一步]。您應該會看到系統進行兩項操作，第一個是新增輸出。

![graphic16][graphic16]

第二個操作是測試連線。Azure Stream Analytics 會嘗試連接至儲存體帳戶，然後檢查輸入的認證是否正確。完成後，您應該會看到兩則訊息其中之一。

![graphic17][graphic17]

如果這是後者，按一下 [詳細資料] ，就可以看到完整的錯誤詳細資料。

![graphic18][graphic18]

在這個範例中，提供的認證不正確。您可以更正認證，然後按一下 [測試連線] 按鈕，重新測試。

![graphic19][graphic19]

## 取得說明
如需進一步的協助，請參閱我們的 [Azure Stream Analytics 論壇](https://social.msdn.microsoft.com/Forums/zh-tw/home?forum=AzureStreamAnalytics)

## 後續步驟

- [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
- [開始使用 Azure Stream Analytics](stream-analytics-get-started.md)
- [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)




[graphic1]: ./media/stream-analytics-connect-data-event-input-output/1-stream-analytics-connect-data-event-input-output.png
[graphic2]: ./media/stream-analytics-connect-data-event-input-output/2-stream-analytics-connect-data-event-input-output.png
[graphic3]: ./media/stream-analytics-connect-data-event-input-output/3-stream-analytics-connect-data-event-input-output.png
[graphic4]: ./media/stream-analytics-connect-data-event-input-output/4-stream-analytics-connect-data-event-input-output.png
[graphic5]: ./media/stream-analytics-connect-data-event-input-output/5-stream-analytics-connect-data-event-input-output.png
[graphic6]: ./media/stream-analytics-connect-data-event-input-output/6-stream-analytics-connect-data-event-input-output.png
[graphic7]: ./media/stream-analytics-connect-data-event-input-output/7-stream-analytics-connect-data-event-input-output.png
[graphic8]: ./media/stream-analytics-connect-data-event-input-output/8-stream-analytics-connect-data-event-input-output.png
[graphic9]: ./media/stream-analytics-connect-data-event-input-output/9-stream-analytics-connect-data-event-input-output.png
[graphic10]: ./media/stream-analytics-connect-data-event-input-output/10-stream-analytics-connect-data-event-input-output.png
[graphic11]: ./media/stream-analytics-connect-data-event-input-output/11-stream-analytics-connect-data-event-input-output.png
[graphic12]: ./media/stream-analytics-connect-data-event-input-output/12-stream-analytics-connect-data-event-input-output.png
[graphic13]: ./media/stream-analytics-connect-data-event-input-output/13-stream-analytics-connect-data-event-input-output.png
[graphic14]: ./media/stream-analytics-connect-data-event-input-output/14-stream-analytics-connect-data-event-input-output.png
[graphic15]: ./media/stream-analytics-connect-data-event-input-output/15-stream-analytics-connect-data-event-input-output.png
[graphic16]: ./media/stream-analytics-connect-data-event-input-output/16-stream-analytics-connect-data-event-input-output.png
[graphic17]: ./media/stream-analytics-connect-data-event-input-output/17-stream-analytics-connect-data-event-input-output.png
[graphic18]: ./media/stream-analytics-connect-data-event-input-output/18-stream-analytics-connect-data-event-input-output.png
[graphic19]: ./media/stream-analytics-connect-data-event-input-output/19-stream-analytics-connect-data-event-input-output.png
 

<!---HONumber=62-->