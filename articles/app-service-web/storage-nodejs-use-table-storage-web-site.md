<properties
	pageTitle="使用 Azure 表格服務的 Node.js Web 應用程式"
	description="本教學課程說明如何使用 Azure 表格服務，儲存 Azure App Service Web 應用程式代管的 Node.js 應用程式資料。"
	tags="azure-portal"
	services="app-service\web, storage"
	documentationCenter="nodejs"
	authors="MikeWasson"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
	ms.date="04/28/2015"
	ms.author="mwasson"/>



# 使用 Azure 表格服務的 Node.js Web 應用程式

## 概觀

本教學課程說明如何使用 Azure 資料管理所提供的表格服務，儲存及存取 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web 應用程式代管的 [node] 應用程式資料。本教學課程假設您先前有過一些使用節點及 [Git] 的經驗。

您將了解：

* 如何使用 npm (節點封裝管理員) 來安裝節點模組

* 如何使用 Azure 表格服務

* 如何使用 Azure CLI 建立 Web 應用程式。

依照本教學課程的指示，您將組建一個簡單的網頁型「待辦事項清單」應用程式，用於建立、擷取、完成工作。工作存放於資料表服務中。

以下是完成的應用程式：

![顯示空白工作清單的網頁][node-table-finished]

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期入門 Web 應用程式。不需要信用卡；沒有承諾。


## 必要條件

在依照本文中的指示進行之前，您應確定已安裝下列項目：

* [node] 版本 0.10.24 或更高版本

* [Git]


[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## 建立儲存體帳戶

建立 Azure 儲存體帳戶。應用程式會使用此帳戶來儲存待辦事項。

1.  登入 [Azure 入口網站](https://portal.azure.com)。

2. 按一下入口網站左下方的 [新增] 圖示，然後按一下 [資料 + 儲存體] >[ 儲存體]。為儲存體帳戶指定唯一名稱，並為它建立新的[資源群組](../resource-group-overview.md)。

  	![新增按鈕](./media/storage-nodejs-use-table-storage-web-site/configure-storage.png)

	建立儲存體帳戶後，[通知] 按鈕便會閃爍綠色 [成功]，儲存體帳戶的刀鋒視窗會開啟，顯示它屬於您所建立的新資源群組。

5. 在儲存體帳戶的刀鋒視窗中，按一下 [設定] > [金鑰]。將主要存取金鑰複製到剪貼簿。

    ![存取金鑰][portal-storage-access-keys]


##安裝模組及產生樣板

在本節中，您將建立新的 Node 應用程式，並使用 npm 來新增模組封裝。針對此應用程式，您將使用 [Express] 和 [Azure] 模組。Express 模組提供節點的模型檢視控制器架構，Azure 模組則提供資料表服務的連線。

### 安裝 Express 及產生樣板

1. 從命令列中，建立名為 **tasklist** 的新目錄，並切換至該目錄。  

2. 輸入下列命令以安裝 Express 模組。

		npm install express-generator@4.2.0 -g

    視作業系統而定，您可能需要在命令之前加上 'sudo'：

		sudo npm install express-generator@4.2.0 -g

    輸出應該如下所示：

		express-generator@4.2.0 /usr/local/lib/node_modules/express-generator
		├── mkdirp@0.3.5
		└── commander@1.3.2 (keypress@0.1.0)

	> [AZURE.NOTE]'-g' 參數會全域安裝模組。這樣我們就可以使用 **express** 來產生 Web 應用程式樣板，而不需額外輸入路徑資訊。

4. 若要建立應用程式的樣板，請輸入 **express** 命令：

        express

	此命令的輸出應類似這樣：

		   create : .
		   create : ./package.json
		   create : ./app.js
		   create : ./public
		   create : ./public/images
		   create : ./routes
		   create : ./routes/index.js
		   create : ./routes/users.js
		   create : ./public/stylesheets
		   create : ./public/stylesheets/style.css
		   create : ./views
		   create : ./views/index.jade
		   create : ./views/layout.jade
		   create : ./views/error.jade
		   create : ./public/javascripts
		   create : ./bin
		   create : ./bin/www

		   install dependencies:
		     $ cd . && npm install

		   run the app:
		     $ DEBUG=my-application ./bin/www

	在 **tasklist** 目錄中應有數個新的目錄和檔案。

### 安裝其他模組

**express** 建立的其中一個檔案是 **package.json**。此檔案包含模組相依性清單。之後，當您將應用程式部署至 App Service Web Apps 時，此檔案將決定 Azure 上需要安裝哪些模組。

從命令列中，輸入下列命令安裝 **package.json** 檔案中所指出的模組。您可能需要使用 'sudo'。

    npm install

此命令的輸出應類似這樣：

	debug@0.7.4 node_modules\debug

	cookie-parser@1.0.1 node_modules\cookie-parser
	├── cookie-signature@1.0.3
	└── cookie@0.1.0

    [...]


接著，輸入下列命令安裝 [azure]、[node-uuid]、[nconf] 和 [async] 模組：

	npm install azure-storage node-uuid async nconf --save

**--save** 旗標會將這些模組的項目新增至 **package.json** 檔案。

此命令的輸出應類似這樣：

	async@0.9.0 node_modules\async

	node-uuid@1.4.1 node_modules\node-uuid

	nconf@0.6.9 node_modules\nconf
	├── ini@1.2.1
	├── async@0.2.9
	└── optimist@0.6.0 (wordwrap@0.0.2, minimist@0.0.10)

	[...]


## 建立應用程式

接下來我們要建置應用程式。

### 建立模型

「模型」是代表您應用程式中資料的物件。對於應用程式，唯一的模型是工作物件，代表待辦事項清單中的項目。工作將會有下列欄位：

- PartitionKey
- RowKey
- name (字串)
- category (字串)
- completed (布林值)

**PartitionKey** 和 **RowKey** 由表格服務用來做為資料表索引鍵。如需詳細資訊，請參閱[了解表格服務資料模型](https://msdn.microsoft.com/library/azure/dd179338.aspx)。


1. 在 **tasklist** 目錄中，建立新目錄 **models**。

2. 在 **models** 目錄中，建立新檔案 **task.js**。此檔案將包含您的應用程式建立之工作的模型。

3. 在 **task.js** 檔案的開頭加入以下程式碼以參考所需的程式庫：

        var azure = require('azure-storage');
  		var uuid = require('node-uuid');
		var entityGen = azure.TableUtilities.entityGenerator;

4. 新增下列程式碼以定義和匯出 Task 物件。此物件負責連接至資料表。

  		module.exports = Task;

		function Task(storageClient, tableName, partitionKey) {
		  this.storageClient = storageClient;
		  this.tableName = tableName;
		  this.partitionKey = partitionKey;
		  this.storageClient.createTableIfNotExists(tableName, function tableCreated(error) {
		    if(error) {
		      throw error;
		    }
		  });
		};

5. 新增下列程式碼以定義 Task 物件上的其他方法，允許與資料表中存放的資料互動：

		Task.prototype = {
		  find: function(query, callback) {
		    self = this;
		    self.storageClient.queryEntities(this.tableName, query, null, function entitiesQueried(error, result) {
		      if(error) {
		        callback(error);
		      } else {
		        callback(null, result.entries);
		      }
		    });
		  },

		  addItem: function(item, callback) {
		    self = this;
		    // use entityGenerator to set types
			// NOTE: RowKey must be a string type, even though
            // it contains a GUID in this example.
		    var itemDescriptor = {
		      PartitionKey: entityGen.String(self.partitionKey),
		      RowKey: entityGen.String(uuid()),
		      name: entityGen.String(item.name),
		      category: entityGen.String(item.category),
		      completed: entityGen.Boolean(false)
		    };
		    self.storageClient.insertEntity(self.tableName, itemDescriptor, function entityInserted(error) {
		      if(error){  
		        callback(error);
		      }
		      callback(null);
		    });
		  },

		  updateItem: function(rKey, callback) {
		    self = this;
		    self.storageClient.retrieveEntity(self.tableName, self.partitionKey, rKey, function entityQueried(error, entity) {
		      if(error) {
		        callback(error);
		      }
		      entity.completed._ = true;
		      self.storageClient.updateEntity(self.tableName, entity, function entityUpdated(error) {
		        if(error) {
		          callback(error);
		        }
		        callback(null);
		      });
		    });
		  }
		}

6. 儲存並關閉 **task.js** 檔案。

### 建立控制器

「控制器」處理 HTTP 要求並呈現 HTML 回應。

1. 在 **tasklist/routes** 目錄中建立新檔案 **tasklist.js**，然後在文字編輯器中開啟檔案。

2. 在 **tasklist.js** 中加入以下程式碼。這會載入 azure 和 async 模組，它們是由 **tasklist.js** 所使用。這也會定義 **TaskList** 函數，系統會傳遞我們稍早定義的 **Task** 物件執行個體給它：

		var azure = require('azure-storage');
		var async = require('async');

		module.exports = TaskList;

3. 定義 **TaskList** 物件。

		function TaskList(task) {
		  this.task = task;
		}


4. 將下列方法新增至 **TaskList**：

		TaskList.prototype = {
		  showTasks: function(req, res) {
		    self = this;
		    var query = new azure.TableQuery()
		      .where('completed eq ?', false);
		    self.task.find(query, function itemsFound(error, items) {
		      res.render('index',{title: 'My ToDo List ', tasks: items});
		    });
		  },

		  addTask: function(req,res) {
		    var self = this
		    var item = req.body.item;
		    self.task.addItem(item, function itemAdded(error) {
		      if(error) {
		        throw error;
		      }
		      res.redirect('/');
		    });
		  },

		  completeTask: function(req,res) {
		    var self = this;
		    var completedTasks = Object.keys(req.body);
		    async.forEach(completedTasks, function taskIterator(completedTask, callback) {
		      self.task.updateItem(completedTask, function itemsUpdated(error) {
		        if(error){
		          callback(error);
		        } else {
		          callback(null);
		        }
		      });
		    }, function goHome(error){
		      if(error) {
		        throw error;
		      } else {
		       res.redirect('/');
		      }
		    });
		  }
		}


### 修改 app.js

1. 在 **tasklist** 目錄中，開啟 **app.js** 檔案。此檔案是稍早執行 **express** 命令所建立。

2. 在檔案開頭，加入下列內容以載入 azure 模組、設定資料表名稱、資料分割索引鍵，以及設定此範例所使用的儲存體認證：

		var azure = require('azure-storage');
		var nconf = require('nconf');
		nconf.env()
		     .file({ file: 'config.json', search: true });
		var tableName = nconf.get("TABLE_NAME");
		var partitionKey = nconf.get("PARTITION_KEY");
		var accountName = nconf.get("STORAGE_NAME");
		var accountKey = nconf.get("STORAGE_KEY");

	> [AZURE.NOTE]nconf 會從環境變數或 **config.json** 檔案 (我們稍後會建立) 載入組態值。

3. 在 app.js 檔中，向下捲動到看見此行處：

		app.use('/', routes);
		app.use('/users', users);

	將以上幾行取代為以下顯示的程式碼。這會初始化 <strong>Task</strong> 的執行個體，並連接到您的儲存體帳戶。這會傳遞給 <strong>TaskList</strong>，它將用來與表格服務通訊：

		var TaskList = require('./routes/tasklist');
		var Task = require('./models/task');
		var task = new Task(azure.createTableService(accountName, accountKey), tableName, partitionKey);
		var taskList = new TaskList(task);

		app.get('/', taskList.showTasks.bind(taskList));
		app.post('/addtask', taskList.addTask.bind(taskList));
		app.post('/completetask', taskList.completeTask.bind(taskList));

4. 儲存 **app.js** 檔案。

### 修改索引檢視

1. 以文字編輯器開啟 **tasklist/views/index.jade** 檔案。

2. 將檔案的整個內容取代為下列程式碼。這會定義顯示現有工作的檢視，並且包括加入新工作及將現有工作標示為完成的表單。

		extends layout

		block content
		  h1= title
		  br

		  form(action="/completetask", method="post")
		    table.table.table-striped.table-bordered
		      tr
		        td Name
		        td Category
		        td Date
		        td Complete
		      if (typeof tasks === "undefined")
		        tr
		          td
		      else
		        each task in tasks
		          tr
		            td #{task.name._}
		            td #{task.category._}
		            - var day   = task.Timestamp._.getDate();
		            - var month = task.Timestamp._.getMonth() + 1;
		            - var year  = task.Timestamp._.getFullYear();
		            td #{month + "/" + day + "/" + year}
		            td
		              input(type="checkbox", name="#{task.RowKey._}", value="#{!task.completed._}", checked=task.completed._)
		    button.btn(type="submit") Update tasks
		  hr
		  form.well(action="/addtask", method="post")
		    label Item Name:
		    input(name="item[name]", type="textbox")
		    label Item Category:
		    input(name="item[category]", type="textbox")
		    br
		    button.btn(type="submit") Add item

3. 儲存並關閉 **index.jade** 檔案。

### 修改全域版面配置

**views** 目錄中的 **layout.jade** 檔案是其他 **.jade** 檔案的全域範本。在此步驟中，您將修改它以使用 [Twitter Bootstrap](https://github.com/twbs/bootstrap)，這個工具組能夠方便設計美觀的 Web 應用程式。

下載並解壓縮 [Twitter Bootstrap](http://getbootstrap.com/) 的檔案。將 **bootstrap.min.css** 檔案從 Bootstrap **css** 資料夾複製到您應用程式的 **public/stylesheets** 目錄。

從 **views** 資料夾，開啟 **layout.jade**，並將全部內容取代為：

	doctype html
	html
	  head
	    title= title
	    link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')
	    link(rel='stylesheet', href='/stylesheets/style.css')
	  body.app
	    nav.navbar.navbar-default
	      div.navbar-header
	      a.navbar-brand(href='/') My Tasks
	    block content

### 建立組態檔

為了在本機執行應用程式，我們會將 Azure 儲存體認證放入組態檔。使用下列 JSON 建立名為 **config.json** 的檔案：

	{
		"STORAGE_NAME": "<storage account name>",
		"STORAGE_KEY": "<storage access key>",
		"PARTITION_KEY": "mytasks",
		"TABLE_NAME": "tasks"
	}

將 **storage account name** 取代為您先前建立的儲存體帳戶名稱，並將 **storage access key** 取代為您儲存體帳戶的主要存取金鑰。例如：

	{
	    "STORAGE_NAME": "nodejsappstorage",
	    "STORAGE_KEY": "KG0oDd..."
	    "PARTITION_KEY": "mytasks",
	    "TABLE_NAME": "tasks"
	}

將此檔案儲存在比 **tasklist** 目錄*高一個目錄層級*的位置，像這樣：

	parent/
	  |-- config.json
	  |-- tasklist/

執行此動作的原因是為了避免將組態檔簽入原始檔控制而成為公用。當我們將應用程式部署至 Azure 時，我們會使用環境變數而不用組態檔。


## 在本機執行應用程式

若要在本機電腦測試應用程式，請執行下列步驟：

1. 使用命令列變更目錄至 **tasklist** 目錄。

2. 使用下列命令在本機啟動應用程式：

        npm start

3. 開啟瀏覽器並瀏覽至 http://127.0.0.1:3000。

這應該會顯示如下所示的網頁：

![顯示空白工作清單的網頁][node-table-finished]

若要建立新的待辦事項，請輸入名稱和類別目錄，然後按一下 [新增項目]。若要將工作標示為完成，請核取 [完成] 並且按一下 [更新工作]。

![An image of the new item in the list of tasks][node-table-list-items]

雖然應用程式是在本機執行，但是它會將資料儲存在 Azure 表格服務中。


## 將應用程式部署至 Azure

本節的步驟使用 Azure 命令列工具在 App Service 中建立新的 Web 應用程式，然後使用 Git 部署應用程式。若要執行這些步驟，必須有 Azure 訂閱。

> [AZURE.NOTE]您也可以使用 [Azure 入口網站](https://portal.azure.com)執行這些步驟。請參閱[在 Azure App Service 中建置和部署 Node.js Web 應用程式]。
>
> 如果這是您建立的第一個 Web 應用程式，您必須使用 Azure 入口網站部署此應用程式。

若要開始，請從命令列輸入下列命令安裝 [Azure 命令列介面]：

	npm install azure-cli -g

### 匯入發佈設定

在此步驟中，您將會下載包含您訂用帳戶相關資訊的檔案。

1. 輸入下列命令：

		azure account download

	此命令會啟動瀏覽器並瀏覽至下載頁面。若出現提示，請使用與您的 Azure 訂用帳戶相關聯的帳戶登入。

	<!-- ![The download page][download-publishing-settings] -->檔案應該會自動開始下載，如果沒有，您可以按一下頁面開頭的連結手動下載檔案。儲存檔案，並記下檔案路徑。

2. 輸入下列命令以匯入設定：

		azure account import <path-to-file>

	指定前一個步驟下載之發佈設定檔案的路徑和檔案名稱。

3. 匯入設定之後，您應該刪除發佈設定檔案。這個檔案已經不再需要，而且包含與 Azure 訂用帳戶相關的機密資訊。

### 建立 App Service Web 應用程式

1. 從命令列將目錄變更至 **tasklist** 目錄。

2. 使用下列命令建立新的 Web 應用程式。

		azure site create --git

	系統會提示您輸入 Web 應用程式名稱和位置。請提供唯一的名稱，並且選取與您的 Azure 儲存體帳戶相同的地理位置。

	`--git` 參數會在 Azure 上建立此 Web 應用程式的 Git 儲存機制。它也會在目前的目錄中初始化 Git 儲存機制 (如果不存在)，並且新增名為 'azure' 的 [Git 遠端]，用來將應用程式發佈至 Azure。最後，它會建立 **web.config** 檔案，其中包含 Azure 代管 node 應用程式所使用的設定。

	> [AZURE.NOTE]如果目錄已有 Git 儲存機制，命令就不會重新初始化目錄。此外，如果您省略 `--git` 參數，但目錄中有 Git 儲存機制，則命令仍會建立 'azure' 遠端。

	一旦此命令完成，您將會看到類似以下的輸出。請注意，**Website created at** 開頭的這一行包含 Web 應用程式的 URL。

		info:   Executing command site create
		help:   Need a site name
		Name: TableTasklist
		info:   Using location southcentraluswebspace
		info:   Executing `git init`
		info:   Creating default .gitignore file
		info:   Creating a new web site
		info:   Created web site at  tabletasklist.azurewebsites.net
		info:   Initializing repository
		info:   Repository initialized
		info:   Executing `git remote add azure https://username@tabletasklist.azurewebsites.net/TableTasklist.git`
		info:   site create command OK

	> [AZURE.NOTE]如果這是您的訂用帳戶的第一個 App Service Web 應用程式，系統將指示您使用 Azure 入口網站來建立 Web 應用程式。如需詳細資訊，請參閱[在 Azure App Service 中建置和部署 Node.js Web 應用程式]。

### 設定環境變數

在此步驟中，您會新增環境變數至您在 Azure 上的 Web 應用程式組態。從命令列中，輸入下列命令：

	azure site appsetting add
		STORAGE_NAME=<storage account name>;STORAGE_KEY=<storage access key>;PARTITION_KEY=mytasks;TABLE_NAME=tasks


將 **<storage account name>** 取代為您先前建立的儲存體帳戶名稱，並將 **<storage access key>** 取代為您儲存體帳戶的主要存取金鑰。(使用與您稍早建立的 config.json 檔案相同的值)。

或者，您可以在 [Azure 入口網站](https://portal.azure.com)中設定環境變數：

1.  依序按一下 [瀏覽] > [Web 應用程式] > Web 應用程式名稱，開啟 Web 應用程式的刀鋒視窗。

1.  在您的 Web 應用程式刀鋒視窗中，按一下 [所有設定] > [應用程式設定]。

  	<!-- ![Top Menu](./media/storage-nodejs-use-table-storage-web-site/PollsCommonWebSiteTopMenu.png) -->

1.  向下捲動至 [應用程式設定] 區段，並新增金鑰/值組。

  	![應用程式設定](./media/storage-nodejs-use-table-storage-web-site/storage-tasks-appsettings.png)

1. 按一下 [儲存]。


### 發佈應用程式

若要發佈應用程式，請將程式碼檔案認可到 Git，然後推送至 azure/master。

1. 設定您的部署認證。

		azure site deployment user set <name> <password>

2. 新增並認可您的應用程式檔案。

		git add .
		git commit -m "adding files"

3. 將認可推送至 App Service Web 應用程式：

		git push azure master

	使用 **master** 做為目標分支。在部署結束時，您應該會看到如下所示的陳述式：

		To https://username@tabletasklist.azurewebsites.net/TableTasklist.git
 		 * [new branch]      master -> master

4. 完成推送作業後，瀏覽至先前由 `azure create site` 命令傳回的 Web 應用程式 URL 以檢視您的應用程式。


## 後續步驟

雖然本文的步驟說明如何使用資料表服務來存放資訊，您也可以使用 MongoDB。如需詳細資訊，請參閱[使用 MongoDB 的 Node.js Web 應用程式]。

## 其他資源

[Azure 命令列介面]

## 變更的項目
* 如需從網站變更為 App Service 的指南，請參閱：[Azure App Service 及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 如需從舊的入口網站變更為新入口網站的指南，請參閱：[巡覽預覽入口網站的參考](http://go.microsoft.com/fwlink/?LinkId=529715)

[在 Azure App Service 中建置和部署 Node.js Web 應用程式]: web-sites-nodejs-develop-deploy-mac.md
[Continuous deployment using GIT in Azure App Service]: web-sites-publish-source-control.md
[Azure Developer Center]: /develop/nodejs/


[node]: http://nodejs.org
[Git]: http://git-scm.com
[Express]: http://expressjs.com
[for free]: http://windowsazure.com
[Git 遠端]: http://git-scm.com/docs/git-remote

[使用 MongoDB 的 Node.js Web 應用程式]: web-sites-nodejs-store-data-mongodb.md
[Azure CLI]: ../xplat-cli.md

[Continuous deployment using GIT in Azure App Service]: web-sites-publish-source-control.md
[azure]: https://github.com/Azure/azure-sdk-for-node
[node-uuid]: https://www.npmjs.com/package/node-uuid
[nconf]: https://www.npmjs.com/package/nconf
[async]: https://www.npmjs.com/package/async

[Azure Portal]: https://portal.azure.com


[node-table-finished]: ./media/storage-nodejs-use-table-storage-web-site/table_todo_empty.png
[node-table-list-items]: ./media/storage-nodejs-use-table-storage-web-site/table_todo_list.png
[download-publishing-settings]: ./media/storage-nodejs-use-table-storage-web-site/azure-account-download-cli.png
[portal-new]: ./media/storage-nodejs-use-table-storage-web-site/plus-new.png
[portal-storage-account]: ./media/storage-nodejs-use-table-storage-web-site/new-storage.png
[portal-quick-create-storage]: ./media/storage-nodejs-use-table-storage-web-site/quick-storage.png
[portal-storage-access-keys]: ./media/storage-nodejs-use-table-storage-web-site/manage-access-keys.png

[go-to-dashboard]: ./media/storage-nodejs-use-table-storage-web-site/go_to_dashboard.png
[web-configure]: ./media/storage-nodejs-use-table-storage-web-site/sql-task-configure.png
[app-settings-save]: ./media/storage-nodejs-use-table-storage-web-site/savebutton.png
[app-settings]: ./media/storage-nodejs-use-table-storage-web-site/storage-tasks-appsettings.png

[Create and deploy a Node.js application to an Azure Web Site]: web-sites-nodejs-develop-deploy-mac.md
 

<!---HONumber=62-->