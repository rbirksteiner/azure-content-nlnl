<properties 
   pageTitle="在 Microsoft Azure 應用程式服務中建立 B2B 程序" 
   description="建立企業對企業程序的概觀" 
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
   ms.date="03/22/2015"
   ms.author="hariag"/>


# 建立 B2B 程序


## 商務案例 
Contoso 和 Northwind 是兩個商業夥伴。Contoso (零售商) 會透過業界等級傳輸 (例如 AS2) 將採購單傳送至 Northwind (供應商)。Northwind 會在其雲端儲存體中儲存接收的所有訂單。採購單會是這兩個夥伴之間的 XML 訊息。將訊息儲存在 Northwind 的雲端儲存體之後，Northwind 的內部程序便會接手處理訂單。
 
本教學課程的目標是 Northwind 可以如何藉由從其夥伴 Contoso 透過 AS2 接收的訊息 (採用 XML 格式的採購單) 來建立商務程序，然後將它保存在其雲端儲存體中。


## 示範的功能 
本教學課程可協助展現下列功能：

- **訊息傳輸**：零售商和供應商可以位於不同平台，但它們仍然可以進行兩者之間的通訊。在本教學課程中，他們會透過 AS2 (Applicability Statement 2) 進行通訊。在企業對企業通訊中，AS2 是在交易夥伴之間傳輸資料的一個常用方式。
- **資料永續性**：在透過 AS2 接收到訊息之後，Northwind 想要將它保存以便進一步處理。它可以使用連接器來將訊息保存在其雲端儲存體中。在本教學課程中，Northwind 會採用 Azure Blob 做為雲端儲存體 。
- **建立商務程序**：在流程中，您可以結合多個 API 應用程式以達到如下所示的商務成果。


## 開始之前
本教學課程會假設您已具備 Azure 應用程式服務的基本知識，知道如何建立 API 應用程式以及結合流程。


## 完成此商務案例的步驟
**建立和設定必要的 API 應用程式**

1. 建立 **Azure 儲存體 Blob 連接器**的執行個體。這需要 Azure 儲存體帳戶的認證。確定您在開始建立之前已將它準備好。
2. 建立 **BizTalk 交易夥伴管理**的執行個體。這需要一個空白的 SQL Database 才能運作。確定您在開始建立之前已將它準備好。
3. 建立 **AS2 連接器**的執行個體。這也需要一個空白的 SQL Database 才能運作。確定您在開始建立之前已將它準備好。此外，如果您想要在 AS2 處理的過程中封存訊息，您可能要在其建立期間提供 Azure Blob 的認證。
4. 設定已建立的 TPM (交易夥伴管理) 服務：
	1. 瀏覽至上述步驟中所建立之 TPM 服務的執行個體。
	2. 使用 [*元件*] 下的[**夥伴**] 選項，來**新增**一個名為 **Contoso** 的夥伴，並在其設定檔中加入必要的 AS2 身分識別。
	3. 使用 [*元件*] 下的[**夥伴**] 選項，來**新增**一個名為 **Northwind** 的夥伴，並在其設定檔中加入必要的 AS2 身分識別。
	4. 使用 [*元件*] 下的 [**協議**] 選項，以在 Northwind 和 Contoso 之間**加入**新的 AS2 協議。在這裡，Northwind 將是託管夥伴，而 Contoso 將是來賓夥伴。在此協議建立期間，視情況設定簽章、加密、壓縮和通知。瀏覽所建立的 TPM 服務時，如果需要使用憑證，您可以透過 [**憑證**] 選項將他們上傳。


## 建立流程 / 商務程序
1. 建立第一個步驟是 AS2 的新流程。拖放 [**AS2 連接器**]，並選擇已建立的執行個體。在功能中選擇觸發程序

![][1]

2. 接下來，拖放 [**Azure 儲存體 Blob 連接器**]，並選擇已建立的執行個體。在功能中選擇動作，並在其中選取 [上傳 Blob] 做為所需的功能。依適當情況設定

3. 現在建立/部署流程


## 訊息處理與疑難排解
1. 現在可以開始測試已部署的流程。將包裝在 AS2 的 XML 訊息 (根據上面建立 AS2 協議) 傳送至可供已建立 AS2Connector 執行個體瀏覽的 AS2 端點。若要能夠公開存取端點，您可能需要設定端點的驗證。
2. 您可以瀏覽流程的相關執行資訊，方法是瀏覽至流程，然後進入執行流程執行個體的步驟。
3. 如需 AS2 處理資訊，請瀏覽至叫用的 AS2Connector 執行個體，然後緊跟著進入 [追蹤] 部分開始執行。您可以使用相關的篩選，以將檢視限制在所需的資訊。

![][2]

<!--Image references-->
[1]: ./media/app-service-logic-create-a-b2b-process/Flow.jpg
[2]: ./media/app-service-logic-create-a-b2b-process/Tracking.jpg
 

<!---HONumber=62-->