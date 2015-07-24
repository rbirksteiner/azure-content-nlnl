<properties title="Creating an Oracle WebLogic Server 12c cluster in Azure" pageTitle="在 Azure 中建立 Oracle WebLogic Server 12c 叢集" description="逐步執行在 Microsoft Azure 中建立 Oracle WebLogic Server 12c 叢集的範例。" services="virtual-machines" authors="bbenz" documentationCenter=""/>
<tags ms.service="virtual-machines" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="infrastructure-services" ms.date="06/22/2015" ms.author="bbenz" />
#在 Azure 中建立 Oracle WebLogic Server 12c 叢集
以下範例會向您說明如何在 Azure 中以由 Microsoft 所提供並在 Windows Server 2012 上執行的 Oracle WebLogic Server 12c 映像為基礎，建立 Oracle WebLogic Server 叢集。

WebLogic Server 叢集中的每個執行個體都必須執行相同版本的 Oracle WebLogic Server。此範例使用 WebLogic Server 12c Enterprise Edition。

##建立虛擬機器來於叢集中使用
您將會建立虛擬機器來做為叢集管理伺服器使用，而且您將會建立額外的虛擬機器來做為叢集的一部份。

### 建立虛擬機器來做為管理伺服器使用

使用 Azure 中可用的 **Windows Server 2012 上的 Oracle WebLogic Server 12c Enterprise Edition** 建立虛擬機器。建立此虛擬機器的步驟可以在[在 Azure 中建立 Oracle WebLogic Server 12c 虛擬機器](#creating-an-oracle-weblogic-server-12c-virtual-machine-in-azure-new-article)中找到。基於本教學課程的目的，請將虛擬機器命名為 **MYVM1-ADMIN**。記下 VM 的虛擬網路名稱，您在建立其他 VM 以新增至叢集時將會用到該值。(VM 名稱與虛擬網路名稱可以是您喜好的任何名稱，只要它們在 Azure 中是唯一的名稱即可。)

### 建立受管理虛擬機器來於叢集中使用

使用 Azure 中可用的 Oracle WebLogic Server 12c 映像，建立將由管理伺服器管理的其他虛擬機器。基於本教學課程的目的，請將其他虛擬機器命名為 **MYVM2-MANAGED** 和 **MYVM3-MANAGED**。建立這些虛擬機器的步驟可以在[在 Azure 中建立 Oracle WebLogic Server 12c 虛擬機器](#creating-an-oracle-weblogic-server-12c-virtual-machine-in-azure-new-article)中找到，*但是*需要執行以下變更：

-  建立虛擬機器時，請勿建立新的虛擬網路。特別是，在 [選用設定] > [虛擬網路] 對話方塊中，改為選取為您的管理伺服器 VM 建立的虛擬網路，而不要使用預設的 [建議新的虛擬網路]。例如，如果您在建立管理伺服器時建立名為 **EXAMPLE** 的虛擬網路，請在您建立受管理叢集 VM 時選取 **EXAMPLE**。

##建立網域

1. 登入 [Azure 入口網站](https://ms.portal.azure.com/)。

2. 按一下 [虛擬機器]。

3. 按一下您建立做為叢集管理伺服器之虛擬機器的名稱 (例如，**MYVM1-ADMIN**)。

4. 按一下 [連接]。

5. 視需要回應提示以連接虛擬機器。當要求提供系統管理員名稱和密碼的提示出現時，請使用在建立虛擬機器時提供的值。

6. 在 [Fusion Middleware 設定精靈] 對話方塊的 [第 1 頁]，按一下 [建立新網域]，然後按一下 [下一步]。(如果尚未開啟 [Fusion Middleware 設定精靈] 對話方塊，請按一下 [Windows 開始]，輸入**設定精靈**，然後按一下**設定精靈**圖示。)

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image19.png)

7. 在 [Fusion Middleware 設定精靈] 對話方塊的 [第 2 頁]，選取 [基本 WebLogic 伺服器網域] 範本，然後按一下 [下一步]。

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image20.png)

8. 在 [Fusion Middleware 設定精靈] 對話方塊的 [第 3 頁]：

	1. [選用] 將使用者名稱從 **weblogic** 變更為您選擇的值。
	
	2. 指定並確認 WebLogic Server 系統管理員密碼。
	
	3. 按 [下一步]。

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image21.png)

9. 在 [Fusion Middleware 設定精靈] 對話方塊的 [第 4 頁]，選取 [生產] 網域模式，選取可用的 JDK (或瀏覽至想要的 JDK)，然後選取 [下一步]。

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image22.png)

10.  在 [Fusion Middleware 設定精靈] 對話方塊的 [第 5 頁] 中不要選取任何選項，然後按一下 [下一步]。

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image23.png)

11.  在 [Fusion Middleware 設定精靈] 對話方塊的 [第 6 頁]，按一下 [建立]：

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image24.png)

12.  在建立網域之後，於 [Fusion Middleware 設定精靈] 對話方塊的 [第 7 頁]，按一下 [下一步]。

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image25.png)

13.  在 [Fusion Middleware 設定精靈] 對話方塊的 [第 8 頁]，檢查啟動管理伺服器，然後按一下 [完成]。

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image26.png)

14.  就會啟動 **startWebLogic.cmd** 的命令提示字元。出現提示時，請提供您的 WebLogic 使用者名稱與密碼。

##設定叢集

1. 在已登入管理虛擬機器的狀態下，執行 [WebLogic Server 管理主控台] (<http://localhost:7001/console>)。出現提示時，請提供您的 WebLogic Server 使用者名稱與密碼。

2. 在 [WebLogic Server 管理主控台] 中，按一下 [鎖定和編輯]。

3. 在 [網域結構] 窗格中，展開 [環境]，然後按一下 [叢集]。

4. 在 [叢集摘要] 對話方塊中，按一下 [新增]，然後按一下 [叢集]。

5. 在 [叢集屬性] 對話方塊中：

	1. 輸入叢集的名稱。

	2. 為 [傳訊模式] 選取 [Unicast]。

		![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image001.png)

	
	3. 按一下 [確定]。

6. 在 [網域結構] 窗格中，展開 [環境]，然後按一下 [伺服器]。

7. 新增您的第一個受管理伺服器到叢集。

	1. 按一下 [新增]。

	2. 在 [建立新伺服器] 對話方塊中：

		1. 對於 [伺服器名稱]，請輸入您第一個受管理伺服器的名稱。例如，MYVM2-MANAGED。

		2. 對於 [伺服器接聽位址]，請再輸入一次名稱。

		3. 對於 [接聽連接埠]，請輸入 **7008**。

		4. 勾選 [是，讓此伺服器成為現有叢集的成員]。

		5. 在 [選取叢集] 下拉式清單中，選取您之前建立的叢集。

			34d27e82-bb2e-4f9c-aaad-ca3e28c0f5fc

		6. 按 [下一步]。

		7. 按一下 [完成]。

8. 使用上述步驟新增您的第二個受管理伺服器到叢集。對於 [伺服器名稱] 和 [伺服器接聽位址]，請使用第二部受管理電腦的名稱。對於 [接聽連接埠]，請使用 **7008.**。

9. 繼續在 WebLogic Server 管理主控台中，按一下 [啟用變更]。

10. 在管理虛擬機器上，建立名為 **SERVER_HOME** 的環境變數，並將其值設為 **C:\\Oracle\\Middleware\\Oracle_Home\\wlserver**。 您可以使用下列步驟建立環境變數：

	1. 按一下 [Windows 開始]，輸入**控制台**，按一下 [控制台] 圖示，然後依序按一下 [系統及安全性]、[系統] 及 [進階系統設定]。

	2. 按一下 [進階] 索引標籤，然後按一下 [環境變數]。

	3. 在 [系統變數] 區段底下，按一下 [新增] 來建立變數。

	4. 在 [新增系統變數] 對話方塊中，輸入 **SERVER_HOME** 做為變數名稱，然後輸入 **C:\\Oracle\\Middleware\\Oracle_Home\\wlserver** 做為變數。

	5. 按一下 [確定] 來儲存新的環境變數並關閉 [新增系統變數] 對話方塊。

	6. 關閉由控制台開啟的其他對話方塊。

11. 開啟新的命令提示字元 (**SERVER_HOME** 環境變數才會生效)。

	>[AZURE.NOTE]在您登入您的虛擬機器之後，部分其餘步驟需使用命令提示字元。若要了解您是登入哪一部機器，請在您開啟命令提示字元之後，執行 **title %COMPUTERNAME%**。
	>
	>**( %COMPUTERNAME%** 是一個自動對電腦名稱設定的系統定義的環境變數。執行 **title** **%COMPUTERNAME%** 命令將會使命令提示字元標題列顯示電腦名稱。)

12. 執行以下命令：

		%SERVER_HOME%\\common\\bin\\pack.cmd -managed=true -domain=C:\\Oracle\\Middleware\\Oracle_Home\\user_projects\\domains\\base_domain -template=c:\\mytestdomain.jar -template_name="mytestdomain" 

	此命令會建立名為 **c:\\mytestdomain.jar** 的 JAR。 您稍後將會把這個 JAR 複製到您叢集中的受管理虛擬機器。

13. 允許透過防火牆輸入連線至連接埠 7001。

	1. 在已登入虛擬機器的情況下，按一下 [Windows 開始]，輸入**具有進階安全性的 Windows 防火牆**，然後按一下 [具有進階安全性的 Windows 防火牆] 圖示。這會開啟 [具有進階安全性的 Windows 防火牆] 管理主控台。

	2. 在防火牆管理主控台中，按一下左邊窗格內的 [輸入規則] (如果您沒有看到 [輸入規則]，請展開左邊窗格內的最上層節點)，然後按一下右邊窗格內的 [新增規則]。

	3. 對於 [規則類型]，請選取 [連接埠]，然後按一下 [下一步]。

	4. 對於 [通訊協定與連接埠]，請選取 [TCP]，選取 [特定本機連接埠]，輸入 **7001** 做為連接埠，然後按一下 [下一步]。

	5. 選取 [允許連線] 並按一下 [下一步]。

	6. 接受套用規則之設定檔的預設值，然後按一下 [下一步]。

	7. 指定規則的名稱並選擇性指定描述，然後按一下 [完成]。

14. 針對每部受管理虛擬機器：

	1. 登入虛擬機器。

	2. 建立名為 **SERVER_HOME** 的環境變數，並將其值設為 **C:\\Oracle\\Middleware\\Oracle_Home\\wlserver**。

	3. 將 c:\\mytestdomain.jar 從管理虛擬機器複製到受管理虛擬機器上的 c:\\mytestdomain.jar。

	4. 開啟命令提示字元 (並請記得在命令提示字元執行 **title %COMPUTERNAME%**，以讓它清除正在存取的電腦)。

	5. 執行以下命令：

			%SERVER_HOME%\\common\\bin\\unpack.cmd -domain=C:\\Oracle\\Middleware\\Oracle_Home\\user_projects\\domains\\base_domain -template=c:\\mytestdomain.jar

	6. 將命令提示字元目前目錄變更至 **C:\\Oracle\\Middleware\\Oracle_Home\\user_projects\\domains\\base_domain\\bin**。

	7. 執行 start<<*MACHINENAME*>>.cmd，其中 <<*MACHINENAME*>> 為受管理電腦的名稱。例如，**startMYVM2-MANAGED**。

	8. 出現提示時，請提供 WebLogic Server 使用者名稱與密碼。

	9. 允許透過防火牆輸入連線至連接埠 7008。(依照用來在管理伺服器上開啟連接埠 7001 的步驟執行，但針對受管理伺服器改為使用 7008。)

15. 在管理虛擬機器上，開啟 [WebLogic Server 管理主控台] (<http://localhost:7001/console>)，並查看正在執行的伺服器。

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image003.png)

16. 為您的受管理虛擬機器建立負載平衡端點集：

	1. 在 [Azure 入口網站](https://ms.portal.azure.com/) 的 [虛擬機器]**** 區段中，選取第一部受管理虛擬機器 (例如 **MYVM2-MANAGED)**。
	2. 依序按一下 [設定]、[端點] 及 [新增]。

	3. 指定端點名稱、指定 **TCP** 做為通訊協定、指定公用連接埠 **80** 與私人連接埠 **7008**。 維持其餘選項不變。

	4. 勾選 [建立負載平衡集]，然後按一下 [完成]。

	5. 指定負載平衡集的名稱，接受其他參數的預設值，然後按一下 [完成]。

17. 為虛擬機器建立端點：

	1. 登入 [Azure 入口網站](https://ms.portal.azure.com/)。

	2. 按一下 [瀏覽]

	3. 按一下 [虛擬機器]

	4. 選取虛擬機器

	5. 按一下 [設定]

	6. 按一下 [負載平衡集]。

	7. 按一下 [加入]。

	8. 將「負載平衡集」類型設為 [內部]

	9. 指定端點的名稱

		1. 使用 [TCP] 做為通訊協定

		2. 使用 [80] 做為公用連接埠

		3. 使用 [7008] 做為探查連接埠。

	10. 維持其餘選項不變

	11. 按一下 [確定]。

	12. 請先等待此虛擬機器加入負載平衡集，然後再繼續下一個步驟。

18. 在 [Azure 入口網站](https://ms.portal.azure.com/) 的 [虛擬機器] 區段中，選取第二部受管理虛擬機器 (例如 **MYVM3-MANAGED**)。依照上面的步驟加入您為第一部受管理虛擬機器建立的負載平衡集。

##將應用程式部署至叢集

此時，您可以使用下列步驟部署您的應用程式。假設您是在部署 Oracle 購物車應用程式 (可在 <http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war> 下載)。

1. 請都入您用於管理 WebLogic Server 叢集的虛擬機器 (例如 **MYVM1-ADMIN**)。 

2. 將 shoppingcart.war 複製到本機。例如，建立名為 **c:\\mywar** 的資料夾，然後將位於 <http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war> 的 WAR 儲存至 **c:\\mywar**。

3. 開啟 [WebLogic Server 管理主控台] (<http://localhost:7001/console>)。出現提示時，請提供您的 WebLogic 使用者名稱與密碼。

4. 在 [WebLogic Server 管理主控台] 中，依序按一下 [鎖定與編輯]、[部署] 及 [安裝]。

5. 對於 [路徑]，請輸入 **c:\\myway\\shoppingcart.war**。

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image004.png)

	按 [下一步]。

6. 選取 [將此部署安裝為應用程式]，然後按一下 [下一步]。

7. 按一下 [完成]。

8. 對於 [可用目標]，請選取您之前建立的叢集，並確定已選取 [叢集中的所有伺服器]，然後按一下 [下一步]。

9. 在 [來源協助工具] 底下，選取 [將此應用程式複製到我的每個目標]，然後按一下 [完成]。

10.  在 [WebLogic Server 管理主控台] 中，按一下 [儲存]，然後按一下 [啟用變更]。

11.  按一下 [部署]，選取 [購物車]，然後依序按一下 [開始] 和 [服務所有要求]。在系統提示您確認時，按一下 [是]。

12.  若要查看在網際網路上執行的購物車應用程式，請開啟瀏覽器並連線至格式為 `http://<<unique_domain_name>>/shoppingcart` 的 URL。(您可以按一下 [虛擬機器]，然後選取您用來執行 Orable WebLogic Server 的虛擬機器來決定 [Azure 入口網站](https://ms.portal.azure.com/) 中 `<<unique_domain_name>>` 的值)。

## 後續步驟

若要進一步查看您的叢集是否如預期般運作，您可以修改 shoppingcart.war 專案來顯示服務瀏覽器工作階段的機器名稱、啟動瀏覽器工作階段、停止服務瀏覽器工作階段的機器，以及重新整理瀏覽器工作階段以查看是否有不同的機器繼續服務瀏覽器工作階段。

例如：

1. 修改 **DWRHeader1.jspf** 檔案以在檔案頂端包含以下程式碼：

		<table>
		
		<tr><td><CENTER><b><h3><% out.println("Your request is served from " + System.getenv("computername") ); %></h3></b></CENTER></td></tr>
		
		</table>             

2. 修改 **weblogic.xml** 檔案以在註解行 `Insert session descriptor element here` 之後包含以下程式碼：

		<session-descriptor>
			<persistent-store-type>replicated_if_clustered</persistent-store-type>
		</session-descriptor>


3. 重新編譯及重新部署已更新的 shoppingcart.war。

4. 開啟瀏覽器工作階段並執行購物車應用程式。新增一些項目到購物車，並觀察是哪一部機器服務瀏覽器工作階段。

5. 在 Azure 入口網站的 [虛擬機器] 使用者介面中，選取服務瀏覽器工作階段的 VM 並按一下 [關閉]。請先等待 VM 狀態**停止 (已取消配置)**，然後再繼續。

6. 重新整理執行購物車應用程式的瀏覽器工作階段，然後查看是否有不同的機器服務瀏覽器工作階段。

7. 按一下購物車連結，然後查看之前新增的項目是否仍在購物車中。

## 其他資源

現在您已經設定好執行 Oracle WebLogic Server 的叢集了，請參閱下列主題了解其他資訊。

- [Oracle 虛擬機器映像 - 其他考量](virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images.md)

- [Oracle WebLogic Server 產品文件](http://www.oracle.com/technetwork/middleware/weblogic/documentation/index.html)

- [Microsoft Azure 上使用 Linux 的 Oracle WebLogic Server 12c](http://www.oracle.com/technetwork/middleware/weblogic/learnmore/oracle-weblogic-on-azure-wp-2020930.pdf)

<!---HONumber=July15_HO2-->