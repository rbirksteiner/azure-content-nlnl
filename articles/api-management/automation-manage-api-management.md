<properties
	pageTitle="使用 Azure 自動化管理 Azure API 管理"
	description="了解如何使用 Azure 自動化服務來管理 Azure API 管理。"
	services="api-management, automation"
	documentationCenter=""
	authors="csand-msft"
	manager="eamono"
	editor=""/>

<tags
	ms.service="api-management"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/16/2015"
	ms.author="csand"/>



#使用 Azure 自動化管理 Azure API 管理

本指南將為您介紹 Azure 自動化服務，以及如何使用它來簡化 Azure API 管理。

## 什麼是 Azure 自動化？

[Azure 自動化](http://azure.microsoft.com/services/automation/) 是一項 Azure 服務，可經由程序自動化簡化雲端管理。使用 Azure 自動化，可以自動執行手動、經常重複、長時間執行及容易出錯的工作，以提高您的組織的可靠性、 效率和時間價值。

Azure 自動化提供高度可靠、高度可用的工作流程執行引擎，可加以調整以符合您的需求。在 Azure 自動化中，可以手動方式、由協力廠商系統或依排定的間隔開始執行程序，讓工作只發生在必要時刻。

將您的雲端管理工作交由「Azure 自動化」自動執行，以減少營運負擔並釋出 IT 和開發維運人力，使其專注於能夠為企業創造價值的工作上。


## Azure 自動化如何協助管理 Azure API 管理？

您可以在 Azure 自動化中利用 [API 管理 REST API](https://msdn.microsoft.com/library/azure/dn776326.aspx) 來管理「API 管理」。在 Azure 自動化內，您可以利用 REST API 撰寫 PowerShell 工作流程指令碼，以執行許多 API 管理工作。您也可以將 Azure 自動化中的這些 REST API 與其他 Azure 服務的 Cmdlet 搭配，以透過 Azure 服務和協力廠商系統自動執行複雜的工作。


## 後續步驟

了解 Azure 自動化的基本概念以及如何用它來管理 Azure API 管理之後，請參考下列連結以深入了解。

* 請參閱 Azure 自動化[快速入門教學課程](../automation-create-runbook-from-samples.md)。
* 請參閱 [#Azure API 管理 REST API 的 PowerShell 模組](https://alexandrebrisebois.wordpress.com/2014/08/17/powershell-module-for-the-azure-api-management-rest-apis/)社群部落格文章。
 

<!---HONumber=62-->