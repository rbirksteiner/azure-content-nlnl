<properties 
	pageTitle="在 Azure 中使用 Visual Studio Online 來連續傳遞" 
	description="了解如何設定 Visual Studio Online Team 專案，以自動建置和部署至 Azure App Service 或雲端服務中的 Web 應用程式功能。" 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/08/2015" 
	ms.author="kempb"/>


# 使用 Visual Studio Online 連續傳遞至 Azure

  您可以將 Visual Studio Online 小組專案設定為自動建置和部署至 Azure Web 應用程式或雲端服務。(如需如何使用「內部部署」的 Team Foundation Server 來設定連續組建及部署系統的相關資訊，請參閱 [Azure 中雲端服務的連續傳遞](cloud-services-dotnet-continuous-delivery.md))。

本教學課程假設您已安裝 Visual Studio 2013 和 Azure SDK。如果尚無 Visual Studio 2013，請至 **www.visualstudio.com** 選擇 [免費開始用][](http://www.visualstudio.com) 連結來下載。從[這裡](http://go.microsoft.com/fwlink/?LinkId=239540)安裝 Azure SDK。

> [AZURE.NOTE]您需要 Visual Studio Online 帳戶，才能完成本教學課程：您可以[開啟免費的 Visual Studio Online 帳戶](http://go.microsoft.com/fwlink/p/?LinkId=512979)。

若要使用 Visual Studio Online 將雲端服務設定為自動建立和部署至 Azure，請依照下列步驟進行：

-   [步驟 1：建立 Team 專案。][]

-   [步驟 2：將專案簽入至原始檔控制。][]

-   [步驟 3：將專案連線至 Azure。][]

-   [步驟 4：進行變更並觸發重建和重新部署。][]

-   [步驟 5：重新部署舊版組建 (選用)][]

-   [步驟 6：變更生產部署 (僅雲端服務)][]

-	[步驟 7：執行單元測試 (選用)][]

## <a name="step1"></a>步驟 1：建立 Team 專案

請遵循[這裡](http://go.microsoft.com/fwlink/?LinkId=512980)的指示來建立您的 Team 專案，並將專案連結至 Visual Studio。本逐步解說假設您使用 Team Foundation 版本控制 (TFVC) 做為原始檔控制解決方案。如果您想使用 Git 進行版本控制，請參閱[本逐步解說的 Git 版本](http://go.microsoft.com/fwlink/p/?LinkId=397358) (英文)。

## <a name="step2"> </a>步驟 2：將專案簽入原始檔控制

1. 在 Visual Studio 中，開啟您要部署的方案，或建立新方案。您可以依照此逐步解說的步驟部署 Web 應用程式或雲端服務 (Azure 應用程式)。如果要建立新方案，請建立新的 Azure 雲端服務專案，或建立新的 ASP.NET MVC 專案。請確定專案以 .NET Framework 4 或 4.5 為目標，如果是建立雲端服務專案，請加入 ASP.NET MVC Web 角色和背景工作角色，然後對 Web 角色選擇網際網路應用程式。出現提示時，選擇 [**網際網路應用程式**]。如果要建立 Web 應用程式，請選擇 ASP.NET Web 應用程式的專案範本，然後選擇 [MVC]。請參閱「[在 Azure App Service 中建立 ASP.NET Web 應用程式](../web-sites-dotnet-get-started.md)」。

> [AZURE.NOTE]Visual Studio Online 目前僅支援 Visual Studio Web 應用程式的 CI 部署。Web Site 專案超出範圍。


2. 開啟方案的內容功能表，選取 [**將方案加入至原始檔控制**]。<br/>
![][5]

3. 接受或變更預設值，然後選擇 [確定] 按鈕。處理完成之後，[方案總管] 中會出現原始檔控制圖示。<br/>
![][6]

4. 開啟解決方案的捷徑功能表，選擇 [**簽入**]。<br/>
![][7]

5. 在 Team Explorer 的 [暫止的變更] 區域中，輸入簽入註解，然後選擇 [**簽入**] 按鈕。<br/>
![][8]

<br/> 請注意，簽入時用來包含或排除特定變更的選項。如果已排除您要的變更，請選擇 [**全部包含**]。<br/>
![][9]

## <a name="step3"> </a>步驟 3：將專案連接至 Azure

1. 您現有一個 VSO 小組專案，且裡面有一些原始程式碼，可以準備將小組專案連線至 Azure。在 [Azure 入口網站](http://manage.windowsazure.com)中，選取您的雲端服務或 Web 應用程式，或選取左下方的 + 圖示，選擇 [**雲端服務**] 或 [**Web 應用程式**]，然後選取 [**快速建立**]，建立新的雲端服務或 Web 應用程式。選擇 [**使用 Visual Studio Online 設定發行**] 連結。<br/>
![][10]

2. 在精靈中，在文字方塊中輸入 Visual Studio Online 帳戶的名稱，然後按一下 [立即授權] 連結。可能會要求您登入。<br/>
![][11]

3. 在 OAuth 快顯對話方塊中，選擇 [**接受**]，授權 Azure 在 VSO 中設定 Team 專案。<br/>
![][12]

4. 授權成功時，將出現含有 Visual Studio Online 小組專案清單的下拉清單。選取您在先前步驟中建立的小組專案，然後選擇精靈的勾選記號按鈕。<br/>
![][13]

5. 連結專案之後，將出現一些指示，指出如何將變更簽入至 Visual Studio Online 小組專案。下次簽入時，Visual Studio Online 就會建立專案並部署至 Azure。現在就試著按一下 [**從 Visual Studio 簽入**] 連結，再按一下 [**啟動 Visual Studio**] 連結 (或入口網站畫面底部同等的 [**Visual Studio**] 按鈕)。<br/>
![][14]

## <a name="step4"> </a>步驟 4：觸發重建和重新部署專案

1. 在 Visual Studio 的 Team Explorer 中，按一下 [**原始檔控制總管**] 連結。<br/>
![][15]

2. 瀏覽至方案檔並開啟。<br/>
![][16]

3. 在 [方案總管] 中，開啟檔案進行變更。例如，在 MVC Web 角色中，變更 Views\Shared 資料夾下的 _Layout.cshtml 檔案。<br/>
![][17]

4. 編輯網站的標誌，然後按一下 Ctrl+S 儲存檔案。<br/>
![][18]

5. 在 Team Explorer 中，選擇 [**暫止的變更**] 連結。<br/>
![][19]

6. 輸入註解，然後選擇 [**簽入**] 按鈕。<br/>
![][20]

7. 選擇 [首頁] 按鈕回到 Team Explorer 首頁。<br/>
![][21]

8. 選擇 [組建] 連結來檢視進行中的組件。<br/>
![][22]
<br/>
Team Explorer 會顯示簽入已觸發的組建。<br/>
![][23]

9. 按兩下進行中的組建名稱，檢視進行中組建的詳細記錄。<br/>
![][24]

10. 當組建進行時，查看您使用精靈將 TFS 連結至 Azure 時所建立的組建定義。開啟組建定義的捷徑功能表，然後選擇 [編輯組建定義]。<br/>
![][25]
<br/>
在 [**觸發程序**] 索引標籤中，您會看到已設為依預設每次簽入時建置的組建定義。<br/>
![][26]
<br/>
在 [**處理序**] 索引標籤中，您可以看到部署環境已設為您的雲端服務或 Web 應用程式的名稱。如果您使用的是 Web 應用程式，則顯示的屬性跟下面的屬性不同。<br/>
![][27]
<br/>
如果不想要使用預設值，請指定屬性的值。Azure 發行屬性在 [部署] 區段中。下表顯示 [部署] 區段中可用的屬性：
	<table>
<tr><td><b>屬性</b></td><td><b>預設值</b></td></tr>
><tr><td>允許未受信任的憑證</td><td>如果為 false，SSL 憑證必須經過根授權單位簽署。</td></tr>
<tr><td>允許升級</td><td>允許部署更新現有的部署，而不是建立新的部署。保留 IP 位址。</td></tr><tr><td>不要刪除</td><td>如果為 true，則不要覆寫現有不相關的部署 (允許升級)。</td></tr>
<tr><td>部署設定的路徑</td><td>Web 應用程式的 .pubxml 檔的路徑，這是儲存機制之根資料夾的相對路徑。雲端服務則會忽略。</td></tr>
<tr><td>SharePoint 部署環境</td><td>與服務名稱相同</td></tr>
<tr><td>Azure 部署環境</td><td>Web 應用程式或雲端服務的名稱</td></tr>
</table> <br/>

如果使用多個服務組態 (.cscfg 檔)，則可以在 [組建、進階、MSBuild 引數] 設定中指定所需的服務組態。例如，若要使用 ServiceConfiguration.Test.cscfg，請設定 MSBuild 引數的命令列選項 /p:TargetProfile=Test。<br/>
![][38]

11. 現在應該已順利完成您的組建。<br/>
![][28]

12. 如果按兩下組建名稱，Visual Studio 會顯示 [**組建摘要**]，包括與單元測試專案相關聯的任何測試結果。<br/>
![][29]

13. 在 [Azure 入口網站](http://manage.windowsazure.com)中，選取預備環境之後，您可以在 [部署] 索引標籤上檢視相關聯的部署。<br/>
![][30]

14.	瀏覽至網站的 URL。若是 Web 應用程式，請按一下命令列上的 [瀏覽] 按鈕。若是雲端服務，請在 [**儀表板**] 頁面的 [**快速概覽**] 區段中選擇 URL，以顯示雲端服務的預備環境。依預設，來自雲端服務連續整合的部署會發行至預備環境。您可以將 [替代雲端服務環境] 屬性設為 [生產] 來變更此設定。以下是網站 URL 在雲端服務儀表板頁面上的位置：<br/>
![][31]
<br/>
新的瀏覽器索引標籤會開啟，顯示您執行中的網站。<br/>
![][32]

15.	若為雲端服務，如果對專案進行其他變更，則會觸發更多組建，將累積多個部署。最後一個會標示為「作用中」。<br/>
![][33]

## <a name="step5"> </a>步驟 5：重新部署舊版組建

此步驟適用於雲端服務，且為選用的。在管理入口網站中，選取先前的部署，然後按一下 [**重新部署**] 按鈕，將網站倒回到更早的簽入。請注意，這會在 TFS 中觸發新的組建，並在部署歷程記錄中建立新的項目。<br/>
![][34]

## <a name="step6"> </a>步驟 6：變更生產部署

此步驟僅適用於雲端服務，不適用於 Web 應用程式。準備就緒後，您可以在管理入口網站中選擇 [交換] 按鈕，將預備環境升級至生產環境。新部署的預備環境會升級至「生產」，而先前的生產環境 (若有的話) 會變成預備環境。「作用中」部署可能與生產和預備環境不同，但最近組建的部署歷程記錄都一樣，與環境無關。<br/>
![][35]

## <a name="step7"> </a>步驟 7：執行單元測試

此步驟僅適用於 Web 應用程式，不適用於雲端服務。若要為部署的品質把關，您可以執行單元測試；如果測試失敗，則可以停止部署。

1.  在 Visual Studio 中，加入單元測試專案。<br/>
![][39]

2.  將專案參考加入您要測試的專案。<br/>
![][40]

3.  加入一些單元測試。若要開始使用，請嘗試一律會通過的虛擬測試。

		using System;
		using Microsoft.VisualStudio.TestTools.UnitTesting;
		
		namespace UnitTestProject1
		{
		    [TestClass]
		    public class UnitTest1
		    {
		        [TestMethod]
		        [ExpectedException(typeof(NotImplementedException))]
		        public void TestMethod1()
		        {
		            throw new NotImplementedException();
		        }
		    }
		}

4.  編輯組建定義，選擇 [處理序] 索引標籤，然後展開 [測試] 節點。


5.  將 [在測試失敗時使組建失敗] 設為 True。換句話說，除非通過測試，否則不會進行部署。<br/>
![][41]

6.  將新組建排入佇列。<br/>
![][42]
<br/>
![][43]

7. 在建置進行時，檢查其進度。<br/>
![][44]
<br/>
![][45]

7. 在建置完成時，檢查測試結果。<br/>
![][46]
<br/>
![][47]

8.  嘗試建立將失敗的測試。透過複製第一個測試、將其重新命名，並將 NotImplementedException 標記為預期的例外狀況的程式碼行加上註解，來加入新的測試。

		[TestMethod]
		//[ExpectedException(typeof(NotImplementedException))]
		public void TestMethod2()
		{
		    throw new NotImplementedException();
		}

9. 簽入變更，以將新組建排入佇列。<br/>
![][48]

10. 檢視測試結果，以查看失敗的詳細資料。<br/>
![][49]
<br/>
![][50]

如需在 Visual Studio Online 中進行單元測試的詳細資訊，請參閱[在建置中執行單元測試](http://go.microsoft.com/fwlink/p/?LinkId=510474)。

如需詳細資訊，請參閱 [Visual Studio Online](http://go.microsoft.com/fwlink/?LinkId=253861)。如果您使用的是 Git，請參閱「[在 Git 中共用程式碼](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx)」和「[在 Azure App Service 中使用 GIT 連續部署](../web-sites-publish-source-control.md)」。

[步驟 1：建立 Team 專案。]: #step1
[步驟 2：將專案簽入至原始檔控制。]: #step2
[步驟 3：將專案連線至 Azure。]: #step3
[步驟 4：進行變更並觸發重建和重新部署。]: #step4
[步驟 5：重新部署舊版組建 (選用)]: #step5
[步驟 6：變更生產部署 (僅雲端服務)]: #step6
[步驟 7：執行單元測試 (選用)]: #step7
[0]: ./media/cloud-services-continuous-delivery-use-vso/tfs0.PNG
[1]: ./media/cloud-services-continuous-delivery-use-vso/tfs1.png
[2]: ./media/cloud-services-continuous-delivery-use-vso/tfs2.png


[5]: ./media/cloud-services-continuous-delivery-use-vso/tfs5.png
[6]: ./media/cloud-services-continuous-delivery-use-vso/tfs6.png
[7]: ./media/cloud-services-continuous-delivery-use-vso/tfs7.png
[8]: ./media/cloud-services-continuous-delivery-use-vso/tfs8.png
[9]: ./media/cloud-services-continuous-delivery-use-vso/tfs9.png
[10]: ./media/cloud-services-continuous-delivery-use-vso/tfs10.png
[11]: ./media/cloud-services-continuous-delivery-use-vso/tfs11.png
[12]: ./media/cloud-services-continuous-delivery-use-vso/tfs12.png
[13]: ./media/cloud-services-continuous-delivery-use-vso/tfs13.png
[14]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
[15]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
[16]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
[17]: ./media/cloud-services-continuous-delivery-use-vso/tfs17.png
[18]: ./media/cloud-services-continuous-delivery-use-vso/tfs18.png
[19]: ./media/cloud-services-continuous-delivery-use-vso/tfs19.png
[20]: ./media/cloud-services-continuous-delivery-use-vso/tfs20.png
[21]: ./media/cloud-services-continuous-delivery-use-vso/tfs21.png
[22]: ./media/cloud-services-continuous-delivery-use-vso/tfs22.png
[23]: ./media/cloud-services-continuous-delivery-use-vso/tfs23.png
[24]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
[25]: ./media/cloud-services-continuous-delivery-use-vso/tfs25.png
[26]: ./media/cloud-services-continuous-delivery-use-vso/tfs26.png
[27]: ./media/cloud-services-continuous-delivery-use-vso/tfs27.png
[28]: ./media/cloud-services-continuous-delivery-use-vso/tfs28.png
[29]: ./media/cloud-services-continuous-delivery-use-vso/tfs29.png
[30]: ./media/cloud-services-continuous-delivery-use-vso/tfs30.png
[31]: ./media/cloud-services-continuous-delivery-use-vso/tfs31.png
[32]: ./media/cloud-services-continuous-delivery-use-vso/tfs32.png
[33]: ./media/cloud-services-continuous-delivery-use-vso/tfs33.png
[34]: ./media/cloud-services-continuous-delivery-use-vso/tfs34.png
[35]: ./media/cloud-services-continuous-delivery-use-vso/tfs35.png
[36]: ./media/cloud-services-continuous-delivery-use-vso/tfs36.PNG
[37]: ./media/cloud-services-continuous-delivery-use-vso/tfs37.PNG
[38]: ./media/cloud-services-continuous-delivery-use-vso/AdvancedMSBuildSettings.PNG
[39]: ./media/cloud-services-continuous-delivery-use-vso/AddUnitTestProject.PNG
[40]: ./media/cloud-services-continuous-delivery-use-vso/AddProjectReferences.PNG
[41]: ./media/cloud-services-continuous-delivery-use-vso/EditBuildDefinitionForTest.PNG
[42]: ./media/cloud-services-continuous-delivery-use-vso/QueueNewBuild.PNG
[43]: ./media/cloud-services-continuous-delivery-use-vso/QueueBuildDialog.PNG
[44]: ./media/cloud-services-continuous-delivery-use-vso/BuildInTeamExplorer.PNG
[45]: ./media/cloud-services-continuous-delivery-use-vso/BuildRequestInfo.PNG
[46]: ./media/cloud-services-continuous-delivery-use-vso/BuildSucceeded.PNG
[47]: ./media/cloud-services-continuous-delivery-use-vso/TestResultsPassed.PNG
[48]: ./media/cloud-services-continuous-delivery-use-vso/CheckInChangeToMakeTestsFail.PNG
[49]: ./media/cloud-services-continuous-delivery-use-vso/TestsFailed.PNG
[50]: ./media/cloud-services-continuous-delivery-use-vso/TestsResultsFailed.PNG
 

<!----HONumber=62-->