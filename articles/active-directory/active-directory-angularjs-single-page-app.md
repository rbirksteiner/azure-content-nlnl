<properties
	pageTitle="如何使用 Azure AD 建置 AngularJS 單頁應用程式"
	description="示範使用 Active Directory 驗證程式庫 (ADAL) for JavaScript 來保護 AngularJS 式單一頁面應用程式，使用 ASP.NET Web API 後端實作，其呼叫使用 CORS 的另一個 ASP.NET Web API。"
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="terrylan"
	editor="LisaToft"/>

<tags
	ms.service="active-directory"
	ms.devlang="javascript"
	ms.topic="article" 
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="05/11/2015"
	ms.author="justinha"/>


# 如何使用 Azure AD 建置 AngularJS 單頁應用程式

本教學課程示範使用 Active Directory 驗證程式庫 (ADAL) for JavaScript 來保護 AngularJS 式單一頁面應用程式，使用 ASP.NET Web API 後端實作，其呼叫使用 CORS 的另一個 ASP.NET Web API。如需查看用於此教學課程的程式碼範例，請參閱 GitHub 上的 [AzureADSamples/SinglePageApp-AngularJS-DotNet](https://github.com/AzureADSamples/SinglePageApp-AngularJS-DotNet)。

ADAL for Javascript 是開放原始碼程式庫。如需分佈選項、原始程式碼和投稿內容，請參閱 [ADAL JS 儲存機制](https://github.com/AzureAD/azure-activedirectory-library-for-js)。

如需通訊協定在此案例和其他案例下如何運作的詳細資訊，請參閱 [Azure AD 的驗證案例](http://go.microsoft.com/fwlink/?LinkId=394414)。

## 如何執行此範例

開始使用相當簡單！ 若要執行此範例，您需要：

- Visual Studio 2013
- 網際網路連線
- Azure 訂用帳戶 ([免費試用](https://account.windowsazure.com/organization)已足夠)

每個 Azure 訂用帳戶都有相關聯的 Azure Active Directory (Azure AD) 租用戶。此範例所使用的所有 Azure AD 功能都可供免費使用。

## 複製或下載此儲存機制

從您的殼層或命令列：`git clone https://github.com/AzureADSamples/SinglePageApp-WebAPI-AngularJS-DotNet.git`

## 向 Azure Active Directory 租用戶註冊 To Go API 服務

1. 登入 [Azure 管理入口網站](https://manage.windowsazure.com)。
2. 在左側導覽中，按一下 [**Active Directory**]。
3. 按一下您要註冊範例應用程式的目錄。
4. 按一下 [**應用程式**] 索引標籤。
5. 在下拉式清單中，按一下 [**新增**]。
6. 按一下 [Add an application my organization is developing]。
7. 輸入應用程式的易記名稱 (例如 "To Go API")，並選取 [**Web 應用程式**] 和 (或) [**Web API**]，然後按 [**下一步**]。
8. 在 [登入 URL] 中，輸入範例的基礎 URL，預設為 `https://localhost:44327/`。
9. 針對應用程式識別碼 URI，請輸入 `https://<your_directory_name>/ToGoAPI`，並將 `<your_directory_name>` 取代為您 Azure AD 目錄的名稱。儲存組態。

全部完成了！ 繼續進行下一步之前，您需要尋找您的 API 的應用程式識別碼 URI。

1. 在 Azure 入口網站中時，按一下您應用程式的 [**設定**] 索引標籤。
2. 找到應用程式識別碼 URI 值，並將它複製到剪貼簿。

## 設定 To Go API 來使用您的 Azure Active Directory 租用戶

1. 在 Visual Studio 2013 中開啟方案。
2. 在 ToGoAPI 專案中，開啟 `web.config` 檔案。
3. 尋找應用程式金鑰 `ida:Tenant`，並將該值取代為您的 Azure AD 租用戶名稱。
4. 尋找應用程式金鑰 `ida:Audience`，並將該值取代為您從 Azure 入口網站複製的應用程式識別碼 URI。
5. 同樣在 ToGoAPI 專案中，開啟檔案 `Controllers/ToGoListController.cs`。在 `[EnableCors...]` 屬性中，輸入 To Do SPA 用戶端的位置。根據預設，它是 `https://localhost:44326`。請確定省略結尾斜線。
5. 在 TodoSPA 專案中，開啟檔案 `App/Scripts/App.js` 並找出 `endpoints` 物件的宣告。
6. 輸入 To Go API 端點位置的對應，其資源識別碼或應用程式識別碼 URI。`endpoints` 物件的屬性名稱應該是 To Go API 的位置。根據預設，它是 `https://localhost:44327/`。這個屬性的值應該是您從入口網站中複製的應用程式識別碼 URI，例如 `https://<your_tenant_name>/ToGoAPI`。
8. 別擔心此檔案中的其他組態值，我們很快會加以說明。
9. 同樣在 TodoSPA 專案中，開啟檔案 `App/Scripts/toGoListSvc.js`。將 `apiEndpoint` 變數的值取代為您的 To Go API 的位置。根據預設，它是 `https://localhost:44327/`。

## 向 Azure Active Directory 租用戶註冊 To Do 單頁應用程式

1. 重新登入 [Azure 管理入口網站](https://manage.windowsazure.com)。
2. 在左側導覽中，按一下 [**Active Directory**]。
3. 按一下您要註冊範例應用程式的租用戶。
4. 按一下 [**應用程式**] 索引標籤。
5. 在下拉式清單中，按一下 [**新增**]。
6. 按一下 [Add an application my organization is developing]。
7. 輸入應用程式的易記名稱 (例如 "To Do SPA")，並選取 [**Web 應用程式] 和/或 [Web API**]，然後按 [**下一步**]。
8. 在 [登入 URL] 中，輸入範例的基礎 URL，預設為 `https://localhost:44326/`。
9. 針對應用程式識別碼 URI，請輸入 `https://<your_directory_name>/ToDoSPA`，並將 `<your_directory_name>` 取代為您 Azure AD 目錄的名稱。
10. 在 [**其他應用程式的權限**] 區段中，按一下 [**新增應用程式**]。在 [**顯示**] 下拉式清單中選取 [**其他**]，然後按一下上方的核取記號。找到並按一下 To Go API，然後按一下底部的核取記號，以新增應用程式。從 [**委派的權限**] 下拉式清單中選取 [**存取 To Go API**]，並儲存組態。

全部完成了！ 繼續進行下一步之前，您需要尋找您的應用程式的用戶端識別碼。

1. 在 Azure 入口網站中時，按一下您應用程式的 [**設定**] 索引標籤。
2. 找到用戶端識別碼值，並將它複製到剪貼簿。


## 為您的應用程式啟用 OAuth2 隱含授權

根據預設，在 Azure AD 中佈建的應用程式並未啟用以使用 OAuth2 隱含授權。為執行這個範例，您必須明確選擇加入。

1. 從先前步驟中，您的瀏覽器應該仍然在 Azure 管理入口網站上，具體而言，顯示您應用程式項目的 [**設定**] 索引標籤。
2. 使用下拉清單中的 [**管理資訊清單**] 按鈕，下載應用程式的資訊清單檔案，並將它儲存到磁碟。
3. 使用文字編輯器開啟資訊清單檔案。搜尋 `oauth2AllowImplicitFlow` 屬性。您會發現它設定為 `false`；將它變更為 `true` 並儲存檔案。
4. 使用 [**管理資訊清單**] 按鈕，上傳更新的資訊清單檔案。儲存應用程式的組態。

## 設定 To Do SPA 來使用您的 Azure Active Directory 租用戶

1. 在 Visual Studio 2013 中開啟方案。
2. 在 TodoSPA 專案中，開啟 `web.config` 檔案。
3. 尋找應用程式金鑰 `ida:Tenant`，並將該值取代為您的 Azure AD 目錄名稱。
4. 尋找應用程式金鑰 `ida:Audience`，並將該值取代為來自 Azure 入口網站的用戶端識別碼。
5. 同樣在 TodoSPA 專案中，開啟檔案 `App/Scripts/App.js` 並找出文字行 `adalAuthenticationServiceProvider.init(`。
6. 將 `tenant` 的值取代為您的 Azure AD 目錄名稱。
7. 將 `clientId` 的值取代為來自 Azure 入口網站的用戶端識別碼。

## 執行範例

清理方案、重建方案並執行它。

您可以觸發登入體驗，方法是按一下右上角的登入連結，或直接按一下 [**To Do List**] 或 [**To Go List**] 索引標籤。藉由登入、新增項目至待辦清單、移除使用者帳戶並重新開始來探索範例。新增位置至 To Go List 中，使用 CORS 對 To Go API 執行 CRUD 作業。

## 如何將此範例部署至 Azure

若要將 To Do SPA 和 To Go API 部署至 Azure 網站，您將建立兩個網站、將每個專案發行至網站，以及更新這兩個專案，以參考新位置，而非 IIS Express。

### 建立 To Go API Azure 網站

1. 登入 [Azure 管理入口網站](https://manage.windowsazure.com)。
2. 在左側導覽中，按一下 [**網站**]。
3. 在左下角按一下 [**新增**]、選取 [**計算**] > [**網站**] > [**自訂建立**]、選取主控方案和區域，並提供您的網站的名稱 (例如 togo-contoso.azurewebsites.net)。選取要使用的資料庫，或建立新資料庫。按一下 [建立網站]。
4. 建立網站之後，按一下網站來加以管理。在這組步驟中，下載 .publishsettings 檔案，並加以儲存。也可以使用其他部署機制，例如從原始檔控制。如需使用 .publishsettings 檔案的詳細資訊，請參閱[如何：連線至訂用帳戶](../install-configure-powershell.md#Connect)。

### 建立 To Do SPA Azure 網站

1. 瀏覽至 [Azure 管理入口網站](https://manage.windowsazure.com)。
2. 在左側導覽中，按一下 [**網站**]。
3. 在左下角按一下 [**新增**]、選取 [**計算**] > [**網站**] > [**自訂建立**]、選取主控方案和區域，並提供您的網站的名稱 (例如 todo-contoso.azurewebsites.net)。選取要使用的資料庫；與 To Go API 相同的資料庫即可。按一下 [建立網站]。
4. 建立網站之後，按一下網站來加以管理。同樣地，下載此站台的 .publishsettings 檔案，並加以儲存。

### 更新兩個專案以使用 Azure 網站

1. 在 Visual Studio 中，移至 TodoSPA 專案。
2. 需要兩項變更。在 `App\Scripts\app.js` 中，將 `endpoints` 物件的屬性名稱取代為 To Go API 的新位置，例如 `https://togo-contoso.azurewebsites.net/`。在 `App\Scripts\toGoListSvc.js` 中，將 `apiEndpoint` 變數取代為相同的值。
3. 在 ToGoAPI 專案中，只需進行一項變更。在 `Controllers\ToGoListController.cs` 中，更新 `[EnableCors...]` 屬性，以反映 To Do SPA 的新位置，例如 `https://todo-contoso.azurewebsites.net`。同樣地，請確定省略結尾斜線。

### 將 To Go API 發行至 Azure 網站

1. 切換至 Visual Studio，並移至 ToGoAPI 專案。在 [方案總管] 中，以滑鼠右鍵按一下專案，再選取 [**發行**]。按一下 [**匯入**]，並匯入您所下載的 To Go API 發行設定檔。
6. 在 [**連線**] 索引標籤上，更新目的地 URL，使得它是 https (例如 https://togo-constoso.azurewebsites.net)。按 [下一步]。
7. 在 [**設定**] 索引標籤上，請確定「未」選取 [**啟用組織驗證**]。按一下 [發行]。
8. Visual Studio 將發行專案，並自動開啟瀏覽器到專案的 URL。如果您看到專案的預設 Web 網頁，發行即成功。

### 將 To Do SPA 發行至 Azure 網站

1. 切換至 Visual Studio，並移至 TodoSPA 專案。在 [方案總管] 中，以滑鼠右鍵按一下專案，再選取 [**發行**]。按一下 [**匯入**]，並匯入您所下載的 To Do SPA .publishsettings 檔案。
6. 在 [**連線**] 索引標籤上，更新目的地 URL，使得它是 https (例如 https://todo-contoso.azurewebsites.net)。按 [下一步]。
7. 在 [**設定**] 索引標籤上，請確定「未」選取 [**啟用組織驗證**]。按一下 [發行]。
8. Visual Studio 將發行專案，並自動開啟瀏覽器到專案的 URL。如果您看到專案的預設 Web 網頁，發行即成功。

### 在目錄租用戶中更新 To Do SPA 組態

1. 瀏覽至 [Azure 管理入口網站](https://manage.windowsazure.com)。
2. 在左側導覽中按一下 **Active Directory**，並選取您的租用戶。
3. 在 [**應用程式**] 索引標籤上，選取 [**To Do SPA**] 應用程式。
4. 在 [**設定**] 索引標籤上，將 [**登入 URL**] 和 [**回覆 URL**] 欄位更新為您的 SPA 的位址，例如 https://todo-contoso.azurewebsites.net。儲存組態。

## 關於程式碼

包含驗證邏輯的重要檔案如下所示：

- **App.js** - 插入 ADAL 模組相依性、提供 ADAL 用來推動與 Azure AD 之間的通訊協定互動的應用程式組態值，並指出若沒有先前的驗證不應該存取哪些路由。

- **index.html** - 包含 adal.js 的參考

- **HomeController.js** - 示範如何利用 ADAL 中的 login() 和 logOut() 方法。

- **UserDataController.js** - 示範如何從快取的 id_token 擷取使用者資訊。

特別感謝 @matvelloso 協助建立本教學課程。


## 後續步驟

以下是一些額外的資源，有助於使用 Azure AD 將驗證和授權加入 Web 應用程式及 Web API。

+ [Azure Active Directory 程式碼範例](https://msdn.microsoft.com/library/azure/dn646737.aspx)
+ [Azure AD 的驗證案例](https://msdn.microsoft.com/library/azure/dn499820.aspx)
 

<!---HONumber=62-->