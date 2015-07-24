<properties 
	pageTitle="Azure Stream Analytics 版本資訊 |Azure" 
	description="Azure Stream Analytics 版本資訊" 
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="07/09/2015" 
	ms.author="jeffstok"/>

#Microsoft 串流分析版本資訊

## 串流分析 2015/07/09 版本的注意事項 ##

此版本包含下列更新。

<table border="1">
<tr>
<th>課程名稱</th>
<th>說明</th>
</tr>

<tr>
<td>自訂 Blob 輸出資料分割</td>
<td>Blob 儲存體輸出現在允許指定寫入輸出 Blob 的頻率，以及輸出資料路徑資料夾結構的結構和格式的選項。</td>
</tr>
</table>

## 串流分析 2015/05/03 版本的注意事項 ##

此版本包含下列更新。

<table border="1">
<tr>
<th>課程名稱</th>
<th>說明</th>
</tr>

<tr>
<td>增加次序錯誤允許的時間範圍的最大值</td>
<td>次序錯誤允許的時間範圍的大小上限現在是 59:59 (MM:SS)</td>
</tr>

<tr>
<td>JSON 輸出格式：以行分隔或陣列</td>
<td>現在當輸出至 Blob 儲存體或事件中心時，還有另一個選擇，就是以 JSON 物件的陣列輸出，或透過以新行分隔 JSON 物件來輸出。</td>
</tr>
</table>

## 串流分析 2015/04/16 版本的注意事項 ##

<table border="1">
<tr>
<th>課程名稱</th>
<th>說明</th>
</tr>

<tr>
<td>Azure 儲存體帳戶組態的延遲</td>
<td>第一次在某個區域中建立 Stream Analytics 工作時，系統會提示您建立新的儲存體帳戶或指定現有帳戶，以監視該區域中的 Stream Analytics 工作。由於設定監視作業會有所延遲，若在 30 分鐘內於相同區域中建立另一個 Stream Analytics 工作，系統將提示您指定第二個儲存體帳戶，而不會在 [監視儲存體帳戶] 下拉式清單中顯示最近設定的帳戶。若要避免建立不必要的儲存體帳戶，請在第一次於某地區中建立工作的 30 分鐘後，再於該區域中佈建其他工作。</td>
</tr>

<tr>
<td>工作升級</td>
<td>Stream Analytics 目前無法對執行中工作的定義或組態進行即時編輯。若要變更執行中工作的輸入、輸出、查詢、調整或組態，您必須先停止工作。</td>
</tr>

<tr>
<td>從輸入來源推斷而來的資料類型</td>
<td>如果未使用 CREATE TABLE 陳述式，系統會從輸入格式衍生輸入類型，例如 CSV 的所有欄位都是字串。必須使用 CAST 函數，將欄位明確轉換成正確的類型，以避免發生類型不一致錯誤。</td>
</tr>

<tr>
<td>遺漏的欄位在輸出後會變成 null 值</td>
<td>參考的欄位如果在輸入來源中找不到，將會在輸出事件中產生 null 值。</td>
</tr>

<tr>
<td>WITH 陳述式前面必須是 SELECT 陳述式</td>
<td>在您的查詢中，SELECT 陳述式後面必須是 WITH 陳述式中定義的下列子查詢。</td>
</tr>

<tr>
<td>記憶體不足問題</td>
<td>Stream Analytics 工作如果可以容忍大量的失控事件和/或存在大量狀態的複雜查詢，可能會導致工作耗盡記憶體，進而造成工作重新開始。啟動作業和停止作業會顯示在工作的作業記錄中。若要避免此行為，請將查詢橫向擴充至多個資料分割。在未來版本中，將會藉由降低受影響之工作的效能 (而不是加以重新啟動)，來解決這項限制。</td>
</tr>

<tr>
<td>沒有裝載時間戳記的大型 Blob 輸入，可能會造成記憶體不足問題</td>
<td>Blob 儲存體用於大量的檔案時，如果未透過 TIMESTAMP BY 指定時間戳記欄位，則可能會造 Stream Analytics 工作崩潰。要避免此問題，請讓每個 Blob 的大小保持在 10 MB 以下。</td>
</tr>

<tr>
<td>SQL 資料庫事件容量限制</td>
<td>使用 SQL 資料庫做為輸出來源時，非常大量的輸出資料可能會導致 Stream Analytics 工作無法在限定的時間內完成。要解決這個問題，請使用彙總或篩選運算子減少輸出量，或改以 Azure Blob 儲存體或事件中樞做為輸出來源。</td>
</tr>

<tr>
<td>PowerBI 資料集只能包含一個資料表</td>
<td>PowerBI 不允許指定的資料集中存在多個資料表。</td>
</tr>
</table>

## 取得說明
如需進一步的協助，請參閱我們的 [Azure Stream Analytics 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## 後續步驟

- [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
- [開始使用 Azure Stream Analytics](../stream.analytics.get.started.md)
- [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics 管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 

<!---HONumber=July15_HO2-->