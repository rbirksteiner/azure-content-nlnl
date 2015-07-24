<properties 
	pageTitle="如何設定 App Service 環境" 
	description="設定、管理和監視 App Service環境" 
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

# 設定 App Service 環境 #

## 概觀 ##

App Service 環境是目前以預覽狀態提供的新 Premium 層次功能。它還提供新的調整和網路存取功能。這項新的調整功能可讓您將 Azure App Service 的執行個體放入 VNET 中。如果您不熟悉 App Service 環境 (ASE) 功能，請閱讀此處的文件：[What is an App Service Environment](app-service-app-service-environment-intro.md)。如需有關如何建立 ASE 的資訊，請讀取文件：[如何建立 App Service 環境](app-service-web-how-to-create-an-app-service-environment.md)。

App Service 環境是由數個主要元件所組成：

- 在 Azure App 環境託管服務中執行的計算資源
- 儲存體
- 資料庫
- 具有至少一個子網路的虛擬網路
- 在其中執行 Azure App 環境託管服務的子網路

為了協助管理和監視 App Service 環境，您可以從 Azure Preview 入口網站中的 [瀏覽] -> [App Service 環境] 存取該目的的 UI。初始版本有管理系統所需的功能，而且將會持續在未來幾週內藉由其他功能提供改善。

![][1]

## 監視 ##

初始預覽版本中並沒有很多計量功能，但是會在近期內推出。這些計量功能將協助系統管理員做出系統調整和作業相關決策。

即使現在，您可以在入口網站中列出 ASE 中的所有 App Service 方案，以及 App Service 環境中的所有 Web 應用程式。若要查看任何一份清單，請移至 [設定]，並選取您感興趣的項目。

![][3]

在這兩份清單中，您可以看到背景工作集區指派有多少個執行個體，以及所使用的計算資源大小。開啟 App Service 方案 UI，即可跟平常一樣取得個別 App Service 方案內效能的相關詳細資訊。

![][4]

## 計算資源 ##

儲存體和資料庫等計算資源全都是由 Azure App Service 操作。計算資源的數量和大小是由使用者來決定。

不論計算資源的大小為何，最少的使用量會具有 2 個前端伺服器和 2 個背景工作。App Service 環境可設定為最多總計使用 55 個計算資源。在這 55 個計算資源中，只有 50 個可用來裝載工作負載。其原因有兩個。前端計算資源的下限為 2 個。上限可高達 53 個，用以支援背景工作集區配置。為了提供容錯功能，您需要根據下列規則配置額外的計算資源：

- 每個背景工作集區至少需要一個無法指派工作負載的額外計算資源
- 當集區中的計算資源數量超出特定值時，則需要另一個計算資源

在任何單一背景工作集區中，容錯需求就是指派給背景工作集區的 X 個資源的指定值：

- 如果 X 介於 2 到 20，您可用於工作負載的可用計算資源數量為 X-1
- 如果 X 介於 21 到 40，您可用於工作負載的可用計算資源數量為 X-2
- 如果 X 介於 41 到 53，您可用於工作負載的可用計算資源數量為 X-3

除了能夠管理您可指派給指定集區的計算資源數量以外，您也可以控制大小。在 App Service 環境中，您可以選擇 4 個標示為 P1 至 P4 的不同大小。如需這些大小及其定價的詳細資訊，請參閱 [App Service 訂價](../app-service/app-service-value-prop-what-is.md)。P1 至 P3 計算資源大小就是通常可用的大小。P4 計算資源提供 14 GB RAM 的八核心， 而且只適用於 App Service 環境。

如先前所述，App Service 環境功能目前為預覽狀態，因此仍有成長空間。除了額外的監視功能以外，更多管理功能將會隨著 App Service 環境移至 GA 而推出。現在只可以在這個介面中管理下列幾項：

- 每個集區中的計算資源數目
- 每個集區中的計算資源大小
- 可用的 IP 位址數目

若要控制這些項目，請選取位於頂端的 [調整] 設定項目。

![][2]

在此可以調整每個集區中的計算資源數量與其大小。不過在進行任何變更之前，請務必注意下列事項：

- 視要求多少變更而定，所做的變更可能需要數小時才能完成
- 已經有 App Service 環境組態變更正在進行時，您就無法開始另一項變更
- 如果您變更背景工作集區中使用的計算資源大小，您可能會導致在該背景工作集區中執行的 Web 應用程式中斷

將其他執行個體加入至背景工作集區是一項良性作業，不會對系統造成影響。不過，變更用於背景工作集區中的計算資源大小則是另一回事。若要避免在背景工作集區的大小變更期間發生停機，最好是：

- 使用未使用的背景工作集區，以提出所需大小的必要執行個體
- 將 App Service 方案調整為新的背景工作集區。  
 
相較於變更具有執行中工作負載的計算資源大小，這麼做比較不會干擾執行中的應用程式。如需在 App Service 環境中調整 Web 應用程式的詳細資訊，請移至[在 App Service 環境中調整 Web Apps](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

## 虛擬網路 ##

[虛擬網路][virtualnetwork]和子網路全都在使用者的控制之下。App Service 環境的確有一些網路需求，但其餘部分是由使用者控制。這些 ASE 需求包括：

- 至少有 512 個位址的 VNET
- 至少有 256 個位址的子網路 
- VNET 必須是區域 VNET  
 
透過一般虛擬網路 UI 即可管理您的 VNET。

因為這項功能會將 Azure App Service 放入 VNET，這表示裝載於 ASE 的應用程式現在可以存取直接透過 ExpressRoute 或站台對站台 VPN 提供的資源。您的 APP Service 環境中的應用程式不需要額外的網路功能，即可存取裝載 App Service 環境的 VNET 可用的資源。

如有需要，您現在也可以使用網路安全性群組來控制存取。這項功能可讓您鎖住您的 App Service 環境，只您想要限制它的 IP 位址。如需相關詳細資訊，請參閱以下文件：[如何在 App Service 環境中控制輸入流量](app-service-app-service-environment-control-inbound-traffic.md)。

## 刪除 App Service 環境 ##

如果您要刪除 App Service 環境，只需使用 App Service 環境刀鋒視窗頂端的 [刪除] 動作。不過，您無法刪除有內容存在的 ASE。請務必移除所有的 Web 應用程式和 App Service 方案，以便刪除 App Service 環境。

## 開始使用

若要開始使用 App Service 環境，請參閱[如何建立 App Service 環境](app-service-web-how-to-create-an-app-service-environment.md)

如需有關 Azure App Service 平台的詳細資訊，請參閱 [Azure App Service](../app-service/app-service-value-prop-what-is.md)。

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-configure-an-app-service-environment/configureaseblade.png
[2]: ./media/app-service-web-configure-an-app-service-environment/configurescale.png
[3]: ./media/app-service-web-configure-an-app-service-environment/configureasplist.png
[4]: ./media/app-service-web-configure-an-app-service-environment/configurewebapplist.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment/
[ControlInbound]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[virtualnetwork]: https://msdn.microsoft.com/library/azure/dn133803.aspx
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
 

<!---HONumber=62-->