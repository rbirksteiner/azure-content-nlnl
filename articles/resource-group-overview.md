<properties
   pageTitle="Azure Resource Manager 概觀"
   description="描述如何使用 Azure 資源管理員在 Azure 上進行資源的部署、管理及存取控制。"
   services="multiple"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/22/2015"
   ms.author="tomfitz"/>

# Azure Resource Manager 概觀

應用程式通常由許多元件組成 – 也許是 web 應用程式、資料庫、資料庫伺服器、儲存體和協力廠商服務。您看不到這些元件做為個別的實體，而是看到它們做為單一實體相關且彼此相依的組件。您會想要將其當成群組來部署、管理和監視。Azure 資源管理員可讓您將應用程式中的資源做為群組使用。您可以透過單一、協調的作業來部署、更新或刪除應用程式的所有資源。您會使用部署的範本，且該範本可以用於不同的環境，例如測試、預備和生產環境。您可以檢視整個群組的彙總成本，為您的組織釐清計費。

Azure 資源管理員會以原生方式將存取控制整合到管理平台，讓您可以指定組織中的使用者可以對資源群組採取什麼動作。

> [AZURE.NOTE]本主題描述使用預覽入口網站示範概念的資源、群組和範本。不過，您也可以使用 [Azure CLI for Mac 和 Windows](virtual-machines/xplat-cli-azure-resource-manager.md) 以及 [PowerShell](powershell-azure-resource-manager.md) 建立、管理及刪除 Azure 資源。

## 資源群組

資源群組是保留應用程式相關資源的容器。資源群組可以包含應用程式的所有資源，或只保留以邏輯方式分組在一起的資源。您可以決定如何根據對組織最有利的方式，將資源配置到資源群組。

定義資源群組時，必須考慮一些重要因素：

1. 群組中的所有資源必須共用相同的生命週期。您將一起部署、更新和刪除它們。如果類似資料庫伺服器這樣的資源必須存在於不同的部署週期，它應該位於另一個資源群組中。
2. 每個資源只能存在於一個資源群組中。
3. 您可以隨時在資源群組中新增或移除資源。
4. 資源群組可以包含位於不同區域中的資源。
5. 資源群組可以用來設定系統管理動作的存取控制範圍。

在 Azure Preview 入口網站中，所有新的資源都會建立在資源群組中。即使您只建立單一資源 (例如網站)，您也必須決定是否要將該資源新增至現有的群組，或為該資源建立新的群組。

下圖顯示具有網站、資料庫與 Application Insights 的資源群組。

![資源群組摘要](./media/resource-group-overview/resourcegroupsummary.png)

資源群組也可以連結至另一個資源群組中的資源。當不同資源群組中的資源之間存在著部署相依性時，資源會被視為已連結。例如，如果一個資源群組中的 web 應用程式連接到另一個資源群組中的資料庫，這些資源則為已連結。

![已連結的資源](./media/resource-group-overview/linkedresource.png)

從預覽入口網站中，您可以輕鬆檢視成本、監視事件，並管理警示。下圖顯示群組的合併計費。

![計費](./media/resource-group-overview/billing.png)

## 範本部署

利用 Azure 資源管理員，您可以建立定義應用程式之部署和設定的簡單範本 (以 JSON 格式)。此範本就是所謂的 Azure 範本，並提供定義部署的宣告方式。藉由使用範本，您可以在整個應用程式週期重複部署應用程式，並確信您的資源會部署在一致的狀態中。

在此範本中，您會定義應用程式的基礎結構、如何設定基礎結構，以及如何將應用程式程式碼發佈至該基礎結構。您不需要擔心部署的順序，因為 Azure 資源管理員會分析相依性，確保以正確的順序建立資源。

您也可以使用範本進行基礎結構的更新。例如，您可以將新的資源新增至您的應用程式，並將組態規則新增至已部署的資源。如果此範本會指定建立新的資源，但該資源已經存在，Azure 資源管理員會執行更新，而不必建立新資產。Azure 資源管理員會將現有資產更新為和新資產相同的狀態。

您可以指定範本中的參數，以允許部署中的自訂和彈性。例如，您可以傳遞可修改測試環境部署的參數值。藉由指定參數，您可以將相同的部署範本用於所有的應用程式環境。

您可以在整個範本中執行部署和組態的所有必要步驟，而且您不能有任何剩餘的手動步驟。當您需要其他作業 (例如安裝不包含在安裝程式的特定軟體) 時，Azure 資源管理員會提供案例的延伸模組。如果您已經使用組態管理服務，例如 DSC、Chef 或 Puppet，您可以透過使用擴充功能繼續使用該服務。

當您從 Marketplace 建立方案，方案會自動包含部署範本。您不必從頭建立您的範本，因為您可以從方案的範本開始，並自訂範本以符合您的特定需求。

最後，範本會成為應用程式原始碼的一部分。您可以檢查您的原始程式碼存放庫，並隨著您的應用程式發展加以更新。您可以透過 Visual Studio 編輯範本。

如需關於定義範本的詳細資訊，請參閱[編寫 Azure 資源管理員範本](./resource-group-authoring-templates.md)。

如需使用範本進行部署的相關資訊，請參閱 [利用 Azure 資源管理員範本部署應用程式](azure-portal/resource-group-template-deploy.md)和[如預期般在 Azure 中部署複雜的應用程式](app-service-web/app-service-deploy-complex-application-predictably.md)。

## 標記

Azure 資源管理員提供標記的功能，可讓您根據管理或計費需求將資源分類。當您有複雜的資源群組和資源集合，而且必須以最有利的方式視覺化資產，您可能會想要使用標記。例如，您可以標記在組織中具有類似角色，或屬於相同部門的資源。

在預覽入口網站中，您可以在標記圖示上按一下以開始使用標記。

![tags](./media/resource-group-overview/tags.png)

資源不需要位於相同的資源群組即可共用標記。您可以建立自己的標記分類，以確保組織中的所有使用者都使用常見的標記，而不是使用者意外套用稍有不同的標記 (例如 "dept" 而不是 "department")。

如需標記的詳細資訊，請參閱[使用標記來組織您的 Azure 資源](./resource-group-using-tags.md)。

## 存取控制

Azure 資源管理員可讓您控制哪些人能存取組織的特定動作。以原生方式將 OAuth 和角色型存取控制 (RBAC) 整合至管理平台，並將存取控制套用至資源群組中的所有服務。您可以將使用者新增至預先定義的平台和資源專用的角色，並將這些角色套用至訂用帳戶、資源群組或資源以限制存取。例如，您可以利用稱為 SQL DB 參與者的預先定義角色 (可讓使用者管理資料庫，但無法管理資料庫伺服器或安全性原則)。您在組織中新增的使用者需要 SQL DB 參與者的這類存取，並將該角色套用至訂用帳戶、資源群組或資源。

在預覽入口網站中，您可以按一下存取按鈕以定義存取控制。

![使用者存取控制](./media/resource-group-overview/access.png)

Azure 資源管理員會自動記錄使用者的動作以進行稽核。

您也可以明確地鎖定重要的資源，以防止使用者刪除或修改它們。

如需角色型存取控制的詳細資訊，請參閱 [Microsoft Azure Preview 入口網站中的角色型存取控制](./role-based-access-control-configure.md)。

如需設定存取原則的範例，請參閱[管理與稽核資源存取權](azure-portal/resource-group-rbac.md)。

## 一致的管理層

Azure 資源管理員透過 Azure PowerShell、Azure CLI for Mac、Linux 和 Windows、Azure 入口網站或 REST API 提供完全相容的作業。您可以使用最適合您的介面，而且可以在介面之間快速移動，而不會造成混淆。入口網站甚至會顯示在網站外部採取動作的通知。

如需 PowerShell 的相關資訊，請參閱 [Azure PowerShell 搭配資源管理員使用](./powershell-azure-resource-manager.md)和 [Azure 資源管理員 Cmdlet](https://msdn.microsoft.com/library/azure/dn757692.aspx)

如需 Azure CLI 的相關資訊，請參閱 [Azure CLI for Mac、Linux 和 Windows 搭配 Azure 資源管理使用](./virtual-machines/xplat-cli-azure-resource-manager.md)。

如需 REST API 的相關資訊，請參閱 [Azure 資源管理員 REST API 參考](https://msdn.microsoft.com/library/azure/dn790568.aspx)。

## 後續步驟
開始使用

- [Azure PowerShell 搭配資源管理員使用](./powershell-azure-resource-manager.md)
- [Azure CLI 搭配資源管理員使用](./virtual-machines/xplat-cli-azure-resource-manager.md)
- [使用 Azure 入口網站管理資源](azure-portal/resource-group-portal.md)

建立和部署應用程式

- [撰寫範本](./resource-group-authoring-templates.md)
- [部署範本](azure-portal/resource-group-template-deploy.md)
- [疑難排解部署](virtual-machines/resource-group-deploy-debug.md)
- [透過可預測方式在 Azure 中部署複雜應用程式](app-service-web/app-service-deploy-complex-application-predictably.md)
- [使用 .NET 程式庫和範本部署](virtual-machines/arm-template-deployment.md)
- [範本函式](./resource-group-template-functions.md)
- [進階範本作業](./resource-group-advanced-template.md)

組織資源

- [使用標記組織您的資源](./resource-group-using-tags.md)

管理和稽核存取權

- [管理和稽核資源存取權](azure-portal/resource-group-rbac.md)
- [預覽入口網站中的角色型存取控制](./role-based-access-control-configure.md)
- [驗證服務主體](./resource-group-authenticate-service-principal.md)
- [使用 Azure 入口網站建立新的服務主體](./resource-group-create-service-principal-portal.md)

<!---HONumber=July15_HO1-->