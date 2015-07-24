<properties
   pageTitle="Service Fabric 應用程式模型"
   description="如何在 Service Fabric 中模型化應用程式"
   services="service-fabric"
   documentationCenter=".net"
   authors="alexwun"
   manager="timlt"
   editor="mani-ramaswamy"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/23/2015"
   ms.author="alexwun"/>

# 在 Service Fabric 中模型化應用程式

本文提供 Service Fabric 應用程式模型的概觀，並說明如何透過資訊清單檔案定義應用程式和服務，並讓應用程式封裝準備好進行部署。

## 了解應用程式模型

應用程式是組成服務的集合，這些服務會執行特定函數。服務會執行完整且獨立的函數 (它們可以獨立於其他服務啟動和執行)，並且是由程式碼、組態和資料組成。針對每個服務，程式碼是由可執行檔二進位檔組成、組態是由可以在執行階段載入的服務設定組成，而資料是由讓服務使用的任意靜態資料組成。此階層應用程式模型中的每個元件都可以獨立建立版本和升級。

![][1]


應用程式類型是應用程式的分類，應用程式是由服務類型的組合所組成。服務類型是服務的分類，可以有不同的設定和組態，但是核心功能保持不變。服務的執行個體是相同服務類型的不同服務組態變形。

應用程式和服務的類別 (或「類型」) 是使用 XML 檔案 (應用程式資訊清單和服務資訊清單) 來加以說明，該檔案是應用程式可以針對它具現化的範本。不同應用程式執行個體的程式碼會執行為個別的程序，即使是由相同的 Service Fabric 節點所裝載。此外，每個應用程式執行個體的生命週期可以獨立進行管理 (也就是升級)。下圖顯示應用程式類型如何由服務類型組成，依序分別為程式碼、組態和封裝的組成。

![Service Fabric ApplicationTypes 和 ServiceTypes][Image1]

兩個不同的資訊清單檔案用來說明應用程式和服務：服務資訊清單和應用程式資訊清單，後續章節中有詳細說明。

叢集中可以有一或多個使用中的服務類型執行個體。例如，可設定狀態的服務執行個體或複本，藉由複寫叢集中不同節點上的複本之間的狀態達到高可靠性 (即使叢集中其中一個節點失敗，基本上會提供備援讓服務可供使用)。[分割服務](service-fabric-concepts-partitioning.md)進一步在叢集中的節點之間分割其狀態 (並且存取該狀態的模式)。

下圖顯示應用程式和服務執行個體、分割和複本之間的關聯性。

![服務內的分割和複本][Image2]


## 描述服務

服務資訊清單以宣告方式定義服務類型和版本，並指定服務中繼資料，例如服務類型、健康情況屬性、負載平衡度量，以及服務二進位檔和組態檔。換句話說，它會描述組成服務封裝的程式碼、組態和資料封裝，以支援一或多個服務類型。以下是簡單的範例服務資訊清單：

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="MyCode" Version="CodeVersion1">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="MyConfig" Version="ConfigVersion1" />
  <DataPackage Name="MyData" Version="DataVersion1" />
</ServiceManifest>
~~~

**版本**屬性為非結構化字串，不是由系統剖析。這些是用於每個元件的版本以進行升級。

**ServiceTypes**會宣告此資訊清單中 **CodePackages** 支援哪些服務類型。當針對其中一種服務類型來具現化服務時，會藉由執行其進入點來啟動此資訊清單中宣告的所有程式碼封裝。產生的處理程序預期都必須在執行階段註冊支援的服務類型。請注意，服務類型是在資訊清單層級而非程式碼封裝層級宣告。因此如果有多個程式碼封裝，每當系統尋找任何一個宣告的服務類型時，它們都會啟動。

**SetupEntryPoint** 是以與 Service Fabric 相同的認證執行的特殊權限進入點 (通常 *LocalSystem* 帳戶)，優先於任何其他進入點。**EntryPoint** 指定的可執行檔通常是長時間執行服務主機，所以有個別進入點可以避免以高度權限執行服務主機延長的時間期間。**EntryPoint** 指定的可執行檔是在 **SetupEntryPoint** 成功結束之後執行。如果曾經終止或當機，產生的程序會監視並重新啟動 (以 **SetupEntryPoint** 再次開始)。

**DataPackage** 宣告 **名稱**屬性所命名的資料夾，包含由程序在執行階段使用的任意靜態資料。

**ConfigPackage** 宣告 **名稱**屬性所命名的資料夾，其中包含 *Settings.xml* 檔案。此檔案包含程序可以在執行階段讀回的使用者定義、索引鍵-值配對設定的區段。在升級期間，如果只有 **ConfigPackage** **版本**已變更，則不會重新啟動執行中程序。相反地，回呼會通知程序組態設定已變更，因此它們可以動態方式重新載入。以下是 *Settings.xml* 檔案的範例：

~~~
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSecion">
    <Parameter Name="MySettingA" Value="Foo" />
    <Parameter Name="MySettingB" Value="Bar" />
  </Section>
</Settings>
~~~

> [AZURE.NOTE]服務資訊清單可以包含多個程式碼、組態和資料封裝，每個皆可獨立進行版本建立。

<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Resources
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## 描述應用程式


應用程式資訊清單以宣告方式描述應用程式類型和版本，並指定服務組成中繼資料，例如穩定的名稱、分割配置、執行個體計數/複寫因數、安全性/隔離原則、安置限制、組態覆寫和組成服務類型。也說明要放置應用程式的負載平衡網域。因此，應用程式資訊清單描述應用程式層級的元素，並參考用來組成應用程式類型的一個或多個服務資訊清單。以下是簡單的範例應用程式資訊清單：

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ApplicationManifest
      ApplicationTypeName="MyApplicationType"
      ApplicationTypeVersion="AppManifestVersion1"
      xmlns="http://schemas.microsoft.com/2011/01/fabric"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example application manifest</Description>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyServiceManifest" ServiceManifestVersion="SvcManifestVersion1"/>
  </ServiceManifestImport>
  <DefaultServices>
     <Service Name="MyService">
         <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
             <SingletonPartition/>
         </StatelessService>
     </Service>
  </DefaultServices>
</ApplicationManifest>
~~~

如同服務資訊清單，**版本**屬性為非結構化字串，不是由系統剖析。這些也用於每個元件的版本以進行升級。

**ServiceManifestImport** 包含組成此應用程式類型之服務資訊清單的參考。匯入的服務資訊清單會決定此應用程式類型中哪些服務類型有效。

**DefaultServices** 宣告服務執行個體，該執行個體會在每次應用程式針對此應用程式類型具現化時建立。預設服務只是為了方便起見，在建立之後，其行為在每個方面就像正常的服務。它們會與應用程式執行個體中的其他任何服務一起升級，也可以一起移除。

> [AZURE.NOTE]應用程式資訊清單可以包含多個服務資訊清單匯入和預設服務。每個服務資訊清單匯入都可以獨立建立版本。

<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Application parameters
*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->

## 封裝應用程式

### 封裝版面配置

應用程式資訊清單、服務資訊清單和其他必要封裝檔案必須以特定版面配置組織，才能部署至 Service Fabric 叢集。在本文中的範例資訊清單必須組織成下列目錄結構：

~~~
PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
~~~

命名資料夾以符合每個對應元素的**名稱**屬性。例如，如果服務資訊清單包含名稱為 **MyCodeA** 和 **MyCodeB** 的兩個程式碼封裝，則必須為兩個具有相同名稱的資料夾，其中包含每個程式碼封裝所需的二進位檔。

### 使用 Visual Studio 建置封裝

如果您使用 Visual Studio 2015 來建立您的應用程式，您可以使用 [封裝] 命令來自動建立符合上述版面配置的封裝。

若要建立封裝，只要以滑鼠右鍵按一下 [方案總管] 中的應用程式專案，然後選擇 [封裝] 命令，如下所示：

![][2]

封裝完成時，您會在 [輸出] 視窗中發現封裝的位置。請注意，當您在 Visual Studio 中部署或偵錯應用程式時，封裝步驟會自動進行。

### 測試封裝

封裝結構可以透過 PowerShell 使用 **Test-ServiceFabricApplicationPackage** 命令，進行本機驗證，該命令會檢查資訊清單剖析問題並驗證所有參考。請注意，此命令只會驗證封裝中目錄和檔案的結構正確性 - 它不會驗證任何程式碼或資料封裝內容，檢查所有必要檔案都存在：

~~~
PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
False
Test-ServiceFabricApplicationPackage : The EntryPoint MySetup.bat is not found.
FileName: C:\Users\servicefabric\AppData\Local\Temp\TestApplicationPackage_7195781181\nrri205a.e2h\MyApplicationType\MyServiceManifest\ServiceManifest.xml
~~~

這個錯誤顯示程式碼封裝中遺漏服務資訊清單 **SetupEntryPoint** 中參考的 *MySetup.bat* 檔案。加入遺漏的檔案之後，應用程式驗證就會通過：

~~~
PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat

PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
True
PS D:\temp>
~~~

一旦應用程式正確封裝並通過驗證，就可供部署。

## 後續步驟

[部署與移除應用程式][10]

<!--Image references-->
[1]: ./media/service-fabric-application-model/application-model.jpg
[2]: ./media/service-fabric-application-model/vs-package-command.png
[Image1]: media/service-fabric-application-model/Service1.jpg
[Image2]: media/service-fabric-application-model/Service2.jpg

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md

 

<!---HONumber=July15_HO2-->