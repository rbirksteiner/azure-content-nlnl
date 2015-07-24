## 設定儲存體連接字串

Azure Storage Client Library for .NET 可支援使用儲存體連接字串，來設定存取儲存服務時所需的端點與認證。建議您在組態檔中維護儲存體連接字串，而非將它以硬式編碼的方式寫入您的應用程式。您有兩個選項可以用來儲存連接字串：

- 如果您的應用程式會在 Azure 雲端服務中執行，則使用 Azure 服務組態系統 (`*.csdef` 和 `*.cscfg` 檔案) 儲存連接字串。如需有關 Azure 雲端服務組態的詳細資訊，請參閱[如何建立及部署雲端服務](../articles/cloud-services/cloud-services-how-to-create-deploy.md)。
- 如果您的應用程式會在 Azure 虛擬機器中執行，或如果您要建置在 Azure 外部執行的 .NET 應用程式，則使用 .NET 組態系統 (例如 `web.config` 或 `app.config` 檔案) 儲存連接字串。

稍後在本指南中，我們將說明如何從您的程式碼中擷取連接字串。

### 從 Azure 雲端服務設定連接字串

Azure 雲端服務具備獨特的服務組態機制，可讓您從 Azure 管理入口網站動態變更組態設定，而無需重新部署應用程式。

若要在 Azure 服務設定中設定連接字串：

1.  在 Visual Studio 的 [方案總管]，於 Azure 部署專案之 [角色] 資料夾中的 Web 角色或背景工作角色上按一下滑鼠右鍵，再按一下 [屬性]。![在 Visual Studio 中，於雲端服務角色上選取屬性][connection-string1]

2.  按一下 [設定] 索引標籤，再按 [加入設定] 按鈕。![在 Visual Studio 中新增雲端服務設定][connection-string2]

    設定清單中隨即顯示新的 [Setting1] 項目。

3.  在新 [Setting1] 項目的 [類型] 下拉式清單中，選擇 [連接字串]。![設定連接字串類型][connection-string3]

4.  按一下 [Setting1] 項目右側的 [...] 按鈕。[儲存體帳戶連接字串] 對話方塊隨即開啟。

5.  選擇要以儲存體模擬器 (本機電腦上模擬的 Microsoft Azure 儲存體)，還是雲端中的儲存體帳戶為目標。不論您選擇哪一種，本指南的程式碼都適用。

	> [AZURE.NOTE]您可以選擇以儲存體模擬器為目標，以避免產生與 Azure 儲存體相關聯的任何費用。不過，如果您選擇以雲端中的 Azure 儲存體帳戶為目標，則執行本教學課程的費用可以忽略不計。

	如果您要選擇以雲端中的儲存體帳戶為目標，則輸入該儲存體帳戶的主要存取金鑰。若要了解如何透過 Azure 管理入口網站複製主要存取金鑰，請參閱[檢視、複製和重新產生儲存體存取金鑰](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)。

	> [AZURE.NOTE]儲存體帳戶金鑰很類似儲存體帳戶的根密碼。請務必保護您的金鑰。請避免轉發給其他使用者，或將它儲存在其他人可以存取的純文字檔案。如果您認為金鑰可能遭到破解，請使用管理入口網站重新產生金鑰。
	
    ![Select target environment][connection-string4]

6.  將 [名稱] 項目從 [Setting1] 變為更易記的名稱，如 **StorageConnectionString**。您稍後將在本指南的程式碼中參照此連接字串。![變更連接字串名稱][connection-string5]
	
### 使用 .NET 設定來設定連接字串

如果您要撰寫的應用程式並非 Azure 雲端服務 (請參閱上一節)，建議您使用 .NET 組態系統 (例如 `web.config` 或 `app.config`)。這類應用程式包括 Azure 網站或 Azure 虛擬機器，以及設計成在 Azure 外執行的應用程式。您可以使用 `<appSettings>` 元素儲存連接字串，如下所示。以您的儲存體帳戶名稱取代 `account-name`，並以您的帳戶存取金鑰取代 `account-key`：

	<configuration>
  		<appSettings>
    		<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
  		</appSettings>
	</configuration>

例如，組態檔中的組態設定可能如下所示：

	<configuration>
    	<appSettings>
      		<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=nYV0gln9fT7bvY+rxu2iWAEyzPNITGkhM88J8HUoyofpK7C8fHcZc2kIZp6cKgYRUM74lHI84L50Iau1+9hPjB==" />
    	</appSettings>
	</configuration>

現在您已準備就緒，可以開始執行本指南的「作法」工作。

[connection-string1]: ./media/storage-configure-connection-string-include/connection-string1.png
[connection-string2]: ./media/storage-configure-connection-string-include/connection-string2.png
[connection-string3]: ./media/storage-configure-connection-string-include/connection-string3.png
[connection-string4]: ./media/storage-configure-connection-string-include/connection-string4.png
[connection-string5]: ./media/storage-configure-connection-string-include/connection-string5.png

[Configuring Connection Strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx

<!---HONumber=July15_HO2-->