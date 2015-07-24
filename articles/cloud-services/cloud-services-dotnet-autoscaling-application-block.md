<properties 
	pageTitle="使用自動調整應用程式區塊 (.NET) - Azure" 
	description="了解如何使用 Azure 的自動調整應用程式。程式碼範例是以 C# 撰寫並使用 .NET API。" 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="squillace" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/18/2015" 
	ms.author="rasquill"/>







# 如何使用自動調整應用程式區塊

本指南將示範如何從 [Microsoft Enterprise Library 5.0 Integration Pack for Azure][] 使用自動調整應用程式區塊執行一般案例。這些範例均以 C# 撰寫並使用 .NET API。涵蓋的案例包括**主控區塊**、**使用限制規則**和**使用重新啟動規則**。如需自動調整應用程式區塊的詳細資訊，請參閱[後續步驟][]一節。

## 目錄

[何謂自動調整應用程式區塊？][] [概念][] [從目標 Azure 應用程式收集效能計數器資料][] [設定自動調整應用程式區塊的主機應用程式][] [作法：具現化和執行自動調整器][] [作法：定義您的服務模型][] [作法：定義您的自動調整規則][] [作法：設定自動調整應用程式區塊][] [後續步驟][]

## <a id="WhatIs"> </a>何謂自動調整應用程式區塊？

自動調整應用程式區塊可以根據您專為應用程式所定義的規則，自動調整 Azure 應用程式。您可以使用這些規則，來協助 Azure 應用程式維護其輸送量，以回應其工作負載中變更，同時控制與主控 Azure 中的應用程式相關聯的成本。除了增加或減少應用程式中的角色執行個體數目來進行調整外，區塊還可讓您使用其他調整動作，例如節流應用程式內的特定功能，或使用自定的動作。

您可以選擇主控 Azure 角色或內部部署應用程式中的區塊。

自動調整應用程式區塊是 [Microsoft Enterprise Library 5.0 Integration Pack for Azure][] 的一部分。

## <a id="Concepts"> </a>概念

在下圖中，綠色線顯示兩天內 Azure 角色的執行中執行個體數目的繪圖。執行個體數目會在一段時間後自動變更，以回應一組自動調整規則。

![diagram of sample autoscaling](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling01.png)

區塊會使用兩種規則，來定義應用程式的自動調整行為：

-   **限制規則：**若要設定執行個體數目的上限和下限，比方說，在每個早上 8:00 與 10:00 之間，您想要最少四個，最多八個執行個體，則可以使用**限制規則**。在圖解中，紅色線和藍色線代表限制規則。例如，在圖解中的點 **A**，角色執行個體的數目下限從二升高至四，以便可在此時容納預期增加的應用程式工作負載。在圖解中的點 **B**，避免角色執行個體的數目上升超過五，以便可以控制應用程式的執行成本。

-   **重新啟動規則：**若要讓角色執行個體的數目可以變更，以回應需求中不可預期的變更，請使用**重新啟動規則**。在圖解的點 **C**，區塊會自動將角色執行個體的數目從四減少至三，以回應工作負載的減少。在點 **D**，區塊偵測到工作負載增加，並自動將執行中的角色執行個體數目從三增加至四。

區塊會將其組態設定儲存在兩個存放區中：

-   **規則存放區：**規則存放區會為您保存商業組態；您已對 Azure 應用程式定義的所有自動調整規則的清單。此存放區通常為 XML 檔案，位於自動調整應用程式區塊可以讀取它的位置，例如，位於 Azure Blob 儲存體或位於本機檔案。

-   **伺服器資訊存放區：**服務資訊存放區會存放您的作業組態，也就是 Azure 應用程式的服務模型。此服務模型包括 Azure 應用程式的所有相關資訊 (例如名稱和儲存體帳戶詳細資料)，區塊需要這些資訊，才能從目標 Azure 應用程式收集資料點，並執行調整作業。

## <a id="PerfCounter"> </a>從目標 Azure 應用程式收集效能計數器資料

重新啟動規則可以使用角色中的效能計數器資料，作為規則定義的一部分。例如，重新啟動規則可能監視 Azure 角色的 CPU 使用率，以判定區塊是否應該初始化調整作業。區塊會從 Azure 儲存體中名稱為 **WADPerformanceCountersTable** 的 Azure 診斷資料表讀取效能計數器資料。

依預設，Azure 不會將效能計數器資料寫入 Azure 儲存體中的 Azure 診斷資料表。因此，您應該修改需要從中收集效能計數器資料的角色，以儲存此資料。如需如何在應用程式中啟用效能計數器的詳細資料，請參閱[在 Azure 中使用效能計數器][]。

## <a id="CreateHost"> </a>設定自動調整應用程式區塊的主機應用程式

您可以利用 Azure 角色或內部部署應用程式主控自動調整應用程式區塊。通常，從您要自動調整的目標應用程式中，利用個別應用程式主控自動調整應用程式區塊。本節提供如何設定主機應用程式的指導方針。

### 取得自動調整應用程式區塊 NuGet 套件

可在 Visual Studio 專案中使用自動調整應用程式區塊前，您將需要取得自動調整應用程式區塊二進位檔，並在專案新增中它們的參考。NuGet Visual Studio 擴充功能可讓您輕易地安裝和更新 Visual Studio 和 Visual Web Developer 中的程式庫和工具。自動調整應用程式區塊 NuGet 套件是取得自動調整應用程式區塊 API 的最簡單方法。如需 **NuGet** 的詳細資訊，以及如何安裝和使用 **NuGet** Visual Studio 擴充功能的詳細資訊，請參閱 [NuGet][] 網站。

一旦安裝了 NuGet Package Manager，若要在應用程式中安裝自動調整 NuGet 套件，請執行下列動作：

1.  開啟 [NuGet Package Manager 主控台] 視窗。在 [工具] 功能表中，依序選取 [Library Package Manager]、[Package Manager Console]。

2.  在 NuGet Package Manager 主控台視窗中輸入下列命令：

        PM> Install-Package EnterpriseLibrary.WindowsAzure.Autoscaling

安裝 NuGet 更新，即可利用您使用自動調整應用程式區塊時所需的所有必要組件和參考來更新專案。您的專案現在包括 XML 結構描述檔案，用於自動調整規則定義和自動調整服務資訊。專案現在也包括 readme 檔案，其中包含自動調整應用程式區塊的重要資訊：

![files configured by autoscaling NuGet package](./media/cloud-services-dotnet-autoscaling-application-block/auotscaling02.png)


### 將目標 Framework 設定為 .NET Framework 4

您的專案必須以 .NET Framework 4 為目標。若要變更或驗證目標架構：

1.  在 [方案總管] 中，於專案名稱上按一下滑鼠右鍵，然後選取 [內容]。

2.  在內容視窗的 [應用程式] 索引標籤中，確定目標架構設為 [.NET Framework 4]。

	![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling03.png)


### 新增命名空間參考

將下列程式碼命名空間宣告，新增至您想要在其中以程式設計方式存取自動調整應用程式區塊之任何 C# 檔案內的頂端：

    using Microsoft.Practices.EnterpriseLibrary.Common.Configuration;
    using Microsoft.Practices.EnterpriseLibrary.WindowsAzure.Autoscaling;

## <a id="Instantiate"> </a>作法：具現化和執行自動調整器

使用 **IServiceLocator.GetInstance** 方法，來具現化自動調整器，然後呼叫 **Autoscaler.Start** 方法來執行**自動調整器**。

    Autoscaler scaler =
        EnterpriseLibraryContainer.Current.GetInstance<Autoscaler>();
    scaler.Start();

## <a id="DefineServiceModel"> </a>作法：定義您的服務模型

通常，您會將服務模型 (Azure 環境的描述，其中包括訂用帳戶、託管服務、角色和儲存體帳戶的相關資訊) 儲存在 XML 檔案中。您可在專案的 **AutoscalingServiceModel.xsd** 檔案中找到此 XML 檔案的結構描述複本。在 Visual Studio 中，當您編輯服務模型 XML 檔案時，此結構描述會提供 Intellisense 和驗證。

在專案中建立一個稱為 **services.xml** 的新 XML 檔案。

在 Visual Studio 中，您必須確定服務模型檔案複製至輸出資料夾。作法：

1.  在檔案上按一下滑鼠右鍵，然後選取 [內容]。

2.  在 [內容] 窗格內，將 [複製到輸出目錄] 值設為 [永遠複製]。

    ![Set Copy to Output Directory value](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling04.png)


	下列程式碼範例顯示 **services.xml** 檔案中的範例服務模型：

    <?xml version="1.0" encoding="utf-8" ?> <serviceModel xmlns="http://schemas.microsoft.com/practices/2011/entlib/autoscaling/serviceModel"> <subscriptions> <subscription name="[subscriptionname]"
                      certificateThumbprint="[managementcertificatethumbprint]"
                      subscriptionId="[subscriptionid]"
                      certificateStoreLocation="CurrentUser"
                      certificateStoreName="My"> <services> <service dnsPrefix="[hostedservicednsprefix]" slot="Staging"> <roles> <role alias="AutoscalingApplicationRole"
                      roleName="[targetrolename]"
                      wadStorageAccountName="targetstorage"/> </roles> </service> </services> <storageAccounts> <storageAccount alias="targetstorage"
              connectionString="DefaultEndpointsProtocol=https;AccountName=[storageaccountname];AccountKey=[storageaccountkey]"> </storageAccount> </storageAccounts> </subscription> </subscriptions> </serviceModel>

您必須將方括弧中的值取代為環境和目標應用程式特有的值。若要找出其中多個值，您將需要登入 [Azure 管理入口網站][]。

登入管理入口網站。

-   **[subscriptionname]:** 選擇一個用以參照 Azure 訂用帳戶的易記名稱，該訂用帳戶包含您要在其中使用自動調整的應用程式 。

-   **[subscriptionid]:** Azure 訂用帳戶的唯一 ID，該訂用帳戶包含您要在其中使用自動調整的應用程式。

    1.  在 Azure 管理入口網站中，按一下 [**雲端服務**]。

    2.  在雲端服務清單中，按一下主控您要在其中使用自動調整之應用程式的服務。右邊的 [快速瀏覽] 窗格將顯示 [訂閱識別碼]。

        ![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling05.png)

  
	-   **[hostedservicednsprefix]:** 您要在其中使用自動調整之託管服務的 DNS 前置詞。

    1.  在 Azure 管理入口網站中，按一下 [**雲端服務**]。

    2.  在雲端服務清單中，找出主控您要在其中使用自動調整之應用程式的服務。雲端服務的名稱為 **DNS Prefix**。

        ![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling06.png)
 
	-   **[targetrolename]:** 自動調整規則之目標的角色名稱。

    1.  在 Azure 管理入口網站中，按一下 [**雲端服務**]。

    2.  在雲端服務清單中，按一下主控您要在其中使用自動調整之應用程式的服務，然後按一下 [執行個體]。**[角色*] 資料欄顯示目標角色的名稱。

        ![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling07.png)


	-   **[storageaccountname]** 和 **[storageaccountkey]:** 用於目標 Azure 應用程式的 Azure 儲存體帳戶名稱。

    1.  在 Azure 管理入口網站中，按一下 [**儲存體**]。

    2.  在儲存體帳戶的清單中，選取您要使用的儲存體帳戶。[名稱] 資料欄將顯示 [名稱]。

    3.  按一下畫面底端的 [管理金鑰] 按鈕，以取得主要存取金鑰。

        ![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling08.png)
  
 
	-   **[managementcertificatethumbprint]:** 管理憑證的**指紋**，區塊將使用此指紋保護目標應用程式之調整要求的安全。

    1.  在 Azure 管理入口網站中，按一下 [**設定**]。

    2.  [指紋] 資料欄將顯示 [指紋]。

        ![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling09.png)
 

若要深入了解服務模型檔案的內容，請參閱[儲存您的服務資訊資料][]。

## <a id="DefineAutoscalingRules"> </a>作法：定義您的自動調整規則

通常，您可以將控制目標應用程式中角色執行個體數目的自動調整規則儲存在 XML 檔案中。您可在專案的 **AutoscalingRules.xsd** 檔案中找到此 XML 檔案的結構描述複本。在 Visual Studio 中，當您編輯 XML 檔案時，此結構描述會提供 Intellisense 和驗證。

在專案中建立一個名稱為 **rules.xml** 的新 XML 檔案。

在 Visual Studio 中，您必須確定規則檔案複製至輸出資料夾。作法：

1.  在檔案上按一下滑鼠右鍵，然後選取 [內容]。

2.  在 [內容] 窗格內，將 [複製到輸出目錄] 值設為 [永遠複製]。

下列程式碼範例顯示 **rules.xml** 檔案中設定的範例規則：

    <?xml version="1.0" encoding="utf-8" ?>
    <rules xmlns="http://schemas.microsoft.com/practices/2011/entlib/autoscaling/rules">
      <constraintRules>
        <rule name="default" enabled="true" rank="1" description="The default constraint rule">
          <actions>
            <range min="2" max="6" target="AutoscalingApplicationRole"/>
          </actions>
        </rule>
      </constraintRules>
      <reactiveRules>
        <rule name="ScaleUpOnHighUtilization" rank="10" description="Scale up the web role" enabled="true" >
          <when>
            <any>
               <greaterOrEqual operand="WebRoleA_CPU_Avg_5m" than="60"/>
            </any>
          </when>
          <actions>
            <scale target="AutoscalingApplicationRole" by="1"/>
          </actions>
        </rule>
        <rule name="ScaleDownOnLowUtilization" rank="10" description="Scale up the web role" enabled="true" >
          <when>
            <all>
              <less operand="WebRoleA_CPU_Avg_5m" than="60"/>
            </all>
          </when>
          <actions>
            <scale target="AutoscalingApplicationRole" by="-1"/>
          </actions>
        </rule>
      </reactiveRules>
      <operands>
        <performanceCounter alias="WebRoleA_CPU_Avg_5m"
          performanceCounterName="\Processor(_Total)% Processor Time"
          source ="AutoscalingApplicationRole"
          timespan="00:05:00" aggregate="Average"/>
      </operands>
    </rules>

在此範例中，有三個自動調整規則 (一個**限制規則**和兩個**重新啟動規則**)，在名稱為 **AutoscalingApplicationRole** (其為**服務模型**中定義的角色別名) 上操作：

-   限制規則永遠作用中，並將角色執行個體的數目下限設為二，而將角色執行個體的數目上限設為六。

-   這兩個重新啟動規則會使用名稱為 **WebRoleA_CPU_Avg_5m** 的**運算元**，計算名稱為 **AutoscalingApplicationRole** 的 Azure 角色最後五分鐘的平均 CPU 使用率。 此角色定義在**服務模型**中。

-   如果過去五分鐘的平均 CPU 使用率大於或等於 60%，則名稱為 **ScaleUpOnHighUtilization** 的重新啟動規則會增加目標角色的執行個體計數。

-   如果過去五分鐘的平均 CPU 使用率小於 60%，則名稱為 **ScaleDownOnLowUtilization** 的重新啟動規則會減少目標角色的執行個體計數。

## <a id="Configure"> </a>作法：設定自動調整應用程式區塊

在定義了服務模型和自動調整規則後，您必須將自動調整應用程式區塊設定成使用它們。此作業組態資訊儲存在應用程式組態檔中。

依預設，自動調整應用程式區塊預期自動調整規則和服務模型儲存在 Azure Blob 中。在此範例中，您將區塊設定成從本機檔案系統中載入它們。

### 在主機應用程式中設定自動調整應用程式區塊

1.  在方案總管的 **App.config** 檔案上按一下滑鼠右鍵，然後按一下 [Edit Configuration File]。

2.  在 [**區塊**] 功能表中，按一下 [**新增自動調整設定**]：![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling10.png)
  
3.  展開 [**自動調整設定**]，然後按一下 [**資料點存放區儲存體帳戶**] 旁邊的省略符號 (...)、新增 Azure 儲存體帳戶的 [**帳戶名稱**] 和 [**帳戶金鑰**]，區塊將在此儲存體帳戶儲存其收集的資料點 (如果不確定要在哪裡尋找這些值，請參閱[作法：定義您的服務模型][])，然後按一下 [**確定**]：

	![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling11.png)

4.  展開 [Autoscaling Settings] 區段，以顯示 [Rules Store] 和 [Service Information Store] 區段。依預設，它們會設定成使用 Azure Blob 儲存體：![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling12.png)


5.  按一下 [Rules Store] 旁邊的加號 (+)、指向 [Set Rules Store]，然後按一下 [Use Local File Rules Store]，再按一下 [是]。

6.  在 [檔案名稱] 方塊中，輸入 **rules.xml**。這是包含自動調整規則的檔案名稱：![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling13.png)


7.  按一下 [Service Information Store] 旁邊的加號 (+)、指向 [Set Service Information Store]，然後按一下 [Use Local File Service Information Store]，再按一下 [是]。

8.  在 [檔案名稱] 方塊中，輸入 **services.xml**。這是包含自動調整規則的檔案名稱：![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling14.png)


9.  在 [Enterprise Library Configuration] 視窗的 [檔案] 功能表上，按一下[儲存] 以儲存您的組態變更。然後，在 [Enterprise Library Configuration] 視窗的 [檔案] 功能表上，按一下 [結束]。

若要取得自動調整應用程式區塊正在執行之動作的詳細資訊，您需要擷取其寫入的記錄訊息。例如，如果您是在主控台應用程式中主控區塊，則可在 Visual Studio 的 [輸出] 視窗中檢視記錄訊息。下節顯示如何設定此行為。

### 設定如何記錄在自動調整應用程式區塊主機應用程式中

1.  在 Visual Studio 中，按兩下方案總管中的 **App.config** 檔案，以在編輯器中開啟它。然後，新增 **system.diagnostics** 區段，如下列範例所示：

        <?xml version="1.0" encoding="utf-8" ?>
        <configuration>
          ...
          <system.diagnostics>
            <sources>
              <sourcename="Autoscaling General" switchName="SourceSwitch" switchType="System.Diagnostics.SourceSwitch" />
              <sourcename="Autoscaling Updates" switchName="SourceSwitch" switchType="System.Diagnostics.SourceSwitch" />
            </sources>
            <switches>
              <addname="SourceSwitch" value="Verbose, Information, Warning, Error, Critical" />
            </switches>
          </system.diagnostics>
        </configuration>

2.  儲存您的變更。

您現在可以執行自動調整應用程式區塊主機主控台應用程式，並觀察自動調整規則如何使用您的目標 Azure 應用程式。當執行主機主控台應用程式時，您應該在 Visual Studio 的 [輸出] 視窗中看到如下的訊息。這些記錄訊息可協助您了解區塊的行為。例如，它們指出區塊符合哪些規則，以及區塊正在採取哪些動作。

    Autoscaling General Verbose: 1002 : Rule match.
    [BEGIN DATA]{"EvaluationId":"6b27dfa0-b671-44a3-adf1-bb1e0b7c3726",
    "MatchingRules":[{"RuleName":"default","RuleDescription":"The default constraint rule",
    "Targets":["AutoscalingApplicationRole"]},{"RuleName":"ScaleUp","RuleDescription":"Scale up the web role",
    "Targets":[]},{"RuleName":"ScaleDown","RuleDescription":"Scale up the web role","Targets":[]}]}

    Autoscaling Updates Verbose: 3001 : The current deployment configuration for a hosted service is about to be checked
    to determine if a change is required (for role scaling or changes to settings).
    [BEGIN DATA]{"EvaluationId":"6b27dfa0-b671-44a3-adf1-bb1e0b7c3726",
    "HostedServiceDetails":{"Subscription":"AutoscalingHowTo","HostedService":"autoscalingtarget",
    "DeploymentSlot":"Staging"},"ScaleRequests":{"AutoscalingApplicationRole":{"Min":2,"Max":6,"AbsoluteDelta":0,
    "RelativeDelta":0,"MatchingRules":"default"}},"SettingChangeRequests":{}}

    Autoscaling Updates Verbose: 3003 : Role scaling requests for hosted service about to be submitted.
    [BEGIN DATA]{"EvaluationId":"6b27dfa0-b671-44a3-adf1-bb1e0b7c3726",
    "HostedServiceDetails":{"Subscription":"AutoscalingHowTo","HostedService":"autoscalingtarget",
    "DeploymentSlot":"Staging"},
    "ScaleRequests":{"AutoscalingApplicationRole":
    {"Min":2,"Max":6,"AbsoluteDelta":0,"RelativeDelta":0,"MatchingRules":"default"}},
    "SettingChangeRequests":{},"InstanceChanges":{"AutoscalingApplicationRole":{"CurrentValue":1,"DesiredValue":2}},
    "SettingChanges":{}}

    Autoscaling Updates Information: 3002 : Role configuration changes for deployment were submitted.
    [BEGIN DATA]{"EvaluationId":"6b27dfa0-b671-44a3-adf1-bb1e0b7c3726",
    "HostedServiceDetails":{"Subscription":"AutoscalingHowTo","HostedService":"autoscalingtarget",
    "DeploymentSlot":"Staging"},"ScaleRequests":{"AutoscalingApplicationRole":{"Min":2,"Max":6,"AbsoluteDelta":0,
    "RelativeDelta":0,"MatchingRules":"default"}},"SettingChangeRequests":{},
    "InstanceChanges":{"AutoscalingApplicationRole":{"CurrentValue":1,"DesiredValue":2}},
    "SettingChanges":{},"RequestID":"f8ca3ada07c24559b1cb075534f02d44"}

## <a id="NextSteps"> </a>後續步驟

既然已了解使用自動調整應用程式區塊的基礎概念，請遵循下列連結，以了解如何實作更複雜的自動調整案例：

-   [在背景工作角色中主控自動調整應用程式區塊][]
-   [實作節流行為][]
-   [了解規則等級和重新調整][]
-   [延伸並修改自動調整應用程式區塊][]
-   [使用最佳化穩定器來避免高頻率振盪和最佳化成本][]
-   [使用通知和手動調整][]
-   [定義調整群組][]
-   [使用 WASABiCmdlets 透過 Windows PowerShell 操作區塊][]
-   [Enterprise Library 5.0 Integration Pack for Azure 開發人員指南][]
-   [Sage 如何使用自動調整減少 Azure 主控成本][]
-   [利用 Azure 上的自動調整減少 TechNet 和 MSDN 主控成本和環境影響][]

  [Microsoft Enterprise Library 5.0 Integration Pack for Azure]: http://go.microsoft.com/fwlink/?LinkID=235134
  [後續步驟]: #NextSteps
  [何謂自動調整應用程式區塊？]: #WhatIs
  [概念]: #Concepts
  [從目標 Azure 應用程式收集效能計數器資料]: #PerfCounter
  [設定自動調整應用程式區塊的主機應用程式]: #CreateHost
  [作法：具現化和執行自動調整器]: #Instantiate
  [作法：定義您的服務模型]: #DefineServiceModel
  [作法：定義您的自動調整規則]: #DefineAutoscalingRules
  [作法：設定自動調整應用程式區塊]: #Configure
  [在 Azure 中使用效能計數器]: http://www.windowsazure.com/develop/net/common-tasks/performance-profiling/
  [NuGet]: http://nuget.org/
  [Azure 管理入口網站]: http://manage.windowsazure.com
  [儲存您的服務資訊資料]: http://msdn.microsoft.com/library/hh680878(PandP.50).aspx
  [在背景工作角色中主控自動調整應用程式區塊]: http://msdn.microsoft.com/library/hh680914(PandP.50).aspx
  [實作節流行為]: http://msdn.microsoft.com/library/hh680896(PandP.50).aspx
  [了解規則等級和重新調整]: http://msdn.microsoft.com/library/hh680923(PandP.50).aspx
  [延伸並修改自動調整應用程式區塊]: http://msdn.microsoft.com/library/hh680889(PandP.50).aspx
  [使用最佳化穩定器來避免高頻率振盪和最佳化成本]: http://msdn.microsoft.com/library/hh680951(PandP.50).aspx
  [使用通知和手動調整]: http://msdn.microsoft.com/library/hh680885(PandP.50).aspx
  [定義調整群組]: http://msdn.microsoft.com/library/hh680902(PandP.50).aspx
  [使用 WASABiCmdlets 透過 Windows PowerShell 操作區塊]: http://msdn.microsoft.com/library/hh680938(PandP.50).aspx
  [Enterprise Library 5.0 Integration Pack for Azure 開發人員指南]: http://msdn.microsoft.com/library/hh680949(PandP.50).aspx
  [Sage 如何使用自動調整減少 Azure 主控成本]: http://msdn.microsoft.com/library/jj838716(PandP.50).aspx
  [利用 Azure 上的自動調整減少 TechNet 和 MSDN 主控成本和環境影響]: http://msdn.microsoft.com/library/jj838718(PandP.50).aspx
 

<!---HONumber=62-->