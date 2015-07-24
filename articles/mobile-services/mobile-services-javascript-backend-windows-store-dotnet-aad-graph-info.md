<properties 
	pageTitle="存取 Azure Active Directory Graph 資訊 (Windows 市集) | 行動開發人員中心" 
	description="了解如何在 Windows 市集應用程式中使用 Graph API 存取 Azure Active Directory 資訊。" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="multiple" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/18/2015" 
	ms.author="wesmc"/>

# 存取 Azure Active Directory 圖形資訊


[AZURE.INCLUDE [mobile-services-selector-aad-graph](../../includes/mobile-services-selector-aad-graph.md)]
##概觀

如同其他隨行動服務提供的身分識別提供者，Azure Active Directory (AAD) 提供者也支援豐富的圖形 API，可用於對目錄的程式設計存取。在本教學課程中，您會使用[Graph REST API] 從目錄中擷取的其他使用者資訊來更新 ToDoList 應用程式，藉此個人化已驗證使用者的應用程式體驗。

如需 Azure AD Graph API 的詳細資訊，請參閱 [Azure Active Directory 圖形團隊部落格]。

>[AZURE.NOTE]本教學課程的目的是要擴充您使用 Azure Active Directory 進行驗證的知識。您應先使用 Azure Active Directory 驗證提供者，完成[將驗證新增至您的應用程式]教學課程。本教學課程接著會更新[將驗證新增至您的應用程式]教學課程中使用的 TodoItem 應用程式。



##必要條件 

在開始本教學課程之前，您必須已完成下列行動服務教學課程：

+ [將驗證新增至您的應用程式]<br/>將登入需求新增至 TodoList 範例應用程式。

+ [自訂 API 教學課程]<br/>說明如何呼叫自訂 API。



##在 AAD 中產生應用程式註冊的存取金鑰


在進行[將驗證新增至您的應用程式]教學課程期間，您已在完成[註冊使用 Azure Active Directory 登入]步驟時，為整合的應用程式建立註冊。在本節中，您將產生在使用該整合的應用程式用戶端識別碼讀取目錄資訊時所將使用的金鑰。

[AZURE.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../../includes/mobile-services-generate-aad-app-registration-access-key.md)]



##建立 GetUserInfo 自訂 API

在本節中，您將建立 GetUserInfo 自訂 API，以使用[圖形 REST API] 從 AAD 擷取使用者的其他相關資訊。

如果您未曾搭配使用自訂 API 與行動服務，您應考慮先參閱[自訂 API 教學課程]，再開始執行本節步驟。

1. 在 [Azure 管理入口網站]中，為您的行動服務建立新的 GetUserInfo 自訂 API，然後將 get 方法的參數設為 [僅限通過驗證的使用者]。

    ![][0]

2. 為新的 GetUserInfo API 開啟指令碼編輯器，並將下列變數移至指令碼的最上方。

        var appSettings = require('mobileservice-config').appSettings;
        var tenant_domain = appSettings.AAD_TENANT_DOMAIN;
        var clientID = appSettings.AAD_CLIENT_ID;
        var key = appSettings.AAD_CLIENT_KEY;



3. 為 `getAADToken` 函數新增下列定義。

        function getAADToken(callback) {
            var req = require("request");
            var options = {
                url: "https://login.windows.net/" + tenant_domain + "/oauth2/token?api-version=1.0",
                method: 'POST',
                form: {
                    grant_type: "client_credentials",
                    resource: "https://graph.windows.net",
                    client_id: clientID,
                    client_secret: key
                }
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body).access_token);
            });
        }

    在給定 *tenant_domain*、整合的應用程式*用戶端識別碼*和應用程式*金鑰*的情況下，此函數會提供用來讀取目錄資訊的圖形存取權杖。

4. 新增下列使用圖形 API 傳回使用者資訊的 `getUser` 函數。

        function getUser(access_token, objectId, callback) {
            var req = require("request");
            var options = {
                url: "https://graph.windows.net/" + tenant_domain + "/users/" + objectId + "?api-version=1.0",
                method: 'GET',
                headers: {
                    "Authorization": "Bearer " + access_token
                }
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body));
            });
        };

    此函數是圖形 REST API 的[取得使用者]端點附近的精簡型包裝函式。它會使用圖形存取權杖和使用者的物件識別碼，來取得使用者資訊。

5. 依照下列方式使用其他函數更新匯出的 `get` 方法，以傳回使用者資訊。

        exports.get = function (request, response) {
            if (request.user.level == 'anonymous') {
                response.send(statusCodes.UNAUTHORIZED, null);
                return;
            }
            var errorHandler = function (err) {
                console.log(err);
                response.send(statusCodes.INTERNAL_SERVER_ERROR, err);
            };
            request.user.getIdentities({
                success: function (identities) {
                    var objectId = identities.aad.oid;
                    getAADToken(function (err, access_token) {
                        if (err) errorHandler(err);
                        else getUser(access_token, objectId, function (err, user_info) {
                            if (err) errorHandler(err);
                            else {
                                console.log('GetUserInfo: ' + JSON.stringify(user_info, null, 4));
                                response.send(statusCodes.OK, user_info);
                              }
                            });
                    });
                },
                error: errorHandler
            });
        };


##更新應用程式以使用 GetUserInfo


在本節中，您會更新您在[開始使用驗證] 教學課程中實作的 `AuthenticateAsync` 方法，以呼叫自訂 API 並從 AAD 傳回使用者的其他相關資訊。

[AZURE.INCLUDE [mobile-services-aad-graph-info-update-app](../../includes/mobile-services-aad-graph-info-update-app.md)]


 


##測試應用程式

[AZURE.INCLUDE [mobile-services-aad-graph-info-test-app](../../includes/mobile-services-aad-graph-info-test-app.md)]




##後續步驟

在下一個教學課程[行動服務中使用 AAD 的角色型存取控制]中，您將會搭配使用角色型存取控制與 Azure Active Directory (AAD) 來檢查群組成員資格，然後允許存取。



<!-- Images -->
[0]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/create-getuserinfo.png


<!-- URLs. -->
[將驗證新增至您的應用程式]: ../mobile-services-windows-store-dotnet-get-started-users.md
[How to Register with the Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[Azure 管理入口網站]: https://manage.windowsazure.com/
[自訂 API 教學課程]: mobile-services-windows-store-dotnet-call-custom-api.md
[Store Server Scripts]: mobile-services-store-scripts-source-control.md
[註冊使用 Azure Active Directory 登入]: mobile-services-how-to-register-active-directory-authentication.md
[Graph API]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[Graph REST API]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[圖形 REST API]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[取得使用者]: http://msdn.microsoft.com/library/azure/dn151678.aspx
[行動服務中使用 AAD 的角色型存取控制]: mobile-services-javascript-backend-windows-store-dotnet-aad-rbac.md
[Azure Active Directory 圖形團隊部落格]: http://go.microsoft.com/fwlink/?LinkId=510536

<!---HONumber=July15_HO2-->