<properties 
	pageTitle="了解 Azure SQL Database 和 Azure VM 中的 SQL Server" 
	description="了解 Azure 虛擬機器中的 Azure SQL Database 和 SQL Server檢閱常見的商業動因，以判斷哪一種 SQL 技術最符合應用程式需求。" 
	services="sql-database, virtual-machines" 
	documentationCenter="" 
	authors="Selcin" 
	manager="jeffreyg" 
	editor="tysonn"/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="vm-windows-sql-server" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/23/2015" 
	ms.author="selcint"/>

# 了解 Azure SQL Database 和 Azure VM 中的 SQL Server

Microsoft Azure 會提供裝載 SQL Server 的兩個選項：[**Azure SQL Database**] 和 [**Azure 虛擬機器中的 SQL Server**]。在本文中，我們先來看看每個選項會如何配合 Microsoft 資料平台的大環境，然後再根據激發您選擇的商務需求，繼續更深入的討論。無論您是以節省成本為優先考量，或將精簡管理視為首要條件，本文可依據每種方法所提供的與您最重視的商務需求進行比較，以協助您決定最適合的方法。

- [Microsoft 資料平台](#platform)
- [仔細看看 Azure SQL Database 和 Azure VM 中的 SQL Server](#close)	
- [選擇 Azure SQL Database 或 Azure VM 中的 SQL Server 時的商業動機](#business)	
	- [成本](#cost)
		- [計費和授權基本概念](#billing)	
		- [計算應用程式總成本](#appcost)	
	- [系統管理](#admin)	
	- [服務等級協定 (SLA)](#sla)	
	- [產品上市時間](#market)	
- [摘要](#summary)	
- [通知](#ack)	
- [其他資源](#resources)	


##<a name="platform"></a>Microsoft 的資料平台

在 Azure 與內部部署 SQL Server 資料庫的任何討論中，您要了解的第一件事情是您可以兩者都用。Microsoft 資料平台會利用 SQL Server 技術，使其可在跨內部部署實體機器、私人雲端環境、協力廠商代管的私人雲端環境，和公用雲端中使用。這可讓您透過內部和雲端代管部署的結合來滿足獨特的多樣化業務需求，並同時在這些環境中使用相同的伺服器產品、開發工具和專業知識組合。

   ![][1]

如圖所示，每個產品可依您在基礎結構 (在 X 軸) 中所擁有的管理層級，並依資料庫層級彙總和自動化 (在 Y 軸) 所達到的成本效益程度等特性加以分類。

設計應用程式時，有四個基本選項可用來主控屬於應用程式一部分的 SQL Server：

- 非虛擬化實體機器上的 SQL Server 
- 內部部署虛擬機器中的 SQL Server (私人雲端)
- Azure 虛擬機器中的 SQL Server (公用雲端)
- Azure SQL Database (公用雲端)

在下列章節中，我們將了解最後兩個基本選項：Azure SQL Database 和 Azure VM 中的 SQL Server。此外，我們將探討常見的商業動機，以判斷哪一個選項最符合應用程式需求。

##<a name="close"></a>仔細看看 Azure SQL Database 和 Azure VM 中的 SQL Server

[**Microsoft Azure SQL Database (Azure SQL Database)**] 是關聯式資料庫即服務，屬於「*平台即服務 (PaaS)*」產業類別。Azure SQL Database 會建立在 Microsoft 所擁有、代管及維護的標準化硬體和軟體上。有了 SQL 資料庫，您可以使用內建的特色與功能在服務上直接開發。使用 SQL 資料庫時，您可以隨用隨付，並使用擴大或向外延展選項以取得更強大的功能。

[**Azure 虛擬機器 (VM) 中的 SQL Server**] 屬於「*基礎結構即服務 (IaaS)*」產業類別，可讓您在雲端的虛擬機器中執行 SQL Server。類似於 Azure SQL Database，它會建立在 Microsoft 所擁有、代管及維護的標準化硬體上。使用 VM 中的 SQL Server 時，您可以在 Azure 中使用自己的 SQL Server 授權，或使用 Azure 入口網站中其中一個預先設定的 SQL Server 映像。

一般而言，這兩個 SQL 選項適合用於不同的用途：

- [**Azure SQL Database**] 已經過最佳化，可將佈建和管理許多資料庫的整體成本降到最低。由於您無需管理任何虛擬機器、作業系統或資料庫軟體 (包括升級、高可用性和備份)，它會將進行中的系統管理成本降到最低。一般而言，SQL Database 可能會大幅增加由單一 IT 或開發資源管理的資料庫數目。
- [**Azure VM 中執行的 SQL Server**] 已經過最佳化，可在混合式案例中將現有的內部部署 SQL Server 應用程式延伸到 Azure，或在移轉案例或開發/測試案例中將現有的應用程式部署到 Azure。混合式案例的一個範例是[透過 Azure 虛擬網路](http://msdn.microsoft.com/library/azure/jj156007.aspx)，在 Azure 中保留一份次要資料庫複本。有了 Azure VM 中的 SQL server，您即擁有專用 SQL Server 執行個體和雲端架構 VM 的完整系統管理權限。當組織擁有可用來維護虛擬機器的 IT 資源時，此選項會是最佳選擇。有了 VM 中的 SQL Server，您可以建立高度自訂的系統，以解決應用程式的特定效能和可用性需求。

下表摘要說明 Azure SQL Database 和 Azure VM 中 SQL Server 的主要特性：

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle"></th>
   <th align="left" valign="middle">Azure SQL Database</th>
   <th align="left" valign="middle">Azure VM 中 SQL Server</th>
   
</tr>
<tr>
   <td valign="middle"><p><b>適用於</b></p></td>
   <td valign="middle">
          <ul>
          <li type=round>開發與行銷階段有時間限制的新雲端式設計應用程式。
          <li type=round>需有內建自動高可用性、災害復原解決方案及升級機制的應用程式。
          <li type=round>如果您有數百或數千個資料庫，但您不想要管理基礎作業系統、硬體和組態設定。
         <li type=round>使用向外延展模式的應用程式。
         <li type=round>大小高達 500 GB 的資料庫。
         <li type=round>建置軟體即服務應用程式。
         
  </ul>
</td>
   <td valign="middle">
      <ul>
      <li type=round>需要幾乎無需進行任何變更即可快速移轉至雲端的現有應用程式。
      <li type=round>需要透過安全通道，從 Azure 存取內部部署資源 (例如 Active Directory) 的 SQL Server 應用程式。
      <li type=round>如果您需要包含完整系統管理權限的自訂 IT 環境。
      <li type=round>當您不想購買內部部署 SQL Server 非生產硬體時的快速開發和測試案例。
      <li type=round>使用 <a href="http://msdn.microsoft.com/library/jj919148.aspx">Azure 儲存體上的備份</a>或 <a href="http://msdn.microsoft.com/library/azure/jj870962.aspx">Azure VM 中的 AlwaysOn 複本</a>的內部部署 SQL Server 應用程式災害復原。
      <li type=round>大於 1 TB 的大型資料庫。
      </ul></td>
   
</tr>
<tr>
   <td valign="middle"><p><b>資源</b></p></td>
   <td valign="middle">
       <ul>
       <li type=round>您不想要運用 IT 資源來支援和維護基礎結構。
       <li type=round>您想要將焦點放在應用程式層上。
       </ul></td>
   <td valign="middle"><ul><li type=round>您擁有可支援和維護的 IT 資源。</ul></td>
   
</tr>
<tr>
   <td valign="middle"><p><b>擁有權的總成本</b></p></td>
   <td valign="middle"><ul><li type=round>排除硬體成本。降低管理成本。</ul></td>
   <td valign="middle"><ul><li type=round>排除硬體成本。</ul></td>
   
</tr>
<tr>
   <td valign="middle"><p><b>業務持續性</b></p></td>
   <td valign="middle"><ul><li type=round>除了內建的容錯基礎結構功能以外，Azure SQL Database 還提供可增加業務持續性的功能，例如時間點還原時間、地理還原和地理複寫。如需詳細資訊，請參閱 <a href="http://msdn.microsoft.com/library/azure/hh852669.aspx">Azure SQL Database 業務持續性</a>。</ul></td>
   <td valign="middle"><ul><li type=round>Azure VM 中的 SQL Server 可讓您設定高可用性和災害復原解決方案，以滿足您的資料庫特定需求。因此，您可以有已針對您的應用程式進行高度最佳化的系統。您可以視需要自我測試並執行容錯移轉。如需詳細資訊，請參閱 <a href="http://msdn.microsoft.com/library/azure/jj870962.aspx">Azure 虛擬機器中的 SQL Server 高可用性和災害復原</a>。</ul></td>
   
</tr>
<tr>
   <td valign="middle"><p><b>混合式雲端</b></p></td>
   <td valign="middle"><ul><li type=round>您的內部部署應用程式可以存取 Azure SQL Database 中的資料。</ul></td>
   <td valign="middle"><ul>
      <li type=round>有了 Azure VN 中的 SQL Server，您的應用程式可以部分在雲端中執行和部分在內部部署中執行。比方說，您可以透過 <a href="http://msdn.microsoft.com/library/azure/gg433091.aspx">Azure 網絡服務</a>，將內部部署網路和 Active Directory 網路延伸到雲端。此外，您可以使用 <a href="http://msdn.microsoft.com/library/dn385720.aspx">Azure 功能中的 SQL Server 資料檔案</a>，將內部部署資料檔案儲存在 Azure 儲存體中。如需詳細資訊，請參閱 <a href="http://msdn.microsoft.com/library/dn606154.aspx">SQL Server 2014 混合式雲端簡介</a>。
      <li type=round>支援使用 <a href="http://msdn.microsoft.com/library/jj919148.aspx">Azure 儲存體上的備份</a>或 <a href="http://msdn.microsoft.com/library/azure/jj870962.aspx">Azure VM 中的 AlwaysOn 複本</a>的內部部署 SQL Server 災害復原。
      </ul></td>
   
</tr>
</table>

##<a name="business"></a>選擇 Azure SQL Database 或 Azure VM 中的 SQL Server 時的商業動機

###<a name="cost"></a>成本

無論您是現金不足的新公司，或是在預算有限的情況下運作的已成立公司內部小組，有限資金經常會是決定主控資料庫方式的主要關鍵。在本節中，我們將首先了解 Azure 中，下列這兩個關聯式資料庫選項的相關計費和授權基本概念：Azure SQL Database 和 Azure VM 中的 SQL Server。然後，我們將了解應該如何計算應用程式總成本。

####<a name="billing"></a>計費和授權基本概念

[**Azure SQL Database**] 會以服務 (不含授權) 的形式銷售給客戶，而 Azure VM 中的 SQL Server 則需要傳統的 SQL Server 授權。

目前，您可以在數個服務層中使用 [**Azure SQL Database**]。若是基本、標準和高階服務層，我們會根據您所選擇的服務層和效能層級，以固定費率向您收取每小時的費用。基本、標準和高階服務層的設計目的在於提供多個效能層級的可預測效能，以滿足應用程式的尖峰需求。您可以在服務層和效能層級之間進行變更，以滿足應用程式的不同輸送量需求。如需目前支援的服務層最新資訊，請參閱 [Azure SQL Database 服務層 (版本)](http://msdn.microsoft.com/library/azure/dn741340.aspx)。

有了 [**Azure SQL Database**]，世界各地資料中心內的 Microsoft Azure 便會自動設定、修補和升級資料庫軟體。因此，您就能降低管理成本。此外，它[內建的備份](http://msdn.microsoft.com/library/azure/jj650016.aspx)功能可協助您達到有效節省成本，尤其是當您擁有為數眾多的資料庫時效果更為顯著。使用 Azure SQL Database 時，您不必支付針對 Azure SQL Database 執行的個別查詢或傳入/傳出網際網路流量費用。如果您的資料庫具有高交易量，且必須支援許多並行使用者，建議您使用高階 (而不是基本或標準) 服務層。

有了 [**Azure VM 中的 SQL Server**]，您可以利用傳統的 SQL Server 授權。您可以使用平台所提供的 SQL Server 映像，或在 Azure 中使用您的 SQL Server 授權。使用 SQL Server 平台所提供的映像時，成本會取決於您所選擇的 VM 大小以及 SQL Server 版本。基本上，您須支付 SQL Server 的每分鐘授權成本、Windows Server 每分鐘授權和 Azure 儲存體成本。每分鐘計費選項可讓您隨時使用 SQL Server，而無需購買完整的 SQL Server 授權。如果在 Azure 中使用自己的 SQL Server 授權，您僅需支付 Azure 計算和儲存體成本。如需詳細資訊，請參閱 [Azure 上透過軟體保證的授權機動性](http://azure.microsoft.com/pricing/license-mobility/)。

####<a name="appcost"></a>計算應用程式總成本

當您開始使用雲端平台時，執行應用程式的成本主要包括開發和管理成本，以及公用雲端平台所需的服務成本。

以下是針對在 Azure SQL Database 和 Azure VM 中的 SQL Server 上執行之應用程式的詳細成本計算：

**使用 Azure SQL Database 時：**

*應用程式的總成本 = 降到最低的系統管理成本 + 軟體開發成本 + Azure SQL Database 服務成本*

**使用 Azure VM 中的 SQL Server 時：**

*應用程式的總成本 = 降到最低的軟體開發/修改成本 + 系統管理成本 + SQL Server 與 Windows Server 授權成本 + Azure 儲存體成本*

**重要事項：**目前，Azure SQL Database 不支援 SQL Server 的所有功能。如需詳細的比較資訊，請參閱 [Azure SQL Database 方針和限制](http://msdn.microsoft.com/library/azure/ff394102.aspx)。當您要將現有的資料庫移至 Azure SQL Database 時請留意這個資訊，因為在資料庫重新設計上您可能需要一些額外預算。Azure SQL Database 是 Microsoft 平台即服務產品。當您將現有的內部部署 SQL Server 應用程式移轉至 Azure SQL Database 時，建議您更新應用程式以充分利用平台即服務產品的所有好處。例如，開始在應用程式層上使用 [Azure 網站](http://azure.microsoft.com/documentation/services/websites/)或 [Azure 雲端服務](http://azure.microsoft.com/services/cloud-services/)，以增加成本利益。此外，請針對不同的 Azure SQL Database 服務層驗證您的應用程式，並檢查哪一個最符合您的應用程式需求。此程序可協助您達到更佳的效能結果並將成本降到最低。如需詳細資訊，請參閱 [Azure SQL Database 服務層和效能層級](http://msdn.microsoft.com/library/azure/dn741336.aspx)。

如需詳細的成本估計，請使用 [Azure 定價計算程式](http://azure.microsoft.com/pricing/calculator/)。

如需定價的詳細資訊，請參閱下列資源：

- [Azure SQL Database 定價詳細資料](http://azure.microsoft.com/pricing/details/sql-database/) 
- [虛擬機器定價詳細資料](http://azure.microsoft.com/pricing/details/virtual-machines/)
- [Azure VM 中的 SQL Server - 定價詳細資料](http://azure.microsoft.com/pricing/details/virtual-machines/#sql-server)
- [Azure VM 中的 Windows Server - 定價詳細資料](http://azure.microsoft.com/pricing/details/virtual-machines/#windows) 

###<a name="admin"></a>系統管理

如果您手上已經有許多工作，或許您並不期望採用伺服器和資料庫系統管理。對許多企業來說，決定使用雲端服務的關鍵在於降低系統管理複雜度的能力。有了 [**Azure SQL Database**]，Microsoft 可以管理實體硬體 (例如硬碟、伺服器和儲存體)、自動複製所有資料以提供高可用性、設定及升級資料庫軟體、管理負載平衡，並在伺服器故障時進行透明容錯移轉。您可以繼續管理您的 Azure SQL Database 執行個體，但無需控制基礎 SQL Server 執行個體和 Azure 平台的實體資源。例如，您可以管理資料庫和登入、執行索引微調，以及最佳化查詢，但無法管理系統資料表和檔案群組管理。如需詳細資訊，請參閱 [Azure SQL Database 方針和限制](http://msdn.microsoft.com/library/ff394102.aspx)。

相反地，您可能有內部專業人員，並想保持只由機器本身控制資料庫位置。有了 [**Azure VM 中執行的 SQL Server**]，您可以完全掌控作業系統和 SQL Server 執行個體組態。有了 VM，您可以決定何時更新/升級作業系統與資料庫軟體，以及何時安裝任何其他軟體 (例如防毒和備份工具)。此外，您還可以控制 VM 的大小、磁碟的數目及其儲存體組態。例如，Azure 可讓您視需要變更正在執行的 VM 大小。如需相關資料，請參閱 [Azure 的虛擬機器和雲端服務大小](http://msdn.microsoft.com/library/azure/dn197896.aspx)。

###<a name="sla"></a>服務等級協定 (SLA)

對於某些人而言，達到服務等級協定 (SLA) 的正常運作時間義務是首要任務。在本節中，我們將瞭解 SLA 對每個資料庫主控選項所代表的意義。

在 [**Azure SQL Database**] 中，若是基本、標準和高階服務層，Microsoft 提供 99.99% 的可用性 SLA。請注意，可用性 SLA 提供連接到資料庫的能力。換句話說，它是個資料庫層級 SLA。如需 SLA 的最新資訊，請參閱[服務等級協定](http://azure.microsoft.com/support/legal/sla/)。如需 Azure SQL Database 服務層 (版本) 和支援的業務持續性計劃最新資訊，請參閱 [Azure SQL Database 服務層](http://msdn.microsoft.com/library/dn741340.aspx)。

在 [**Azure 託管的虛擬機器**] 中，Microsoft 提供 99.95% 的可用性 SLA，這個可用性適用於 VM，不適用於 VM 內部執行的程序 (例如 SQL Server)。[VM SLA](http://www.microsoft.com/download/details.aspx?id=38427) 要求您在可用性集合中至少主控兩個 VM。有了此類組態，Azure 會保證至少其中一個 VM 將會 99.95%的時間可用。如需 VM 內的資料庫高可用性 (HA)，您應在 SQL Server 中設定其中一個支援的高可用性選項，例如 AlwaysOn 可用性群組。請注意，在 Azure 中設定 AlwaysOn 需要一些手動設定和管理，而且您需要額外支付所操作的每個次要資料庫。


###<a name="market"></a>產品上市時間

當開發人員生產力和快速產品上市時間為關鍵所在時，[**Azure SQL Database**] 是雲端式設計應用程式的理想解決方案。有了程式設計 DBA 類似功能，此選項非常適合雲端架構設計人員和開發人員，因為它會降低管理基礎作業系統和資料庫的需求。它可協助開發人員了解與設定資料庫相關工作。例如，您可以使用 [REST API](http://msdn.microsoft.com/library/azure/dn505719.aspx) 和 [PowerShell Cmdlet](http://msdn.microsoft.com/library/azure/dn546726.aspx) 來自動化及管理數千個資料庫的管理作業。透過在雲端的彈性調整，您可以輕易地將重點放在應用程式層，並更快速地將應用程式上市。

如果現有和新的應用程式需要存取與控制 SQL Server 執行個體的所有功能，並想要依現狀將現有的內部部署應用程式和資料庫移轉至雲端，[**Azure VM 中執行的 SQL Server**] 會是理想選項。由於您無需變更簡報、應用程式和資料層，您會在重新架構現有解決方案時節省時間和預算。相反地，您可以將重點放在將您的所有方案套件移轉至 VM，並進行 Azure 平台所需的某些效能最佳化作業。如需相關資訊，請參閱 [Azure 虛擬機器中的 SQL Server 效能最佳作法](http://msdn.microsoft.com/library/azure/dn133149.aspx)。

##<a name="summary"></a>摘要

在本文中，我們探討了：Azure SQL Database 和 Azure VM 中的 SQL Server。此外，我們還討論了可能會影響選擇決策制定的常見商業動因。

下列是建議摘要，可用來考慮使用其中一個選項的時機：

如果是下列情形，請選擇 [**Azure SQL Database**]：

- 您打算建置全新的雲端架構應用程式，或您想要將現有的 SQL Server 資料庫移轉至 Azure，而且您的資料庫未使用 Azure SQL Database 中不支援的功能。如需詳細資訊，請參閱 [Azure SQL Database Transact-SQL 參考](http://msdn.microsoft.com/library/azure/ee336281.aspx)。此方法提供全面管理雲端服務的優點，並可確保快速的產品上市時間。

- 您想要讓 Microsoft 在資料庫上執行一般管理作業，因而資料庫需要更強大的可用性 SLA。這種方法可以減少系統管理成本，並同時提供資料庫的保證可用性。

如果是下列情形，請選擇 [**Azure VM 中的 SQL Server**]：

- 您有現有的內部部署應用程式，而且想要停止維護自己的硬體或考慮混合式解決方案。這種方法可讓您更快速地存取高資料庫容量，並透過安全通道將內部部署應用程式連接到雲端。

- 您有現有的 IT 資源、需要 SQL Server 的完整系統管理權限，並需要與內部部署 SQL server 的完全相容 (例如，Azure SQL Database 不提供某些功能)。這種方法可讓您彈性地執行大部分的應用程式，將開發或修改現有應用程式的成本降至最低。此外，它還提供 VM、作業系統和資料庫組態的完整控制權。

##<a name="ack"></a>通知

本文出自 Microsoft 雲端和企業內容服務群組，是藉助 Microsoft 社群中許多人合力而成的。

**作者：**Selcin Turkarslan

**技術參與者：**Conor Cunningham

**技術校閱者：**Joanne Marone (Hodgins)、Karthika Raman、Lindsey Allen、Lori Clark、Luis Carlos Vargas Herring、Nosheen Syed Wajahatulla Hussain、Pravin Mittal、Shawn Bice、Silvano Coriani、Tony Petrossian、Tracy Daugherty。

**編輯校閱者：**Heidi Steen、Maggie Sparkman。

謝謝各位讓這篇文章得已出刊！

##<a name="resources"></a>其他資源 

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">資源</th>
   <th align="left" valign="middle">說明</th>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/azure/ee336279.aspx">MSDN：Azure SQL Database</a></p>
<p><a href="http://msdn.microsoft.com/library/azure/jj823132.aspx">MSDN：Azure 虛擬機器中的 SQL Server</a></p>

<p><a href="http://azure.microsoft.com/services/sql-database/">Azure.com：Azure SQL Database</a></p></td>
   <td valign="middle">文件庫文件的連結。</td>   
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/dn574746.aspx">AAzure 虛擬機器中的 SQL Server 應用程式模式和開發策略</p></td>
   <td valign="middle">本文將討論適用於 Azure VM 中的 SQL Server 的最常見應用程式模式，以及包括 Azure SQL Database 的混合式案例。</td>   
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/hh680934(v=PandP.50).aspx">Microsoft Enterprise 程式庫暫時性錯誤處理應用程式區塊</p></td>
   <td valign="middle">此程式庫可讓開發人員以更彈性的方式在 Azure SQL Database 上執行其應用程式，方法是新增強固的暫時性錯誤處理邏輯。暫時性錯誤是因為某個暫時情況 (如網路連線問題或服務無法使用) 而發生的錯誤。由於 Azure SQL Database 是多租用戶服務，因此處理此類錯誤以將任何應用程式停機時間降至最低十分重要。</td>   
</tr>
</table>

<!--Image references-->
[1]: ./media/data-management-azure-sql-database-and-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png
 

<!---HONumber=July15_HO2-->