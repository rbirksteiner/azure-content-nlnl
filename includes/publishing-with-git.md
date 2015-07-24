# 在 Azure App Service 中使用 GIT 連續部署

[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) 支援從原始程式碼控制和儲存機制工具 (如 BitBucket、CodePlex、Dropbox、Git、GitHub、Mercurial 和 TFS) 連續部署至 Web Apps。您可以使用這些工具維護應用程式的內容和程式碼，然後快速輕鬆地隨時將變更推送到 Azure Web 應用程式。

在本文中，您將了解如何使用 Git，從本機電腦直接發行到 Web Apps (在 Azure 中，這個發行方法稱為「**本機 Git**」)。您還能了解如何從儲存機制網站 (如 BitBucket、CodePlex、Dropbox、GitHub 或 Mercurial) 進行連續部署。如需有關使用 TFS 持續部署的詳細資訊，請參閱＜[使用 Visual Studio Online 持續傳遞至 Azure]＞(英文)。

> [AZURE.NOTE]本文中描述的許多 Git 命令會在使用[適用於 Mac 和 Linux 的 Azure 命令列工具](/develop/nodejs/how-to-guides/command-line-tools/)建立 Web 應用程式時自動執行

## <a id="Step1"></a>步驟 1：安裝 Git

安裝 Git 所需的步驟會因作業系統而有所不同。如需作業系統特定的發佈和安裝指引，請參閱[安裝 Git]。

> [AZURE.NOTE]在某些作業系統上，會同時提供例 Git 的命令列和 GUI 兩種版本。本文提供的指示將使用命令列版本。

## <a id="Step2"></a>步驟 2：建立本機儲存機制

請執行下列工作以建立新的 Git 儲存機制。

1. 建立名為 MyGitRepository 的目錄，以包含您的 Git 儲存機制和 Web 應用程式檔案。

2. 開啟命令列工具，例如 **GitBash** (Windows) 或 **Bash** (Unix Shell)。在 OS X 系統上，您可以透過 **[終端機]** 應用程式來存取命令列。

3. 從命令列，切換至 MyGitRepository 目錄。

		cd MyGitRepository

4. 使用下列命令來初始化新的 Git 儲存機制：

		git init

	這應會傳回一則如下訊息：**已初始化 [path] 中的空白 Git 儲存機制**。

## <a id="Step3"></a>步驟 3：新增網頁

Web 應用程式支援以各種程式設計語言建立的應用程式。以此範例為例，您將使用靜態 .html 檔案。

1. 使用文字編輯器，在 Git 儲存機制的根目錄 (您稍早建立的 MyGitRepository 目錄) 中建立名為 **index.html** 的新檔案。

2. 新增下列文字作為 index.html 檔案的內容，然後儲存檔案。

		Hello Git!

3. 從命令列，驗證您位在 Git 儲存機制的根目錄。然後使用下列命令，將 **index.html** 檔案新增至儲存機制：

		git add index.html 

	> [AZURE.NOTE]在任何 git 命令後輸入 -help 或 --help 即可尋找該命令的說明。例如，如需 add 命令的參數選項，請輸入 'git add -help' 以取得命令列說明，或輸入 'git add --help' 以取得更詳細的說明。

4. 接著，使用下列命令來認可對儲存機制的變更：

		git commit -m "Adding index.html to the repository"

	您應該會看到如下所示的輸出：

		[master (root-commit) 369a79c] Adding index.html to the repository
		 1 file changed, 1 insertion(+)
		 create mode 100644 index.html

## <a id="Step4"></a>啟用 Web 應用程式儲存機制

請執行下列步驟來啟用 Web 應用程式的 Git 儲存機制。

1. 登入 [Azure 預覽入口網站]。

2. 在 Web 應用程式的刀鋒視窗中，向下捲動至 [**部署**] 區段，然後按一下 [**設定連續部署**]。依序按一下 [**選擇來源**]、[**本機 Git 儲存機制**] 以及 [**確定**]。

	![本機 Git 儲存機制](./media/publishing-with-git/azure1-local-git.png)

4. 如果這是您第一次在 Azure 中設定儲存機制，就需要為它建立登入認證。您將使用這些認證來登入 Azure 儲存機制，並推播來自您本機 Git 儲存機制的變更。從 Web 應用程式的刀鋒視窗中，按一下 [設定] > [部署認證]，然後設定您的部署使用者名稱和密碼。完成後，按一下 [**確定**]。

	![](./media/publishing-with-git/azure2-credentials.png)

## <a id="Step5"></a>部署專案

* [將本機檔案推送至 Azure (本機 Git)](#Step6)
* [部署儲存機制網站 (如 BitBucket、CodePlex、Dropbox、GitHub 或 Mercurial) 的檔案](#Step7)
* [從 BitBucket、CodePlex、Dropbox、GitHub 或 Mercurial 部署 Visual Studio 方案](#Step75)

利用下列步驟，使用本機 Git 將 Web 應用程式發行至 Azure。

1. 在 Web 應用程式的刀鋒視窗的 [部署] 區段中，按一下 [**找不到部署**]。

	![](./media/publishing-with-git/azure3-repo-details.png)

	**Git URL** 是從本機儲存機制部署的遠端參考。在下列步驟中，您將使用此 URL。

1. 使用命令列，驗證您位在包含先前建立之 index.html 檔案的本機 Git 儲存機制的根目錄。

2. 使用 `git remote` 加入從步驟 1 的 **Git URL** 中所列的遠端參考。您的命令將類似以下範例：

		git remote add azure https://username@needsmoregit.scm.azurewebsites.net:443/NeedsMoreGit.git

    > [AZURE.NOTE]**remote** 命令會將指定的參考新增至遠端儲存機制。在此範例中，它會為您 Web 應用程式的儲存機制建立名為 'azure' 的參考。

1. 在命令列中使用下列命令，將目前的儲存機制內容從本機儲存機制推送到 'azure' 遠端：

		git push azure master

	系統將會提示您輸入在入口網站中重設部署認證時所建立的密碼。輸入密碼 (請注意，當您輸入密碼時，Gitbash 不會對主控台回應星號)。您應該會看到如下所示的輸出：

		Counting objects: 6, done.
		Compressing objects: 100% (2/2), done.
		Writing objects: 100% (6/6), 486 bytes, done.
		Total 6 (delta 0), reused 0 (delta 0)
		remote: New deployment received.
		remote: Updating branch 'master'.
		remote: Preparing deployment for commit id '369a79c929'.
		remote: Preparing files for deployment.
		remote: Deployment successful.
		To https://username@needsmoregit.scm.azurewebsites.net:443/NeedsMoreGit.git
		* [new branch]		master -> master

	> [AZURE.NOTE]為您的 Web 應用程式建立的儲存機制，預期推送要求會選擇其儲存機制的 <strong>master</strong> 分支，然後將它當做 Web 應用程式的內容使用。

2. 回到 Azure 入口網站中的 Web 應用程式分頁。[**找不到部署**] 應該變更為 [**現用部署**]，其中包含最近推送的記錄項目。

	![](./media/publishing-with-git/azure4-deployed.png)

2. 按一下 Web 應用程式刀鋒視窗頂端 **URL** 底下的連結，確認已部署 **index.html**。隨即會出現包含 'Hello Git!' 的頁面。

	![包含 'Hello Git!' 的網頁][hello-git]

3. 使用文字編輯器，將 **index.html** 檔案內容變更成包含 'Yay!'，然後儲存檔案。

4. 在命令列中使用下列命令，來 **[新增]** 和 **[認可]** 變更，然後將變更 **[推送]** 到遠端儲存機制：

		git add index.html
		git commit -m "Celebration"
		git push azure master

	一旦完成 **[推送]** 命令之後，請重新整理瀏覽器 (您可能需要按 Ctrl+F5，瀏覽器才能正常重新整理)，並請注意頁面內容現已反映最新的認可變更。

### <a id="Step7"></a>部署來自儲存機制網站 (如 BitBucket、CodePlex、Dropbox、GitHub 或 Mercurial) 的檔案

使用本機 Git 將本機檔案推送至 Azure，可讓您將更新從本機專案手動推送至 Azure 中的 Web 應用程式，但若從 BitBucket、CodePlex、Dropbox、GitHub 或 Mercurial 部署，則會轉變成連續部署程序，Azure 將從您的專案中提取最近的更新。

以上兩種方法的結果都是將專案部署到 Web 應用程式，當您有多人同時執行專案，並想確保無論是誰做出最近更新都一定會發行最新版本時，連續部署非常有用。如果您使用上述其中一個工具作為應用程式的中央儲存機制，持續部署也很有用。

無論是從 GitHub、CodePlex 或 BitBucket 部署檔案，都會要求您將本機專案發佈到這些服務的其中之一。如需將專案發佈到這些服務的詳細資訊，請參閱[建立儲存機制 (GitHub)]、[使用 Git 與 CodePlex]、[建立儲存機制 (BitBucket)]、[使用 Dropbox 分享 Git 儲存機制]或[快速入門 - Mercurial]。

1. 先將您的 Web 應用程式檔案放入將用於連續部署的所選儲存機制中。

2. 在入口網站的 Web 應用程式的刀鋒視窗中，向下捲動至 [**部署**] 區段，然後按一下 [**設定連續部署**]。例如，按一下 [**選擇來源**]，然後按一下 **GitHub**。

	![](./media/publishing-with-git/azure6-setup-github.png)
	
2. 在 [**連續部署**] 刀鋒視窗中，按一下 [**授權**]，然後按一下 [**授權**]。Azure 入口網站會將您重新導向到儲存機制網站以完成授權程序。

4. 完成後，請回到 Azure 入口網站，並按一下 [**授權**] 刀鋒視窗中的 [**確定**]。

5. 在 [**連續部署**] 刀鋒視窗中，選擇您想要從其中部署的組織、專案和分支。完成後，按一下 [**確定**]。
  
	![](./media/publishing-with-git/azure7-setup-github-configure.png)

	> [AZURE.NOTE]啟用搭配 GitHub 或 BitBucket 的持續部署時，將會同時顯示公用和私人專案。

Azure 將與所選儲存機制建立關聯，然後從指定的分支提取檔案。此程序完成之後，Web 應用程式刀鋒視窗的 [**部署**] 區段將會顯示 [**現用部署**] 訊息，表示部署成功。

7. 此時，您的專案已從您選擇的儲存機制部署到 Web 應用程式。若要確認 Web 應用程式使用中，按一下入口網站頂端的 **URL**。瀏覽器應該會瀏覽到 Web 應用程式。

8. 若要確認從您選擇的儲存機制進行連續部署，將變更推送至儲存機制。在推送至儲存機制完成後不久，您的 Web 應用程式應該會更新以反映變更。您可以在 Web 應用程式的 [**部署**] 刀鋒視窗上確認它是否已提取更新。

### <a id="Step75"></a>從 BitBucket、CodePlex、Dropbox、GitHub 或 Mercurial 部署 Visual Studio 方案

將 Visual Studio 方案推送至 Azure App Service 中的 Web 應用程式，就像推送簡單的 index.html 檔案一樣容易。Web 應用程式部署程序會簡化所有細節，包含還原 NuGet 相依性，以及建置應用程式二進位檔。您可以只在 Git 儲存機制中遵循維護程式碼的原始檔控制最佳做法，然後讓 Web 應用程式部署負責執行剩餘的部分。

將您的 Visual Studio 方案推播至 Web Apps 的步驟和[上一節](#Step7)的一樣，可提供您設定解決方案和儲存機制的步驟，如下：

-	在您的儲存機制根目錄中，新增 `.gitignore` 檔案，然後指定要從儲存機制排除的所有檔案和資料夾，例如 `Obj`、`Bin`及 `packages` 資料夾 (如需格式資訊，請參閱 [gitignore](http://git-scm.com/docs/gitignore) 文件)。例如：

		[Oo]bj/
		[Bb]in/
		*.user
		/TestResults
		*.vspscc
		*.vssscc
		*.suo
		*.cache
		*.csproj.user
		packages/*
		App_Data/
		/apps
		msbuild.log
		_app/
		nuget.exe

	>[AZURE.NOTE]如果您使用 GitHub，就能在建立儲存機制時選擇是否產生 Visual Studio 特有的 .gitignore 檔案，其中包含所有通用的暫存檔和建置結果等。接著，您可以自訂該檔案，以符合您的特定需求。

-	使用儲存機制根目錄中的 .sln 檔案，將整個解決方案的目錄樹狀結構新增至您的儲存機制。

-	在您的 Visual Studio 方案中，[啟用 NuGet 封裝還原](http://docs.nuget.org/Consume/Package-Restore)可讓 Visual Studio 自動還原遺失的封裝。

一旦您設定儲存機制 (如前所述)，並設定 Azure 中的 Web 應用程式以便從其中一個線上 Git 儲存機制連續發行之後，就能夠在 Visual Studio 中本機開發 ASP.NET 應用程式，並且只需將變更推送至線上 Git 儲存機制，就能連續部署您的程式碼。

## 停用連續部署

連續部署可以從 [**部署**] 刀鋒視窗停用。從 Web 應用程式刀鋒視窗的 [部署] 區段中，按一下 [現用部署]。然後按一下 **[中斷連線**]。

![git-DisconnectFromGitHub](./media/publishing-with-git/azure5-disconnect.png)

對確認訊息回答 [**是**] 之後，如果您要從其他來源設定發行，您可以返回 Web 應用程式的刀鋒視窗，並按一下 [**設定連續部署**]。

## <a id="Step8"></a>疑難排解

使用 Git 發行至 Azure 中的 Web 應用程式時，下列是經常會遇到的錯誤或問題：

****

**徵兆**：無法存取 '[siteURL]'：無法連接至 [scmAddress]

**原因**：如果 Web 應用程式尚未啟動並執行，就會發生這個錯誤。

**解決方式**：在 Azure 入口網站中啟動 Web 應用程式。除非 Web 應用程式正在執行，否則 Git 部署將無法運作。


****

**徵兆**：無法解析主機 'hostname'

**原因**：如果建立 'azure' 遠端時所輸入的位址資訊不正確，便有可能發生此錯誤。

**解決方式**：使用 `git remote -v` 命令，列出所有遠端以及相關聯的 URL。驗證 'azure' 遠端的 URL 是否正確。如有需要，移除此遠端並使用正確的 URL 重新建立。

****

**徵兆**：通常沒有參考且沒有指定；不執行任何動作。或許您應該指定分支，例如 'master'。

**原因**：執行 git 推送操作時，如果您沒有指定分支，且沒有設定 Git 所使用的 push.default 值，便有可能發生此錯誤。

**解決方式**：指定主要分支，重新執行推送操作。例如：

	git push azure master

****

**徵兆**：src refspec [branchname] 沒有任何符合項目。

**原因**：如果您嘗試在 'azure' 遠端上推送至除了主要以外的分支，便有可能發生此錯誤。

**解決方式**：指定主要分支，重新執行推送操作。例如：

	git push azure master

****

**徵兆**：錯誤 - 對遠端儲存機制認可變更，但您的 Web 應用程式未更新。

**原因**：如果您打算部署包含 package.json 檔案的 Node.js 應用程式，但該檔案指出需要額外的模組，便有可能發生此錯誤。

**解決方式**：在發生此錯誤之前應該會有記錄其他包含 'npm ERR!' 的訊息，這些訊息可提供其他關於此失敗的內容。下列是此錯誤的已知原因及其對應的 'npm ERR!' 訊息：

* **格式錯誤的 package.json 檔案**：npm ERR! 無法讀取相依性。

* **原生模組沒有適用於 Windows 的二進位檔發佈**：

	* npm ERR! `cmd "/c" "node-gyp rebuild"` failed with 1

		或

	* npm ERR! [modulename@version] preinstall: `make || gmake`


## 其他資源

* [如何使用適用於 Azure 的 PowerShell]
* [如何使用適用於 Mac 和 Linux 的 Azure 命令列工具]
* [Git 文件]
* [專案 Kudu](https://github.com/projectkudu/kudu/wiki)

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期入門 Web 應用程式。不需要信用卡；沒有承諾。

## 變更的項目
* 如需從網站變更為 App Service 的指南，請參閱：[Azure App Service 及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 如需從舊的入口網站變更為新入口網站的指南，請參閱：[巡覽預覽入口網站的參考](http://go.microsoft.com/fwlink/?LinkId=529715)

[Azure Developer Center]: http://azure.microsoft.com/develop/overview/
[Azure 預覽入口網站]: https://portal.azure.com
[Git website]: http://git-scm.com
[安裝 Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[如何使用適用於 Azure 的 PowerShell]: ../articles/install-configure-powershell.md
[如何使用適用於 Mac 和 Linux 的 Azure 命令列工具]: ../articles/xplat-cli.md
[Git 文件]: http://git-scm.com/documentation

[portal-select-website]: ./media/publishing-with-git/git-select-website.png
[git-WhereIsYourSourceCode]: ./media/publishing-with-git/git-WhereIsYourSourceCode.png
[git-instructions]: ./media/publishing-with-git/git-instructions.png
[portal-deployment-credentials]: ./media/publishing-with-git/git-deployment-credentials.png

[git-ChooseARepositoryToDeploy]: ./media/publishing-with-git/git-ChooseARepositoryToDeploy.png
[hello-git]: ./media/publishing-with-git/git-hello-git.png
[yay]: ./media/publishing-with-git/git-yay.png
[git-githubdeployed]: ./media/publishing-with-git/git-GitHubDeployed.png
[git-GitHubDeployed-Updated]: ./media/publishing-with-git/git-GitHubDeployed-Updated.png
[git-DisconnectFromGitHub]: ./media/publishing-with-git/git-DisconnectFromGitHub.png
[git-DeploymentTrigger]: ./media/publishing-with-git/git-DeploymentTrigger.png
[建立儲存機制 (GitHub)]: https://help.github.com/articles/create-a-repo
[使用 Git 與 CodePlex]: http://codeplex.codeplex.com/wikipage?title=Using%20Git%20with%20CodePlex&referringTitle=Source%20control%20clients&ProjectName=codeplex
[建立儲存機制 (BitBucket)]: https://confluence.atlassian.com/display/BITBUCKET/Create+an+Account+and+a+Git+Repo
[快速入門 - Mercurial]: http://mercurial.selenic.com/wiki/QuickStart
[使用 Dropbox 分享 Git 儲存機制]: https://gist.github.com/trey/2722927
[使用 Visual Studio Online 持續傳遞至 Azure]: ../articles/cloud-services/cloud-services-continuous-delivery-use-vso.md

<!---HONumber=July15_HO2-->