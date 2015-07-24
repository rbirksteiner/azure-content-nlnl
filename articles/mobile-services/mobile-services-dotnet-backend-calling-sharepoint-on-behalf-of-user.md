<properties 
	pageTitle="代表使用者存取 SharePoint | 行動開發人員中心" 
	description="了解如何代表使用者電洽 SharePoint" 
	documentationCenter="" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="04/13/2015" 
	ms.author="mahender"/>

# 代表使用者存取 SharePoint

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>本主題說明如何代表目前登入的使用者存取 SharePoint API。</p>
<p>如果想要看影片，右邊片段播放的步驟與本教學課程相同。在此影片中，Mat Velloso 會引導您更新 Windows 市集應用程式，以與 SharePoint Online 互動。</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Azure-Mobile-Services-AAD-O365-Authentication-identity-across-services" target="_blank" class="label">觀看教學課程</a> <a style="background-image: url('http://media.ch9.ms/ch9/f217/3f8cbf94-f36b-4162-b3da-1c00339ff217/AzureMobileServicesAADO365AuthenticationIdentityA_960.jpg') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Azure-Mobile-Services-AAD-O365-Authentication-identity-across-services" target="_blank" class="dev-onpage-video"><span class="icon">播放影片</span></a> <span class="time">下午 12:51:00</span></div>
</div>

在本教學課程中，您會從「使用 Active Directory Authentication Library 單一登入驗證您的應用程式」教學課程更新應用程式，以在新增 TodoItem 時，在 SharePoint Online 中建立 Word 文件。

本教學課程會逐步引導您完成這些基本步驟，以啟用對 SharePoint 的代理存取：

1. [針對 SharePoint 的委派存取註冊您的應用程式]
2. [將 SharePoint 資訊新增至您的行動服務]
3. [取得存取權杖及呼叫 SharePoint API]
4. [建立及上傳 Word 文件]
5. [測試應用程式]

本教學課程需要下列各項：

* 執行於 Windows 8.1 的 Visual Studio 2013
* 作用中 [SharePoint Online] 訂閱
* 完成[使用 Active Directory Authentication Library 單一登入驗證您的應用程式]教學課程。您應使用 SharePoint 訂閱所提供的租用戶。

## <a name="configure-permissions"></a>針對 SharePoint 的委派存取設定您的應用程式
根據預設，您從 AAD 取得的權杖僅具有有限權限。若要存取第三方資源或 SaaS 應用程式 (例如 SharePoint Online)，您必須明確允許此類存取。

1. 在 **Azure 管理入口網站**的 [Active Directory][] 區段中，選取您的租用戶。導覽至您為行動服務建立的 Web 應用程式。

    ![][0]

2. 在 [設定] 索引標籤中，將頁面向下捲動至「其他應用程式的權限」區段。選取 [Office 365 SharePoint Online]，然後授與 [編輯或刪除使用者的檔案] 委派權限。然後按一下 [儲存]。

    ![][1]

您現在已設定由 AAD 簽發 SharePoint 存取權杖給行動服務。

## <a name="store-credentials"></a>將 SharePoint 資訊新增至您的行動服務

若要呼叫 SharePoint，您必須指定行動服務需要聯繫到的端點。您還必須能夠證明行動服務的身分識別。此動作須使用用戶端識別碼和用戶端密碼的配對來完成。您已在 AAD 登入設定期間取得並儲存行動服務的用戶端識別碼。由於這是機密認證，因此不應以純文字的形式儲存在我們的程式碼中。您應將這些值設為行動服務的應用程式設定。

1. 返回租用戶的 [AAD 應用程式] 索引標籤，然後為您的行動服務選取 Web 應用程式。

2. 在 [設定] 下，向下捲動至 [金鑰]。在產生新的金鑰後，您會取得用戶端密碼。請注意，在您建立金鑰並退出頁面後，即無法再從入口網站存取此金鑰。您必須在建立後，將此值複製並儲存在安全的位置中。選取金鑰的持續時間，然後按一下 [儲存]，並將產生的值複製到他處。

    ![][2]

3. 在 [管理入口網站] 的 [行動服務] 區段中，導覽至 [設定] 索引標籤，然後向下捲動至 [應用程式設定]。在此處您可以提供金鑰值組，以便參考所需的認證。

    ![][3]

4. 將 SP_Authority 設為 AAD 租用戶的授權端點。此項目應與您的用戶端應用程式所使用的授權值相同。其格式會是 https://login.windows.net/contoso.onmicrosoft.com

5. 將 SP_ClientSecret 設為您先前取得的用戶端密碼值。

6. 將 SP_SharePointURL 設為 SharePoint 網站的 URL。其格式應為 https://contoso-my.sharepoint.com

您將可使用 ApiServices.Settings 在我們的程式碼中再次取得這些值。

## <a name="obtain-token"></a>取得存取權杖及呼叫 SharePoint API

若要存取 SharePoint，您必須要以 SharePoint 的特殊存取權杖作為目標對象。若要取得此權杖，您必須使用行動服務的身分識別和為使用者簽發的權杖，來回呼 AAD。

1. 在 Visual Studio 中，開啟您的行動服務後端專案。

[AZURE.INCLUDE [mobile-services-dotnet-adal-install-nuget](../../includes/mobile-services-dotnet-adal-install-nuget.md)]

2. 在您的行動服務後端專案中，建立名為 SharePointUploadContext 的新類別。在其中新增下列項目：

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

    ![][4]


<!-- Images. -->

[0]: ./media/mobile-services-dotnet-backend-calling-sharepoint-on-behalf-of-user/aad-web-application.png
[1]: ./media/mobile-services-dotnet-backend-calling-sharepoint-on-behalf-of-user/aad-sharepoint-permissions.png
[2]: ./media/mobile-services-dotnet-backend-calling-sharepoint-on-behalf-of-user/aad-manage-secret-key.png
[3]: ./media/mobile-services-dotnet-backend-calling-sharepoint-on-behalf-of-user/mobile-services-app-settings-sharepoint.png
[4]: ./media/mobile-services-dotnet-backend-calling-sharepoint-on-behalf-of-user/sharepoint-document-created.png

<!-- Anchors. -->

[針對 SharePoint 的委派存取註冊您的應用程式]: #configure-permissionss
[將 SharePoint 資訊新增至您的行動服務]: #store-credentials
[取得存取權杖及呼叫 SharePoint API]: #obtain-token
[建立及上傳 Word 文件]: #create-document
[測試應用程式]: #test-application

<!-- URLs. -->
[]: https://manage.windowsazure.com/
[SharePoint Online]: http://office.microsoft.com/sharepoint/
[使用 Active Directory Authentication Library 單一登入驗證您的應用程式]: http://azure.microsoft.com/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication/
 

<!---HONumber=July15_HO2-->