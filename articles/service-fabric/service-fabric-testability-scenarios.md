<properties
   pageTitle="執行混亂測試。"
   description="本文討論 Microsoft 提供的預先定義 Service Fabric 案例。"
   services="service-fabric"
   documentationCenter=".net"
   authors="anmolah"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/14/2015"
   ms.author="anmola"/>

# Testability 案例
雲端基礎結構之類的大型分散式系統本身並不可靠。Service Fabric 讓開發人員能夠撰寫可在不可靠的基礎結構上執行的服務。為了撰寫高品質的服務，開發人員必須能夠產生這類不可靠的基礎結構，才能測試其服務的穩定性。Service Fabric 讓開發人員可以引發錯誤動作，藉此以失敗情況測試服務。但鎖定式模擬錯誤就僅只於此了。若要進一步測試，Service Fabric 提供預先定義的測試案例。這些案例會以很長的時間在整個叢集上模擬連續的交錯錯誤，包括非失誤性和失誤性錯誤。一旦設定比率和錯誤類型後，案例會以用戶端工具的形式執行，透過 C# API 或 PowerShell 在叢集和您的服務中產生錯誤。在 Testability 功能中，我們提供以下案例。

1.	混亂測試
2.	容錯移轉測試

## 混亂測試
混亂案例會在整個 Service Fabric 叢集中產生錯誤。此案例會壓縮錯誤，通常是將幾個月或幾年壓縮到幾小時。交錯錯誤和高錯誤率的組合，可以找到會在其他情形下被遺漏的極端狀況。這會使服務的程式碼品質大幅提升。

### 混亂測試中模擬的錯誤
 - 重新啟動節點
 - 重新啟動已部署的程式碼封裝
 - 移除複本
 - 重新啟動複本
 - 移動主要複本 (選擇性)
 - 移動次要複本 (選擇性)

混亂測試會在指定的一段時間中，多次執行反覆的錯誤和叢集驗證。讓叢集穩定和驗證成功的所需時間也是可設定的。當我們在叢集驗證中發生一次失敗，案例就會失敗。例如，請考慮將測試設為執行 1 小時，且最多 3 個並行錯誤。測試會引發 3 個錯誤，然後驗證叢集的健康情況。上一個步驟的測試會反覆進行，直到叢集變成狀況不佳，或經過了 1 小時為止。如果任何反覆運算使叢集變成狀況不佳，也就是在設定的時間內不穩定，則測試就會失敗並產生例外狀況。此例外狀況表示發生了錯誤，且需要進一步調查。以目前的形式來看，測試混亂測試的錯誤產生引擎只會引發安全的錯誤。這表示因為沒有外部錯誤，所以永遠不會發生仲裁或資料遺失。

### 重要的組態選項
 - **TimeToRun**：測試在成功完成前的總執行時間。測試可以提前完成，而不必等驗證失敗。
 - **MaxClusterStabilizationTimeout**：測試失敗前，等候叢集變成狀況良好的時間上限。執行的檢查會查看叢集健康情況或服務健康情況是否正常，或是服務分割區是否達到目標複本的設定大小，以及是否沒有 InBuild 複本。
 - **MaxConcurrentFaults**：每個反覆運算中引發的最大並行錯誤數。數量越大，測試會越積極，因此導致更複雜的容錯移轉和轉換組合。無論此組態的數量多高，測試都能保證缺少外部錯誤時，就不會發生仲裁或資料遺失。
 - **EnableMoveReplicaFaults**：啟用或停用造成主要或次要複本移動的錯誤。預設會停用這些錯誤。
 - **WaitTimeBetweenIterations**：反覆運算之間的等待時間長度，也就是在一輪的錯誤與對應的驗證後等待下一輪。

### 如何執行混亂測試
C# 範例

```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll.

using System;
using System.Fabric;
using System.Fabric.Testability;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";

        Console.WriteLine("Starting Chaos Test Scenario...");
        try
        {
            RunChaosTestScenarioAsync(clusterConnection).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Chaos Test Scenario did not complete: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Chaos Test Scenario completed.");
        return 0;
    }

    static async Task RunChaosTestScenarioAsync(string clusterConnection)
    {
        TimeSpan maxClusterStabilizationTimeout = TimeSpan.FromSeconds(180);
        uint maxConcurrentFaults = 3;
        bool enableMoveReplicaFaults = true;

        // Create FabricClient with connection & security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);

        // The Chaos Test Scenario should run at least 60 minutes or up until it fails.
        TimeSpan timeToRun = TimeSpan.FromMinutes(60);
        ChaosTestScenarioParameters scenarioParameters = new ChaosTestScenarioParameters(
          maxClusterStabilizationTimeout,
          maxConcurrentFaults,
          enableMoveReplicaFaults,
          timeToRun);

        // Other related parameters:
        // Pause between two iterations for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenIterations = TimeSpan.FromSeconds(30);
        // Pause between concurrent actions for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenFaults = TimeSpan.FromSeconds(10);

        // Create the scenario class and execute it asynchronously.
        ChaosTestScenario chaosScenario = new ChaosTestScenario(fabricClient, scenarioParameters);

        try
        {
            await chaosScenario.ExecuteAsync(CancellationToken.None);
        }
        catch (AggregateException ae)
        {
            throw ae.InnerException;
        }
    }
}
```

PowerShell

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```


## 容錯移轉測試

容錯移轉測試案例是以特定服務分割區為目標的混亂測試案例版本。此測試會測試容錯移轉對特定服務分割區的影響，且其他服務不會受到影響。設定了目標分割區資訊和其他參數後，此測試會以用戶端工具的形式執行，並使用 C# API 或 Powershell 來產生服務分割區的錯誤。案例會在您的商務邏輯執行時，反覆進行一連串模擬的錯誤及服務驗證，同時提供工作負載。服務驗證中若有失敗，表示有需要進一步調查的問題。

### 在容錯移轉測試中模擬的錯誤
- 重新啟動分割區所在的已部署程式碼封裝
- 移除主要/次要複本或無狀態執行個體
- 重新啟動主要的次要複本 (如果是保存的服務)
- 移動主要複本
- 移動次要複本
- 重新啟動分割區。

容錯移轉測試工作會引發選定的錯誤，然後在服務上執行驗證，以確保其穩定性。容錯移轉測試一次只會引發一個錯誤，不像混亂測試中可能會有多個錯誤。如果在每個錯誤後，服務分割區沒有在設定的逾時內變穩定，測試會失敗。此測試只會引發安全的錯誤。這表示因為沒有外部錯誤，所以不會發生仲裁或資料遺失。

### 重要的組態選項
 - **PartitionSelector**：指定需要做為目標分割區的選取器物件。
 - **TimeToRun**：測試在完成前的總執行時間。
 - **MaxServiceStabilizationTimeout**：測試失敗前，等候叢集變成狀況良好的時間上限。執行的檢查會查看服務健康情況是否正常，或是所有分割區是否達到目標複本的設定大小，以及是否沒有 InBuild 複本。
 - **WaitTimeBetweenFaults**：每個錯誤和驗證的循環之間要等候的時間長度。

### 如何執行容錯移轉測試
C# 範例

```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll.

using System;
using System.Fabric;
using System.Fabric.Testability;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");

        Console.WriteLine("Starting Chaos Test Scenario...");
        try
        {
            RunFailoverTestScenarioAsync(clusterConnection, serviceName).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Chaos Test Scenario did not complete: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Chaos Test Scenario completed.");
        return 0;
    }

    static async Task RunFailoverTestScenarioAsync(string clusterConnection, Uri serviceName)
    {
        TimeSpan maxServiceStabilizationTimeout = TimeSpan.FromSeconds(180);
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

        // Create FabricClient with connection & security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);

        // The Chaos Test Scenario should run at least 60 minutes or up until it fails.
        TimeSpan timeToRun = TimeSpan.FromMinutes(60);
        FailoverTestScenarioParameters scenarioParameters = new FailoverTestScenarioParameters(
          randomPartitionSelector,
          timeToRun,
          maxServiceStabilizationTimeout);

        // Other related parameters:
        // Pause between two iterations for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenIterations = TimeSpan.FromSeconds(30);
        // Pause between concurrent actions for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenFaults = TimeSpan.FromSeconds(10);

        // Create the scenario class and execute it asynchronously.
        FailoverTestScenario chaosScenario = new FailoverTestScenario(fabricClient, scenarioParameters);

        try
        {
            await chaosScenario.ExecuteAsync(CancellationToken.None);
        }
        catch (AggregateException ae)
        {
            throw ae.InnerException;
        }
    }
}
```


PowerShell

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/SampleApp/SampleService"

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindSingleton
```

 

<!---HONumber=July15_HO2-->