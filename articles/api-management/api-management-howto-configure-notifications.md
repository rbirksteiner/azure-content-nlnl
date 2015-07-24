<properties 
	pageTitle="如何在 Azure API 管理中設定通知和電子郵件範本" 
	description="了解如何在 Azure API 管理中設定通知和電子郵件範本。" 
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

# 如何在 Azure API 管理中設定通知和電子郵件範本

API 管理可讓您設定特定事件的通知，以及設定用來與 API 管理執行個體的管理員和開發人員通訊的電子郵件範本。本主題說明如何為可用的事件設定通知，並提供設定這些事件所使用之電子郵件範本的概觀。

## <a name="publisher-notifications"> </a>設定發行者通知

若要設定通知，請在 API 管理服務的 Azure 入口網站中按一下 [**管理**]。這會帶您前往 API 管理發行者入口網站。

![發行者入口網站][api-management-management-console]

>如果您尚未建立 API 管理服務執行個體，請參閱[開始使用 Azure API 管理][]教學課程中的[建立 API 管理服務執行個體][]。

從左邊的 [API 管理] 功能表中按一下 [通知]，以檢視可用的通知。

![Publisher notifications][api-management-publisher-notifications]

下列事件清單可設定通知。

-	**訂用帳戶要求 (需要核准)** - 關於需要核准的 API 產品訂用帳戶要求，指定的電子郵件收件者和使用者會收到電子郵件通知。
-	**新的訂用帳戶** - 關於新的 API 產品訂用帳戶，指定的電子郵件收件者和使用者會收到電子郵件通知。
-	**應用程式庫要求** - 當新的應用程式提交至應用程式庫時，指定的電子郵件收件者和使用者會收到電子郵件通知。
-	**BCC** - 指定的電子郵件收件者和使用者會收到所有寄給開發人員的電子郵件的密件副本。
-	**新的問題或意見** - 當開發人員入口網站上提出新的問題或意見時，指定的電子郵件收件者和使用者會收到電子郵件通知。
-	**關閉帳戶訊息** - 有帳戶關閉時，指定的電子郵件收件者和使用者會收到電子郵件通知。
-	**接近訂用帳戶配額限制** - 當訂用帳戶使用量接近使用量配額時，下列電子郵件收件者和使用者會收到電子郵件通知。

針對每一個事件，您可以使用電子郵件地址文字方塊來指定電子郵件地址，或從清單中選取使用者。

若要指定要通知的電子郵件地址，請在電子郵件地址文字方塊中輸入。如果您有多個電子郵件地址，請以逗號分隔。

![Notification recipients][api-management-email-addresses]

若要指定要通知的使用者，請按一下 [add recipient]，選取要通知之使用者旁邊的核取方塊，然後按一下 [確定]。

>請注意，清單中只顯示系統管理員。

設定通知收件者之後，按一下 [儲存]，套用已更新的通知收件者。

>如果您離開 [Publisher Notifications] 索引標籤，API 管理入口網站會警告有未儲存的變更。

## <a name="email-templates"> </a>設定電子郵件範本

對於管理和使用服務期間傳送的電子郵件訊息，API 管理提供電子郵件範本。提供的電子郵件範本如下。

-	已核准應用程式庫提交
-	開發人員離職信
-	開發人員接近配額限制通知
-	邀請使用者
-	問題中加入新的意見
-	收到新的問題
-	已啟用新的訂用帳戶
-	確認訂用帳戶已更新
-	拒絕訂用帳戶要求
-	收到訂用帳戶要求

可依需要修改這些範本。

若要檢視和設定 API 管理執行個體的電子郵件範本，請從左邊的 [API 管理] 功能表中按一下 [通知]，然後選取 [電子郵件範本] 索引標籤。

![Email templates][api-management-email-templates]

若要檢視或修改特定的範本，請從 [範本] 下拉式清單中選取它。

![Email templates list][api-management-email-templates-list]

每一個電子郵件範本都有純文字的主旨，以及 HTML 格式的本文定義。可依需要自訂每一個項目。

![Email template editor][api-management-email-template]

[參數] 清單包含一份參數清單，當插入至主旨或本文時，則會在傳送電子郵件時取代為指定的值。若要插入參數，請將游標移至您要放置參數的地方，然後按一下參數名稱左邊的箭頭。

按一下 [預覽] 或 [Send a test]，以預覽電子郵件的內容或傳送測試電子郵件。

>請注意，預覽或傳送測試時，不會以實際值來取代參數。
>
>若要儲存電子郵件範本的變更，請按一下 [儲存]，若要取消變更，請按一下 [取消]。



[api-management-management-console]: ./media/api-management-howto-configure-notifications/api-management-management-console.png
[api-management-publisher-notifications]: ./media/api-management-howto-configure-notifications/api-management-publisher-notifications.png
[api-management-email-addresses]: ./media/api-management-howto-configure-notifications/api-management-email-addresses.png


[api-management-email-templates]: ./media/api-management-howto-configure-notifications/api-management-email-templates.png
[api-management-email-templates-list]: ./media/api-management-howto-configure-notifications/api-management-email-templates-list.png
[api-management-email-template]: ./media/api-management-howto-configure-notifications/api-management-email-template.png


[Configure publisher notifications]: #publisher-notifications
[Configure email templates]: #email-templates

[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer

[建立 API 管理服務執行個體]: api-management-get-started.md
[開始使用 Azure API 管理]: api-management-get-started.md#create-service-instance

<!---HONumber=62-->