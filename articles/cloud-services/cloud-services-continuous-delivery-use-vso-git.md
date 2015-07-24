<properties 
	pageTitle="在 Azure 中使用 Visual Studio Online 來連續傳遞" 
	description="了解如何設定 Visual Studio Online Team 專案，以自動建置和部署至 Azure App Service 或雲端服務中的 Web 應用程式功能。" 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="kempb" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/07/2015" 
	ms.author="kempb"/>




# 使用 Visual Studio Online 和 Git 連續傳遞至 Azure

您可以使用 Visual Studio Online Team 專案託管原始程式碼的 Git 儲存機制，並在每次將認可推送至儲存機制時，自動建置該機制並部署至 Azure Web 應用程式或雲端服務。

您需要安裝 Visual Studio 2013 和 Azure SDK。如果尚無 Visual Studio 2013，請至 **www.visualstudio.com** 選擇 [免費開始用][](http://www.visualstudio.com) 連結來下載。從[這裡](http://go.microsoft.com/fwlink/?LinkId=239540)安裝 Azure SDK。


> [AZURE.NOTE]您需要 Visual Studio Online 帳戶，才能完成本教學課程：您可以[開啟免費的 Visual Studio Online 帳戶](http://go.microsoft.com/fwlink/p/?LinkId=512979)。

若要使用 Visual Studio Online 將雲端服務設定為自動建立和部署至 Azure，請依照下列步驟進行：

-   [步驟 1：建立 Git 儲存機制。][]

-   [步驟 2：建立專案並推送至 Git 儲存機制。][]

-   [步驟 3：將專案連線至 Azure。][]

-   [步驟 4：進行變更並觸發重建和重新部署。][]

-   [步驟 5：重新部署舊版組建 (選用)][]

-   [步驟 6：變更生產部署][]

-	[步驟 7：從工作分支部署][]

## <a name="step1"></a>步驟 1：建立 Git 儲存機制


1. 如果您還沒有 Visual Studio Online 帳戶，請依照[這裡](http://go.microsoft.com/fwlink/?LinkId=397665)的指示進行。建立小組專案時，請選擇 Git 作為原始檔控制系統。依照指示將 Visual Studio 連接至小組專案。

2. 在 Team Explorer 中，選擇 [**複製這個儲存機制**] 連結。 
![][3]

3. 指定本機複本的位置，然後選擇 [**複製**] 按鈕。
 
## <a name="step2"> </a>步驟 2：建立專案並向儲存機制認可該專案

1. 在 Team Explorer 中，在 [方案] 區段中選擇 [新增] 連結，在本機儲存機制中建立新專案。<br/>
![][4]

2. 您可以依照此逐步解說的步驟部署 Web 應用程式或雲端服務 (Azure 應用程式)。建立新的 Azure 雲端服務專案，或建立新的 ASP.NET MVC 專案。請確定專案以 .NET Framework 4 或 4.5 為目標，如果是建立雲端服務專案，請加入 ASP.NET MVC Web 角色和背景工作角色。如果要建立 Web 應用程式，請選擇 ASP.NET Web 應用程式的專案範本，然後選擇 [MVC]。請參閱「[在 Azure App Service 中建立 ASP.NET Web 應用程式](../web-sites-dotnet-get-started.md)」。

3. 開啟方案的捷徑功能表，選擇 [**認可**]。<br/>
![][7]

4. 如果是第一次在 Visual Studio Online 中使用 Git，您需要提供一些資訊在 Git 中識別您的身分。在 Team Explorer 的 [暫止的變更] 區域中，輸入您的使用者名稱和電子郵件地址。輸入認可的註解，然後選擇 [**認可**]。<br/>
![][8]

5. 請注意簽入時用來包含或排除特定變更的選項。如果已排除您要的變更，請選擇 [**全部包含**]。<br/>

6. 您現在已在儲存機制的本機複本中認可變更。接下來，將這些變更同步至伺服器。選擇 [**同步處理**] 連結。

## <a name="step3"> </a>步驟 3：將專案連接至 Azure

1. 現在，您在 Visual Studio Online 中有一個 Git 儲存機制，裡面還有一些原始程式碼，您可以準備將 Git 儲存機制連接至 Azure。在 [Azure 入口網站](http://manage.windowsazure.com)中，選取您的雲端服務或 Web 應用程式，或選取左下方的 + 圖示，選擇 [**雲端服務**] 或 [**Web 應用程式**]，然後選擇 [**快速建立**]，建立新的雲端服務或 Web 應用程式。<br.>
![][9]

3. 若是雲端服務，請擇 [**Set up publishing with Visual Studio Online**] 連結。若是 Web 應用程式，請選擇「**從來源控制設定部署**」連結。<br/>
![][10]

2. 在精靈中，在文字方塊中輸入 Visual Studio Online 帳戶的名稱，然後選擇 [**立即授權**] 連結。可能會要求您登入。<br/>
![][11]

3. 在 OAuth 快顯對話方塊中，選擇 [**接受**]，授權 Azure 在 Visual Studio Online 中設定小組專案。<br/>
![][12]

4. 授權成功時，將出現含有 Visual Studio Online 小組專案的下拉式清單。選取您在先前步驟中建立的小組專案，然後選擇精靈的勾選記號按鈕。<br/>
![][13]

您下次將認可推送至儲存機制時，Visual Studio Online 將會建置專案並部署至 Azure。<br/>


## <a name="step4"> </a>步驟 4：觸發重建和重新部署專案

1. 在 Visual Studio 中，開啟檔案進行變更。例如，在 MVC Web 角色中，變更 Views\Shared 資料夾下的 _Layout.cshtml 檔案。<br/>
![][17]

2. 編輯網站的頁尾文字，然後儲存檔案。<br/>
![][18]

3. 在 [方案總管] 中，開啟方案節點、專案節點或您變更之檔案的快顯功能表，然後選擇 [認可]。<br/>

4. 輸入註解並選擇 [**認可**]。<br/>
![][20]

5. 選擇 [**同步處理**] 連結。<br/>
![][38]

6. 選擇 [推送] 連結，將認可推送至 Visual Studio Online 中的儲存機制(您也可以使用 [同步處理] 按鈕，將認可複製到儲存機制。差別在於 [**同步處理**] 還會從儲存機制中提取最新的變更)。<br/>
![][39]

7. 選擇 [首頁] 按鈕回到 Team Explorer 首頁。<br/>
![][21]

8. 選擇 [**組建**] 來檢視進行中的組件。<br/>
![][22]
<br/>
Team Explorer 會顯示簽入已觸發的組建。<br/>
![][23]

9. 若要在組建進行時檢視詳細記錄，請按兩下進行中組建的名稱。<br/>

10. 當組建進行時，查看您使用精靈連結至 Azure 時所建立的組建定義。開啟組建定義的捷徑功能表，然後選擇 [編輯組建定義]。<br/>
![][25]
<br/>
在 [**觸發程序**] 索引標籤中，您會看到已設為依預設每次簽入時建置的組建定義。(若是雲端服務，Visual Studio Online 會自動建置主要分支並部署至預備環境。您仍然必須執行手動步驟來部署至即時網站。對於沒有預備環境的 Web 應用程式，它會將主要分支直接部署到即時網站。<br/>
![][26]
<br/>
在 [**處理序**] 索引標籤中，您可以看到部署環境已設為您的雲端服務或 Web 應用程式的名稱。<br/>
![][27]
<br/>
如果不想要使用預設值，請指定屬性的值。Azure 發行屬性在 [部署] 區段中，您也可能需要設定 MSBuild 參數。例如，在雲端服務專案中，若要指定 "Cloud" 以外的服務組態，請將 MSbuild 參數設為 /p:TargetProfile=*YourProfile*，其中，*YourProfile* 符合一個以 ServiceConfiguration.*YourProfile*.cscfg 命名的服務組態檔。下表顯示 [部署] 區段中可用的屬性：
	<table>
<tr><td><b>屬性</b></td><td><b>預設值</b></td></tr>
><tr><td>允許未受信任的憑證</td><td>如果為 false，SSL 憑證必須經過根授權單位簽署。</td></tr>
<tr><td>允許升級</td><td>允許部署更新現有的部署而非建立新的部署。保留 IP 位址。</td></tr><tr><td>不要刪除</td><td>如果為 true，則不要覆寫現有不相關的部署 (允許升級)。</td></tr>
<tr><td>部署設定的路徑</td><td>Web 應用程式的 .pubxml 檔的路徑，這是儲存機制之根資料夾的相對路徑。雲端服務則會忽略。</td></tr>
<tr><td>SharePoint 部署環境</td><td>與服務名稱相同</td></tr>
<tr><td>Azure 部署環境</td><td>Web 應用程式或雲端服務的名稱</td></tr>
</table> <br/>

11. 現在應該已順利完成您的組建。<br/>
![][28]

12. 如果按兩下組建名稱，Visual Studio 會顯示 [**組建摘要**]，包括與單元測試專案相關聯的任何測試結果。<br/>
![][29]

13. 在 [Azure 入口網站](http://manage.windowsazure.com)中，選取預備環境之後，您可以在 [部署] 索引標籤上檢視相關聯的部署。<br/>
![][30]

14.	瀏覽至網站的 URL。若是 Web 應用程式，請選擇入口網站中的 [**瀏覽**] 按鈕。若是雲端服務，請在 [**儀表板**] 頁面的 [**快速概覽**] 區段中選擇 URL，以顯示預備環境。依預設，來自雲端服務連續整合的部署會發行至預備環境。您可以將 [替代雲端服務環境] 屬性設為 [生產] 來變更此設定。以下是網站 URL 在雲端服務儀表板頁面上的位置：<br/>
![][31]
<br/>
新的瀏覽器索引標籤會開啟，顯示您執行中的網站。<br/>
![][32]

15.	如果對專案進行其他變更，則會觸發更多組建，將累積多個部署。最後一個會標示為「作用中」。<br/>
![][33]

## <a name="step5"> </a>步驟 5：重新部署舊版組建

此為選用步驟。在管理入口網站中，選取先前的部署，然後按一下 [**重新部署**]，將網站倒回到更早的簽入。請注意，這會在 TFS 中觸發新的組建，並在部署歷程記錄中建立新的項目。<br/>
![][34]

## <a name="step6"> </a>步驟 6：變更生產部署

 準備就緒後，您可以在管理入口網站中選擇 [**交換**] 按鈕，將預備環境升級至生產環境。新部署的預備環境會升級至「生產」，而先前的生產環境 (若有的話) 會變成預備環境。「作用中」部署可能與生產和預備環境不同，但最近組建的部署歷程記錄都一樣，與環境無關。<br/>
![][35]

## <a name="step7"> </a>步驟 6：透過工作分支部署。

使用 Git 時，您通常會在工作分支中進行變更，等到開發達到完成狀態時，再整合至主要分支。在專案的開發階段期間，您可以建置工作分支並部署至 Azure。

1. 在 Team Explorer 中，選擇 [**首頁**] 按鈕，然後選擇 [**分支**] 按鈕。<br/>
![][40]

2. 選擇 [**新增分支**] 連結。<br/>
![][41]

3. 輸入分支的名稱，例如 "working"，然後選擇 [建立分支]。這樣會建立新的本機分支。<br/>
![][42]

4. 發行分支。在 [**取消發行的分支**] 中選擇分支名稱，然後選擇 [**發行**]。<br/>
![][44]

6. 依預設，只有主要分支的變更才會觸發連續組建。若要設定工作分支的連續組建，請在 Team Explorer 中選擇 [組建] 頁面，然後選擇 [編輯組建定義]。

7. 開啟 [來源設定] 索引標籤。在 [**監視連續整合和組建的分支**] 下，選擇 [**按一下這裡加入新的列**]。<br/>
![][47]

8. 指定您建立的分支，例如 refs/heads/working。
![][48]

9. 變更程式碼，開啟已變更之檔案的快顯功能表，然後選擇 [**認可**]。<br/>
![][43]

10. 選擇 [**未同步處理的認可**] 連結，再選擇 [**同步處理**] 按鈕或 [**推送**] 連結，將變更複製到 Visual Studio Online 中工作分支的複本。
![][45]

11. 瀏覽至 [組建] 檢視，找出工作分支剛觸發的組建。

如需詳細資訊，請參閱 [Visual Studio Online](http://go.microsoft.com/fwlink/?LinkId=253861)。如需有關使用 Git 搭配 Visual Studio Online 的其他祕訣，請參閱「[在 Git 中共用程式碼](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx)」；關於使用非 Visual Studio Online 管理的 Git 儲存機制發佈至 Azure 的詳細資訊，請參閱「[在 Azure App Service 中使用 GIT 連續部署](../web-sites-publish-source-control.md)」。

[步驟 1：建立 Git 儲存機制。]: #step1
[步驟 2：建立專案並推送至 Git 儲存機制。]: #step2
[步驟 3：將專案連線至 Azure。]: #step3
[步驟 4：進行變更並觸發重建和重新部署。]: #step4
[步驟 5：重新部署舊版組建 (選用)]: #step5
[步驟 6：變更生產部署]: #step6
[步驟 7：從工作分支部署]: #step7
[0]: ./media/cloud-services-continuous-delivery-use-vso/tfs0.PNG
[1]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateTeamProjectInGit.PNG
[2]: ./media/cloud-services-continuous-delivery-use-vso/tfs2.png
[3]: ./media/cloud-services-continuous-delivery-use-vso-git/CloneThisRepository.PNG
[4]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateNewSolutionInClonedRepo.PNG
[7]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitMenuItem.PNG
[8]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[9]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateCloudService.PNG
[10]: ./media/cloud-services-continuous-delivery-use-vso-git/SetUpPublishingWithVSO.PNG
[11]: ./media/cloud-services-continuous-delivery-use-vso-git/AuthorizeConnection.PNG
[12]: ./media/cloud-services-continuous-delivery-use-vso-git/ConnectionRequest.PNG
[13]: ./media/cloud-services-continuous-delivery-use-vso-git/ChooseARepo3.PNG
[14]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
[15]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
[16]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
[17]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs17.png
[18]: ./media/cloud-services-continuous-delivery-use-vso-git/MakeACodeChange.PNG
[20]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[21]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerHome.png
[22]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerBuilds.PNG
[23]: ./media/cloud-services-continuous-delivery-use-vso-git/BuildInQueue.png
[24]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
[25]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs25.png
[26]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs26.png
[27]: ./media/cloud-services-continuous-delivery-use-vso-git/ProcessTab.PNG
[28]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs28.png
[29]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs29.png
[30]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs30.png
[31]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs31.png
[32]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs32.png
[33]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs33.png
[34]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs34.png
[35]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs35.png
[36]: ./media/cloud-services-continuous-delivery-use-vso/tfs36.PNG
[37]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateANewAccount.PNG
[38]: ./media/cloud-services-continuous-delivery-use-vso-git/SyncChanges2.PNG
[39]: ./media/cloud-services-continuous-delivery-use-vso-git/PushCurrentBranch.PNG
[40]: ./media/cloud-services-continuous-delivery-use-vso-git/BranchesInTeamExplorer.PNG
[41]: ./media/cloud-services-continuous-delivery-use-vso-git/NewBranch.PNG
[42]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateBranch.PNG
[43]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[44]: ./media/cloud-services-continuous-delivery-use-vso-git/PublishBranch.PNG
[45]: ./media/cloud-services-continuous-delivery-use-vso-git/SyncChanges2.PNG
[47]: ./media/cloud-services-continuous-delivery-use-vso-git/SourceSettingsPage.PNG
[48]: ./media/cloud-services-continuous-delivery-use-vso-git/IncludeWorkingBranch.PNG
 

<!----HONumber=62-->