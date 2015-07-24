<properties 
	pageTitle="在 Azure App Service 中使用混合式連線管理員" 
	description="在 Azure App Service 中安裝和設定混合式連線管理員；微服務架構" 
	services="app-service\logic" 
	documentationCenter=".net,nodejs,java"
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/14/2015" 
	ms.author="mandia"/>

# 在 Azure App Service 中使用混合式連線管理員
某些連結器可以連接到內部部署系統，例如 SQL Server、SAP、SharePoint 等等。為了使用內部部署系統，Azure App Service 會使用混合式連線管理員。

混合式連線管理員 (HCM) 是 Click Once 安裝程式，安裝在您的網路內防火牆後方的 IIS 伺服器上。HCM 會使用 Azure 服務匯流排轉送，向 Azure 中的連接器驗證內部部署系統。

> [AZURE.NOTE]只有當您連接到防火牆後方的內部部署資源時，才需要混合式連線管理員。如果您未連接至內部部署系統時，則不需要混合式連線管理員。

若要開始，您需要：

- Azure 服務匯流排轉送命名空間 SAS 連接字串。請參閱[服務匯流排定價](http://azure.microsoft.com/pricing/details/service-bus/)，判斷哪一層包含轉送。
- 內部部署系統登入資訊，包括使用者名稱和密碼。例如，如果您要連線至內部部署 SQL Server，您需要 SQL Server 登入帳戶和密碼。
- 內部部署伺服器資訊，包括連接埠號碼和伺服器名稱。例如，如果您要連線至內部部署 SQL Server，您需要 SQL Server 名稱和 TCP 連接埠號碼。

## 取得服務匯流排連接字串

在 Azure 入口網站中，複製服務匯流排根 SAS 連接字串。此連接字串會將 Azure 連接器連接至內部部署系統。

1. 在 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=213885)中，選取您的服務匯流排命名空間，然後選取 [**連接資訊**]：

	![][SB_ConnectInfo]

2. 複製 SAS 連接字串：

	![][SB_SAS]

## 安裝混合式連線管理員

1. 在預覽 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)中，選取您建立的連接器。若要開啟它，您可以選取 [**瀏覽**]，選取 [**API Apps**]，然後選取您的連接器或 API 應用程式。<br/><br/> 在 [**混合式連線**] 中，設定**未完成**：<br/> ![][2] 

2. 選取 [**混合式連線**]。將會列出您先前輸入的服務匯流排連接字串。
3. 複製 [**主要組態字串**]：<br/> ![][PrimaryConfigString]

4. 在 [**內部部署混合式連線管理員**] 下，您可以下載混合式連線管理員，或直接從入口網站安裝它。<br/><br/> 若要直接從入口網站安裝，請移至內部部署 IIS 伺服器，瀏覽至入口網站，然後選取 [**下載及設定**]。<br/><br/> 若要下載混合式連線管理員，請移至內部部署 IIS 伺服器，並移至 **ClickOnce 應用程式** (http://hybridclickonce.azurewebsites.net/install/Microsoft.Azure.BizTalk.Hybrid.ClickOnce.application)。將會自動開始安裝讓您執行。

5. 在 [**接聽程式設定**] 視窗中，輸入您先前 (在步驟 3) 貼上的 [**主要組態字串**]，然後選取 [**安裝**]。

安裝完成時會顯示下列訊息：<br/> ![][3]

現在當您重新瀏覽至連接器時，混合式連線狀態為 [**已連接**]。您可能必須關閉連接器，再重新開啟它：<br/> ![][4]

> [AZURE.NOTE]若要切換至次要連接字串，請重新執行混合式連線設定，並輸入 [**次要組態字串**]。


## TCP 連接埠與安全性

當您建立混合式連線時，您的本機內部部署 IIS 伺服器上會建立一個網站。IIS 伺服器可以在 DMZ 中。IIS 伺服器上的 TCP 連接埠需求包括：

TCP 連接埠 | 理由
--- | ---
 | 不需要任何內送 TCP 連接埠。
9350 - 9354 | 這些連接埠用於資料傳輸。服務匯流排轉送管理員會探查連接埠 9350，以判斷 TCP 連線是否可用。如果可用的話，則會假設連接埠 9352 也是可用。資料流量會經過連接埠 9352。<br/><br/>允許對這些連接埠的輸出連線。
5671 | 當連接埠 9352 用於資料流量時，連接埠 5671 就做為控制通道。<br/><br/>允許對此連接埠的輸出連線。 
80、443 | 如果連接埠 9352 和 5671 無法使用，*則*連接埠 80 和 443 是用於資料傳輸和控制通道的後援連接埠。<br/><br/>允許對這些連接埠的輸出連線。
內部部署系統連接埠 | 在內部部署系統上，開啟系統所使用的連接埠。例如，SQL Server 通常會使用連接埠 1433。開啟此 TCP 連接埠。

[利用服務匯流排在防火牆後面進行裝載](http://msdn.microsoft.com/library/azure/ee706729.aspx)

## 疑難排解

![][HCMFlow]

### 內部部署疑難排解

1. 在 IIS 伺服器上，確認已安裝 IIS Web 角色，而且所有 IIS 服務都已啟動。
2. 在 IIS 伺服器上，確認混合式連線管理員已安裝且正在執行：
 - 在 IIS 管理員 (inetmgr) 中，***MicrosoftAzureBizTalkHybridListener*** 網站應該會列出且在執行中。 
 - 此網站使用以 *NetworkService* 本機內建使用者帳戶執行的 ***HybridListenerAppPool***。此 AppPool 也應該啟動。
3. 在 IIS 伺服器上，確認該連接器已安裝且在執行中： 
 - 已經為您的 App Service 連接器建立一個網站。例如，如果您已建立 SQL 連接器，則會有 ***MicrosoftSqlConnector_nnn*** 網站。在 IIS 管理員 (inetmgr) 中，請確認此網站列出且已啟動。 
 - 此網站使用自己的 IIS 應用程式集區，名稱為 ***HybridAppPoolnnn***。此 AppPool 以 *NetworkService* 本機內建使用者帳戶執行。此網站和 AppPool 都應該啟動。 
 - 瀏覽本機連接器。例如，如果您的連接器網站使用連接埠 6569，請瀏覽至 http://localhost:6569。因為沒有設定預設文件，所以會發生 `HTTP Error 403.14 - Forbidden error`。
4. 在您的防火牆，請確認本主題列出的 TCP 連接埠已開啟。
5. 查看來源或目的地系統：
 - 有些內部部署系統需要額外的相依性檔案。例如，如果您要連線至內部部署 SAP，一些額外的 SAP 檔案必須安裝在 IIS 伺服器上。
 - 使用登入帳戶檢查系統的連線。比方說，系統所使用的 TCP 連接埠必須開啟，例如 SQL Server 的連接埠 1433。您在 Azure 入口網站中所輸入的登入帳戶必須有系統的存取權。
6. 在 IIS 伺服器上，檢查事件記錄檔中是否有任何錯誤。 
7. 清理並重新安裝混合式連線管理員： 
 - 在 IIS 中，手動刪除連接器網站及其應用程式集區。 
 - 重新執行混合式連線管理員，並確認您輸入連接器的正確 [**主要組態字串**]。



### 在 Azure 入口網站

1. 確認服務匯流排命名空間為**作用中**狀態。
2. 當您建立連接器時，輸入服務匯流排 SAS 連接字串。不要輸入 ACS 連接字串。


## 常見問題集

**問題**：有兩個混合式連線管理員。有何不同？<br/> **回答**：這是主要由 Web Apps (先前的網站) 和 Mobile Apps (先前的行動服務) 用來連接至內部部署的[混合式連線](../integration-hybrid-connection-overview.md)技術。此混合式連線管理員是其本身的[設定](../integration-hybrid-connection-create-manage.md)，而且使用 Azure BizTalk 服務 (幕後)。它僅支援 TCP 和 HTTP 通訊協定。

在 Azure App Service 連接器方面，我們也有混合式連線管理員。此混合式連線管理員*不*使用 Azure BizTalk 服務 (幕後)，而且支援除了 TCP 和 HTTP 以外的更多通訊協定。請參閱[連接器和 API 應用程式清單](app-service-logic-connectors-list.md)。

兩者都使用 Azure 服務匯流排來連接到內部部署系統。

**問題**：當我建立自訂的 API 應用程式時，我可以使用 App Service 混合式連線管理員來連接至內部部署嗎？ <br/> **回答**：這不符合慣例。您可以使用內建的連接器，設定 App Service 混合式連線管理員來連接至內部部署系統。然後，使用此連接器搭配您的自訂 API 應用程式，可能是使用邏輯應用程式。目前，您無法開發或建立自己的混合式 API 應用程式 (例如 SQL 連接器或檔案連接器)。

如果您的自訂 API 使用 TCP 或 HTTP 連接埠，您可以使用[混合式連線](../integration-hybrid-connection-overview.md)及其混合式連線管理員。在此情況下會使用 Azure BizTalk 服務。[從 Web 應用程式連接至內部部署 SQL Server](../app-service-web/web-sites-hybrid-connection-connect-on-premises-sql-server.md) 可能會有幫助。


## 閱讀更多資訊

[監視 Logic Apps](app-service-logic-monitor-your-logic-apps.md)<br/> [服務匯流排定價](http://azure.microsoft.com/pricing/details/service-bus/)



[SB_ConnectInfo]: ./media/app-service-logic-hybrid-connection-manager/SB_ConnectInfo.png
[SB_SAS]: ./media/app-service-logic-hybrid-connection-manager/SB_SAS.png
[PrimaryConfigString]: ./media/app-service-logic-hybrid-connection-manager/PrimaryConfigString.png
[HCMFlow]: ./media/app-service-logic-hybrid-connection-manager/HCMFlow.png
[2]: ./media/app-service-logic-hybrid-connection-manager/BrowseSetupIncomplete.jpg
[3]: ./media/app-service-logic-hybrid-connection-manager/HybridSetup.jpg
[4]: ./media/app-service-logic-hybrid-connection-manager/BrowseSetupComplete.jpg

 

<!---HONumber=62-->