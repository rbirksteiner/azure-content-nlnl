<properties
   pageTitle="診斷和疑難排解 Service Fabric 服務"
   description="概念資訊和教學課程可協助您診斷、監視和疑難排解 Service Fabric 服務。"
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/21/2015"
   ms.author="ryanwi"/>

# 診斷和監視 Service Fabric 服務
監視、偵測、診斷和疑難排解可讓服務繼續順利執行，盡可能減少服務中斷的使用者經驗。若要深入了解，請閱讀：

- [如何在本機上監視和診斷服務](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
- [設定 Service Fabric 應用程式的 Application Insights](service-fabric-diagnostics-application-insights-setup.md)
- [疑難排解應用程式升級失敗](service-fabric-application-upgrade-troubleshooting.md)
- [疑難排解監視失敗的應用程式升級](../service-fabric-application-monitored-upgrade-troubleshooting.md)
- [可靠動作項目的診斷和效能監視](service-fabric-reliable-actors-diagnostics.md)
- [可靠服務的診斷和效能監視](service-fabric-reliable-services-diagnostics.md)

## 疑難排解叢集
下列資訊可協助您疑難排解本機開發叢集：

- [疑難排解本機開發叢集設定](service-fabric-troubleshoot-local-cluster-setup.md)

## 健康狀態模型
Service Fabric 導入了健康狀態模型，可提供豐富、彈性且可延伸的報告以及 Service Fabric 實體的評估功能。Service Fabric 元件會針對所有實體提供現成的健康狀態報告。使用者服務可透過專屬於其邏輯的資訊來充實健康狀態資料，以及進行本身或叢集中其他實體的報告。若要深入了解，請閱讀：

- [Service Fabric 健康狀態監視簡介](service-fabric-health-introduction.md)
- [如何檢視 Service Fabric 健康狀態報告](service-fabric-view-entities-aggregated-health.md)
- [使用系統健康狀態報告進行疑難排解](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [新增自訂 Service Fabric 健康狀態報告](service-fabric-report-health.md)
 

<!---HONumber=July15_HO2-->