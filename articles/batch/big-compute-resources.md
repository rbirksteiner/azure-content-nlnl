<properties
   pageTitle="在 Azure 中的大量計算：批次和高效能計算 (HPC) 的技術資源"
   description="本文列出技術資源，用以協助您在 Azure 中執行您的大規模平行、批次和 HPC 工作負載。"
   services="batch, cloud-services, virtual-machines"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="big-compute"
   ms.date="06/24/2015"
   ms.author="danlep"/>

# 在 Azure 中的大量計算：批次和高效能計算 (HPC) 的技術資源
這是技術資源的指南，將幫助您在 Azure 中執行您的大規模平行、批次和 HPC 工作負載。使用各種 Azure 服務擴充您現有的批次或 HPC 工作負載至 Azure 雲端，或在 Azure 中建置新的大量計算解決方案。

## 方案選項

了解在 Azure 中的大量計算選項，並為您的工作負載和商務需求選擇正確的方法。

* [概觀](https://msdn.microsoft.com/library/azure/dn482130.aspx)

* [影片：Azure 和 HPC 在雲端的大量計算](http://azure.microsoft.com/documentation/videos/teched-europe-2014-big-compute-in-the-cloud-with-high-performance-computing-on-azure/)


## Azure 批次

[批次](http://azure.microsoft.com/services/batch/)是一種平台服務，可以輕鬆地啟用您應用程式的雲端功能，並無需設定和管理叢集及工作排程器即可執行工作。使用 SDK 透過各種不同語言、接移資料至 Azure和建立工作執行管線，將用戶端應用程式與 Azure 批次整合。

* 申請 [預覽](http://azure.microsoft.com/services/preview/)

* [說明文件](http://azure.microsoft.com/documentation/services/batch/)

* [API 參考資料](https://msdn.microsoft.com/library/azure/dn820177.aspx)

* [批次論壇](https://social.msdn.microsoft.com/Forums/home?forum=azurebatch)

* [教學課程：開始使用適用於 .NET 的 Azure 批次程式庫](batch-dotnet-get-started.md)

* [批次影片](https://azure.microsoft.com/documentation/videos/index/?services=batch)

## HPC 叢集解決方案

部署或擴充您現有的 Windows 或 Linux HPC 叢集至 Azure，以執行您的計算密集工作負載。

### Microsoft HPC Pack

[HPC Pack](https://technet.microsoft.com/library/cc514029) 是 Microsoft 的免費叢集管理程式，以及內部部署、混合式和以雲端架構的 HPC 工作排程解決方案。

* [使用 HPC Pack 將量擴大到 Azure](https://technet.microsoft.com/library/gg481749.aspx)

* [Azure VM 中的 HPC Pack](https://msdn.microsoft.com/library/azure/dn518135.aspx)

* [教學課程：在 Azure 中使用 HPC Pack 設定混合式叢集](../cloud-services-setup-hybrid-hpcpack-cluster.md)

* [Windows HPC 論壇](https://social.microsoft.com/Forums/home?category=windowshpc)

### Linux 叢集解決方案
使用這些 Azure 資源管理員範本來部署 Linux HPC 叢集。

* [加速 SLURM 叢集](http://azure.microsoft.com/documentation/templates/slurm/)和[部落格文章](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx)

* [加速扭力叢集](http://azure.microsoft.com/documentation/templates/torque-cluster/)

## Microsoft MPI

[Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) (MS-MPI) 是為了在 Windows 平台上開發及執行平行應用程式，Microsoft 實作的訊息傳遞介面標準。


* [下載 MS-MPI](http://go.microsoft.com/FWLink/p/?LinkID=389556)

* [MS-MPI 參考](https://msdn.microsoft.com/library/dn473458.aspx)

* [MPI 論壇](https://social.microsoft.com/Forums/home?forum=windowshpcmpi)


## 計算密集型執行個體

Azure 提供[各種大小](../virtual-machines/virtual-machines-size-specs.md)，包括計算密集的 [A8、A9、A10 和 A11 執行個體](../virtual-machines/virtual-machines-a8-a9-a10-a11-specs.md)，以執行您的批次和 HPC 工作負載。

* [A8 和 A9 執行個體：HPC Pack 快速入門](https://msdn.microsoft.com/library/azure/dn594431.aspx)

* [在 A8 和 A9 執行個體上執行 MPI 應用程式](https://msdn.microsoft.com/library/azure/dn592104.aspx)

## 架構藍圖

* [大規模計算 - 金融服務](http://go.microsoft.com/fwlink/?LinkId=536378) (PDF) 會示範風險管理、報告和模擬如何在雲端有可操作性，並協調大規模計算和資料分析。

## 範例和示範

* [Azure 的批次程式碼範例](https://github.com/Azure/azure-batch-samples)

* [批次應用程式 Blender 範例](https://github.com/Azure/azure-batch-apps-blender)和[部落格文章](http://azure.microsoft.com/blog/2015/01/26/blender-on-azure-batch/)

## 相關的 Azure 服務

* [Data Factory](http://azure.microsoft.com/documentation/services/data-factory/)

* [機器學習服務](http://azure.microsoft.com/documentation/services/machine-learning/)

* [HDInsight](http://azure.microsoft.com/documentation/services/hdinsight/)

* [虛擬機器](http://azure.microsoft.com/documentation/services/virtual-machines/)

* [雲端服務](http://azure.microsoft.com/documentation/services/cloud-services/)

* [媒體服務](http://azure.microsoft.com/documentation/services/media-services/)



## 後續步驟

* 最新宣告請參閱 [Microsoft HPC 和批次的小組部落格](http://blogs.technet.com/b/windowshpc/)以及[Azure 部落格](http://azure.microsoft.com/blog/tag/hpc/)。
* 另請參閱[批次的新功能](http://azure.microsoft.com/updates/?service=batch)或訂閱 [RSS 摘要](http://azure.microsoft.com/updates/feed/?service=batch)。

<!---HONumber=62-->