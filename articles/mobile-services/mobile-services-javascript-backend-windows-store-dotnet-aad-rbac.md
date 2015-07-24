<properties 
	pageTitle="行動服務和 Azure Active Directory 中的角色型存取控制 (Windows 市集) | 行動開發人員中心" 
	description="了解如何在 Windows Store 應用程式中控制以 Azure Active Directory 角色為基礎的存取權。" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/15/2015" 
	ms.author="wesmc"/>

# 行動服務和 Azure Active Directory 中的角色型存取控制

[AZURE.INCLUDE [mobile-services-selector-rbac](../../includes/mobile-services-selector-rbac.md)]

##概觀

角色型存取控制 (RBAC) 是將您的使用者可具備的權限指派給角色，而妥善定義哪些類別的使用者可執行哪些動作的實務作法。本教學課程將逐步解說如何將基本 RBAC 新增至 Azure 行動服務。

本教學課程將說明角色型存取控制，檢查每個使用者在 Azure Active Directory (AAD) 中定義之「銷售」群組的成員資格。存取檢查將在行動服務後端中，使用 JavaScript 和 Azure Active Directory 的[圖形 API] 來執行。只有屬於「銷售」角色的使用者能夠查詢資料。


>[AZURE.NOTE]本教學課程的目的是要擴充驗證知識以加入授權實務作法。您應先使用 Azure Active Directory 驗證提供者完成[將驗證新增至您的行動服務應用程式]教學課程。本教學課程接著將更新[將驗證新增至您的行動服務應用程式]教學課程中使用的 TodoItem 應用程式。

##必要條件

本教學課程需要下列各項：

* 執行於 Windows 8.1 的 Visual Studio 2013。
* 使用 Azure Active Directory 驗證提供者完成[將驗證新增至您的行動服務應用程式]教學課程。
* 完成[儲存伺服器指令碼]教學課程，以熟悉如何使用 Git 儲存機制來儲存伺服器指令碼。
 


##建立具有成員資格的銷售群組

[AZURE.INCLUDE [mobile-services-aad-rbac-create-sales-group](../../includes/mobile-services-aad-rbac-create-sales-group.md)]


##為整合的應用程式產生金鑰


在進行[將驗證新增至您的行動服務應用程式]教學課程期間，您已在完成[註冊使用 Azure Active Directory 登入]步驟時，為整合的應用程式建立註冊。在本節中，您將產生在使用該整合的應用程式用戶端識別碼讀取目錄資訊時所將使用的金鑰。

如果您已瀏覽[存取 Azure Active Directory Graph 資訊]教學課程，代表您已經完成此步驟，並可略過本節。

[AZURE.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../../includes/mobile-services-generate-aad-app-registration-access-key.md)]





##將檢查成員資格的共用指令碼新增至行動服務

在本節中，您會使用 Git 將名為 *rbac.js* 的共用指令碼檔案部署至您的行動服務。此共用指令碼檔案所包含的函數，會使用[圖形 API] 來檢視使用者的群組成員資格。

如果您不熟悉使用 Git 將指令碼部署至行動服務的作業，請先參閱[儲存伺服器指令碼]教學課程，再開始進行本節。

1. 在行動服務之本機儲存機制的 *./service/shared/* 目錄中，建立名為 *rbac.js* 的新指令碼檔案。
2. 將下列指令碼新增至定義 `getAADToken` 函數的檔案頂端。在給定 *tenant_domain*、整合的應用程式*用戶端識別碼*和應用程式*金鑰*的情況下，此函數會提供用來讀取目錄資訊的圖形存取權杖。

    >[AZURE.NOTE]您應快取權杖，而不要為每個存取檢查建立一個新權杖。接著，請在嘗試使用 401 Authentication_ExpiredToken 回應中的權杖結果 (如[圖形 API 錯誤參考]所說明) 時重新整理快取。為求單純性，下方的程式碼中並不會說明此作業，但這將可以減輕對您 Active Directory 的額外網路流量。

        var appSettings = require('mobileservice-config').appSettings;
        var tenant_domain = appSettings.AAD_TENANT_DOMAIN;
        var clientID = appSettings.AAD_CLIENT_ID;
        var key = appSettings.AAD_CLIENT_KEY;
        exports.SalesGroup = appSettings.AAD_GROUP_ID;
         
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


3. 將下列程式碼新增至 *rbac.js*，以定義 `getGroupId` 函數。此函數會使用存取權杖，根據篩選器中使用的群組名稱取得群組識別碼。
 
    >[AZURE.NOTE]此程式碼會依名稱查閱 Active Directory 群組。在許多情況下，將群組識別碼儲存為行動服務應用程式設定，並只使用該群組識別碼，會是較理想的作法。這是因為群組名稱可能會變更，但識別碼會保持相同。但是，當群組名稱變更時，角色的領域中通常至少會有一項變更可能也需要行動服務程式碼的更新。

        function getGroupId(groupname, accessToken, callback) {
            var req = require("request");
            var options = {
                url: "https://graph.windows.net/" + tenant_domain + "/groups" + 
                      "?$filter=displayName%20eq%20'" + groupname + "'" + 
	              "&api-version=2013-04-05" ,
                method: 'GET',
                headers: {
                    "Authorization": "Bearer " + accessToken,
                    "Content-Type": "application/json",
                }
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body).value[0].objectId);
            });
        }


4. 將下列程式碼新增至 *rbac.js*，以定義會呼叫圖形 REST API 之 [IsMemberOf] 端點的 `isMemberOf` 函數。

    此函數是圖形 REST API 的 [IsMemberOf] 端點附近的精簡型包裝函式。它會使用圖形存取權杖，根據群組識別碼檢查使用者的目錄物件識別碼在群組中是否具有成員資格。

        function isMemberOf(access_token, userObjectId, groupObjectId, callback) {
            var req = require("request");
            var options = {
                url: "https://graph.windows.net/" + tenant_domain + "/isMemberOf" + "?api-version=2013-04-05",
                method: 'POST',
                headers: {
                    "Authorization": "Bearer " + access_token,
                    "Content-Type": "application/json",
                },
                body: JSON.stringify({
                "groupId": groupObjectId,
                "memberId": userObjectId
                })
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body).value);
            });
        };

    

7. 將下列匯出的 `checkGroupMembership` 函數新增至 *rbac.js*。

    此函數會包裝其他指令碼函數的用法，並從要由其他指令碼呼叫以執行實際存取檢查的共用指令碼中匯出。在給定行動服務使用者物件和群組識別碼的情況下，指令碼將會擷取 Azure Active Directory 物件識別碼以取得使用者的身分識別，並驗證群組的成員資格。

        exports.checkGroupMembership = function (user, groupName, callback) {
            user.getIdentities({
                success: function (identities) {
                    var objectId = identities.aad.oid;
                    getAADToken(function (err, access_token) {
                        if (err) callback(err);
		        else getGroupId(groupName, access_token, function (err, groupId) { 
                            if (err) callback(err);
                            else isMemberOf(access_token, objectId, groupId, function (err, isInGroup) {
       	                        if (err) errorHandler(err);
               	                else callback(null, isInGroup);
                            });
                        });
                    });
                },
                error: callback
            });
        }

8. 將您的變更儲存至 *rbac.js*。

##將角色型存取檢查新增至資料庫作業


當您完成[將驗證新增至您的行動服務應用程式]教學課程，您應該已將資料表作業設定為需要驗證，如下所示。

![][3]

透過需要驗證的每個資料庫作業，我們可以新增會將使用者物件用於存取檢查的指令碼。

下列步驟說明如何使用指令碼將角色型存取控制部署至行動服務中的每個資料表作業。對於每個資料表作業，都會執行使用共用 *rbac.js* 指令碼的指令碼。

1. 在行動服務之本機 Git 儲存機制的 *./service/table/* 目錄中，新增名為 *todoitem.insert.js* 的指令碼檔案。請將下列指令碼貼到該檔案中。

        function insert(item, user, request) {
        
            var RBAC = require('../shared/rbac.js');
        
            RBAC.checkGroupMembership(user, "sales", function(err, isInGroup) {
                if (err) request.respond(err);
                else if (!isInGroup) request.respond(statusCodes.UNAUTHORIZED, null);
                else {
                    request.execute();
                }
            });
        }

2. 在行動服務之本機 Git 儲存機制的 *./service/table/* 目錄中，新增名為 *todoitem.read.js* 的指令碼檔案。請將下列指令碼貼到該檔案中。

        function read(query, user, request) {
        
            var RBAC = require('../shared/rbac.js');
        
            RBAC.checkGroupMembership(user, "sales", function(err, isInGroup) {
                if (err) request.respond(err);
                else if (!isInGroup) request.respond(statusCodes.UNAUTHORIZED, null);
                else {
                    request.execute();
                }
            });
        }

3. 在行動服務之本機 Git 儲存機制的 *./service/table/* 目錄中，新增名為 *todoitem.update.js* 的指令碼檔案。請將下列指令碼貼到該檔案中。

        function update(item, user, request) {
        
            var RBAC = require('../shared/rbac.js');
        
            RBAC.checkGroupMembership(user, "sales", function(err, isInGroup) {
                if (err) request.respond(err);
                else if (!isInGroup) request.respond(statusCodes.UNAUTHORIZED, null);
                else {
                    request.execute();
                }
            });
        }

4. 在行動服務之本機 Git 儲存機制的 *./service/table/* 目錄中，新增名為 *todoitem.delete.js* 的指令碼檔案。請將下列指令碼貼到該檔案中。

        function del(id, user, request) {
        
            var RBAC = require('../shared/rbac.js');
        
            RBAC.checkGroupMembership(user, "sales", function(err, isInGroup) {
                if (err) request.respond(err);
                else if (!isInGroup) request.respond(statusCodes.UNAUTHORIZED, null);
                else {
                    request.execute();
                }
            });
        }

5. 在您 Git 儲存機制的命令列介面中執行下列命令，為您所新增的指令碼檔案新增追蹤。

        git add .

6. 在您 Git 儲存機制的命令列介面中執行下列命令，以認可更新。

        git commit -m "Added role based access control to table operations"
  
7. 在您 Git 儲存機制的命令列介面中執行下列命令，以將本機 Git 儲存機制的更新部署至行動服務。此命令假設您已完成*主要*分支中的更新。

        git push origin master

8. 在 [Azure 管理入口網站]中，您應能夠導覽至行動服務的資料表作業，並在下方顯示的位置中檢視更新。

    ![][4]

##測試用戶端的存取

[AZURE.INCLUDE [mobile-services-aad-rbac-test-app](../../includes/mobile-services-aad-rbac-test-app.md)]







<!-- Images -->
[0]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/users.png
[1]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/group-membership.png
[2]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/sales-group.png
[3]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/table-perms.png
[4]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/insert-table-op-view.png
[5]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/sales-group-id.png
[6]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/client-id-and-key.png

<!-- URLs. -->
[將驗證新增至您的行動服務應用程式]: mobile-services-javascript-backend-windows-universal-dotnet-get-started-users.md
[How to Register with the Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[Azure 管理入口網站]: https://manage.windowsazure.com/
[Directory Sync Scenarios]: http://msdn.microsoft.com/library/azure/jj573653.aspx
[儲存伺服器指令碼]: mobile-services-store-scripts-source-control.md
[註冊使用 Azure Active Directory 登入]: mobile-services-how-to-register-active-directory-authentication.md
[圖形 API]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[圖形 API 錯誤參考]: http://msdn.microsoft.com/library/azure/hh974480.aspx
[IsMemberOf]: http://msdn.microsoft.com/library/azure/dn151601.aspx
[存取 Azure Active Directory Graph 資訊]: mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info.md

<!---HONumber=July15_HO2-->