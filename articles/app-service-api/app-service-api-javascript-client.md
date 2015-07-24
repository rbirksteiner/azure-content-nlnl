<properties 
	pageTitle="利用 HTML 和 JavaScript 存取 Azure API 應用程式" 
	description="了解如何利用 HTML 和 JavaScript 存取 API 應用程式後端。" 
	services="app-service\api" 
	documentationCenter=".net"
	authors="bradygaster"
	manager="mohisri" 
	editor="tdykstra"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="JavaScript" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="bradygaster"/>

# 利用 HTML 和 JavaScript 取用 Azure API 應用程式

## 概觀

本文說明如何在 [Azure App Service](/documentation/services/app-service/) 中建立 [API 應用程式](app-service-api-apps-why-best-platform.md)的 HTML 和 JavaScript 用戶端。本文假設您已具備 HTML 和 JavaScript 的知識，並將使用 [AngularJS](https://angularjs.org/) JavaScript 架構對 API 應用程式進行 REST 呼叫。

在此之前先閱讀某些文件，可幫助您開始使用。

1. 在[建立 API 應用程式](app-service-dotnet-create-api-app.md)中，您會建立 API 應用程式專案。
2. 在[部署 API 應用程式](app-service-dotnet-deploy-api-app.md)中，您會將 API 應用程式部署至您的 Azure 訂用帳戶。
3. 在[偵錯 API 應用程式](../app-service-dotnet-remotely-debug-api-app.md)，您可使用 Visual Studio 遠端偵錯在 Azure 中執行的程式碼。

這篇文章以上述這些文章為基礎，示範 HTML 應用程式如何使用 JavaScript 來存取後端 API 應用程式。

## 啟用 CORS

一般而言，HTML 應用程式需要 CORS (跨來源資源共用)，其將由不同的主機而非 與 API 本身提供服務 。在 API 應用程式中，至少有兩個選項可啟用 CORS。本節將概述這些選項。

### 對 API 應用程式閘道啟用 CORS

API 應用程式閘道可設定為使用 Azure Preview 入口網站啟用 CORS。藉由新增 **MS_CrossDomainOrigins** *appSetting*，您可以指定允許呼叫 API 應用程式的 URL。本章將說明如何使用此 *appSetting* 在 API 閘道層級啟用 CORS。

1. 瀏覽至您要啟用 CORS 的 API 應用程式的 Azure 預覽入口網站刀鋒視窗。一旦抵達，請按一下 API 應用程式的*閘道* 圖示。 

	![按一下 [API 應用程式閘道] 按鈕](./media/app-service-api-javascript-client/19-api-app-blade.png)

1. 按一下入口網站刀鋒視窗中的 **閘道主機**連結。

	![按一下 [API 應用程式閘道主機] 連結](./media/app-service-api-javascript-client/20-gateway-host-blade.png)

1. 按一下入口網站刀鋒視窗中的 **所有設定**連結。

	![所有閘道設定連結](./media/app-service-api-javascript-client/21-gateway-blade-all-settings.png)

1. 按一下入口網站刀鋒視窗中的 **應用程式設定**按鈕。

	![閘道應用程式設定](./media/app-service-api-javascript-client/22-gateway-app-settings-blade.png)

1. 新增 **MS_CrossDomainOrigins** 應用程式設定。讓此設定的值成為您要提供 API 應用程式存取權的 HTTP 主機清單 (以逗號分隔)。如果您要提供多部主機的存取權，則可將 *appSetting* 的值設定為類似下列的程式碼。

		http://foo.azurewebsites.net, https://foo.azurewebsites.net, http://contactlistwebapp.azurewebsites.net

	下一節將逐步解說建立含有簡單 HTML 頁面的個別 Web 應用程式的程序，以從在與 API 應用程式相同的 Azure 資源群組中執行的 Web 應用程式呼叫 API 應用程式。由於這是唯一獲准存取 API 應用程式的主機，所以此設定將會設為包含一部主機。

		http://contactlistwebapp.azurewebsites.net

	下列螢幕擷取畫面示範您在 Azure 預覽入口網站中儲存此設定後呈現的畫面。

	![](./media/app-service-api-javascript-client/23-app-settings-set.png)

部落格文章 [Azure 行動服務 .NET 更新](http://azure.microsoft.com/blog/2014/07/28/azure-mobile-services-net-updates/)中有 **MS_CrossDomainOrigins** 應用程式設定的詳細討論，如需此設定的詳細資訊，請查看這篇文章。

### 在 Web API 程式碼中啟用 CORS

在 Web API 中啟用 CORS 的程序已詳載於 ASP.NET 文章 [在 ASP.NET Web API 2 中啟用跨來源要求](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api)。若為使用 ASP.NET Web API 建置的 API 應用程式，此程序完全一樣，但會在以下摘要說明。如果您已啟用 CORS，請略過這一節，因為您的 API 應用程式應該已正確設定。

1. CORS 功能是由 [Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/) NuGet 封裝提供。開啟 **Package Manager Console** 加以安裝，並執行下列 PowerShell 指令碼。 

		Install-Package Microsoft.AspNet.WebApi.Cors

1. 一旦命令執行時，Package Manager Console 和 **packages.config** 就會反映加入新的 NuGet 封裝。

	![方案總管中的 apiapp.json 和中繼資料](./media/app-service-api-javascript-client/01-cors-installed.png)

1. 開啟 *App_Start/WebApiConfig.cs* 檔案。將底下這行程式碼加入至檔案中 **WebApiConfig** 類別的 **Register** 方法。

		config.EnableCors();

	一旦更新檔案，程式碼應如下所示：

		public static class WebApiConfig
	    {
	        public static void Register(HttpConfiguration config)
	        {
	            // Web API configuration and services
	            
				config.EnableCors(); /* -- NEW CODE -- */
	
	            // Web API routes
	            config.MapHttpAttributeRoutes();
	
	            config.Routes.MapHttpRoute(
	                name: "DefaultApi",
	                routeTemplate: "api/{controller}/{id}",
	                defaults: new { id = RouteParameter.Optional }
	            );
	        }
	    }

1. 啟用 CORS 的最後一個步驟是區分您要啟用的個別動作方法。如下列程式碼所示，經由每個方法或在整個控制器上新增 **EnableCors** 屬性。

	> **注意**：將萬用字元使用於具有 EnableCors 屬性的所有參數僅供示範之用，並且將您的 API 開啟至所有來源和所有 HTTP 要求 。請謹慎使用這個屬性，並了解相關暗示。

		using ContactList.Models;
		using System.Collections.Generic;
		using System.Web.Http;
		using System.Web.Http.Cors;
	
		namespace ContactList.Controllers
		{
		    [EnableCors(origins:"*", headers:"*", methods: "*")] /* -- NEW CODE -- */
		    public class ContactsController : ApiController
		    {
		        [HttpGet]
		        public IEnumerable<Contact> Get()
		        {
		            return new Contact[]
		            {
		                new Contact { Id = 1, EmailAddress = "barney@contoso.com", Name = "Barney Poland"},
		                new Contact { Id = 2, EmailAddress = "lacy@contoso.com", Name = "Lacy Barrera"},
		                new Contact { Id = 3, EmailAddress = "lora@microsoft.com", Name = "Lora Riggs"}
		            };
		        }
		
		        [HttpPost]
		        public Contact Post([FromBody] Contact contact)
		        {
		            return contact;
		        }
		    }
		}
1. 如果您已在加入 CORS 支援之前，先將 API 應用程式部署至 Azure，請重新部署程式碼，以便在 Azure 代管的 API 中啟用 CORS。 

## 建立 Web 應用程式以取用 API 應用程式

在本節中，將會建立新的空白 Web 應用程式、在其中安裝和使用 AngularJS，以及將簡單的 HTML 前端繫結至 APPI 應用程式。您會將使用中的 Web 應用程式部署至 Azure App Service。HTML Web 應用程式將繫結至並顯示從 API 應用程式擷取的資料，以及提供 Contacts API 的簡單 UI 給使用者。

1. 以滑鼠右鍵按一下您稍早於[建立 API 應用程式](app-service-dotnet-create-api-app.md)中建立的解決方案，然後依序選取 [新增] -> [新增專案]

	![方案總管中的 apiapp.json 和中繼資料](./media/app-service-api-javascript-client/02-add-project.png)

1. 選取 [**ASP.NET Web 應用程式**] 範本。

	![方案總管中的 apiapp.json 和中繼資料](./media/app-service-api-javascript-client/03-new-web-project.png)

1. 選取 One ASP.NET 對話方塊中的 [**空白**] 範本。

	![方案總管中的 apiapp.json 和中繼資料](./media/app-service-api-javascript-client/04-empty-web.png)

1. 使用 [**封裝管理員**] 或使用 [**管理 NuGet 封裝**] 內容功能表項目，安裝 [AngularJS](https://www.nuget.org/packages/angularjs) NuGet 封裝。

	![方案總管中的 apiapp.json 和中繼資料](./media/app-service-api-javascript-client/05-install-angular.png)

1. 使用 [**封裝管理員**] 或使用 [**管理 NuGet 封裝**] 內容功能表項目，安裝 [Bootstrap](https://www.nuget.org/packages/bootstrap) NuGet 封裝。

	![方案總管中的 apiapp.json 和中繼資料](./media/app-service-api-javascript-client/05-install-bootstrap.png)

1. 將新的 HTML 檔案加入至 Web 應用程式專案。

	![方案總管中的 apiapp.json 和中繼資料](./media/app-service-api-javascript-client/06-new-html-file.png)

1. 將檔案命名為 *index.html*。

	![方案總管中的 apiapp.json 和中繼資料](./media/app-service-api-javascript-client/07-index-html.png)

1. 將 Bootstrap CSS 和 AngularJS JavaScript 檔案新增至 HTML 網頁，以及使用簡單的 Bootstrap 範本 ([如同這一個](http://getbootstrap.com/examples/starter-template/)) 並建立空的指令碼標記來準備網頁。
	
	> 注意：下列 HTML 和 JavaScript 程式碼中的註解是這一節中後續步驟的起頭。

		<!DOCTYPE html>
		<html xmlns="http://www.w3.org/1999/xhtml">
		<head>
		    <title>Contacts HTML Client</title>
		    <link href="Content/bootstrap.css" rel="stylesheet" />
		    <style type="text/css">
		        body {
		            margin-top: 60px;
		        }
		    </style>
		</head>
		<body>
		
		    <nav class="navbar navbar-inverse navbar-fixed-top">
		        <div class="container">
		            <div class="navbar-header">
		                <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#navbar" aria-expanded="false" aria-controls="navbar">
		                    <span class="sr-only">Toggle navigation</span>
		                    <span class="icon-bar"></span>
		                    <span class="icon-bar"></span>
		                    <span class="icon-bar"></span>
		                </button>
		                <a class="navbar-brand" href="#">Contacts</a>
		            </div>
		            <div id="navbar" class="collapse navbar-collapse">
		                <ul class="nav navbar-nav"></ul>
		            </div>
		        </div>
		    </nav>
		
		    <div class="container">
		        <!-- contacts ui here -->
		    </div>
		
		    <script src="Scripts/angular.js" type="text/javascript"></script>
		    <script type="text/javascript">
		        /* client javascript code here */
		    </script>
		
		</body>
		</html>

1. 將下面所示的 HTML 資料表程式碼新增至 HTML 中的 **container** *div* 元素。

		<!-- contacts ui here -->
        <table class="table table-striped" ng-app="myApp" ng-controller="contactListCtrl">
            <thead>
                <tr>
                    <th>ID</th>
                    <th>Name</th>
                    <th>Email</th>
                    <th></th>
                </tr>
            </thead>
            <tbody>
                <tr ng-repeat="con in contacts">
                    <td>[[con.Id]]</td>
                    <td>[[con.Name</td>
                    <td>[[con.EmailAddress]]</td>
                    <td></td>
                </tr>
            </tbody>
            <tfoot>
                <tr>
                    <th>Create a new Contact</th>
                    <th colspan="2">API Status: [[status]]</th>
                    <th><button class="btn btn-sm btn-info" ng-click="refresh()">Refresh</button></th>
                </tr>
                <tr>
                    <td><input type="text" placeholder="ID" ng-model="newId" /></td>
                    <td><input type="text" placeholder="Name" ng-model="newName" /></td>
                    <td><input type="text" placeholder="Email Address" ng-model="newEmail" /></td>
                    <td><button class="btn btn-sm btn-success" ng-click="create()">Create</button></td>
                </tr>
            </tfoot>
        </table>

1. 在 `tbody` 和 `tfoot` 元素中，將每個 [ 替換成 {，每個 ] 替換成 }。(此網站目前無法在程式碼區塊中顯示雙大括弧運算式。)

2. 以滑鼠右鍵按一下 *index.html* 檔案，然後按一下 [設定為起始頁]。

3. 以滑鼠右鍵按一下 *index.html* 檔案，然後按一下 [在瀏覽器中檢視]。

	留意 HTML 輸出中的範本 Handlebars。您將在下一個步驟中使用 AngularJS 進行這些 HTML 元素的資料繫結。

	![方案總管中的 apiapp.json 和中繼資料](./media/app-service-api-javascript-client/09-template-ui.png)

1. 將以下 JavaScript 程式碼加入至 *index.html* 檔案，以呼叫 API 並將 HTML UI 資料繫結至 API 輸出。

		/* client javascript code here */
        angular.module('myApp', []).controller('contactListCtrl', function ($scope, $http) {
            $scope.baseUrl = 'http://localhost:1578';

            $scope.refresh = function () {
                $scope.status = "Refreshing Contacts...";
                $http({
                    method: 'GET',
                    url: $scope.baseUrl + '/api/contacts',
                    headers: {
                        'Content-Type': 'application/json'
                    }
                }).success(function (data) {
                    $scope.contacts = data;
                    $scope.status = "Contacts loaded";
                }).error(function (data, status) {
                    $scope.status = "Error loading contacts";
                });
            };

            $scope.create = function () {
                $scope.status = "Creating a new contact";

                $http({
                    method: 'POST',
                    url: $scope.baseUrl + '/api/contacts',
                    headers: {
                        'Content-Type': 'application/json'
                    },
                    data: {
                        'Id': $scope.newId,
                        'Name': $scope.newName,
                        'EmailAddress': $scope.newEmail
                    }
                }).success(function (data) {
                    $scope.status = "Contact created";
                    $scope.refresh();
                    $scope.newId = 0;
                    $scope.newName = '';
                    $scope.newEmail = '';
                });
            };

            $scope.refresh();
        });

1，在您剛才加入至 index.html 的程式碼中，將基底 URL (`http://localhost:1578`) 中的通訊埠編號替換成 API 專案的實際通訊埠編號。

	> **請注意不要使用** HTML 用戶端專案的連接埠號碼。您可以在 API 專案上按一下滑鼠右鍵，再按一下 [偵錯] > [啟動新執行個體]，就能看到顯示連接埠號碼的瀏覽器視窗

1. 執行 HTML 用戶端時，請確定 API 應用程式專案也在執行中，否則 JavaScript HTML 將無法正常運作。以滑鼠右鍵按一下方案，然後選取 [**屬性**]。然後將這兩個 Web 專案設定為 [**啟動但不偵錯**]，而且先執行 API 專案。 

	![方案總管中的 apiapp.json 和中繼資料](./media/app-service-api-javascript-client/10-run-both-web-projects.png)

1. 執行方案，HTML/JavaScript 用戶端便會連接並顯示 API 應用程式專案中的資料。

	![方案總管中的 apiapp.json 和中繼資料](./media/app-service-api-javascript-client/11-web-client-running.png)

## 部署 Web 應用程式

在本節中，您會將 HTML/JavaScript 用戶端部署為 App Service Web 應用程式。部署完成之後，您要變更 JavaScript 用來取用部署的 API 應用程式的 URL。

> 注意： 本節假設您已閱讀並完成[部署 API 應用程式](app-service-dotnet-deploy-api-app.md)文章，或您先前已部署自己的 API 應用程式。

1. 在 Azure 預覽入口網站中開啟 API 應用程式的刀鋒視窗。按一下刀鋒視窗中的 URL 以在瀏覽器中開啟。開啟之後，從瀏覽器網址列複製 API 應用程式的 URL。 

	![方案總管中的 apiapp.json 和中繼資料](./media/app-service-api-javascript-client/12-open-api-app-from-blade.png)

1. 貼上 API 應用程式的 URL，以覆寫 JavaScript 程式碼中 **$scope.baseUrl** 屬性先前的值。

		$scope.baseUrl = 'https://microsoft-apiappf7e042ba8e5233ab4312021d2aae5d86.azurewebsites.net';

	留意 URL 是否指定 HTTPS。不可選擇使用 HTTPS；API Apps 不支援 HTTP。

1. 以滑鼠右鍵按一下 HTML/JavaScript Web 專案，然後選取 [**發行**] 內容功能表項目。

	![方案總管中的 apiapp.json 和中繼資料](./media/app-service-api-javascript-client/13-publish-web-app.png)

1. 選取 [發行 Web] 對話方塊中的 [**Microsoft Azure Web 應用程式**] 選項。

	![方案總管中的 apiapp.json 和中繼資料](./media/app-service-api-javascript-client/14-publish-web-dialog.png)

1. 按一下 [**新增** ] 按鈕以建立新的 Web 應用程式。

	![方案總管中的 apiapp.json 和中繼資料](./media/app-service-api-javascript-client/15-new-web-app.png)

1. 選取您的 API 應用程式已在其中執行的相同應用程式主控方案和資源群組。

	> **注意**：這並非必要條件，但是為了示範，如果所有項目都包含在一個資源群組中，稍後就能比較輕鬆地清除您的 Azure 資源。

	![方案總管中的 apiapp.json 和中繼資料](./media/app-service-api-javascript-client/16-new-web-app-creation-dialog.png)

1. 完成 [Web 發行] 步驟，以將 HTML/JavaScript 用戶端部署至 App Service Web 應用程式。
1. 一旦部署 Web 應用程式，就應該在 Web 瀏覽器中自動開啟並顯示來自 API 應用程式的資料。 

	![方案總管中的 apiapp.json 和中繼資料](./media/app-service-api-javascript-client/17-web-app-running-in-ie.png)

1. 此時，如果您瀏覽至資源群組，您會看到新的 Web 應用程式與 API 應用程式一起執行。

	![方案總管中的 apiapp.json 和中繼資料](./media/app-service-api-javascript-client/18-web-app-visible-in-resource-group.png)

## 摘要 
此範例示範如何使用 AngularJS 做為 JavaScript 平台，以供存取 API 應用程式後端。您可以將 REST 存取功能變更為使用任何其他 JavaScript 架構。

 

<!---HONumber=62-->