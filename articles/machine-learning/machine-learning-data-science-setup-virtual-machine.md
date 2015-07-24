<properties 
	pageTitle="設定適用於資料科學的 Azure 虛擬機器" 
	description="設定 Azure 虛擬機器，在使用 IPython 伺服器的雲端架構資料科學環境中使用。" 
	metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="msolhab" 
	manager="jacob.spoelstra" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/17/2015" 
	ms.author="mohabib;xibingao;bradsev" />

# 設定適用於資料科學的 Azure 虛擬機器

本主題示範如何佈建及設定 Azure 虛擬機器，可用來做為雲端架構資料科學環境的一部分。Windows 虛擬機器是使用支援工具 (例如，IPython Notebook、Azure 儲存體總管、AzCopy)，以及其他對於資料科學專案非常實用的公用程式來設定。例如，Azure 儲存體總管和 AzCopy 會提供便利的方法，將資料從本機電腦上傳至 Azure Blob 儲存體，或者從 Blob 儲存體將資料下載到本機電腦。

## <a name="create-vm"></a>步驟 1：建立一般用途的 Azure 虛擬機器

如果您已經具有 Azure 虛擬機器，而且只想在其上設定 IPython Notebook 伺服器，就可以略過此步驟，繼續執行[步驟 2：將 IPython Notebook 的端點新增到現有的虛擬機器](#add-endpoint)。 
 
在 Azure 上建立虛擬機器的程序開始之前，您必須決定處理適用於其專案之資料所需的機器大小。比起較大型的機器，較小型的機器配備較少的記憶體和較少的 CPU 核心數目，但價格也較便宜。如需機器類型和價格清單，請參閱[虛擬機器定價](http://azure.microsoft.com/pricing/details/virtual-machines/)頁面 

1. 登入 https://manage.windowsazure.com，然後按一下左下角的 [**新增**]。隨即會快顯一個視窗。依序選取 [**計算**] -> [**虛擬機器**] -> [**從組件庫**]。

	![Create workspace][24]

2. 選擇下列其中一個映像： 

	* Windows Server 2012 R2 Datacenter 
	* Windows Server Essentials 體驗 (Windows Server 2012 R2) 

	然後按一下右下方指向右側的箭號，前往下一個設定頁面。
	
	![Create workspace][25]

3. 輸入您想要建立的虛擬機器名稱、根據機器即將處理的資料大小和您想要的機器功能有多強大來選取機器的大小 (記憶體大小和運算核心數目)、輸入機器的使用者名稱和密碼。接著，按一下指向右側的箭號，前往下一個設定頁面。

	![Create workspace][26]

4. 選取 [**區域/同質群組/虛擬網路**]，其中包含您規劃要用於此虛擬機器的 [**儲存體帳戶**]，然後選取該儲存體帳戶。藉由輸入端點的名稱 (此處為 "IPython")，在 [**端點**] 欄位底部新增端點。您可以選擇任何字串做為端點的 [**名稱**]，以及任何介於 0 和 65536 之間的整數，**可用來**做為 [**公用連接埠**]。[**私用連接埠**] 必須為 **9999**。使用者應該**避免**使用已經針對網際網路服務指派的公用連接埠。[適用於網際網路服務的連接埠](http://www.chebucto.ns.ca/~rakerman/port-table.html)會提供已指派且應避免的連接埠清單。 

	![Create workspace][27]

	>[AZURE.NOTE] 如果已在此步驟中新增端點，就可以略過[步驟 2：將 IPython Notebook 的端點新增到現有的虛擬機器](#add-endpoint)。

5. 按一下勾號以啟動虛擬機器佈建程序。 

	![Create workspace][28]


它可能需要 15-25 分鐘，才能完成虛擬機器佈建程序。建立虛擬機器之後，此機器的狀態應顯示為 [**執行中**]。

![Create workspace][29]
	
## <a name="add-endpoint"></a>步驟 2：將 IPython Notebook 的端點新增到現有的虛擬機器

如果您已遵循步驟 1 中的指示建立虛擬機器，則已經新增適用於 IPython Notebook 的端點，可略過此步驟。 

如果虛擬機器已經存在，而且您需要新增將在以下步驟 3 中安裝的 IPython Notebook 端點，請先登入 Azure 管理入口網站、選取虛擬機器，然後新增 IPython Notebook 伺服器的端點。下圖包含在將 IPython Notebook 的端點新增到 Windows 虛擬機器之後入口網站的螢幕擷取畫面。 

![Create workspace][17]

## <a name="run-commands"></a>步驟 3：安裝 IPython Notebook 和其他支援工具

建立虛擬機器之後，請使用遠端桌面通訊協定 (RDP) 登入 Windows 虛擬機器。如需指示，請參閱[如何登入執行 Windows Server 的虛擬機器](../virtual-machines-log-on-windows-server.md)。以系統管理員身分開啟 [**命令提示字元**] (**不是 Powershell 命令視窗**)，並執行下列命令。
 
    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/MachineSetup/Azure_VM_Setup_Windows.ps1'

	@powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

完成安裝時，IPython Notebook 伺服器會在 *C:\Users\&#60;user name>\Documents\IPython Notebooks* 目錄中自動啟動。

出現提示時，請輸入 IPython Notebook 的密碼，以及機器系統管理員的密碼。這讓 IPython Notebook 能夠做為機器上的服務來執行 。 

## <a name="access"></a>步驟 4：從 Web 瀏覽器存取 IPython Notebook
若要存取 IPython Notebook 伺服器，請開啟 Web 瀏覽器，然後在 [URL] 文字方塊中輸入 *https://&#60;virtual machine DNS name>:&#60;public port number>*。在此處，*&#60;公用連接埠號碼>* 應該是您在新增 IPython Notebook 端點時所指定的連接埠號碼。如果您選擇 *443* 做為公用連接埠號碼，就可以存取 IPython Notebook，而不需在 [URL] 文字方塊中明確指定連接埠號碼。否則，就需要提供 **&#60;公用連接埠號碼>*。 

*&#60;虛擬機器 DNS 名稱>* 可在 Azure 的管理入口網站中找到。登入管理入口網站之後，按一下 [**虛擬機器**]、選取您建立的機器，然後選取 [**儀表板**]，DNS 名稱隨即顯示，如下所示：

![Create workspace][19]

您將會遇到一個警告，指出「_這個網站的安全性憑證有問題_」(Internet Explorer) 或「_您的連線不是私用的_」(Chrome)，如下圖所示。按一下 [**繼續瀏覽此網站 (不建議)**] (Internet Explorer)，或者按一下 [**進階**]，然後 [**繼續前往 &#60;*DNS 名稱*> (不安全)**] (Chrome)，以便繼續進行。接著，輸入您先前指定的密碼來存取 IPython Notebook。

Internet Explorer：
![Create workspace][20]

Chrome：
![Create workspace][21]

登入 IPython Notebook 之後，目錄 *DataScienceSamples* 將顯示於瀏覽器上。此目錄包含由 Microsoft 共用的 IPython Notebook 範例，可協助使用者進行資料科學工作。這些 IPython Notebook 範例是在 IPython Notebook 伺服器設定程序期間，從 [**Github 儲存機制**](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) 簽出至虛擬機器。Microsoft 會經常維護並更新此儲存機制。使用者可以瀏覽 Github 儲存機制，以取得最近更新的 IPython Notebook 範例。 
![Create workspace][18]

## <a name="upload"></a>步驟 5：從本機電腦將現有的 IPython Notebook 上傳至 IPython Notebook 伺服器

IPython Notebook 提供一種簡單的方式，讓使用者可將其本機電腦上現有的 IPython Notebook 上傳至虛擬機器上的 IPython Notebook 伺服器。當使用者在 Web 瀏覽器上登入 IPython Notebook 之後，可按一下以進入將上傳 IPython Notebook 的**目錄**。然後，在 [**檔案總管**] 中選取要從本機電腦上傳的 IPython Notebook .ipynb 檔案，並將它拖放到 Web 瀏覽器上的 IPython Notebook 目錄。按一下 [**上傳**] 按鈕，將 .ipynb 檔案上傳到 IPython Notebook 伺服器。其他使用者接著就可以從 Web 瀏覽器開始使用它。

![Create workspace][22]

![Create workspace][23]


## <a name="shutdown"></a>關閉並取消配置未使用的虛擬機器

Azure 虛擬機器的定價策略是「**只針對您使用的項目進行付費**」。若要確定您在不使用虛擬機器時不會被計費，則當它閒置時，其狀態必須是 [**已停止 (已取消配置)**] 狀態。

> [AZURE.NOTE] 如果您從 VM 內部關閉虛擬機器 (使用 Windows 電源選項)，雖然 VM 已停止，但仍然處於已配置狀態。若要確保您不會繼續被計費，一律從 [Azure 管理入口網站](http://manage.windowsazure.com/)停止虛擬機器。您也可以藉由呼叫 **ShutdownRoleOperation** 搭配相當於 "StoppedDeallocated" 的 "PostShutdownAction"，透過 Powershell 來停止 VM。

關閉及取消配置虛擬機器：

1. 使用您的帳戶登入 [Azure 管理入口網站](http://manage.windowsazure.com/)。  

2. 從左側導覽列選取 [**虛擬機器**]。

3. 在虛擬機器清單中，按一下虛擬機器的名稱，然後前往 [**儀表板**] 頁面。

4. 按一下頁面底部的 [**關閉**]。 

![VM Shutdown][15]

虛擬機器將會取消配置，但不是刪除。您隨時都可從 Azure 管理入口網站重新啟動您的虛擬機器。

## 您的 Azure VM 已準備好可供使用：下一步是什麼？

您的虛擬機器已經準備好在資料科學練習中使用。虛擬機器也已經準備好用來做為 IPython Notebook 伺服器，以用於進行資料探索和處理，以及其他可與 Azure Machine Learning 和雲端資料科學程序一起使用的工作。 

資料科學程序中的後續步驟已顯示於[學習指南：Azure 中的進階資料處理](machine-learning-data-science-advanced-data-processing.md)中的地圖上，而且可能包含將資料移至 HDInsight、在那裡處理資料並進行取樣，以準備使用 Azure Machine Learning 從資料中進行學習的步驟。

* 如需如何從 Azure Blob 儲存體將資料移至 HDInsight 的相關資訊，請參閱[建立並將資料從 Azure Blob 儲存體載入 Hive 資料表](machine-learning-data-science-hive-tables.md)。
* 如需在 HDInsight 中使用 Hive 查詢處理資料的相關資訊，請參閱[在雲端資料科學程序中將 Hive 查詢提交至 HDInsight Hadoop 叢集](machine-learning-data-science-process-hive-tables.md)。
* 如需在 HDInsight 中取樣資料的相關資訊，請參閱[在 Azure HDInsight Hive 資料表中進行資料取樣](machine-learning-data-science-sample-data-hive.md)。



[15]: ./media/machine-learning-data-science-setup-virtual-machine/vmshutdown.png
[17]: ./media/machine-learning-data-science-setup-virtual-machine/add-endpoints-after-creation.png
[18]: ./media/machine-learning-data-science-setup-virtual-machine/sample-ipnbs.png
[19]: ./media/machine-learning-data-science-setup-virtual-machine/dns-name-and-host-name.png
[20]: ./media/machine-learning-data-science-setup-virtual-machine/browser-warning-ie.png
[21]: ./media/machine-learning-data-science-setup-virtual-machine/browser-warning.png
[22]: ./media/machine-learning-data-science-setup-virtual-machine/upload-ipnb-1.png
[23]: ./media/machine-learning-data-science-setup-virtual-machine/upload-ipnb-2.png
[24]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-1.png
[25]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-2.png
[26]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-3.png
[27]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-4.png
[28]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-5.png
[29]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-6.png




<!--HONumber=49--> 