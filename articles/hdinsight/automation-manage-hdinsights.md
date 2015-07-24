<properties
	pageTitle="使用 Azure 自動化管理 Azure HDInsight"
	description="了解如何使用 Azure 自動化服務來管理 Azure HDInsight。"
	services="HDInsight, automation"
	documentationCenter=""
	authors="elcooper"
	manager="eamono"
	editor=""/>

<tags
	ms.service="HDInsight"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/13/2015"
	ms.author="elcooper"/>



#使用 Azure 自動化管理 Azure HDInsight
本指南將為您介紹 Azure 自動化服務，以及如何使用它來簡化叢集的管理及自動執行 Azure HDInsight 中的一般工作。

## 什麼是 Azure 自動化？
[Azure 自動化](http://azure.microsoft.com/services/automation/) 是一項 Azure 服務，可經由程序自動化簡化雲端管理。使用 Azure 自動化，可以自動執行手動、經常重複、長時間執行及容易出錯的工作，以提高您的組織的可靠性、 效率和時間價值。

Azure 自動化提供高度可靠、高度可用的工作流程執行引擎，可加以調整以符合您的需求。在 Azure 自動化中，可以手動方式、由協力廠商系統或依排定的間隔開始執行程序，讓工作只發生在必要時刻。

降低運作費用，並且讓 IT 和 DevOps 人員藉由排定雲端管理工作在 Azure 自動化上自動執行的排程，而將重心放在增加商務價值。


## Azure 自動化如何協助管理 Azure HDInsight？

使用 [Azure PowerShell 工具](https://msdn.microsoft.com/library/azure/jj156055.aspx)中可用的 [Azure HDInsight Cmdlet](https://msdn.microsoft.com/library/azure/dn479228.aspx)，即可在 Azure 自動化中管理 HDInsight。Azure 自動化會提供立即可用的 Cmdlet，以便您在服務中執行 HDInsight 管理工作。您也可以將 Azure 自動化中的這些 Cmdlet 與其他 Azure 服務的 Cmdlet 搭配，以透過 Azure 服務和協力廠商系統自動執行複雜的工作。

利用 Azure HDInsight Cmdlet，您可以自動執行下列工作：例如在 Linux 或 Windows 上佈建 HDInsight 叢集、調整叢集、管理叢集，以及提交 MapReduce 工作。這些只是您可以在 Azure 自動化中使用 PowerShell 自動執行的許多工作的一部分。


## 後續步驟
現在您已了解 Azure 自動化的基本概念以及如何用來管理 Azure HDInsight，依循此連結可深入了解 Azure 自動化。

* 請參閱 Azure 自動化[快速入門教學課程](../automation-create-runbook-from-samples.md)。

 

<!---HONumber=July15_HO2-->