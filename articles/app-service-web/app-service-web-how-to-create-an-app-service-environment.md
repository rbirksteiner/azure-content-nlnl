<properties 
	pageTitle="如何建立 App Service 環境" 
	description="App Service 的建立流程說明" 
	services="app-service\web" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="ccompy"/>

# 如何建立 App Service 環境 #

App Service 環境 (ASE) 是目前預覽狀態下 Azure App Service 的 Premium 服務選項。它提供的增強設定功能並不適用於多租用戶戳記。若要更深入了解 App Service 環境所提供的功能，請閱讀[什麼是 App Service 環境][WhatisASE]文件。

### 概觀 ###

ASE 功能基本上會將 Azure App Service 部署到客戶的 VNET 中。若要這麼做，客戶需要：

- 區域 VNET，有超過 512 (/ 23) 個或更多地址
- 此 VNET 中的子網路，其中有 256 (/ 24) 個或更多地址

如果您尚未有想用來裝載 App Service 環境的 VNET，您可以在 App Service 環境建立期間建立一個 VNET。

每個 ASE 部署都是 Azure 管理和維護的託管服務。雖然客戶會管理執行個體的數量和其大小，但不能存取裝載 ASE 系統角色的計算資源。

## 建立 App Service 環境 ##

存取 ASE 建立 UI 的方法有兩種。在 Azure Marketplace 中搜尋 ***App Service 環境***或經由 [新增] -> [Web + 行動]，即可找到。

### 快速建立 ###
輸入建立 UI 之後，只要輸入部署的名稱，即可快速建立 ASE。接著會建立具有 512 個位址的 VNET、在該 VNET 中具有 256 個位址的子網路，以及在背景工作集區 1 中有 2 個前端和 2 個背景工作的 ASE 環境。請務必選取您要設置系統的位置，以及您要系統所屬的訂用帳戶。唯一可使用 ASE 來裝載內容的帳戶必須屬於用來建立帳戶的訂用帳戶。

針對 ASE 指定的名稱將用於在 ASE 中建立的 Web 應用程式。如果 ASE 的名稱是 appsvcenvdemo，則網域名稱會是 .*appsvcenvdemo.p.azurewebsites.net*。如果您因此建立名為 mytestapp 的 Web 應用程式，則可定址於 *mytestapp.appsvcenvdemo.p.azurewebsites.net*。您無法在名稱中使用空白字元。如果您在名稱中使用大寫字元，則網域名稱會是該名稱的全小寫版本。


![][1]

### 計算資源集區 ###

用於 App Service 環境的計算資源會在計算資源集區中進行管理，除了設定其大小以外，還可設定您在集區中有多少個計算資源執行個體。App Service 環境是由前端伺服器和背景工作所組成。前端伺服器可處理應用程式連線負載和執行應用程式程式碼的背景工作。前端伺服器是在專用的計算資源集區中進行管理。而背景工作則是在以下 3 個不同的計算資源集區中進行管理：

- 背景工作集區 1
- 背景工作集區 2
- 背景工作集區 3

如果您有針對簡單 Web 應用程式的大量要求，您便可能相應增加前端，而有較少的背景工作。如果您有需要大量 CPU 或記憶體但流量不高的 Web 應用程式，則您不需要許多前端，但可能需要更多或更大的背景工作。

不論計算資源的大小為何，最少的使用量會具有 2 個前端伺服器和 2 個背景工作。App Service 環境可設定為最多總計使用 55 個計算資源。在這 55 個計算資源中，只有 50 個可用來裝載工作負載。其原因有兩個。前端計算資源的下限為 2 個。上限可高達 53 個，用以支援背景工作集區配置。為了提供容錯功能，您需要根據下列規則配置額外的計算資源：

- 每個背景工作集區至少需要一個無法指派工作負載的額外計算資源
- 當集區中的計算資源數量超出特定值時，則需要另一個計算資源

在任何單一背景工作集區中，容錯需求就是指派給背景工作集區的 X 個資源的指定值：

- 如果 X 介於 2 到 20，您可用於工作負載的可用計算資源數量為 X-1
- 如果 X 介於 21 到 40，您可用於工作負載的可用計算資源數量為 X-2
- 如果 X 介於 41 到 53，您可用於工作負載的可用計算資源數量為 X-3

除了能夠管理您可指派給指定集區的計算資源數量以外，您也可以控制大小。在 App Service 環境中，您可以選擇 4 個標示為 P1 至 P4 的不同大小。如需大小及其定價的詳細資訊，請參閱 [App Service 定價][AppServicePricing]。P1 至 P3 計算資源大小與多租用戶環境中可用的大小相同。P4 計算資源提供 14 GB RAM 的八核心， 而且只適用於 App Service 環境。

App Service 環境的定價是根據指派的計算資源。無論是否裝載工作負載，您都需支付配置給您的 App Service 環境的計算資源。



### 建立 VNET ###
雖然快速建立功能可自動建立新的 VNET，但這項功能還支援選取現有的 VNET 和手動建立 VNET。如果現有的 VNET 夠大，您可加以選取，以支援 App Service 環境部署。VNET 必須有 512 個或更多位址。如果您選取預先存在的 VNET，您也必須指定要使用的子網路或建立新的子網路。子網路必須有 256 個或更多位址。

如果要經由 VNET 建立 UI，您必須提供：

- VNET 名稱
- CIDR 表示法中的 VNET 位址範圍
- 子網路名稱
- CIDR 表示法中的子網路範圍

如果您不熟悉 CIDR 表示法，它是採用 10.0.0.0/22 格式，其中 /22 指定範圍。在此範例中，/22 表示 1024 個位址或 10.0.0.0 -10.0.3.255 的範圍。/23 表示 512 個位址等等。

![][2]

### App Service 環境大小定義 ###

接下來要設定的是系統的範圍。依預設有 2 個前端 P2 計算資源、2 個 P1 背景工作和 1 個 IP 位址。有 2 個前端，以提供高可用性和分散負載。前端的最小大小為 P2，以確有有足夠的容量來支援適當的系統。如果您知道系統需要支援大量要求，您可以調整前端的數量和所用的伺服器大小。

如先前所述，ASE 中有 3 個客戶可以定義的背景工作集區。計算資源大小可以是 P1 至 P4。依預設背景工作集區 1 中只會設定 2 個 P1 背景工作。這足以支援具有 1 個執行個體的單一 App Service 方案。

滑桿會自動調整，以反映 App Service 環境中可用的總計算容量。隨著滑桿在任何一個集區內調整，其他滑桿會跟著變更，以反映在到達 55 前剩餘的可用計算資源數量。
 
![][3]

新增可用的執行個體時速度不會很快。如果您知道即將需要額外的計算資源，您應該事先佈建妥當。根據要加入系統的計算資源數量而定，佈建時間可能需要很多小時。請記住，為了確保您的系統可符合容錯需求，每個 ASE 都需有在每個背景工作集區中可用的保留執行個體。

根據預設，ASE 隨附 1 個可供 IP SSL 使用的 IP 位址。如果您知道您將需要更多數量，您可以在此進行指定或在建立後加以管理。
  
### 在 App Service 環境建立之後 ###

建立 ASE 之後，您可以調整：

- 前端的數量 (最小值：2)
- 背景工作的數量 (最小值：2)
- IP 位址的數量
- 前端或背景工作所使用的計算資源大小 (前端大小下限為 P2)

您無法變更：

- 位置
- 訂閱
- 資源群組
- 使用的 VNET
- 使用的子網路

以下有更多關於管理及監視 App Service 環境的詳細資料： [如何設定 App Service 環境][ASEConfig]

有其他無法自訂的相依性，例如資料庫和儲存體。這些都是由 Azure 處理並由系統隨附。系統儲存體為整個 App Service 環境最多支援 500 GB。


## 開始使用

若要開始使用 App Service 環境，請參閱 [App Service 環境簡介][WhatisASE]

如需有關 Azure App Service 平台的詳細資訊，請參閱 [Azure App Service][AzureAppService]。

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/createaseblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/createasenetwork.png
[3]: ./media/app-service-web-how-to-create-an-app-service-environment/createasescale.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ASEConfig]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/

<!---HONumber=62-->