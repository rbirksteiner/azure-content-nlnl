<properties 
   pageTitle="備份 Azure 自動化"
   description="描述如何備份自動化帳戶的內容，這樣就可以在刪除自動化帳戶之後保留其內容。"
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/13/2015"
   ms.author="bwren" />

# 備份 Azure 自動化

在 Microsoft Azure 中刪除自動化帳戶時，會刪除帳戶中的所有物件，包括 Runbook、模組、設定、工作和資產。刪除帳戶之後，就無法復原物件。您可以使用下列資訊，在刪除之前備份您的自動化帳戶的內容。

## Runbook

您可以使用 Azure 管理入口網站或 Windows PowerShell 中的 [Get-AzureAutomationRunbookDefinition](https://msdn.microsoft.com/library/dn690269.aspx) Cmdlet，將您的 Runbook 匯出為指令碼檔案。可以將這些指令碼檔案匯入到另一個自動化帳戶，如[建立或匯入 Runbook](https://msdn.microsoft.com/library/dn643637.aspx) 中所述。


## 整合模組

您無法從 Azure 自動化匯出整合模組。您必須確定它們可供在自動化帳戶外部使用。

## Assets

您無法從 Azure 自動化匯出 [資產](https://msdn.microsoft.com/library/dn939988.aspx)。使用 Azure 管理入口網站，您必須記下變數、認證、憑證、連接和排程的詳細資料。然後必須手動建立您匯入到另一個自動化的 Runbook 所使用的任何資產。

您可以使用 [Azure Cmdlet](https://msdn.microsoft.com/library/dn690262.aspx) 來擷取未加密的資產的詳細資料並加以儲存供日後參考，或在另一個自動化帳戶中建立對等的資產。

您無法使用 Cmdlet 擷取加密的變數的值或認證的密碼欄位。如果您不知道這些值，則可以從 Runbook 使用 [Get-AutomationVariable](https://msdn.microsoft.com/library/dn940012.aspx) 和 [Get-AutomationPSCredential](https://msdn.microsoft.com/library/dn940015.aspx) 活動來擷取它們。

您無法從 Azure 自動化匯出憑證。您必須確定 Azure 外部有任何憑證可供使用。

## 相關文章

- [建立或匯入 Runbook](https://msdn.microsoft.com/library/dn643637.aspx)
- [自動化資產](https://msdn.microsoft.com/library/dn939988.aspx)
- [Azure Cmdlet](https://msdn.microsoft.com/library/dn690262.aspx) 

<!---HONumber=62-->