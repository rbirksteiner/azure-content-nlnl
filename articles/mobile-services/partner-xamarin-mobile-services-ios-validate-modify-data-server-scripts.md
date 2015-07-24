<properties
	pageTitle="使用伺服器指令碼來驗證與修改資料 (Xamarin iOS) |行動開發人員中心"
	description="了解如何驗證和修改從 Xamarin iOS 應用程式使用伺服器指令碼傳送的資料。"
	services="mobile-services"
	documentationCenter="xamarin"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="04/24/2015"
	ms.author="ggailey777"/>

# 使用伺服器指令碼在行動服務中驗證與修改資料

[AZURE.INCLUDE [mobile-services-selector-validate-modify-data](../../includes/mobile-services-selector-validate-modify-data.md)]

本主題將示範如何在 Azure 行動服務中運用伺服器指令碼。您可在行動服務中註冊伺服器指令碼，並使用該指令碼來針對插入和更新資料執行各種操作，包括驗證與資料修改。在本教學課程中，您將定義並註冊可驗證與修改資料的伺服器指令碼。由於伺服器端指令碼的行為常會影響用戶端，您也可以更新您的 iOS 應用程式，以充分利用這些新行為。完成的程式碼可從 [ValidateModifyData 應用程式][GitHub]範例中取得。

本教學課程將逐步引導您完成下列基本步驟：

1. [新增字串長度驗證]
2. [更新用戶端以支援驗證]
3. [插入時新增時間戳記]
4. [更新用戶端以顯示時間戳記]

本教學課程會以上一堂教學課程[開始使用資料]中的步驟和範例應用程式為基礎。在開始本教學課程之前，您應該先完成[開始使用資料]。

## <a name="string-length-validation"></a>新增驗證

驗證使用者提交的資料長度一向是最佳做法。首先，註冊可驗證傳送至行動服務之字串資料長度的指令碼，並拒絕太長的字串，在此案例中為不得超過 10 個字元。

1. 登入 [Azure 管理入口網站]，按一下 [行動服務]，然後按一下您的應用程式。

	![][0]

2. 按一下 [資料] 索引標籤，然後按一下 [TodoItem] 資料表。

   	![][1]

3. 按一下 [指令碼]，然後選取 [插入] 作業。

   	![][2]

4. 以下列函數取代現有的指令碼，然後按一下 [儲存]。

				function insert(item, user, request) {
        	if (item.text.length > 10) {
            request.respond(statusCodes.BAD_REQUEST, 'Text length must be 10 characters or less.');
          } else {
            request.execute();
          }
        }

    本指令碼會檢查 **text** 屬性的長度，並在長度超過 10 個字元時傳送錯誤回應。否則，系統會呼叫 **execute** 方法以完成插入。

    > [AZURE.NOTE]您可以將位於 [指令碼] 索引標籤上的已註冊指令碼移除，方法是依序按一下 [清除]、[儲存]。

## <a name="update-client-validation"></a>更新用戶端

現在，行動服務正在驗證資料並傳送錯誤回應，您必須更新您的應用程式以便處理驗證的錯誤回應。

1. 在 Xamarin Studio 中，開啟您完成[開始使用資料]教學課程時所修改的專案。

2. 按 [**執行**] 按鈕以建置專案並啟動應用程式，在文字方塊中輸入超過 10 個字元的文字，然後按一下加號 (**+**) 圖示。

	請留意到，由於行動服務傳回了 400 回應 (不正確的要求)，因此應用程式發出未處理的錯誤。

3. 在 TodoService.cs 檔案的 **InsertTodoItemAsync** 方法中，找出目前的 <code>try/catch</code> 例外狀況處理，並將 <code>catch</code> 取代為：

    catch (Exception ex) { var exDetail = (ex.InnerException.InnerException as MobileServiceInvalidOperationException); Console.WriteLine(exDetail.Message);

        UIAlertView alert = new UIAlertView() {
            	Title = "Error",
            	Message = exDetail.Message
        } ;
        alert.AddButton("Ok");
        alert.Show();

        return -1;
		}

	此時會出現快顯視窗，對使用者顯示錯誤。

4. 在 **TodoListViewController.cs** 中找出 **OnAdd** 方法。請更新此方法，以確保傳回的 <code>index</code> 不會和 **InsertTodoItemAsync** 中的例外狀況處理一樣傳回 <code>-1</code>。在此案例中，我們不會在 <code>TableView</code> 中增加新的資料列。

    if (index != -1) { TableView.InsertRows(new { NSIndexPath.FromItemSection(index, 0) }, UITableViewRowAnimation.Top); itemText.Text = ""; }


5. 重新建置並啟動應用程式。

	![][4]

	請留意到，此時會處理錯誤，並對使用者顯示錯誤訊息。


## <a name="next-steps"> </a>後續步驟

現在，您已完成本教學課程，請考慮繼續進行資料數列中最後的教學課程：[使用分頁縮小查詢範圍]。

您也可以在授權使用者及傳送推播通知時使用伺服器指令碼。如需詳細資訊，請參閱下列教學課程：

* [使用指令碼授權使用者] <br/>了解如何根據通過驗證使用者的 ID 來篩選資料。

* [開始使用推播通知] <br/>了解如何將極為基本的推播通知傳送到應用程式。

* [行動服務伺服器指令碼參考] <br/>深入了解如何註冊和使用伺服器指令碼。

<!-- Anchors. -->
[新增字串長度驗證]: #string-length-validation
[更新用戶端以支援驗證]: #update-client-validation
[插入時新增時間戳記]: #add-timestamp
[更新用戶端以顯示時間戳記]: #update-client-timestamp
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-services-selection.png
[1]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-portal-data-tables.png
[2]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-insert-script-users.png

[4]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-quickstart-data-error-ios.png

<!-- URLs. -->
[行動服務伺服器指令碼參考]: http://go.microsoft.com/fwlink/?LinkId=262293
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-ios
[使用指令碼授權使用者]: /develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
[使用分頁縮小查詢範圍]: /develop/mobile/tutorials/add-paging-to-data-xamarin-ios
[開始使用資料]: /develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-xamarin-ios
[開始使用推播通知]: /develop/mobile/tutorials/get-started-with-push-xamarin-ios

[Management Portal]: https://manage.windowsazure.com/
[Azure 管理入口網站]: https://manage.windowsazure.com/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331330
 

<!---HONumber=July15_HO1-->