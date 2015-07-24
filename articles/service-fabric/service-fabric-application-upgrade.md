<properties
   pageTitle="Service Fabric 應用程式升級"
   description="本文章簡介升級 Service Fabric 應用程式。"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="samgeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/15/2015"
   ms.author="subramar"/>


# Service Fabric 應用程式升級


Service Fabric 應用程式是服務集合。在升級期間，Service Fabric 會比較新的[應用程式資訊清單](service-fabric-application-model.md#describe-an-application)與舊版本，並決定應用程式中哪些服務需要更新。這是由比較服務資訊清單與先前版本的版本號碼來決定。如果服務未變更，則該服務不會升級。

## 輪流升級概觀
在輪流應用程式升級中，升級是階段執行。在每個階段中，升級會套用至叢集中的節點子集，稱為升級網域。如此一來，應用程式在整個升級過程中仍然可供使用。升級期間，叢集可能包含舊和新版本的混合。因此，兩個版本必須是向前及向後相容。如果它們不相容，應用程式系統管理員負責執行多階段升級，以維護可用性。此作業的完成方法是在升級至最終版本之前，使用與先前版本相容的中繼版本進行升級。

當您設定叢集時，會在叢集資訊清單中指定升級網域。您不應該假設升級網域會以特定順序收到更新。升級網域是應用程式的部署邏輯單元。升級網域可以讓服務在升級期間維持高可用性。

如果升級套用到叢集中的所有節點 (應用程式只有一個升級網域就是這種情形)，則不可能進行輪流升級。不建議執行此作業，因為升級時服務會當機且無法使用。此外，當叢集僅以一個升級網域進行設定時，Azure 不提供任何保證。

## 升級期間的健康狀態檢查
對於升級，需要設定健康狀態原則 (或可能會使用預設值)。當所有升級網域在指定的逾時內升級，且所有升級網域視為健康，則稱為成功升級。健康的升級網域表示升級網域會通過健康狀態原則中指定的所有健康狀態檢查 - 例如，健康狀態原則可能要求應用程式執行個體中的所有服務都必須<em>健康狀態良好</em>，如 Service Fabric 定義的健康狀態。

Service Fabric 在升級期間進行的健康狀態原則以及檢查是不限於服務和應用程式。也就是說，不會檢查任何服務的特定測試。例如，您的服務有最少的輸送量需求。不過，Service Fabric 並沒有為此進行測試的資訊，因此將不會檢查針對您的應用程式所定義的輸送量。請參閱[健康狀態文章](service-fabric-health-introduction.md)，以了解執行的檢查 - 如果應用程式封裝正確複製、如果執行個體已啟動等等，則包括升級期間的檢查。

應用程式健康狀態是應用程式的子系實體彙總。簡單地說，Service Fabric 會透過應用程式報告的健康狀態，以及應用程式之服務的健康狀態，來評估應用程式的健康狀態。應用程式服務的健康狀態會進一步藉由彙總其子項的健康狀態 (例如服務複本) 以進行評估。一旦滿足應用程式健康狀態原則，升級可以繼續，如果違反健康狀態原則，則應用程式升級會失敗。

## 升級模式

升級的常見模式 (和建議) 是「監視」。監視會在一個升級網域上執行升級，如果所有健康狀態檢查都通過 (每個指定的原則)，即會自動移至下一個升級網域，依此類推。如果健康狀態檢查失敗及/或達到逾時，升級網域的升級可能會回復，或者如果在升級時選取 UnmonitoredManual 選項，則會變更為該模式。

UnmonitoredManual 在每次於升級網域上升級之後需要手動介入，以開始進行下一個升級網域上的升級。未執行任何 Service Fabric 健康狀態檢查，且依賴使用者介入，在開始下一個升級網域中的升級之前，執行健康狀態或狀態檢查。

## 應用程式升級流程圖

以下流程圖可以協助了解 Service Fabric 應用程式的升級程序。特別是，流程說明逾時如何包括 *HealthCheckStableDuration*、*HealthCheckRetryTimeout* 和 *UpgradeHealthCheckInterval*，協助控制某個升級網域中的升級被視為成功或失敗。

![Service Fabric 應用程式的升級程序][image]


## 後續步驟

[升級教學課程](service-fabric-application-upgrade-tutorial.md)

[升級參數](service-fabric-application-upgrade-parameters.md)

[資料序列化](service-fabric-application-upgrade-data-serialization.md)

[進階主題](service-fabric-application-upgrade-advanced.md)

[疑難排解應用程式升級](service-fabric-application-upgrade-troubleshooting.md)



[image]: media/service-fabric-application-upgrade/service-fabric-application-upgrade-flowchart.png
 

<!---HONumber=July15_HO2-->