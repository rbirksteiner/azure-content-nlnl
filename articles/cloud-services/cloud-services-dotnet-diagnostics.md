<properties 
	pageTitle="如何使用診斷 (.NET) - Azure 功能指南" 
	description="了解如何在 Azure 中使用診斷資料進行偵錯、測量效能、監視、流量分析等。" 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="rboucher" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="robb"/>



# 在 Azure 雲端服務和虛擬機器中啟用診斷

Azure 診斷 1.2 和 1.3 可讓您從 Azure 中執行的背景工作角色、Web 角色或虛擬機器收集診斷資料。本指南說明如何使用 Azure 診斷 1.2 和 1.3。如需建立記錄及追蹤策略，以及使用診斷和其他技術來疑難排解問題的其他深入指引，請參閱[開發 Azure 應用程式的疑難排解最佳作法][] (英文)。

## 概觀

Azure 診斷 1.2 和 1.3 是可讓您從 Azure 中執行的背景工作角色、Web 角色或虛擬機器收集診斷遙測資料的 Azure 擴充功能。遙測資料會儲存在 Azure 儲存體帳戶中，並可用來進行偵錯和疑難排解、測量效能、監視資源使用量、進行流量分析和容量規劃，以及進行稽核。

Azure 診斷 1.2 適用於 Azure SDK for.NET 2.4 和更早版本搭配使用。Azure 診斷 1.3 適用於 Azure SDK for.NET 2.5 和更新版本搭配使用。

如果您過去曾使用診斷 1.0 版，您會發現與診斷 1.2 和 1.3 相較下有三點顯著差異：

1.	診斷 1.2 和 1.3 可部署至虛擬機器及雲端服務。
2.	Diagnostics 1.0 是 Azure SDK 的一部分，會在部署雲端服務時一併部署。診斷 1.2 和 1.3 是擴充功能，並可從您的雲端服務部署中個別部署。
3.	診斷 1.2 和 1.3 可收集 ETW 和.NET EventSource 事件。

如需更詳細的比較，請參閱本文結尾的[比較 Azure 診斷版本][]。

Azure 診斷可收集下列類型的遙測資料：

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
	<tr>
			<td style="width: 100px;"><strong>資料來源</strong></td>
			<td><strong>說明</strong></td>
	</tr>
	<tr>
		<td>IIS 記錄檔</td>
		<td>IIS 網站的相關資訊。</td>            
	</tr>
	<tr>
		<td>Azure 診斷基礎結構記錄檔</td>
		<td>診斷本身的相關資訊。</td>            
	</tr>
	<tr>
		<td>IIS 失敗要求記錄檔	</td>
		<td>IIS 網站或應用程式之失敗要求的相關資訊。</td>            
	</tr>
	<tr>
		<td>Windows 事件記錄檔</td>
		<td>傳送至 Windows 事件記錄系統的資訊。</td>            
	</tr>
	<tr>
		<td>效能計數器</td>
		<td>作業系統和自訂效能計數器。</td>            
	</tr>
	<tr>
		<td>損毀傾印</td>
		<td>應用程式損毀時之處理序狀態的相關資訊。</td>            
	</tr>
	<tr>
		<td>自訂錯誤記錄檔</td>
		<td>您的應用程式或服務所建立的記錄檔。</td>            
	</tr>
	<tr>
		<td>.NET EventSource</td>
		<td>您的程式碼使用 .NET <a href="http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx">EventSource 類別</a>所產生的事件。</td>            
	</tr>
	<tr>
		<td>以資訊清單為基礎的 ETW</td>
		<td>任何處理序所產生的 ETW 事件。</td>            
	</tr>
		
</tbody>
</table>

## 如何在背景工作角色中啟用診斷

本逐步解說說明如何實作 Azure 背景工作角色，該角色使用 .NET EventSource 類別發出遙測資料。Azure 診斷可用來收集遙測資料，並將資料儲存在 Azure 儲存體帳戶。建立背景工作角色時，Visual Studio 會自動啟用診斷 1.0 來做為 Azure SDK for.NET 2.4 及更早版本中解決方案的一部分。下列指示說明建立背景工作角色、從解決方案停用診斷 1.0，以及將診斷 1.2 或 1.3 部署至背景工作角色的程序。

### 必要條件
本文假設您擁有 Azure 訂用帳戶，並且搭配 Azure SDK 使用 Visual Studio 2013。如果您沒有 Azure 訂用帳戶，可以註冊[免費試用版][]。請務必[安裝及設定 Azure PowerShell 0.8.7 版或更新版本][]。

### 步驟 1：建立背景工作角色
1.	啟動 **Visual Studio 2013**。
2.	從以 .NET Framework 4.5 為目標的**雲端**範本，建立新的 **Azure 雲端服務**專案。將專案命名為 "WadExample" 並按一下 [確定]。
3.	選取 [**背景工作角色**] 並按一下 [確定]。將會建立專案。 
4.	在 [方案總管] 中，按兩下 **WorkerRole1** 屬性檔。
5.	在 [**組態**] 索引標籤中取消核取 [**啟用診斷**] 以停用診斷 1.0 (Azure SDK 2.4 和更早版本)。
6.	建置您的解決方案以確認無誤。

### 步驟 2：實作您的程式碼
以下列程式碼取代 WorkerRole.cs 的內容。繼承自 [EventSource 類別][]的 SampleEventSourceWriter 類別實作四種記錄方法：**SendEnums**、**MessageMethod**、**SetOther** 和 **HighFreq**。傳遞至 **WriteEvent** 方法的第一個參數定義個別事件的識別碼。Run 方法實作一個無限迴圈，每 10 秒呼叫一次在 **SampleEventSourceWriter** 類別中實作的每種記錄方法。

	using Microsoft.WindowsAzure.ServiceRuntime;
	using System;
	using System.Diagnostics;
	using System.Diagnostics.Tracing;
	using System.Net;
	using System.Threading;

	namespace WorkerRole1
	{
    sealed class SampleEventSourceWriter : EventSource
    {
        public static SampleEventSourceWriter Log = new SampleEventSourceWriter();
        public void SendEnums(MyColor color, MyFlags flags) { if (IsEnabled())  WriteEvent(1, (int)color, (int)flags); }// Cast enums to int for efficient logging.
        public void MessageMethod(string Message) { if (IsEnabled())  WriteEvent(2, Message); }
        public void SetOther(bool flag, int myInt) { if (IsEnabled())  WriteEvent(3, flag, myInt); }
        public void HighFreq(int value) { if (IsEnabled()) WriteEvent(4, value); }

    }

    enum MyColor
    {
        Red,
        Blue,
        Green
    }

    [Flags]
    enum MyFlags
    {
        Flag1 = 1,
        Flag2 = 2,
        Flag3 = 4
    }

    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
            // This is a sample worker implementation. Replace with your logic.
            Trace.TraceInformation("WorkerRole1 entry point called");

            int value = 0;

            while (true)
            {
                Thread.Sleep(10000);
                Trace.TraceInformation("Working");

                // Emit several events every time we go through the loop
                for (int i = 0; i < 6; i++)
                {
                    SampleEventSourceWriter.Log.SendEnums(MyColor.Blue, MyFlags.Flag2 | MyFlags.Flag3);
                }

                for (int i = 0; i < 3; i++)
                {
                    SampleEventSourceWriter.Log.MessageMethod("This is a message.");
                    SampleEventSourceWriter.Log.SetOther(true, 123456789);
                }

                if (value == int.MaxValue) value = 0;
                SampleEventSourceWriter.Log.HighFreq(value++);
            }
        }

        public override bool OnStart()
        {
            // Set the maximum number of concurrent connections 
            ServicePointManager.DefaultConnectionLimit = 12;

            // For information on handling configuration changes
            // see the MSDN topic at http://go.microsoft.com/fwlink/?LinkId=166357.

            return base.OnStart();
        }
    }
	}


### 步驟 3：部署您的背景工作角色
1.	在 [方案總管] 中選取 **WadExample** 專案，然後從 [**建置**] 功能表選取 [**發行**]，以從 Visual Studio 將背景工作角色部署至 Azure。
2.	選擇您的訂用帳戶。
3.	在 [**Microsoft Azure 發行設定**] 對話方塊中，選取 [**建立新的…**]。
4.	在 [**建立雲端服務和儲存體帳戶**] 對話方塊中，輸入一個 [**名稱**] (例如 "WadExample") 並選取一個區域或同質群組。
5.	將 [環境] 設為 [預備]。
6.	適當地修改其他任何**設定**，然後按一下 [發行]。
7.	部署完成之後，請在 Azure 入口網站中確認您的雲端服務是否處於 [執行中] 狀態。

### 步驟 4：建立您的診斷組態檔並安裝擴充功能
1.	執行下列 PowerShell 命令，以下載公用組態檔結構描述定義：
2.	
		(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd' 

2.	以滑鼠右鍵按一下 **WorkerRole1** 專案，然後選取 [加入] -> [新增項目...] -> [Visual C# 項目] -> [資料] -> [XML 檔]，將 XML 檔加入您的 **WorkerRole1** 專案。將檔案命名為 "WadExample.xml"。

	![CloudServices_diag_add_xml](./media/cloud-services-dotnet-diagnostics/AddXmlFile.png)

3.	將 WadConfig.xsd 與組態檔產生關聯。確定 WadExample.xml 編輯器視窗是使用中視窗。按 **F4** 鍵開啟 [屬性] 視窗。在 [屬性] 視窗中，按一下 [結構描述] 屬性。在 [結構描述] 屬性中，按一下 [...]。按一下 [**加入...**] 按鈕並瀏覽至您儲存 XSD 檔的位置，然後選取檔案 WadConfig.xsd。按一下 [確定]。
4.	以下列 XML 取代 WadExample.xml 組態檔的內容，然後儲存檔案。此組態檔可定義兩個要收集的效能計數器：一個用於 CPU 使用率，一個用於記憶體使用率。組態會接著定義四個事件，分別對應至 SampleEventSourceWriter 類別中的方法。

		<?xml version="1.0" encoding="utf-8"?>
		<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  			<WadCfg>
    			<DiagnosticMonitorConfiguration overallQuotaInMB="25000">
      			<PerformanceCounters scheduledTransferPeriod="PT1M">
        			<PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)% Processor Time" sampleRate="PT1M" unit="percent" />
        			<PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT1M" unit="bytes"/>
      				</PerformanceCounters>
      				<EtwProviders>
        				<EtwEventSourceProviderConfiguration provider="SampleEventSourceWriter" scheduledTransferPeriod="PT5M">
          					<Event id="1" eventDestination="EnumsTable"/>
          					<Event id="2" eventDestination="MessageTable"/>
          					<Event id="3" eventDestination="SetOtherTable"/>
          					<Event id="4" eventDestination="HighFreqTable"/>
          					<DefaultEvents eventDestination="DefaultTable" />
        				</EtwEventSourceProviderConfiguration>
      				</EtwProviders>
    			</DiagnosticMonitorConfiguration>
  			</WadCfg>
		</PublicConfig>

### 步驟 5：在您的背景工作角色上安裝診斷
用於管理 Web 角色或背景工作角色上之診斷的 PowerShell Cmdlet 為：Set-AzureServiceDiagnosticsExtension、Get-AzureServiceDiagnosticsExtension 和 Remove-AzureServiceDiagnosticsExtension。

1.	開啟 Azure PowerShell。
2.	執行指令碼，在您的背景工作角色上安裝診斷 (以 wadexample 儲存體帳戶的儲存體帳戶金鑰取代 *StorageAccountKey*)：

		$storage_name = "wadexample"
		$key = "<StorageAccountKey>"
		$config_path="c:\users<user>\documents\visual studio 2013\Projects\WadExample\WorkerRole1\WadExample.xml"
		$service_name="wadexample"
		$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key 
		Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Staging -Role WorkerRole1


### 步驟 6：查看您的遙測資料
在 Visual Studio 的 [伺服器總管] 中，瀏覽至 wadexample 儲存體帳戶。在雲端服務執行約 5 分鐘之後，您應該會看到資料表 **WADEnumsTable**、**WADHighFreqTable**、**WADMessageTable**、**WADPerformanceCountersTable** 和 **WADSetOtherTable**。按兩下其中一個資料表以檢視收集的遙測資料。![CloudServices_diag_tables](./media/cloud-services-dotnet-diagnostics/WadExampleTables.png)

## 如何在虛擬機器中啟用診斷

本逐步解說說明如何從遠端的開發電腦將診斷安裝至 Azure 虛擬機器。您也將了解如何實作在該 Azure 虛擬機器上執行，並使用 .NET [EventSource 類別][]發出遙測資料的應用程式。Azure 診斷可用來收集遙測資料，並將資料儲存在 Azure 儲存體帳戶。

### 必要條件
本逐步解說假設您擁有 Azure 訂用帳戶，並且搭配 Azure SDK 使用 Visual Studio 2013。如果您沒有 Azure 訂用帳戶，可以註冊[免費試用版][]。請務必[安裝及設定 Azure PowerShell 0.8.7 版或更新版本][]。

### 步驟 1：建立虛擬機器
1.	在您的開發電腦上，啟動 Visual Studio 2013。
2.	在 Visual Studio [**伺服器總管**] 中展開 [**Azure**]，以滑鼠右鍵按一下 [**虛擬機器**]，然後選取 [**建立虛擬機器**]。
3.	在 [選擇訂用帳戶] 對話方塊中，選取您的 Azure 訂用帳戶，然後按 [下一步]。
4.	在 [**選取虛擬機器映像**] 對話方塊中，選取 [**Windows Server 2012 R2 Datacenter，2014 年 11 月**]，然後按 [**下一步**]。
5.	在 [**虛擬機器基本設定**] 中，將虛擬機器名稱設為 "wadexample"。設定您的系統管理員使用者名稱和密碼，然後按 [下一步]。
6.	在 [**雲端服務設定**] 對話方塊中，建立名為 "wadexampleVM" 的新雲端服務。建立名為 "wadexample" 的新儲存體帳戶，然後按 [**下一步**]。
7.	按一下 [建立]。

### 步驟 2：建立您的應用程式
1.	在您的開發電腦上，啟動 Visual Studio 2013。
2.	建立以 .NET Framework 4.5 為目標的新 Visual C# 主控台應用程式。將專案命名為 "WadExampleVM"。![CloudServices_diag_new_project](./media/cloud-services-dotnet-diagnostics/NewProject.png)
3.	以下列程式碼取代 Program.cs 的內容。**SampleEventSourceWriter** 類別實作四種記錄方法：**SendEnums**、**MessageMethod**、**SetOther** 和 **HighFreq**。傳遞至 WriteEvent 方法的第一個參數定義個別事件的識別碼。Run 方法實作一個無限迴圈，每 10 秒呼叫一次在 **SampleEventSourceWriter** 類別中實作的每種記錄方法。

		using System;
		using System.Diagnostics;
		using System.Diagnostics.Tracing;
		using System.Threading;

		namespace WadExampleVM
		{
    	sealed class SampleEventSourceWriter : EventSource
    	{
        	public static SampleEventSourceWriter Log = new SampleEventSourceWriter();
        	public void SendEnums(MyColor color, MyFlags flags) { if (IsEnabled())  WriteEvent(1, (int)color, (int)flags); }// Cast enums to int for efficient logging.
        	public void MessageMethod(string Message) { if (IsEnabled())  WriteEvent(2, Message); }
        	public void SetOther(bool flag, int myInt) { if (IsEnabled())  WriteEvent(3, flag, myInt); }
        	public void HighFreq(int value) { if (IsEnabled()) WriteEvent(4, value); }

    	}

    	enum MyColor
    	{
        	Red,
        	Blue,
        	Green
    	}

    	[Flags]
    	enum MyFlags
    	{
        	Flag1 = 1,
        	Flag2 = 2,
        	Flag3 = 4
    	}

    	class Program
    	{
        static void Main(string[] args)
        {
            Trace.TraceInformation("My application entry point called");
            
            int value = 0;

            while (true)
            {
                Thread.Sleep(10000);
                Trace.TraceInformation("Working");

                // Emit several events every time we go through the loop
                for (int i = 0; i < 6; i++)
                {
                    SampleEventSourceWriter.Log.SendEnums(MyColor.Blue, MyFlags.Flag2 | MyFlags.Flag3);
                }

                for (int i = 0; i < 3; i++)
                {
                    SampleEventSourceWriter.Log.MessageMethod("This is a message.");
                    SampleEventSourceWriter.Log.SetOther(true, 123456789);
                }

                if (value == int.MaxValue) value = 0;
                SampleEventSourceWriter.Log.HighFreq(value++);
            }

        }
    	}
		}


4.	儲存檔案，然後從 [建置] 功能表選取 [建置方案]，以建置您的程式碼。


### 步驟 3：部署應用程式
1.	在 [**方案總管**] 中以滑鼠右鍵按一下 **WadExampleVM** 專案，然後選擇 [**在檔案總管中開啟資料夾**]。
2.	瀏覽至 *bin\Debug* 資料夾並複製所有檔案 (WadExampleVM.*)。
3.	在 [伺服器總管] 中，以滑鼠右鍵按一下虛擬機器並選擇 [使用遠端桌面連線]。
4.	連線至 VM 之後，建立名為 WadExampleVM 的資料夾，並將您的應用程式檔案貼到資料夾中。
5.	啟動 WadExampleVM.exe 應用程式。您應該會看見空白的主控台視窗。

### 步驟 4：建立您的診斷組態並安裝擴充功能
1.	執行下列 PowerShell 命令，將公用組態檔結構描述定義下載至您的開發電腦：

		(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd' 

2.	在已開啟專案的 Visual Studio 中，或在未開啟專案的 Visual Studio 執行個體中，開啟新的 XML 檔。在 Visual Studio 中，選取 [加入] -> [新增項目...] -> [Visual C# 項目] -> [資料] -> [XML 檔]。將檔案命名為 "WadExample.xml"
3.	將 WadConfig.xsd 與組態檔產生關聯。確定 WadExample.xml 編輯器視窗是使用中視窗。按 **F4** 鍵開啟 [屬性] 視窗。在 [屬性] 視窗中，按一下 [結構描述] 屬性。在 [結構描述] 屬性中，按一下 [...]。按一下 [**加入...**] 按鈕並瀏覽至您儲存 XSD 檔的位置，然後選取檔案 WadConfig.xsd。按一下 [確定]。
4.	以下列 XML 取代 WadExample.xml 組態檔的內容，然後儲存檔案。此組態檔可定義兩個要收集的效能計數器：一個用於 CPU 使用率，一個用於記憶體使用率。組態會接著定義四個事件，分別對應至 SampleEventSourceWriter 類別中的方法。

		<?xml version="1.0" encoding="utf-8"?>
		<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  			<WadCfg>
    			<DiagnosticMonitorConfiguration overallQuotaInMB="25000">
      			<PerformanceCounters scheduledTransferPeriod="PT1M">
        			<PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)% Processor Time" sampleRate="PT1M" unit="percent" />
        			<PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT1M" unit="bytes"/>
      				</PerformanceCounters>
      				<EtwProviders>
        				<EtwEventSourceProviderConfiguration provider="SampleEventSourceWriter" scheduledTransferPeriod="PT5M">
          					<Event id="1" eventDestination="EnumsTable"/>
          					<Event id="2" eventDestination="MessageTable"/>
          					<Event id="3" eventDestination="SetOtherTable"/>
          					<Event id="4" eventDestination="HighFreqTable"/>
          					<DefaultEvents eventDestination="DefaultTable" />
        				</EtwEventSourceProviderConfiguration>
      				</EtwProviders>
    			</DiagnosticMonitorConfiguration>
  			</WadCfg>
		</PublicConfig>


### 步驟 5：從遠端將診斷安裝至您的 Azure 虛擬機器上
用於管理 VM 上之診斷的 PowerShell Cmdlet 為：Set-AzureVMDiagnosticsExtension、Get-AzureVMDiagnosticsExtension 和 Remove-AzureVMDiagnosticsExtension。

1.	在您的開發電腦上，開啟 Azure PowerShell。
2.	執行指令碼，從遠端將診斷安裝至您的 VM (以 wadexamplevm 儲存體帳戶的儲存體帳戶金鑰取代 *StorageAccountKey*)：

		$storage_name = "wadexamplevm"
		$key = "<StorageAccountKey>"
		$config_path="c:\users<user>\documents\visual studio 2013\Projects\WadExampleVM\WadExampleVM\WadExample.xml"
		$service_name="wadexamplevm"
		$vm_name="WadExample"
		$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key 
		$VM1 = Get-AzureVM -ServiceName $service_name -Name $vm_name
		$VM2 = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $config_path -Version "1.*" -VM $VM1 -StorageContext $storageContext
		$VM3 = Update-AzureVM -ServiceName $service_name -Name $vm_name -VM $VM2.VM


### 步驟 6：查看您的遙測資料
在 Visual Studio 的 [伺服器總管] 中，瀏覽至 wadexample 儲存體帳戶。在 VM 執行約 5 分鐘之後，您應該會看到資料表 **WADEnumsTable**、**WADHighFreqTable**、**WADMessageTable**、**WADPerformanceCountersTable** 和 **WADSetOtherTable**。按兩下其中一個資料表以檢視收集的遙測資料。![CloudServices_diag_wadexamplevm_tables](./media/cloud-services-dotnet-diagnostics/WadExampleVMTables.png)

## 組態檔結構描述

診斷組態檔定義當診斷監視器啟動時，用來初始化診斷組態設定的值。您可以在下列位置找到範例組態檔及有關其結構描述的詳細文件：[Azure Diagnostics 1.2 組態結構描述][]。

## 疑難排解

### Azure 診斷未啟動
診斷是由兩個元件所組成：客體代理程式外掛程式和監視代理程式。客體代理程式外掛程式的記錄檔位於下列檔案中：

*%SystemDrive%\ WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics<DiagnosticsVersion>*\CommandExecution.log

外掛程式會傳回下列錯誤碼：

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
	<tr>
			<td style="width: 100px;"><strong>結束代碼</strong></td>
			<td><strong>說明</strong></td>
	</tr>
    <tr>
		<td>0</td>
		<td>成功。</td>            
	</tr>
    <tr>
		<td>-1</td>
        <td>一般錯誤。</td>		            
	</tr>
    <tr>
		<td>-2</td>
        <td><p>無法載入 rcf 檔。</p>
<p>這是內部錯誤，只有當不正確地在 VM 上以手動方式叫用客體代理程式外掛程式啟動器時才會發生。</p></td>		            
	</tr>
    <tr>
		<td>-3</td>
        <td><p>無法載入診斷組態檔。</p>
<p>解決方法：這是組態檔未通過結構描述驗證的結果。解決方法是提供以結構描述編譯的組態檔。</p></td>		            
	</tr>
    <tr>
		<td>-4</td>
        <td><p>診斷之監視代理程式的另一個執行個體已在使用本機資源目錄。</p>
<p>解決方法：為 <strong>LocalResourceDirectory</strong> 指定其他值。</p></td>		            
	</tr>
    <tr>
		<td>-6</td>
        <td><p>客體代理程式外掛程式啟動器嘗試使用無效的命令列來啟動診斷。</p>
<p>這是內部錯誤，只有當不正確地在 VM 上以手動方式叫用客體代理程式外掛程式啟動器時才會發生。</p></td>		            
	</tr>
    <tr>
		<td>-10</td>
        <td>診斷外掛程式結束並發生未處理的例外狀況。</td>		            
	</tr>
    <tr>
		<td>-11</td>
        <td><p>客體代理程式無法建立負責啟動及監視監視代理程式的處理序。</p>

<p>解決方法：確認有足夠的系統資源，可啟動新的處理序。</p></td>		            
	</tr>
    <tr>
		<td>-101</td>
        <td><p>呼叫診斷外掛程式時的引數無效。</p>

<p>這是內部錯誤，只有當不正確地在 VM 上以手動方式叫用客體代理程式外掛程式啟動器時才會發生。</p></td>		            
	</tr>
    <tr>
		<td>-102</td>
        <td><p>外掛程式處理序無法對本身進行初始化。</p> 

<p>解決方法：確認有足夠的系統資源，可啟動新的處理序。</p></td>		            
	</tr>
    <tr>
		<td>-103</td>
        <td><p>外掛程式處理序無法對本身進行初始化。具體而言，其無法建立記錄器物件。</p>

<p>解決方法：確認有足夠的系統資源，可啟動新的處理序。</p></td>		            
	</tr>
    <tr>
		<td>-104</td>
        <td><p>無法載入客體代理程式提供的 rcf 檔。</p>

<p>這是內部錯誤，只有當不正確地在 VM 上以手動方式叫用客體代理程式外掛程式啟動器時才會發生。</p></td>		            
	</tr>
    <tr>
		<td>-105</td>
        <td><p>診斷外掛程式無法開啟診斷組態檔。</p>

<p>這是內部錯誤，只有當不正確地在 VM 上以手動方式叫用診斷外掛程式時才會發生。</p></td>		            
	</tr>
    <tr>
		<td>-106</td>
        <td><p>無法讀取診斷組態檔。</p>

<p>解決方法：這是組態檔未通過結構描述驗證的結果。因此，解決方法是提供以結構描述編譯的組態檔。您可以在 VM 上的 <i>%SystemDrive%\WindowsAzure\Config</i> 資料夾中，找到傳送給診斷擴充功能的 XML。開啟適當的 XML 檔並搜尋 <strong>Microsoft.Azure.Diagnostics</strong>，然後搜尋 [xmlCfg]<strong></strong> 欄位。該資料是以 base64 編碼，因此您不需要<a href="http://www.bing.com/search?q=base64+decoder">進行解碼</a>便能查看診斷載入的 XML。</p></td>		            
	</tr>
    <tr>
		<td>-107</td>
        <td><p>傳遞給監視代理程式的資源目錄無效。</p>

<p>這是內部錯誤，只有當不正確地在 VM 上以手動方式叫用監視代理程式時才會發生。</p></td>		            
	</tr>
    <tr>
		<td>-108	</td>
        <td><p>無法將診斷組態檔轉換成監視代理程式組態檔。</p>

<p>這是內部錯誤，只有當使用無效的組態檔以手動方式叫用診斷外掛程式時才會發生。</p></td>		            
	</tr>
    <tr>
		<td>-110</td>
        <td><p>一般診斷組態錯誤。</p>

<p>這是內部錯誤，只有當使用無效的組態檔以手動方式叫用診斷外掛程式時才會發生。</p></td>		            
	</tr>
    <tr>
		<td>-111</td>
        <td><p>無法啟動監視代理程式。</p>

<p>解決方法：確認有足夠的系統資源可用。</p></td>		            
	</tr>
    <tr>
		<td>-112</td>
        <td>一般錯誤</td>		            
	</tr>    
</tbody>
</table>

### 診斷資料未記錄至儲存體
遺漏事件資料的最常見原因是不正確地定義儲存體帳戶資訊。

解決方法：更正 Diagnostics 組態檔，並重新安裝 Diagnostics。將資料上傳至您的儲存體帳戶之前，資料是儲存在資料夾中。請參閱上述有關 **LocalResourceDirectory** 的詳細資訊。

如果這個資料夾中沒有檔案，則監視代理程式將無法啟動。這通常是由於無效的組態檔所導致，應該已在 CommandExecution.log 中回報。如果監視代理程式成功收集事件資料，您將看到組態檔中所定義之每個事件的 .tsf 檔案。

監視代理程式會在 MaEventTable.tsf 檔案中記錄遇到的任何錯誤。若要檢查此檔案的內容，請執行下列命令：

		%SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.[IaaS | PaaS]Diagnostics\1.3.0.0\Monitor\x64\table2csv maeventtable.tsf

這個工具會產生名為 maeventtable.csv 的檔案，您可以開啟檔案並檢查記錄檔中是否有任何失敗。


## 常見問題集
以下是一些常見問題和回答：

**問：** 如何將我的 Visual Studio 解決方案從 Azure Diagnostics 1.0 升級至 Azure Diagnostics 1.1？

**答：** 將 Visual Studio 解決方案從 Diagnostics 1.0 升級至 Diagnostics 1.1 (或更新版本) 是手動程序：- 請停用您的 Visual Studio 方案中的診斷，以防止 Diagnostics 1.0 隨您的角色部署 - 如果您的程式碼使用追蹤接聽項，則需要修改程式碼才能使用 .NET EventSource。Diagnostics 1.1 及更新版本不支援追蹤接聽項。- 請修改您的部署處理序，以安裝 Diagnostics 1.1 擴充功能。

**問：** 如果我已經在角色或 VM 上安裝診斷 1.1 擴充功能，如何才能升級至診斷 1.2 或 1.3？

**答：** 如果您在安裝 Diagnostics 1.1 時指定了 “–Version “1.*”"，下次角色或 VM 重新啟動時，便會自動更新為符合規則運算式 “1.*” 的最新版本。如果您在安裝 Diagnostics 1.1 時指定了 “–Version “1.1””，則可以重新執行 Set- Cmdlet 並指定您要安裝的版本，以更新為更新版本。

**問：** 資料表的命名方式為何？

**答：** 資料表的命名依據如下：

		if (String.IsNullOrEmpty(eventDestination)) {
		    if (e == "DefaultEvents")
		        tableName = "WADDefault" + MD5(provider);
		    else
		        tableName = "WADEvent" + MD5(provider) + eventId;
		}
		else
		    tableName = "WAD" + eventDestination;

下列是一個範例：

		<EtwEventSourceProviderConfiguration provider=”prov1”>
		  <Event id=”1” />
		  <Event id=”2” eventDestination=”dest1” />
		  <DefaultEvents />
		</EtwEventSourceProviderConfiguration>
		<EtwEventSourceProviderConfiguration provider=”prov2”>
		  <DefaultEvents eventDestination=”dest2” />
		</EtwEventSourceProviderConfiguration>

這會產生 4 個資料表：

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
	<tr>
			<td style="width: 100px;"><strong>事件</strong></td>
			<td><strong>資料表名稱</strong></td>			
	</tr>
	<tr>
			<td>provider=”prov1” &lt;Event id=”1” /></td>
			<td>WADEvent+MD5(“prov1”)+”1”</td>			
	</tr>
	<tr>
			<td>provider=”prov1” &lt;Event id=”2” eventDestination=”dest1” /></td>
			<td>WADdest1</td>			
	</tr>
	<tr>
			<td>provider=”prov1” &lt;DefaultEvents /></td>
			<td>WADDefault+MD5(“prov1”)</td>			
	</tr>
	<tr>
			<td>provider=”prov2” &lt;DefaultEvents eventDestination=”dest2” /></td>
			<td>WADdest2</td>			
	</tr>
	

</table>
</tbody>

## 比較 Azure 診斷版本

下表比較 Azure Diagnostics 1.0 版與 1.1/1.2/1.3 版所支援的功能：

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
	<tr>
			<td style="width: 100px;"><strong>支援的角色類型</strong></td>
			<td><strong>Diagnostics 1.0</strong></td>
			<td><strong>Diagnostics 1.1/1.2/1.3</strong></td>
	</tr>

	<tr>
			<td>Web 角色</td>
			<td>是</td>
			<td>是</td>
	</tr>
	<tr>
			<td>背景工作角色</td>
			<td>是</td>
			<td>是</td>
	</tr>
	<tr>
			<td>IaaS</td>
			<td>否</td>
			<td>是</td>
	</tr>
</tbody>
</table>

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
	<tr>
			<td style="width: 100px;"><strong>組態和部署</strong></td>
			<td><strong>Diagnostics 1.0</strong></td>
			<td><strong>Diagnostics 1.1/1.2/1.3</strong></td>
	</tr>

	<tr>
			<td>與 Visual Studio 的整合 - 已整合到 Azure Web/背景工作開發體驗。</td>
			<td>是</td>
			<td>否</td>
	</tr>
	<tr>
			<td>PowerShell 指令碼- 用於管理在角色上安裝及設定診斷的指令碼。</td>
			<td>是</td>
			<td>是</td>
	</tr>
	
</tbody>
</table>

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
	<tr>
			<td style="width: 100px;"><strong>資料來源</strong></td>
			<td><strong>預設集合</strong></td>
			<td><strong>格式</strong></td>
			<td><strong>說明</strong></td>
			<td><strong>Diagnostics 1.0</strong></td>
			<td><strong>Diagnostics 1.1/1.2</strong></td>
			<td><strong>Diagnostics 1.3</strong></td>
	</tr>
	<tr>
			<td>System.Diagnostics.Trace 記錄檔</td>
			<td>是</td>
			<td>資料表</td>
			<td>記錄從您的程式碼傳送至追蹤接聽項的追蹤訊息 (追蹤接聽項必須加入 web.config 或 app.config 檔)。記錄檔資料將依 scheduledTransferPeriod 傳輸間隔傳輸至儲存體資料表 WADLogsTable。</td>
			<td>是</td>
			<td>否 (使用 EventSource)</td>
			<td>是</td>
	</tr>
	<tr>
			<td>IIS 記錄檔</td>
			<td>是</td>
			<td>Blob</td>
			<td>記錄 IIS 網站的相關資訊。記錄檔資料將依 scheduledTransferPeriod 傳輸間隔傳輸至您指定的容器。</td>
			<td>是</td>
			<td>是</td>
			<td>是</td>
	</tr>
	<tr>
			<td>Azure 診斷基礎結構記錄檔</td>
			<td>是</td>
			<td>資料表</td>
			<td>記錄診斷基礎結構、RemoteAccess 模組和 RemoteForwarder 模組的相關資訊。記錄檔資料將依 scheduledTransferPeriod 傳輸間隔傳輸至儲存體資料表 WADDiagnosticInfrastructureLogsTable。</td>
			<td>是</td>
			<td>是</td>
			<td>是</td>
	</tr>
	<tr>
			<td>IIS 失敗要求記錄檔</td>
			<td>否</td>
			<td>Blob</td>
			<td>記錄 IIS 網站或應用程式之失敗要求的相關資訊。您也必須透過在 Web.config 的 system.WebServer 下設定追蹤選項來加以啟用。記錄檔資料將依 scheduledTransferPeriod 傳輸間隔傳輸至您指定的容器。</td>
			<td>是</td>
			<td>是</td>
			<td>是</td>
	</tr>
	<tr>
			<td>Windows 事件記錄檔</td>
			<td>否</td>
			<td>資料表</td>
			<td>記錄作業系統、應用程式或驅動程式之執行效能的相關資訊。您必須明確指定效能計數器。加入這些記錄檔時，效能計數器資料會依 scheduledTransferPeriod 傳輸間隔傳輸至儲存體資料表 WADPerformanceCountersTable。</td>
			<td>是</td>
			<td>是</td>
			<td>是</td>
	</tr>
	<tr>
			<td>效能計數器</td>
			<td>否</td>
			<td>資料表</td>
			<td>記錄作業系統、應用程式或驅動程式之執行效能的相關資訊。您必須明確指定效能計數器。加入這些記錄檔時，效能計數器資料會依 scheduledTransferPeriod 傳輸間隔傳輸至儲存體資料表 WADPerformanceCountersTable。</td>
			<td>是</td>
			<td>是</td>
			<td>是</td>
	</tr>
	<tr>
			<td>損毀傾印</td>
			<td>否</td>
			<td>Blob</td>
			<td>記錄系統當機時之作業系統狀態的相關資訊。會在本機收集小型損毀傾印。您可以啟用完整傾印。記錄檔資料將依 scheduledTransferPeriod 傳輸間隔傳輸至您指定的容器。由於 ASP.NET 處理大多數例外狀況，這通常只對背景工作角色或 VM 才有用。</td>
			<td>是</td>
			<td>是</td>
			<td>是</td>
	</tr>
	<tr>
			<td>自訂錯誤記錄檔</td>
			<td>否</td>
			<td>Blob</td>
			<td>透過本機儲存體資源，您可以記錄自訂資料並立即傳輸至您指定的容器。</td>
			<td>是</td>
			<td>是</td>
			<td>是</td>
	</tr>
	<tr>
			<td>EventSource</td>
			<td>否</td>
			<td>資料表</td>
			<td>記錄您的程式碼使用 .NET EventSource 類別所產生的事件。</td>
			<td>否</td>
			<td>是</td>
			<td>是</td>
	</tr>
	<tr>
			<td>以資訊清單為基礎的 ETW</td>
			<td>否</td>
			<td>資料表</td>
			<td>任何處理序所產生的 ETW 事件。</td>
			<td>否</td>
			<td>是</td>
			<td>是</td>
	</tr>
</tbody>
</table>

## 其他資源

- [開發 Azure 應用程式的疑難排解最佳作法][]
- [使用 Windows Azure 診斷收集記錄資料][]
- [偵錯 Azure 應用程式][]
- [為 Azure 雲端服務和虛擬機器設定診斷功能][]

  

[Overview]: #overview
[How to Enable Diagnostics in a Worker Role]: #worker-role
[How to Enable Diagnostics in a Virtual Machine]: #virtual-machine
[Sample Configuration File and Schema]: #configuration-file-schema
[Troubleshooting]: #troubleshooting
[Frequently Asked Questions]: #faq
[比較 Azure 診斷版本]: #comparing
[Additional Resources]: #additional
[EventSource 類別]: http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx
  
[為 Azure 雲端服務和虛擬機器設定診斷功能]: http://msdn.microsoft.com/library/windowsazure/dn186185.aspx
[偵錯 Azure 應用程式]: http://msdn.microsoft.com/library/windowsazure/ee405479.aspx
[使用 Windows Azure 診斷收集記錄資料]: http://msdn.microsoft.com/library/windowsazure/gg433048.aspx
[開發 Azure 應用程式的疑難排解最佳作法]: http://msdn.microsoft.com/library/windowsazure/hh771389.aspx
[免費試用版]: http://azure.microsoft.com/pricing/free-trial/
[安裝及設定 Azure PowerShell 0.8.7 版或更新版本]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/
[Azure Diagnostics 1.2 組態結構描述]: http://msdn.microsoft.com/library/azure/dn782207.aspx
[Set-AzureServiceDiagnosticsExtension]: http://msdn.microsoft.com/library/dn495270.aspx
[Get-AzureServiceDiagnosticsExtension]: http://msdn.microsoft.com/library/dn495145.aspx
[Remove-AzureServiceDiagnosticsExtension]: http://msdn.microsoft.com/library/dn495168.aspx
 

<!---HONumber=62-->