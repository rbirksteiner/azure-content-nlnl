<properties
	pageTitle="將行動應用程式連接到企業 SaaS | 行動開發人員中心"
	description="了解如何呼叫 SharePoint Online 之類的企業資源"
	documentationCenter=""
	authors="mattchenderson"
	manager="dwrede"
	editor="na"
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="get-started-article" 
	ms.date="06/19/2015"
	ms.author="mahender"/>

# 將您的行動應用程式連接到 SaaS API

在本教學課程中，您會將行動應用程式連接到企業的軟體即服務 (SaaS) 解決方案。您會從[使用 Active Directory Authentication Library 單一登入驗證您的應用程式]更新應用程式，以在每次新增 TodoItem 時，在 SharePoint Online 中建立 Word 文件。

本教學課程需要下列各項：

* 執行於 Windows 8.1 的 Visual Studio 2013
* 作用中 [SharePoint Online] 訂閱
* 完成[使用 Active Directory Authentication Library 單一登入驗證您的應用程式]教學課程。您應使用 SharePoint 訂閱所提供的租用戶。

## <a name="configure-permissions"></a>針對 SharePoint 的委派存取設定您的應用程式
根據預設，您從 AAD 取得的權杖僅具有有限權限。若要存取第三方資源或 SaaS 應用程式 (例如 SharePoint Online)，您必須明確允許此類存取。

1. 在 **Azure 管理入口網站**的 [Active Directory][] 區段中，選取您的租用戶。導覽至您為應用程式服務建立的 Web 應用程式。

2. 在 [設定] 索引標籤中，將頁面向下捲動至「其他應用程式的權限」區段。選取 [Office 365 SharePoint Online]，然後授與 [編輯或刪除使用者的檔案] 委派權限。然後按一下 [儲存]。

    ![][1]

您現在已設定由 AAD 簽發 SharePoint 存取權杖給應用程式服務。

## <a name="store-credentials"></a>將 SharePoint 資訊新增至您的行動應用程式

若要呼叫 SharePoint，您必須指定行動應用程式需要聯繫到的端點。您還必須能夠證明應用程式服務的身分識別。此動作須使用用戶端識別碼和用戶端密碼的配對來完成。您已在 AAD 登入設定期間取得並儲存應用程式服務的用戶端識別碼。由於這是機密認證，因此不應以純文字的形式儲存在我們的程式碼中。您應將這些值設為行動應用程式程式碼套件的應用程式設定。

1. 返回租用戶的 [AAD 應用程式] 索引標籤，然後為您的應用程式服務選取 Web 應用程式。

2. 在 [設定] 下，向下捲動至 [金鑰]。在產生新的金鑰後，您會取得用戶端密碼。請注意，在您建立金鑰並退出頁面後，即無法再從入口網站存取此金鑰。您必須在建立後，將此值複製並儲存在安全的位置中。選取金鑰的持續時間，然後按一下 [儲存]，並將產生的值複製到他處。

3. 在 [管理入口網站] 的 [行動應用程式程式碼] 區段中，導覽至 [設定] 索引標籤，然後向下捲動至 [應用程式設定]。在此處您可以提供金鑰值組，以便參考所需的認證。

* 將 SP_Authority 設為 AAD 租用戶的授權端點。此項目應與您的用戶端應用程式所使用的授權值相同。其格式會是 `https://login.windows.net/contoso.onmicrosoft.com`

* 將 SP_ClientSecret 設為您先前取得的用戶端密碼值。

* 將 SP_SharePointURL 設為 SharePoint 網站的 URL。其格式應為 `https://contoso-my.sharepoint.com`

您將可使用 ApiServices.Settings 在您的程式碼中再次取得這些值。

## <a name="obtain-token"></a>取得存取權杖及呼叫 SharePoint API

若要存取 SharePoint，您必須要以 SharePoint 的特殊存取權杖作為目標對象。若要取得此權杖，您必須使用應用程式服務的身分識別和為使用者簽發的權杖，來回呼 AAD。

1. 在 Visual Studio 中開啟您的行動應用程式程式碼專案。

[AZURE.INCLUDE [app-service-mobile-dotnet-adal-install-nuget](../../includes/app-service-mobile-dotnet-adal-install-nuget.md)]

2. 在 NuGet 封裝管理員中，按一下 [**線上**]。輸入 **Microsoft.Azure.Mobile.Server.AppService** 作為搜尋字詞。然後按一下 [**安裝**] 以安裝 [Mobile Apps .NET Backend App Service Extension] 封裝。此封裝提供延伸方法，以便處理目前已登入使用者的相關資訊。

2. 在您的行動應用程式程式碼專案中，建立名為 SharePointUploadContext 的新類別。將 `using Microsoft.Azure.Mobile.Server.AppService;` 陳述式新增至檔案。然後，在類別中新增下列程式碼：

        private String accessToken;
        private String mySiteApiPath;
        private String clientId;
        private String clientSecret;
        private String sharepointURL;
        private String authority;
        private const string getFilesPath = "/getfolderbyserverrelativeurl('Documents')/Files";

        public static async Task<SharePointUploadContext> createContext(ServiceUser user, ServiceSettingsDictionary settings)
        {
            //Get the current access token
            AzureActiveDirectoryCredentials creds = (await user.GetIdentitiesAsync()).OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
            string userToken = creds.AccessToken;
            return new SharePointUploadContext(userToken, settings);
        }

        private SharePointUploadContext(string userToken, ServiceSettingsDictionary settings)
        {
            //Call ADAL and request a token to SharePoint with the access token
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = ac.AcquireToken(sharepointURL, new ClientCredential(clientId, clientSecret), new UserAssertion(userToken));
            accessToken = ar.AccessToken;
            string upn = ar.UserInfo.UserId;
            mySiteApiPath = "/personal/" + upn.Replace('@','_').Replace('.','_') + "/_api/web";
            clientId = settings.AzureActiveDirectoryClientId;
            clientSecret = settings["SP_ClientSecret"];
            sharepointURL = settings["SP_SharePointURL"];
            authority = settings["SP_Authority"];
        }

3. 現在，請建立將檔案新增至使用者文件庫的方法：

        public async Task<bool> UploadDocument(string docName, byte[] document)
        {
            string uploadUri = sharepointURL + mySiteApiPath + getFilesPath + string.Format(@"/Add(url='{0}.docx', overwrite=true)", docName);
            using (HttpClient client = new HttpClient())
            {
                Func<HttpRequestMessage> requestCreator = () =>
                {
                    HttpRequestMessage UploadRequest = new HttpRequestMessage(HttpMethod.Post, uploadUri);
                    UploadRequest.Content = new System.Net.Http.ByteArrayContent(document);
                    UploadRequest.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                    UploadRequest.Content.Headers.ContentType.Parameters.Add(new NameValueHeaderValue("odata", "verbose"));
                    return UploadRequest;
                };
                using (HttpRequestMessage uploadRequest = requestCreator.Invoke())
                {
                    uploadRequest.Headers.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
                    HttpResponseMessage uploadResponse = await client.SendAsync(uploadRequest);
                }
            }
            return true;
        }

## <a name="create-document"></a>建立及上傳 Word 文件

若要建立 Word 文件，您將會使用 OpenXML NuGet 封裝。請開啟 NuGet Manager 並搜尋 DocumentFormat.OpenXml，然後安裝此封裝。

1. 將下列程式碼新增至 TodoItemController。這將會根據 TodoItem 建立 Word 文件。文件的文字將會是項目的名稱。

        private static byte[] CreateWordDocument(TodoItem todoItem)
        {
            byte[] document;
            using (MemoryStream generatedDocument = new MemoryStream())
            {
                using (WordprocessingDocument package = WordprocessingDocument.Create(generatedDocument, WordprocessingDocumentType.Document))
                {
                    MainDocumentPart mainPart = package.MainDocumentPart;
                    if (mainPart == null)
                    {
                        mainPart = package.AddMainDocumentPart();
                        new Document(new Body()).Save(mainPart);
                    }
                    Body body = mainPart.Document.Body;
                    Paragraph p =
                        new Paragraph(
                            new Run(
                                new Text(todoItem.Text)));
                    body.Append(p);
                    mainPart.Document.Save();
                }
                document = generatedDocument.ToArray();
            }
            return document;
        }

2. 以下列程式碼取代 PostTodoItem：

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            SharePointUploadContext context = await SharePointUploadContext.createContext((ServiceUser)this.User, Services.Settings);
            byte[] document = CreateWordDocument(item);
            bool uploadResult = await context.UploadDocument(item.Id, document);

            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

## <a name="test-application"></a>測試應用程式

1. 將變更發行至後端，然後執行您的用戶端應用程式。在出現提示時登入，然後插入新的 TodoItem。

2. 導覽至 SharePoint 網站，並以相同的使用者身分登入。

3. 選取 [OneDrive] 索引標籤。在 [文件資料夾] 下，您應會看見具有 GUID 標題的 Word 文件。當您開啟此文件時，您應會看見 TodoItem 的文字。

<!-- Images. -->

[1]: ./media/app-service-mobile-dotnet-backend-get-started-connect-to-enterprise/aad-sharepoint-permissions.png

<!-- URLs. -->

[Preview Azure Management Portal]: https://portal.azure.com/
[]: https://manage.windowsazure.com/
[SharePoint Online]: http://office.microsoft.com/zh-tw/sharepoint/
[使用 Active Directory Authentication Library 單一登入驗證您的應用程式]: app-service-mobile-dotnet-backend-ios-aad-sso-preview.md
[Mobile Apps .NET Backend App Service Extension]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.AppService/
 

<!---HONumber=62-->