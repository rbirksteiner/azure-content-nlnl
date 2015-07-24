<properties 
   pageTitle="SAP 連接器" 
   description="如何使用 SAP 連接器" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="harishkragarwal" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/20/2015"
   ms.author="hariag"/>


# SAP 連接器 #

在邏輯應用程式中，連接器可以在執行流程時用來擷取、處理或發送資料。在某些案例中，您可能需要用到在內部部署安裝並位於防火牆後面的 SAP。透過在流程中運用 SAP 連接器，您可以達到各種案例的目的。幾個範例：

1.	透過 web 或行動使用者前端公開位於 SAP 上的部分資料。
2.	處理到期後將資料發佈到 SAP
3.	從 SAP 擷取資料，以供商務程序使用

在這些案例中，請務必完成下列需求：

1. 建立 SAP 連接器 API 應用程式的執行個體
2. 建置 API 應用程式可與內部部署 SAP 進行通訊的混合式連線
3. 在邏輯應用程式中使用建立的 API 應用程式，以達成所需商務程序的目的

## 建立 SAP 連接器 API 應用程式的執行個體 ##

若要使用 SAP 連接器，您必須建立 SAP 連接器 API 應用程式的執行個體。以下步驟可以達到此目的：

1. 使用 Azure 入口網站左下方的 [+新增] 選項開啟 Azure Marketplace。
2. 瀏覽至 [Web 與行動] > [API 應用程式]，並搜尋「SAP 連接器」。
3. 進行下列設定：
	1. 在第一個分頁中提供一般詳細資訊，例如名稱、應用程式服務方案等等
	2. 封裝設定過程中必須提供 SAP 認證。並提供 Azure 服務匯流排連接字串。這可用來建置與內部部署 SAP 的混合式連線。 
	3. RFC、TRFC、BAPI 和 IDOC 必須根據案例需求進行設定。如果需要多個值，則以逗號分隔的方式提供

![][1]

## 設定剛建立的 SAP 連接器 API 應用程式 ##

透過 [瀏覽] -> [API 應用程式] -> [<Name of the API App just created>] 瀏覽到剛建立的 API 應用程式，您將會看到下列行為。因為尚未建立混合式連線，所以安裝未完成。

![][2]

若要建置混合式連線，請執行下列作業：

1. 複製主要連接字串
2. 按一下 [下載及設定] 連結
3. 依照剛起始的安裝程序執行，並在要求時提供主要連接字串
4. 完成安裝程序後，便會顯示如下所示的對話方塊

![][3]

現在當您重新瀏覽至建立的 API 應用程式時，您會看到混合式連線狀態為 [已連線]。

![][4]

注意：如果您要切換到次要連線字串，您只需重做混合式設定，並提供次要連接字串來取代主要連接字串即可

## 邏輯應用程式中的使用方式 ##

在邏輯應用程式中，SAP 連接器只可以做為動作/步驟使用。

建立/編輯邏輯應用程式時，請選擇上面剛建立的 SAP 連接器 API 應用程式。這會一次列出可供選擇的所有允許動作。

![][5]

選取動作時，會同時列出該動作的輸入參數。請提供適當的值，並按一下 [勾號] 圖示。

![][6]

邏輯應用程式中的步驟/動作現在會顯示為已設定。作業的輸出會隨即顯示，而可在後續步驟中用做輸入值。

![][7]

完成邏輯應用程式以定義商務程序，然後執行它來達到所需的目的。

<!--Image references-->
[1]: ./media/app-service-logic-connector-sap/Create.jpg
[2]: ./media/app-service-logic-connector-sap/BrowseSetupIncomplete.jpg
[3]: ./media/app-service-logic-connector-sap/HybridSetup.jpg
[4]: ./media/app-service-logic-connector-sap/BrowseSetupComplete.jpg
[5]: ./media/app-service-logic-connector-sap/LogicApp1.jpg
[6]: ./media/app-service-logic-connector-sap/LogicApp2.jpg
[7]: ./media/app-service-logic-connector-sap/LogicApp3.jpg


 

<!---HONumber=62-->