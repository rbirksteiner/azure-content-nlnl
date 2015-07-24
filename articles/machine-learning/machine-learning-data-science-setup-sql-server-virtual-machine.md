<properties
	pageTitle="將 Azure SQL Server 虛擬機器設定為 IPython Notebook 伺服器 | Microsoft Azure"
	description="使用 SQL Server 和 IPython Server 來設定資料科學虛擬機器。"
	services="machine-learning"
	solutions="" documentationCenter=""
	authors="msolhab" 
	manager="paulettm"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/29/2015"
	ms.author="mohabib;xibingao;bradsev" />

# 將 Azure SQL Server 虛擬機器設定為 IPython Notebook 伺服器供進階分析使用

本主題示範如何佈建及設定 SQL Server 虛擬機器，以用來做為雲端架構資料科學環境的一部分。Windows 虛擬機器是使用支援工具 (例如，IPython Notebook、Azure 儲存體總管及 AzCopy)，以及其他對於資料科學專案非常實用的公用程式來設定。例如，Azure 儲存體總管和 AzCopy 會提供便利的方法，將資料從本機電腦上傳至 Azure Blob 儲存體，或者從 Blob 儲存體將資料下載到本機電腦。

Azure 虛擬機器組件庫涵蓋數個包含 Microsoft SQL Server 的映像。選取適合您資料需求的 SQL Server VM 映像。建議的映像如下：

- SQL Server 2012 SP2 Enterprise，適用於小型到中型的資料大小
- SQL Server 2012 SP2 Enterprise Optimized for DataWarehousing Workloads，適用於大型到非常大型的資料大小

 >[AZURE.NOTE]SQL Server 2012 SP2 Enterprise 的映像**不包含資料磁碟**。您必須新增和 (或) 連接一或多個虛擬硬碟來儲存資料。當您建立 Azure 虛擬機器時，它會有一個作業系統的磁碟對應至 C 磁碟機，還有一個暫存磁碟對應至 D 磁碟機。請勿使用 D 磁碟機來儲存資料。顧名思義，它只提供暫存儲存空間。它並不提供備援或備份，因為它不在 Azure 儲存體內。


##<a name="Provision"></a>連線到 Azure 管理入口網站並佈建 SQL Server 虛擬機器

1.  使用您的帳戶登入 [Azure 管理入口網站](http://manage.windowsazure.com/)。如果您沒有 Azure 帳戶，請造訪 [Azure 免費試用](http://www.windowsazure.com/pricing/free-trial/)。

2.  在 Azure 管理入口網站中，依序按一下網頁左下角的 [**+NEW**]、[**運算**]、[**虛擬機器**] 和 [**從組件庫**]。

3.  在「**建立虛擬機器**」頁面上，根據您的資料需求選取包含 SQL Server 的虛擬機器映像，然後按一下頁面右下角的 [下一步] 箭號。如需 Azure 支援之 SQL Server 映像的最新資訊，請參閱 [Azure 虛擬機器中的 SQL Server](http://go.microsoft.com/fwlink/p/?LinkId=294720) 文件集內的[開始使用 Azure 虛擬機器中的 SQL Server](http://go.microsoft.com/fwlink/p/?LinkId=294719) 主題。

	![選取 SQL Server VM][1]

4.  在第一個 [**虛擬機器組態**] 頁面，請提供下列資訊：

    -   提供 [虛擬機器名稱]。
    -   在 [新使用者名稱] 方塊中，輸入 VM 本機系統管理員帳戶的唯一使用者名稱。
    -   在 [**新增密碼**] 方塊中，輸入增強式密碼。如需詳細資訊，請參閱[增強式密碼](http://msdn.microsoft.com/library/ms161962.aspx)。
    -   在 [**確認密碼**] 方塊中，重新輸入密碼。
    -   從 [**大小**] 下拉式清單中選取適當的大小。

     >[AZURE.NOTE]在佈建期間指定虛擬機器的大小：A2 是建議用於生產工作負載的最小大小。使用 SQL Server Enterprise Edition 時，虛擬機器的最小建議大小為 A3。當您使用 SQL Server Enterprise Edition 時，請選取 A3 或以上的大小。使用 SQL Server 2012 或 2014 Enterprise Optimized for Transactional Workloads 映像時，請選取 A4。使用 SQL Server 2012 或 2014 Enterprise Optimized for Data Warehousing Workloads 映像時，請選取 A7。選取的大小會限制可設定的資料磁碟數目。如需可用之虛擬機器大小和可連接至虛擬機器之資料磁碟數目的最新資訊，請參閱[虛擬機器](http://msdn.microsoft.com/library/azure/dn197896.aspx)。如需定價資訊，請參閱「[虛擬機器定價](http://azure.microsoft.com/pricing/details/virtual-machines/)」。

    按一下右下角的 [下一步] 箭頭以繼續操作。

    ![VM 組態][2]

5.  在第二個 [虛擬機器組態] 頁面上，請設定網路、儲存體和可用性的資源：

    -   在 [雲端服務] 方塊中，選擇 [Create a new cloud service]。
    -   在 [Cloud Service DNS Name] 方塊中，提供選擇之 DNS 名稱的第一個部分，使其形成 **TESTNAME.cloudapp.net** 格式的名稱
    -   在 [REGION/AFFINITY GROUP/VIRTUAL NETWORK] 方塊中，選取代管這個虛擬映像的所在區域。
    -   在 [儲存體帳戶] 中，選取現有的儲存體帳戶或選取自動產生的儲存體帳戶。
    -   在 [**可用性設定組**] 方塊中，選取 [**(無)**]。
    -   閱讀並接受定價資訊。

6.	在 [**端點**] 區段中，按一下 [**名稱**] 底下的空白下拉式清單並選取 [**MSSQL**]，然後鍵入 Database Engine 執行個體的連接埠號碼 (預設執行個體的連接埠號碼是 **1433**)。

7.  您的 SQL Server VM 也可以用來做為 IPython Notebook 伺服器 (將在稍後步驟中設定)。新增端點以指定可供 IPython Notebook 伺服器使用的連接埠。在 [**名稱**] 資料行中輸入名稱、選取您為公用連接埠選擇的連接埠號碼，然後針對私用連接埠選取 9999。

	按一下右下角的 [下一步] 箭頭以繼續操作。

	![選取 MSSQL 和 IPython 連接埠][3]

8.  接受已選取的預設 [**安裝 VM 代理程式**] 選項，然後按一下精靈右下角的核取記號，完成 VM 佈建程序。

	![VM 的最後選項][4]

9.  等待 Azure 準備好您的虛擬機器。虛擬機器應該會依序經歷以下狀態變更：

    -   啟動中 (佈建中)
    -   已停止
    -   啟動中 (佈建中)
    -   執行中 (佈建中)
    -   執行中

##<a name="RemoteDesktop"></a>使用遠端桌面開啟虛擬機器並完成設定

1.  佈建完成時，請按一下虛擬機器的名稱以前往 [儀表板] 頁面。按一下頁面底部的 [**連接**]。

2.  選擇使用 Windows 遠端桌面程式 (`%windir%\system32\mstsc.exe`) 開啟 rpd 檔案。

3.  在 [**Windows 安全性**] 對話方塊中，提供在先前步驟中指定的本機系統管理員帳戶密碼。(系統可能會要求您確認虛擬機器的認證。)

4.  當您第一次登入這個虛擬機器時，可能需要完成數個程序，包括設定桌面、Windows Update，以及完成 Windows 初始組態工作 (sysprep)。待 Windows sysprep 完成後，SQL Server 安裝程式會完成組態工作。在完成這些工作的期間，可能會造成幾分鐘的時間延遲。可能要等到 SQL Server 設定完成，`SELECT @@SERVERNAME` 才會傳回正確的名稱，SQL Server Management Studio 才會出現在起始畫面中。

使用 Windows 遠端桌面連接到虛擬機器之後，虛擬機器的運作方式與任何其他電腦很像。請依照正常方法使用 SQL Server Management Studio (於虛擬機器上運作) 連接 SQL Server 的預設執行個體。

##<a name="InstallIPython"></a>安裝 IPython Notebook 和其他支援工具

若要設定新的 SQL Server VM 做為 IPython Notebook 伺服器，並安裝其他的支援工具 (例如，AzCopy、Azure 儲存體總管、實用的資料科學 Python 封裝，以及其他工具)，系統為您提供了一個特殊的自訂指令碼。若要安裝：

- 以滑鼠右鍵按一下 Windows [開始] 圖示，然後按一下 [**命令提示字元 (系統管理員)**]
- 複製下列命令並貼至命令提示字元。

    	set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/MachineSetup/Azure_VM_Setup_Windows.ps1'
    	@powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

- 出現提示時，輸入您為 IPython Notebook 伺服器選擇的密碼。
- 自訂指令碼會自動執行數個後續安裝程序，包括：
	+ 安裝和設定 IPython Notebook 伺服器
	+ 在稍早建立之端點的 Windows 防火牆中開啟 TCP 連接埠：
	+ 適用於 SQL Server 遠端連線
	+ 適用於 IPython Notebook 伺服器遠端連線
	+ 擷取 IPython Notebook 範例和 SQL 指令碼
	+ 下載並安裝實用的資料科學 Python 封裝
	+ 下載並安裝 Azure 工具，例如 AzCopy 和 Azure 儲存體總管<br>
- 您可以使用 `https://<virtual_machine_DNS_name>:<port>` 格式的 URL，從任何本機或遠端瀏覽器存取並執行 IPython Notebook，URL 格式中的 port 是您佈建虛擬機器時選取的 IPython 公用連接埠。
- IPython Notebook 伺服器正以背景服務形式執行，而且將在您重新啟動虛擬機器時自動重新啟動。

##<a name="Optional"></a>視需要連接資料磁碟

如果您的 VM 映像不包含資料磁碟，亦即，磁碟不是 C 磁碟機 (作業系統磁碟) 和 D 磁碟機 (暫存磁碟)，您就需要新增一或多個資料磁碟來儲存資料。SQL Server 2012 SP2 Enterprise Optimized for DataWarehousing Workloads 的 VM 映像是使用其他磁碟預先設定的，可供 SQL Server 資料和記錄檔使用。

 >[AZURE.NOTE]請勿使用 D 磁碟機來儲存資料。顧名思義，它只提供暫存儲存空間。它並不提供備援或備份，因為它不在 Azure 儲存體內。

若要連接其他資料磁碟，請依照「[如何將資料磁碟連接至 Windows 虛擬機器](storage-windows-attach-disk.md)」中所述的步驟執行，這將引導您完成：

1. 將空磁碟連接至先前步驟中佈建的虛擬機器
2. 在虛擬機器中將新磁碟初始化


##<a name="SSMS"></a>連接到 SQL Server Management Studio 並啟用混合模式驗證

SQL Server Database Engine 須有網域環境才能使用 Windows 驗證。若要從另一部電腦連接 Database Engine，請設定 SQL Server 以進行混合模式驗證。混合模式驗證可允許 SQL Server 驗證和 Windows 驗證。需要 SQL 驗證模式，才能在 [Azure Machine Learning Studio](https://studio.azureml.net) 中透過「讀取器」模組，從您的 SQL Server VM 資料庫直接擷取資料。

1.  使用遠端桌面連接到虛擬機器時，請在 Windows [**Search**] 窗格中鍵入 **SQL Server Management Studio** (SMSS)。按一下以啟動 SQL Server Management Studio (SSMS)。您可能想要將捷徑新增到桌面上的 SSMS，以供日後使用。

    ![啟動 SSMS][5]

    當您首次開啟 Management Studio 時，它必須建立使用者 Management Studio 環境。這可能需要花費幾分鐘的時間。

2.  當 Management Studio 開啟時，它會顯示 [**連接到伺服器**] 對話方塊。在 [**伺服器名稱**] 方塊中，輸入虛擬機器的名稱以利用物件總管連接 Database Engine。除了虛擬機器名稱之外，您還可以使用 [(本機)]，或將一個句點當做 [伺服器名稱]。選取 [Windows 驗證]，並保留 [使用者名稱] 方塊中的 [*your_VM_name*\your_local_administrator]。按一下 [連接]。

    ![連接到伺服器][6]

	<br>

	 >[AZURE.TIP]您可以使用 Windows 登錄機碼變更，或使用 SQL Server Management Studio，來變更 SQL Server 驗證模式。若要使用登錄機碼變更來變更驗證模式，請啟動 [**新增查詢**]，然後執行下列指令碼：

		USE master
    	go

    	EXEC xp_instance_regwrite N'HKEY_LOCAL_MACHINE', N'Software\Microsoft\MSSQLServer\MSSQLServer', N'LoginMode', REG_DWORD, 2
    	go


	若要使用 SQL Server management Studio 變更驗證模式：

3.  在 SQL Server Management Studio 物件總管中，以滑鼠右鍵按一下 SQL Server 執行個體的名稱 (虛擬機器名稱)，然後按一下 [**屬性**]。

    ![伺服器屬性][7]

4.  在 [安全性] 頁面的 [伺服器驗證] 下方，選取 [SQL Server 及 Windows 驗證模式]，然後按一下 [確定]。

    ![選取驗證模式][8]

5.  在 [SQL Server Management Studio] 對話方塊中，按一下 [確定] 以確認重新啟動 SQL Server 的需求。

6.  在物件總管中，以滑鼠右鍵按一下伺服器，然後按一下 [重新啟動]。(如果 SQL Server Agent 處於執行狀態，您也必須將其重新啟動。)

    ![重新啟動][9]

7.  在 [SQL Server Management Studio] 對話方塊中，按一下 [**是**] 以同意重新啟動 SQL Server。

##<a name="Logins"></a>建立 SQL Server 驗證登入

若要從另一部電腦連接 Database Engine，您至少必須建立一個 SQL Server 驗證登入。

> [AZURE.TIP]您也可以透過程式設計方式或使用 SQL Server Management Studio，來建立新的 SQL Server 登入。若要以程式設計的方式透過 SQL 驗證建立新的系統管理員使用者，請啟動下 [**新增查詢**]，然後執行下列指令碼。使用您選擇的使用者名稱和密碼取代 <new user name> 和 <new password>。視需要調整密碼原則 (程式碼範例會關閉原則檢查及密碼到期日)。如需 SQL Server 登入的詳細資訊，請參閱[建立登入](http://msdn.microsoft.com/library/aa337562.aspx)。

    USE master
    go

    CREATE LOGIN <new user name> WITH PASSWORD = N'<new password>',
    	CHECK_POLICY = OFF,
    	CHECK_EXPIRATION = OFF;

    EXEC sp_addsrvrolemember @loginame = N'<new user name>', @rolename = N'sysadmin';

若要使用 SQL Server Management Studio 建立新的 SQL Server 登入：

1.  在 SQL Server Management Studio 物件總管中，展開要建立新登入之伺服器執行個體的資料夾。

2.  以滑鼠右鍵按一下 [**安全性**] 資料夾，指向 [**新增**]，然後選取 [**登入...**]。

    ![新增登入][10]

3.  在 [登入 - 新增] 對話方塊的 [一般] 頁面中，於 [登入名稱] 方塊輸入新使用者的名稱。

4.  選取 [SQL Server 驗證]。

5.  在 [密碼] 方塊中，輸入新使用者的密碼。在 [確認密碼] 方塊中再次輸入密碼。

6.  若要強制執行複雜性和強制性密碼原則選項，請選取 [強制執行密碼原則] (建議)。此為選取 SQL Server 驗證時的預設選項。

7.  若要強制執行逾期密碼原則選項，請選取 [強制執行密碼逾期] (建議)。您必須選取強制執行密碼原則才能啟用此核取方塊。此為選取 SQL Server 驗證時的預設選項。

8.  若要強制使用者在首次登入後建立新密碼，請選取 [使用者必須在下次登入時變更密碼] (如果此登入是供其他使用者使用，建議您選取此選項。如果此登入是供您自己使用，請勿選取此選項。) 您必須選取強制執行密碼逾期才能啟用此核取方塊。此為選取 SQL Server 驗證時的預設選項。

9.  在 [預設資料庫] 清單中，選取登入的預設資料庫。**master** 是此選項的預設值。如果您尚未建立使用者資料庫，請保留 [master] 的設定。

10. 在 [預設語言] 清單中，保留 [default] 值。

    ![登入屬性][11]

11. 如果您正在建立第一個登入，也許會想要將此登入指定為 SQL Server 系統管理員。如果是的話，請在 [伺服器角色] 頁面中勾選 [系統管理員 (sysadmin)]。

    **安全性注意事項：**系統管理員 (sysadmin) 固定伺服器角色的成員擁有 Database Engine 的完整控制權限。因此請謹慎限制此角色的成員資格。

    ![系統管理員 (sysadmin)][12]

12. 按一下 [確定]。

##<a name="DNS"></a>決定虛擬機器的 DNS 名稱

若要從另一部電腦連接 SQL Server Database Engine，您必須知道虛擬機器的網域名稱系統 (DNS) 名稱。(這是網際網路用來識別虛擬機器的名稱。您可以使用 IP 位址，不過當 Azure 因備援或維護而移動資源時，IP 位址可能會改變。DNS 名稱是穩定的，因為它可以重新導向新的 IP 位址。)

1.  在 Azure 管理入口網站 (或前一個步驟) 中選取 [**虛擬機器**]。

2.  在「**虛擬機器執行個體**」頁面的 [**DNS 名稱**] 欄中，尋找並複製前方加上 **http://** 的虛擬機器 DNS 名稱。(使用者介面可能無法顯示完整名稱，不過您可以滑鼠右鍵按一下名稱，然後選取 [複製])。

##<a name="cde"></a>從另一台電腦連接到 Database Engine

1.  在連接網際網路的電腦上開啟 SQL Server Management Studio。

2.  在 [連接到伺服器] 或 [連接到 Database Engine] 對話方塊的 [伺服器名稱] 方塊中，輸入虛擬機器的 DNS 名稱 (於上一個工作中決定) 和 *DNSName,portnumber* 格式的公用端點連接埠名稱 (如 **tutorialtestVM.cloudapp.net,57500**)。

3.  在 [驗證] 方塊中，選取 [SQL Server 驗證]。

4.  在 [登入] 方塊中，輸入於先前工作中建立之登入的名稱。

5.  在 [密碼] 方塊中，輸入於先前工作中建立之登入的密碼。

6.  按一下 [**連接**]。

##<a name="amlconnect"></a>從 Azure Machine Learning 連接 Database Engine

在「進階分析程序和技術」的後期階段，您將使用 [Azure Machine Learning Studio](https://studio.azureml.net) 建置和部署機器學習服務模型。若要將從 SQL Server VM 資料庫擷取的資料直接匯入 Azure Machine Learning 供訓練或評分使用，請在新的 [Azure Machine Learning Studio](https://studio.azureml.net) 實驗中使用「讀取器」模組。《進階分析程序和技術》指南中的連結提供更多與本主題相關的詳細資訊。如需簡介，請參閱「[什麼是 Azure Machine Learning Studio？」](machine-learning-what-is-ml-studio.md)。

2.	在「[讀取器](https://msdn.microsoft.com/library/azure/dn905997.aspx)」模組的 [**屬性**] 窗格中，從 [**資料來源**] 下拉式清單中選取 [**Azure SQL Database**]。

3.	在 [**資料庫伺服器名稱**] 文字方塊中，輸入 `tcp:<DNS name of your virtual machine>,1433`

4.	在 [**伺服器使用者帳戶名稱**] 文字方塊中輸入 SQL 使用者名稱。

5.	在 [**伺服器使用者帳戶密碼**] 文字方塊中輸入 SQL 使用者的密碼。

	![Azure ML 讀取器][13]

##<a name="shutdown"></a>關閉並解除配置非使用中的虛擬機器

Azure 虛擬機器的定價策略是「**只針對您使用的項目進行付費**」。為了確保未使用虛擬機器時不會被計費，您必須將虛擬機器的狀態設為 [**已停止 (已解除配置)**]。

> [AZURE.NOTE]從 VM 內部關閉虛擬機器 (使用 Windows 電源選項) 時，雖然 VM 已停止，但仍然處於已配置狀態。為了確保您不會繼續被計費，請一律在「[Azure 管理入口網站](http://manage.windowsazure.com/)」中停止虛擬機器。您也可以藉由呼叫 ShutdownRoleOperation 搭配相當於 "StoppedDeallocated" 的 "PostShutdownAction"，透過 Powershell 來停止 VM。

關閉及取消配置虛擬機器：

1. 使用您的帳戶登入 [Azure 管理入口網站](http://manage.windowsazure.com/)。  

2. 從左側導覽列選取 [**虛擬機器**]。

3. 在虛擬機器清單中，按一下虛擬機器的名稱，然後移至 [**儀表板**] 頁面。

4. 按一下頁面底部的 [**關閉**]。

![VM 關閉][15]

虛擬機器將會取消配置，但不是刪除。您隨時都可從 Azure 管理入口網站重新啟動您的虛擬機器。

## 您的 Azure SQL Server VM 已準備好可供使用：下一步是什麼？

您的虛擬機器已經準備好在資料科學練習中使用。虛擬機器也已經準備好做為 IPython Notebook 伺服器使用，以供進行資料探索和處理，以及其他需透過 Azure Machine Learning 與「進階分析程序和技術」的完成工作使用。

「[學習指南：Azure 中的進階資料處理](machine-learning-data-science-advanced-data-processing.md)」中說明了資料科學程序的後續步驟，其中可能包含將資料移至 HDInsight，並在其中處理資料與取樣，做為透過 Azure Machine Learning 從資料學習的準備。


[1]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/selectsqlvmimg.png
[2]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/4vm-config.png
[3]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/sqlvmports.png
[4]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/vmpostopts.png
[5]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/searchssms.png
[6]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/19connect-to-server.png
[7]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/20server-properties.png
[8]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/21mixed-mode.png
[9]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/22restart2.png
[10]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/23new-login.png
[11]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/24test-login.png
[12]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/25sysadmin.png
[13]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/amlreader.png
[15]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/vmshutdown.png
 

<!---HONumber=62-->