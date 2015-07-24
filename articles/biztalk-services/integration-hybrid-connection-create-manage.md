<properties 
	pageTitle="建立和管理混合式連線 | Azure" 
	description="了解如何建立混合式連線、管理連線和安裝 Hybrid Connection Manager。MABS，WABS" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/08/2015" 
	ms.author="mandia"/>


# 建立和管理混合式連線


## 步驟概觀
1. 輸入私人網路中內部部署資源的主機名稱或 IP 位址，建立「混合式連線」。
2. 將 Azure Web Apps 或 Azure Mobile Apps 連結至混合式連線。
3. 安裝混合式連線管理員在內部部署資源上，並連接至特定的混合式連線。Azure 入口網站提供按一下即可安裝和連接的體驗。
4. 管理混合式連線及其連接金鑰。

本主題列出這些步驟。


## <a name="CreateHybridConnection"></a>建立混合式連線

使用 Web Apps **或** BizTalk 服務可以在 Azure 管理入口網站中建立「混合式連線」。

**若要使用 Web Apps 建立「混合式連線」**，請參閱「[將 Azure Web Apps 連接到內部部署資源](../web-sites-hybrid-connection-get-started.md)」(英文)。

**若要在 BizTalk 服務中建立「混合式連線」**：

1. 登入 [Azure 管理入口網站](http://go.microsoft.com/fwlink/p/?LinkID=213885)。
2. 在左側瀏覽窗格中，選取 [**BizTalk 服務**]，然後選取您的 BizTalk 服務。<br/>如果您沒有現有的 BizTalk 服務，您可以 [建立 BizTalk 服務](biztalk-provision-services.md)。
3. 選取 [混合式連線] 索引標籤：<br/> ![混合式連線索引標籤][HybridConnectionTab]

4. 選取 [**建立混合式連接**]，或在工作列中選取 [**新增**] 按鈕。輸入以下資訊：

	屬性 | 說明
--- | ---
名稱 | 混合式連線名稱必須是唯一的，且不能與 BizTalk 服務的名稱相同。您可以輸入任何名稱，但要能夠具體表示用途。範例包括：<br/><br/>Payroll*SQLServer*<br/>SupplyList*SharepointServer*<br/>Customers*OracleServer*
主機名稱 | 輸入內部部署資源的完整主機名稱、僅主機名稱，或 IPv4 位址。範例包括：<br/><br/>mySQLServer<br/>*mySQLServer*.*Domain*.corp.*yourCompany*.com<br/>*myHTTPSharePointServer*<br/>*myHTTPSharePointServer*.*yourCompany*.com<br/>10.100.10.10
連接埠 | 輸入內部部署資源的連接埠號碼。例如，如果您使用 Web Apps，請輸入連接埠 80 或 443。如果您使用 SQL Server，請輸入連接埠 1433。

5. 選取核取記號以完成設定。

#### 其他

- 可建立多個混合式連線。請參閱「[BizTalk 服務：版本圖表](biztalk-editions-feature-chart.md)」(英文) 瞭解允許的連接數量。 
- 每個「混合式連線」都是由一對連接字串建立而成：分別是負責「傳送」的應用程式金鑰，以及負責「接聽」的內部部署金鑰。每一對都有「主要」和「次要」金鑰。 


## <a name="LinkWebSite"></a>連結 Azure Web Apps 或 Azure Mobile Apps

若要將 Azure Web Apps 連結至現有的「混合式連線」，請在 [混合式連線] 刀鋒視窗中選取 [**使用現有的混合式連線**]。請參閱「[將 Azure Web Apps 連接到內部部署資源](../web-sites-hybrid-connection-get-started.md)」(英文)。

若要將 Azure Mobile Apps 連結至現有的「混合式連線」，請在變更或建立「行動服務」時選取 [**新增混合式連線**]。請參閱 [Azure 行動服務和混合式連線](../mobile-services-dotnet-backend-hybrid-connections-get-started.md) (英文)。


## <a name="InstallHCM"></a>在內部部署安裝混合式連線管理員

建立混合式連線之後，請在內部部署資源上安裝混合式連線管理員。您可從 Azure Web Apps 或 BizTalk 服務下載此程式。BizTalk 服務步驟：

1. 登入 [Azure 管理入口網站](http://go.microsoft.com/fwlink/p/?LinkID=213885)。
2. 在左側瀏覽窗格中，選取 [**BizTalk 服務**]，然後選取您的 BizTalk 服務。 
3. 選取 [**混合式連線**] 索引標籤：<br/> ![混合式連線索引標籤][HybridConnectionTab]
4. 在工作列中，選取 [**內部部署設定**]：<br/> ![On-Premises Setup][HCOnPremSetup]
5. 選取 [**安裝和設定**]，在內部部署系統上執行或下載混合式連線管理員。 
6. 選取核取記號來開始安裝。 

<!--
You can also download the Hybrid Connection Manager MSI file and copy the file to your on-premises resource. Specific steps:

1. Copy the on-premises primary Connection String. See [Manage Hybrid Connections](#ManageHybridConnection) in this topic for the specific steps.
2. Download the Hybrid Connection Manager MSI file. 
3. On the on-premises resource, install the Hybrid Connection Manager from the MSI file. 
4. Using Windows PowerShell, type: 
> Add-HybridConnection -ConnectionString “*Your On-Premises Connection String that you copied*” 
-->

#### 其他
- 混合式連線支援下列作業系統上安裝的內部部署資源：

	- Windows Server 2008 R2
	- Windows Server 2012
	- Windows Server 2012 R2


- 安裝混合式連線管理員之後的情況如下：

	- 裝載於 Azure 的混合式連線會自動設定為使用「主要應用程式連接字串」。 
	- 內部部署資源會自動設定為使用「主要內部部署連接字串」。

- 混合式連線管理員必須使用有效的內部部署連接字串，才能進行授權。Azure Web Apps 或 Azure Mobile Apps 必須使用有效的應用程式連接字串，才能進行授權。


## <a name="ManageHybridConnection"></a>管理混合式連線
若要管理混合式連線，您可以：

- 使用 Azure 入口網站並移至您的 BizTalk 服務。 
- 使用 [REST API](http://msdn.microsoft.com/library/azure/dn232347.aspx)。

#### 複製/重新產生混合式連線字串

1. 登入 [Azure 管理入口網站](http://go.microsoft.com/fwlink/p/?LinkID=213885)。
2. 在左側瀏覽窗格中，選取 [**BizTalk 服務**]，然後選取您的 BizTalk 服務。 
3. 選取 [**混合式連線**] 索引標籤：<br/> ![混合式連線索引標籤][HybridConnectionTab]
4. 選取 [混合式連線]。在工作列中，選取 [**管理連線**]：[<br/> ![管理選項][HCManageConnection] <br/> **管理連線**] 會列出「應用程式」和「內部部署」連接字串。您可以複製連接字串，或重新產生連接字串所使用的存取金鑰。<br/> <br/> **如果您選取 [重新產生]**，連接字串所使用的共用存取金鑰便會變更。執行下列動作：
- 在 Azure 管理入口網站中，選取 Azure 應用程式中的 [**同步金鑰**]。
- 重新執行 [**內部部署設定**]。重新執行內部部署設定時，內部部署資源會自動設定為使用已更新的主要連接字串。


#### 使用群組原則來控制混合式連線使用的內部部署資源

1. 下載[混合式連線管理員的系統管理範本](http://www.microsoft.com/download/details.aspx?id=42963)。
2. 將檔案解壓縮。
3. 在修改群組原則的電腦上，執行下列動作： 

	- 將 .ADMX 檔案複製到 *%WINROOT%\PolicyDefinitions* 資料夾。
	- 將 .ADML 檔案複製到 *%WINROOT%\PolicyDefinitions\zh-tw* 資料夾。

複製之後，您可以使用群組原則編輯器來變更原則。




## 下一步

[將 Azure Web Apps 連接到內部部署資源](../web-sites-hybrid-connection-get-started.md) (英文)<br/> [從 Azure Web Apps 連接到內部部署 SQL Server](../web-sites-hybrid-connection-connect-on-premises-sql-server.md) (英文)<br/> [Azure 行動服務和混合式連線](../mobile-services-dotnet-backend-hybrid-connections-get-started.md) (英文)<br/> [混合式連線概觀](integration-hybrid-connection-overview.md)


## 另請參閱

[用於管理 Microsoft Azure 上之 BizTalk 服務的 REST API](http://msdn.microsoft.com/library/azure/dn232347.aspx)<br/> [BizTalk 服務：版本圖表](biztalk-editions-feature-chart.md)<br/> (英文) [使用 Azure 管理入口網站建立 BizTalk 服務](biztalk-provision-services.md) (英文)<br/> [BizTalk 服務：儀表板、監視和調整索引標籤](biztalk-dashboard-monitor-scale-tabs.md) (英文)<br/>


[HybridConnectionTab]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionManageConn.png

<!---HONumber=62-->