<properties 
	pageTitle="有效率的清單查詢" 
	description="了解如何減少傳回清單項目的數目，以及減少每個項目傳回的資訊量。" 
	services="batch" 
	documentationCenter="" 
	authors="davidmu1" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="vm-windows" 
	ms.workload="multiple"
	ms.date="05/09/2015" 
	ms.author="davidmu"/>

# 有效率的清單查詢

下列方法是每個使用 Azure 批次應用程式皆必須執行的作業範例，而且通常必須經常執行：

- [ListTasks](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.iworkitemmanager.listtasks.aspx)
- [ListJobs](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.iworkitemmanager.listjobs.aspx)
- [ListWorkitems](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.iworkitemmanager.listworkitems.aspx)
- [ListPools](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.ipoolmanager.listpools.aspx)
- [ListCertificates](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.icertificatemanager.listcertificates.aspx)

監視是常見的使用狀況；例如，決定集區的容量和狀態將需要查詢所有集區的 VM。另一個範例是查詢一個工作的作業，以判斷是否有任何仍在佇列中的作業。在某些情況下必須有一組豐富的資料，但在其他情況下則只需所有項目的計數或某個特定狀態中的項目。

請務必了解可以傳回的項目數目可能非常龐大，而代表項目清單的所需資料大小也可能非常龐大。只要簡單地查詢許多會有龐大回應結果的項目，就有可能導致一些問題發生：

- 批次 API 的回應時間可能會非常緩慢。批次服務查詢項目的數目越多，所需的查詢時間就越久。龐大數目的項目必須分割成許多區塊，因此用戶端程式庫可能需要對服務呼叫多個服務 API，以取得一份清單的所有項目。
- 由應用程式呼叫的批次 API 處理程序需要處理的項目越多，則所需花費的時間就越久。
- 越多的項目和/或越大的項目需要處理，則應用程式所呼叫的批次將耗用更多的記憶體。
- 越多的項目和/或越大的項目將導致網路流量增加。這將花費更多時間傳送，並依據應用程式架構，資料傳送到批次帳戶區域外將可能導致網路費用增加。

批次 API 提供可同時減少傳回清單項目的數目，以及減少每個項目傳回資訊量的功能。類型的一個參數 [DetailLevel](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.detaillevel.aspx) 可以指定清單作業。DetailLevel 是一個抽象的基底類別，並且實際上需要建立及通過一個 [ODATADetailLevel](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx) 物件以作為參數。

下列適用於所有的 API：

- 每個屬性名稱是一個字串，並對應至物件的屬性
- 所有屬性名稱都會區分大小寫，但屬性值不會區分大小寫
- 日期/時間字串可以使用兩種格式之一，並需要在前面加上 DateTime
	- W3CDTF (例如 creationTime gt DateTime’2011-05-08T08:49:37Z’)
	- RFC1123 (例如 creationTime gt DateTime’Sun, 08 May 2011 08:49:37 GMT’)
- 布林值的字串為 "true" 或 "false"
- 如果指定一個無效的屬性或運算子，則將會建立例外狀況及一個「400 (錯誤的要求)」的內部例外狀況。
- 使用 Select 和 Expand 子句的 DetailLevel 參數也能通過適當的 "Get" 方法。例如 IPoolManager.GetPool()

ODataDetailLevel 物件有三個公用屬性，可以在建構函式中指定或是直接設定。三個屬性皆為字串：

- [FilterClause](#filter) – 篩選並且可能減少傳回項目的數目
- [SelectClause](#select) – 指定傳回的特定屬性值，並減少項目和回應的大小
- [ExpandClause](#expand) – 一次呼叫傳回所有的必要資料，而非多次呼叫

### <a id="filter"></a> FilterClause

篩選字串可減少傳回項目的數目。可以指定一個或多個屬性值，以確保只會傳回必要的項目。例如，只列出作用中的工作項目、只列出工作中正在執行的作業，及只列出可執行作業的 VM。

[FilterClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx) 是包含一個或多個運算式的字串，其運算式包含屬性名稱、運算子和值。每個屬性將由運算子支援，而可以指定的屬性將指定給每個 API 呼叫。多個運算式可以由邏輯運算子 "and" 和 "or" 結合。

例如，列出作業的篩選可以是：

	startswith(name, 'MyTask') and (state eq 'Running')

### <a id="select"></a> SelectClause

每個項目傳回的屬性值可以藉由使用選取字串限制。可以指定項目的屬性清單，然後只傳回那些屬性值。

[SelectClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx) 是由一個屬性名稱清單所組成的字串，其中屬性名稱以逗號分隔。可以指定所有清單傳回項目中的屬性。

	"name, state, stateTransitionTime"

### <a id="expand"></a> ExpandClause

擴充字串可以降低 API 呼叫的數目。用一個 API 所呼叫的清單，就可以取得每一個清單項目的詳細資訊，相對應於取得清單後，再為每一個清單項目進行呼叫。

[ExpandClause](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx) 與 Select 子句類似，Expand 子句可控制是否在結果中傳回特定資料。Expand 子句只支援工作項目清單、作業清單、集區清單和工作清單。目前僅支援統計資料資訊。當需要所有屬性卻沒有 Select 子句時，則必須用 Expand 子句以取得統計資料資訊。如果使用 Select 子句取得屬性的子集，那麼統計資料可以在 Select 子句中指定，而 Expand 子句就可以為 null。

> [AZURE.NOTE]建議您一律為清單 API 呼叫使用篩選和 Select 子句，以確保您的應用程式有最高效率和最佳效能。

<!---HONumber=62-->