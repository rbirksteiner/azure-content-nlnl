<properties 
	pageTitle="使用 Azure Preview 入口網站管理 Azure 資源" 
	description="將多個資源分組為邏輯群組，使該邏輯群組成為所含之資源的生命週期界限。" 
	services="azure-portal" 
	documentationCenter="" 
	authors="" 
	writer="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-portal" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/22/2015" 
	ms.author="tomfitz"/>


# 使用 Azure Preview 入口網站管理 Azure 資源

## 簡介

過去在 Microsoft Azure 中管理資源 (使用者管理的實體，例如資料庫伺服器、資料庫或網站) 時，您必須逐一對每項資源執行作業。如果您有某個應用程式是由多項資源所組成，此應用程式的管理工作就會很複雜。在 Azure Preview 入口網站中，您可以建立資源群組，以同時管理某個應用程式中的所有資源。資源群組是 Azure 中的新概念，可作為生命週期內各項資源的生命週期界限。

資源群組可讓您同時管理某個應用程式中的所有資源。資源群組會由新的管理功能「Azure 資源管理員」所啟用。Azure 資源管理員可讓您將多項資源分成邏輯群組，以作為生命週期內各項資源的生命週期界限。一般而言，一個群組會包含一個應用程式的相關資源。例如，一個群組可能會包含一項代管公用網站的網站資源、一個儲存網站所使用之關聯式資料的 SQL Database，和一個儲存非關聯式資產的儲存體帳戶。

以下將簡單說明如何在 Azure 入口網站中使用資源群組。

## 建立資源群組

資源在入口網站中建立時，一律會建立在資源群組內。您可以選擇建立新的資源群組，或使用建立流程中的現有資源群組。<br><br />

![建立資源群組](./media/resource-group-portal/1_createWebsite.png)

當您建立由幾個一起運作的資源 (例如網站 + 資料庫) 所組成的應用程式時，該應用程式一律會建立在其本身的資源群組中，以便您可以使用資源群組來管理所有相關資產的生命週期。您可以隨著應用程式的變化，在資源群組中新增或移除資源。

![建立資源群組](./media/resource-group-portal/2_createWSandDB.png)

## 瀏覽資源群組

您可以按一下位於畫面左側的 Jumpbar，以瀏覽各個資源群組。資源群組會有一個分頁為您顯示特定資源群組的所有資訊。此資源群組分頁會為您提供資源群組中各項資源的計費和監視資訊的統合檢視。

摘要區段會顯示資源群組中各項資源的視覺化對應，同時也會顯示其他資源群組中與此資源群組連結的資源。資源對應也會顯示每項資源的狀態。![資源群組摘要](./media/resource-group-portal/3_1BrowseRGs.png)

資源對應部分可以自訂，以放大顯示該資源群組內包含的所有資源，以及其他資源群組中的連結資源。此部分可釘選而複製到右側。

![釘選](./media/resource-group-portal/3_2BrowseRGs.png)

按一下資源對應，可啟動該資源對應上所有資源的清單檢視。此檢視會列出資源群組內的所有資源及其連結資源。按一下這些資源，將會啟動其刀鋒視窗。

![查看資源](./media/resource-group-portal/3_3BrowseRGs.png)

## 將資源新增至資源群組

您可以在資源群組刀鋒視窗上使用 **Add** 命令，將資源新增至資源群組。依循流程中的步驟，可將其他資源新增至資源群組。

![新增資源](./media/resource-group-portal/4_AddResource.png)

建議您不要將小組專案放置在與其他 Azure 資源相同的資源群組中。如果您在新的帳戶和群組中建立小組專案，然後建立網站，則網站群組將會預設為最後使用的群組 (VSO 群組)，且您的執行階段/開發人員資源將會放在相同的群組中。

## 刪除資源群組

由於資源群組可讓您管理所有內含資源的生命週期，因此若刪除資源群組，將一併刪除群組內包含的所有資源。您也可以刪除資源群組內的個別資源。在刪除資源群組時應多加留意，因為可能會有其他資源連結至該群組。您可以檢視資源對應中的連結資源，並執行必要的步驟，以避免在刪除資源群組時出現非預期的結果。

## 後續步驟
開始使用

- [Azure 資源管理員概觀](../resource-group-overview.md)  
- [搭配使用 Azure PowerShell 與 Azure 資源管理員](../powershell-azure-resource-manager.md)
- [搭配使用適用於 Mac、Linux 和 Windows 的 Azure CLI 與 Azure 資源管理](../xplat-cli-azure-resource-manager.md)  
  
建立和部署應用程式
  
- [編寫 Azure 資源管理員範本](../resource-group-authoring-templates.md)  
- [使用 Azure 資源管理員範本部署應用程式](resource-group-template-deploy.md)
- [透過可預測方式在 Azure 中部署複雜應用程式](../app-service-web/app-service-deploy-complex-application-predictably.md)
- [Azure 中的資源群組部署疑難排解](../resource-group-deploy-debug.md)  
- [Azure 資源管理員範本函數](../resource-group-template-functions.md)  
- [進階範本作業](../resource-group-advanced-template.md)  
- [使用 .NET 程式庫和範本部署 Azure 資源](../arm-template-deployment.md)
  
組織資源
  
- [使用標記來組織您的 Azure 資源](../resource-group-using-tags.md)  
  
管理和稽核存取權
  
- [管理和稽核資源存取權](resource-group-rbac.md)  
- [使用 Azure 資源管理員驗證服務主體](../resource-group-authenticate-service-principal.md)  
- [使用 Azure 傳統入口網站建立新的 Azure 服務主體](../resource-group-create-service-principal-portal.md)  
  


 

<!---HONumber=62-->