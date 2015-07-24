

您必須先在服務中建立新的儲存資料表，才能在新行動服務中儲存資料。下列步驟將示範如何使用 Visual Studio 2013，在行動服務中建立新資料表。然後您必須更新應用程式，以便使用行動服務在 Azure 中儲存項目，而非在本機收集中儲存。


1. 在 [伺服器總管] 中，展開 [Azure 行動服務]，以滑鼠右鍵按一下您的行動服務，按一下[建立資料表]，然後在 [資料表名稱] 中輸入 `TodoItem`。

	![在 VS 2013 中建立資料表](./media/mobile-services-create-new-table-vs2013/mobile-create-table-vs2013.png)

2. 展開 **[進階]**，並驗證資料表操作權限已預設為 **[具有應用程式金鑰的任何人]**，然後按一下 **[建立]**。

	這會在伺服器上建立 TodoItem 資料表，具有應用程式金鑰的任何人都可從中存取並變更資料，而無需先經過驗證。

	>[AZURE.NOTE]應用程式金鑰會與應用程式一起散發。因為這個金鑰不會安全地散發，所以無法將它視為安全性 Token。若要確保行動服務資料的存取安全，您必須改在存取之前驗證使用者。如需詳細資訊，請參閱[權限](http://msdn.microsoft.com/library/windowsazure/jj193161.aspx)。
	>
	>新的資料表是使用 Id、__createdAt__、updatedAt 和 __version 等資料行建立而成。啟用動態結構描述時，行動服務會根據插入或更新要求中的 JSON 物件自動產生新資料行。如需詳細資訊，請參閱[動態結構描述](http://msdn.microsoft.com/library/windowsazure/jj193175.aspx)。

<!---HONumber=July15_HO2-->