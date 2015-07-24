<properties
 pageTitle="排程器是什麼？"
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
 ms.topic="get-started-article" 
 ms.date="05/12/2015"
 ms.author="krisragh"/>

# 排程器是什麼？

Azure 排程器可讓您以宣告方式描述在雲端中執行的動作。然後它會排程這些動作並且自動執行。Azure 排程器會使用 [Azure 入口網站](scheduler-get-started-portal.md)、程式碼、[REST API](https://msdn.microsoft.com/library/dn528946) 或 PowerShell 來完成這個動作。

Azure 排程器會維護、管理、排程，以及叫用已排程的工作。Azure 排程器不會裝載任何工作負載或執行任何程式碼。它只會_叫用_其他位置裝載的程式碼；此程式碼可能會裝載於 Azure 、內部部署或另一個提供者。它會透過 HTTP、HTTPS 或儲存體佇列叫用。

Azure 排程器會排程工作，保留使用者可以詢問的工作執行結果的歷程記錄，並且決定性地和可靠地排程要執行的工作負載。Azure 行動服務排程指令碼、Azure Web Apps WebJob，以及其他 Azure 排程功能在幕後使用 Azure 排程器。[排程器 REST API](https://msdn.microsoft.com/library/dn528946) 會協助管理這些動作的通訊。因此，排程器支援[複雜的排程和進階週期](scheduler-advanced-complexity.md)。

有數個案例會將本身劃分為 Azure 排程器的使用量。例如：

+ _週期性應用程式動作_：定期蒐集 Twitter 的資料並且將資料蒐集至意見反應。
+ _每日維護_：每日剪除記錄檔、執行備份及其他維護工作。例如，系統管理員選擇在接下來的 9 個月每天的上午 1 點備份資料庫。

排程器可讓您使用指令碼，在入口網站中以程式設計方式建立、更新、刪除、檢視及管理[「工作集合」和「工作」](scheduler-concepts-terms.md)。

## 另請參閱

 [排程器概念、術語及實體階層](scheduler-concepts-terms.md)

 [在管理入口網站中開始使用排程器](scheduler-get-started-portal.md)

 [Azure 排程器的計劃和計費](scheduler-plans-billing.md)

 [如何使用 Azure 排程器建立複雜的排程和進階週期](scheduler-advanced-complexity.md)

 [排程器 REST API 參考](https://msdn.microsoft.com/library/dn528946)

 [排程器 PowerShell Cmdlet 參考](scheduler-powershell-reference.md)

 [排程器高可用性和可靠性](scheduler-high-availability-reliability.md)

 [排程器限制、預設值和錯誤碼](scheduler-limits-defaults-errors.md)

 [排程器輸出驗證](scheduler-outbound-authentication.md)
 

<!---HONumber=July15_HO2-->