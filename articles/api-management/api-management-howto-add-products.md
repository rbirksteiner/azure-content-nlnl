<properties 
	pageTitle="如何在 Azure API 管理中建立及發行產品" 
	description="了解如何在 Azure API 管理中建立及發行產品。" 
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
	ms.date="06/18/2015" 
	ms.author="sdanie"/>

# 如何在 Azure API 管理中建立及發行產品

在 Azure API 管理中，產品包含一或多個 API，以及使用量配額與使用規定。發行產品之後，開發人員便可訂閱產品，並開始使用產品的 API。本主題提供建立產品、加入 API 和發行供開發人員使用的指引。

## <a name="create-product"> </a>建立產品

請在發行者入口網站新增和設定 API 的作業。若要存取發行者入口網站，請在 API 管理服務的 Azure 入口網站中按一下 [**管理**]。

![發行者入口網站][api-management-management-console]

>如果您尚未建立 API 管理服務執行個體，請參閱[開始使用 Azure API 管理][]教學課程中的[建立 API 管理服務執行個體][]。

按一下左邊功能表中的 [產品]，以顯示 [產品] 頁面，然後按一下 [加入產品]。

![產品][api-management-products]

![New product][api-management-add-new-product]

在 [名稱] 欄位中輸入產品的描述性名稱，在 [描述] 欄位中輸入產品的描述。

API 管理中的產品可以是**開放**或**受保護**的。受保護產品必須先擁有訂用帳戶才能使用，開放產品則可以使用而不需訂用帳戶。若要建立需要訂用帳戶的受保護產品，請核取 [**需要訂閱**]。這是預設設定。

如果您希望管理員檢閱並接受或拒絕對此產品的訂閱嘗試，請核取 [Require subscription approval]。如果未核取方塊，將會自動核准訂閱嘗試。如需訂閱的詳細資訊，請參閱[檢視產品的訂閱者][]。

若要允許開發人員帳戶訂閱產品多次，請核取 [**允許多項訂閱**] 核取方塊。如果未核取此方塊，則每個開發人員帳戶只能訂閱產品一次。

![無限制的多項訂閱][api-management-unlimited-multiple-subscriptions]

若要限制多項同時訂閱的數目，請核取 [**將同時訂閱數目限制為**] 核取方塊，然後輸入訂閱限制。在以下範例中，同時訂閱數已限制為每個開發人員帳戶四個。

![四個多項訂閱][api-management-four-multiple-subscriptions]

設定好所有新產品的選項後，請按一下 [**儲存**] 來建立新產品。

![產品][api-management-products-page]

>依預設不會發行新產品，且只有 [Administrators] 群組才能看見。

若要設定產品，請在 [產品] 索引標籤中按一下產品名稱。

## <a name="add-apis"> </a>將 API 加入至產品

[**產品**] 頁面包含組態的四個連結：**摘要**、**設定**、**可見度**和**訂閱者**。[**摘要**] 索引標籤可讓您加入 API，以及發行或取消發行產品。

![摘要][api-management-new-product-summary]

發行產品之前，您必須加入一或多個 API。若要這樣做，請安一下 [加入 API 至產品]。

![Add APIs][api-management-add-apis-to-product]

選取想要的 API，按一下 [儲存]。

## <a name="add-description"> </a>將描述性資訊加入至產品

[**設定**] 索引標籤可讓您提供產品的詳細資訊，例如用途、它提供存取的 API 及其他有用的資訊。內容以將會呼叫 API 的開發人員為對象，可使用純文字或 HTML 標記來撰寫。

![Product settings][api-management-product-settings]

若要建立需要使用訂用帳戶的受保護產品，請核取 [**需要訂閱**]，或是清除核取方塊，以建立不需訂用帳戶即可呼叫的開放產品。

如果您要手動核准所有產品訂閱要求，請選取 [**需要訂閱**]。依預設會自動同意所有產品訂閱。

若要允許開發人員帳戶訂閱產品多次，請核取 [**允許多項訂閱**] 核取方塊，並選擇是否指定限制。如果未核取此方塊，則每個開發人員帳戶只能訂閱產品一次。

選擇性填寫 [**使用規定**] 欄位，描述訂閱者必須接受才可使用產品的產品使用規定。

## <a name="publish-product"> </a>發行產品

產品必須發行，才能呼叫產品中的 API。在產品的 [摘要] 索引標籤上，按一下 [發行]，然後按一下 [是，發行] 表示確認。若要將先前發行的產品設為私人，請按一下 [取消發行]。

![Publish product][api-management-publish-product]

## <a name="make-visible"> </a>讓開發人員看見產品

[**可見度**] 索引標籤可讓選擇哪些角色可在開發人員入口網站上看見產品及訂閱產品。

![Product visibility][api-management-product-visiblity]

若要允許或不允許群組中的開發人員看見產品，請核取或取消核取群組旁邊的核取方塊，然後按一下 [儲存]。

>如需詳細資訊，請參閱[如何在 Azure API 管理中建立和使用群組來管理開發人員帳戶][]。

## <a name="view-subscribers"> </a>檢視產品的訂閱者

[**訂閱者**] 索引標籤會列出已訂閱產品的開發人員。按一下開發人員的名稱，即可檢視開發人員的詳細資料和設定。在此範例中，還沒有開發人員訂閱產品。

![開發人員][api-management-developer-list]

## <a name="next-steps"> </a>後續步驟

加入想要的 API 並發行產品之後，開發人員就可以訂閱產品並開始呼叫 API。如需有關這些項目和進階產品組態的示範教學課程，請參閱[如何在 Azure API 管理中建立和設定進階產品設定][]。

如需關於使用產品的詳細資訊，請觀看以下影片。

> [AZURE.VIDEO using-products]

[Create a product]: #create-product
[Add APIs to a product]: #add-apis
[Add descriptive information to a product]: #add-description
[Publish a product]: #publish-product
[Make a product visible to developers]: #make-visible
[檢視產品的訂閱者]: #view-subscribers
[Next steps]: #next-steps

[api-management-management-console]: ./media/api-management-howto-add-products/api-management-management-console.png
[api-management-add-product]: ./media/api-management-howto-add-products/api-management-add-product.png
[api-management-add-new-product]: ./media/api-management-howto-add-products/api-management-add-new-product.png
[api-management-unlimited-multiple-subscriptions]: ./media/api-management-howto-add-products/api-management-unlimited-multiple-subscriptions.png
[api-management-four-multiple-subscriptions]: ./media/api-management-howto-add-products/api-management-four-multiple-subscriptions.png
[api-management-products-page]: ./media/api-management-howto-add-products/api-management-products-page.png
[api-management-new-product-summary]: ./media/api-management-howto-add-products/api-management-new-product-summary.png
[api-management-add-apis-to-product]: ./media/api-management-howto-add-products/api-management-add-apis-to-product.png
[api-management-product-settings]: ./media/api-management-howto-add-products/api-management-product-settings.png
[api-management-publish-product]: ./media/api-management-howto-add-products/api-management-publish-product.png
[api-management-product-visiblity]: ./media/api-management-howto-add-products/api-management-product-visibility.png
[api-management-developer-list]: ./media/api-management-howto-add-products/api-management-developer-list.png



[api-management-products]: ./media/api-management-howto-add-products/api-management-products.png
[api-management-]: ./media/api-management-howto-add-products/
[api-management-]: ./media/api-management-howto-add-products/


[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[建立 API 管理服務執行個體]: api-management-get-started.md
[開始使用 Azure API 管理]: api-management-get-started.md#create-service-instance
[Next steps]: #next-steps
[如何在 Azure API 管理中建立和使用群組來管理開發人員帳戶]: api-management-howto-create-groups.md
[如何在 Azure API 管理中建立和設定進階產品設定]: api-management-howto-product-with-rules.md

<!---HONumber=62-->