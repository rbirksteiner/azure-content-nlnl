<properties 
	pageTitle="在 Azure App Service 中調整定價層" 
	description="了解如何在 Azure App Service 中調整 Web、行動、API 和邏輯應用程式，包括自動調整。" 
	services="app-service" 
	documentationCenter="" 
	authors="stepsic-microsoft-com" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/25/2015" 
	ms.author="stepsic"/>

# 在 Azure App Service 中調整定價層

若要在 Microsoft Azure 上提高應用程式的效能和輸送量，可以使用 [Azure 入口網站](https://portal.azure.com/)，將您的 App Service 方案從 [**免費**] 調整為 [**共用**]、[**基本**]、[**標準**] 或 [**高階**]。

App Service 方案的服務層級是以[方案的*定價層*](/pricing/details/app-service/) 為基礎。較高的定價層 (如 [**標準**] 和 [**高階**]) 能讓您以更健全、更彈性的方式決定 Azure 中的資源使用情況。變更定價層會影響您的服務具有的核心數和記憶體量，這就稱為*相應增加* (或*相應減少*)。

除了相應增加定價層以外，也可以增加您的服務具有的執行個體數目。這就稱為*相應放大* 或*相應縮小*。請參閱[自動或手動調整執行個體計數](../insights-how-to-scale.md)，以深入了解*相應放大* 和*相應縮小*。

如需 App Service 方案的相關資訊，請參閱[什麼是 App Service 方案？](../web-sites-web-hosting-plan-overview.md)和 [Azure App Service 方案深入概觀](azure-web-sites-web-hosting-plans-in-depth-overview.md)。若想了解個別 App Service 方案的定價資訊及功能，請參閱 [App Service 定價詳細資料](/pricing/details/app-service/)。

> [AZURE.NOTE]從 [**免費**] 模式切換為 [**基本**]、[**標準**] 或 [**高階**] 模式之前，您必須先適當地移除 Azure App Service 訂用帳戶的支出費用上限。若要檢視或變更 Microsoft Azure App Service 訂用帳戶的選項，請參閱 [Microsoft Azure 訂用帳戶][azuresubscriptions]。

<a name="scalingsharedorbasic"></a> <a name="scalingstandard"></a>

## 調整定價層

1. 在瀏覽器中，開啟 [Azure 入口網站][portal]並瀏覽至您要調整的應用程式。
	
2. 在應用程式的 [**Essentials**] 中，按一下 [**App Service 方案/定價層**] 連結。

3. 按一下 [**定價**]，然後您會看見方案的可能服務層級清單。每一層都附有估價，以便您大致了解該層的平均成本。
	
	![選擇方案](./media/app-service-scale/ChoosePricingTier.png)
	
4. 選擇定價層後，請按一下 [**選取**]。
	
	當作業完成時，[**通知**] 索引標籤會有綠色的 **成功**字樣閃爍顯示。
		
<a name="ScalingSQLServer"></a>
##調整相關資源
如果您的應用程式相依於其他服務 (如 SQL 或儲存體)，您也可以根據需求調整這些服務。

1. 在 [**Essentials**] 中，按一下 [**資源群組**] 連結。

2. 然後，在資源群組刀鋒視窗的 [**摘要**] 部件中，按一下其中一個的資料庫 (或您要調整的任何其他資源)。

	![連結的資料庫](./media/app-service-scale/ResourceGroup.png)
	
3. 在該連結的資源刀鋒視窗中，按一下 [**定價層**] 部件，根據您的效能需求選取其中一層，然後按一下 [**選取**]。
	
	![調整您的 SQL Database](./media/app-service-scale/ScaleDatabase.png)
	
4. 如果您的應用程式使用儲存體，則異地複寫會在您選擇支援異地複寫的定價層時自動設定。另一方面，對於 SQL，您需要手動設定異地複寫，以增加 SQL 資料庫的高可用性和災害復原功能。若要這樣做，請按一下 [**異地複寫**] 部件。
	
	![針對 SQL Database 設定異地複寫](./media/app-service-scale/GeoReplication.png)
	
<a name="devfeatures"></a>
## 開發人員功能
視定價層而定，可使用下列以開發人員為導向的功能：

### 位元 ###

- [**基本**]、[**標準**] 及 [**高階**] 層支援 64 位元和 32 位元的應用程式。
- [**免費**] 和 [**共用**] 方案層僅支援 32 位元的應用程式。

### 偵錯工具支援 ###

- 在每個 App Service 方案 1 個同時連線的情況下，偵錯工具支援適用於 [免費]、[共用] 及 [基本] 模式。
- 在每個 App Service 方案有 5 個同時連線的情況下，偵錯工具支援適用於 [標準] 和 [高階] 模式。

<a name="OtherFeatures"></a>
## 其他功能

- 如需應用程式服務計劃其他所有功能的詳細資訊，包括所有使用者 (包括開發人員) 關心的定價和功能，請參閱[App Service 定價詳細資料](/pricing/details/web-sites/)。

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期入門 Web 應用程式。不需要信用卡；沒有承諾。

<a name="Next Steps"></a>
## 後續步驟

- 若要開始使用 Azure，請參閱 [Microsoft Azure 免費試用](/pricing/free-trial/)。
- 如需定價、支援及 SLA 的相關資訊，請參閱以下連結：
	
	[資料傳輸定價詳細資料](/pricing/details/data-transfers/)
	
	[Microsoft Azure 支援方案](/support/plans/)
	
	[服務等級協定](/support/legal/sla/)
	
	[SQL Database 定價詳細資料](/pricing/details/sql-database/)
	
	[Windows Azure 的虛擬機器和雲端服務大小][vmsizes]
	
	[App Service 定價詳細資料](/pricing/details/app-service/)
	
	[App Service 定價詳細資料 - SSL 連線](/pricing/details/web-sites/#ssl-connections)

- 如需 Azure App Service 最佳作法 (包括建置可調整且具彈性的架構) 的詳細資訊，請參閱[最佳作法：Azure App Service Web Apps](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx)。

- 調整 Web Apps 規模的相關影片：
	
	- [何時該調整 Azure 網站 - Stefan Schackow](/documentation/videos/azure-web-sites-free-vs-standard-scaling/)
	- [自動調整 Azure 網站、CPU 或排程 - Stefan Schackow](/documentation/videos/auto-scaling-azure-web-sites/)
	- [如何調整 Azure 網站 - Stefan Schackow](/documentation/videos/how-azure-web-sites-scale/)

## 變更的項目
* 如需從網站變更為 App Service 的指南，請參閱：[Azure App Service 及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 如需從舊的入口網站變更為新入口網站的指南，請參閱：[巡覽預覽入口網站的參考](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- LINKS -->
[vmsizes]: http://go.microsoft.com/fwlink/?LinkId=309169
[SQLaccountsbilling]: http://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]: http://go.microsoft.com/fwlink/?LinkID=235288
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png
 

<!---HONumber=62-->