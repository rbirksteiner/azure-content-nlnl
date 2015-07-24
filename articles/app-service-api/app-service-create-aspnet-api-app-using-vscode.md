<properties
   pageTitle="在 Visual Studio 程式碼中建立 ASP.NET 5 API 應用程式"
   description="本教學課程示範如何使用 Visual Studio 程式碼建立 ASP.NET 5 API 應用程式。"
   services="app-service\api"
   documentationCenter=".net"
   authors="erikre"
   manager="wpickett"
   editor="jimbe"/>

<tags
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="erikre"/>

# 在 Visual Studio 程式碼中建立 ASP.NET 5 API 應用程式

> [AZURE.SELECTOR]
- [Visual Studio 2013](app-service-dotnet-create-api-app.md)
- [Visual Studio 2015 RC](app-service-dotnet-create-api-app-vs2015.md)
- [Visual Studio Code](app-service-create-aspnet-api-app-using-vscode.md)

## 概觀

本教學課程示範如何使用 [Visual Studio 程式碼](http://code.visualstudio.com//Docs/whyvscode)建立 ASP.NET 5 API 應用程式。ASP.NET 5 是大幅重新設計的 ASP.NET。ASP.NET 5 是新的開放原始碼和跨平台架構，用於使用 .NET 建置現代雲端 Web 應用程式。如需詳細資訊，請參閱 [ASP.NET 5 簡介](http://docs.asp.net/en/latest/conceptual-overview/aspnet.html)。如需 API 應用程式的詳細資訊，請參閱[什麼是 API 應用程式？](app-service-api-apps-why-best-platform.md)。

> [AZURE.NOTE]若要完成此教學課程，您需要 Microsoft Azure 帳戶。如果您沒有帳戶，則可以[申請免費試用](/pricing/free-trial/)或[啟用自己的 MSDN 訂戶權益](/pricing/member-offers/msdn-benefits-details/)。您也可以免費試用 [App Service 應用程式範例](http://tryappservice.azure.com)。

## 必要條件  

* 安裝和設定 [Visual Studio 程式碼](http://code.visualstudio.com/Docs/setup)。
* 安裝 [Node.js](http://nodejs.org/download/)。<br\>
	[Node.js](http://nodejs.org/) 是一種平台，可使用 JavaScript 建置快速和可擴充的伺服器應用程式。節點是執行階段 (節點)，而 [npm](http://www.npmjs.com/) 是節點模組的封裝管理員。您將使用 npm，在本教學課程中建立 ASP.NET 5 API 應用程式的結構。

## 安裝 ASP.NET 5 和 DNX
ASP.NET 5/DNX 是精簡的 .NET 堆疊，可建置 OS X、Linux 和 Windows 上執行的現代雲端和 Web 應用程式。已從頭建置，以將最佳化的開發架構提供給已部署至雲端或執行內部部署的應用程式。其由額外負荷最低的模組化元件組成，以便您可以在建構解決方案時保留彈性。

> [AZURE.NOTE]OS X 和 Linux 上的 ASP.NET 5 和 DNX (.NET 執行環境) 處於早期 Beta/預覽狀態。

本教學課程的設計旨在讓您使用最新開發版本的 ASP.NET 5 和 DNX 開始建置應用程式。如果您想要更穩定的發行經驗，請移至 [http://www.asp.net/vnext](http://www.asp.net/vnext)。下列是 Windows 特有的指示。如需 OS X、Linux 和 Windows 的更詳細安裝指示，請參閱[安裝 ASP.NET 5 和 DNX](https://code.visualstudio.com/Docs/ASPnet5#_installing-aspnet-5-and-dnx)。

1. 若要在 Windows 中安裝 .NET 版本管理員 (DNVM)，請在 [命令視窗] 中執行下列命令。

	<pre class="prettyprint">
@powershell -NoProfile -ExecutionPolicy unrestricted -Command "&amp;{$Branch='dev';iex ((new-object net.webclient).DownloadString('https://raw.githubusercontent.com/aspnet/Home/dev/dnvminstall.ps1'))}"
	</pre> 
	這將下載 DNVM 指令碼，並將它放在您的使用者設定檔中。

2. 您可能需要在輸入上述命令之後登出，PATH 環境變數的變更才會生效。
3. 在 [命令視窗] 中執行下列命令，以檢查 DNVM 的位置： 

	<pre class="prettyprint">
where dnvm
	</pre>
	[命令視窗] 將顯示如下路徑：

	![dnvm 位置](./media/app-service-create-aspnet-api-app-using-vscode/00-where-dnvm.png)

4. 現在您具有 DNVM，您需要使用其來下載 DNX，才能執行應用程式。從 [命令視窗] 中執行下列命令：

	<pre class="prettyprint">
dnvm upgrade
</pre>

5. 確認您的 DNVM，並在 [命令視窗] 中輸入下列命令，檢視使用中執行階段：

	<pre class="prettyprint">
dnvm list
	</pre>
	[命令視窗] 將顯示使用中執行階段的詳細資料：

	![dnvm 位置](./media/app-service-create-aspnet-api-app-using-vscode/00b-dnvm-list.png)

如需 OS X、Linux 和 Windows 的更詳細安裝指示，請參閱[安裝 ASP.NET 5 和 DNX](https://code.visualstudio.com/Docs/ASPnet5#_installing-aspnet-5-and-dnx)。

## 建立 API 應用程式 

本節顯示如何建立新的應用程式 ASP.NET API 應用程式的結構。您將使用 Node Package Manager (NPM) 安裝 [Yeoman](http://yeoman.io/)、[Grunt](http://gruntjs.com/) 和 [Bower](http://bower.io/)。

1. 安裝 Visual Studio 程式碼和 Node.js 之後，請使用系統管理員權限開啟命令提示字元，並巡覽至所有使用 VSCode 的 ASP.NET 專案所在位置。
2. 在命令視窗中輸入下列命令來安裝 Yeoman 和支援的工具：

	<pre class="prettyprint">
npm install -g yo grunt-cli generator-aspnet bower
</pre>

3. 在命令視窗中輸入下列命令來建立專案資料夾，並建立應用程式的結構：

	<pre class="prettyprint">
yo aspnet
</pre>

4. 捲動和選取 **Web API 應用程式**型別，以遵循產生器所提供的指示。

	![Yoman - ASP.NET 5 產生器](./media/app-service-create-aspnet-api-app-using-vscode/01-yo-aspnet.png)

5. 將新 ASP.NET API 應用程式的名稱設為 **ContactsList**。此名稱將用於本教學課程稍後提供的程式碼。<br>
	Yeoman 將建立名為 **ContactsList** 的新資料夾，以及新應用程式必要的檔案。
6. 開啟 **Visual Studio 程式碼**。<br> 
	輸入 **code**，即可從 [命令視窗] 中開啟 VSCode。
7. 從 [**檔案**] 功能表中，選取 [**開啟資料夾**]，然後選取您 ASP.NET API 應用程式所在的資料夾。

	![[選取資料夾] 對話方塊](./media/app-service-create-aspnet-api-app-using-vscode/02-open-folder.png)

	VSCode VS 將載入您的專案，並將其檔案顯示在 [**瀏覽**] 視窗中。

	![顯示 Contact 專案的 VSCode](./media/app-service-create-aspnet-api-app-using-vscode/03-vscode-contacts-project.png)

8. 在 [**VSCode**] 中，選取 [**檢視**] 功能表中的 [**命令調色盤**]。
9. 在 [**命令調色盤**] 中，輸入下列命令：

	<pre class="prettyprint">
dnx:dnu restore - (ContactsList)
	</pre>
	當您開始輸入時，您將從清單中看到完整的命令列。

	![Restore 命令](./media/app-service-create-aspnet-api-app-using-vscode/04-dnu-restore.png)

	Restore 命令會安裝必要的 NuGet 封裝來執行應用程式。備妥之後，命令視窗將顯示 [**還原完成**]。

## 修改 API 應用程式

現在，加入 **Contact** 類別和 **ContactsController** 類別，以修改 **ContactsList** 應用程式。

1. 將游標放在 **ContactsList** 專案名稱上方，並使用 [新增資料夾] 圖示加入名為 *Models* 的新資料夾。

	![新 Models 資料夾](./media/app-service-create-aspnet-api-app-using-vscode/07-new-folder.png)

2. 以滑鼠右鍵按一下 **Models** 資料夾，使用下列程式碼加入名為 *Contact.cs* 的新類別檔案：

	<pre class="prettyprint">
namespace ContactsList.Models
{
    public class Contact
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public string EmailAddress { get; set; }
    }
}
</pre>

3. 以滑鼠右鍵按一下 **Controllers** 資料夾，並加入 *ContactsController.cs* 檔案使其出現，如下所示：

	<pre class="prettyprint">
using System.Collections.Generic;
using Microsoft.AspNet.Mvc;
using ContactsList.Models;

namespace ContactsList.Controllers
{
    [Route("api/[controller]")]
    public class ContactsController : Controller
    {
        // GET: api/Contacts
        [HttpGet]
        public IEnumerable&lt;Contact> Get()
        {
            return new Contact[]{
                new Contact { Id = 1, EmailAddress = "barney@contoso.com", Name = "Barney Poland"},
                new Contact { Id = 2, EmailAddress = "lacy@contoso.com", Name = "Lacy Barrera"},
                new Contact { Id = 3, EmailAddress = "lora@microsoft.com", Name = "Lora Riggs"}
            };
        }
    }
}
</pre>

4. 選取 [**檔案**] > [**全部儲存**]，確定儲存您的所有檔案。
5. 從 [**命令調色盤**] 中，輸入下列命令以在本機執行應用程式：

	<pre class="prettyprint">
dnx: kestrel - (ContactsList, Microsoft.AspNet.Hosting --server Kestrel --server.urls http://localhost:5001
	</pre>
	命令視窗將在其中顯示 [*已啟動*]。如果命令視窗未顯示 [*已啟動*]，請檢查 VSCode 左下角，找出專案中的錯誤。

5. 開啟瀏覽器，並巡覽至下列 URL：

	**http://localhost:5001/api/Contacts**

	然後，您可以檢視 *Contacts.json* 的內容。檢視檔案時，您會看到下列內容：

	![傳回的 json 內容](./media/app-service-create-aspnet-api-app-using-vscode/08-contacts-json.png)

## 修改 API 應用程式中繼資料
可讓 ASP.NET API 專案部署為 API 應用程式的中繼資料必須包含在專案根目錄的 *apiapp.json* 檔案中。

1. 在 VSCode 中，以滑鼠右鍵按一下 *wwwroot* 資料夾，然後選取 [**新增檔案**] 選項。
2. 將新檔案命名為 *apiapp.json*。<br\>
	請確定 *apiapp.json* 位於 *wwwroot* 資料夾中。
3. 將下列程式碼加入 *apiapp.json* 檔案：

	<pre class="prettyprint">
{
    "$schema": "http://json-schema.org/schemas/2014-11-01/apiapp.json#",
    "id": "ContactsList",
    "namespace": "microsoft.com",
    "gateway": "2015-01-14",
    "version": "1.0.0",
    "title": "ContactsList",
    "summary": "",
    "author": "",
    "endpoints": null
}
</pre>

在 *apiapp.json* 檔案中，您可以指定動態 Swagger API 定義 JSON 的端點，但在本教學課程中，您將使用靜態 API 定義檔。如需使用動態 Swagger 產生的範例，請參閱[將 Web API 專案設定為 API 應用程式](app-service-dotnet-create-api-app-visual-studio.md)。

## 加入 Swagger 靜態 API 定義
若要提供靜態 Swagger 2.0 API 定義檔，您必須在 Web 根目錄中建立資料夾，並在其中放置 API 定義檔。

1. 在 VSCode 中，於 *wwwroot* 資料夾中建立名為 *metadata* 的資料夾。
2. 以滑鼠右鍵按一下新的 *metadata* 資料夾，並加入名為 *apiDefinition.swagger.json* 的新檔案。 
3. 將下列 json 語法加入新檔案中：

	<pre class="prettyprint">
{
  "swagger": "2.0",
  "info": {
    "version": "v1",
    "title": "ContactsList"
  },
  "host": "MUST REPLACE THIS WITH YOUR HOST URL",
  "schemes": [
    "https"
  ],
  "paths": {
    "/api/Contacts": {
      "get": {
        "tags": [
          "Contacts"
        ],
        "operationId": "Contacts_Get",
        "consumes": [],
        "produces": [
          "application/json",
          "text/json",
          "application/xml",
          "text/xml"
        ],
        "responses": {
          "200": {
            "description": "OK",
            "schema": {
              "type": "array",
              "items": {
                "$ref": "#/definitions/Contact"
              }
            }
          }
        },
        "deprecated": false
      },
      "post": {
        "tags": [
          "Contacts"
        ],
        "operationId": "Contacts_Post",
        "consumes": [
          "application/json",
          "text/json",
          "application/xml",
          "text/xml",
          "application/x-www-form-urlencoded"
        ],
        "produces": [
          "application/json",
          "text/json",
          "application/xml",
          "text/xml"
        ],
        "parameters": [
          {
            "name": "contact",
            "in": "body",
            "required": true,
            "schema": {
              "$ref": "#/definitions/Contact"
            }
          }
        ],
        "responses": {
          "200": {
            "description": "OK",
            "schema": {
              "$ref": "#/definitions/Object"
            }
          }
        },
        "deprecated": false
      }
    }
  },
  "definitions": {
    "Contact": {
      "type": "object",
      "properties": {
        "Id": {
          "format": "int32",
          "type": "integer"
        },
        "Name": {
          "type": "string"
        },
        "EmailAddress": {
          "type": "string"
        }
      }
    },
    "Object": {
      "type": "object",
      "properties": {}
    }
  }
}
</pre>

稍後，在本教學課程中，您會將上述的主機 URL 預留位置字串取代為您稍後將建立和複製的 Azure 主機 URL。

## 在 Azure 預覽入口網站中建立 API 應用程式

> [AZURE.NOTE]若要完成此教學課程，您需要 Microsoft Azure 帳戶。如果您沒有帳戶，則可以[申請免費試用](/pricing/free-trial/)或[啟用自己的 MSDN 訂戶權益](/pricing/member-offers/msdn-benefits-details/)。您也可以免費試用 [App Service 應用程式範例](http://tryappservice.azure.com)。

1. 登入 [Azure 預覽入口網站](https://portal.azure.com)。

2. 按一下入口網站左上角的 [**新增**]。

3. 按一下 **[Web + 行動] > [API 應用程式]**。

	![Azure 的新 API 應用程式](./media/app-service-create-aspnet-api-app-using-vscode/09-azure-newapiapp.png)

4. 輸入 [**名稱**] 的值 (例如 ContactsList)。

5. 選取 App Service 方案或新建一個。如果您建立新方案，請選取定價層、位置和其他選項。

	![Azure 的新 API 應用程式刀鋒視窗](./media/app-service-create-aspnet-api-app-using-vscode/10-azure-newappblade.png)

6. 按一下 [建立]。

	![API 應用程式刀鋒視窗](./media/app-service-create-aspnet-api-app-using-vscode/11-azure-apiappblade.png)

	如果您在建立應用程式時將 [**釘選到「開始面板」**] 核取方塊保持選取狀態，則按一下 [**首頁**] 或 [**瀏覽**] 即可輕鬆地找到應用程式。如果您已清除此核取方塊，請按一下左側的 [**通知**] 查看 API 應用程式建立狀態，然後按一下通知可移至新 API 應用程式的刀鋒視窗。

7. 按一下 **[設定] > [應用程式設定]**。

8. 將存取權層級設定為 [**公用 (匿名)**]。

9. 按一下 [儲存]。

	![API 應用程式設定](./media/app-service-create-aspnet-api-app-using-vscode/12-azure-appsettings.png)

## 啟用新 API 應用程式的 Git 發佈

Git 是一個您可用來部署 Azure 網站的分散式版本控制系統。您將會在本機 Git 儲存機制中儲存您為 API 應用程式撰寫的程式碼，並藉由發送至遠端儲存機制，將您的程式碼部署至 Azure。此部署方法是您可以在 API 應用程式中使用的 App Service Web 應用程式功能，因為 API 應用程式是以 Web 應用程式為基礎：Azure App Service 中的 API 應用程式是具備其他用於裝載 Web 服務之功能 Web 應用程式。

在入口網站中，您可以在 [**API 應用程式**] 刀鋒視窗中管理 API 應用程式的特定功能，而您可以在 [**API 應用程式主機**] 刀鋒視窗中管理與 Web 應用程式共用的功能。在本節中，您會前往 [**API 應用程式主機**] 刀鋒視窗以設定 Git 部署功能。

1. 在 [API 應用程式] 刀鋒視窗中，按一下 [**API 應用程式主機**]。

	![Azure API 應用程式主機](./media/app-service-create-aspnet-api-app-using-vscode/13-azure-apiapphost.png)

2. 尋找 [**API 應用程式**] 刀鋒視窗的 [**部署**] 區段，然後按一下 [**設定連續部署**]。您可能需要向下捲動至刀鋒視窗的這個部分。

	![Azure API 應用程式主機](./media/app-service-create-aspnet-api-app-using-vscode/14-azure-deployment.png)

3. 按一下 **[選擇來源] > [本機 Git 儲存機制]**。

5. 按一下 [確定]。

	![Azure 本機 Git 儲存機制](./media/app-service-create-aspnet-api-app-using-vscode/15-azure-localrepository.png)

6. 如果您先前尚未設定部署認證以供發佈 API 應用程式或其他 App Service 應用程式，請立即設定：

	* 按一下 [**設定部署認證**]。

	* 建立使用者名稱和密碼。稍後設定 Git 時，您將需要此密碼。

	* 按一下 [儲存]。

	![Azure 部署認證](./media/app-service-create-aspnet-api-app-using-vscode/16-azure-credentials.png)

7. 在 [**API 應用程式主機**] 刀鋒視窗中，按一下 **[設定] > [屬性]**。您將部署至的遠端 Git 儲存機制 URL 會顯示在 "GIT URL" 下方。

8. 複製 **GIT URL** 以便稍後在教學課程中使用。

	![Azure Git URL](./media/app-service-create-aspnet-api-app-using-vscode/17-azure-giturl.png)

9. 此外，在 [**API 應用程式**] 刀鋒視窗中，複製您將用來更新 *apiDefinition.swagger.json* 檔案中 "host" 值的 **URL**。

	![Azure URL](./media/app-service-create-aspnet-api-app-using-vscode/18-azure-url.png)

10. 在 VSCode 中，開啟 *apiDefinition.swagger.json* 檔案，並將主機值 "MUST REPLACE THIS WITH YOUR HOST URL" 取代為您在上述步驟中複製的 **URL**。
11. 同時，移除主機值開頭的 "https://" 字元。
12. 將變更儲存至 *apiDefinition.swagger.json* 檔案。

## 將 API 應用程式發佈至 Azure App Service

在本節中，您會建立本機 Git 儲存機制並從該儲存機制發送至 Azure，以便將範例應用程式部署至在 Azure App Service 中執行的 API 應用程式。

1. 在 VSCode 中，選取導覽列左側的 [Git] 選項。
2. 如果尚未安裝 Git，請選擇其中一個提供的連結 ([Chocolatey](https://chocolatey.org/packages/git) 或 [git-scm.com](http://git-scm.com/downloads)) 來安裝它。如果您不熟悉 Git，請選擇 **git-scm.com**，並從 [Windows 命令提示字元] 中選取要搭配使用 Git 與 GitBash 的選項。 
3. 安裝 Git 之後，請重新啟動 VSCode，然後選取左列上的 Git 選項。
4. 在 VSCode 中，選取 [**初始化 git 儲存機制**]，確定您的工作區受到 git 原始檔控制。 

	![初始化 Git](./media/app-service-create-aspnet-api-app-using-vscode/19-initgit.png)

5. 
6. 加入認可訊息，然後選取 [**全部認可**] 核取方塊。

	![Git 全部認可](./media/app-service-create-aspnet-api-app-using-vscode/20-git-commit.png)

6. 尋找並開啟 **GitBash**。另一種方式是使用 [Windows 命令提示字元]。
7. 在 **GitBash** 中，將資料夾變更為 VSCode 專案資料夾。例如：

	<pre class="prettyprint">
cd c:\VSCodeProjects\ContactsList
</pre>

7. 使用您稍早複製的 Git URL (結尾為 ".git")，建立遠端參考，以便將更新發送至之前建立的 Web 應用程式 (API 應用程式主機)：

	<pre class="prettyprint">
git remote add azure [URL for remote repository]
</pre>

8. 輸入下列命令，將您的變更發送至 Azure：

	<pre class="prettyprint">
git push azure master
	</pre>
	系統會提示您輸入先前建立的密碼。**注意：將看不到您的密碼。**

	上述命令的輸出結尾會出現部署成功的訊息：

	<pre class="prettyprint">
remote: Deployment successful.
	 https://user@testsite.scm.azurewebsites.net/testsite.git
[new branch]      master -> master
</pre>

> [AZURE.NOTE]如果您變更應用程式，則可以重新發佈，方法是在 VSCode 中選取 [**全部認可**] 核取方塊，然後在 **GitBash** 中輸入 **git push azure master** 命令。

## 在 Azure 預覽入口網站中檢視 API 定義
現已將 API 部署到您的 API 應用程式，您可在 Azure 預覽入口網站中查看 API 定義。一開始要先重新啟動閘道器，這可讓 Azure 認可 API 應用程式的 API 定義已經變更。閘道器是一個 Web 應用程式，可處理資源群組中 API 應用程式的 API 管理和授權。

1. 在 Azure 預覽入口網站中，移至您稍早建立之 API 應用程式的 [**API 應用程式**] 刀鋒視窗，然後按一下 [**閘道器**] 連結。
2. 在 [**閘道器**] 刀鋒視窗中，按一下 [**重新啟動**]。您現在可以關閉此刀鋒視窗。
3. 在 [**API 應用程式**] 刀鋒視窗中，按一下 [**重新啟動**]。 
4. 在 [**API 應用程式**] 刀鋒視窗中，按一下 [**API 定義**]。<br>
	[API 定義] 刀鋒視窗會顯示兩種方法。如果您未立即看到 GET 和 POST 方法，請等候幾秒的時間，讓 Azure 重新整理應用程式。然後，在 [**API 應用程式**] 刀鋒視窗中，按一下 [**API 定義**]。

## 在 Azure 中執行應用程式
在 Azure 預覽入口網站中，移至 API 應用程式的 [**API 應用程式主機**] 刀鋒視窗，然後按一下 [**瀏覽**]。然後，在 URL 結尾加入 **api/Contacts**，以檢視連絡人詳細資料。


## 結論
在本教學課程中，您學到如何在 Visual Studio 程式碼中建立 API 應用程式。如需 Visual Studio 程式碼的詳細資訊，請參閱 [Visual Studio 程式碼](https://code.visualstudio.com/Docs/)。如需 API 應用程式的相關資訊，請參閱[什麼是 API 應用程式？](app-service-api-apps-why-best-platform.md)。
 

<!---HONumber=62-->