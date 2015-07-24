<properties 
	pageTitle="在原始檔控制中儲存伺服器指令碼 - Azure 行動服務" 
	description="了解如何使用新的 Relic 附加元件監視行動服務。" 
	documentationCenter="" 
	authors="stepsic-microsoft-com" 
	manager="carolz" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="03/16/2015" 
	ms.author="stepsic"/>

# 使用 New Relic 監視行動服務

本主題說明如何設定協力廠商 New Relic 附加元件使用 Azure 行動服務，以提供增強式的行動服務監視。

本教學課程將引導您完成下列步驟：

1. [使用 Azure 市集註冊 New Relic]。
2. [安裝 New Relic 模組]。
3. [啟用行動服務的 New Relic 開發人員分析]。
4. [在 New Relic 儀表板中監視行動服務]。

若要完成本教學課程，您必須已經建立行動服務，方法是完成[開始使用行動服務]或[開始使用資料]教學課程。

##<a name="sign-up"></a>使用 Azure 市集註冊 New Relic

第一個步驟是購買 New Relic 服務。本教學課程說明如何從 Azure 市集購買此服務。行動服務支援不是購買自 Azure 市集的 New Relic 訂閱。

1. 登入 [Azure 管理入口網站](https://manage.windowsazure.com)。

2. 在管理入口網站的下方窗格中，按一下 [新增]。

3. 按一下 [市集]。

4. 在 [選擇附加元件] 對話方塊中，選取 [New Relic]，然後按 [下一步]。

5. 在 [個人化附加元件] 對話方塊中，選取您要的 New Relic 方案。

7. 輸入要在 Azure 設定中顯示的 New Relic 服務名稱，或接受預設值 **NewRelic**。此名稱在已訂閱的 Azure 市集項目清單中必須是唯一的。

8. 選擇地區值，例如 [美國西部]。

9. 按 [下一步]。

10. 在 [檢閱購買項目] 對話方塊中，檢閱方案和價格資訊，並檢閱法律條款。如果您同意接受這些條款，請按一下 [購買]。

11. 按一下 [購買] 之後，New Relic 帳戶的建立程序就會開始。您可以在 Azure 管理入口網站中監視狀態。

##<a name="install-module"></a>安裝 New Relic 模組

在註冊 New Relic 服務之後，您必須在行動服務中安裝 New Relic Node.js 模組。您必須啟用行動服務的原始檔控制，才能上傳此模組。

1. 如果您尚未這麼做，請依照[在原始檔控制中儲存伺服器指令碼]教學課程中的步驟進行，以啟用行動服務的原始檔控制、複製儲存機制，及安裝 <a href="http://nodejs.org/" target="_blank">Node Package Manager (NPM)</a>。

2. 瀏覽至本機 Git 儲存機制的 `.\service` 資料夾，然後在命令提示字元中執行下列命令：

		npm install newrelic

	NPM 會在 `\newrelic` 子目錄中安裝 [New Relic 模組][newrelic]。

3. 開啟 Git 命令列工具，例如 **GitBash** (Windows) 或 **Bash** (Unix Shell)，並在 Git 命令提示字元中鍵入下列命令：

		$ git add .
		$ git commit -m "added newrelic module"
		$ git push origin master
		
	這會將新的 `newrelic` 模組上傳至您的行動服務。

接著，您將在[管理入口網站][Azure Management Portal]中啟用 New Relic 監視行動服務。

##<a name="enable-service"></a>啟用行動服務的 New Relic 開發人員分析

1. 在[管理入口網站][Azure Management Portal]中，選取您的行動服務，然後按一下 [設定] 索引標籤。

	![][0]

2. 向下捲動到 [開發人員分析] 並執行下列其中一個動作，視 New Relic 訂閱的購買方式而定：

	+ 在 Azure 市集中購買：

		按一下 [附加元件] ，從 [選擇附加元件] 中選取 New Relic 附加元件，然後按一下 [儲存]。

		![][1]

	+ 直接從 New Relic 購買：

		按一下 [自訂]，從 [提供者] 中選取 New Relic，輸入您的金鑰，然後按一下 [儲存]。

		![][2]

		您可以從您的 New Relic 儀表板中取得金鑰。

3. 在註冊完成後，您將會在 [應用程式設定] 中看到新值：

	![][3]

##<a name="monitor"></a>在 New Relic 儀表板中監視行動服務

1. 執行您的用戶端應用程式，以對行動服務產生讀取、建立、更新和刪除要求。

2. 等候幾分鐘的時間來完成資料處理，然後瀏覽到 New Relic 儀表板。

	當以附加元件方式購買 New Relic 訂閱時，請在[管理入口網站][Azure Management Portal]中選取此訂閱，並按一下 [管理]。

3. 在 New Relic 中按一下 [應用程式]，然後按一下您的行動服務。

	![][4]

4. 按一下 [Web 交易]，以查看您剛對行動服務所做的最新要求：

	![][5]

##<a name="next-steps"> </a>後續步驟

+ 若要最佳化您的 **iOS**/**Android** 行動應用程式效能，請參閱 [New Relic Mobile]。
+ 如需定價資訊，請參閱＜[Azure 市集的 New Relic 頁面]＞。
+ 如需有關使用 New Relic 的詳細資訊，請參閱 New Relic 文件中的[應用程式概觀] (英文)。 

<!-- Anchors. -->
[使用 Azure 市集註冊 New Relic]: #sign-up
[安裝 New Relic 模組]: #install-module
[啟用行動服務的 New Relic 開發人員分析]: #enable-service
[在 New Relic 儀表板中監視行動服務]: #monitor
[Next steps]: #next-steps

<!-- Images. -->
[0]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-tab.png
[1]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-new-relic-monitoring.png
[2]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-new-relic-monitoring-custom.png
[3]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-new-relic-monitoring-complete.png
[4]: ./media/store-new-relic-mobile-services-monitor/mobile-new-relic-dashboard.png
[5]: ./media/store-new-relic-mobile-services-monitor/mobile-new-relic-dashboard-2.png

<!-- URLs. -->
[Source control]: http://msdn.microsoft.com/library/windowsazure/c25aaede-c1f0-4004-8b78-113708761643
[Work with server scripts in Mobile Services]: ../work-with-server-scripts.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Node.js API Documentation: Modules]: http://nodejs.org/api/modules.html
[在原始檔控制中儲存伺服器指令碼]: /develop/mobile/tutorials/store-scripts-in-source-control/
[newrelic]: https://npmjs.org/package/newrelic
[Azure 市集的 New Relic 頁面]: /gallery/store/new-relic/new-relic/
[應用程式概觀]: https://docs.newrelic.com/docs/applications-dashboards/applications-overview
[開始使用行動服務]: /develop/mobile/tutorials/get-started/
[開始使用資料]: /develop/mobile/tutorials/get-started-with-data-dotnet
[New Relic Mobile]: http://newrelic.com/mobile-monitoring

 

<!---HONumber=July15_HO1-->