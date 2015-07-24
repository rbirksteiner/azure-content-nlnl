<properties 
	pageTitle="Azure 虛擬機器上的 SQL Server" 
	description="本文章簡介 Azure IaaS 虛擬機器上架設的 SQL Server。本文章也提供深度內容的連結。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="rothja" 
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services" 
	ms.date="04/17/2015"
	ms.author="jroth"/>

# Azure 虛擬機器上的 SQL Server

## 概觀
您可以利用各種設定來架設 [Azure 虛擬機器上的 SQL Server][sqlvmlanding]，範圍包括單一資料庫伺服器到使用 AlwaysOn 可用性群組和 Azure 虛擬網路的多電腦設定。

> [AZURE.NOTE]在 Azure VM 上執行 SQL Server，是用來將關聯式資料儲存到 Azure 的選項之一。您也可以使用 Azure SQL Database 服務。如需詳細資訊，請參閱[了解 Azure SQL Database 和 Azure VM 中的 SQL Server][sqldbcompared]。
 
## 在單一 VM 上部署 SQL Server 執行個體
當您[使用 Azure 入口網站建立 Azure 虛擬機器][createvmportal]或採用自動化方法後，您可以安裝任何您有授權的 SQL Server 執行個體。不過，您必須採取其他步驟，在 SQL Server 電腦和其他用戶端電腦之間[設定連線][setupconnectivity]。
 
您也可以從組件庫許多不同 SQL Server 虛擬機器映像中，安裝其中一個。這些映像包含的 SQL Server 授權，是以 VM 為計價單位。如需詳細資訊，請參閱[在 Azure 上佈建 SQL Server 虛擬機器][provisionsqlvm]。

## 部署具有多個 VM 的高可用性設定
您可以使用 SQL Server AlwaysOn 可用性群組，就可以實現 SQL Server 的高可用性。這牽涉到虛擬網路中多個 Azure VM。Azure 預覽入口網站有一個範本，己經有您需要的設定。如需詳細資訊，請參閱 [Microsoft Azure 入口網站組件庫提供的 SQL Server AlwaysOn][sqlalwaysonportal]。或者，您也可以[手動設定 AlwaysOn 可用性群組][sqlalwaysonmanual]。如需其他高可用性注意事項，請參閱 [Azure 虛擬機器中的 SQL Server 高可用性和災害復原][sqlhadr]。

## 在 Azure 中執行商業智慧、資料倉儲和 OLTP 工作負載   
您可以在 Azure 虛擬機器執行常見的 SQL Server 工作負載。SQL Server 在映像庫中有數個可用的最佳化虛擬機器。這些包括[商務智慧][sqlbi]、[資料倉儲][sqldw]和 [OLTP][sqloltp] 的映像。

## 遷移資料
要將資料遷移到執行 SQL Server 的 Azure VM，有幾個方法辦得到。第一次佈建 SQL Server 虛擬機器時，可以使用 Azure 入口網站、PowerShell 自動化或 SQL Server Management Studio 的部署精靈。最佳化的 SQL Server 映像會在其計價模式中包含授權，不過您也可以使用自己的授權來安裝 SQL Server。若要遷移您的資料，有數個選項可選，例如使用部署精靈或將資料磁碟遷移到目標虛擬機器。如需詳細資訊，請參閱[準備遷移到 Azure 虛擬機器中的 SQL Server][migratesql]。

## 備份與還原
至於內部部署資料庫，Azure 可以充當次要資料中心，負責儲存 SQL Server 備份檔案。[SQL Server 備份至 URL][backupurl] 會將 Azure 的備份檔案儲存至 Azure Blob 儲存體。[SQL Server 託管備份][managedbackup]可以讓您在 Azure 中安排和保留備份。這些服務可以搭配內部部署 SQL Server 執行個體或 Azure VM 上執行的 SQL Server。Azure VM 也可以利用 SQL Server 專用的[自動備份][autobackup]和[自動化修補][autopatching]功能。如需詳細資訊，請參閱 [Azure 虛擬機器的 SQL Server 管理工作][managementtasks]。

## 其他資源：
[Azure VM 中的 SQL Server][sqlmsdnlanding]

[開始使用 Azure 虛擬機器中的 SQL Server][sqlvmgetstarted]

[Azure 虛擬機器中的 SQL Server 效能最佳作法][sqlperf]

[Azure 虛擬機器中的 SQL Server 安全注意事項][sqlsecurity]

[Azure 虛擬機器中 SQL Server 適用的技術文件][technicalarticles]

  [sqlvmlanding]: http://azure.microsoft.com/services/virtual-machines/sql-server/
  [sqldbcompared]: http://azure.microsoft.com/documentation/articles/data-management-azure-sql-database-and-sql-server-iaas
  [createvmportal]: http://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial/
  [setupconnectivity]: https://msdn.microsoft.com/library/azure/dn133152.aspx
  [provisionsqlvm]: http://azure.microsoft.com/documentation/articles/virtual-machines-provision-sql-server/
  [sqlalwaysonportal]: http://go.microsoft.com/fwlink/?LinkId=526941
  [sqlalwaysonmanual]: https://msdn.microsoft.com/library/azure/dn249504.aspx
  [sqlhadr]: https://msdn.microsoft.com/library/azure/jj870962.aspx
  [sqlbi]: https://msdn.microsoft.com/library/azure/jj992719.aspx
  [sqldw]: https://msdn.microsoft.com/library/azure/dn387396.aspx
  [sqloltp]: https://msdn.microsoft.com/library/azure/eb0188e2-5569-48ff-b92c-1f6c0bf79620#about
  [migratesql]: https://msdn.microsoft.com/library/azure/dn133142.aspx
  [backupurl]: https://msdn.microsoft.com/library/dn435916(v=sql.120).aspx
  [managedbackup]: https://msdn.microsoft.com/library/dn449496.aspx
  [autobackup]: https://msdn.microsoft.com/library/azure/dn906091.aspx
  [autopatching]: https://msdn.microsoft.com/library/azure/dn961166.aspx
  [managementtasks]: https://msdn.microsoft.com/library/azure/dn906886.aspx
  [sqlmsdnlanding]: https://msdn.microsoft.com/library/azure/jj823132.aspx
  [sqlvmgetstarted]: https://msdn.microsoft.com/library/azure/dn133151.aspx
  [sqlperf]: https://msdn.microsoft.com/library/azure/dn133149.aspx
  [sqlsecurity]: https://msdn.microsoft.com/library/azure/dn133147.aspx
  [technicalarticles]: https://msdn.microsoft.com/library/azure/dn248435.aspx

<!---HONumber=July15_HO2-->