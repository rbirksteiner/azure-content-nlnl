<properties 
   pageTitle="Azure 客體作業系統可支援性和淘汰原則指南 |Azure" 
   description="提供關於雲端服務使用之 Azure 客體作業系統的 Microsoft 支援事項資訊。" 
   services="cloud-services" 
   documentationCenter="na" 
   authors="Thraka" 
   manager="timlt" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd" 
   ms.date="05/19/2015"
   ms.author="adegeo"/>

# Azure 客體作業系統可支援性和淘汰原則
本頁面中的資訊涉及雲端服務背景工作和 Web 角色 (PaaS) 適用的 Azure 客體作業系統 ([客體 OS](https://msdn.microsoft.com/library/azure/ff729422.aspx))。這些資訊不適用於虛擬機器 (IaaS)。

Microsoft 已發佈[客體 OS 支援原則](http://support.microsoft.com/gp/azure-cloud-lifecycle-faq)。您正在閱讀的頁面描述原則的實施方式。

原則包括

1. Microsoft 將支援至少兩個**最新系列的客體 OS**。當某個系列遭到淘汰時，客戶有 12 個月的時間 (從官方淘汰日期開始計算) 更新為較新的受支援客體作業系統系列。
2. Microsoft 將支援**至少兩個最新版本的支援客體 OS系列**。 
3. Microsoft 將支援**至少兩個最新版本的 Azure SDK**。當某個版本的 SDK 遭到淘汰時，客戶有 12 個月的時間 (從官方淘汰日期開始計算) 更新為較新版本。 

Microsoft 有時候會支援兩個以上的系列或版本。正式的客體 OS 支援資訊會公告於「[Azure 客體 OS 版本與 SDK 相容性比較表](cloud-services-guestos-update-matrix.md)」中。


## 當客體作業系統系列或版本遭到淘汰時 


Microsoft 會在新的正式版本 Windows Server 作業系統發行後，擇日推出新的客體 OS **系列**。每當推出新客體作業系統時，Microsoft 都會淘汰最舊的客體作業系統系列。

Microsoft 大約會每月推出新的客體 OS **版本**，以納入最新的 MSRC 更新。因每月定期更新之故，每個客體作業系統版本通常會在發行的 60 天後遭到停用。如此可保持每個系列至少有兩個客體作業系統版本可供使用。

### 客體作業系統系列淘汰期間的程序 


從 Microsoft 公佈淘汰資訊到正式移除服務中最舊的系列，客戶有 12 個月的「轉換」期。Microsoft 會斟酌是否延長這段轉換時間。更新資訊會張貼在「[Azure 客體 OS 版本與 SDK 相容性比較表](cloud-services-guestos-update-matrix.md)」。

漸進式淘汰程序將會在轉換期的 6 個月前開始。在這段時間：

1. Microsoft 將通知客戶有關淘汰的資訊。 
2. 新版本的 Azure SDK 將不支援淘汰的客體作業系統系列。
3. 淘汰的系列將不允許新部署和重新部署的雲端服務。

Microsoft 將繼續推出併入最新 MSRC 更新的新客體作業系統版本，直到轉換期的最後一天為止 (稱為「到期日」)。屆時，Azure SLA 將不支援任何依然處於運作狀態的雲端服務。Microsoft 可決定是否在該日期後強制升級、刪除或停止這些服務。



### 客體作業系統版本淘汰期間的程序 
如果客戶將客體作業系統設定為自動更新，他們永遠不需要擔心該如何處置客體作業系統版本的問題。他們一律能使用最新版本的客體作業系統。

Microsoft 會在每個月發行客體作業系統。由於定期發行頻繁之故，每個版本都有固定的週期。

到了週期的第 60 天，版本即會遭到「*停用*」。「停用」表示 Microsoft 會從 Azure 管理入口網站將該版本移除。您也無法再從 CSCFG 組態檔加以設定。現有部署仍可繼續執行，但新部署和現有部署的程式碼與組態更新將遭到禁止。

經過一段時間後，客體作業系統版本將會「*到期*」，屆時所有仍執行該版本的安裝將受迫升級，以及設定為在未來自動更新客體作業系統。到期係以批次進行，因此從停用到到期之間的這段期間不盡相同。

Microsoft 可自行決定是否延長該期間，以緩和客戶轉換時的不便。所有的更新資訊都會發佈於「[Azure 客體 OS 版本與 SDK 相容性比較表](cloud-services-guestos-update-matrix.md)」。



### 淘汰期間的通知 

* **系列淘汰** <br>Microsoft 會透過部落格文章和管理入口網站通知公告。對於仍在使用遭淘汰之客體作業系統系列的客戶，Microsoft 會透過直接通訊 (電子郵件、入口網站訊息、撥打電話) 通知其指派的服務管理員。所有變更資訊都將張貼在這個頁面和頁面開頭所列的 RSS 摘要。 


* **版本淘汰** <br>所有的變更資訊都將張貼在此頁面和頁面開頭所列的 RSS 摘要中，包括版本、停用日及到期日。如果遭停用的客體作業系統版本或系列之上仍有運作中的部署，服務管理員將會收到電子郵件。這些電子郵件的發送時間不盡相同。雖然發送電子郵件的時間不屬於官方 SLA，不過 Microsoft 通常會在停用之前至少一個月發出。


## 常見問題集

**如何降低移轉的影響？**

您應該使用最新的客體作業系統系列來設計雲端服務。

1. 儘早規劃新系列的移轉。 
2. 設定暫時性的測試部署，以測試在新系列上運作的雲端服務。 
3. 將[客體 OS 版本](https://msdn.microsoft.com/library/azure/gg433101.aspx)設為「Automatic」([.CSCFG](https://msdn.microsoft.com/library/azure/gg456324.aspx) 檔案中的 osVersion=*)，新客體 OS 版本的移轉作業即能自動執行。

**如果 Web 應用程式需要與 OS 更深入的整合，該怎麼辦？**

如果 Web 應用程式架構與基礎作業系統之間需要更深入的相依性，您可以使用「[啟動工作](https://msdn.microsoft.com/library/windowsazure/gg456327.aspx)」之類的平台支援功能或我們未來可能會推出的其他擴充性機制。您也可以使用 [Azure 虛擬機器](http://azure.microsoft.com/documentation/scenarios/virtual-machines/) (IaaS – 基礎結構即服務)，只不過您需要自行負責維護基礎作業系統。
 

<!---HONumber=62-->