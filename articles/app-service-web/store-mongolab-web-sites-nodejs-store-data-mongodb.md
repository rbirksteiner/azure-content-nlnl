<properties
	pageTitle="使用 MongoLab 附加元件在有 MongoDB 的 Azure 上建立 Node.js Web 應用程式"
	description="了解如何在 Azure App Service 中建立 Node.js Web 應用程式，連線到 MongoLab 上主控的 MongoDB 執行個體。"
	tags="azure-classic-portal"
	services="app-service\web, virtual-machines"
	documentationCenter="nodejs"
	authors="chrisckchang"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
	ms.date="04/20/2014"
	ms.author="mwasson"/>






# 使用 MongoLab 附加元件在有 MongoDB 的 Azure 上建立 Node.js Web 應用程式


<p><em>作者：MongoLab 的 Eric Sedor</em></p>

探險家們大家好！ 歡迎使用「MongoDB 即服務」。在本教學課程中，您將：

1. [佈建資料庫][provision] - Azure Marketplace 中的 [MongoLab](http://mongolab.com) (英文) 附加元件將提供您在 Azure 雲端主控並由 MongoLab 的雲端資料庫平台管理的 MangoDB 資料庫。
2. [建立應用程式][create] - 簡單的 Node.js 應用程式，用於維護工作清單。
3. [部署應用程式][deploy] - 只要將幾個組態勾點連結在一起，將程式碼推播到 [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) 就可以變得輕鬆無比。
4. [管理資料庫][manage] - 最後，我們將展示 MongoLab 的網頁式資料庫管理入口網站，您可在此輕鬆執行資料的搜尋、虛擬化、修改。

進行本教學課程的過程中若有任何問題，可隨時發送電子郵件至 [support@mongolab.com](mailto:support@mongolab.com)。

請確定您已安裝下列各項再繼續：

* [Node.js] 0.10.29 以上版本

* [Git]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## 快速入門
如果您已對 Azure 市集有一定的了解，請從本節著手快速開始。否則，請繼續前往到底下的[佈建資料庫][provision]。

1. 按一下 [新增] > [Markeplace] 來開啟 Azure Marketplace。  
	<!--- ![Store][button-store] -->
2. 按一下 **MongoLab** 附加元件。  
	![MongoLab][entry-mongolab]
3. 在 [附加元件] 清單中按一下您的 **MongoLab** 附加元件，然後按一下 [連線資訊]。  
	![ConnectionInfoButton][button-connectioninfo]  
4. 將 **MONGOLAB_URI** 複製到剪貼簿。  
	![ConnectionInfoScreen][screen-connectioninfo]

	>[AZURE.NOTE]此 URI 包含您的資料庫使用者名稱和密碼。這是機密資訊，請勿洩露。

5. 在 Azure App Service 中 Web 應用程式的 [組態] 功能表中，將這個值新增到 [連接字串] 清單：![WebAppConnectionStrings][focus-website-connectinfo]
6. 在 [名稱] 中，輸入 **MONGOLAB_URI**。
7. 在 [值] 中，貼上我們在上一節取得的連接字串。
8. 在 [類型] 下拉式清單中選取 [自訂] (取代預設的 [SQLAzure])。
9. 執行 `npm install mongoose` 以取得 Mongoose (MongoDB 節點驅動程式)。
10. 在程式碼中設定勾點，以從環境變數取得 MongoLab 連線 URI 並進行連接：

        var mongoose = require('mongoose');  
 		...
 		var connectionString = process.env.CUSTOMCONNSTR_MONGOLAB_URI
 		...
 		mongoose.connect(connectionString);

注意：Azure 會將前置詞 **CUSTOMCONNSTR_** 新增到原始宣告的連接字串前面，所以程式碼會參考 **CUSTOMCONNSTR_MONGOLAB_URI** 而非 **MONGOLAB_URI**。

現在，回到教學課程...

<a name="provision"></a>
## 佈建資料庫

[AZURE.INCLUDE [howto-provision-mongolab](../../includes/howto-provision-mongolab.md)]

<a name="create"></a>
## 建立應用程式

在本節中，您將設定開發環境，使用 Node.js、Express、MongoDB 設置用於基本工作清單 Web 應用程式的程式碼。[Express] (英文) 提供節點的檢視控制器架構，[Mongoose] (英文) 則是用來與節點中的 MongoDB 通訊的驅動程式。

### 設定

#### 產生樣板及安裝模組

1. 在命令列，建立 **tasklist** 目錄並瀏覽至此。這將是您的專案目錄。
2. 輸入以下命令以安裝 express。

		npm install express -g

	`-g` 表示全域模式，用於在未指定目錄路徑的情況下使 <strong>express</strong> 模組可供使用。如果您收到<strong>錯誤：EPERM, chmod '/usr/local/bin/express'</strong>，請使用 <strong>sudo</strong> 在更高的權限層級執行 npm。

    此命令的輸出應類似這樣：

		express@4.9.1 C:\Users\mongolab\AppData\Roaming\npm\node_modules\express
		├── merge-descriptors@0.0.2
		├── utils-merge@1.0.0
		├── fresh@0.2.4
		├── cookie@0.1.2
		├── range-parser@1.0.2
		├── escape-html@1.0.1
		├── cookie-signature@1.0.5
		├── finalhandler@0.2.0
		├── vary@1.0.0
		├── media-typer@0.3.0
		├── parseurl@1.3.0
		├── serve-static@1.6.2
		├── methods@1.1.0
		├── path-to-regexp@0.1.3
		├── depd@0.4.5
		├── qs@2.2.3
		├── on-finished@2.1.0 (ee-first@1.0.5)
		├── debug@2.0.0 (ms@0.6.2)
		├── proxy-addr@1.0.1 (ipaddr.js@0.1.2)
		├── etag@1.3.1 (crc@3.0.0)
		├── send@0.9.2 (destroy@1.0.3, ms@0.6.2, mime@1.2.11)
		├── accepts@1.1.0 (negotiator@0.4.7, mime-types@2.0.1)
		└── type-is@1.5.1 (mime-types@2.0.1)

3. 若要建立用於此應用程式的樣板，使用 **express** 命令：

    	express

    請注意，本教學課程使用 Express v4.x.x。如果您的系統上已安裝 Express 3 應用程式產生器，您應該先將它解除安裝：

    	npm uninstall -g express

    現在，請為 4.x.x 版安裝新的產生器：

    	npm install -g express-generator

	在執行 **express** 命令後，應會出現如下的輸出：

		create : .
		create : ./package.json
		create : ./app.js
		create : ./public
		create : ./public/javascripts
		create : ./public/images
		create : ./public/stylesheets
		create : ./public/stylesheets/style.css
		create : ./routes
		create : ./routes/index.js
		create : ./routes/users.js
		create : ./views
		create : ./views/index.jade
		create : ./views/layout.jade
		create : ./views/error.jade
		create : ./bin
		create : ./bin/www

		install dependencies:
		$ cd . && npm install


	此命令完成後，在 **tasklist** 目錄中應有數個新的目錄和檔案。

4. 輸入下面這一行安裝 **package.json** 檔案中所指出的模組：

        npm install

    此命令的輸出應類似這樣：

		cookie-parser@1.3.3 node_modules/cookie-parser
		├── cookie@0.1.2
		└── cookie-signature@1.0.5

		debug@2.0.0 node_modules/debug
		└── ms@0.6.2

		serve-favicon@2.1.4 node_modules/serve-favicon
		├── ms@0.6.2
		├── fresh@0.2.4
		└── etag@1.3.1 (crc@3.0.0)

		morgan@1.3.1 node_modules/morgan
		├── basic-auth@1.0.0
		├── depd@0.4.5
		└── on-finished@2.1.0 (ee-first@1.0.5)

		express@4.9.1 node_modules/express
		├── utils-merge@1.0.0
		├── merge-descriptors@0.0.2
		├── cookie@0.1.2
		├── fresh@0.2.4
		├── escape-html@1.0.1
		├── range-parser@1.0.2
		├── cookie-signature@1.0.5
		├── finalhandler@0.2.0
		├── vary@1.0.0
		├── media-typer@0.3.0
		├── serve-static@1.6.2
		├── parseurl@1.3.0
		├── methods@1.1.0
		├── path-to-regexp@0.1.3
		├── depd@0.4.5
		├── qs@2.2.3
		├── etag@1.3.1 (crc@3.0.0)
		├── on-finished@2.1.0 (ee-first@1.0.5)
		├── proxy-addr@1.0.1 (ipaddr.js@0.1.2)
		├── send@0.9.2 (destroy@1.0.3, ms@0.6.2, mime@1.2.11)
		├── type-is@1.5.1 (mime-types@2.0.1)
		└── accepts@1.1.0 (negotiator@0.4.7, mime-types@2.0.1)

		body-parser@1.8.2 node_modules/body-parser
		├── media-typer@0.3.0
		├── raw-body@1.3.0
		├── bytes@1.0.0
		├── depd@0.4.5
		├── on-finished@2.1.0 (ee-first@1.0.5)
		├── qs@2.2.3
		├── iconv-lite@0.4.4
		└── type-is@1.5.1 (mime-types@2.0.1)

		jade@1.6.0 node_modules/jade
		├── character-parser@1.2.0
		├── commander@2.1.0
		├── void-elements@1.0.0
		├── mkdirp@0.5.0 (minimist@0.0.8)
		├── monocle@1.1.51 (readdirp@0.2.5)
		├── transformers@2.1.0 (promise@2.0.0, css@1.0.8, uglify-js@2.2.5)
		├── constantinople@2.0.1 (uglify-js@2.4.15)
		└── with@3.0.1 (uglify-js@2.4.15)

	**package.json** 檔案是 **express** 命令建立的檔案之一。這個檔案包含 Express 應用程式的其他必要模組清單。之後，當您將此應用程式部署至 Azure App Service Web Apps，此檔案將用於決定 Azure 上需要安裝哪些模組來支援您的應用程式。

5. 接下來，輸入以下命令在本機安裝 Mongoose 模組並將其所用項目儲存至 **package.json** 檔案：

		npm install mongoose --save

	此命令的輸出應類似這樣：

		mongoose@3.8.16 node_modules/mongoose
		├── regexp-clone@0.0.1
		├── muri@0.3.1
		├── sliced@0.0.5
		├── hooks@0.2.1
		├── mpath@0.1.1
		├── mpromise@0.4.3
		├── ms@0.1.0
		├── mquery@0.8.0 (debug@0.7.4)
		└── mongodb@1.4.9 (readable-stream@1.0.31, kerberos@0.0.3, bson@0.2.12)

### 程式碼

現在，環境和樣板已準備好了，我們要藉著加入 **task.js** 檔案的方式擴充由 **express** 命令建立的基本應用程式 (task.js 檔案包含工作的模型)。您也將修改現有的 **app.js** 及建立新的 **tasklist.js** 控制器檔案，以使用模型。

#### 建立模型

1. 在 **tasklist** 目錄中，建立新目錄 **models**。

2. 在 **models** 目錄中，建立新檔案 **task.js**。此檔案將包含您的應用程式建立之工作的模型。

3. 在 **task.js** 檔案中加入以下程式碼：

        var mongoose = require('mongoose'),
          Schema = mongoose.Schema;

        var TaskSchema = new Schema({
	      itemName      : String,
	      itemCategory  : String,
	      itemCompleted : { type: Boolean, default: false },
	      itemDate      : { type: Date, default: Date.now }
        });

        module.exports = mongoose.model('TaskModel', TaskSchema);

5. 儲存並關閉 **task.js** 檔案。

#### 建立控制器

1. 在 **tasklist/routes** 目錄中建立新檔案 **tasklist.js**，然後在文字編輯器中開啟檔案。

2. 在 **tasklist.js** 中加入以下程式碼。這會載入 mongoose 模組和在 **task.js** 中定義的工作模型。TaskList 函數會根據 **connection** 值與 MongoDB 伺服器建立連線，並提供 **showTasks**、**addTask**、**completeTasks** 方法：

		var mongoose = require('mongoose'),
 		  task = require('../models/task.js');

		module.exports = TaskList;

		function TaskList(connection) {
		  mongoose.connect(connection);
		}

		TaskList.prototype = {
		  showTasks: function(req, res) {
		    task.find({ itemCompleted : false }, function foundTasks(err, items) {
		    res.render('index', { title: 'My ToDo List', tasks: items })
		    });
		  },

		  addTask: function(req,res) {
		    var item = req.body;
		    var newTask = new task();
		    newTask.itemName = item.itemName;
		    newTask.itemCategory = item.itemCategory;
		    newTask.save(function savedTask(err) {
		      if(err) {
		        throw err;
		      }
		    });
		    res.redirect('/');
		  },


		  completeTask: function(req,res) {
		    var completedTasks = req.body;
		    for(taskId in completedTasks) {
		      if(completedTasks[taskId]=='true') {
		        var conditions = { _id: taskId };
		        var updates = { itemCompleted: completedTasks[taskId] };
		        task.update(conditions, updates, function updatedTask(err) {
		          if(err) {
		            throw err;
		          }
		        });
		      }
		    }
		    res.redirect('/');
		  }
		}

3. 儲存 **tasklist.js** 檔案。

#### 修改索引檢視

1. 將目錄變更為 **views** 目錄，在文字編輯器中開啟 **index.jade** 檔案。

2. 以下列程式碼取代 **index.jade** 檔案的內容。這會定義用於顯示現有工作的檢視，以及用於加入新工作及將現有工作標示為完成的表單。

		h1 #{title}
		form(action="/completetask", method="post")
		  table(border="1")
		    tr
		      td Name
		      td Category
		      td Date
		      td Complete
		    each task in tasks
		      tr
		        td #{task.itemName}
		        td #{task.itemCategory}
		        - var day   = task.itemDate.getDate();
		        - var month = task.itemDate.getMonth() + 1;
		        - var year  = task.itemDate.getFullYear();
		        td #{month + "/" + day + "/" + year}
		        td
		          input(type="checkbox", name="#{task._id}", value="#{!task.itemCompleted}", checked=task.itemCompleted)
		  input(type="submit", value="Update tasks")
		hr
		form(action="/addtask", method="post")
		  table(border="1")
		    tr
		      td Item Name:
		      td
		        input(name="itemName", type="textbox")
		    tr
		      td Item Category:
		      td
		        input(name="itemCategory", type="textbox")
		  input(type="submit", value="Add item")

3. 儲存並關閉 **index.jade** 檔案。

#### 取代 app.js

1. 在 **tasklist** 目錄中，在文字編輯器中開啟 **app.js** 檔案。此檔案是稍早執行 **express** 命令所建立。
2. 在 **app.js** 檔案的開頭加入以下程式碼。這將以 MongoDB 伺服器的連接字串將 **TaskList** 初始化：

		var TaskList = require('./routes/tasklist');
		var taskList = new TaskList(process.env.CUSTOMCONNSTR_MONGOLAB_URI);

 	請注意第二行，您會存取稍後將設定的環境變數，此環境變數包含您的 mongo 執行個體的連線資訊。若您有為開發目的而執行的本機 mongo 執行個體，請暫時將此值設為 "localhost" 而非 `process.env.CUSTOMCONNSTR_MONGOLAB_URI`。

3. 找出以下幾行：

		app.use('/', routes);
		app.use('/users', users);

	將其取代為：

		app.get('/', taskList.showTasks.bind(taskList));
		app.post('/addtask', taskList.addTask.bind(taskList));
		app.post('/completetask', taskList.completeTask.bind(taskList));

	這會加入 **tasklist.js** 中定義的函數做為路由。

4. 若要初始化您的應用程式，請移至 **app.js** 檔案底部，並新增以下一行：

		app.listen(3000); // Listen on port 3000
		module.exports = app;

5. 儲存 **app.js** 檔案。

<a name="deploy"></a>
## 部署應用程式。

現在應用程式已經開發好，該開始以 Azure App Service 建立用以主控它的 Web 應用程式、設定該 Web 應用程式及部署程式碼。本節的重點是 MongoDB 連接字串 (URI) 的使用。您即將在 We 應用程式中以此 URI 設定環境變數，使 URI 和您的程式碼分開。您應該將 URI 當做敏感資訊，因為它包含連線至您的資料庫的認證。

本節的步驟使用適用於 Mac、Linux 及 Windows 的 Azure 命令列介面，在 Azure App Service 中建立新的 Web 應用程式，然後使用 Git 部署應用程式。若要執行這些步驟，必須有 Azure 定用帳戶。

### 安裝 Azure CLI

若要安裝 Azure CLI，使用下列命令：

	npm install azure-cli -g

若您已從 <a href="/develop/nodejs/">Azure 開發人員中心</a>安裝 <strong>Azure SDK for Node.js</strong>，則應該已安裝 Azure CLI。如需詳細資訊，請參閱 <a href="../virtual-machines-command-line-tools.md">Azure CLI</a>。

雖然 Azure CLI 工具主要是為了 Mac 和 Linux 使用者而建立，但這些工具會以 Node.js 為基礎，且應該可在任何可執行 Node 的系統上使用。

### 匯入發佈設定

在使用 Azure CLI 之前，必須先下載包含訂用帳戶資訊的檔案。請執行以下步驟來下載及匯入此檔案。

1. 在命令列中，輸入以下命令以啟動瀏覽器，並瀏覽至下載頁面。若出現提示，請用與訂用帳戶相關聯的帳戶登入。

		azure account download

	![下載頁面][download-publishing-settings]

	檔案應該會自動開始下載，如果沒有，您可以按一下頁面開頭的連結手動下載檔案。

2. 檔案下載完畢後，使用以下命令匯入設定：

		azure account import <path-to-file>

	指定前一個步驟下載之發佈設定檔案的路徑和檔案名稱。一旦命令完成，您應該會看到類似這樣的輸出：

		info:   Executing command account import
		info:   Found subscription: subscriptionname
		info:   Setting default subscription to: subscriptionname
		warn:   The '/Users/mongolab/.azure/publishSettings.xml' file contains sensitive information.
		warn:   Remember to delete it now that it has been imported.
		info:   Account publish settings imported successfully
		info:   account import command OK


3. 匯入完成後，您應該刪除發佈設定檔案，因為您不再需要它，而它卻含有關於 Azure 訂閱的敏感資訊。

### 建立新 Web 應用程式並推播程式碼

在 Azure App Service 中建立 Web 應用程式非常簡單。如果這是您的第一個 Azure Web 應用程式，您必須使用入口網站。如果您已有至少一個網站，請直接跳至步驟 7。

1. 在 Azure 入口網站，按一下 [新增]。  
	![新增][button-new]
2. 選取 [運算] > [Web 應用程式] > [快速建立]。  
	<!--- ![Create Web App][screen-mongolab-newwebsite] -->
3. 輸入 URL 前置詞。選擇您喜愛的名稱，但記住一定要獨一無二 (很可能無法使用 'mymongoapp')。
4. 按一下 [建立 Web 應用程式]。
5. 當 Web 應用程式建立完成，按一下 [Web 應用程式] 清單中的 Web 應用程式名稱。Web 應用程式儀表板隨即顯示。  
	<!--- ![Web App Dashboard][screen-mongolab-websitedashboard] -->
6. 按一下 [快速概覽] 下的 [設定從原始檔控制進行部署]，選取 GitHub，並輸入您要使用的 git 使用者名稱和密碼。要發佈至您的 Web 應用程式時將使用這個密碼 (步驟 9)。  
7. 如果您以上述步驟建立 Web 應用程式，以下命令將會完成此程序。不過，若您已有多個 Web 應用程式，可以跳過以上步驟，同樣使用此命令建立新的 Web 應用程式。在您的 **tasklist** 專案目錄中：

		azure site create myuniquewebappname --git  
	以您 Web 應用程式獨有的名稱取代 'myuniquewebappname'。如果執行此命令時會建立 Web 應用程式，系統將提示您輸入即將放置 Web 應用程式的資料中心。選取地理位置與您的 MongoLab 資料庫最近的資料中心。

	`--git` 參數將建立：* 本機 git 儲存機制，位置在 **tasklist** 資料夾中 (若其中還沒有此項目)。* 名為 'azure' 的 [Git 遠端]，這會用於將應用程式發佈至 Azure。* [iisnode.yml] 檔案，內含 Azure 用於主控節點應用程式的設定。* .gitignore 檔案，用以防止 node-modules 資料夾發佈至 .git。

	一旦此命令完成，您將會看到類似以下的輸出。請注意，以 **Created site at** 開頭的程式行包含 Web 應用程式的 URL。

		info:   Executing command site create
		info:   Using location southcentraluswebspace
		info:   Executing `git init`
		info:   Creating default web.config file
		info:   Creating a new web site
		info:   Created web site at  mongodbtasklist.azurewebsites.net
		info:   Initializing repository
		info:   Repository initialized
		info:   Executing `git remote add azure http://gitusername@myuniquewebappname.azurewebsites.net/mongodbtasklist.git`
		info:   site create command OK

8. 使用以下命令將檔案加入本機 Git 儲存機制並予以認可：

		git add .
		git commit -m "adding files"

9. 推播您的程式碼：

		git push azure master  

	將最新的 Git 儲存機制變更發佈至 Azure App Service 中的 Web 應用程式時，必須指定目標分支為 **master**，因為它將用於 Web 應用程式內容。若看到輸入密碼的提示，請輸入先前步驟中為 Web 應用程式設定 git 發佈時所建立的密碼。

	您將看到類似以下的輸出：部署開始時，Azure 會下載所有 npm 模組。

		Counting objects: 17, done.
		Delta compression using up to 8 threads.
		Compressing objects: 100% (13/13), done.
		Writing objects: 100% (17/17), 3.21 KiB, done.
		Total 17 (delta 0), reused 0 (delta 0)
		remote: New deployment received.
		remote: Updating branch 'master'.
		remote: Preparing deployment for commit id 'ef276f3042'.
		remote: Preparing files for deployment.
		remote: Running NPM.
		...
		remote: Deploying Web.config to enable Node.js activation.
		remote: Deployment successful.
		To https://username@mongodbtasklist.azurewebsites.net/MongoDBTasklist.git
 		 * [new branch]      master -> master

就快要完成了！

### 設定環境
還記得程式碼中的 process.env.CUSTOMCONNSTR_MONGOLAB_URI 嗎？ 我們要替這個環境變數填入您佈建 MongoLab 資料庫時提供給 Azure 的值。

#### 取得 MongoLab 連線字串

[AZURE.INCLUDE [howto-get-connectioninfo-mongolab](../../includes/howto-get-connectioninfo-mongolab.md)]

#### 將連接字串加入 Web 應用程式的環境變數

[AZURE.INCLUDE [howto-save-connectioninfo-mongolab](../../includes/howto-save-connectioninfo-mongolab.md)]

## 成功！

從您的專案目錄執行 `azure site browse` 可自動開啟瀏覽器，或手動開啟瀏覽器並瀏覽至您的 Web 應用程式 URL (myuniquewebappname.azurewebsites.net)：

![顯示空白工作清單的網頁][node-mongo-finished]

<a name="manage"></a>
## 管理資料庫

[AZURE.INCLUDE [howto-access-mongolab-ui](../../includes/howto-access-mongolab-ui.md)]

恭喜！ 您剛啟動 Node.js 應用程式，並有由 MongoLab 主控之 MongoDB 資料庫的支援！ 現在您已經有了 MongoLab 資料庫，對於資料庫的任何問題或疑惑，或是需要有關 MongoDB 或節點驅動程式本身的協助，您都可以連絡 [support@mongolab.com](mailto:support@mongolab.com)。祝您好運！

## 變更的項目
* 如需從網站變更為 App Service 的指南，請參閱：[Azure App Service 及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 如需從舊的入口網站變更為新入口網站的指南，請參閱：[巡覽預覽入口網站的參考](http://go.microsoft.com/fwlink/?LinkId=529715)

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期入門 Web 應用程式。不需要信用卡；沒有承諾。


[screen-mongolab-websitedashboard]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/screen-mongolab-websitedashboard.png
[screen-mongolab-newwebsite]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/screen-mongolab-newwebsite.png
[button-new]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-new.png
[button-store]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-store.png
[entry-mongolab]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/entry-mongolab.png
[button-connectioninfo]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-connectioninfo.png
[screen-connectioninfo]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/dialog-mongolab_connectioninfo.png
[focus-website-connectinfo]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/focus-mongolab-websiteconnectionstring.png
[provision]: #provision
[create]: #create
[deploy]: #deploy
[manage]: #manage
[Node.js]: http://nodejs.org
[MongoDB]: http://www.mongodb.org
[Git]: http://git-scm.com
[Express]: http://expressjs.com
[Mongoose]: http://mongoosejs.com
[for free]: /pricing/free-trial
[Git 遠端]: http://git-scm.com/docs/git-remote
[azure-sdk-for-node]: https://github.com/WindowsAzure/azure-sdk-for-node
[iisnode.yml]: https://github.com/WindowsAzure/iisnode/blob/master/src/samples/configuration/iisnode.yml
[Azure CLI]: ../virtual-machines-command-line-tools.md
[Azure Developer Center]: /develop/nodejs/
[Create and deploy a Node.js application to Azure Web Sites]: /develop/nodejs/tutorials/create-a-website-(mac)/
[Continuous deployment using GIT in Azure App Service]: web-sites-publish-source-control.md
[MongoLab]: http://mongolab.com
[Node.js Web Application with Storage on MongoDB (Virtual Machine)]: /develop/nodejs/tutorials/website-with-mongodb-(mac)/
[node-mongo-finished]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/todo_list_noframe.png
[node-mongo-express-results]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/express_output.png
[download-publishing-settings]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/azure-account-download-cli.png
[import-publishing-settings]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/azureimport.png
[mongolab-create]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/mongolab-create.png
[mongolab-view]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/mongolab-view.png
 

<!-----HONumber=62-->