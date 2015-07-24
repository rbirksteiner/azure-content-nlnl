<properties 
   pageTitle="Azure Government 概觀" 
   description="本文提供 Azure Government 雲端功能，以及支援聯邦政府、州政府與當地政府組織及其合作夥伴之適用法務遵循，值得信賴的設計與安全性概觀。" 
   services="Azure-Government" 
   documentationCenter="" 
   authors="joharve2" 
   manager="chrisnie" 
   editor=""/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="azure-government" 
   ms.date="03/13/2015"
   ms.author="john.harvey@microsoft.com"/>

#  Microsoft Azure Government 概觀 

<p> Microsoft Azure Government 是 Microsoft Azure 的實體及網路隔離執行個體。此開發人員指南將針對應用程式開發人員和系統管理員需要與這些 Azure 個別區域互動及處理的差異，提供相關提供詳細資料。


## <a name="Overview"></a>概觀

Azure Government 是一個政府社群雲端 (GCC)，設計來支援美國政府機構需要速度、大規模、安全性、法務遵循與高經濟效益的策略性政府狀況。它是根據 Microsoft 在其他 Microsoft 雲端產品中 (例如 Azure Public、Office 365、O365 GCC，Microsoft CRM Online 等) 提供軟體、安全性、法務遵循與控制項方面豐富的經驗所開發。

此外，Azure Government 的設計符合法規(例如，聯邦政府風險與授權管理計畫 (FedRAMP)、國防部企業雲端服務仲介 (ECSB)、刑事犯罪資料 (CJIS) 安全性政策與健康保險可攜與責任法 (HIPAA)) 中機密專用之美國公共部門工作量，更高層級的安全性與法務遵循需求。

以下是 Azure Government 可用來協助政府組織建置混合式雲端解決方案，以滿足其目標的雲端基礎結構、網狀架構、服務和架構。因為下列部分服務只於預覽版中提供，請參閱[區域頁面](http://azure.microsoft.com/regions/#services)中所列出之可用的最新服務。

![][2]

Azure Government 包括基礎設施即服務 (IaaS) 和平台即服務 (PaaS) 的核心元件。這包括基礎結構、網路、儲存體、資料管理、身分識別管理和許多其他服務。Azure Government 支援公用 Azure 客戶所運用的相同絕佳功能，例如同步通信資料複寫和自動調整。Microsoft 藉由領導業界的分析師，已經視為是 <a href="https://www.gartner.com/doc/2575715/magic-quadrant-cloud-infrastructure-service" target="_new">IaaS</a> 和 <a href="https://www.gartner.com/doc/2645317/magic-quadrant-enterprise-application-platform" target="_new">PaaS<a/> 兩者的領導者。

除了提供公用 Azure 的強固服務和功能，Azure Government 為了確保美國政府實體及其資料的安全性，還提供了許多功能：

- **實體和網路隔離的執行個體** – Azure Government 環境是一個完全獨立於公用 Microsoft Azure 的執行個體，只由限定的美國政府機構和解決方案提供者所使用。

- **安全性、隱私權和法務遵循** - Microsoft 已實作其強固的安全性、隱私權和法務遵循控制項架構，再加上其他嚴苛的控制項，以符合 ECSB 影響等級和 CJIS 中的高層級要求。

- **資料儲存體** – Azure Government 環境維護 2 個距離超過 500 英哩遠的資料中心。所有客戶管理資料都存放於美國本土 (CONUS) 的資料中心

- **美國人員** – 所有 Azure Government 操作員和系統管理員都是經過篩選的美國公民。

- **身分識別管理** – Azure Government 環境內的「身分識別管理」是 Azure Active Directory 的個別執行個體。

- **法務遵循** – Microsoft 持續致力於符合與維持嚴格與持續變更的聯邦、州與當地法務遵循要求，例如適用於美國政府雲端解決方案的 FedRAMP、CJIS、ECSB 與 HIPAA。

- **雲端整合** – Azure Government 提供了一個與 O365 Government 整合的環境，允許跨雲端服務與增強服務的單一登入，例如 1TB 的 OneDrive 儲存空間。

Azure Government 也讓組織維持其現有的技術投資，並實現雲端服務的優點。因為 Azure Government 是互通的雲端平台，具有組織可從頭開始建置更開放的應用程式的產品與技術。機構可以選擇適用於其解決方案的工具、服務、作業系統、結構與架構，包括 Windows、Linux、Oracle、SharePoint、.NET、Java、PHP 與 Node.js。Azure Government 平台的彈性允許跨機構的共同作業、應用程式開發與整合。

對雲端服務有興趣的美國政府組織，可以信賴 Azure Government 能提供大規模與嚴格的安全性作法，以滿足其不斷成長的需求。







## <a name="Features"></a> Microsoft Azure Government 中目前可用的功能
Azure Government 目前在在「美國政府愛荷華州」(US GOV IOWA) 及「美國政府維吉尼亞州 (US GOV VIRGINIA)」區域有下列可用的服務：

- 虛擬機器
- 雲端服務
- 儲存體
- Active Directory
- 排程器
- 虛擬網路
- SQL Database
- Azure 檔案
- 媒體服務
- 流量管理員
- 服務匯流排

有其他服務可用，並且會持續新增更多服務。如需最新的服務清單，請參閱[區域頁面](http://azure.microsoft.com/regions/#services)，當中將醒目提示每個可用的區域及其服務。

目前，「美國政府愛荷華州」和「美國政府維吉尼亞州」是支援 Azure Government 的資料中心。如需目前的資料中心及可用的服務，請參閱上述區域頁面。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged -->

## <a name="next"></a>接續步驟

如果您有興趣深入了解 Azure Government，請利用下面的連結。

- **<A href="http://azure.com/gov">取得和存取 Azure Government</a>**

- **<A href="/azure-government-developer-guide">Azure Government 開發人員指南</a>**

<!--- **<A href="/azure-government-service-description">Azure Government Service Descriptions</a>**-->




<!-- Images. -->

[1]: ./media/azure-government-developer-guide/publisherguide.png
[2]: ./media/azure-government-overview/azure-gov-overview.jpg

<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines/virtual-machines-windows-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: app-service-web/web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: storage-whatis-account.md

<!---HONumber=July15_HO1-->