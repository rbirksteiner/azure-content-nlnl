<properties
   pageTitle="在 Visual Studio 中使用 F5 偵錯 Service Fabric 應用程式"
   description="透過使用 Visual Studio 和本機開發叢集，改善您服務的可靠性和效能。"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/18/2015"
   ms.author="jesseb"/>

# 在 Visual Studio 中使用 F5 偵錯 Service Fabric 應用程式

您可以透過在本機電腦開發叢集中部署和偵錯 Service Fabric 應用程式來節省時間和金錢。Visual Studio 可以將應用程式部署至本機叢集，並自動將偵錯工具連接至您應用程式的所有執行個體。

1. 遵循＜[設定 Service Fabric 開發環境](service-fabric-get-started.md)＞中的步驟來啟動本機開發叢集。

2. 按 **F5**，或按一下 [**偵錯**] > [**開始偵錯**]

    ![開始偵錯應用程式][startdebugging]

3. 透過按一下 [**偵錯**] 功能表中的命令在您的程式碼中設定中斷點並逐步執行應用程式。

    > [AZURE.NOTE]Visual Studio 將附加至您應用程式的所有執行個體。逐步執行程式碼時，系統可能會透過多個程序叫用中斷點而導致並行工作階段。若要嘗試在叫用之後停用中斷點，請在執行緒識別碼上使用條件式中斷點，或使用 [診斷事件]。

4. [**診斷事件**] 視窗將自動開啟以即時檢視診斷事件。

    ![即時檢視診斷事件][diagnosticevents]

5. 您也可以在 [伺服器總管] 開啟 [**診斷事件**] 視窗。在 **Azure** 下方，以滑鼠右鍵按一下 [**Service Fabric 叢集**] > [**檢視診斷事件...**]

    ![開啟診斷事件視窗][viewdiagnosticevents]

6. 您可以在自動產生的 **ServiceEventSource.cs** 中查看診斷事件，並從應用程式程式碼進行呼叫。

    ```csharp
    ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, Service.ServiceTypeName);
    ```

7. [**診斷事件**] 視窗支援即時篩選、暫停和檢查事件。篩選是事件訊息的簡易字串搜尋，包括其內容。

    ![篩選、暫停和繼續，或即時檢查事件][diagnosticeventsactions]

8. 偵錯服務類似於偵錯任何其他應用程式。中斷點通常可以透過 Visual Studio 進行設定以輕鬆偵錯。即使可靠集合會在多個節點上進行複寫，其仍會實作 IEnumerable，這表示您可以在 Visual Studio 中使用 [結果檢視]，同時進行偵錯以查看已在內部儲存的內容。您可以在程式碼中的任何位置輕鬆設定中斷點。

    ![開始偵錯應用程式][breakpoint]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟

- [測試 Service Fabric 服務](service-fabric-test-your-service-index.md)。
- [在 Visual Studio 中管理 Service Fabric 應用程式](service-fabric-manage-application-in-visual-studio.md)。

<!--Image references-->
[startdebugging]: ./media/service-fabric-debugging-your-application/startdebugging.png
[diagnosticevents]: ./media/service-fabric-debugging-your-application/diagnosticevents.png
[viewdiagnosticevents]: ./media/service-fabric-debugging-your-application/viewdiagnosticevents.png
[diagnosticeventsactions]: ./media/service-fabric-debugging-your-application/diagnosticeventsactions.png
[breakpoint]: ./media/service-fabric-debugging-your-application/breakpoint.png
 

<!---HONumber=July15_HO2-->