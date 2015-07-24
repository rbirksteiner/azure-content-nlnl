<properties 
	pageTitle="使用 HDInsight 開發指令碼動作| Microsoft Azure" 
	description="了解如何使用指令碼動作來自訂 Hadoop 叢集。" 
	services="hdinsight" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/10/2015" 
	ms.author="bradsev"/>

# 使用 HDInsight 開發指令碼動作 

指令碼動作可提供 Azure HDInsight 功能，以供用來安裝其他在 Hadoop 叢集上執行的軟體，或變更叢集上所安裝應用程式的組態。指令碼動作是在部署 HDInsight 叢集時，在叢集節點上執行的指令碼，一旦叢集中的節點完成 HDInsight 組態之後，就會執行這些指令碼動作。指令碼動作是依據系統管理員帳戶的權限來執行，並具有叢集節點的完整存取權限。您可對每個叢集提供一份依其指定順序來執行的指令碼動作清單。

您可以從 Azure PowerShell 或使用 HDInsight .NET SDK 來部署「指令碼動作」。如需詳細資訊，請參閱[使用指令碼動作自訂 HDInsight 叢集][hdinsight-cluster-customize]。



## <a name="bestPracticeScripting"></a>指令碼開發的最佳做法

為 HDInsight 叢集開發自訂的指令碼時，有數個最佳做法需要牢記在心：

* [檢查 Hadoop 版本](#bPS1)
* [提供穩定的指令碼資源連結](#bPS2)
* [確保叢集自訂指令碼具有等冪性](#bPS3)
* [在最佳位置安裝自訂元件](#bPS4)
* [確保叢集架構具有高可用性](#bPS5)
* [設定自訂元件來使用 Azure Blob 儲存體](#bPS6)

### <a name="bPS1"></a>檢查 Hadoop 版本
只有 HDInsight 3.1 版 (Hadoop 2.4) 和以上版本才支援使用指令碼動作在叢集上安裝自訂元件。在自訂指令碼中，您必須使用 **Get-HDIHadoopVersion** 協助程式方法，先檢查 Hadoop 的版本，再繼續執行指令碼中的其他工作。


### <a name="bPS2"></a>提供穩定的指令碼資源連結 
使用者應該確定在叢集的整個存留期間，於叢集自訂中使用的所有指令碼及其他成品都保持可用，並且這些檔案的版本在此持續時間內不會變更。如需為叢集中的節點重新製作映像，就必須有這些資源。最佳做法是下載並封存使用者所控制之儲存體帳戶中的所有項目。這可以是預設的儲存體帳戶，或是在部署時為自訂叢集指定的任何其他儲存體帳戶。例如，在文件中所提供的 Spark 和 R 自訂叢集範例中，我們已經在本機複製一份此儲存體帳戶中的資源：https://hdiconfigactions.blob.core.windows.net/


### <a name="bPS3"></a>確保叢集自訂指令碼具有等冪性
您必須預期在叢集存留期間將會為 HDInsight 叢集的節點重新製作映像。每當重新製作叢集映像時，都會執行叢集自訂指令碼。此指令碼必須設計成具有等冪性，意思就是在重新製作映像時，此指令碼應該確保叢集會回到與當初建立叢集時，指令碼剛剛第一次執行後相同的自訂狀態。例如，如果自訂指令碼在第一次執行時，在 D:\\AppLocation 中安裝了某個應用程式，則在後續每次的執行中，當重新製作映像時，此指令碼應該先檢查 D:\\AppLocation 位置中是否有該應用程式，再繼續執行指令碼中的其他步驟。


### <a name="bPS4"></a>在最佳位置安裝自訂元件 
重新製作叢集節點映像時，C:\\ 資源磁碟機和 D:\\ 系統磁碟機可能被重新格式化，而導致資料及安裝在這些磁碟機上的應用程式遺失。如果隸屬於叢集的 Azure 虛擬機器 (VM) 節點故障，而被新節點取代時，也可能發生這種情況。您可以將元件安裝在 D:\\ 磁碟機上，或叢集上的 C:\\apps 位置中。C:\\ 磁碟機上的所有其他位置則已預留他用。請在叢集自訂指令碼中指定要用來安裝應用程式或程式庫的位置。


### <a name="bPS5"></a>確保叢集架構具有高可用性

為了獲得高可用性，HDInsight 具備主動/被動架構，在此架構中，有一個處於使用中模式的前端節點 (此節點正在執行 HDInsight 服務)，以及另一個處於待命模式的前端節點 (此節點未執行 HDInsight 服務)。如果 HDInsight 服務中斷，這兩個節點就會切換主動和被動模式。如果為了獲得高可用性，而使用指令碼動作在這兩個前端節點安裝服務，請注意 HDInsight 的容錯移轉機制並無法自動容錯移轉這些由使用者所安裝的服務。因此使用者在 HDInsight 前端節點上所安裝的服務若想要有高可用性，則必須有自己的主動/被動模式時的容錯移轉機制，或是處於主動/被動模式。

當您指定前端節點角色做為 *ClusterRoleCollection* 參數的值時 (記載於下面的[如何執行指令碼動作](#runScriptAction)一節)，HDInsight 指令碼動作命令會同時在這兩個前端節點上執行。因此，當您設計自訂指令碼時，請確定您的指令碼知道這項設定。您不應該發生在這兩個前端節點上安裝並啟動相同的服務，而最終導致彼此競爭的問題。此外也請注意，重新製作映像時，資料將會遺失，因此透過指令碼動作所安裝的軟體必須要能夠從這類事件復原。應用程式在設計上應該要能夠與分散在眾多節點上的高可用性資料搭配運作。請注意，最多可同時為叢集中 1/5 的節點重新製作映像。


### <a name="bPS6"></a>設定自訂元件來使用 Azure Blob 儲存體
您安裝在叢集節點上的自訂元件可能有使用 Hadoop 分散式檔案系統 (HDFS) 儲存體的預設組態。您應該變更此組態，使其改為使用 Azure Blob 儲存體。在重新製作叢集映像時，會格式化 HDFS 檔案系統，因此您會遺失儲存在其中的所有資料。改用 Azure Blob 儲存體可確保資料保留下來。

## <a name="helpermethods"></a>自訂指令碼的協助程式方法 

指令碼動作提供下列可在撰寫自訂指令碼時使用的協助程式方法。

協助程式方法 | 說明
-------------- | -----------
**Save-HDIFile** | 從指定的統一資源識別項 (URI) 將檔案下載到與指派給叢集的 Azure VM 節點相關聯的本機磁碟位置。
**Expand-HDIZippedFile** | 將壓縮檔解壓縮。
**Invoke-HDICmdScript** | 從 cmd.exe 執行指令碼。
**Write-HDILog** | 撰寫用於指令碼動作之自訂指令碼的輸出。
**Get-Services** | 取得執行指令碼的機器上所執行之服務的清單。
**Get-Service** | 輸入特定服務名稱，來取得執行指令碼的機器上特定服務的詳細資訊 (服務名稱、處理序識別碼、狀態等)。
**Get-HDIServices** | 取得執行指令碼的電腦上所執行之 HDInsight 服務的清單。
**Get-HDIService** | 輸入特定 HDInsight 服務名稱，來取得執行指令碼的機器上特定服務的詳細資訊 (服務名稱、處理序識別碼、狀態等)。
**Get-ServicesRunning** | 取得執行指令碼的電腦上所執行之服務的清單。
**Get-ServiceRunning** | 檢查執行指令碼的電腦上是否在執行特定服務 (依名稱)。
**Get-HDIServicesRunning** | 取得執行指令碼的電腦上所執行之 HDInsight 服務的清單。
**Get-HDIServiceRunning** | 檢查執行指令碼的電腦上是否在執行特定 HDInsight 服務 (依名稱)。
**Get-HDIHadoopVersion** | 取得執行指令碼的電腦上所安裝的 Hadoop 版本。
**Test-IsHDIHeadNode** | 檢查執行指令碼的電腦是否為前端節點。
**Test-IsActiveHDIHeadNode** | 檢查執行指令碼的電腦是否為作用中前端節點。
**Test-IsHDIDataNode** | 檢查執行指令碼的電腦是否為資料節點。
**Edit-HDIConfigFile** | 編輯組態檔 hive-site.xml、core-site.xml、hdfs-site.xml、mapred-site.xml 或 yarn-site.xml。

## <a name="commonusage"></a>常見使用模式

本節指引您如何實作某些撰寫自訂指令碼時可能遇到的常見使用模式。

### 設定環境變數

在開發指令碼動作時，您往往會感到需要設定環境變數。例如，最可能的案例是當您從外部網站下載二進位檔，將它安裝在叢集上，然後將其安裝位置加入 ‘PATH’ 環境變數中。下列程式碼片段顯示如何在自訂指令碼中設定環境變數。

	Write-HDILog "Starting environment variable setting at: $(Get-Date)";
	[Environment]::SetEnvironmentVariable('MDS_RUNNER_CUSTOM_CLUSTER', 'true', 'Machine');

此陳述式將環境變數 **MDS_RUNNER_CUSTOM_CLUSTER** 設為 'true' 值，並將此變數的範圍設為整個機器。有時候您必須將環境變數設定在適當範圍 – 機器或使用者。如需設定環境變數的詳細資訊，請參閱[這裡][1]。

### 存取自訂指令碼儲存所在位置

用來自訂叢集的指令碼必須位於叢集的預設儲存體帳戶，或是位於其他任何儲存體帳戶上的公用唯讀容器。如果指令碼存取位於他處的資源，則這些資源必須是可公開存取的資源 (至少是公用唯讀狀態)。例如，您可能會想要用 SaveFile-HDI 命令存取檔案並加以儲存。

	Save-HDIFile -SrcUri 'https://somestorageaccount.blob.core.windows.net/somecontainer/some-file.jar' -DestFile 'C:\apps\dist\hadoop-2.4.0.2.1.9.0-2196\share\hadoop\mapreduce\some-file.jar'

在此範例中，您必須確定儲存體帳戶 'somestorageaccount' 中的容器 'somecontainer' 可公開存取。否則，指令碼將會擲回「找不到」例外狀況而且失敗。

### 對於失敗的叢集部署擲回例外狀況

如果您想要在叢集自訂程序未如預料般成功時準確收到通知，請務必擲回例外狀況並將叢集佈建作業判定為失敗。例如，您可能會想要在檔案存在時予以進行處理，並處理檔案不存在時的錯誤狀況。此做法可確保指令碼正常結束，並正確得知叢集的狀態。下列程式碼片段示範如何實現此一目的：

	If(Test-Path($SomePath)) {
		#Process file in some way
	} else {
		# File does not exist; handle error case
		# Print error message
	exit
	}

在此程式碼片段中，如果檔案不存在，則會導致指令碼在列印錯誤訊息後確實正常結束的狀態，而叢集會達到執行中狀態 (前提是它「成功」完成叢集自訂程序)。如果您要精確地獲知叢集自訂作業因為遺漏檔案而未如預期般成功的事實，就更適合擲回例外狀況並使叢集自訂步驟失敗。若要達到這個目的，您必須改用下列範例程式碼片段。

	If(Test-Path($SomePath)) {
		#Process file in some way
	} else {
		# File does not exist; handle error case
		# Print error message
	throw
	}


## <a name="deployScript"></a>指令碼動作的部署檢查清單
以下是我們在準備部署這些指令碼時所採取的步驟：

1. 將包含自訂指令碼的檔案放在叢集節點可於部署期間存取的位置。此位置可以是部署叢集時所指定的預設或其他儲存體帳戶，或是其他任何可公開存取的儲存體容器。
2. 在指令碼中加入檢查以確定它們以等冪方式執行，使得指令碼可以在相同的節點上執行多次。
3. 使用 **Write-Output** Azure PowerShell Cmdlet 來列印至 STDOUT 以及 STDERR。請勿使用 **Write-Host**。
4. 使用暫存檔案資料夾 (例如 $env:TEMP) 來存放指令碼所使用的下載檔案，然後在執行完指令碼之後將這些檔案清除。
5. 安裝自訂軟體，位置只能是 D:\\ 或 C:\\apps。不應該使用 C: 磁碟機上的其他位置，因為它們已預留他用。請注意，如果將檔案安裝在 C: 磁碟機上 C:\\apps 資料夾以外的位置，可能會導致在重新製作節點映像期間設定失敗。
6. 如果作業系統層級設定或 Hadoop 服務組態檔已變更，您可能會想要重新啟動 HDInsight 服務，讓它們可以載入任何作業系統層級設定，例如指令碼中設定的環境變數。


## <a name="runScriptAction"></a>如何執行指令碼動作

您可以透過 Azure 入口網站、Azure PowerShell 或 HDInsight .NET SDK，使用指令碼動作自訂 HDInsight 叢集。如需指示，請參閱[如何使用指令碼動作](../hdinsight-hadoop-customize-cluster/#howto)。


## <a name="sampleScripts"></a>自訂指令碼範例

Microsoft 提供了在 HDInsight 叢集上安裝元件的範例指令碼。您可以在下方的連結中找到這些範例指令碼和其使用方式的說明：

- [在 HDInsight 叢集上安裝及使用 Spark][hdinsight-install-spark]
- [在 HDInsight Hadoop 叢集上安裝和使用 R][hdinsight-r-scripts]
- [在 HDInsight 叢集上安裝及使用 Solr](../hdinsight-hadoop-solr-install)
- [在 HDInsight 叢集上安裝及使用 Giraph](../hdinsight-hadoop-giraph-install)  

> [AZURE.NOTE]範例指令碼只能與 HDInsight 叢集版本 3.1 或更高版本搭配使用。如需 HDInsight 叢集版本的詳細資訊，請參閱 [HDInsight 叢集版本](../hdinsight-component-versioning/)。

## <a name="testScript"></a>如何使用 HDInsight Emulator 測試自訂指令碼

在將自訂指令碼用於 HDInsight 指令碼動作命令前若要先進行測試，最簡單的方式是在 HDInsight Emulator 中執行看看。您可以將 HDInsight Emulator 安裝在本機上、安裝在 Azure 基礎結構即服務 (IaaS) Windows Server 2012 R2 VM 上，或安裝在本機機器上，然後看看指令碼執行起來是否正確。但請注意，Windows Server 2012 R2 VM 是 HDInsight 用於其節點的相同 VM。

本節概述的是在本機使用 HDInsight Emulator 進行測試的程序，但與使用 VM 的程序是類似的。

**安裝 HDInsight Emulator** - 若要在本機執行指令碼動作，您必須安裝 HDInsight Emulator。如需其安裝方式的說明，請參閱[開始使用 HDInsight Emulator](../hdinsight-get-started-emulator/)。

**設定 Azure PowerShell 的執行原則** - 開啟 Azure PowerShell，並以系統管理員身分執行下列命令，將執行原則設定為 [*LocalMachine*] 以及 [*不受限制*]：
 
	Set-ExecutionPolicy Unrestricted –Scope LocalMachine

指令碼未經過簽署，因此需要讓此原則不受限制。

**下載指令碼動作**以便在本機目的地執行。下列範例指令碼可從下列位置中下載：

* **Spark**：https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1
* **R**：https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1
* **Solr**：https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1
* **Giraph**：https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1

**執行指令碼動作** - 以系統管理員模式開啟新的 Azure PowerShell 視窗，並從本機儲存位置執行 Spark 或 R 安裝指令碼。

**使用範例** - 當您使用 Spark 和 R 叢集時，HDInsight Emulator 中可能沒有所需的資料檔案。因此您可能需要將含有資料的相關 .txt 檔案上傳到 HDFS 中的路徑，然後使用該路徑存取資料。例如：

	val file = sc.textFile("/example/data/gutenberg/davinci.txt")


請注意，在某些情況下，自訂指令碼可能會實際依存於 HDInsight 元件，例如偵測特定 Hadoop 服務是否已啟動。在此情況下，您將必須把自訂指令碼部署到實際的 HDInsight 叢集，以測試這些自訂指令碼。


## <a name="debugScript"></a>如何對自訂指令碼進行偵錯

指令碼錯誤記錄檔會與其他輸出一起儲存在您建立叢集時為其指定的預設儲存體帳戶中。記錄檔是以 *u<\\cluster-name-fragment><\\time-stamp>setuplog* 的名稱儲存在資料表中。這些是彙總的記錄檔，包含來自指令碼執行所在之所有叢集節點 (前端節點和背景工作節點) 的記錄。

您也可以遠端登入到叢集節點以查看 STDOUT 和 STDERR 中的自訂指令碼。每個節點上的記錄檔都只與該節點有關，並且會記錄到 **C:\\HDInsightLogs\\DeploymentAgent.log**。這些記錄檔會記錄自訂指令碼的所有輸出。Spark 指令碼動作的範例記錄程式碼片段看起來像這樣：

	Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand; Details : BEGIN: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.; 
	Version : 2.1.0.0; 
	ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692; 
	AzureVMName : HEADNODE0; 
	IsException : False; 
	ExceptionType : ; 
	ExceptionMessage : ; 
	InnerExceptionType : ; 
	InnerExceptionMessage : ; 
	Exception : ;
	...

	Starting Spark installation at: 09/04/2014 21:46:02 Done with Spark installation at: 09/04/2014 21:46:38;
	
	Version : 2.1.0.0; 
	ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692; 
	AzureVMName : HEADNODE0; 
	IsException : False; 
	ExceptionType : ; 
	ExceptionMessage : ; 
	InnerExceptionType : ; 
	InnerExceptionMessage : ; 
	Exception : ;
	...
	
	Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand; 
	Details : END: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.; 
	Version : 2.1.0.0; 
	ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692; 
	AzureVMName : HEADNODE0; 
	IsException : False; 
	ExceptionType : ; 
	ExceptionMessage : ; 
	InnerExceptionType : ; 
	InnerExceptionMessage : ; 
	Exception : ;

 
在此記錄中，清楚指出已經在名為 HEADNODE0 的 VM 上執行 Spark 指令碼動作，並且在執行期間沒有擲回任何例外狀況。

如果發生執行失敗的情況，描述它的輸出也會包含在這個記錄檔中。對可能發生的指令碼問題進行偵錯時，這些記錄檔中提供的資訊應該很有幫助。


## <a name="seeAlso"></a>另請參閱

[使用指令碼動作來自訂 HDInsight 叢集][hdinsight-cluster-customize]


[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-cluster-customize]: ../hdinsight-hadoop-customize-cluster
[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-r-scripts]: ../hdinsight-hadoop-r-scripts/
[powershell-install-configure]: ../install-configure-powershell/

<!--Reference links in article-->
[1]: https://msdn.microsoft.com/library/96xafkes(v=vs.110).aspx
 

<!---HONumber=July15_HO2-->