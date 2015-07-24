<properties 
 pageTitle="雲端服務的大小" 
 description="列出 Azure 雲端服務 Web 和背景工作角色不同的大小。" 
 services="cloud-services" 
 documentationCenter="" 
 authors="Thraka" 
 manager="timlt" 
 editor=""/>
<tags 
 ms.service="cloud-services" 
 ms.devlang="na" 
 ms.topic="article" 
 ms.tgt_pltfrm="na" 
 ms.workload="tbd"
 ms.date="06/04/2015" 
 ms.author="adegeo"/>
 
# 雲端服務的大小

本主題描述雲端服務角色執行個體 (Web 角色和背景工作角色)的可用大小和選項。同時也提供計劃使用這些資源時，需注意的部署考量。

Azure 虛擬機器和雲端服務是 Azure 所提供數種計算資源類型的其中兩種。如需說明，請參閱＜[計算 Azure 所提供的裝載選項](fundamentals-application-models.md)＞。

> [AZURE.NOTE]若要查看相關的 Azure 限制，請參閱 ＜[Azure 訂用帳戶和服務限制、配額及條件約束](../azure-subscription-service-limits.md)＞

## 用於 Web 和背景工作角色執行個體的大小

下列考量可協助您決定大小：

* 執行個體現在可以設定為使用 D 系列 VM。這些是為了執行要求更高計算能力和暫存磁碟效能的應用程式所設計。D 系列 VM 提供更快的處理器、較高的記憶體至核心比率和使用固態硬碟 (SSD) 的暫存磁碟。如需詳細資訊，請參閱 Azure 部落格的公告，[新 D 系列的虛擬機器大小](http://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/)。  

* 因系統需求，Web 角色和背景工作角色比 Azure 虛擬機器需要更多的暫存磁碟空間。系統檔案保留 4 GB 的空間供 Windows 分頁檔和 2 GB 的空間供 Windows 傾印檔案。

* 作業系統磁碟包含 Windows 客體 OS，並且包含 Program Files 資料夾 (包括透過啟動工作完成的安裝，除非您指定另一個磁碟)、登錄變更、System32 資料夾和 .NET Framework。

* 本機資源磁碟包含 Azure 記錄檔和設定檔、Azure 診斷 (其中包括 IIS 記錄檔)，以及您所定義的任何本機儲存體資源。

* 應用程式磁碟是您解壓縮 .cspkg 的地方，並包含您的網站、二進位檔、角色主機處理序、啟動工作、web.config 等等。

* A8/A10 和 A9/A11 虛擬機器大小有相同的容量。A8 和 A9 虛擬機器執行個體包含額外的網路介面卡，其會連線到遠端直接記憶體存取 (RDMA) 網路，以利在虛擬機器之間進行快速通訊。A8 和 A9 執行個體是專為執行期間，節點之間需要常數和低延遲通訊的高效能計算應用程式所設計，例如，使用訊息傳遞介面 (MPI) 的應用程式。A10 和 A11 虛擬機器執行個體不包含額外的網路介面卡。A10 和 A11 執行個體專為不需要常數和低度延遲節點的高效能計算應用程式設計，也就是所謂的參數式或窘迫平行應用程式。

|大小|CPU<br>核心|記憶體|磁碟大小|
|---|---|---|---|
|特小型|1|768 MB|作業系統 = 客體 OS 大小<br/>本機資源 = 19 GB<br/>應用程式 = 大約 1.5 GB|
|小型|1|1.75 GB|作業系統 = 客體 OS 大小<br/>本機資源 = 224 GB<br/>應用程式 = 大約 1.5 GB|
|中型|2|3.5 GB|作業系統 = 客體 OS 大小<br/>本機資源 = 489 GB<br/>應用程式 = 大約 1.5 GB|
|大型|4|7 GB|作業系統 = 客體 OS 大小<br/>本機資源 = 999 GB<br/>應用程式 = 大約 1.5 GB|
|特大型|8|14 GB|作業系統 = 客體 OS 大小<br/>本機資源 = 2,039 GB<br/>應用程式 = 大約 1.5 GB|
|A5|2|14 GB|作業系統 = 客體 OS 大小<br/>本機資源 = 489 GB<br/>應用程式 = 大約 1.5 GB|
|A6|4|28 GB|作業系統 = 客體 OS 大小<br/>本機資源 = 999 GB<br/>應用程式 = 大約 1.5 GB|
|A7|8|56 GB|作業系統 = 客體 OS 大小<br/>本機資源 = 2,039 GB<br/>應用程式 = 大約 1.5 GB
|A8|8|56 GB|OS = 客體 OS 大小<br/>本機資源 = 1.77 TB<br/>應用程式 = 大約 1.5 GB<blockquote> 注意：如需使用此大小的資訊與考量，請參閱<a href="http://go.microsoft.com/fwlink/p/?linkid=328042">關於 A8、A9、A10 和 A11 計算密集型執行個體</a>。</blockquote>|
|A9|16|112 GB|OS = 客體 OS 大小<br/>本機資源 = 1.77 TB<br/>應用程式 = 大約 1.5 GB<blockquote> 注意：如需使用此大小的資訊與考量，請參閱<a href="http://go.microsoft.com/fwlink/p/?linkid=328042">關於 A8、A9、A10 和 A11 計算密集型執行個體</a>。</blockquote>|
|A10|8|56 GB|OS = 客體 OS 大小<br/>本機資源 = 1.77 TB<br/>應用程式 = 大約 1.5 GB<blockquote> 注意：如需使用此大小的資訊與考量，請參閱<a href="http://go.microsoft.com/fwlink/p/?linkid=328042">關於 A8、A9、A10 和 A11 計算密集型執行個體</a>。</blockquote>|
|A11|16|112 GB|OS = 客體 OS 大小<br/>本機資源 = 1.77 TB<br/>應用程式 = 大約 1.5 GB<blockquote> 注意：如需使用此大小的資訊與考量，請參閱<a href="http://go.microsoft.com/fwlink/p/?linkid=328042">關於 A8、A9、A10 和 A11 計算密集型執行個體</a>。</blockquote>|
|標準_D1|1|3.5 GB|作業系統 = 客體 OS 大小<br/>本機資源 = 50 GB<br/>應用程式 = 大約 1.5 GB|
|標準_D2|2|7 GB|作業系統 = 客體 OS 大小<br/>本機資源 = 100 GB<br/>應用程式 = 大約 1.5 GB|
|標準_D3|4|14 GB|作業系統 = 客體 OS 大小<br/>本機資源 = 200 GB<br/>應用程式 = 大約 1.5 GB|
|標準_D4|8|28 GB|作業系統 = 客體 OS 大小<br/>本機資源 = 400 GB<br/>應用程式 = 大約 1.5 GB|
|標準_D11|2|14 GB|作業系統 = 客體 OS 大小<br/>本機資源 = 100 GB<br/>應用程式 = 大約 1.5 GB|
|標準_D12|4|28 GB|作業系統 = 客體 OS 大小<br/>本機資源 = 200 GB<br/>應用程式 = 大約 1.5 GB|
|標準_D13|8|56 GB|作業系統 = 客體 OS 大小<br/>本機資源 = 400 GB<br/>應用程式 = 大約 1.5 GB|
|標準_D14|16|112 GB|作業系統 = 客體 OS 大小<br/>本機資源 = 800 GB<br/>應用程式 = 大約 1.5 GB|

## 後續步驟

[設定 Azure 的雲端服務](https://msdn.microsoft.com/library/hh124108) [設定雲端服務大小](https://msdn.microsoft.com/library/ee814754)

<!---HONumber=62-->