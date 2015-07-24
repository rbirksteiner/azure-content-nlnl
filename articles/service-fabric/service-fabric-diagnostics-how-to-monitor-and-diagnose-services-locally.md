<properties
   pageTitle="Microsoft Azure Service Fabric 如何在本機上監視和診斷服務"
   description="本文章將說明如何監視和診斷您在本機開發電腦上使用 Microsoft Azure Service Fabric 所撰寫的服務。"
   services="service-fabric"
   documentationCenter=".net"
   authors="kunaldsingh"
   manager="samgeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/22/2015"
   ms.author="kunalds"/>


# 監視和診斷本機開發設定中的服務
監視、偵測、診斷和疑難排解可讓服務繼續順利執行，盡可能減少服務中斷的使用者經驗。雖然實際上已部署的生產環境中相當重要，然而效率將取決於服務開發期間所採用的類似模型服務，以便在您移動至實際設定時能確保其正常運作。Service Fabric 可讓服務開發人員輕鬆實作診斷，可以在單一電腦本機開發和實際生產叢集安裝上順暢地工作。

## 追蹤和記錄
[Windows 事件追蹤](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) 是我們建議的技術，可用於追蹤 Service Fabric 中的訊息。這樣做的原因是：

* ETW 相當快速。其是以一種追蹤技術建置而成，並對您程式碼執行時間的影響降到最低。
* ETW 會在本機開發環境以及實際叢集設定順暢地進行追蹤。這表示當您準備好將程式碼部署至實際叢集時，您不需要重寫追蹤程式碼。
* Service Fabric 系統程式碼也會將 ETW 用於內部追蹤。這可讓您檢視與 Service Fabric 系統追蹤交錯的應用程式追蹤，更容易了解在基礎系統中應用程式程式碼與事件之間的序列和相互關係。
* 內建支援的 Service Fabric Visual Studio 工具可供您檢視 ETW 事件。


## 在 Visual Studio 中檢視 Service Fabric 系統事件

Service Fabric 會發出 ETW 事件，以協助應用程式開發人員了解平台中發生的事情。若要檢視這些事件，請遵循下列步驟：

1. 您必須安裝下列必要條件。
   * Visual Studio 2015
   * Service Fabric SDK
2. 以系統管理員身分啟動 Visual Studio。
3. 建立 (或開啟現有的) 專案，以取得可設定狀態或無狀態動作項目或服務。![建立 Service Fabric 專案](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/CreateServiceFabricProject.png)

4. 按 F5 偵錯應用程式。Service Fabric 事件應該會出現在 [診斷事件] 視窗中。每個事件皆有標準的中繼資料資訊，可告訴您事件所來自的節點、應用程式和服務。您也可以使用視窗上方的 [篩選事件] 方塊來篩選事件清單，例如您可以篩選 [節點名稱] 或 [服務名稱]。![Visual Studio 診斷事件檢視器](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/DiagEventsExamples2.png)

5. 若 [診斷事件] 視窗不會自動顯示，請移至 Visual Studio 中的 [伺服器總管] 索引標籤，以滑鼠右鍵按一下 [Service Fabric 叢集]，並選擇內容功能表中的 [檢視診斷事件]。

![開啟 Visual Studio 診斷事件檢視器](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/ServerExViewDiagEvents.png)

## 將您自己自訂的追蹤新增至應用程式程式碼
Service Fabric Visual Studio 專案範本包含範例程式碼。程式碼示範如何新增自訂的應用程式程式碼 ETW 追蹤，其會與來自 Service Fabric 的系統追蹤一併顯示在 Visual Studio ETW 檢視器中。這個方法的優點是中繼資料會自動新增至追蹤，且 Visual Studio 診斷檢視器已設定為顯示追蹤。

針對從**服務範本** (無狀態或可設定狀態) 所建立專案：

1. 開啟 **Service.cs** 檔案。使用 *RunAsync* 方法呼叫 `ServiceEventSource.Current.Message` 時會顯示來自應用程式程式碼的自訂 ETW 追蹤範例。
2. 在 **ServiceEventSource.cs** 檔案中，`ServiceEventSource.Message` 的多載方法會顯示如何撰寫自訂 ETW 追蹤的選項。

針對從**動作項目範本** (無狀態或可設定狀態) 所建立專案：

1. 開啟 **"ProjectName".cs** 檔案，其中 *ProjectName* 是您針對 Visual Studio 專案所選擇的名稱。  
2. 使用 *DoWorkAsync* 方法找到程式碼 `ActorEventSource.Current.ActorMessage(this, "Doing Work");`。這是來自應用程式程式碼的自訂 ETW 追蹤範例。  
3. 在 **ActorEventSource.cs** 檔案中，`ActorEventSource.ActorMessage` 方法的多載會顯示如何撰寫自訂 ETW 追蹤的選項。

將自訂 ETW 追蹤新增至服務程式碼之後，您可以再次建置、部署，以及執行應用程式以查看診斷檢視器中的事件。如果您使用 F5 來偵錯應用程式，診斷檢視器將會自動開啟。

##敬請期待
在 Azure 叢集上執行相同程式碼時，您針對本機診斷在上方應用程式所新增的相同追蹤程式碼，將會使用可以用來檢視這些事件的工具。我們即將更新相關的詳細資料。

## 後續步驟

* [Service Fabric 健康狀態簡介](service-fabric-health-introduction.md)
* [Application Insights 設定](service-fabric-diagnostics-application-insights-setup.md)
* [Azure Service Fabric 動作項目診斷與效能監視](service-fabric-reliable-actors-diagnostics.md)
* [可設定狀態可靠服務診斷](service-fabric-reliable-services-diagnostics.md)
 

<!---HONumber=July15_HO2-->