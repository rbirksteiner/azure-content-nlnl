在本節中，您將使用 Visual Studio 在 IIS Express 中的本機開發工作站上代管行動服務。接著，您將測試應用程式和後端服務。


1. 在 Visual Studio 中按 F7 鍵，或從 [建置] 功能表中按一下 [建置方案]，以建置 Windows 市集應用程式和行動服務。請在 Visual Studio 的輸出視窗中，驗證兩個專案都順利建置而未產生錯誤。

2. 在 Visual Studio 中按 F5 鍵，或從 [偵錯] 功能表中按一下 [開始偵錯]，以執行應用程式，並在 IIS Express 中本機代管行動服務。

 
3. 輸入新 todoitem 的文字。然後按一下 [儲存]。這會在 IIS Express 本機代管的行動服務所建立的資料庫中，插入新的 todoItem。

    ![](./media/mobile-services-dotnet-backend-test-local-service-data/new-local-todoitem.png)

4. 按一下其中一個項目的核取方塊，將其標示為已完成。

    ![](./media/mobile-services-dotnet-backend-test-local-service-data/local-item-checked.png)

5. 在 Visual Studio 中，您可以開啟 [伺服器總管] 並展開 [資料連線]，以檢視您為後端服務建立之資料庫中的變更。以滑鼠右鍵按一下 [MS_TableConnectionString] 下的 TodoItems 資料表，然後按一下 [顯示資料表資料]。

    ![](./media/mobile-services-dotnet-backend-test-local-service-data/vs-show-local-table-data.png)

<!---HONumber=July15_HO2-->