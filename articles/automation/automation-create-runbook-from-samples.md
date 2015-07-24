<properties
	pageTitle="開始使用 Azure 自動化"
	description="了解如何在 Azure 中匯入和執行自動化工作。"
	services="automation"
	documentationCenter=""
	authors="bwren"
	manager="stevenka"
	editor=""/>

<tags
	ms.service="automation"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="05/19/2015"
	ms.author="bwren"/>


# 開始使用 Azure 自動化

## 什麼是 Azure 自動化？

Microsoft Azure 自動化可讓使用者將執行於雲端環境中的手動、長時間執行、易發生錯誤且重複性高的工作自動化。您可以使用 Runbook 來建立、監視、管理及部署 Azure 環境中的資源；而 Runbook 是以 Windows PowerShell 工作流程為基礎。在本指南中，您將逐步完成執行簡單範例 Runbook 的教學課程。然後，您將尋找用來探索更多服務進階功能的資源。

## 教學課程
本教學課程將逐步引導您建立自動化帳戶、將範例 "Hello World" Runbook 匯入 Azure 自動化中、執行該 Runbook 並檢視其輸出。

若要完成此教學課程，您需要 Azure 訂用帳戶。如果您沒有這類帳戶，可以<a href="/pricing/member-offers/msdn-benefits-details/" target="_blank">啟用自己的 MSDN 訂戶權益</a> 或<a href="/pricing/free-trial/" target="_blank">申請免費試用</a>。

[AZURE.INCLUDE [automation-note-authentication](../../includes/automation-note-authentication.md)]

## <a name="automationaccount"></a>建立自動化帳戶

自動化帳戶是 Azure 自動化資源的容器：可供您劃分您的環境或進一步組織您的工作流程。如需詳細資訊，請參閱自動化程式庫中的[自動化帳戶](http://aka.ms/runbookauthor/azure/automationaccounts)。如果您已經建立自動化帳戶，則可以略過此步驟。

1.	登入 [Azure 管理入口網站](http://manage.windowsazure.com)。

2.	在管理入口網站中，按一下 [Create an Automation Account]。

	![Create Account](./media/automation-create-runbook-from-samples/automation_01_CreateAccount.png)

3.	在 [**加入新的自動化帳戶**] 頁面上，輸入名稱並挑選帳戶所在區域。此區域指定帳戶中的自動化資源將儲存在哪裡。這不會影響帳戶的功能，但如果帳戶區域靠近其他 Azure 資源的儲存位置，則 Runbook 可能會執行得更快。當您準備好時，按一下核取記號。

	![Add New Account](./media/automation-create-runbook-from-samples/automation_02_addnewautoacct.png)

## <a name="importrunbook"></a>從 Runbook 組件庫匯入 Runbook

[Runbook 組件庫](http://aka.ms/runbookgallery)包含您可以直接匯入 Azure 自動化帳戶的範例 Runbook，可讓您運用其他 Azure 自動化和 PowerShell 使用者的工作。在此步驟中，您將使用組件庫匯入"Hello World" 範例 Runbook。

4.	在 [自動化] 頁面上，按一下您剛剛建立的新帳戶。

	![New Account](./media/automation-create-runbook-from-samples/automation_03_NewAutoAcct.png)

5.	按一下 [Runbook]。

	![Runbooks Tab](./media/automation-create-runbook-from-samples/automation_04_RunbooksTab.png)

6.	按一下 [新增] > [Runbook] > [從組件庫]。

	![Runbook Gallery](./media/automation-create-runbook-from-samples/automation_05_ImportGallery.png)

7.  選取 [**教學課程**] 類別，然後選取 [**Hello World for Azure Automation**]。按一下向右箭頭按鈕。

	![Import Runbook](./media/automation-create-runbook-from-samples/automation_06_ImportRunbook.png)

8.  檢閱 Runbook 的內容，然後按一下向右箭頭按鈕。

	![Runbook Definition](./media/automation-create-runbook-from-samples/automation_07_RunbookDefinition.png)

8.	檢閱 Runbook 詳細資料，然後按一下核取記號按鈕。

	![Runbook Details](./media/automation-create-runbook-from-samples/automation_08_RunbookDetails.png)

## <a name="publishrunbook"></a>發佈 Runbook

Runbook 會先以草稿模式匯入。這表示在您授權為可執行的新版本之前，可以繼續在其上執行作業。由於此範例 Runbook 不需要額外設定，所以您會立即依現況發佈。如需詳細資訊，請參閱[發佈 Runbook](http://aka.ms/runbookauthor/azure/publishrunbook)。

9.	當 Runbook 匯入完成時，按一下 [Write-HelloWorld]。

	![Imported Runbook](./media/automation-create-runbook-from-samples/automation_07_ImportedRunbook.png)

9.	按一下 [作者]，然後按一下 [草稿]。

	您可以在 [草稿] 模式下修改 Runbook 的內容。對這個 Runbook 無須進行任何修改。

	![Author Draft](./media/automation-create-runbook-from-samples/automation_08_AuthorDraft.png)

10.	按一下 [**發佈**] 以提升 Runbook，使其可用於生產環境中。

	![Publish](./media/automation-create-runbook-from-samples/automation_085_Publish.png)

11.	系統提示您進行確認時，請按一下 [**是**]。

	![Save and Pub prompt](./media/automation-create-runbook-from-samples/automation_09_SavePubPrompt.png)

## <a name="startrunbook"></a>啟動 Runbook

匯入並發佈 Runbook 後，您現在即可加以執行，然後檢查輸出。如需詳細資訊，請參閱[啟動 Runbook](http://aka.ms/runbookauthor/azure/startrunbook) 和 [Runbook 輸出和訊息](http://aka.ms/runbookauthor/azure/runbookoutput)。

12.	開啟 **Write-HelloWorld** Runbook 後，按一下 [啟動]。

	![已發佈](./media/automation-create-runbook-from-samples/automation_10_PublishStart.png)

13.	在 [Specify the runbook parameter values] 頁面上，輸入將作為 Write-HelloWorld.ps1 指令碼之輸入參數的 [名稱]，然後按一下核取記號。

	![Runbook Parameters](./media/automation-create-runbook-from-samples/automation_11_RunbookParams.png)

14.	按一下 [JOBS] 以查看您剛剛啟動之 Runbook 工作的狀態，然後按一下 [工作啟動] 欄中的時間戳記，以檢視工作摘要。

	![Runbook Status](./media/automation-create-runbook-from-samples/automation_12_RunbookStatus.png)

15.	在 [摘要] 頁面上，您可以檢視工作的摘要、輸入參數和輸出。

	![Runbook Summary](./media/automation-create-runbook-from-samples/automation_13_RunbookSummary_callouts.png)

恭喜！ 您已完成本教學課程。

## <a name="nextsteps"></a>後續步驟
1. 本教學課程中的簡單 Runbook **不會管理 Azure 服務**。大部分的 Runbook 將會使用 [Azure Cmdlet](http://msdn.microsoft.com/library/jj156055.aspx) 來執行此作業，而這需要向您的 Azure 訂用帳戶進行驗證。請依照[設定 Azure 以便依 Runbook 管理](http://aka.ms/azureautomationauthentication)中的指示來設定您的 Azure 訂用帳戶，才能使用這些 Cmdlet。  
2. 如需 Azure 自動化功能的詳細資訊，請參閱下列[資源](#resources)。
3. 訂閱 [Azure 自動化部落格](http://azure.microsoft.com/blog/tag/azure-automation)，隨時掌握 Azure 自動化團隊的最新消息。

## <a name="resources"></a>資源

您可利用各種其他資源，深入了解 Azure 自動化，並建立自己的 Runbook。

- [Azure 自動化程式庫](http://go.microsoft.com/fwlink/p/?LinkId=392860)提供完整的文件，說明如何設定和管理 Azure 自動化，以及撰寫自己的 Runbook。
- [Azure PowerShell Cmdlet](http://msdn.microsoft.com/library/jj156055.aspx) 提供使用 Windows PowerShell 自動執行 Azure 作業的資訊。Runbook 會使用這些 Cmdlet 來處理 Azure 資源。
- [Azure 自動化部落格](http://azure.microsoft.com/blog/tag/azure-automation)提供 Microsoft 的最新 Azure 自動化資訊。
- [自動化論壇](http://go.microsoft.com/fwlink/p/?LinkId=390561)可讓您張貼有關要由 Microsoft 和自動化社群解決的 Azure 自動化問題。


## 範例和工具 Runbook

Microsoft 和 Azure 自動化社群會提供範例 Runbook，協助您開始建立自己的解決方案和公用程式 Runbook，而您可以將這些 Runbook 做為大型自動化工作的建置組塊。您可以從[指令碼中心](http://go.microsoft.com/fwlink/p/?LinkId=393029)下載這些 Runbook，或使用 [Runbook 程式庫](http://aka.ms/runbookgallery)直接將其匯入 Azure 自動化中。


## 意見反應

<strong>請不吝提供意見！</strong> 如果您要尋找 Azure 自動化 Runbook 解決方案或整合模組，請在指令碼中心提出指令碼要求。如果您有關於 Azure 自動化的任何意見或功能要求，請張貼在 [User Voice](http://feedback.windowsazure.com/forums/34192--general-feedback) 上。感謝您！
 

<!---HONumber=62-->