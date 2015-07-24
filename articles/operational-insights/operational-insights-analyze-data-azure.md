<properties
   pageTitle="在 Microsoft Azure 中分析來自伺服器的資料"
   description="您可以藉由啟用 Azure 診斷來搜尋雲端服務和虛擬機器的事件與 IIS 記錄檔。"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor=""/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/02/2015"
   ms.author="banders"/>
# 在 Microsoft Azure 中分析來自伺服器的資料

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Operational Insights 會在內部部署或雲端基礎結構中使用來自伺服器的資料。您可以在 Azure 診斷產生電腦資料時，從 Azure 儲存體加以收集。

利用收集自 Azure 儲存體的資料，您可以藉由啟用 [Azure 診斷](operational-insights-log-collection.md)來快速搜尋雲端服務和虛擬機器的事件與 IIS 記錄檔。您也可以安裝 Microsoft Monitoring Agent，從您的虛擬機器取得額外的見解。

更新評估、變更追蹤和 SQL 評估解決方案皆可搭配使用 Microsoft Monitoring Agent，在您的虛擬機器上提供更深入的見解。如果您還沒有這麼做，您可以在登入 [Operational Insights 入口網站](https://preview.opinsights.azure.com/)時[新增解決方案](operational-insights-add-solution.md)。

若為 Azure 虛擬機器，有兩個簡單的方法可啟用代理程式型資料收集：

- 在 Microsoft Azure 管理入口網站中

- 使用 PowerShell

使用記錄資料的代理程式型集合時，您必須在 [Operational Insights 入口網站](https://preview.opinsights.azure.com/)的記錄管理組態頁面中設定要收集的記錄檔。

 >[AZURE.NOTE]如果您已設定 Operational Insights 使用 Azure 診斷來索引記錄資料，並設定代理程式以收集記錄檔，則相同的記錄檔會編製索引兩次。這兩個資料來源會依照一般資料費率向您收費。如果您已安裝代理程式，您應該使用代理程式收集記錄資料，而不將 Azure 診斷所收集的記錄檔編製索引。

## Microsoft Azure 管理入口網站

您可以從 [Azure 入口網站](https://manage.windowsazure.com/#Workspaces/OperationalInsightExtension/Workspaces)安裝 Operational Insights 的代理程式。

### 安裝 Operational Insights 的代理程式
1. 在 Azure 入口網站中，移至 Operational Insights 工作區，然後選取 [**伺服器**] 索引標籤。
2. 在索引標籤上，您會看到虛擬機器清單。選取您要在其中安裝代理程式的虛擬機器，然後按一下 [**啟用 Op Insights**]。

隨即會為 Operational Insights 工作區自動安裝和設定代理程式。

![Operational Insights 伺服器頁面的影像](./media/operational-insights-analyze-data-azure/servers.png)

 >[AZURE.NOTE]必須安裝 [Azure VM 代理程式](https://msdn.microsoft.com/library/azure/dn832621.aspx) 以自動安裝 Operational Insights 的代理程式。



## PowerShell

如果您想要編寫指令碼以變更 Azure 虛擬機器，您可以使用 PowerShell 以啟用 Microsoft Monitoring Agent。

Microsoft Monitoring Agent 是 [Azure 虛擬機器擴充功能](https://msdn.microsoft.com/library/azure/dn832621.aspx)，您可以使用 PowerShell 管理它，如下列範例所示。

```powershell
Add-AzureAccount

$workspaceId="enter workspace here"
$workspaceKey="enter workspace key here"
$hostedService="enter hosted service here"

$vm = Get-AzureVM –ServiceName $hostedService
Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'MicrosoftMonitoringAgent' -Version '1.*' -PublicConfiguration "{'workspaceId':  '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose
```

使用 PowerShell 進行設定時，您必須提供工作區識別碼及主要金鑰。您可以在 Operational Insights 入口網站的 [**設定**] 頁面上找到您的工作區識別碼及主要金鑰。

![來源](./media/operational-insights-analyze-data-azure/sources01.png)

## 使用 Azure 診斷收集資料

Operational Insights 可以分析由 Azure 診斷寫入至 Azure 儲存體的資料。有 2 個要執行的主要步驟：

- 設定將診斷資料收集至 Azure 儲存體
- 設定 Operational Insights 來分析儲存體帳戶中的資料

下列主題說明如何將診斷資料收集至 Azure 儲存體，接著說明如何設定 Operational Insights 來分析 Azure 儲存體中的資料。

Azure 診斷是 Azure 擴充功能，可讓您從 Azure 中執行的背景工作角色、Web 角色或虛擬機器收集診斷資料。資料儲存在 Azure 儲存體帳戶，接著可由 Operational Insights 使用。

>[AZURE.NOTE]將診斷傳送至儲存體帳戶時，或是當 Operational Insights 從您的儲存體帳戶讀取資料時，您將需要支付儲存和交易的一般資料費用。

Azure 診斷可收集下列類型的遙測資料：

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>資料來源</b></td>
		<td><b> 說明 </b></td>
    </tr>
    <tr align="left" valign="top">
		<td>IIS 記錄檔</td>
		<td>IIS 網站的相關資訊。</td>
    </tr>
    <tr align="left" valign="top">
		<td>Azure 診斷基礎結構記錄檔</td>
		<td>診斷本身的相關資訊。</td>
    </tr>
	<tr align="left" valign="top">
		<td>IIS 失敗要求記錄檔 </td>
		<td>IIS 網站或應用程式之失敗要求的相關資訊。</td>
    </tr>
	    <tr align="left" valign="top">
		<td>Windows 事件記錄檔</td>
		<td>傳送至 Windows 事件記錄系統的資訊。</td>
    </tr>
    <tr align="left" valign="top">
		<td>效能計數器</td>
		<td>作業系統和自訂效能計數器。</td>
    </tr>
    <tr align="left" valign="top">
		<td>損毀傾印</td>
		<td>應用程式損毀時之處理序狀態的相關資訊。</td>
    </tr>
    <tr align="left" valign="top">
		<td>自訂錯誤記錄檔</td>
		<td>您的應用程式或服務所建立的記錄檔。</td>
    </tr>
    <tr align="left" valign="top">
		<td>NET EventSource</td>
		<td>您的程式碼使用 .NET <a href="https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx">EventSource 類別所產生的事件。</td>
    </tr>
    <tr align="left" valign="top">
		<td>以資訊清單為基礎的 ETW</td>
		<td>任何處理序所產生的 ETW 事件。</td>
    &lt;/tr
    <tr align="left" valign="top">
		<td>Syslog</td>
		<td>傳送至 Syslog 或 Rsyslog 精靈的事件</td>
    </tr>
    </tbody>
    </table>


Operational Insights 目前能夠分析：

- 來自 Web 角色和虛擬機器的 IIS 記錄檔
- 從 Web 角色、背景工作角色和 Azure 虛擬機器執行 Windows 作業系統的 Windows 事件記錄檔
- 從 Azure 虛擬機器執行 Linux 作業系統的 Syslog

記錄檔必須位於下列位置：

- WADWindowsEventLogsTable (資料表儲存體) – 包含來自 Windows 事件記錄檔的資訊。
- wad-iis-logfiles (Blob 儲存體) – 包含 IIS 記錄檔的相關資訊。
- LinuxsyslogVer2v0 (資料表儲存體) – 包含 Linux syslog 事件。

 >[AZURE.NOTE]的 IIS 記錄檔，以啟用額外的見解。

若為虛擬機器，您也可以選擇將 [Microsoft Monitoring Agent ](http://go.microsoft.com/fwlink/?LinkId=517269)安裝於虛擬機器以啟用其他見解。除了分析 IIS 記錄檔和事件記錄檔之外，您也能夠執行其他分析，包括組態變更追蹤、SQL 評估及更新評估。

您可以幫助我們為 Operational Insights 排定其他記錄檔的優先順序，按[意見反應頁面](http://feedback.azure.com/forums/267889-azure-operational-insights/category/88086-log-management-and-log-collection-policy)的投票進行分析。

## 在 Web 角色中針對 IIS 記錄檔和事件集合啟用 Azure 診斷

請參閱[如何在雲端服務中啟用診斷](https://msdn.microsoft.com/library/azure/dn482131.aspx)。您將從該處使用基本資訊，並在這裡自訂用於 Microsoft Azure Operational Insights 的步驟。

啟用 Azure 診斷時：

- 預設會儲存 IIS 記錄檔，並在 scheduledTransferPeriod 傳輸間隔傳輸記錄檔資料。

- 預設不會傳輸 Windows 事件記錄檔。

### 啟用診斷

若要啟用 Windows 事件記錄檔，或變更 scheduledTransferPeriod，請使用 XML 組態檔 (diagnostics.wadcfg) 設定 Azure 診斷，如「如何在雲端服務中啟用診斷」主題中的[步驟 2：將 diagnostics.wadcfg 檔案加入至您的 Visual Studio 解決方案](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step2)和[步驟 3：設定應用程式的診斷](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step3)中所示。下列範例組態檔會從應用程式和系統記錄檔中收集 IIS 記錄檔和所有事件：

    <?xml version="1.0" encoding="utf-8" ?>
    <DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"
          configurationChangePollInterval="PT1M"
          overallQuotaInMB="4096">

      <Directories bufferQuotaInMB="0"
         scheduledTransferPeriod="PT10M">  
        <!-- IISLogs are only relevant to Web roles -->
        <IISLogs container="wad-iis" directoryQuotaInMB="0" />
      </Directories>

      <WindowsEventLog bufferQuotaInMB="0"
         scheduledTransferLogLevelFilter="Verbose"
         scheduledTransferPeriod="PT10M">
        <DataSource name="Application!*" />
        <DataSource name="System!*" />
      </WindowsEventLog>

    </DiagnosticMonitorConfiguration>


在「如何在雲端服務中啟用診斷」主題中的[步驟 4：設定診斷資料的儲存體](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step4)中，確定您的 ConfigurationSettings 會指定儲存體帳戶，如下列範例所示：


    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>


**AccountName** 和 **AccountKey** 值可在 Microsoft Azure 管理入口網站的儲存體帳戶儀表板的 [管理存取金鑰] 下找到。連接字串的通訊協定必須為 **https**。

一旦將更新的診斷組態套用至您的雲端服務，且其正在將診斷寫入 Azure 儲存體，您已準備好設定 Operational Insights。

## 為事件記錄檔和 IIS 記錄檔集合啟用虛擬機器中的 Azure 診斷

您可以搭配使用下列程序與 Microsoft Azure 管理入口網站，為事件記錄檔和 IIS 記錄檔集合啟用虛擬機器中的 Azure 診斷。

### 使用 Azure 管理入口網站啟用虛擬機器中的 Azure 診斷

1. 建立虛擬機器時安裝 VM 代理程式。如果虛擬機器已經存在，請確認已安裝 VM 代理程式。
	- 如果您使用預設的 Azure 管理入口網站建立虛擬機器，請執行 [**自訂建立**] 並選取 [**安裝 VM 代理程式**]。
	- 如果使用新的 Azure 管理入口網站來建立虛擬機器，請依序選取 [**選用設定**] 及 [**診斷**]，並將 [**狀態**] 設為 [**開啟**]。

	完成時，VM 會自動安裝和執行 Azure 診斷擴充功能以負責收集診斷資料。

2. 在現有的 VM 上啟用監視和設定事件記錄。您可以在 VM 層級上啟用診斷。若要啟用診斷然後設定事件記錄，請執行下列步驟：
	1. 選取 VM。
	2. 按一下 [**監視**]。
	3. 按一下 [**診斷**]。
	4. 將 [**狀態**] 設為 [**開啟**]。
	5. 選取您想要使用的每個診斷度量。Operational Insights 可分析 Windows 事件系統記錄檔、Windows 事件應用程式記錄檔和 IIS 記錄檔。
	7. 按一下 [確定]。

您可以使用 Azure PowerShell，更精確地指定寫入至 Azure 儲存體的事件。請參閱 Azure 診斷 1.2 組態結構描述，以取得組態檔範例和其結構描述的詳細文件。確定從[如何安裝和設定 Azure PowerShell](powershell-install-configure) 安裝並設定 Azure PowerShell 0.8.7 版或更新版本。如果您安裝 1.2 版之前的 Microsoft Azure 診斷，您無法使用新的入口網站來啟用或設定診斷。

您可以使用下列 PowerShell 指令碼啟用和更新代理程式。您也可以搭配使用此指令碼與自訂記錄組態。您必須修改指令碼來設定儲存體帳戶、服務名稱和虛擬機器名稱。

### 使用 Azure PowerShell 啟用虛擬機器中的診斷

檢閱下列指令碼範例、複製它、視需要修改它、將範例儲存為 PowerShell 指令碼檔案，然後再執行指令碼。


	#Connect to Azure
	Add-AzureAccount

	# settings to change:
	$wad_storage_account_name = "myStorageAccount"
	$service_name = "myService"
	$vm_name = "myVM"

	#Construct Azure Diagnostics public config and convert to config format

	# Collect just system error events:
	$wad_xml_config = "<WadCfg><DiagnosticMonitorConfiguration><WindowsEventLog scheduledTransferPeriod=""PT1M""><DataSource name=""System!* "" /></WindowsEventLog></DiagnosticMonitorConfiguration></WadCfg>"

	$wad_b64_config = [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($wad_xml_config))
	$wad_public_config = [string]::Format("{{""xmlCfg"":""{0}""}}",$wad_b64_config)

	#Construct Azure diagnostics private config

	$wad_storage_account_key = (Get-AzureStorageKey $wad_storage_account_name).Primary
	$wad_private_config = [string]::Format("{{""storageAccountName"":""{0}"",""storageAccountKey"":""{1}""}}",$wad_storage_account_name,$wad_storage_account_key)

	#Enable Diagnostics Extension for Virtual Machine

	$wad_extension_name = "IaaSDiagnostics"
	$wad_publisher = "Microsoft.Azure.Diagnostics"
	$wad_version = (Get-AzureVMAvailableExtension -Publisher $wad_publisher -ExtensionName $wad_extension_name).Version # Gets latest version of the extension

	(Get-AzureVM -ServiceName $service_name -Name $vm_name) | Set-AzureVMExtension -ExtensionName $wad_extension_name -Publisher $wad_publisher -PublicConfiguration $wad_public_config -PrivateConfiguration $wad_private_config -Version $wad_version | Update-AzureVM


## 使用 Operational Insights 啟用 Azure 儲存體分析

您可以使用下列程序啟用儲存體分析，並設定從 Azure 儲存體帳戶使用 Azure 診斷讀取 Operational Insights。

### 由 Operational Insights 啟用分析

1. 在預設的 Azure 入口網站中，瀏覽至 Operational Insights 工作區，然後選取 [**儲存體**] 索引標籤。![工作區儲存體索引標籤](./media/operational-insights-analyze-data-azure/workspace-storage-tab.png)
2. 按一下 [**新增儲存體帳戶**] 開啟 [**加入儲存體帳戶**] 方塊。
3. 選取您要使用的儲存體帳戶。
4. 在**資料類型**清單中選取資料類型：任何 **事件**、**IIS 記錄檔**或 **Syslog (Linux)**。
5. 按一下 [**確定**] 映像。![儲存體帳戶方塊](./media/operational-insights-analyze-data-azure/storage-account.png)
6. 為您想要收集的每一個資料類型和儲存體帳戶組合重複上述步驟。

大約 1 小時後，您會開始看到來自儲存體帳戶的資料，這些資訊可在 Operational Insights 中進行分析。

## 相關內容

- [將電腦直接連線到 Operational Insights](operational-insights-direct-agent)
- [部落格文章：啟用 Azure 虛擬機器的 Operational Insights](http://azure.microsoft.com/updates/easily-enable-operational-insights-for-azure-virtual-machines/)

## 後續步驟

[設定 Proxy 和防火牆設定 (選擇性)](../operational-insights-proxy-filewall.md)

<!---HONumber=July15_HO2-->