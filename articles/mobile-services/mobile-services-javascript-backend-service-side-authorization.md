<properties
	pageTitle="行動服務中使用 JavaScript 後端的使用者服務端授權 |行動開發人員中心"
	description="了解如何在 Azure 行動服務的 JavaScript 後端為使用者授權。"
	services="mobile-services"
	documentationCenter=""
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.topic="article"
	ms.devlang="javascript"
	ms.date="05/20/2015"
	ms.author="krisragh"/>

# 在行動服務中的使用者服務端授權

> [AZURE.SELECTOR-LIST (Platform | Backend)]
- [(Any | .NET)](mobile-services-dotnet-backend-service-side-authorization.md)
- [(Any | Javascript)](mobile-services-javascript-backend-service-side-authorization.md)

本主題說明如何使用伺服器端指令碼來授權使用者。在此教學課程中，您使用 Azure 行動服務註冊指令碼，根據使用者識別碼篩選查詢，並且只提供使用者對自己資料的存取權。根據使用者識別碼篩選使用者查詢結果是最基本的授權形式。根據特定案例，您也可能也會想要建立使用者或角色資料表，以便追蹤更詳細的使用者授權資訊，例如使用者可以存取哪些端點。

本教學課程是根據＜行動服務快速入門＞，並且以[將驗證新增至現有的行動服務應用程式]教學課程為基礎而建立。請先完成[將驗證新增至現有的行動服務應用程式]。

## <a name="register-scripts"></a>註冊指令碼

1. 登入 [Azure 管理入口網站]，按一下 [行動服務]，然後按一下您的行動服務。按一下 [資料] 索引標籤，然後按一下 [TodoItem] 資料表。

2. 按一下 [**指令碼**]，選取 [**插入**] 作業，將現有指令碼取代為下列函數，然後按一下 [**儲存**]。

        function insert(item, user, request) {
          item.userId = user.userId;
          request.execute();
        }

	這個指令碼會在插入之前，將已驗證使用者的使用者識別碼新增至項目。

    >[AZURE.NOTE]請確定[動態結構描述](https://msdn.microsoft.com/library/azure/jj193175.aspx)已啟用，否則系統不會自動加入 *userId* 資料行。依預設，系統會為新的行動服務啟用此設定。

3. 同樣地，以下列函數取代現有的 **Read** 作業。此指令碼會篩選傳回的 TodoItem 物件，因此使用者只會收到他們自己插入的項目。

        function read(query, user, request) {
           query.where({ userId: user.userId });
           request.execute();
        }

## <a name="test-app"></a>測試應用程式

1. 請注意，現在當您執行您的用戶端應用程式時，雖然 _TodoItem_ 資料表中已經有來自先前教學課程的項目，但是並不會傳回任何項目。這是因為先前的項目插入時沒有使用者識別碼資料行，現在則具有 Null 值。驗證剛新增的項目在 _TodoItem_ 資料表中有關聯的 userId 值。

2. 如果您有其他登入帳戶，請驗證使用者只能看到他們自己的資料，方法是關閉並刪除應用程式，然後重新執行。顯示登入認證對話方塊時，請輸入不同的登入，然後驗證是否不會顯示在前一次登入下所輸入的項目。

<!-- Anchors. -->
[Register server scripts]: #register-scripts
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->

[Windows Push Notifications & Live Connect]: http://go.microsoft.com/fwlink/p/?LinkID=257677
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[My Apps dashboard]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[將驗證新增至現有的行動服務應用程式]: /develop/mobile/tutorials/get-started-with-users-ios

[Azure 管理入口網站]: https://manage.windowsazure.com/
 

<!---HONumber=July15_HO1-->