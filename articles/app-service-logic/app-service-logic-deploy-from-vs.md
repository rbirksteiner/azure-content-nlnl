<properties 
	pageTitle="從 Visual Studio 部署" 
	description="在 Visual Studio 中建立專案來管理邏輯應用程式。" 
	authors="stepsic-microsoft-com" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2015"
	ms.author="stepsic"/>
	
# 從 Visual Studio 部署

雖然 [Azure 入口網站](https://portal.azure.com)有很好的方法讓您設計和管理邏輯應用程式，但您也可以從 Visual Studio 部署邏輯應用程式。這帶來一些重要的功能：

- 將邏輯應用程式連同其他資產一起儲存在方案中，因此可含括應用程式的所有層面。
- 保持邏輯應用程式簽入原始檔控制中，讓您利用 TFS 或 Git 來追蹤它的修訂 

您必須安裝 Azure SDK 2.6 才能執行下列步驟。在這裡尋找[最新版的 SDK for VS](http://azure.microsoft.com/downloads/)。

## 建立專案

1. 移至 [**檔案**] 功能表，選取 [**新增**] > [**專案**] (或者，移至 [**加入**]，再選取 [**新增專案**]，以加入至現有的方案)。![[檔案] 功能表](./media/app-service-logic-deploy-from-vs/filemenu.png)

2. 在對話方塊中，尋找 [**雲端**]，然後選取 [**Azure 資源群組**]。輸入 [**名稱**]，然後按一下 [**確定**]。![加入新的專案](./media/app-service-logic-deploy-from-vs/addnewproject.png)

3. 您現在必須選取想要 [**邏輯應用程式**]，還是 [**邏輯應用程式+API 應用程式**]。選取 [**邏輯應用程式**] 需要指向現有的 API 的應用程式。如果選取 [**邏輯應用程式+API 應用程式**]，則也可以同時建立新的空白 API 應用程式。![選取 Azure 範本](./media/app-service-logic-deploy-from-vs/selectazuretemplate.png)

4. 選取 [**範本**] 之後，按一下 [**確定**]。

邏輯應用程式現在會加入至方案。您在 [方案總管] 中應該會看到部署：![Deployment](./media/app-service-logic-deploy-from-vs/deployment.png)

## 設定邏輯應用程式

建立專案後，您可以在 VS 內編輯邏輯應用程式的定義。在方案總管中，按一下 JSON 檔案。您會看到預留位置定義，您可以在其中填入應用程式的邏輯。

建議在整個定義中都使用**參數**。當您想同時部署至開發與生產環境時，這樣會很有用。在此情況下，您應該將環境專用的所有組態放入 `.param` 檔案中，並使用參數，而不是實際字串。

目前，Visual Studio 沒有內建的設計工具，如果想要使用圖形介面 (而不是撰寫 JSON)，您需要使用 Azure 入口網站。

如果您先前在 Azure 入口網站內建立邏輯應用程式，現在想要將它簽入原始檔控制中，您可以：移至入口網站中的 [**程式碼檢視**]，並複製定義。- 使用邏輯應用程式 [REST API](https://msdn.microsoft.com/library/azure/dn948510.aspx) 取得定義。- 使用 [Azure 資源管理員 PowerShell](../powershell-azure-resource-manager.md) (特別是 [`Get-AzureResource` 命令](https://msdn.microsoft.com/library/dn654579.aspx)) 下載定義。

## 部署邏輯應用程式

最後，設定應用程式之後，只要幾個步驟，就能從 Visual Studio 直接部署。

1. 在 [方案總管] 中，以滑鼠右鍵按一下部署，移至 [**部署**] > [**新增部署...**] ![新增部署](./media/app-service-logic-deploy-from-vs/newdeployment.png)

2. 系統會提示您登入 Azure 訂用帳戶。

3. 現在，針對邏輯應用程式要部署到的資源群組，您需要選擇其詳細資料。![部署到資源群組](./media/app-service-logic-deploy-from-vs/deploytoresourcegroup.png)

    務必為資源群組選取正確的範本和參數檔 (例如，如果是部署至生產環境，請選擇生產參數檔)。
    
4. [**輸出**] 視窗會顯示部署狀態 (您可能需要選擇 [**Azure 佈建**]。![輸出](./media/app-service-logic-deploy-from-vs/output.png)

未來，您可以在原始檔控制中修改邏輯應用程式，並利用 Visual Studio 來部署新的版本。請注意，如果您直接在 Azure 入口網站中修改定義，則下次從 Visual Studio 部署時會覆寫這些變更。

如果您不想使用 Visual Studio，但仍想有工具從原始檔控制來部署邏輯應用程式，您永遠都能直接利用 [API](https://msdn.microsoft.com/library/azure/dn948510.aspx) 或 [Powershell](../powershell-azure-resource-manager.md) 來自動化部署。

<!---HONumber=62-->