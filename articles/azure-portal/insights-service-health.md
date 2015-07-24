<properties 
	pageTitle="追蹤服務健全狀況" 
	description="在 Azure 發生效能降低或服務中斷時獲知消息。" 
	authors="stepsic-microsoft-com" 
	manager="kamrani" 
	editor="" 
	services="azure-portal" 
	documentationCenter="na"/>

<tags 
	ms.service="azure-portal" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/25/2015" 
	ms.author="stepsic"/>

# 追蹤服務健全狀況

每次發生服務中斷或效能降低，Azure 就會發出公告。您可以在 Azure 入口網站中瀏覽這些事件，您也可以使用 [REST API](https://msdn.microsoft.com/library/azure/dn931927.aspx) 或 [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) 以程式控制的方式存取完整的事件集。

## 依地區查看服務健全狀況

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 在 [**首頁**]，您會看到名為 [**服務健全狀況**] 的磚![Home](./media/insights-service-health/Insights_Home.png)

3. 按一下該磚，會顯示 Azure 中所有地區的清單。您可以按一下任何地區，查看該地區的服務健全狀況記錄。![Home](./media/insights-service-health/Insights_Regions.png)

4. 您也可以按一下表格中的個別事件，查看該事件的詳細資料。

## 瀏覽完整的服務健全狀況記錄檔

2. 按一下 [**瀏覽**]，然後選取 [**稽核記錄檔**]。![Browse Hub](./media/insights-service-health/Insights_Browse.png)

3. 這將會開啟一個刀鋒視窗，顯示過去 7 天影響任何訂用帳戶的所有事件。服務健全狀況項目會顯示在此清單中，但是因為清單內容非常多，所以您可能很難找到清單需要的事件。

4. 按一下 [**篩選**] 命令。

5. 選取 [**事件類別**]，然後選擇 [**服務健全狀況**]：![所有事件](./media/insights-service-health/Insights_Filter.png)

6. 按一下 [**已更新**]。

7. 您即可查看所有對您的訂用帳戶造成影響的服務健全狀況事件：![Resource groups](./media/insights-service-health/Insights_HealthEvent.png)

8. 您可以從這裡移至 [詳細資料] 刀鋒視窗，以查看事件的細節。
   
## 後續步驟

* 每當事件發生時[接收警示通知](insights-receive-alert-notifications.md)。
* [監視服務計量](insights-how-to-customize-monitoring.md)以確保您的服務可用且可回應。
* 使用 Application Insights [監視任何網頁的可用性和回應性](../app-insights-monitor-web-app-availability.md)，讓您可以找出您的頁面是否關閉。
 

<!---HONumber=62-->