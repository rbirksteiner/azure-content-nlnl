<properties
   pageTitle="設定 Service Fabric 應用程式的 Application Insights"
   description="在 Application Insights 中接收應用程式的 Service Fabric 事件。"
   services="service-fabric"
   documentationCenter=".net"
   authors="mattrowmsft"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/22/2015"
   ms.author="mattrow"/>

# 設定 Service Fabric 應用程式的 Application Insights
 本文章將引導您啟用 Service Fabric 應用程式的 Application Insights。

## 必要條件

本文章假設您已在 Visual Studio 中建立 Service Fabric 應用程式。若要尋找說明，請[按一下這裡](service-fabric-reliable-services-quick-start.md)。

## 安裝 NuGet 封裝
我們 NuGet 封裝 Microsoft.ServiceFabric.Telemetry.ApplicationInsights 的發行前版本將發行為 Service Fabric SDK 的一部分。此封裝會將 Service Fabric EventSource 事件與 Application Insights 繫結在一起，針對 Service Fabric 應用程式提供自動化檢測。此封裝將繼續使用應用程式自動發出的新事件進行更新。

您可以使用下列步驟安裝封裝：

1. 開啟 Service Fabric 應用程式的 NuGet 封裝管理員。此操作可以透過以滑鼠右鍵按一下 Visual Studio 中的專案，然後選取 [管理 NuGet 封裝] 來完成。
2. 您必須選取 [Microsoft Azure Service Fabric] 作為封裝來源，以列出包含在 Service Fabric SDK 的套件。![VS2015 NuGet 封裝管理員](media/service-fabric-diagnostics-application-insights-setup/AI-nuget-package-manager.jpg)
3. 選取左側的 Microsoft.ServiceFabric.Telemetry.ApplicationInsights 封裝。
4. 按一下 [安裝] 開始安裝程序。
5. 檢閱並接受使用者授權合約。

## 啟用 Service Fabric 事件
若要在 Application Insights 中會自動接收 Service Fabric 事件，您必須啟用我們的接聽程式。您可以將下列程式碼行插入至應用程式來完成此操作。

```csharp
    Microsoft.ServiceFabric.Telemetry.ApplicationInsights.Listener.Enable(EventLevel.Verbose);
```
 
### StatefulActor\\Program.cs 的範例：

```csharp
    public static void Main(string[] args)
    {
        Microsoft.ServiceFabric.Telemetry.ApplicationInsights.Listener.Enable(EventLevel.Verbose);
        try
        {
            using (FabricRuntime fabricRuntime = FabricRuntime.Create())
            {
                fabricRuntime.RegisterActor(typeof(StatefulActor));

                Thread.Sleep(Timeout.Infinite);
            }
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e);
            throw;
        }
    }
```

您可以在[這裡](service-fabric-reliable-actors-diagnostics.md)了解可靠動作項目執行階段所發出的事件，若要了解可靠服務執行階段請參閱[這裡](service-fabric-reliable-services-diagnostics.md)。

請注意，若要取得可靠動作項目執行階段方法呼叫，則必須使用 EventLevel.Verbose (如上述範例所示)。

## 設定 Application Insights
檢測設備金鑰是用來將 Service Fabric 應用程式繫結至 Application Insights 資源。您可以遵循＜[Application Insight 指南](../app-insights-create-new-resource.md#create-an-application-insights-resource)＞了解如何取得檢測設備金鑰。建立資源時針對應用程式類型選取 [其他]。

![針對 AI 應用程式類型選取 [其他]](media/service-fabric-diagnostics-application-insights-setup/AI-app-type-other.JPG)

取得檢測設備金鑰之後，您可以將它插入 ApplicationInsights.config 檔案，如下所示：

```xml
    <InstrumentationKey>INSERT YOUR KEY HERE</InstrumentationKey>
```

## 檢視資料
您可以[自訂 App Insights 刀鋒視窗](../app-insights-metrics-explorer.md)以符合您的需求。多數 Service Fabric 事件將顯示為 [自訂事件]，同時網狀架構動作項目方法呼叫和服務 RunAsync() 呼叫將會顯示為要求。將這些事件模型化為要求，可讓您在建立圖表使用 [要求名稱] 維度和 [要求持續時間] 計量。我們將持續新增新的圖表、計量和事件，以便您日後加以利用。

## 後續步驟
進一步了解使用 Application Insights 來檢測您的 Service Fabric 應用程式。

- [開始使用 Application Insights](../app-insights-get-started.md)
- [了解如何建立您自己的自訂事件和計量](../app-insights-custom-events-metrics-api.md)
 

<!---HONumber=July15_HO2-->