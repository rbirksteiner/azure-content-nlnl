<properties
   pageTitle="Azure 自動化中的 Runbook 執行"
   description="描述如何處理 Azure 自動化中的 Runbook 的詳細資料。"
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
   ms.date="04/28/2015"
   ms.author="bwren" />

# Azure 自動化中的 Runbook 執行


當您在 Azure 自動化中啟動 Runbook 時即會建立一個工作。工作是 Runbook 的單一執行個體。會指派 Azure 自動化背景工作，以執行每項工作。雖然背景工作是由多個 Azure 帳戶共用，來自不同自動化帳戶的工作會彼此隔離。您對將服務您的要求工作的背景工作沒有控制權。單一 Runbook 一次可以執行多個工作。當您在 Azure 入口網站中檢視 Runbook 的清單時，它會列出已針對每一個 Runbook 啟動的上一個工作的狀態。您可以檢視每一個 Runbook工作的清單，以追蹤每個 Runbook 的狀態。如需不同的工作狀態的描述，請參閱[工作狀態](automation-viewing-the-status-of-a-runbook-job.md#job-statuses)。

![工作狀態](./media/automation-runbook-execution/job-statuses.png)


藉由對您的 Azure 訂用帳戶進行連接，您的工作將可以存取您的 Azure 資源。如果可從公用雲端存取這些資源，他們將只可以存取您的資料中心中的資源。

## 權限

Azure 自動化中的 Runbook 通常會需要存取 Azure 訂用帳戶中的資源。[使用 Azure Active Directory 向 Azure 驗證](http://aka.ms/runbookauthor/authentication)說明如何設定 Azure Active Directory 和 Azure 自動化中的[認證]()，用於向 Azure 資源進行驗證。本主題也包含有關使用 [Add-AzureAccount Cmdlet ](http://aka.ms/runbookauthor/azurecmdlets/addazureaccount) 存取 Runbook 中您建立的 Azure 資源。請參閱您匯入的個別 Runbook 的文件集，以取得其安全性需求。

## 公平共用

為了在雲端中的所有 Runbook 之間共用資源，Azure 自動化會在任何工作已執行了 3 小時之後暫時卸除工作，然後從上一個[檢查點](http://aka.ms/runbookauthor/checkpoints)重新啟動。在此期間，工作的顯示狀態為「執行中，正在等候資源」。如果 Runbook 沒有檢查點，或在卸載工作之前工作未到達第一個檢查點，則會從頭重新啟動。

如果 Runbook 從相同檢查點或從 Runbook 的開頭重新啟動連續三次，它將被終止，狀態為「失敗，正在等候資源」。這是為了防止 Runbook 無限期地執行而不會完成，因為它們在未重新卸載的情況下，無法進入下一個檢查點。在此情況下，您會收到下列失敗例外狀況。

工作無法繼續執行，因為它是從相同的檢查點重複收回。請確定您的 Runbook 在未保存其狀態的情況下，不會長時間執行作業。

建立 Runbook 時，您應該確定在兩個檢查點之間執行的任何活動的時間不會超過 3 小時。您可能需要將檢查點加入至您的 Runbook，以確保它不會到達此 3 小時的限制。您也可能需要中斷長時間執行的作業。例如，您的 Runbook 可能在大型 SQL 資料庫上執行重新索引。如果此單一作業未在公平共用的限制內完成，則將會卸載工作並且從頭開始重新啟動。在此情況下，您應該將重新索引作業分成多個步驟，例如一次重新索引一個資料表，然後在每個作業之後插入檢查點，讓工作可以在最後一個作業完成後繼續。

## 相關文章

- [在 Azure 自動化中啟動 Runbook](automation-starting-a-runbook)
- [在 Azure 自動化中檢視 Runbook 工作的狀態](automation-viewing-the-status-of-a-runbook-job)
 

<!---HONumber=62-->