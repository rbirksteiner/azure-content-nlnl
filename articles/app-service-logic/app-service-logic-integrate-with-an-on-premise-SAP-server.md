<properties 
	pageTitle="與 Microsoft Azure 應用程式服務中的內部部署 SAP 伺服器整合"
	description="了解如何與內部部署 SAP 伺服器整合"
	authors="rajeshramabathiran" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/22/2015"
	ms.author="harish"/>


# 與內部部署 SAP 伺服器整合
使用 SAP 連接器，您可以將 Azure 應用程式服務 Web、行動及邏輯應用程式連接到現有的 SAP 伺服器。您可以叫用 RFC、BAPI、tRFC，以及將 IDOC 傳送至 SAP 伺服器。
	
SAP 伺服器甚至可以位於在內部部署的防火牆後面。如果是內部部署伺服器，連線能力會透過混合式接聽程式建立，如下所示：

![Hybrid connectivity flow][1]

雲端中的 SAP 連接器無法直接連接到位於防火牆後面的 SAP 伺服器。混合式接聽程式會透過裝載可讓連接器建立與 SAP 伺服器安全連線的轉送端點來跨越此障礙。


## 與 SAP 整合的不同方式
支援下列動作：

- 呼叫 RFC
- 呼叫 TRFC
- 呼叫 BAPI
- 傳送 IDoc

## 必要條件
在已安裝並執行混合式接聽程式的用戶端電腦上，SAP 特定用戶端程式庫是必要項目。[這裡][9] (位於章節標題為 **SAP 配接器**底下) 會擷取精確的詳細資料。


## 建立新的 SAP 配接器
1. 登入 Microsoft Azure 管理入口網站。 
2. 選取 [**新增**]。
3. 在 [建立] 分頁中，依序選取 [**計算**] > [**Azure Marketplace**]。
4. 在 [Marketplace] 分頁中，選取 [**API 應用程式**]，並在搜尋列中搜尋 SAP：
	
	![SAP 連接器 API 應用程式][2]	
5. 選取 Microsoft 所發佈的 **SAP 連接器**。
6. 在 [SAP 連接器] 分頁中，選取 [**建立**]。
7. 在開啟的新分頁中，輸入下列內容：
	1. **位置** - 選擇您要部署連接器的地理位置
	2. **訂閱** - 選擇您要建立此連接器的訂閱
	3. **資源群組** - 選取或建立連接器所在的資源群組
	4. **Web 裝載方案** - 選取或建立 Web 裝載方案
	5. **定價層** - 選擇連接器的定價層
	6. **名稱** - 輸入 SAP 連接器的名稱
	7. **封裝設定**
		- **伺服器名稱** - 輸入 SAP 伺服器名稱。範例："SAPserver" 或 "SAPserver.mydomain.com"。
		- **使用者名稱** - 輸入要連接到 SAP 伺服器的有效使用者名稱。
		- **密碼** - 輸入要連接到 SAP 伺服器的有效密碼。
		- **系統編號** - 輸入 SAP 應用程式伺服器的系統編號。
		- **語言** - 輸入登入語言，例如 "EN"。如果未輸入任何值，則會被認定為 "EN"。
		- **內部部署** - 輸入 SAP 伺服器是否內部部署在防火牆後面。如果設定為 TRUE，則您必須在能夠存取 SAP 伺服器的伺服器上安裝接聽程式代理程式。您可以移至您的 API 應用程式摘要頁面，然後選取 [混合式連線] 來安裝代理程式。
		- **服務匯流排連接字串** - 如果 SAP 伺服器是內部部署伺服器，則請輸入這個參數。這應該會是有效的服務匯流排命名空間連接字串。
		- **RFC** - 在 SAP 中輸入可由連接器呼叫的 RFC。
		- **TRFC** - 在 SAP 中輸入可由連接器呼叫的 TRFC。
		- **BAPI** - 在 SAP 中輸入可由連接器呼叫的 BAPI。
		- **IDOC** - 在 SAP 中輸入可由連接器傳送的 IDOC。
	8. 選取 [選取]。在幾分鐘內，您的 SAP 連接器便可建立完成。


## 安裝混合式接聽程式
瀏覽至您透過 [**瀏覽**] > [**API 應用程式**] > [*連接器的名稱*] 所建立的 SAP 連接器

在 [連接器] 分頁中，請注意混合式連線的狀態為擱置中。選取 [混合式連線]。[混合式連線] 分頁便會隨即開啟：

![混合式連線分頁][3]

複製主要閘道設定字串您稍後會在混合式接聽程式的安裝過程中用到該資訊。

選取 [**下載及設定**] 連結，並執行 [Click Once 安裝程式]：

![混合式連線 Click Once 安裝程式][4]

選取 [**安裝**]，然後輸入您先前複製的閘道組態設定：

![轉送接聽連接字串][5]

選取 [**安裝**] 以完成混合式連線管理員安裝程式：

![混合式連線管理員安裝進行中][6]

![混合式連線管理員安裝已完成][7]

## 驗證混合式連線
瀏覽至您透過 [**瀏覽**] > [**API 應用程式**] > [*連接器的名稱*] 所建立的 SAP 連接器

在 [連接器] 分頁中，請注意混合式連線狀態為*已連接*：

![混合式連線狀態 - 已連接][8]


## 在邏輯應用程式中使用 SAP 連接器
建立 SAP 連接器之後，它便可以用於邏輯應用程式工作流程中。

透過 [**新增**] > [**邏輯應用程式**] > [**建立**] 來建立新的邏輯應用程式。輸入邏輯應用程式的中繼資料，包括資源群組。

選取 [觸發程序和動作]。[邏輯應用程式工作流程] 設計工具便會隨即開啟。

從右側窗格中選取 SAP 連接器，然後從 [動作] 索引標籤中選取動作。

> [AZURE.NOTE]動作清單會取決於您在建立 SAP 連接器時所輸入的組態。

在選取的動作中，您會看到輸入和輸出參數。您可以在動作的輸入中輸入，並在其他 API 應用程式中使用目前動作的輸出，可能供工作流程中制定進一步決策時使用。

<!--Image references-->
[1]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnectivityFlow.PNG
[2]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/SAPConnector.APIApp.PNG
[3]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnection.PNG
[4]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnection.ClickOnceInstaller.PNG
[5]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnection.ClickOnceInstaller.RelayInformation.PNG
[6]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnectionManager.Install.InProgress.PNG
[7]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnectionManager.Install.Completed.PNG
[8]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/SAPConnector.HybridConnection.Connected.PNG
[9]: http://download.microsoft.com/download/2/D/7/2D7CE8DF-A6C5-45F0-8319-14C3F1F9A0C7/InstallationGuide.htm



 

<!---HONumber=62-->