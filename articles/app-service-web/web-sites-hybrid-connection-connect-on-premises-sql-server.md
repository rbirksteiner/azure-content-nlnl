<properties 
	pageTitle="使用混合式連線從 Azure App Service 內的 Web 應用程式連線至內部部署 SQL Server" 
	description="在 Microsoft Azure 上建立 Web 應用程式，並將它連接到內部部署 SQL Server 資料庫"
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/02/2015" 
	ms.author="cephalin"/>

# 使用混合式連線從 Azure App Service 內的 Web 應用程式連線至內部部署 SQL Server

「混合式連線」可將 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps 連接到使用靜態 TCP 連接埠的內部部署資源。支援的資源包括 Microsoft SQL Server、MySQL、HTTP Web API、行動服務和大部分的自訂 Web 服務。

在本教學課程中，您將了解如何在 [Azure 預覽](http://go.microsoft.com/fwlink/?LinkId=529715)中建立 App Service Web 應用程式、使用新的「混合式連線」功能將 Web 應用程式連接到您的本機內部部署 SQL Server 資料庫、建立將使用混合式連線的簡易 ASP.NET 應用程式，以及將應用程式部署至 App Service Web 應用程式。Azure 上已完成的 Web 應用程式會將使用者認證儲存在內部部署的成員資格資料庫中。本教學課程假設您沒有使用 Azure 或 ASP.NET 的經驗。

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期入門 Web 應用程式。不需要信用卡；沒有承諾。
>
>「混合式連線」功能的 Web Apps 部分僅適用於 [Azure 預覽入口網站](https://portal.azure.com)。若要在 BizTalk 服務中建立連線，請參閱[混合式連線](http://go.microsoft.com/fwlink/p/?LinkID=397274)。

## 必要條件 ##

若要完成本教學課程，您將需要下列產品。所有產品都有免費版本可使用，因此您可以免費進行 Azure 相關開發。

- **Azure 訂閱** - 如需免費訂閱，請參閱 [Azure 免費試用](/pricing/free-trial/)。 

- **Visual Studio 2013** - 若要下載 Visual Studio 2013 的免費試用版，請參閱 [Visual Studio 下載](http://www.visualstudio.com/downloads/download-visual-studio-vs)。請先安裝此項目再繼續作業。

- **Microsoft .NET Framework 3.5 Service Pack 1** - 如果您的作業系統是 Windows 8.1、Windows Server 2012 R2、Windows 8、Windows Server 2012、Windows 7 或 Windows Server 2008 R2，您可以在 [控制台] > [程式和功能] > [開啟或關閉 Windows 功能] 中啟用此項目。否則，您可以從 [Microsoft 下載中心](http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=22)下載。

- **SQL Server 2014 Express with Tools** - 請在 [Microsoft Web Platform Database 頁面](http://www.microsoft.com/web/platform/database.aspx)下載免費的 Microsoft SQL Server Express。選擇 [Express] (非 LocalDB) 版本。[Express with Tools] 版本包含您將在此教學課程中使用的 SQL Server Management Studio。

- **SQL Server Management Studio Express** - 此項目隨附於前述的 SQL Server 2014 Express with Tools 下載中，但您必須個別加以安裝，您可以從 [SQL Server Express 下載頁面](http://www.microsoft.com/web/platform/database.aspx)加以下載並安裝。

本教學課程假設您具有 Azure 訂閱、您已安裝 Visual Studio 2013，並且已安裝或啟用 .NET Framework 3.5。本教學課程將說明如何在可與 Azure 混合式連線功能妥善搭配運作的組態中安裝 SQL Server 2014 Express (使用靜態 TCP 連接埠的預設執行個體)。在開始本教學課程之前，如果您尚未安裝 SQL Server，請先從前述位置下載 SQL Server 2014 Express with Tools。

### 注意事項 ###
若要透過混合式連線使用內部部署 SQL Server 或 SQL Server Express 資料庫，必須在靜態連接埠上啟用 TCP/IP。SQL Server 上的預設執行個體會使用靜態連接埠 1433，但指定的執行個體則否。

安裝內部部署混合式連線管理員代理程式的電腦：

- 必須有透過下列連接埠的 Azure 輸出連線：

> <table border="1">
    <tr>
       <th><strong>連接埠</strong></th>
        <th>理由</th>
    </tr>
    <tr>
        <td>80</td>
        <td><strong>必要</strong>可供 HTTP 連接埠進行憑證驗證以及可供進行資料連線 (選用)。</td>
    </tr>
    <tr>
        <td>443</td>
        <td><strong>選用</strong>可供進行資料連線。如果無法使用 443 的輸出連線，則使用 TCP 連接埠 80。</td>
    </tr>
	<tr>
        <td>5671 和 9352</td>
        <td><strong>建議</strong>但可供進行資料連線 (選用)。請注意，此模式通常會產生較高的輸送量。如果無法使用這些連接埠的輸出連線，則使用 TCP 連接埠 443。</td>
	</tr>
</table>

- 必須能夠連繫內部部署資源的 *hostname*:*portnumber*。 

本文中的步驟假設您使用將主控內部部署混合式連線代理程式之電腦中的瀏覽器。

如果您已在組態和符合前述條件的環境中安裝 SQL Server，您可以直接開始[在內部部署中建立 SQL Server 資料庫](#CreateSQLDB)。

<a name="InstallSQL"></a>
## 答：在內部部署中安裝 SQL Server Express、啟用 TCP/IP 及建立 SQL Server 資料庫 ##

本節說明如何安裝 SQL Server Express、啟用 TCP/IP 及建立資料庫，讓您的 Web 應用程式可在 Azure 預覽環境中運作。

### 安裝 SQL Server Express ###

1. 若要安裝 SQL Server Express，請執行您已下載的 **SQLEXPRWT_x64_ENU.exe** 或 **SQLEXPR_x86_ENU.exe** 檔案。[SQL Server 安裝中心] 精靈會隨即出現。
	
	![SQL Server Install][SQLServerInstall]
	
2. 選擇 [新的 SQL Server 獨立安裝或將功能加入到現有安裝]。遵循指示接受預設選項和設定，直到您進入 [執行個體組態] 頁面。
	
3. 在 [執行個體組態] 頁面上，選擇 [預設執行個體]。
	
	![Choose Default Instance][ChooseDefaultInstance]
	
	根據預設，SQL Server 的預設執行個體會在靜態連接埠 1433 上接聽來自 SQL Server 用戶端的要求，而這正是混合式連線功能的需求。指定的執行個體會使用動態連接埠和 UDP，而混合式連線並不加以支援。
	
4. 接受 [伺服器組態] 頁面上的預設值。
	
5. 在 [資料庫引擎組態] 頁面上的 [驗證模式] 下，選擇 [混合模式 (SQL Server 驗證和 Windows 驗證)]，並提供密碼。
	
	![Choose Mixed Mode][ChooseMixedMode]
	
	在本教學課程中，您將使用 SQL Server 驗證。請務必記住您提供的密碼，因為後續將會用到。
	
6. 逐步完成精靈的其餘步驟，以完成安裝。

### 啟用 TCP/IP ###
若要啟用 TCP/IP，您必須使用您在安裝 SQL Server Express 時所安裝的 SQL Server 組態管理員。請先執行[為 SQL Server 啟用 TCP/IP 網路通訊協定](http://technet.microsoft.com/library/hh231672%28v=sql.110%29.aspx)中的步驟，再繼續作業。

<a name="CreateSQLDB"></a>
### 在內部部署中建立 SQL Server 資料庫 ###

您的 Visual Studio Web 應用程式需要可由 Azure 存取的成員資格資料庫。這必須要有 SQL Server 或 SQL Server Express 資料庫 (不是 MVC 範本依預設使用的 LocalDB 資料庫)，因此您接下來將會建立成員資格資料庫。

1. 在 SQL Server Management Studio 中，連接到您剛剛安裝的 SQL Server。(如果 [連線到伺服器] 對話方塊未自動出現，請導覽至左窗格中的 [物件總管]，依序按一下 [連接] 和 [資料庫引擎]。)![連線到伺服器][SSMSConnectToServer]
	
	針對 [伺服器類型]，選擇 [資料庫引擎]。對於 [伺服器名稱]，您可以使用 **localhost** 或您要使用之電腦的名稱。選擇 [SQL Server 驗證]，然後以您先前建立的 sa 使用者名稱和密碼登入。
	
2. 若要使用 SQL Server Management Studio 建立新資料庫，請在 [物件總管] 中以滑鼠右鍵按一下 [資料庫]，然後按一下 [New Database]。
	
	![Create new database][SSMScreateNewDB]
	
3. 在 [New Database] 對話方塊中，輸入 MembershipDB 做為資料庫名稱，然後按一下 [確定]。
	
	![Provide database name][SSMSprovideDBname]
	
	請注意，到目前為止您並未對資料庫做任何變更。成員資格資訊後續會在您執行 Web 應用程式時由該應用程式自動新增。
	
4. 在 [物件總管] 中，如果您展開 [資料庫]，您會發現成員資格資料庫已建立。
	
	![MembershipDB created][SSMSMembershipDBCreated]
	
<a name="CreateSite"></a>
## B.在 Azure 預覽入口網站中建立 Web 應用程式 ##

> [AZURE.NOTE]如果您已在 Azure 預覽入口網站中建立要用於此教學課程的 Web 應用程式，您可以直接跳到[建立混合式連線和 BizTalk 服務](#CreateHC)繼續作業。

1. 在 [Azure 預覽入口網站][](https://portal.azure.com) 中，按一下 [新增] > [Web + 行動] > [Web 應用程式]。
	
	![New button][New]
	
2. 設定您的 Web 應用程式，然後按一下 [建立]。
	
	![Website name][WebsiteCreationBlade]
	
3. 經過一段時間之後，Web 應用程式會建立，並顯示它的 Web 應用程式分頁。此分頁是垂直捲動的儀表板，可供您管理 Web 應用程式。
	
	![Website running][WebSiteRunningBlade]
	
	若要確認 Web 應用程式是否已上線啟用，您可以按一下 [瀏覽] 圖示以顯示預設頁面。
	
接著，您將為 Web 應用程式建立混合式連線和 BizTalk 服務。

<a name="CreateHC"></a>
## C.建立混合式連線和 BizTalk 服務 ##

1. 返回入口網站，向下捲動 Web 應用程式的分頁，然後按一下 [混合式連線]。
	
	![Hybrid connections][CreateHCHCIcon]
	
2. 在 [混合式連線] 分頁上，按一下 [新增] > [建立混合式連線]。
	
3. 在 [建立混合式連線分頁] 上：
	- 在 [名稱] 中，提供連線的名稱。
	- 針對 [主機名稱]，輸入您的 SQL Server 主機電腦的電腦名稱。
	- 針對 [連接埠]，輸入 1433 (SQL Server 的預設連接埠)。
	- 按一下 [Biz Talk 服務] 並輸入 BizTalk 服務的名稱。
	
	![Create a hybrid connection][TwinCreateHCBlades]
		
5. 按兩次 [確定]。

	程序完成時，[通知] 區域會閃爍綠色 [成功]，而且 [混合式連線] 分頁會顯示新的混合式連線，且狀態為 [未連線]。
	
	![One hybrid connection created][CreateHCOneConnectionCreated]
	
至此，您已完成雲端混合式連線基礎結構的重要部分。接下來，您將建立對應的內部部署部分。

<a name="InstallHCM"></a>
## D.安裝內部部署混合式連線管理員以完成連線 ##

[AZURE.INCLUDE [app-service-hybrid-connections-manager-install](../../includes/app-service-hybrid-connections-manager-install.md)]

現在，混合式連線基礎結構已完成，您將建立使用此基礎結構的 Web 應用程式。

<a name="CreateASPNET"></a>
## E.建立基本 ASP.NET Web 專案、編輯資料庫連接字串，和在本機執行專案 ##

### 建立基本 ASP.NET 專案 ###
1. 在 Visual Studio 的 [檔案] 功能表上，建立新的專案：
	
	![New Visual Studio project][HCVSNewProject]
	
2. 在 [新增專案] 對話方塊的 [範本] 區段中選取 [Web]，再選擇 [ASP.NET Web 應用程式]，然後按一下 [確定]。
	
	![Choose ASP.NET Web Application][HCVSChooseASPNET]
	
3. 在 [新增 ASP.NET 專案] 對話方塊中，選擇 [MVC]，然後按一下 [確定]。
	
	![Choose MVC][HCVSChooseMVC]
	
4. 建立專案之後，會出現應用程式 Readme 頁面。請還不要執行 Web 專案。
	
	![Readme page][HCVSReadmePage]

### 編輯應用程式的資料庫連接字串 ###

在此步驟中您會編輯連接字串，以指示應用程式應至何處尋找您的本機 SQL Server Express 資料庫。連接字串位於應用程式的 Web.config 檔案中，其中包含應用程式的組態資訊。

> [AZURE.NOTE]為確保您的應用程式使用的是您在 SQL Server Express 中建立的資料庫，而不是 Visual Studio 的預設 LocalDB 中的資料庫，請務必先完成此步驟，再執行您的專案。

1. 在 [方案總管] 中，按兩下 Web.config 檔案。
	
	![Web.config][HCVSChooseWebConfig]
	
2. 依照下列範例中的語法編輯 [connectionStrings] 區段，將 SQL Server 資料庫指向您的本機電腦：
	
	![Connection string][HCVSConnectionString]
	
	編譯連接字串時，請留意下列事項：
	
	- 如果您要連接到指定的執行個體而非預設執行個體 (例如 YourServer\SQLEXPRESS)，您必須將 SQL Server 設定成使用靜態連接埠。如需設定靜態連接埠的相關資訊，請參閱[如何將 SQL Server 設定成在特定連接埠上接聽](http://support.microsoft.com/kb/823938)。根據預設，指定的執行個體會使用 UDP 和動態連接埠，而混合式連線並不加以支援。 
	
	- 建議您指定連接埠 (依預設為 1433，如範例所示) 和連接字串，以確定您的本機 SQL Server 會啟用 TCP 並使用正確的連接埠。
	
	- 請務必使用 SQL Server 驗證進行連接，以在您的連接字串中指定使用者識別碼和密碼。
	
3. 在 Visual Studio 中按一下 [儲存]，以儲存 Web.config 檔案。

### 在本機執行專案和註冊新使用者 ###

1. 現在，請按一下 [偵錯] 下的瀏覽按鈕，在本機執行您新的 Web 專案。此範例使用 Internet Explorer。
	
	![Run project][HCVSRunProject]
	
2. 在預設網頁的右上方，選擇 [註冊] 以註冊新帳戶：
	
	![Register a new account][HCVSRegisterLocally]
	
3. 輸入使用者名稱和密碼：
	
	![Enter user name and password][HCVSCreateNewAccount]
	
	這會在您的本機 SQL Server 上自動建立一個資料庫，存放您應用程式的成員資格資訊。其中一個資料表 (**dbo.AspNetUsers**) 包含如同您剛剛輸入的 Web 應用程式使用者認證。稍後在教學課程中會看見此資料表。
	
4. 關閉預設網頁的瀏覽器視窗。這會停止 Visual Studio 中的應用程式。

現在您已可執行下一個步驟，也就是將應用程式發行至 Azure，並加以測試。

<a name="PubNTest"></a>
## F.將 Web 應用程式發行至 Azure 並加以測試 ##

現在，您會將應用程式發行至您的 App Service Web 應用程式並加以測試，以確認您先前設定的混合式連線是否可用來將您的 Web 應用程式連接到本機電腦上的資料庫。

### 發行 Web 應用程式 ###

1. 您可以在 Azure 預覽入口網站中下載 App Service Web 應用程式的發行設定檔。在您 Web 應用程式的分頁上，按一下 [取得發行設定檔]，然後將檔案儲存至您的電腦。
	
	![Download publish profile][PortalDownloadPublishProfile]
	
	接著，您會將此檔案匯入 Visual Studio Web 應用程式中。
	
2. 在 Visual Studio 的 [方案總管] 中，以滑鼠右鍵按一下專案名稱，並選取 [發行]。
	
	![Select publish][HCVSRightClickProjectSelectPublish]
	
3. 在 [Publish Web] 對話方塊的 [設定檔] 索引標籤上，選擇 [匯入]。
	
	![Import][HCVSPublishWebDialogImport]
	
4. 瀏覽至您已下載的發行設定檔並加以選取，然後按一下 [確定]。
	
	![Browse to profile][HCVSBrowseToImportPubProfile]
	
5. 您的發行資訊會匯入並顯示在對話方塊的 [連線] 索引標籤上。
	
	![Click Publish][HCVSClickPublish]
	
	按一下 [發行]。
	
	發行完成後，瀏覽器將會啟動並顯示您熟悉的 ASP.NET 應用程式，差別在於網站現在已運作於 Azure 雲端中。

接著，您將使用即時 Web 應用程式來檢視其運作中的混合式連線。

### 測試 Azure 上已完成的 Web 應用程式 ###

1. 在您位於 Azure 上的網頁中，從右上方選擇 [登入]。
	
	![Test log in][HCTestLogIn]
	
2. 您的 App Service Web 應用程式現在已連接到您本機電腦上的 Web 應用程式成員資格資料庫。若要驗證這一點，請以您先前在本機資料庫中輸入的相同認證登入。
	
	![Hello greeting][HCTestHelloContoso]
	
3. 若要進一步測試新的混合式連線，請登出您的 Azure Web 應用程式，再以另一名使用者的身分註冊。提供新的使用者名稱和密碼，然後按一下 [註冊]。
	
	![Test register another user][HCTestRegisterRelecloud]
	
4. 若要驗證新使用者的認證已透過混合式連線儲存在您的本機資料庫中，請在本機電腦上開啟 SQL Management Studio。在 [物件總管] 中展開 **MembershipDB** 資料庫，然後展開 [資料表]。以滑鼠右鍵按一下 **hybridService1.TodoItems** 成員資格資料表，然後選擇 [Select Top 1000 Rows] 以檢視結果。
	
	![View the results][HCTestSSMSTree]
	
5. 您的本機成員資格資料表此時會將兩個帳戶都顯示出來 - 您在本機建立的帳戶，以及您在 Azure 雲端中建立的帳戶。您在雲端中建立的帳戶已透過 Azure 的混合式連線功能儲存到您的內部部署資料庫。
	
	![Registered users in on-premises database][HCTestShowMemberDb]
	
現在，您已在 Azure 雲端中的 Web 應用程式與內部部署 SQL Server 資料庫之間，建立並部署使用混合式連線的 ASP.NET Web 應用程式。恭喜！

## 另請參閱 ##
[混合式連線概觀](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Josh Twist 介紹混合式連線 (第 9 頻道視訊)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[混合式連線概觀](/services/biztalk-services/)

[BizTalk 服務：儀表板、監視器、調整、設定和混合式連線索引標籤](../biztalk-dashboard-monitor-scale-tabs/)

[透過絕佳的應用程式可攜性建置真實的混合式雲端 (第 9 頻道視訊)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[使用混合式連線從 Azure 行動服務連線至內部佈署 SQL Server](../mobile-services-dotnet-backend-hybrid-connections-get-started.md)

[使用混合式連線從 Azure 行動服務連線到內部部署 SQL Server (第 9 頻道視訊)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

[ASP.NET 身分識別概觀](http://www.asp.net/identity)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]
 

<!-- IMAGES -->
[SQLServerInstall]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A01SQLServerInstall.png
[ChooseDefaultInstance]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A02ChooseDefaultInstance.png
[ChooseMixedMode]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A03ChooseMixedMode.png
[SSMSConnectToServer]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A04SSMSConnectToServer.png
[SSMScreateNewDB]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A05SSMScreateNewDBlh.png
[SSMSprovideDBname]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A06SSMSprovideDBname.png
[SSMSMembershipDBCreated]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A07SSMSMembershipDBCreated.png
[New]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B01New.png
[NewWebsite]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B02NewWebsite.png
[WebsiteCreationBlade]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B03WebsiteCreationBlade.png
[WebSiteRunningBlade]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B04WebSiteRunningBlade.png
[Browse]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B05Browse.png
[DefaultWebSitePage]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B06DefaultWebSitePage.png
[CreateHCHCIcon]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C01CreateHCHCIcon.png
[CreateHCAddHC]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C02CreateHCAddHC.png
[TwinCreateHCBlades]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C03TwinCreateHCBlades.png
[CreateHCCreateBTS]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C04CreateHCCreateBTS.png
[CreateBTScomplete]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C05CreateBTScomplete.png
[CreateHCSuccessNotification]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C06CreateHCSuccessNotification.png
[CreateHCOneConnectionCreated]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C07CreateHCOneConnectionCreated.png
[HCIcon]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D01HCIcon.png
[NotConnected]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D02NotConnected.png
[NotConnectedBlade]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D03NotConnectedBlade.png
[ClickListenerSetup]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D04ClickListenerSetup.png
[ClickToInstallHCM]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D05ClickToInstallHCM.png
[ApplicationRunWarning]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D06ApplicationRunWarning.png
[UAC]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D07UAC.png
[HCMInstalling]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D08HCMInstalling.png
[HCMInstallComplete]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D09HCMInstallComplete.png
[HCStatusConnected]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D10HCStatusConnected.png
[HCVSNewProject]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E01HCVSNewProject.png
[HCVSChooseASPNET]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E02HCVSChooseASPNET.png
[HCVSChooseMVC]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E03HCVSChooseMVC.png
[HCVSReadmePage]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E04HCVSReadmePage.png
[HCVSChooseWebConfig]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E05HCVSChooseWebConfig.png
[HCVSConnectionString]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E06HCVSConnectionString.png
[HCVSRunProject]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E06HCVSRunProject.png
[HCVSRegisterLocally]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E07HCVSRegisterLocally.png
[HCVSCreateNewAccount]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E08HCVSCreateNewAccount.png
[PortalDownloadPublishProfile]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F01PortalDownloadPublishProfile.png
[HCVSPublishProfileInDownloadsFolder]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F02HCVSPublishProfileInDownloadsFolder.png
[HCVSRightClickProjectSelectPublish]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F03HCVSRightClickProjectSelectPublish.png
[HCVSPublishWebDialogImport]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F04HCVSPublishWebDialogImport.png
[HCVSBrowseToImportPubProfile]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F05HCVSBrowseToImportPubProfile.png
[HCVSClickPublish]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F06HCVSClickPublish.png
[HCTestLogIn]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F07HCTestLogIn.png
[HCTestHelloContoso]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F08HCTestHelloContoso.png
[HCTestRegisterRelecloud]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F09HCTestRegisterRelecloud.png
[HCTestSSMSTree]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F10HCTestSSMSTree.png
[HCTestShowMemberDb]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F11HCTestShowMemberDb.png
 

<!---HONumber=62-->