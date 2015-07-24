<properties 
   pageTitle="設定效能負載平衡"
   description="本文將協助您在流量管理員中設定效能負載平衡"
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/27/2015"
   ms.author="joaoma;cherylmc" />

# 設定效能負載平衡

為了能夠負載平衡位於全球不同資料中心 (也稱為區域) 的雲端服務和網站 (端點)，您可以將連入流量從要求用戶端導向具有最低延遲的端點。通常，具有最低延遲的資料中心會對應到最短地理距離的資料中心。[效能] 負載平衡方法可讓您根據最低延遲進行分配，但不會將網路組態或負載中的即時變更納入考量。如需 Azure 流量管理員所提供之不同負載平衡方法的詳細資訊，請參閱[關於流量管理員負載平衡方法](traffic-manager-load-balancing-methods.md)。

## 負載平衡流量會視一組端點中的最低延遲而定：

1. 在管理入口網站的左側窗格中，按一下 [流量管理員] 圖示以開啟 [流量管理員] 窗格。如果您尚未建立流量管理員設定檔，請參閱[管理流量管理員設定檔](traffic-manager-manage-profiles.md)的步驟來建立基本的流量管理員設定檔。
2. 在管理入口網站的 [流量管理員] 窗格中，找出包含您要修改設定的流量管理員設定檔，然後按一下設定檔名稱右側的箭號。這會開啟設定檔的設定頁面。
3. 在您的設定檔頁面上，按一下頁面頂端的 [端點]，並確認您要納入組態的服務端點已存在。如需在設定檔中加入或移除端點的步驟，請參閱[在流量管理員中管理端點](traffic-manager-endpoints.md)。
4. 在您的設定檔頁面上，按一下頂端的 [設定] 以開啟 [組態] 頁面。
5. 在 [負載平衡方法設定] 中，確認負載平衡方法為 [效能*]**。如果不是，請按一下下拉式清單中的 [效能]。6. 確認已正確地設定 [監視設定]。監視可確保處於離線狀態的端點不會傳送流量。為了要能夠監視端點，您必須指定路徑和檔案名稱。請注意，正斜線 “/“ 是相對路徑的有效項目，表示檔案位於根目錄 (預設值)。如需有關監視的詳細資訊，請參閱[關於流量管理員監視](traffic-manager-monitoring.md)。
7. 完成組態變更之後，請按一下頁面底部的 [儲存]。
8. 測試組態中的變更。如需詳細資訊，請參閱[測試流量管理員設定](traffic-manager-testing-settings.md)。
9. 在已設定並執行流量管理員設定檔之後，您可以編輯授權 DNS 伺服器上的 DNS 記錄，以將您的公司網域名稱指向流量管理員網域名稱。如需有關如何執行這項操作的詳細資訊，請參閱[將公司網際網路網域指向流量管理員網域](traffic-manager-point-internet-domain.md)。

## 另請參閱

[關於流量管理員的負載平衡方法](traffic-manager-load-balancing-methods.md)

[流量管理員組態工作](https://msdn.microsoft.com/library/azure/hh744830.aspx)

[流量管理員概觀](../traffic-manmager-overview.md)

[雲端服務](http://go.microsoft.com/fwlink/?LinkId=314074)

[網站](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[流量管理員的相關作業 (REST API 參考)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Azure 流量管理員 Cmdlet](http://go.microsoft.com/fwlink/p/?LinkId=400769)

 

<!---HONumber=July15_HO2-->