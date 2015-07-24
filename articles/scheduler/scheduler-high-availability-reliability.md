<properties 
 pageTitle="排程器高可用性和可靠性" 
 description="" 
 services="scheduler" 
 documentationCenter=".NET" 
 authors="krisragh" 
 manager="dwrede" 
 editor=""/>
<tags 
 ms.service="scheduler" 
 ms.workload="infrastructure-services" 
 ms.tgt_pltfrm="na" 
 ms.devlang="dotnet" 
 ms.topic="article" 
 ms.date="05/12/2015" 
 ms.author="krisragh"/>
 
 
# 排程器高可用性和可靠性

## Azure 排程器高可用性

做為 Azure 平台服務的核心，Azure 排程器高度可用，並以地理區域備援服務部署和地理區域複寫為其特色。

### 地理區域備援服務部署

今日可在 Azure 的幾乎每個地理區域中透過 UI 使用 Azure 排程器。可以使用 Azure 排程器的區域清單[列於這裡](http://azure.microsoft.com/regions/#services)。如果裝載區域中的資料中心表現出無法使用，則 Azure 排程器的容錯移轉功能可從另一個資料中心使用服務。

### 地理區域工作複寫

Azure 排程器前端不僅可供管理要求使用，您自己的工作也是可以進行地理區域複寫。在某個區域中斷執行時，Azure 排程器會容錯移轉，並確保從配對的地理區域中的另一個資料中心執行工作。

比方說，如果您在美國中南部建立工作，Azure 排程器就會在中北部自動複寫該工作。在美國中南部失敗時，Azure 排程器會確保工作從美國中北部執行。[這裡提供配對的 Azure 區域清單](https://msdn.microsoft.com/library/azure/dn758204.aspx)。

![][1]

如此一來，Azure 排程器確保如果 Azure 失敗，您的資料會保留在相同的更廣泛地理區域中。如此一來，您不需要只為了新增高可用性而重複您的工作 – Azure 排程器會為您的工作自動提供高可用性功能。

## Azure 排程器可靠性

Azure 排程器保證它自己的高可用性，並使用者建立的工作採用不同的方法。例如，您的工作可能會叫用無法使用的 HTTP 端點。Azure 排程器仍會嘗試成功執行您的工作，方法為提供您處理失敗的替代選項。Azure 排程器會以兩種方式執行此項動作：

### 可透過 "retryPolicy" 設定重試原則

Azure 排程器可讓您設定重試原則。根據預設，如果工作失敗，排程器會在 30 秒間隔內重新嘗試工作四次。您可能會將此重試原則重新設定為更積極 (例如，在 30 秒間隔內重試十次) 或鬆散 (例如，每日間隔重複兩次。)

舉例來說，當這樣做有幫助時，您可能會建立一週執行一次，並叫用 HTTP 端點的工作。當您的工作執行時，如果 HTTP 端點已關閉幾個小時，則您可能不想多等待一週，讓工作重新執行，即使預設重試原則失敗也一樣。在這種情況下，您可能會每隔三個小時 (舉例來說) 重新設定要重試的標準重試原則，而不是每隔 30 秒。

若要了解如何設定重試原則，請參閱 [retryPolicy](scheduler-concepts-terms.md#retrypolicy)。

### 可透過 "ErrorAction" 設定替代端點的功能

如果您的 Azure 排程器工作的目標端點仍然無法連線，Azure 排程器會在遵循其重試原則之後回復到替代錯誤處理端點。如果已設定替代錯誤處理端點，Azure 排程器會叫用它。有了替代端點，可在面臨失敗時，高度使用您自己的工作。

舉例來說，在下圖中，Azure 排程器會遵循其重試原則，點閱紐約 Web 服務。重試失敗之後，它會檢查是否有替代項。接著會繼續進行，並開始對使用相同重試原則的替代項提出要求。

![][2]

請注意，相同的重試原則同時適用於原始動作和替代錯誤動作。此外，替代錯誤動作的動作類型也可能與主要動作的動作類型不同。例如，當主要動作可能叫用 HTTP 端點時，而錯誤動作則可能改為進行錯誤記錄的儲存體佇列動作。

若要了解如何設定替代端點，請參閱 [errorAction](scheduler-concepts-terms.md#action-and-erroraction)。

## 另請參閱
 
 [排程器是什麼？](scheduler-intro.md)
 
 [排程器概念、術語及實體階層](scheduler-concepts-terms.md)
 
 [在管理入口網站中開始使用排程器](scheduler-get-started-portal.md)
 
 [Azure 排程器的計劃和計費](scheduler-plans-billing.md)
 
 [如何使用 Azure 排程器建立複雜的排程和進階週期](scheduler-advanced-complexity.md)
 
 [排程器 REST API 參考](https://msdn.microsoft.com/library/dn528946)
 
 [排程器 PowerShell Cmdlet 參考](scheduler-powershell-reference.md)
 
 [排程器限制、預設值和錯誤碼](scheduler-limits-defaults-errors.md)
 
 [排程器輸出驗證](scheduler-outbound-authentication.md)
 
 
[1]: ./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image1.png

[2]: ./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png

 

<!---HONumber=July15_HO2-->