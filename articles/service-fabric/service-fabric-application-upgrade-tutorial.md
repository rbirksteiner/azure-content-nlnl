<properties
   pageTitle="Service Fabric 應用程式升級教學課程"
   description="本文章逐步解說升級 Service Fabric 應用程式的體驗。"
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



# 應用程式升級教學課程

最常使用和建議的升級方法是監視輪流升級。Service Fabric 會根據健康狀態原則集，監視正在升級之應用程式的健康狀態。當升級網域 (UD) 中的應用程式已升級時，Service Fabric 會評估應用程式健康狀態，並決定是否要繼續進行下一個升級網域，或根據健康狀態原則讓升級失敗。可以使用受管理或原生 API、PowerShell 或 REST，執行監視應用程式升級。

Service Fabric 監視輪流升級可以讓應用程式系統管理員設定 Service Fabric 用來判斷應用程式健康狀態良好的健康狀態評估原則。此外，它也可讓系統管理員設定當健康狀態評估失敗時採取的動作，例如自動回復。此章節會逐步解說其中一個 SDK 範例的監視升級。

## 步驟 1：建置與部署視覺物件範例

這些步驟可以藉由下列動作來完成：在 Visual Studio 中開啟專案，以滑鼠右鍵按一下解決方案，並且在 Service Fabric 功能表項目中選取部署命令。如需詳細資訊，請參閱[在 Visual Studio 中管理您的 Service Fabric 應用程式](service-fabric-manage-application-in-visual-studio.md)。或者，使用者會使用 PowerShell。

> [AZURE.NOTE]在 PowerShell 中使用任何 Service Fabric 命令之前，必須先使用 `Connect-ServiceFabricCluster` Cmdlet 連接到叢集。同樣地，它會假設已經在本機電腦上設定叢集。請參閱[設定 Service Fabric 開發環境](service-fabric-get-started.md)上的文章

在 Visual Studio 中建置專案之後，使用者會使用 PowerShell 命令 **Copy-ServiceFabricApplicationPackage**，將應用程式封裝複製到 ImageStore，然後藉由使用 **Register-ServiceFabricApplicationPackage** Cmdlet，將應用程式註冊至 Service Fabric 執行階段，最後，使用 **New-ServiceFabricApplication** Cmdlet 來啟動應用程式的執行個體。這三個步驟類似於在 Visual Studio 中使用 [部署] 功能表項目。

現在，您可以使用 [Service Fabric 總管來檢視叢集與應用程式](service-fabric-visualizing-your-cluster.md)。應用程式有 Web 服務，可以瀏覽至 Internet Explorer，方法是在網址列中輸入 [http://localhost:80](http://localhost:80)。您應該會在畫面上看到一些浮動視覺物件四處移動。此外，使用者可能會使用 **Get-ServiceFabricApplication** 檢查應用程式狀態。

## 步驟 2：更新視覺物件範例

您可能會注意到在步驟 1 中已部署的版本，視覺物件不會旋轉。讓我們將這個應用程式升級到其中的視覺物件也會旋轉的版本。

選取 VisualObjects 解決方案內的 VisualObjects.DataService 專案，然後在該專案中開啟 VisualObjects.cs 檔案。在該檔案內瀏覽至方法 `CreateMovedObject`，並尋找設定視覺物件旋轉的程式碼行 (搜尋 `CreateMovedObject` 方法中的下列程式碼行)。註解化程式碼行 `nextObject.Rotation = 1`，並取消註解其下的一行。進行變更之後，您的程式碼應如下所示：

```c#
            //nextObject.Rotation = 1;

            // Once that's deployed, uncomment this line and upgrade the application:

             nextObject.Rotation = (nextObject.Rotation + 10) % 360;
```

我們也需要更新專案 **VisualObjects.DataService** 的 *ServiceManifest.xml* 檔案 (PackageRoot 底下)。更新 *CodePackage* 並且將服務版本更新為 2.0，*ServiceManifest.xml* 檔案中的對應程式碼行應該如下所示 (反白顯示部分顯示變更)：

```xml
<ServiceManifestName="VisualObjects.DataService"Version="2.0"xmlns="http://schemas.microsoft.com/2011/01/fabric"xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code"Version="2.0">
```

現在，我們需要更新 *ApplicationManifest.xml* 檔案 (可以在 **VisualObjects** 解決方案底下的 **VisualObjects** 專案底下找到)，以使用版本 2.0 的 **VisualObjects.DataService** 專案，並且將應用程式版本從 1.0.0.0 更新為 2.0.0.0。現在，*ApplicationManifest.xml* 中的對應程式碼行應該會如下所示：

```xml
<ApplicationManifestxmlns:xsd="http://www.w3.org/2001/XMLSchema"xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"ApplicationTypeName="VisualObjects"ApplicationTypeVersion="2.0.0.0"xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.DataService"ServiceManifestVersion="2.0" />
```

現在建置專案，方法是選取 **VisualObjects** 專案，然後以滑鼠右鍵按一下並選取 Visual Studio 中的組建 (如果您選取全部重新建置，您可能必須更新 **VisualObjects.WebService** 專案的的版本，以及其 *ServiceManifest.xml* 和 *ApplicationManifest.xml*，因為程式碼已變更)。現在讓我們封裝已更新的應用程式，方法是以滑鼠右鍵按一下 *VisualObjects* 專案，選取 [Service Fabric] 功能表，然後選擇 [封裝]。這應該會建立可部署的應用程式封裝。更新的應用程式已準備好進行部署。

## 步驟 3：決定健康狀態原則並升級參數

請您熟悉[應用程式升級參數](service-fabric-application-upgrade-parameters.md)和[升級程序](service-fabric-application-upgrade.md)，以了解套用的各種升級參數、逾時和健康狀態準則。對於此逐步解說，我們會將服務健康狀態評估準則保留為預設值 (和建議值)，這表示升級之後，所有服務和執行個體應該是_健康狀態良好_。但是，讓我們將 *HealthCheckStableDuration* 增加為 60 秒 (如此一來，在升級繼續至下一個升級網域之前，至少有 20 秒的時間服務是健康狀態良好的)。同時也要將 *UpgradeDomainTimeout* 設為 1200 秒，將 *UpgradeTimeout* 設為 3000 秒。最後，我們也要將 *UpgradeFailureAction* 設為回復，以要求 Service Fabric 在升級期間遇到任何問題時，將應用程式回復為舊版。因此，開始升級時我們指定的升級參數 (在步驟 4) 如下所示：

FailureAction = Rollback

HealthCheckStableDurationSec = 60

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000

## 步驟 4：準備應用程式進行升級

現在，應用程式已建置並且準備好進行升級。如果您以系統管理員身分開啟 PowerShell 視窗並且輸入 **Get-ServiceFabricApplication**，它應該會讓您知道它是已部署之 **VisualObjects** 的應用程式類型 1.0.0.0。應用程式封裝儲存在以下的相對路徑，您在其中解壓縮 Service Fabric SDK - *Samples\\Services\\Stateful\\VisualObjects\\VisualObjects\\obj\\x64\\Debug*。您應該會在該目錄中找到 "Package" 資料夾 - 這是應用程式封裝儲存的位置。請檢查時間戳記以確保它是最新組建 (您也可能需要適當地修改路徑)。

現在讓我們將更新的應用程式封裝複製到 Service Fabric ImageStore (Service Fabric 在其中儲存應用程式封裝)。參數 *ApplicationPackagePathInImageStore* 會通知 Service Fabric 可以在哪裡找到應用程式封裝。使用下列命令將更新的應用程式放在 "VisualObjects_V2" (您可能需要再次適當地修改路徑)。

```powershell
Copy-ServiceFabricApplicationPackage  -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package
-ImageStoreConnectionString fabric:ImageStore   -ApplicationPackagePathInImageStore "VisualObjects_V2"
```

下一步是將此應用程式註冊至 Service Fabric，可以使用下列命令來執行：

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects_V2"
```

如果上述命令不成功，很可能是您需要重新建置所有服務。如步驟 2 中所述，您可能也必須更新您的 WebService 版本。

## 步驟 5：開始應用程式升級

現在，我們已經準備好使用下列命令開始應用程式升級：

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


請注意，應用程式名稱如 *ApplicationManifest.xml* 檔案中所述。Service Fabric 會使用這個名稱來識別要升級哪一個應用程式。如果您設定的逾時太短，您可能會遇到失敗訊息，指出此問題。請參閱疑難排解章節，或增加逾時。

現在，應用程式升級會繼續，您可以使用 Service Fabric 總管或使用下列 PowerShell 命令進行監視：**Get-ServiceFabricApplicationUpgrade fabric:/VisualObjects**。

幾分鐘後，使用上述 PowerShell 命令的狀態應該會顯示已升級所有升級網域 (完成)。而且，您應該會在瀏覽器視窗中發現該視覺物件現在已經開始旋轉！

您可能想要嘗試變更版本，從第 2 版移至第 3 版做為練習，或者甚至是從第 2 版移回第 1 版 (是的，您可以從 v2 升級至 v1)。練習逾時和健康狀態原則，讓自己更熟練。當您部署至 Azure 叢集時，所使用的參數將會不同於部署至本機叢集時使用的參數 - 建議保守地設定逾時。


## 後續步驟

[升級參數](service-fabric-application-upgrade-parameters.md)

[資料序列化](service-fabric-application-upgrade-data-serialization.md)

[進階主題](service-fabric-application-upgrade-advanced.md)

[疑難排解應用程式升級](service-fabric-application-upgrade-troubleshooting.md)
 

<!---HONumber=July15_HO2-->