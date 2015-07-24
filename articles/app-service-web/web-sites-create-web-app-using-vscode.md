<properties
   pageTitle="在 Visual Studio Code 中建立 ASP.NET 5 Web 應用程式"
   description="本教學課程說明如何使用 Visual Studio Code 建立 ASP.NET 5 Web 應用程式。"
   services="app-service\web"
   documentationCenter=".net"
   authors="erikre"
   manager="wpickett"
   editor="jimbe"/>

<tags
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/14/2015" 
	ms.author="erikre;tarcher"/>

# 在 Visual Studio Code 中建立 ASP.NET 5 Web 應用程式

## 概觀

本教學課程示範如何使用 [Visual Studio Code (VS Code)](http://code.visualstudio.com//Docs/whyvscode) 建立 ASP.NET Web 5 應用程式，並將其部署到 [Azure App Service](../app-service/app-service-value-prop-what-is.md)。ASP.NET 5 是大幅重新設計的 ASP.NET。ASP.NET 5 是新的開放原始碼和跨平台架構，用於使用 .NET 建置新代雲端式 Web 應用程式。如需詳細資訊，請參閱 [ASP.NET 5 簡介](http://docs.asp.net/en/latest/conceptual-overview/aspnet.html)。如需有關 Azure App Service Web Apps 的詳細資訊，請參閱 [Web Apps 概觀](app-service-web-overview.md)。

[AZURE.INCLUDE [app-service-web-try-app-service.md](../../includes/app-service-web-try-app-service.md)]

## 必要條件  

* 安裝 [VS Code](http://code.visualstudio.com/Docs/setup)。
* 安裝 [Node.js](http://nodejs.org/download/) - [Node.js](http://nodejs.org/) 是一種平台，可使用 JavaScript 建置快速和可擴充的伺服器應用程式。Node 是執行階段 (Node)，而 [npm](http://www.npmjs.com/) 是Node 模組的封裝管理員。您將使用 npm，在本教學課程中建立 ASP.NET 5 Web 應用程式的結構。
* 安裝 Git - 您可以從下列位置安裝它：[Chocolatey](https://chocolatey.org/packages/git) 或 [git scm.com](http://git-scm.com/downloads)。如果您不熟悉 Git，請選擇 [git scm.com](http://git-scm.com/downloads)，並從 Windows 命令提示字元選取要使用 Git 與 GitBash 搭配的選項。一旦您安裝 Git，也需要設定 Git 使用者名稱和電子郵件，因為稍後教學課程將需要用到 (從 VS Code 執行認可時)。  

## 安裝 ASP.NET 5 和 DNX
ASP.NET 5/DNX 是精簡的 .NET 堆疊，可建置 OS X、Linux 和 Windows 上執行的現代雲端和 Web 應用程式。它已從頭建置，以將最佳化的開發架構提供給已部署至雲端或執行內部部署的應用程式。其由額外負荷最低的模組化元件組成，以便您可以在建構解決方案時保留彈性。

> [AZURE.NOTE]OS X 和 Linux 上的 ASP.NET 5 和 DNX (.NET 執行環境) 處於早期 Beta/預覽狀態。

本教學課程的設計旨在讓您使用最新開發版本的 ASP.NET 5 和 DNX 開始建置應用程式。下列是 Windows 特有的指示。如需 OS X、 Linux 和 Windows 的更詳細安裝指示，請參閱[安裝 ASP.NET 5 和 DNX](https://code.visualstudio.com/Docs/ASPnet5#_installing-aspnet-5-and-dnx)。

1. 若要在 Windows 中安裝 .NET 版本管理員 (DNVM)，請開啟命令提示字元並執行下列命令。

		@powershell -NoProfile -ExecutionPolicy unrestricted -Command "&{$Branch='dev';iex ((new-object net.webclient).DownloadString('https://raw.githubusercontent.com/aspnet/Home/dev/dnvminstall.ps1'))}"

	這將下載 DNVM 指令碼，並將它放在您的使用者設定檔目錄中。

2. 重新啟動 Windows 以完成 DNVM 安裝。

3. 開啟命令提示字元，並輸入下列命令確認 DNVM 的位置。

		where dnvm

	命令提示字元將顯示如下路徑。

	![dnvm 位置](./media/web-sites-create-web-app-using-vscode/00-where-dnvm.png)

4. 現在您具有 DNVM，您需要使用其來下載 DNX，以執行應用程式。在命令提示字元執行下列命令。

		dnvm upgrade

5. 確認您的 DNVM，並藉由在命令提示字元輸入下列命令，檢視使用中執行階段。

		dnvm list

	命令提示字元將顯示使用中執行階段的詳細資料。

	![DNVM 位置](./media/web-sites-create-web-app-using-vscode/00b-dnvm-list.png)

6. 如果列出多個 DNX 執行階段，請在命令提示字元輸入下列命令，將作用中 DNX 執行階段的版本設定為 ASP.NET 5 產生器所使用的版本，而該版本是當您稍後在本教學指導中建立 Web 應用程式時此產生器使用的版本。

		dnvm use 1.0.0-beta4 –p

> [AZURE.NOTE]如需 OS X、 Linux 和 Windows 的更詳細安裝指示，請參閱[安裝 ASP.NET 5 和 DNX](https://code.visualstudio.com/Docs/ASPnet5#_installing-aspnet-5-and-dnx)。

## 建立 Web 應用程式 

本節說明如何建立新的應用程式 ASP.NET Web 應用程式的結構。您將使用 node 封裝管理員 (npm) 來安裝 [Yeoman](http://yeoman.io/) (應用程式建立結構工具 - 相當於 Visual Studio [檔案] > [新專案] 作業的 VS Code)、[Grunt](http://gruntjs.com/) (JavaScript 工作執行程式) 和 [Bower](http://bower.io/) (用戶端封裝管理員)。

1. 以系統管理員權限開啟命令提示字元，並瀏覽至您想要用來建立 ASP.NET 專案的位置。

2. 在命令提示字元輸入下列命令來安裝 Yeoman 和支援的工具。

		npm install -g yo grunt-cli generator-aspnet bower

3. 在命令提示字元輸入下列命令，來建立專案資料夾，並建立應用程式的結構。

		yo aspnet

4. 使用方向鍵，從 ASP.NET 5 產生器功能表中選取 [Web Application] 類型，然後按下 &lt;Enter>。

	![Yeoman-ASP.NET 5 產生器](./media/web-sites-create-web-app-using-vscode/01-yo-aspnet.png)

5. 將新的 ASP.NET web 應用程式的名稱設定為 **SampleWebApp**。因為整個教學課程會使用此名稱，所以如果您選取不同的名稱，您需要取代每次出現的 **SampleWebApp**。當您按下 &lt;Enter> 時，Yeoman 將建立名為 **SampleWebApp** 的新資料夾，以及新應用程式必要的檔案。

6. 在命令提示字元輸入下列命令來開啟 VS Code。

		code .

7. 在 VS Code 中，選取 [檔案] > [開啟資料夾]，然後選取包含 ASP.NET Web 應用程式的資料夾。

	![[選取資料夾] 對話方塊](./media/web-sites-create-web-app-using-vscode/02-open-folder.png)

	VS Code 將載入您的專案，並將其檔案顯示在 [瀏覽] 視窗中。

	![顯示 SampleWebApp 專案的 VSCode](./media/web-sites-create-web-app-using-vscode/03-vscode-project.png)

8. 選取 [檢視] >[命令選擇區]。

9. 在 [命令選擇區] 中，輸入下列命令。

		dnx:dnu restore - (SampleWebApp)

	當您開始輸入時，您將從清單中看到完整的命令列。

	![Restore 命令](./media/web-sites-create-web-app-using-vscode/04-dnu-restore.png)

	Restore 命令會安裝必要的 NuGet 封裝以執行應用程式。備妥之後，命令提示字元將顯示 [還原完成]。

## 在本機執行 Web 應用程式

既然您已建立 Web 應用程式，並擷取應用程式的所有 NuGet 封裝，就可以在本機執行 Web 應用程式。

1. 從 VS Code 中的 [命令選擇區]，輸入下列命令以在本機執行應用程式。

		dnx: kestrel - (SampleWebApp, Microsoft.AspNet.Hosting --server Kestrel --server.urls http://localhost:5001

	命令視窗將在其中顯示 [已啟動]。如果命令視窗未顯示 [已啟動]，請檢查 VS Code 左下角，找出專案中的錯誤。

5. 開啟瀏覽器並瀏覽至下列 URL。

	**http://localhost:5001**

	Web 應用程式的預設頁面將出現，如下所示。

	![在瀏覽器中的本機 Web 應用程式](./media/web-sites-create-web-app-using-vscode/08-web-app.png)

## 在 Azure Preview 入口網站中建立 Web 應用程式

下列步驟將引導您在 Azure Preview 入口網站中建立 Web 應用程式。

1. 登入 [Azure Preview 入口網站](https://portal.azure.com)。

2. 按一下入口網站左上角的 [新增]。

3. 按一下 [Web Apps] > [Web 應用程式]。

	![Azure 的新 Web 應用程式](./media/web-sites-create-web-app-using-vscode/09-azure-newwebapp.png)

4. 輸入 [名稱] 的值，例如 **SampleWebAppDemo**。請注意，此名稱必須是唯一的，而且當您嘗試輸入名稱時，入口網站將會強制執行此要求。因此，如果選取或輸入不同值，您將需要以該值取代每次出現的 **SampleWebAppDemo**，您會在本教學課程中看到。

5. 選取現有的 **App Service 方案**或建立新方案。如果您建立新方案，請選取定價層、位置和其他選項。如需 App Service 方案的詳細資訊，請參閱文章：[Azure App Service 方案深入概觀](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)。

	![Azure 的新 Web 應用程式刀鋒視窗](./media/web-sites-create-web-app-using-vscode/10-azure-newappblade.png)

6. 按一下 [建立]。

	![Web 應用程式刀鋒視窗](./media/web-sites-create-web-app-using-vscode/11-azure-webappblade.png)

## 啟用新 Web 應用程式的 Git 發佈

Git 是一個您可用來部署 Azure App Service Web 應用程式的分散式版本控制系統。您將會在本機 Git 儲存機制中儲存您為 Web 應用程式撰寫的程式碼，並藉由發送至遠端儲存機制，將您的程式碼部署至 Azure。

1. 登入 [Azure Preview 入口網站](https://portal.azure.com)。

2. 按一下 [全部瀏覽]。

3. 按一下 [Web Apps]，檢視與 Azure 訂用帳戶相關聯之 Web 應用程式的清單。

4. 選取您已在本教學課程中建立的 Web 應用程式。

5. 在 Web 應用程式的刀鋒視窗中，向下捲動以找出 [部署] 區段，然後按一下 [設定連續部署]。

	![Azure Web 應用程式主機](./media/web-sites-create-web-app-using-vscode/14-azure-deployment.png)

6. 按一下 **[選擇來源] > [本機 Git 儲存機制]**。

7. 按一下 [確定]。

	![Azure 本機 Git 儲存機制](./media/web-sites-create-web-app-using-vscode/15-azure-localrepository.png)

8. 如果您先前尚未設定部署認證以供發佈 Web 應用程式或其他 App Service 應用程式，請立即設定：

	* 按一下 [FTP] 以設定部署認證。

	* 建立使用者名稱和密碼。稍後設定 Git 時，您將需要此密碼。

	* 按一下 [儲存]。

	![Azure 部署認證](./media/web-sites-create-web-app-using-vscode/16-azure-credentials.png)

9. 在 Web 應用程式的刀鋒視窗中，按一下 [設定] > [屬性]。您將部署至的遠端 Git 儲存機制 URL 會顯示在
10.  [URL] 下方。

10. 複製 [GIT URL] 值以供教學課程稍後使用。

	![Azure Git URL](./media/web-sites-create-web-app-using-vscode/17-azure-giturl.png)

## 將您的 Web 應用程式發佈至 Azure App Service

在本節中，您將建立本機 Git 儲存機制，並從該儲存機制發送至 Azure，以將 Web 應用程式部署至 Azure。

1. 在 VS Code 中，選取導覽列左側的 [Git] 選項。

	![VS Code 中的 Git 圖示](./media/web-sites-create-web-app-using-vscode/git-icon.png)

2. 選取 [初始化 git 儲存機制]，確定您的工作區受到 git 原始檔控制。

	![初始化 Git](./media/web-sites-create-web-app-using-vscode/19-initgit.png)

3. 新增認可訊息，並按一下 [全部認可] 核取圖示。

	![Git 全部認可](./media/web-sites-create-web-app-using-vscode/20-git-commit.png)

4. Git 完成處理之後，您會看到沒有檔案列在 Git 視窗的 [變更] 之下。

	![Git 沒有變更](./media/web-sites-create-web-app-using-vscode/no-changes.png)

5. 開啟命令提示字元，切換至裝載 Web 應用程式的目錄。

6. 使用您稍早複製的 Git URL (結尾是 ".git")，建立遠端參考，以便將更新發送至 Web 應用程式。

		git remote add azure [URL for remote repository]

7. 輸入下列命令，將您的變更推播至 Azure。

		git push azure master

	系統會提示您輸入先前建立的密碼。**注意：將看不到您的密碼。**

	上述命令的輸出結尾會出現部署成功的訊息。

		remote: Deployment successful.
		To https://user@testsite.scm.azurewebsites.net/testsite.git
		[new branch]      master -> master

> [AZURE.NOTE]如果您變更應用程式時，則可以重新發行，方法是在 VS Code 中選取 [全部認可] 選項，然後在命令提示字元輸入 **git push azure master** 命令。

## 在 Azure 中執行應用程式
既然您已部署 Web 應用程式，讓我們執行裝載於 Azure 的應用程式。

這有兩種方式可以完成：

* 開啟瀏覽器並輸入 Web 應用程式的名稱，如下所示。   

		http://SampleWebAppDemo.azurewebsites.net
 
* 在 Azure Preview 入口網站中，尋找您的 Web 應用程式的 Web 應用程式刀鋒視窗，然後按一下 [瀏覽]，以預設瀏覽器檢視您的應用程式。

![Azure Web 應用程式](./media/web-sites-create-web-app-using-vscode/21-azurewebapp.png)

## 摘要
在本教學課程中，您學到如何在 VS Code 建立 Web 應用程式，並將其部署至 Azure。如需 VS Code 的詳細資訊，請參閱文章：[為什選擇 Visual Studio Code？](https://code.visualstudio.com/Docs/)。如需 App Service Web Apps 的相關資訊，請參閱 [Web 應用程式概觀](app-service-web-overview.md)。

<!---HONumber=62-->