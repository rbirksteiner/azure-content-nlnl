<properties
   pageTitle="從 Orchestrator 移轉 Runbook 和整合套件"
   description="描述如何將 Runbook 和整合套件從 System Center Orchestrator 移轉到 Azure 自動化。"
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/22/2015"
   ms.author="bwren" />


# 從 Orchestrator 移轉到 Azure 自動化

[System Center Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) 中的 Runbook 是根據來自專為 Orchestrator 編寫的整合套件的活動，而 Azure 自動化中的 Runbook 則是根據 Windows PowerShell 工作流程。Azure 自動化中的圖形化 Runbook 外觀都類似 Orchestrator Runbook，其活動代表 PowerShell Cmdlet、子 Runbook 和資產。

[System Center Orchestrator 遷移工具組](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all)包含工具，可協助您將 Runbook 從 Orchestrator 轉換成 Azure 自動化。除了轉換 Runbook 本身，您必須將整合模組與用來整合模組與 Windows PowerShell Cmdlet 的活動轉換。

以下是將 Orchestrator Runbook 轉換為 Azure 自動化的基本程序。下列各節將詳細說明這些步驟。

1.  下載 [System Center Orchestrator 遷移工具組](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all)，其中包含的本文所討論的工具和模組。
2.  安裝[標準活動模組](#standard-activities-module) 到 Azure 自動化。這包括已轉換的 Runbook 可能使用的標準 Orchestrator 活動的轉換版本。
2.  針對您的 Runbook 所使用的整合套件，安裝 [System Center Orchestrator 整合模組](#system-center-orchestrator-integration-modules) 至 Azure 自動化。
3.  使用[整合套件轉換器](#integration-pack-converter)轉換自訂和使用協力廠商整合套件，並在 Azure 自動化中安裝。
4.  在 Azure 自動化中的 Orchestrator 手動重新建立全域資產，因為沒有任何自動化的方法可執行此移轉。
5.  使用 [Runbook 轉換器](#runbook-converter-coming-soon) (即將推出) 轉換 Orchestrator Runbook，並在 Azure 自動化中安裝。
6.  在本機的資料中心設定 [混合式 Runbook 背景工作](#hybrid-runbook-worker)，以執行轉換的 Runbook。

## 服務管理自動化

[服務管理自動化](http://technet.microsoft.com/library/dn469260.aspx) (SMA) 會在您的本機資料中心 (例如 Orchestrator) 中儲存並執行 Runbook，而它使用與 Azure 自動化相同的整合模組。可用時，[Runbook 轉換器](#runbook-converter-coming-soon)會將 Orchestrator Runbook 轉換為圖形化 Runbook，不過 SMA 中並不支援。您仍然可以安裝[標準活動模組](#standard-activities-module) 和 [System Center Orchestrator 整合模組](#system-center-orchestrator-integration-modules) 到 SMA，但是您必須手動 [重新編寫您的 Runbook](http://technet.microsoft.com/library/dn469262.aspx)。

## 混合式 Runbook 背景工作

Orchestrator 中的 Runbook 會儲存在資料庫伺服器上，並在 Runbook 伺服器上執行，都是在本機資料中心中。Azure 自動化中的 Runbook 會儲存在 Azure 雲端，也可以在您的本機資料中心使用 [混合式 Runbook 背景工作](automation-hybrid-runbook-worker.md)執行。這是您將通常會執行從 Orchestrator 轉換的 Runbook 的方式，因為它們的設計是用來在本機伺服器上執行的 。

## 整合套件轉換器

整合套件轉換器會將使用 Orchestrator 整合工具組 (OIT) 所建立的整合套件轉換為根據 Windows PowerShell 的整合模組，以便匯入 Azure 自動化或服務管理自動化。

執行整合套件轉換器時，您會看到一個精靈，可讓您選取整合套件 (.oip) 檔案。然後精靈會列出該整合套件所包含的活動，並可讓您選取要移轉的項目。完成精靈時，它會建立模組，其中包含針對原始整合套件中的每個活動相對應的 Cmdlet。


### 參數

整合套件中的活動的任何屬性都會轉換成整合模組中對應的 Cmdlet 的參數。Windows PowerShell Cmdlet 有一組[一般參數](http://technet.microsoft.com/library/hh847884.aspx)，可以搭配所有 Cmdlet 使用。例如，-Verbose 參數會導致 Cmdlet 輸出其工作的詳細資訊。沒有 Cmdlet 可以具有與一般參數同名的參數。如果活動具有與一般參數同名的屬性，精靈會提示您為參數提供另一個名稱。

### 監視器活動

在 Runbook Orchestrator 中監視從[監視活動](http://technet.microsoft.com/library/hh403827.aspx)開始，並持續執行等候特定事件叫用。Azure 自動化不支援監視 Runbook，因此，將不會轉換整合套件中的任何監視活動。相反地，會在監視活動的整合模組中建立預留位置 Cmdlet。此 Cmdlet 不具任何功能，但它可讓您安裝使用它的任何轉換的 Runbook。此 Runbook 將無法在 Azure 自動化中執行，但可加以安裝，讓得使用者可以修改它。

### 無法轉換的整合套件

未使用 OIT 建立的整合套件 (包括 Microsoft 建立的一些) 無法使用這項工具進行轉換。Microsoft 所提供的整合套件已經轉換成整合模組，如此您可在 Azure 自動化或服務管理自動化中加以安裝。


## 標準活動模組

Orchestrator 包含未包含在整合套件中、但會使用許多 Runbook 的一組[標準活動](http://technet.microsoft.com/library/hh403832.aspx)。標準活動模組是包含這些活動的每個對等的 Cmdlet 的整合模組。您必須在匯入使用標準活動的任何轉換的 Runbook 之前，於 Azure 自動化中安裝此整合模組。

除了支援轉換的 Runbook，標準活動模組中的 Cmdlet 也可由熟悉 Orchestrator 的其他人在 Azure 自動化中建置新的 Runbook。雖然所有標準活動的功能都可使用 Cmdlet 執行的，它們的運作方式可能不同。已轉換的標準活動模組中的 Cmdlet 的運作將會與對應的活動相同，並使用相同的參數。這可以幫助現有 Orchestrator Runbook 作者轉換至 Azure 自動化 Runbook。

## System Center Orchestrator 整合模組
Microsoft 提供[整合套件](http://technet.microsoft.com/library/hh295851.aspx)，用於建立 Runbook 以自動化 System Center 元件和其他產品。目前，當您從 [TechNet](http://www.microsoft.com/download/details.aspx?id=39622) 下載一些整合套件時，由於已知問題，無法使用整合套件轉換器加以轉換，該問題將隨著 System Center Orchestrator 遷移工具組 RC 版本的發行而修正。[System Center Orchestrator 整合模組](http://www.microsoft.com/download/details.aspx?id=47324&WT.mc_id=rss_alldownloads_all)包含這些整合套件的已轉換版本，可以在此發行之前在 Azure 自動化和服務管理自動化中匯入。

## Runbook 轉換器 (即將推出)

此工具會將 Orchestrator Runbook 轉換為可以匯入 Azure 自動化的圖形化 Runbook。這項工具的進一步資訊將在可取得時提供。

## 相關文章

- [System Center 2012 - Orchestrator](http://technet.microsoft.com/library/hh237242.aspx)
- [服務管理自動化](https://technet.microsoft.com/library/dn469260.aspx)
- [混合式 Runbook 背景工作](automation-hybrid-runbook-worker.md)
- [Orchestrator 標準活動](http://technet.microsoft.com/library/hh403832.aspx)
 

<!---HONumber=62-->