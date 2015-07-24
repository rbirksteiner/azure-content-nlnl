<properties 
	pageTitle="在 Azure 中使用 TFS 來連續傳遞雲端服務" 
	description="了解如何設定 Azure 雲端應用程式的連續傳遞。MSBuild 命令列陳述式和 PowerShell 指令碼的程式碼範例。" 
	services="cloud-services" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/29/2015" 
	ms.author="kempb"/>

# Azure 中雲端服務的連續傳遞

本文所述的程序顯示如何為 Azure 雲端應用程式設定連續傳遞。此程序可讓您自動建立套件，並在每次程式碼簽入時將套件部署到 Azure。本文所述的套件建置程序等同於 Visual Studio 中的 [封裝] 命令，而發佈步驟等同於 Visual Studio 中的 [發行] 命令。文中會說明使用 MSBuild 命令列陳述式與指令碼來建置伺服器的方法，同時也會示範如何選擇性設定 Visual StudioTeam Foundation Server - Team Build 定義來使用 MSBuild 命令及 PowerShell 指令碼。您可根據自己的組建環境及 Azure 目標環境自訂此程序。

此動作也可以用 Visual Studio Online (託管於 Azure 中的 TFS 版本) 來執行，這樣會變得更容易。如需詳細資訊，請參閱[使用 Visual Studio Online 連續傳遞至 Azure][]。

開始之前，您應該先從 Visual Studio 發佈應用程式。如此可確保當您嘗試將發佈程序自動化時，所有資源皆可用並已初始化。

## 步驟 1：設定組建伺服器

您必須先在組建伺服器上安裝必要的軟體與工具，才能使用 MSBuild 建立 Azure 套件。

組建伺服器上不需要安裝 Visual Studio。若要使用 Team Foundation Build Service 來管理組建伺服器，請遵循 [Team Foundation Build Service][] 文件。

1.  在組建伺服器上，安裝 [.NET Framework 4][]、[.NET Framework 4.5][] 或 [.NET Framework 4.5.2][]，其包括 MSBuild。
2.  安裝 [Azure 製作工具][] (尋找 MicrosoftAzureAuthoringTools-x86.msi 或 MicrosoftAzureAuthoringTools-x64.msi，視組建伺服器的處理器而定)。舊版檔案的檔案名稱中可能有 WindowsAzure。
3. 安裝 [Azure 程式庫][] (尋找 MicrosoftAzureLibsForNet-x86.msi 或 MicrosoftAzureLibsForNet-x64.msi)。
4.  將 Microsoft.WebApplication.targets 檔案從 Visual Studio 安裝複製至組建伺服器。在已安裝 Visual Studio 的電腦上，此檔案位於目錄 C:\Program Files(x86)\MSBuild\Microsoft\VisualStudio\v11.0\WebApplications (Visual Studio 2013 為 WebApplications V12.0)。您應該將它複製至組建伺服器上的相同目錄。
5.  安裝 [Azure Tools for Visual Studio][]。尋找 MicrosoftAzureTools.VS110.exe 來建置 Visual Studio 2012 專案，尋找 MicrosoftAzureTools.VS120.exe 來建置 Visual Studio 2013 專案，以及尋找 MicrosoftAzureTools.VS140.exe 來建置 Visual Studio 2015 預覽專案。

## 步驟 2：使用 MSBuild 命令建置封裝

本節說明如何建構 MSBuild 命令來建置 Azure 套件。在組建伺服器上執行這個步驟，確認一切都已正確設定，且 MSBuild 命令會執行您要它執行的動作。您可以將此命令列新增至組建伺服器上的現有組建指令碼，也可以在 TFS 組建定義中使用此命令列 (說明於下節)。如需命令列參數及 MSBuild 的詳細資訊，請參閱 [MSBuild 命令列參考][]。

1.  如果組建伺服器上已安裝 Visual Studio，請按一下 [開始]、[所有程式]，然後在 [Visual Studio Tools] 資料夾中找出並按一下 [Visual Studio Commmand Prompt]。

    如果組建伺服器上未安裝 Visual Studio，請開啟命令提示字元，並確定可在路徑上存取 MSBuild.exe。MSBuild 會與 .NET Framework 一起安裝在路徑 %WINDIR%\Microsoft.NET\Framework*Version* 中。例如，若要在已安裝 .NET Framework 4 時，將 MSBuild.exe 新增至 PATH 環境變數，請在命令提示字元中輸入下列命令：

        set PATH=%PATH%;"C:\Windows\Microsoft.NET\Framework\v4.0.30319"

2.  在命令提示字元中，瀏覽至包含您要建置之 Azure 專案檔案的資料夾。

3.  搭配 /target:Publish 選項執行 msbuild，如下列範例所示：

        MSBuild /target:Publish

    此選項可以縮寫為 /t:Publish。當已安裝 Azure SDK 時，MSBuild 中的 /t:Publish 選項不應該與 Visual Studio 中的 [發行] 命令混淆。/t:Publish 選項只會建置 Azure 套件。其並不會如 Visual Studio 中的 [發行] 命令一樣部署套件。

    (選擇性) 您可以指定專案名稱作為 MSBuild 參數。如果未指定，則會使用目前目錄。如需 MSBuild 命令列選項的詳細資訊，請參閱 [MSBuild 命令列參考][1]。

4.  尋找輸出。依預設，此命令會建立相對於專案根資料夾的目錄，例如 *ProjectDir*\bin*Configuration*\app.publish\。當您建置 Azure 專案時，會產生兩個檔案，即套件檔本身及伴隨的組態檔：

    -   Project.cspkg
    -   ServiceConfiguration.*TargetProfile*.cscfg

    依預設，每個 Azure 專案都會包含一個服務組態檔 (.cscfg 檔) 用於本機 (偵錯) 組建，以及另一個服務組態檔用於 (預備或生產) 雲端組建，但是您可以視需要新增或移除服務組態檔。在 Visual Studio 內建置套件時，系統將問您要隨套件包含哪個服務組態檔。

5.  指定服務組態檔。使用 MSBuild 來建置套件時，預設會包含本機服務組態檔。若要包含不同的服務組態檔，請設定 MSBuild 命令的 TargetProfile 屬性，如下列範例所示：

        MSBuild /t:Publish /p:TargetProfile=Cloud

6.  指定輸出的位置。使用 /p:PublishDir=*Directory*\ 選項來設定路徑 (包括最後的反斜線分隔符號)，如下列範例所示：

        MSBuild /target:Publish /p:PublishDir=\myserver\drops\

    一旦建構並測試出適當的 MSBuild 命令列來建置專案並將它們結合為 Azure 套件，就可以將此命令新增至組建指令碼。如果您的組建伺服器使用自訂指令碼，則此程序將視您自訂建置流程的特性而定。如果您是使用 TFS 作為組建環境，則可以遵循下一步中的指示，將 Azure 套件新增至組建程序。

## 步驟 3：使用 TFS Team Build 建置封裝 (選用)

如果已設定 Team Foundation Server (TFS) 作為組建控制器，並設定組建伺服器作為 TFS 組建機器，則可以為 Azure 套件設定自動化組建。如需關於如何設定並使用 Team Foundation Server 作為組建系統的詳細資訊，請參閱 [了解 Team Foundation Build System][]。特別是，下列程序假設您已如組建伺服器[設定組建機器][]中所述來設定組建伺服器，同時您已建立 Team 專案，已在 Team 專案中建立雲端服務專案。

若要設定 TFS 來建置 Azure 套件，請執行下列步驟：

1.  在開發電腦上的 Visual Studio 中，於 [檢視] 功能表上，選取 [**Team Explorer**] 或選取 Ctrl+\、Ctrl+M。在 Team Explorer 視窗中，展開 [**組建**] 節點或選擇 [**組建**] 頁面，然後選擇 [**新增組建定義**]。

    ![][0]

2.  按一下 [觸發程序] 索引標籤，然後指定所需的條件來代表套件的組建時機。例如，指定 [連續整合]，會在每次發生原始檔控制簽入時建置套件。

3.	選擇 [**來源設定**] 索引標籤，然後確定您的專案資料夾列在 [**原始檔控制資料夾**] 資料行中，並且狀態為 [**作用中**]。

4.  選擇 [組建預設值] 索引標籤，然後在 [組建控制器] 下，確認組建伺服器的名稱無誤。同時，選擇 [將組建輸出複製至下列置放資料夾] 選項，並指定所需的置放位置。

5.  按一下 [處理序] 索引標籤。在 [處理序] 索引標籤上選擇預設範本，於 [**組建**] 下，選擇專案 (若尚未選取)，然後展開格線 [**組建**] 區段中的 [**進階**]。

6.  選擇 [MSBuild 引數]，然後依上面步驟 2 所述，設定適當的 MSBuild 命令列引數。例如，輸入 **/t:Publish /p:PublishDir=\myserver\drops** 以建置套件，並將套件檔複製至位置 \myserver\drops\：

    ![][2]

    **：**將檔案複製至公用共用，將可更輕鬆地手動從開發電腦部署套件。

5.  簽入專案的變更來測試組建步驟是否成功，或將新組建排入佇列。若要將新組建排入佇列，請在 [Team Explorer] 的 [**所有組建定義**] 上按一下滑鼠右鍵，然後選擇 [**將新組建排入佇列**]。

## 步驟 4：使用 PowerShell 指令碼發佈封裝

本節說明如何建構 Windows PowerShell 指令碼，以使用選用參數將雲端應用程式套件發佈至 Azure。呼叫此指令碼的時機可以是執行自訂組建自動化中的組建步驟之後。也可以從 Visual Studio TFS Team Build 中的「流程範本」工作流程活動中呼叫。

1.  安裝 [Azure PowerShell Cmdlet][] (0.6.1 版或更高版本)。在 Cmdlet 設定階段期間，請選擇安裝為嵌入式管理單元。請注意，此正式支援的版本會取代透過 CodePlex 提供的更舊版本 (這些舊版本的編號為 2.x.x)。

2.  使用 [開始] 功能表或 [開始] 頁面啟動 Azure PowerShell。如果以此方式啟動，則會載入 Azure PowerShell Cmdlet。

3.  在 PowerShell 提示中，輸入部分命令 Get-Azure，然後按 Tab 鍵來完成陳述式，以確認已載入 PowerShell Cmdlet。

    如果您重覆按 Tab 鍵，應該會看到各種 Azure PowerShell 命令。

4.  從 .publishsettings 檔匯入您的訂閱資訊，以確認可以連線至自己的 Azure 訂閱。

    Import-AzurePublishSettingsFile c:\scripts\WindowsAzure\default.publishsettings

    然後，提供以下命令

    Get-AzureSubscription

    如此會顯示訂閱的相關資訊。確認一切正確無誤。

4.  將[本文結尾][]提供的指令碼範本儲存至您的指令碼資料夾，如 c:\scripts\WindowsAzure**PublishCloudService.ps1**。

5.  檢閱指令碼的參數區段。新增或修改任何預設值。您永遠可以傳遞明確參數來覆寫這些值。

6.  確定訂閱中建立了可作為發佈指令碼之目標的有效雲端服務與儲存體帳戶。儲存體帳戶 (Blob 儲存) 將用來在建立部署期間上傳並暫時儲存部署套件與組態檔。

    -   若要建立新的雲端服務，您可以呼叫此指令碼或使用 Azure 管理入口網站。雲端服務名稱將作為完整網域名稱中的首碼，因此必須是唯一的。

            New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"

    -   若要建立新的儲存體帳戶，您可以呼叫此指令碼或使用 Azure 管理入口網站。儲存體帳戶名稱將作為完整網域名稱中的首碼，因此必須是唯一的。您可以嘗試使用與雲端服務相同的名稱。

            New-AzureStorageAccount -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"

7.  直接從 Azure PowerShell 呼叫指令碼，或將此指令碼接到主機組建自動化程序中，以在建置套件之後執行。

    **警告：**依預設，如果偵測到現有的部署，指令碼會一律加以刪除或取代。此為不得不的方式，因為如此一來，完全省去使用者互動過程的自動化程序才有辦法進行連續傳遞。

    **範例案例 1：**連續部署至服務的預備環境：

        PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Staging -serviceName mycloudservice -storageAccountName mystoragesaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

    此作業後面通常接著測試執行驗證及 VIP 交換。VIP 交換可以透過 Azure 管理入口網站或使用 Move-Deployment Cmdlet 來完成。

    **範例案例 2：**連續部署至專用測試服務的生產環境

        PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Production -enableDeploymentUpgrade 1 -serviceName mycloudservice -storageAccountName mystorageaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

    **遠端桌面：**

    如果已在 Azure 專案中啟用遠端桌面，則您需要執行更多一次性步驟，以確定正確的雲端服務會上傳至所有作為此指令碼之目標的雲端服務。

    尋找您的角色所預期收到的憑證指紋值。指紋值可在雲端組態檔 (也就是 ServiceConfiguration.Cloud.cscfg) 的 Certificates 區段中看到。在 Visual Studio 的 [遠端桌面組態] 對話方塊按一下 [顯示選項] 並檢視選取的憑證，也可在看到它。

        <Certificates>
              <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="C33B6C432C25581601B84C80F86EC2809DC224E8" thumbprintAlgorithm="sha1" />
        </Certificates>

    使用下列 Cmdlet 指令碼，上傳遠端桌面憑證作為一次性設定步驟：

        Add-AzureCertificate -serviceName <CLOUDSERVICENAME> -certToDeploy (get-item cert:\CurrentUser\MY<THUMBPRINT>)

    例如：

        Add-AzureCertificate -serviceName 'mytestcloudservice' -certToDeploy (get-item cert:\CurrentUser\MY\C33B6C432C25581601B84C80F86EC2809DC224E8

    或者，您也可以使用 Azure 管理入口網站，匯出憑證檔 PFX 與私密金鑰，並將憑證上傳至每個目標雲端服務。若要深入了解，請閱讀下列文章：[http://msdn.microsoft.com/library/windowsazure/gg443832.aspx][]。

    **升級部署以及刪除部署再新增部署**

    當未傳入任何參數，或明確傳遞了值 1 時，指令碼預設會執行升級部署 ($enableDeploymentUpgrade = 1)。對於單一執行個體，這具有比完整部署花費更少時間的優點。對於需要高可用性的執行個體，這也具有一邊升級部分執行個體，一邊留著部分執行個體繼續執行，以及您的 VIP 不會遭到刪除的優點。

    若要停用升級部署，可在指令碼中停用 ($enableDeploymentUpgrade = 0) 或傳遞 -enableDeploymentUpgrade 0 當作參數，如此會將指令碼行為改變為先刪除任何現有的部署，再建立新的部署。

    **警告：**依預設，如果偵測到現有的部署，指令碼會一律加以刪除或取代。此為不得不的方式，因為如此一來，完全省去使用者/作業員互動過程的自動化程序才有辦法進行連續傳遞。

## 步驟 5：使用 TFS Team Build 發佈封裝 (選用)

此步驟會將 TFS Team Build 接到步驟 4 中建立的指令碼，該指令碼負責處理將套件組建發佈至 Azure。這需要修改您的組建定義所使用的流程範本，使其在工作流程結束時執行 Publish 活動。Publish 活動會利用組建傳入的參數，執行 PowerShell 命令。所輸出的 MSBuild 目標與發佈指令碼將透過管道傳送至標準組建輸出。

1.  編輯負責連續部署的「組建定義」。

2.  選取 [處理序] 索引標籤。

3.	遵循[下列指示](http://msdn.microsoft.com/library/dd647551.aspx)，為建置流程範本新增活動專案、下載預設範本，將它新增至專案並簽入。為建置流程範本提供新名稱，例如 AzureBuildProcessTemplate。

3.  回到 [**處理序**] 索引標籤，並使用 [**顯示詳細資料**] 以顯示可用建置流程範本的清單。選擇 [**新增...**] 按鈕，然後巡覽至您剛才加入及簽入的專案。找出剛才建立的範本並選擇 [**確定**]。

4.  開啟選取的流程範本進行編輯。您可以在工作流程設計工具或在 XML 編輯器中直接開啟，以使用 XAML。

5.  在工作流程設計工具的引數索引標籤中，分行新增下列清單中的新引數。所有引數都應該具有 direction=In 及 type=String。這些會用來將組建定義中的參數傳到工作流程中，然後用來呼叫發佈指令碼。

        SubscriptionName
        StorageAccountName
        CloudConfigLocation
        PackageLocation
        Environment
        SubscriptionDataFileLocation
        PublishScriptLocation
        ServiceName

    ![][3]

    對應的 XAML 看起來如下：

        <Activity  _ />
          <x:Members>
            <x:Property Name="BuildSettings" Type="InArgument(mtbwa:BuildSettings)" />
            <x:Property Name="TestSpecs" Type="InArgument(mtbwa:TestSpecList)" />
            <x:Property Name="BuildNumberFormat" Type="InArgument(x:String)" />
            <x:Property Name="CleanWorkspace" Type="InArgument(mtbwa:CleanWorkspaceOption)" />
            <x:Property Name="RunCodeAnalysis" Type="InArgument(mtbwa:CodeAnalysisOption)" />
            <x:Property Name="SourceAndSymbolServerSettings" Type="InArgument(mtbwa:SourceAndSymbolServerSettings)" />
            <x:Property Name="AgentSettings" Type="InArgument(mtbwa:AgentSettings)" />
            <x:Property Name="AssociateChangesetsAndWorkItems" Type="InArgument(x:Boolean)" />
            <x:Property Name="CreateWorkItem" Type="InArgument(x:Boolean)" />
            <x:Property Name="DropBuild" Type="InArgument(x:Boolean)" />
            <x:Property Name="MSBuildArguments" Type="InArgument(x:String)" />
            <x:Property Name="MSBuildPlatform" Type="InArgument(mtbwa:ToolPlatform)" />
            <x:Property Name="PerformTestImpactAnalysis" Type="InArgument(x:Boolean)" />
            <x:Property Name="CreateLabel" Type="InArgument(x:Boolean)" />
            <x:Property Name="DisableTests" Type="InArgument(x:Boolean)" />
            <x:Property Name="GetVersion" Type="InArgument(x:String)" />
            <x:Property Name="PrivateDropLocation" Type="InArgument(x:String)" />
            <x:Property Name="Verbosity" Type="InArgument(mtbw:BuildVerbosity)" />
            <x:Property Name="Metadata" Type="mtbw:ProcessParameterMetadataCollection" />
            <x:Property Name="SupportedReasons" Type="mtbc:BuildReason" />
            <x:Property Name="SubscriptionName" Type="InArgument(x:String)" />
            <x:Property Name="StorageAccountName" Type="InArgument(x:String)" />
            <x:Property Name="CloudConfigLocation" Type="InArgument(x:String)" />
            <x:Property Name="PackageLocation" Type="InArgument(x:String)" />
            <x:Property Name="Environment" Type="InArgument(x:String)" />
            <x:Property Name="SubscriptionDataFileLocation" Type="InArgument(x:String)" />
            <x:Property Name="PublishScriptLocation" Type="InArgument(x:String)" />
            <x:Property Name="ServiceName" Type="InArgument(x:String)" />
          </x:Members>

          <this:Process.MSBuildArguments>

6.  在 [在代理程式上執行] 結尾新增一個順序：

    1.  先新增 If Statement 活動，以檢查是否有有效指令碼檔案。將條件設為此值：

            Not String.IsNullOrEmpty(PublishScriptLocation)

    2.  在「If 陳述式」的 Then 案例中，新增 Sequence 活動。將顯示名稱設為 'Start publish'

    3.  在仍選取著 Start publish 順序時，在工作流程設計工具的變數索引標籤中分行新增下列清單中的新變數。所有變數都應該具有 Variable type =String 及 Scope=Start publish。這些會用來將組建定義中的參數傳到工作流程中，然後用來呼叫發佈指令碼。

        -   SubscriptionDataFilePath，型別為 String

        -   PublishScriptFilePath，型別為 String

            ![][4]

    4.  如果您使用 TFS 2012 或更早版本，請在新序列的開頭新增 ConvertWorkspaceItem 活動。如果您使用 TFS 2013 或更新版本，請在新序列的開頭新增 GetLocalPath 活動。針對 ConvertWorkspaceItem，請依以下方式設定內容：Direction=ServerToLocal、DisplayName='Convert publish script filename'、Input=' PublishScriptLocation'、Result='PublishScriptFilePath'、Workspace='Workspace'。針對 GetLocalPath 活動，請將內容 IncomingPath 設定為 'PublishScriptLocation'，以及將 Result 設定為 'PublishScriptFilePath'。此活動會將發佈指令碼的路徑從 TFS 伺服器位置 (如果適用的話) 轉換為標準本機磁碟路徑。

    5.  如果您使用 TFS 2012 或更早版本，請在新序列的結尾新增另一個 ConvertWorkspaceItem 活動。Direction=ServerToLocal、DisplayName='Convert subscription filename'、Input=' SubscriptionDataFileLocation'、Result= 'SubscriptionDataFilePath'、Workspace='Workspace'。如果您使用 TFS 2013 或更新版本，請新增另一個 GetLocalPath。IncomingPath='SubscriptionDataFileLocation'，以及 Result='SubscriptionDataFilePath'。

    6.  在新順序的結尾新增 InvokeProcess 活動。此活動會利用組建定義傳入的引數呼叫 PowerShell.exe。

        1.  Arguments = String.Format(" -File ""{0}"" -serviceName {1} -storageAccountName {2} -packageLocation ""{3}"" -cloudConfigLocation ""{4}"" -subscriptionDataFile ""{5}"" -selectedSubscription {6} -environment ""{7}""", PublishScriptFilePath, ServiceName, StorageAccountName, PackageLocation, CloudConfigLocation, SubscriptionDataFilePath, SubscriptionName, Environment)

        2.  DisplayName = Execute publish script

        3.  FileName = "PowerShell" (包括引號)

        4.  OutputEncoding= System.Text.Encoding.GetEncoding(System.Globalization.CultureInfo.InstalledUICulture.TextInfo.OEMCodePage)

    7.  在 InvokeProcess 的 [**處理標準輸出**] 區段文字方塊中，將文字方塊值設為 'data'。這是要用來儲存標準輸出資料的變數。

    8.  緊接在 [**處理標準輸出**] 區段下新增 WriteBuildMessage 活動。設定 Importance = 'Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High'、Message='data'。如此可確保指令碼的標準輸出會寫入至組建輸出。

    9.  在 InvokeProcess 的 [**處理錯誤輸出**] 區段文字方塊中，將文字方塊值設為 'data'。這是要用來儲存標準錯誤資料的變數。

    10. 緊接在 [**處理錯誤輸出**] 區段下新增 WriteBuildError 活動。設定 Message='data'。如此可確保指令碼的標準錯誤會寫入至組建錯誤輸出。

	11. 更正由藍色驚嘆號指出的任何錯誤。以滑鼠暫留在驚嘆號上，以取得錯誤的相關提示。儲存工作流程以清除錯誤。

    在設計工具中，發佈工作流程活動的最終結果將看起來如下：

    ![][5]

    在 XAML 中，發佈工作流程活動的最終結果將看起來如下：

		<If Condition="[Not String.IsNullOrEmpty(PublishScriptLocation)]" sap2010:WorkflowViewState.IdRef="If_1">
	        <If.Then>
	          <Sequence DisplayName="Start Publish" sap2010:WorkflowViewState.IdRef="Sequence_4">
	            <Sequence.Variables>
	              <Variable x:TypeArguments="x:String" Name="SubscriptionDataFilePath" />
	              <Variable x:TypeArguments="x:String" Name="PublishScriptFilePath" />
	            </Sequence.Variables>
	            <mtbwa:ConvertWorkspaceItem DisplayName="Convert publish script filename" sap2010:WorkflowViewState.IdRef="ConvertWorkspaceItem_1" Input="[PublishScriptLocation]" Result="[PublishScriptFilePath]" Workspace="[Workspace]" />
	            <mtbwa:ConvertWorkspaceItem DisplayName="Convert subscription filename" sap2010:WorkflowViewState.IdRef="ConvertWorkspaceItem_2" Input="[SubscriptionDataFileLocation]" Result="[SubscriptionDataFilePath]" Workspace="[Workspace]" />
	            <mtbwa:InvokeProcess Arguments="[String.Format(&quot; -File &quot;&quot;{0}&quot;&quot; -serviceName {1}&#xD;&#xA;            -storageAccountName {2} -packageLocation &quot;&quot;{3}&quot;&quot;&#xD;&#xA;            -cloudConfigLocation &quot;&quot;{4}&quot;&quot; -subscriptionDataFile &quot;&quot;{5}&quot;&quot;&#xD;&#xA;            -selectedSubscription {6} -environment &quot;&quot;{7}&quot;&quot;&quot;,&#xD;&#xA;            PublishScriptFilePath, ServiceName, StorageAccountName,&#xD;&#xA;            PackageLocation, CloudConfigLocation,&#xD;&#xA;            SubscriptionDataFilePath, SubscriptionName, Environment)]" DisplayName="'Execute Publish Script'" FileName="[PowerShell]" sap2010:WorkflowViewState.IdRef="InvokeProcess_1">
	              <mtbwa:InvokeProcess.ErrorDataReceived>
	                <ActivityAction x:TypeArguments="x:String">
	                  <ActivityAction.Argument>
	                    <DelegateInArgument x:TypeArguments="x:String" Name="data" />
	                  </ActivityAction.Argument>
	                  <mtbwa:WriteBuildError Message="{x:Null}" sap2010:WorkflowViewState.IdRef="WriteBuildError_1" />
	                </ActivityAction>
	              </mtbwa:InvokeProcess.ErrorDataReceived>
	              <mtbwa:InvokeProcess.OutputDataReceived>
	                <ActivityAction x:TypeArguments="x:String">
	                  <ActivityAction.Argument>
	                    <DelegateInArgument x:TypeArguments="x:String" Name="data" />
	                  </ActivityAction.Argument>
	                  <mtbwa:WriteBuildMessage sap2010:WorkflowViewState.IdRef="WriteBuildMessage_2" Importance="[Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High]" Message="[data]" mva:VisualBasic.Settings="Assembly references and imported namespaces serialized as XML namespaces" />
	                </ActivityAction>
	              </mtbwa:InvokeProcess.OutputDataReceived>
	            </mtbwa:InvokeProcess>
	          </Sequence>
	        </If.Then>
	      </If>
	    </Sequence>


7.  儲存建置流程範本工作流程，並簽入此檔案。

8.  編輯組建定義 (如果已開啟請關閉)，如果您在流程範本清單上尚未看到新範本，請選取 [**新增**] 按鈕。

9.  在 Misc 區段中設定如下參數屬性值：

    1.  CloudConfigLocation ='c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg' *此值衍生自：($PublishDir)ServiceConfiguration.Cloud.cscfg*

    2.  PackageLocation = 'c:\drops\app.publish\ContactManager.Azure.cspkg' *此值衍生自：($PublishDir)($ProjectName).cspkg*

    3.  PublishScriptLocation = 'c:\scripts\WindowsAzure\PublishCloudService.ps1'

    4.  ServiceName = 'mycloudservicename' *在這裡使用適當的雲端服務名稱*

    5.  Environment = 'Staging'

    6.  StorageAccountName = 'mystorageaccountname' *在這裡使用適當的儲存體帳戶名稱*

    7.  SubscriptionDataFileLocation = 'c:\scripts\WindowsAzure\Subscription.xml'

    8.  SubscriptionName = 'default'

    ![][6]

10. 儲存組建定義的變更。

11. 將組建排入佇列，以同時執行套件建置及發佈。如果已將觸發程序設為 Continuous Integration，則每次簽入時都會執行此行為。

### PublishCloudService.ps1 指令碼範本

<pre>
Param(  $serviceName = "",
        $storageAccountName = "",
        $packageLocation = "",
        $cloudConfigLocation = "",
        $environment = "Staging",
        $deploymentLabel = "ContinuousDeploy to $servicename",
        $timeStampFormat = "g",
        $alwaysDeleteExistingDeployments = 1,
        $enableDeploymentUpgrade = 1,
        $selectedsubscription = "default",
        $subscriptionDataFile = ""
     )
      

function Publish()
{
	$deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot -ErrorVariable a -ErrorAction silentlycontinue 
    if ($a[0] -ne $null)
    {
        Write-Output "$(Get-Date -f $timeStampFormat) - No deployment is detected.Creating a new deployment. "
    }
    #check for existing deployment and then either upgrade, delete + deploy, or cancel according to $alwaysDeleteExistingDeployments and $enableDeploymentUpgrade boolean variables
	if ($deployment.Name -ne $null)
	{
		switch ($alwaysDeleteExistingDeployments)
	    {
	        1 
			{
                switch ($enableDeploymentUpgrade)
                {
                    1  #Update deployment inplace (usually faster, cheaper, won't destroy VIP)
                    {
                        Write-Output "$(Get-Date -f $timeStampFormat) - Deployment exists in $servicename. Upgrading deployment."
				        UpgradeDeployment
                    }
                    0  #Delete then create new deployment
                    {
                        Write-Output "$(Get-Date -f $timeStampFormat) - Deployment exists in $servicename. Deleting deployment."
				        DeleteDeployment
                        CreateNewDeployment
                        
                    }
                } # switch ($enableDeploymentUpgrade)
			}
	        0
			{
				Write-Output "$(Get-Date -f $timeStampFormat) - ERROR: Deployment exists in $servicename. Script execution cancelled."
				exit
			}
	    } #switch ($alwaysDeleteExistingDeployments)
	} else {
            CreateNewDeployment
    }
}

function CreateNewDeployment()
{
	write-progress -id 3 -activity "Creating New Deployment" -Status "In progress"
	Write-Output "$(Get-Date -f $timeStampFormat) - Creating New Deployment: In progress"

	$opstat = New-AzureDeployment -Slot $slot -Package $packageLocation -Configuration $cloudConfigLocation -label $deploymentLabel -ServiceName $serviceName
	    
    $completeDeployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $completeDeploymentID = $completeDeployment.deploymentid

    write-progress -id 3 -activity "Creating New Deployment" -completed -Status "Complete"
	Write-Output "$(Get-Date -f $timeStampFormat) - Creating New Deployment: Complete, Deployment ID: $completeDeploymentID"
    
	StartInstances
}

function UpgradeDeployment()
{
	write-progress -id 3 -activity "Upgrading Deployment" -Status "In progress"
	Write-Output "$(Get-Date -f $timeStampFormat) - Upgrading Deployment: In progress"

    # perform Update-Deployment
	$setdeployment = Set-AzureDeployment -Upgrade -Slot $slot -Package $packageLocation -Configuration $cloudConfigLocation -label $deploymentLabel -ServiceName $serviceName -Force
    
    $completeDeployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $completeDeploymentID = $completeDeployment.deploymentid
    
    write-progress -id 3 -activity "Upgrading Deployment" -completed -Status "Complete"
	Write-Output "$(Get-Date -f $timeStampFormat) - Upgrading Deployment: Complete, Deployment ID: $completeDeploymentID"
}

function DeleteDeployment()
{

	write-progress -id 2 -activity "Deleting Deployment" -Status "In progress"
	Write-Output "$(Get-Date -f $timeStampFormat) - Deleting Deployment: In progress"

    #WARNING - always deletes with force
	$removeDeployment = Remove-AzureDeployment -Slot $slot -ServiceName $serviceName -Force

	write-progress -id 2 -activity "Deleting Deployment: Complete" -completed -Status $removeDeployment
	Write-Output "$(Get-Date -f $timeStampFormat) - Deleting Deployment: Complete"
	
}

function StartInstances()
{
	write-progress -id 4 -activity "Starting Instances" -status "In progress"
	Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instances: In progress"

    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $runstatus = $deployment.Status

    if ($runstatus -ne 'Running') 
    {
	    $run = Set-AzureDeployment -Slot $slot -ServiceName $serviceName -Status Running
    }
	$deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
	$oldStatusStr = @("") * $deployment.RoleInstanceList.Count
	
	while (-not(AllInstancesRunning($deployment.RoleInstanceList)))
	{
		$i = 1
		foreach ($roleInstance in $deployment.RoleInstanceList)
		{
			$instanceName = $roleInstance.InstanceName
			$instanceStatus = $roleInstance.InstanceStatus

			if ($oldStatusStr[$i - 1] -ne $roleInstance.InstanceStatus)
			{
				$oldStatusStr[$i - 1] = $roleInstance.InstanceStatus
				Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instance '$instanceName': $instanceStatus"
			}

			write-progress -id (4 + $i) -activity "Starting Instance '$instanceName'" -status "$instanceStatus"
			$i = $i + 1
		}

		sleep -Seconds 1

		$deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
	}

	$i = 1
	foreach ($roleInstance in $deployment.RoleInstanceList)
	{
		$instanceName = $roleInstance.InstanceName
		$instanceStatus = $roleInstance.InstanceStatus

		if ($oldStatusStr[$i - 1] -ne $roleInstance.InstanceStatus)
		{
			$oldStatusStr[$i - 1] = $roleInstance.InstanceStatus
			Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instance '$instanceName': $instanceStatus"
		}

		$i = $i + 1
	}
	
    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
	$opstat = $deployment.Status 
	
	write-progress -id 4 -activity "Starting Instances" -completed -status $opstat
	Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instances: $opstat"
}

function AllInstancesRunning($roleInstanceList)
{
	foreach ($roleInstance in $roleInstanceList)
	{
		if ($roleInstance.InstanceStatus -ne "ReadyRole")
		{
			return $false
		}
	}
	
	return $true
}

#configure powershell with Azure 1.7 modules
Import-Module Azure

#configure powershell with publishsettings for your subscription
$pubsettings = $subscriptionDataFile
Import-AzurePublishSettingsFile $pubsettings
Set-AzureSubscription -CurrentStorageAccountName $storageAccountName -SubscriptionName $selectedsubscription
Select-AzureSubscription $selectedsubscription

#set remaining environment variables for Azure cmdlets
$subscription = Get-AzureSubscription $selectedsubscription
$subscriptionname = $subscription.subscriptionname
$subscriptionid = $subscription.subscriptionid
$slot = $environment

#main driver - publish &amp; write progress to activity log
Write-Output "$(Get-Date -f $timeStampFormat) - Azure Cloud Service deploy script started."
Write-Output "$(Get-Date -f $timeStampFormat) - Preparing deployment of $deploymentLabel for $subscriptionname with Subscription ID $subscriptionid."

Publish

$deployment = Get-AzureDeployment -slot $slot -serviceName $servicename
$deploymentUrl = $deployment.Url

Write-Output "$(Get-Date -f $timeStampFormat) - Created Cloud Service with URL $deploymentUrl."
Write-Output "$(Get-Date -f $timeStampFormat) - Azure Cloud Service deploy script finished."
</pre>

## 後續步驟

若要在使用連續傳遞時啟用遠端偵錯，請參閱[這些指示](http://go.microsoft.com/fwlink/p/?LinkID=402354) (英文)。

  [使用 Visual Studio Online 連續傳遞至 Azure]: cloud-services-continuous-delivery-use-vso.md
  [Team Foundation Build Service]: http://go.microsoft.com/fwlink/p/?LinkId=239963
  [.NET Framework 4]: http://go.microsoft.com/fwlink/?LinkId=239538
  [.NET Framework 4.5]: http://go.microsoft.com/fwlink/?LinkId=245484
  [.NET Framework 4.5.2]: http://go.microsoft.com/fwlink/?LinkId=521668
  [Azure 製作工具]: http://go.microsoft.com/fwlink/?LinkId=239600
  [Azure 程式庫]: http://go.microsoft.com/fwlink/?LinkId=257862
  [Azure Tools for Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=257862
  [MSBuild 命令列參考]: http://msdn.microsoft.com/library/ms164311(v=VS.90).aspx
  [1]: http://go.microsoft.com/fwlink/p/?LinkId=239966
  [了解 Team Foundation Build System]: http://go.microsoft.com/fwlink/?LinkId=238798
  [設定組建機器]: http://go.microsoft.com/fwlink/?LinkId=238799
  [0]: ./media/cloud-services-dotnet-continuous-delivery/tfs-01.png
  [2]: ./media/cloud-services-dotnet-continuous-delivery/tfs-02.png
  [Azure PowerShell Cmdlet]: http://go.microsoft.com/fwlink/?LinkId=256262
  [the .publishsettings file]: https://manage.windowsazure.com/download/publishprofile.aspx?wa=wsignin1.0
  [本文結尾]: #script
  
  [3]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-03.png
  [4]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-04.png
  [5]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-05.png
  [6]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-06.png
 

<!---HONumber=62-->