<properties 
	pageTitle="在原始檔控制中儲存專案程式碼 - Azure 行動服務" 
	description="了解如何儲存您的 .NET 後端專案，並從您的電腦上的本機 Git 儲存機制發佈。" 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="05/20/2015" 
	ms.author="glenga"/>

# 在原始檔控制中儲存專案程式碼

> [AZURE.SELECTOR-LIST (Platform | Backend)]
- [(Any | .NET)](mobile-services-dotnet-backend-store-code-source-control.md)
- [(Any | Javascript)](mobile-services-store-scripts-source-control.md)

本主題將示範如何使用 Azure 行動服務所提供的原始檔控制來儲存您的 .NET 後端服務專案。您可直接從本機 Git 儲存機制上傳專案至生產行動服務以發佈專案。

若要完成本教學課程，您必須已建立行動服務，方法是完成[開始使用行動服務]或[將行動服務新增至現有的應用程式]教學課程。

##<a name="enable-source-control"></a>在行動服務中啟用原始檔控制

[AZURE.INCLUDE [mobile-services-enable-source-control](../../includes/mobile-services-enable-source-control.md)]

##<a name="clone-repo"></a>安裝 Git 與建立本機儲存機制

1. 在您的本機電腦上安裝 Git。 

	安裝 Git 所需的步驟會因作業系統而有所不同。如需作業系統特定的發佈和安裝指引，請參閱[安裝 Git]。

	> [AZURE.NOTE]在某些作業系統上，會同時提供例 Git 的命令列和 GUI 兩種版本。本文提供的指示將使用命令列版本。

2. 開啟命令列，例如 **GitBash** (Windows) 或 **Bash** (Unix Shell)。在 OS X 系統上，您可以透過 **[終端機]** 應用程式來存取命令列。

3. 在命令列中，切換至您要儲存指令碼的目錄。例如，`cd SourceControl`。

4. 使用下列命令來建立新 Git 儲存機制的本機複本，以行動服務的 Git 儲存機制 URL 來取代 `<your_git_URL>`：

		git clone <your_git_URL>

5. 出現提示時，請輸入您在行動服務中啟用原始檔控制時所設定的使用者名稱和密碼。在成功驗證之後，您將會看到如下所示的一連串回應：

		remote: Counting objects: 8, done.
		remote: Compressing objects: 100% (4/4), done.
		remote: Total 8 (delta 1), reused 0 (delta 0)
		Unpacking objects: 100% (8/8), done.

6. 瀏覽至您執行 `git clone` 命令的目錄，注意有一個使用行動服務名稱建立的新目錄。對於 .NET 後端行動服務，git 儲存機制最初是空白的。

在建立本機儲存機制後，您可以從這個儲存機制發佈 .NET 後端服務專案。

##<a name="deploy-scripts"></a>使用 Git 發佈專案

1. 在 Visual Studio 2013 中建立新的 .NET 後端行動服務專案，或將現有的專案移到新的本機儲存機制。  

	如要快速測試，請下載行動服務快速入門專案並儲存到這個資料夾。

2. 移除所有 NuGet 封裝資料夾，保留 packages.config 檔案。

	行動服務會依據 packages.confign 檔案自動還原 NuGet 封裝。您也可以定義 .gitignore 檔案以防止新增封裝目錄。
 
3. 在 Git 命令提示字元中，輸入下列命令以開始追蹤新的指令碼檔案：

		$ git add .
	
4. 輸入下列命令以認可變更：

		$ git commit -m "adding the .NET backend service project"

5. 輸入下列命令，將變更上傳至遠端儲存機制，並提供您的認證：

		$ git push origin master
	
	您應該會看到一連串命令，指出專案已部署到行動服務、已新增封裝，且服務已重新啟動。

6. 瀏覽至 .NET 後端行動服務的 URL，您應該會看到：

	![行動服務啟動頁面](./media/mobile-services-dotnet-backend-store-code-source-control/mobile-service-startup.png)

現在，您的行動服務專案會保存在原始檔控制中，您可以直接從本機儲存機制發送更新以發佈服務更新。如需關於在使用 SQL Database 的 .NET 後端行動服務中變更資料模型的資訊，請參閱[如何對 .NET 後端行動服務進行資料模型變更]。

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Git website]: http://git-scm.com
[Source control]: http://msdn.microsoft.com/library/windowsazure/c25aaede-c1f0-4004-8b78-113708761643
[安裝 Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[開始使用行動服務]: mobile-services-dotnet-backend-ios-get-started.md
[將行動服務新增至現有的應用程式]: mobile-services-dotnet-backend-ios-get-started-data.md
[Azure Management Portal]: https://manage.windowsazure.com/
[Call a custom API from the client]: mobile-services-dotnet-backend-ios-call-custom-api.md
[如何對 .NET 後端行動服務進行資料模型變更]: mobile-services-dotnet-backend-how-to-use-code-first-migrations.md
 

<!---HONumber=July15_HO2-->