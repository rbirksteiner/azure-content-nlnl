<properties 
	pageTitle="如何在 Azure API 管理中建立和使用群組來管理開發人員帳戶" 
	description="了解如何在 Azure API 管理中使用群組來管理管理開發人員帳戶。" 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/16/2015" 
	ms.author="sdanie"/>

# 如何在 Azure API 管理中建立和使用群組來管理開發人員帳戶

在 API 管理中，群組的作用是管理產品對於開發人員的可見度。產品是先設為可讓群組看見，然後群組中的開發人員就能檢視和訂閱與群組相關聯的產品。

API 管理具有下列不可變的系統群組。

-	**管理員** - Azure 訂用帳戶管理員是此群組的成員。管理員可管理 API 管理服務執行個體、建立開發人員所使用的 API、作業和產品。
-	**開發人員** - 已驗證開發人員入口網站使用者屬於此群組。開發人員是使用您的 API 建置應用程式的客戶。開發人員獲授與開發人員入口網站的存取權，並建置呼叫 API 作業的應用程式。
-	**來賓** - 未經驗證的開發人員入口網站使用者 (例如，造訪 API 管理執行個體之開發人員入口網站的潛在客戶) 屬於此群組。他們可獲得特定唯讀存取權限，例如他們可檢視 API 但無法進行呼叫。

除了這些系統群組以外，管理員還可以建立自訂群組，或使用[使用 Azure Active Directory 相關租用戶中的外部群組](api-management-howto-aad.md/#how-to-add-an-external-azure-active-directory-group)。自訂群組和外部群組可以與系統群組一起使用，提供開發人員 API 產品的能見度及存取權。例如，您可以為與特定夥伴組織有關的開發人員建立一個自訂群組，並只允許他們存取來自含相關 API 之產品的 API。使用者可以是多個群組的成員。

本指南說明 API 管理執行個體的管理員如何加入新的群組，並將這些群組與產品和開發人員建立關聯。

## <a name="create-group"> </a>建立群組

若要建立新的群組，請在 Azure 入口網站中，針對您的 API 管理服務按一下 [**管理**]。這會帶您前往 API 管理發行者入口網站。

![發行者入口網站][api-management-management-console]

>如果您尚未建立 API 管理服務執行個體，請參閱[開始使用 Azure API 管理][]教學課程中的[建立 API 管理服務執行個體][]。

從左邊的 [API 管理] 功能表中按一下 [群組]，然後按一下 [加入群組]。

![Add new group][api-management-add-group]

輸入群組的唯一名稱和選用的描述，然後按一下 [儲存]。

![Add new group][api-management-add-group-window]

新群組會顯示在 [群組] 索引標籤中。若要編輯群組的 [名稱] 或 [描述]，請在清單中按一下群組名稱。若要刪除群組，請按一下 [**刪除**]。

![Group added][api-management-new-group]

現在已建立群組，它可以與產品和開發人員建立關聯。

## <a name="associate-group-product"> </a>將群組與產品建立關聯

若要將群組與產品建立關聯，請從左邊的 [API 管理] 功能表中按一下 [產品]，然後按一下所需產品的名稱。

![Set visibility][api-management-add-group-to-product]

選取 [可見度] 索引標籤來加入和移除群組，以及檢視產品的目前群組。若要加入或移除群組，請核取或取消核取所需群組的核取方塊，然後按一下 [儲存]。

![Set visibility][api-management-add-group-to-product-visibility]

>[AZURE.NOTE]若要加入 Azure Active Directory 群組，請參閱[如何在 Azure API 管理中使用 Azure Active Directory 授權開發人員帳戶](api-management-howto-aad.md)。
>
>若要從產品的 [可見度] 索引標籤中設定群組，請按一下 [管理群組]。

當產品與群組建立關聯之後，該群組中的開發人員就可以檢視和訂閱產品。

## <a name="associate-group-developer"> </a>將群組與開發人員建立關聯

若要將群組與開發人員建立關聯，請從左邊的 [**API 管理**] 功能表中，按一下 [**使用者**]，然後核取要與群組建立關聯之開發人員旁邊的核取方塊。

![Add developer to group][api-management-add-group-to-developer]

核取想要的開發人員之後，在 [加入群組] 下拉式清單中按一下所需的群組。使用 [Remove from Group] 下拉式清單，可從群組中移除開發人員。

![開發人員][api-management-add-group-to-developer-saved]

在開發人員與群組之間加入關聯之後，您可以在 [**使用者**] 索引標籤中檢視此關聯。

## <a name="next-steps"> </a>後續步驟

當開發人員加入至群組之後，他們就可以檢視和訂閱與該群組相關聯的產品。如需詳細資訊，請參閱[如何在 Azure API 管理中建立和發行產品][]。


[api-management-management-console]: ./media/api-management-howto-create-groups/api-management-management-console.png
[api-management-add-group]: ./media/api-management-howto-create-groups/api-management-add-group.png
[api-management-add-group-window]: ./media/api-management-howto-create-groups/api-management-add-group-window.png
[api-management-new-group]: ./media/api-management-howto-create-groups/api-management-new-group.png
[api-management-add-group-to-product]: ./media/api-management-howto-create-groups/api-management-add-group-to-product.png
[api-management-add-group-to-product-visibility]: ./media/api-management-howto-create-groups/api-management-add-group-to-product-visibility.png
[api-management-add-group-to-developer]: ./media/api-management-howto-create-groups/api-management-add-group-to-developer.png
[api-management-add-group-to-developer-saved]: ./media/api-management-howto-create-groups/api-management-add-group-to-developer-saved.png

[api-management-]: ./media/api-management-howto-create-groups/api-management-.png

[Create a group]: #create-group
[Associate a group with a product]: #associate-group-product
[Associate groups with developers]: #associate-group-developer
[Next steps]: #next-steps

[如何在 Azure API 管理中建立和發行產品]: api-management-howto-add-products.md

[建立 API 管理服務執行個體]: api-management-get-started.md
[開始使用 Azure API 管理]: api-management-get-started.md#create-service-instance

<!---HONumber=62-->