
驗證使用者提交的資料長度一向是最佳做法。在本節中，您會將程式碼新增到行動服務，此服務會驗證傳送至行動服務之字串資料長度，並拒絕太長的字串，在此案例中為超過 10 個字元。

1. 使用 [以系統管理員身分執行] 選項啟動 Visual Studio，然後開啟解決方案，該解決方案包含您在[開始使用]或[開始使用資料](../articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md)教學課程中使用的行動服務專案。

2. 在 [方案總管] 視窗中展開 Todo 清單服務專案，然後展開 [控制器]。開啟行動服務專案的 TodoItemController.cs 檔案。

   	![](./media/mobile-services-dotnet-backend-add-validation/mobile-services-open-todoitemcontroller.png)

3. 以下列將驗證文字字串不超過 10 個字元的方法，取代 `PostTodoItem` 方法。若為文字字串不超過 10 個字元的項目，此方法將傳回內含描述訊息內容的「HTTP 狀態碼 400 不正確的要求」。


        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            if (item.Text.Length > 10)
            {
                return BadRequest("The Item's Text length must not be greater than 10.");
            }
            else
            {
                TodoItem current = await InsertAsync(item);
                return CreatedAtRoute("Tables", new { id = current.Id }, current);
            } 
        }



4. 在服務專案上按一下滑鼠右鍵，再按一下 [組建] 以組建行動服務專案。驗證未發生錯誤。

   	![](./media/mobile-services-dotnet-backend-add-validation/mobile-services-build-dotnet-service.png)

5. 再次以滑鼠右鍵按一下服務專案，然後按一下 [發行]。使用您先前在[開始使用]或[開始使用資料](../articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md)教學課程中使用的發行設定，將行動服務發行至您的 Microsoft Azure 帳戶。
 
     >[AZURE.NOTE]您可以在 IIS Express 中，使用本機主控的服務進行其他測試。如需詳細資訊，請參閱[開始使用資料](../articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md)教學課程。

    ![](./media/mobile-services-dotnet-backend-add-validation/mobile-services-publish-dotnet-service.png)





<!-- URLs. -->
[開始使用]: ../articles/mobile-services/mobile-services-dotnet-backend-windows-store-dotnet-get-started.md

<!---HONumber=July15_HO2-->