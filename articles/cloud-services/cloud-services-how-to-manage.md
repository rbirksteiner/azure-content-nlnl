<properties 
	pageTitle="如何管理雲端服務 - Azure" 
	description="了解如何在 Azure 管理入口網站中管理雲端服務。" 
	services="cloud-services" 
	documentationCenter="" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/16/2015"
	ms.author="adegeo"/>





# 如何管理雲端服務

在 Azure 管理入口網站的 [雲端服務] 區域中，您可以更新服務角色或部署、將預備部署升級至生產部署、將資源連結至您的雲端服務 (以方便您查看資源依存性並將資源一起調整)，以及刪除雲端服務或部署。


## 作法：更新雲端服務角色或部署

如果您需要更新雲端服務的應用程式程式碼，請使用儀表板上、[雲端服務] 頁面上或 [執行個體] 頁面上的 [更新]。您可以更新單一角色或所有角色。您將需要上傳新的服務套件和服務組態檔。

1. 在 [Azure 管理入口網站](https://manage.windowsazure.com/)中，按一下儀表板上、[雲端服務] 頁面上或 [執行個體] 頁面上的 [更新]。

	![UpdateDeployment](./media/cloud-services-how-to-manage/CloudServices_UpdateDeployment.png)

2. 在 [部署標籤] 中，輸入部署的識別名稱 (例如，mycloudservice4)。您將在儀表板的 [快速入門] 下發現此部署標籤。

3. 在 [封裝] 中，使用 [瀏覽] 上傳服務套件檔 (.cspkg)。

4. 在 [組態] 中，使用 [瀏覽] 上傳服務組態檔 (.cscfg)。

5. 若要升級雲端服務中的所有角色，請在 [角色] 中選取 [全部]。若要執行單一角色更新，請選取您要更新的角色。即使您選取特定角色來更新，服務組態檔中的更新還是會套用至所有角色。

6. 如果更新會造成角色數目或任何角色的大小變更，請選取 [Allow update if role sizes or number of roles changes] 核取方塊，讓更新能夠繼續。

	請注意，如果您變更角色的大小 (亦即，角色執行個體所裝載於之虛擬機器的大小) 或角色數目，則必須重新製作每個角色執行個體 (虛擬機器) 的映像，因而遺失本機資料。

7. 如果有任何服務角色只有一個角色執行個體，請選取 [Update even if one or more role contain a single instance] 核取方塊，讓升級能夠繼續。

	要讓 Azure 保證服務在雲端服務更新期間有 99.95% 的可用性，每個角色都至少必須有兩個角色執行個體 (虛擬機器)。如此才能讓一個虛擬機器在受到更新時，還有另一個虛擬機器可以處理用戶端要求。

8. 按一下 [確定] (核取記號) 開始更新服務。



## 作法：交換部署以使預備部署升格為生產部署

使用 [交換] 將雲端服務的預備部署升格為生產部署。當您決定部署新版的雲端服務時，可以在雲端服務預備環境中預備並測試這個新版本，同時讓您的客戶繼續在生產部署中使用目前版本。當您準備好要將新版部署升格為生產部署時，可以使用 [交換] 將這兩個部署的 URL 位址互換。

您可以在 [雲端服務] 頁面或儀表板交換部署。

1. 在[管理入口網站](https://manage.windowsazure.com/)中，按一下 [雲端服務]。

2. 在雲端服務清單中，按一下雲端服務加以選取。

3. 按一下 [交換]。

	隨即開啟下列確認提示。

	![Cloud Services Swap](./media/cloud-services-how-to-manage/CloudServices_Swap.png)

4. 確認部署資訊無誤之後，按一下 [是] 交換部署。

	交換部署的速度很快，因為唯一變更的是部署的虛擬 IP 位址 (VIP)。

	當您確定新的生產部署如預期執行時，即可刪除預備環境中的部署，以節省運算成本。

## 作法：將資源連結到雲端服務

若要顯示您的雲端服務對其他資源的依存性，您可以將 Azure SQL Database 執行個體或儲存體帳戶連結到雲端服務。您可以在 [**連結的資源**] 頁面上連結和取消連結資源，然後在雲端服務儀表板上監視其使用率。如果連結的儲存體帳戶已開啟監視功能，則您可以在雲端服務儀表板上監視 [要求總數]。

您可以使用 [連結] 將新的或現有 SQL Database 執行個體或儲存體帳戶連結到您的雲端服務。然後，您便可以在 [調整] 頁面上調整資料庫以及使用該資料庫的雲端服務角色。(儲存體帳戶會在使用量增加時自動調整。) 如需詳細資訊，請參閱[如何調整雲端服務和連結的資源](cloud-services-how-to-scale.md) (英文)。

您也可以在管理入口網站的 [資料庫] 節點中監視、管理和調整資料庫。

在這種情況下，將資源「連結」並不會將您的應用程式連接到資源。如果您使用 [連結] 建立新的資料庫，則需要將連接字串新增至應用程式程式碼，然後升級雲端服務。如果您的應用程式使用連結的儲存體帳戶中的資源，您也需要新增連接字串。

下列程序說明如何將新的 SQL Database 執行個體 (部署於新的 SQL Database 伺服器上) 連結到雲端服務。

### 將 SQL Database 執行個體連結到雲端服務

1. 在[管理入口網站](http://manage.windowsazure.com/)中，按一下 [雲端服務]。然後按一下雲端服務的名稱以開啟儀表板。

2. 按一下 [Linked Resources]。

	[Linked Resources] 頁面隨即開啟。

	![LinkedResourcesPage](./media/cloud-services-how-to-manage/CloudServices_LinkedResourcesPage.png)

3. 按一下 [Link a Resource] 或 [連結]。

	[Link Resource] 精靈隨即啟動。

	![Link Page1](./media/cloud-services-how-to-manage/CloudServices_LinkedResources_LinkPage1.png)

4. 按一下 [建立新的資源] 或 [Link an existing resource]。

5. 選擇要連結的資源類型。在[管理入口網站](http://manage.windowsazure.com/)中，按一下 [SQL Database]。(預覽版管理入口網站不支援將儲存體帳戶連結到雲端服務。)

6. 若要完成資料庫組態，請依照管理入口網站 [SQL Databases] 區域的說明指示執行。

	您可以在訊息區域中追蹤連結作業的進度。

	![Link Progress](./media/cloud-services-how-to-manage/CloudServices_LinkedResources_LinkProgress.png)

	連結完成時，您可以在雲端服務儀表板上監視已連結資源的狀態。如需關於調整已連結之 SQL Database 的詳細資訊，請參閱[如何調整雲端服務和連結的資源](cloud-services-how-to-scale.md) (英文)。

### 取消連結已連結的資源

1. 在[管理入口網站](http://manage.windowsazure.com/)中，按一下 [雲端服務]。然後按一下雲端服務的名稱以開啟儀表板。

2. 按一下 [Linked Resources]，然後選取資源。

3. 按一下 [取消連結]。然後在確認提示處按一下 [是]。

	取消連結 SQL Database 並不會影響資料庫或是應用程式與資料庫的連線。您還是可以在管理入口網站的 [SQL Databases] 區域中管理資料庫。



## 作法：刪除部署和雲端服務

刪除雲端服務之前，您必須先刪除每個現有的部署。

為了節省運算成本，您可以在確認生產部署如預期運作之後刪除預備部署。只要角色執行個體存在，您就需要為其支付運算成本，即使雲端服務未執行也一樣。

使用下列程序，刪除部署或雲端服務。

1. 在[管理入口網站](http://manage.windowsazure.com/)中，按一下 [雲端服務]。

2. 選取雲端服務，然後按一下 [刪除]。(若要選取雲端服務而不開啟儀表板，請在雲端服務項目中按一下名稱以外的任何位置。)

	如果您有預備或生產的部署，則會在視窗底部看到與下面類似的選項功能表。刪除雲端服務之前，您必須先刪除任何現有的部署。

	![Delete Menu](./media/cloud-services-how-to-manage/CloudServices_DeleteMenu.png)


3. 若要刪除部署，請按一下 [Delete production deployment] 或 [Delete staging deployment]。然後，在確認提示處按一下 [是]。

4. 如果您計劃刪除雲端服務，請重複步驟 3 (如果需要) 來刪除另一個部署。

5. 若要刪除雲端服務，請按一下 [刪除雲端服務]。然後，在確認提示處按一下 [是]。

> [AZURE.NOTE]如果對雲端服務設定了詳細資訊監視，則當您刪除該雲端服務時，Azure 並不會從您的儲存體帳戶中刪除監視資料。您將需要手動刪除資料。如需關於何處可找到這些量值資料表的詳細資訊，請參閱<a href="http://azure.microsoft.com/documentation/articles/cloud-services-how-to-monitor/">如何監視雲端服務</a>中的＜作法：在管理入口網站外存取詳細資訊監視資料＞。

 

<!---HONumber=62-->