<properties
 pageTitle="虛擬機器大小"
 description="列出虛擬機器的不同大小及其容量。"
 services="virtual-machines"
 documentationCenter=""
 authors="KBDAzure"
 manager="timlt"
 editor=""/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="infrastructure-services"
 ms.date="07/02/2015"
 ms.author="kathydav"/>

# 虛擬機器的大小

## 概觀

本文說明以虛擬機器為基礎之計算資源的可用大小和選項，而您可以使用這類資源來執行應用程式和工作負載。同時也提供計劃使用這些資源時需注意的部署考量。

Azure 虛擬機器和雲端服務是 Azure 所提供數種計算資源類型的其中兩種。如需說明，請參閱＜[計算 Azure 所提供的裝載選項](http://go.microsoft.com/fwlink/p/?LinkID=311926)＞。

>[AZURE.NOTE]若要查看相關的 Azure 限制，請參閱 [Azure 訂用帳戶和服務限制、配額及條件約束](../azure-subscription-service-limits.md)

## 規劃考量

虛擬機器可用於這兩個層級：基本和標準。這兩種類型提供各種大小的選擇，但是基本層級不會提供一些可用於標準層級的功能，例如負載平衡和自動調整。標準層級的大小是由 A 系列、D 系列、DS 系列和 G 系列所組成。

*   D 系列 VM 是為了執行要求更高計算能力和暫存磁碟效能的應用程式所設計。D 系列 VM 提供更快的處理器、較高的記憶體與核心比率，以及適用於暫存磁碟的固態硬碟 (SSD)。如需詳細資訊，請參閱 Azure 部落格的公告，[新 D 系列的虛擬機器大小](http://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/)。  

*   DS 系列 VM 可以使用高階儲存體，針對使用大量 I/O 的工作負載提供高效能、低延遲的儲存體。它會使用固態硬碟 (SSD) 來裝載虛擬機器的磁碟，並提供本機 SSD 磁碟快取。高階儲存體可在 Preview 版本中取得，並且可在特定區域中使用。如需詳細資訊，請參閱[高階儲存體：Azure 虛擬機器工作負載適用的高效能儲存體](../storage-premium-storage-preview-portal.md)。

*   G 系列 VM 提供最大的大小和最佳效能，並且可在具有 Intel Xeon E5 V3 系列處理器的主機上執行。

虛擬機器的大小會影響定價。大小也會影響虛擬機器的處理、記憶體和儲存體容量。儲存體成本會分別根據儲存體帳戶中使用的頁面來計算。如需詳細資訊，請參閱[虛擬機器定價詳細資料](http://go.microsoft.com/fwlink/p/?LinkId=398570)和 [Azure 儲存體定價](http://azure.microsoft.com/pricing/details/storage/)。如需 Azure 虛擬機器中所使用磁碟和儲存體的詳細資訊，請參閱[關於 Azure 中的虛擬機器磁碟](https://msdn.microsoft.com/library/jj672979)。

下列考量可協助您決定大小：

*   只有使用 Azure SDK 1.3 版或更新版本，才能使用 A0\\Basic_A0 大小。  

*   A1\\Basic_A1 是建議針對生產工作負載採用的最小大小。

*   使用 SQL Server Enterprise Edition 時，請選取具有 4 個或 8 個 CPU 核心的虛擬機器。

*   Azure 資料中心的某些實體主機可能不支援較大的虛擬機器大小，例如 A5-A11。因此，您可能會在將現有的虛擬機器調整為新的大小、在 2013 年 4 月 16 日之前建立的虛擬網路中建立新的虛擬機器，或將新的虛擬機器加入現有雲端服務時，看到錯誤訊息：「無法設定虛擬機器 <machine name>」或「無法建立虛擬機器 <machine name>」 。請參閱支援論壇上的主題[錯誤：「無法設定虛擬機器」](http://social.msdn.microsoft.com/Forums/WAVirtualMachinesforWindows/thread/9693f56c-fcd3-4d42-850e-5e3b56c7d6be)，以查看每個部署案例的因應措施。

*   A8/A10 和 A9/A11 虛擬機器大小具有相同的容量。A8 和 A9 虛擬機器執行個體包含額外的網路介面卡，其會連線到遠端直接記憶體存取 (RDMA) 網路，以利在虛擬機器之間進行快速通訊。A8 和 A9 執行個體是專為執行期間，節點之間需要常數和低延遲通訊的高效能計算應用程式所設計，例如，使用訊息傳遞介面 (MPI) 的應用程式。A10 和 A11 虛擬機器執行個體不包含額外的網路介面卡。A10 和 A11 執行個體專為不需要常數和低度延遲節點的高效能計算應用程式設計，也就是所謂的參數式或窘迫平行應用程式。

## 一般限制

下表顯示針對以服務管理工具建立的 VM，適用所有大小的 VM 限制。


[AZURE.INCLUDE [azure-virtual-machines-limits](../../includes/azure-virtual-machines-limits.md)]

下表顯示針對以資源管理員建立的 VM，適用所有大小的 VM 限制。

[AZURE.INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

## 大小資料表

下表顯示每個執行個體所提供的大小和容量。

>[AZURE.NOTE]儲存體容量是使用 1024^3 位元組當成 GB 的度量單位來表示。這有時稱為 gibibyte 或基底 2 定義。比較使用不同基底系統的大小時，請記住，基底 2 的大小可能會顯示為小於基底 10，但是對於任何特定的大小 (例如 1 GB)，基底 2 的系統可提供比基底 10 系統更大的容量，因為 1024^3 大於 1000^3。

## 基本層級

|大小 - 管理入口網站\\Cmdlet 和 API|CPU 核心|記憶體|最大的磁碟大小 - 虛擬機器|最大的資料磁碟 (每個 1023 GB)|最大IOPS (每個磁碟 300)|
|---|---|---|---|---|---|
|A0\\Basic_A0|1|768 MB|<p>作業系統 = 1023 GB</p><p>暫存 = 20 GB</p>|1|1x300|
|A1\\Basic_A1|1|1.75 GB|<p>作業系統 = 1023 GB</p><p>暫存 = 40 GB</p>|2|2x300|
|A2\\Basic_A2|2|3.5 GB|<p>作業系統 = 1023 GB</p><p>暫存 = 60 GB</p>|4|4x300|
|A3\\Basic_A3|4|7 GB|<p>作業系統 = 1023 GB</p><p>暫存 = 120 GB</p>|8|8x300|
|A4\\Basic_A4|8|14 GB|<p>作業系統 = 1023 GB</p><p>暫存 = 240 GB</p>|16|16x300|

## 標準層級
### A 系列和 D 系列

|大小 - 管理入口網站\\Cmdlet 和 API|CPU 核心|記憶體|最大的磁碟大小 - 虛擬機器|最大的資料磁碟 (每個 1023 GB)|最大IOPS (每個磁碟 500)|
|---|---|---|---|---|---|
|A0\\ 超小型|1|768 MB|<p>作業系統 = 1023 GB</p><p>暫存 = 20 GB</p>|1|1x500|
|A1\\小型|1|1.75 GB|<p>作業系統 = 1023 GB</p><p>暫存 = 70 GB</p>|2|2x500|
|A2\\中型|2|3.5 GB|<p>作業系統 = 1023 GB</p><p>暫存 = 135 GB</p>|4|4x500|
|A3\\大型|4|7 GB|<p>作業系統 = 1023 GB</p><p>暫存 = 285 GB</p>|8|8x500|
|A4\\超大型|8|14 GB|<p>作業系統 = 1023 GB</p><p>暫存 = 605 GB</p>|16|16x500|
|A5(相同)|2|14 GB|<p>作業系統 = 1023 GB</p><p>暫存 = 135 GB</p>|4|4X500|
|A6(相同)|4|28 GB|<p>作業系統 = 1023 GB</p><p>暫存 = 285 GB</p>|8|8x500|
|A7(相同)|8|56 GB|<p>作業系統 = 1023 GB</p><p>暫存 = 605 GB</p>|16|16x500|
|A8(相同)|8|56 GB|<p><p>OS = 1023 GB</p><p>暫存 = 382 GB</p><blockquote><p>注意：如需使用此大小的資訊與考量，請參閱<a href="http://go.microsoft.com/fwlink/p/?linkid=328042">關於 A8、A9、A10 與 A11 計算密集型執行個體</a>.</p></blockquote>|16|16x500|
|A9(相同)|16|112 GB|<p><p>OS = 1023 GB</p><p>暫存 = 382 GB</p><blockquote><p>注意：如需使用此大小的資訊與考量，請參閱<a href="http://go.microsoft.com/fwlink/p/?linkid=328042">關於 A8、A9、A10 與 A11 計算密集型執行個體</a>.</p></blockquote>|16|16x500|
|A10(相同)|8|56 GB|<p><p>OS = 1023 GB</p><p>暫存 = 382 GB</p><blockquote><p>注意：如需使用此大小的資訊與考量，請參閱<a href="http://go.microsoft.com/fwlink/p/?linkid=328042">關於 A8、A9、A10 與 A11 計算密集型執行個體</a>.</p></blockquote>|16|16x500|
|A11(相同)|16|112 GB|<p><p>OS = 1023 GB</p><p>暫存 = 382 GB</p><blockquote><p>注意：如需使用此大小的資訊與考量，請參閱<a href="http://go.microsoft.com/fwlink/p/?linkid=328042">關於 A8、A9、A10 與 A11 計算密集型執行個體</a>.</p></blockquote>|16|16x500|
|Standard_D1(相同)|1|3.5 GB|<p>作業系統 = 1023 GB</p><p>暫存 (SSD) = 50 GB</p>|2|2x500|
|Standard_D2(相同)|2|7 GB|<p>作業系統 = 1023 GB</p><p>暫存 (SSD) = 100 GB</p>|4|4x500|
|Standard_D3(相同)|4|14 GB|<p>作業系統 = 1023 GB</p><p>暫存 (SSD) = 200 GB</p>|8|8x500|
|Standard_D4(相同)|8|28 GB|<p>作業系統 = 1023 GB</p><p>暫存 (SSD) = 400 GB</p>|16|16x500|
|Standard_D11(相同)|2|14 GB|<p>作業系統 = 1023 GB</p><p>暫存 (SSD) = 100 GB</p>|4|4x500|
|Standard_D12(相同)|4|28 GB|<p>作業系統 = 1023 GB</p><p>暫存 (SSD) = 200 GB</p>|8|8x500|
|Standard_D13(相同)|8|56 GB|<p>作業系統 = 1023 GB</p><p>暫存 (SSD) = 400 GB</p>|16|16x500|
|Standard_D14(相同)|16|112 GB|<p>作業系統 = 1023 GB</p><p>暫存 (SSD) = 800 GB</p>|32|32x500|


### 標準層級 - DS 系列*

|大小 - 管理入口網站\\Cmdlet 和 API|CPU 核心|記憶體|最大的磁碟大小 - 虛擬機器|最大的資料磁碟 (每個 1023 GB)|快取大小 (GB)|最大的磁碟 IOPS 和頻寬|
|---|---|---|---|---|---|---|
|Standard_DS1(相同)|1|3.5|<p>作業系統 = 1023 GB</p><p>本機 SSD 磁碟 = 7 GB</p>|2|43|<p>3,200</p><p>每秒 32 MB</p>|
|Standard_DS2(相同)|2|7|<p>作業系統 = 1023 GB</p><p>本機 SSD 磁碟 = 14 GB</p>|4|86|<p>6,400</p><p>每秒 64 MB</p>|
|Standard_DS3(相同)|4|14|<p>作業系統 = 1023 GB</p><p>本機 SSD 磁碟 = 28 GB</p>|8|172|<p>12,800</p><p>每秒 128 MB</p>|
|Standard_DS4(相同)|8|28|<p>作業系統 = 1023 GB</p><p>本機 SSD 磁碟 = 56 GB</p>|16|344|<p>25,600</p><p>每秒 256 MB</p>|
|Standard_DS11(相同)|2|14|<p>作業系統 = 1023 GB</p><p>本機 SSD 磁碟 = 28 GB</p>|4|72|<p>6,400</p><p>每秒 64 MB</p>|
|Standard_DS12(相同)|4|28|<p>作業系統 = 1023 GB</p><p>本機 SSD 磁碟 = 56 GB</p>|8|144|<p>12,800</p><p>每秒 128 MB</p>|
|Standard_DS13(相同)|8|56|<p>作業系統 = 1023 GB</p><p>本機 SSD 磁碟 = 112 GB</p>|16|288|<p>25,600</p><p>每秒 256 MB</p>|
|Standard_DS14(相同)|16|112|<p>作業系統 = 1023 GB</p><p>本機 SSD 磁碟 = 224 GB</p>|32|576|<p>50,000</p><p>每秒 512 MB</p>|

*DS 系列 VM 可能的最大每秒輸入/輸出作業 (IOPS) 和輸送量 (頻寬) 會受到磁碟大小所影響。如需詳細資訊，請參閱[高階儲存體：Azure 虛擬機器工作負載適用的高效能儲存體](../storage-premium-storage-preview-portal.md)。

### 標準層級 - G 系列

|大小 - 管理入口網站\\Cmdlet 和 API|CPU 核心|記憶體|最大的磁碟大小 - 虛擬機器|最大的資料磁碟 (每個 1023 GB)|最大IOPS (每個磁碟 500)|
|---|---|---|---|---|---|
|Standard_G1(相同)|2|28 GB|<p>作業系統 = 1023 GB</p><p>本機 SSD 磁碟 = 384 GB</p>|4|4 x 500|
|Standard_G2(相同)|4|56 GB|<p>作業系統 = 1023 GB</p><p>本機 SSD 磁碟 = 768 GB</p>|8|8 x 500|
|Standard_G3(相同)|8|112 GB|<p>作業系統 = 1023 GB</p><p>本機 SSD 磁碟 = 1,536 GB</p>|16|16 x 500|
|Standard_G4(相同)|16|224 GB|<p>作業系統 = 1023 GB</p><p>本機 SSD 磁碟 = 3,072 GB</p>|32|32 x 500|
|Standard_G5(相同)|32|448 GB|<p>作業系統 = 1023 GB</p><p>本機 SSD 磁碟 = 6,144 GB</p>|64|<p>64 x 500</p>|

### 另請參閱

[Azure 訂用帳戶和服務限制、配額與限制](../azure-subscription-service-limits.md)

＜[關於 A8、A9、A10 和 A11 計算密集型執行個體](virtual-machines-a8-a9-a10-a11-specs.md)＞

<!---HONumber=July15_HO2-->