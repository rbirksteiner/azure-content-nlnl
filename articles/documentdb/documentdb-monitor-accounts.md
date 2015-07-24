<properties 
	pageTitle="監視 DocumentDB 帳戶 | Azure" 
	description="了解如何監視 DocumentDB 帳戶的效能度量 (如要求和伺服器錯誤) 和使用方式度量 (如儲存體耗用量)。" 
	services="documentdb" 
	documentationCenter="" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="mimig"/>

#監視 DocumentDB 帳戶  

您可以在 [Microsoft Azure Preview 入口網站](https://portal.azure.com/)中監視 DocumentDB 帳戶。每一個 DocumentDB 帳戶都有效能度量 (例如要求和伺服器錯誤) 和使用量度量 (例如儲存體用量) 可供使用。

## <a id="metrics"></a> 作法：檢視 DocumentDB 帳戶的效能度量
1.	在 [Azure Preview 入口網站](https://portal.azure.com/)中，按一下 [**瀏覽**]、[**DocumentDB 帳戶**]，然後按一下您想要檢視效能度量的 DocumentDB 帳戶名稱。
2.	在 [監視] 透鏡內，依預設可以看到：
	*	當日的要求總數。
	*	當日的每秒平均要求數 
	
	![[監視] 透鏡的螢幕擷取畫面](./media/documentdb-monitor-accounts/madocdb1.png)


3.	按一下 [**要求總數**] 或 [**每秒平均要求數**] 組件會開啟詳細的 [**度量**] 刀鋒視窗。
4.	[**度量**] 刀鋒視窗會顯示您已選取之度量的詳細資料。刀鋒視窗上方是圖形，圖形下方有一個資料表顯示所選度量的彙總值，例如平均值、最小值和最大值。度量刀鋒視窗也會顯示已定義的警示清單，且依目前度量刀鋒視窗上出現的度量來篩選 (因此，如果您有許多警示，只會看到此處顯示相關的警示)。   

	![[度量] 刀鋒視窗的螢幕擷取畫面](./media/documentdb-monitor-accounts/madocdb2.png)


## <a id="custom"></a>自訂 DocumentDB 帳戶的效能度量檢視

1.	若要自訂特定組件中顯示的度量，請以滑鼠右鍵按一下度量圖表，然後選取 [**編輯圖表**]。![包含反白顯示 [編輯圖表] 按鈕的 [要求總數] 圖表螢幕擷取畫面](./media/documentdb-monitor-accounts/madocdb3.png)

2.	在 [**編輯圖表**] 刀鋒視窗上，有選項可修改組件中顯示的度量及其時間範圍。![[編輯圖表] 刀鋒視窗的螢幕擷取畫面](./media/documentdb-monitor-accounts/madocdb4.png)

3.	若要變更組件中顯示的度量，只需選取或清除可用的效能度量，然後在刀鋒視窗底部按一下 [**儲存**]。
4.	若要變更時間範圍，請選擇不同的範圍 (例如，**過去一個小時**)，然後按一下刀鋒視窗底部的 [**儲存**]。  

	![[編輯圖表] 刀鋒視窗的 [時間範圍] 部分螢幕擷取畫面，顯示如何輸入自訂時間範圍](./media/documentdb-monitor-accounts/madocdb5.png)


## <a id="create"></a>建立並排效能度量圖表
Azure Preview 入口網站可讓您建立並排度量圖表。

1.	首先，在您要複製並修改的圖表上按一下滑鼠右鍵，然後選取 [**自訂**]。 

	![包含反白顯示 [自訂] 選項的 [要求總數] 圖表螢幕擷取畫面](./media/documentdb-monitor-accounts/madocdb6.png)

2.	在功能表上按一下 [**複製**] 以複製組件，然後按一下 [**完成自訂**]。

	![包含反白顯示 [複製] 與 [完成] 自訂選項的 [要求總數] 圖表螢幕擷取畫面](./media/documentdb-monitor-accounts/madocdb7.png)


現在，您可以將此組件視為其他任何度量組件，並自訂該組件中顯示的度量和時間範圍。如此一來，您可以同時看到兩個不同的度量圖表並排出現。![[要求總數] 圖表和全新 [要求總數] 前一個小時圖表的螢幕擷取畫面](./media/documentdb-monitor-accounts/madocdb8.png)

## <a id="view"></a>檢視 DocumentDB 帳戶的使用量度量
1.	在 [Azure Preview 入口網站](https://portal.azure.com/)中，按一下 [**瀏覽**]，再按一下 [**DocumentDB 帳戶**]，然後按一下您想要查看使用量度量的 DocumentDB 帳戶名稱。
2.	在 [**使用量**] 透鏡內，依預設可以檢視下列：
	*	估計的成本。
	*	帳戶內耗用的儲存體。
	*	帳戶可用的儲存體上限。
	*	使用者和權限使用量。
	*	附件使用量。

	![[使用量] 透鏡的螢幕擷取畫面](./media/documentdb-monitor-accounts/madocdb9.png)
 
## <a id="setup"></a>設定 DocumentDB 帳戶的效能度量警示
1.	在 [Azure Preview 入口網站](https://portal.azure.com/)中，按一下 [**瀏覽**]，再按一下 [**DocumentDB 帳戶**]，然後按一下您想要設定效能度量警示的 DocumentDB 帳戶名稱。
2.	在 [**作業**] 透鏡內，按一下 [**警示規則**] 組件。![已選取警示規則組件的 [作業] 透鏡螢幕擷取畫面](./media/documentdb-monitor-accounts/madocdb10.png)

3.	在 [警示規則] 刀鋒視窗中，按一下 [**新增警示**]。![包含反白顯示 [新增警示] 按鈕的 [警示規則] 刀鋒視窗螢幕擷取畫面](./media/documentdb-monitor-accounts/madocdb11.png)

4.	在 [Add an alert rule] 刀鋒視窗中，指定：
	*	您設定之警示規則的名稱。
	*	新警示規則的描述。
	*	警示規則的度量。
	*	決定警示何時啟動的條件、臨界值和期間。例如，過去 15 分鐘伺服器錯誤計算大於 5。
	*	警示引發時是否傳送電子郵件給服務管理員和共同管理員。
	*	警示通知的其他電子郵件地址。![[新增警示規則] 刀鋒視窗的螢幕擷取畫面](./media/documentdb-monitor-accounts/madocdb12.png)

 
## <a id="next"></a>接續步驟
若要深入了解 DocumentDB 容量，請參閱[管理 DocumentDB 容量](documentdb-manage.md)。

<!--Anchors-->
[How to view performance metrics for a DocumentDB account]: #How-to-view-performance-metrics-for-a-DocumentDB-account
[Customize performance metric views for a DocumentDB account]: #Customize-performance-metric-views-for-a-DocumentDB-account
[How to create side-by-side performance metric charts]: #How-to-create-side-by-side-performance-metric-charts
[How to view usage metrics for a DocumentDB account]: #How-to-view-usage-metrics-for-a-DocumentDB-account
[How to setup performance metric alerts for a DocumentDB account]: #How-to-setup-performance-metric-alerts-for-a-DocumentDB-account
[Next steps]: #Next-steps
 

<!---HONumber=July15_HO1-->