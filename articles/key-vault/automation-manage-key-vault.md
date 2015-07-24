<properties
	pageTitle="使用 Azure 自動化管理 Azure 金鑰保存庫"
	description="了解如何使用 Azure 自動化服務來管理 Azure 金鑰保存庫。"
	services="Key-Vault, automation"
	documentationCenter=""
	authors="csand-msft"
	manager="eamono"
	editor=""/>

<tags
	ms.service="key-vault"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/16/2015"
	ms.author="csand"/>



#使用 Azure 自動化管理 Azure 金鑰保存庫

本指南將為您介紹 Azure 自動化服務，以及如何使用它來簡化管理 Azure 金鑰保存庫中的金鑰和密碼。

## 什麼是 Azure 自動化？

[Azure 自動化](http://azure.microsoft.com/services/automation/) 是一項 Azure 服務，可經由程序自動化簡化雲端管理。使用 Azure 自動化，可以自動執行手動、經常重複、長時間執行及容易出錯的工作，以提高您的組織的可靠性、 效率和時間價值。

Azure 自動化提供高度可靠、高度可用的工作流程執行引擎，可加以調整以符合您的需求。在 Azure 自動化中，可以手動方式、由協力廠商系統或依排定的間隔開始執行程序，讓工作只發生在必要時刻。

將您的雲端管理工作交由「Azure 自動化」自動執行，以減少營運負擔並釋出 IT 和開發維運人力，使其專注於能夠為企業創造價值的工作上。


## Azure 自動化如何協助管理 Azure 金鑰保存庫？

您可以使用 [Azure PowerShell 工具](https://msdn.microsoft.com/library/azure/jj156055.aspx)中可用的 [Azure 金鑰保存庫 Cmdlet](https://msdn.microsoft.com/library/azure/dn868052.aspx)，在 Azure 自動化中管理金鑰保存庫。Azure 自動化提供這些立即可用的 Cmdlet，讓您在服務內執行許多金鑰保存庫管理工作。您也可以將 Azure 自動化中的這些 Cmdlet 與其他 Azure 服務的 Cmdlet 搭配，以透過 Azure 服務和協力廠商系統自動執行複雜的工作。

Azure 金鑰保存庫 Cmdlet 可讓您執行這些工作：建立或匯入金鑰、建立或更新密碼、更新金鑰的屬性、取得金鑰或密碼，或是刪除金鑰或密碼。


## 後續步驟

了解 Azure 自動化的基本概念以及如何用它來管理 Azure 金鑰保存庫之後，請參考下列連結，以深入了解 Azure 自動化。

* 請參閱 Azure 自動化[快速入門教學課程](../automation-create-runbook-from-samples.md)。
* 請參閱 [Azure 金鑰保存庫 PowerShell 指令碼](https://gallery.technet.microsoft.com/scriptcenter/Azure-Key-Vault-Powershell-1349b091)。
 

<!---HONumber=July15_HO2-->