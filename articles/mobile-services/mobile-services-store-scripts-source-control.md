<properties 
	pageTitle="在原始檔控制中儲存專案程式碼 - Azure 行動服務" 
	description="了解如何在您電腦的本機 Git 存放庫中儲存伺服器指令碼檔案和模組。" 
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
	ms.date="04/24/2015" 
	ms.author="ggailey777"/>

# 在原始檔控制中儲存專案程式碼

> [AZURE.SELECTOR-LIST (Platform | Backend)]
- [(Any | .NET)](mobile-services-dotnet-backend-store-code-source-control.md)
- [(Any | Javascript)](mobile-services-store-scripts-source-control.md)

本主題說明如何使用 Azure 行動服務所提供的原始檔控制來儲存您的伺服器指令碼。指令碼和其他 JavaScript 程式碼後端檔案可以從本機 Git 儲存機制升級到生產行動服務。它還說明如何定義多個指令碼所需的共用程式碼，以及如何使用 package.json 檔案將 Node.js 模組新增至您的行動服務。

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

6. 瀏覽至您執行 `git clone` 命令的目錄，並注意下列目錄結構：

	![4][4]

	在此案例中，會建立以此行動服務的名稱命名的新目錄，這裡就是資料服務的本機儲存機制。

7. 開啟 .\\service\\table 子資料夾並注意其中包含 TodoItem.json 檔案，這是 TodoItem 資料表上操作權限的 JSON 表示。

	當在此資料表上定義伺服器指令碼時，您也會有一或多個名為 <code>TodoItem._&lt;operation&gt;_.js</code> 的檔案，檔案中包含了指定資料表作業的指令碼。排程器和自訂 API 指令碼會在名為排程器和自訂 API 指令碼的個別資料夾中分別進行維護。如需詳細資訊，請參閱[原始檔控制]。

現在您已建立本機儲存機制，您可以對伺服器指令碼進行變更，並將變更推送回行動服務。

##<a name="deploy-scripts"></a>將更新指令碼檔案部署到行動服務

1. 瀏覽至 .\\service\\table 子資料夾，如果 todoitem.insert.js 檔案不存在，請立即建立此檔案。

2. 在文字編輯器中開啟新檔案 todoitem.insert.js，貼上下列程式碼並儲存變更：

		function insert(item, user, request) {
		    request.execute();
		    console.log(JSON.stringify(item, null, 4));
		}
	
	此程式碼僅會將插入項目寫入記錄。如果此檔案已包含程式碼，則只要在此檔案中新增一些有效的 JavaScript 程式碼 (例如，呼叫 `console.log()`)，然後儲存變更。

3. 在 Git 命令提示字元中，輸入下列命令以開始追蹤新的指令碼檔案：

		$ git add .
	

4. 輸入下列命令以認可變更：

		$ git commit -m "updated the insert script"

5. 輸入下列命令以將變更上傳至遠端儲存機制：

		$ git push origin master
	
	您應會看到一連串命令，指出已將認可部署到行動服務。

6. 回到管理入口網站中，按一下 [**資料**] 索引標籤，然後按一下 [**TodoItem**] 資料表、[**指令碼**]，再選取 [**插入**] 作業。
7. 
	請注意，顯示的插入作業指令碼會與您剛才上傳至儲存機制的 JavaScript 程式碼相同。

##<a name="use-npm"></a>在伺服器指令碼中運用共用程式碼和 Node.js 模組

行動服務提供整組核心 Node.js 模組的存取權，透過 **require** 函數，您可以在您的程式碼中使用這些模組。行動服務也可以使用不是核心 Node.js 封裝一部分的 Node.js 模組，您甚至可以將自己的共用程式碼定義為 Node.js 模組。如需關於建立模組的詳細資訊，請參閱 Node.js API 參考文件中的[模組] (英文)。

建議的方法是藉由將參考新增至服務的 package.json 檔案，以將 Node.js 模組新增至您的行動服務。接著，您必須更新 package.json 檔案，以將 [node-uuid] Node.js 模組新增至您的行動服務。當更新發行至 Azure 時，將會重新啟動行動服務，並安裝模組。接著便可使用此模組，來為插入項目上的 **uuid** 屬性產生新的 GUID 值。

2. 導覽至本機 Git 儲存機制的 `.\service` 資料夾在文字編輯器中開啟 package.json 檔案，然後將下列欄位新增至 **dependencies** 物件：

		"node-uuid": "~1.4.3"

	>[AZURE.NOTE]此項 package.json 檔案更新會導致在推送認可後重新啟動您的行動服務。

4. 現在，請瀏覽到 .\\service\\table 子資料夾，開啟並修改 todoitem.insert.js 檔案，如下所示：

		function insert(item, user, request) {
		    var uuid = require('node-uuid');
		    item.uuid = uuid.v1();
		    request.execute();
		    console.log(item);
		}

	此程式碼會在資料表中新增 uuid 資料行，並填入唯一的 GUID 識別碼。

5. 如上一節所示，請在 Git 命令提示字元中輸入下列命令：

		$ git add .
		$ git commit -m "added node-uuid module"
		$ git push origin master
		
	這會新增檔案、認可變更，並將新的 node-uuid 模組和變更推送到行動服務的 todoitem.insert.js 指令碼。

## <a name="next-steps"> </a>後續步驟

現在，您已完成本教學課程，您知道如何在原始檔控制中儲存指令碼。請考慮了解更多有關使用伺服器指令碼和自訂 API 的詳細資料：

+ [在行動服務中使用伺服器指令碼] <br/>說明如何使用伺服器指令碼、工作排程器和自訂 API。

+ [從用戶端呼叫自訂 API] <br/>說明如何建立可從用戶端呼叫的自訂 API。

<!-- Anchors. -->
[Enable source control in your mobile service]: #enable-source-control
[Install Git and create the local repository]: #clone-repo
[Deploy updated script files to your mobile service]: #deploy-scripts
[Leverage shared code and Node.js modules in your server scripts]: #use-npm

<!-- Images. -->
[4]: ./media/mobile-services-store-scripts-source-control/mobile-source-local-repo.png
[5]: ./media/mobile-services-store-scripts-source-control/mobile-portal-data-tables.png
[6]: ./media/mobile-services-store-scripts-source-control/mobile-insert-script-source-control.png

<!-- URLs. -->
[Git website]: http://git-scm.com
[原始檔控制]: http://msdn.microsoft.com/library/windowsazure/c25aaede-c1f0-4004-8b78-113708761643
[安裝 Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[開始使用行動服務]: mobile-services-ios-get-started.md
[將行動服務新增至現有的應用程式]: mobile-services-ios-get-started-data.md
[在行動服務中使用伺服器指令碼]: mobile-services-how-to-use-server-scripts.md
[Azure Management Portal]: https://manage.windowsazure.com/
[從用戶端呼叫自訂 API]: mobile-services-ios-call-custom-api.md
[模組]: http://nodejs.org/api/modules.html
[node-uuid]: https://npmjs.org/package/node-uuid
 

<!---HONumber=July15_HO2-->