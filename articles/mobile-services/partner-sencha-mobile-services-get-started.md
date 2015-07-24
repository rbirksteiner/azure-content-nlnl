<properties
	pageTitle="Azure 行動服務和 Sencha 入門"
	description="遵循此教學課程，可開始使用行動服務和 Sencha HTML5 行動應用程式架構進行開發。"
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-sencha"
	ms.devlang="multiple"
	ms.topic="article" 
	ms.date="04/24/2015"
	ms.author="glenga"/>

# <a name="getting-started"> </a>開始使用行動服務和 Sencha Touch

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>本教學課程展示如何將 Azure 行動服務運用在 Sencha Touch 應用程式中。您將會使用 Sencha Touch 建立簡易的待辦事項應用程式，該應用程式乃使用透過管理入口網站定義的行動服務。本教學課程的目標觀眾為瞭解 JavaScript 及熟悉 Sencha Touch 架構的中級到進階 Web 應用程式開發人員。</p>
<p>如果想要看影片，右邊片段播放的步驟與本教學課程相同。在影片中，Arthur Kay 會說明如何組建使用 Azure 行動服務後端的 Sencha Touch 應用程式。</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=392574" target="_blank" class="label">觀賞教學課程</a> <a style="background-image: url('/media/partner-sencha-mobile-services-get-started/mobile-sencha-get-started-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=392574" target="_blank" class="dev-onpage-video"><span class="icon">播放視訊</span></a> <span class="time">14:37</span></div>
</div>


已完成應用程式的螢幕擷取畫面如下：

![][0]

##需求

- 下載及安裝 [Sencha Touch](http://wwww.sencha.com/products/touch/download" target="_blank")。

- 下載及安裝 [Sencha Cmd 工具](http://www.sencha.com/products/sencha-cmd/download" target="_blank")。

- Java Runtime Environment (JRE) 或 Java Development Kit (如果您要建立 Android 應用程式)

## <a name="create-new-service"> </a>建立新的行動服務

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

##建立 TodoItem 資料表

建立行動服務之後，您可以依照管理入口網站中的簡單快速入門來建立行動服務使用的新資料庫資料表。

1. 在管理入口網站中，按一下 [行動服務]，然後按一下您剛剛建立的行動服務。

2. 在快速入門索引標籤中，按一下 [Choose platform] 下的 [HTML]，並展開 [Create a new HTML app]。

    ![Mobile quickstart html](./media/partner-sencha-mobile-services-get-started/mobile-portal-quickstart-html.png)

    This displays the three easy steps to create and host an HTML app connected to your mobile service.

    ![Mobile quickstart html](./media/partner-sencha-mobile-services-get-started/mobile-quickstart-steps-html.png)

3. 按一下 [Create TodoItems table] 以建立儲存應用程式資料的資料表。

	> [AZURE.NOTE]請勿從管理入口網站下載 HTML 應用程式。反之，我們會在以下小節中手動建立 Sencha Touch 應用程式。


1. 記下管理入口網站中的 [appKey] 和 [appUrl]。您會在本教學指南的其他小節用到它們。

    ![app key](./media/partner-sencha-mobile-services-get-started/mobile-app-key-portal.png)

1. 在 [設定] 索引標籤中，確認 `localhost` 已列在 [跨原始資源共用 (CORS)] 的 [允許來自主機名稱的要求] 清單中。否則，請在 [主機名稱] 欄位中輸入 `localhost`，然後按一下 [儲存]。

    ![Setup CORS for localhost](./media/partner-sencha-mobile-services-get-started/mobile-services-set-cors-localhost.png)

##產生 Touch 應用程式

使用 Sencha Cmd 產生 Sencha Touch 範本應用程式是一項簡單的工作，也是讓應用程式迅速做好執行準備的好方法。

從安裝 Touch 架構的目錄發出下列命令：

	$ sencha generate app Basic /path/to/application

如此能產生名為 'Basic' 的範本 Touch 應用程式。若要啟動應用程式，只要將瀏覽器指向目錄 /path/to/application，系統便應該會呈現標準 Touch 範例應用程式。

##安裝 Sencha Touch Extensions for Azure

您能以手動方式安裝 Azure 擴充功能，或以 Sencha 套件的形式加以安裝。您可以自行選擇要使用的方法。

###手動安裝

在大多數的 Touch 應用程式中，如果您想要新增外部的類別庫，只要下載套件、將套件解除封裝到應用程式目錄，然後再設定 Touch 載入程式的類別庫位置即可。

以下步驟可讓您以手動方式將 Azure 擴充功能新增至應用程式：

1. 從[這裡](https://market.sencha.com/extensions/sencha-touch-extensions-for-windows-azure) (英文) 下載 Azure 擴充功能套件。(您可以使用 Sencha 論壇 ID 來存取此區域。)

2. 從下載目錄將 Azure 擴充功能套件複製到您想要放置套件的位置，然後將套件解除封裝：

        $ cd /path/to/application
	    $ mv /download-location/azure.zip .
    	$ unzip azure.zip  

    如此能建立含有整個套件來源、範例及文件的 **azure** 目錄。來源將位於 **azure/src** 目錄中。


###以 Sencha 套件形式安裝

> [AZURE.NOTE]唯有當您已使用 <code>sencha generate app</code> 命令產生應用程式後才能使用此方法。

對於所有以 Sencha Cmd 產生的應用程式，其根目錄均有 "packages" 資料夾。您可以設定該資料夾的位置，但不論位置為何，"packages" 資料夾的角色在於成為容納應用程式 (或多個應用程式，如果您已建立 Sencha Workspace) 使用之所有套件的儲存體。

由於 Ext.Azure 是 Sencha Cmd 套件，因此您可以輕鬆地使用 Sencha Cmd 來安裝來源程式碼並納入應用程式中。(如需詳細資訊，請參閱 [Sencha Cmd 套件](http://docs.sencha.com/cmd/3.1.2/#!/guide/command_packages) (英文)。)

若要從 Sencha Packages 儲存機制下載及安裝 Azure 擴充功能套件，您需要將套件名稱新增至 **app.json** 檔案並建置應用程式：

1. 將 Azure 套件新增至 app.json 檔案的 requires 區段：

	    {
            "name": "Basic",
	        "requires": [
    	        "touch-azure"
        	]
    	}

2. 使用 **sencha cmd** 重新組建應用程式，以便擷取及安裝套件：

	    $ sencha app build

**sencha app build** 和 **sencha app refresh** 均會立即執行將套件與應用程式整合的必要步驟。一般說來，在變更套件需求後，您需要執行 **sencha app refresh**，使支援 "dev mode" 所需的中繼資料處於最新狀態。

不論您執行哪個命令，Sencha Cmd 均會下載套件並展開到 "packages" 資料夾。之後，您會在工作區內發現 "packages/touch-azure" 資料夾。

##納入及設定 Azure

**檔名**：app.js

既然您已下載 Azure 擴充功能並安裝在應用程式目錄中，下個步驟是指示應用程式該去哪裡搜尋來源檔案及要求這些檔案：

1. 設定 Sencha Loader 的來源程式碼位置：

        Ext.Loader.setConfig({
       	    enabled : true,
           	paths   : {
               	'Ext'       : 'touch/src',
               	'Ext.azure' : '/path-to/azure-for-touch/azure/src'
            }
        });


2. 要求 Azure 類別檔案：

		Ext.application({

			requires: [ 'Ext.azure.Azure' ],

			// ...

		});


3. 設定 Azure

	您可以在應用程式的 launch 區段呼叫 **Ext.Azure.init** 方法來將 Azure 套件初始化。系統會將含有行動服務認證，以及其他要使用之認證和功能的組態物件傳遞給此方法。

	雖然您可以將組態物件直接傳遞給 init 方法，不過我們建議您建立名為 **azure** 的 Sencha 應用程式組態屬性，並將所有適當的資訊放置於屬性中。接著，您可以將此屬性值傳遞給 Ext.Azure.init 方法。

	在 Azure 中建立行動服務時 (請參閱[開始使用 Ext.Azure](http://senchaazuredocs.azurewebsites.net/#!/guide/getting_started) (英文))，系統會將應用程式索引鍵和 URL 指派給該服務。您必須將這些資訊提供給 Azure 套件，使其得以連接服務。

	以下範例是只提供應用程式索引鍵和 URL 的簡易 Azure 組態和初始化：

	    Ext.application({
    	    name: 'Basic',

        	requires: [ 'Ext.azure.Azure' ],

	        azure: {
    	        appKey: 'myazureservice-access-key',
        	    appUrl: 'myazure-service.azure-mobile.net'
	        },

    	    launch: function() {

        	    // Call Azure initialization

            	Ext.Azure.init(this.config.azure);

 	       }
    	});

	如需 Azure 組態選項的詳細資訊，請參閱 Ext.Azure API 文件。


恭喜！ 您的應用程式現在應該可以存取行動服務了。

##組建待辦事項應用程式

既然我們已設定應用程式並使其納入 Azure 擴充功能，以及將行動服務認證提供給應用程式，接下來我們可以繼續建立使用行動服務來檢視及編輯儲存在服務中之待辦事項清單資料的 Touch 應用程式。

###設定 Azure 資料 Proxy

**檔名：**app/model/TodoItem.js

Touch 應用程式將透過資料 Proxy 與行動服務通訊。Proxy 負責執行將要求傳送給行動服務，以及接收來自行動服務之資料等工作。搭配 Touch 資料模型和存放區時，Touch 本身會負責移除及處置遠端資料之處理和將遠端資料納入應用程式等所有繁重的工作。

Sencha Touch 模型能提供用於應用程式之資料記錄的定義。它們不僅能讓您定義資料欄位，還能提供負責處理應用程式和 Azure 行動服務間通訊之 Poxy 的相關組態。

在以下的程式碼中，您可以發現我們不僅定義模型的欄位 (與其類型)，也提供 Poxy 組態。在設定 Poxy 時，您需要提供類型 (即本例中的 'azure')、行動服務 tablename (ToDoItem) 及其他選用參數。在以下範例中，我們將開啟 Poxy 分頁，以便流暢地藉由向前和向後翻頁來瀏覽清單項目。

Azure Poxy 會自動將所有 HTTP 標頭設定為 Azure API 預期的適當 CRUD 操作 (如果有驗證認證的話，亦會包含在內)。

	Ext.define('Basic.model.TodoItem', {
    	extend : 'Ext.data.Model',

	    requires : [
    	    'Ext.azure.Proxy'
    	],

	    config : {
    	    idProperty : 'id',
        	useCache   : false,

	        fields     : [
    	        {
        	        name : 'id',
            	    type : 'int'
            	},
            	{
                	name : 'text',
                	type : 'string'
            	},
            	{
	                name : 'complete',
    	            type : 'boolean'
        	    }
	        ],

	        proxy : {
    	        type               : 'azure',
        	    tableName          : 'TodoItem',
            	enablePagingParams : true
        	}
    	}
	});


###儲存待辦事項

**檔名**：app/store/TodoItems.js

Sencha Touch 存放區可用來儲存資料記錄 (模型) 的集合，這些集合可當做 Touch 元件用來以各種不同方法顯示記錄的來源。包括格線、圖表、清單等。

我們在這裡定義了存放區，並用它來保留所有自 Azure 行動服務擷取的存放區待辦事項清單項目。請注意，存放區組態含有模型類型的名稱 (先前定義的 Basic.model.TodoItem)。它能定義存放區所容納之記錄的結構。

我們也加入一些額外的存放區組態選項，如指定分頁大小 (8 筆記錄)；此外，此存放區的記錄排序是由 Azure 行動服務在遠端執行的 (並未在存放區內部以本機方式進行排序)。

	Ext.define('Basic.store.TodoItems', {
    	extend : 'Ext.data.Store',

	    requires : [
    	    'Basic.model.TodoItem'
	    ],

	    config : {
    	    model        : 'Basic.model.TodoItem',
        	pageSize     : 8,
	        remoteSort   : true,
    	    remoteFilter : true
    	}
	});


###檢視及編輯待辦事項

**檔名**：app/view/DataItem.js

既然我們已定義每個待辦事項的結構，並建立存放區來放置所有記錄，我們應該思考要如何顯示這些資訊給應用程式的使用者。我們通常會透過使用**檢視**來將資訊顯示給使用者檢視可以是任意數目的 Touch 元件，不論是個別存在或與其他元件合併皆可。

以下檢視含有定義每筆記錄之顯示方式的 ListItem，以及容納刪除每筆記錄之動作的按鈕。

	Ext.define('Basic.view.DataItem', {
    	extend : 'Ext.dataview.component.ListItem',
    	xtype  : 'basic-dataitem',

	    requires : [
    	    'Ext.Button',
        	'Ext.layout.HBox',
        	'Ext.field.Checkbox'
    	],

	    config : {
    	    checkbox : {
        	    docked     : 'left',
            	xtype      : 'checkboxfield',
            	width      : 50,
            	labelWidth : 0
        	},

	        text : {
    	        flex : 1
        	},

	        button : {
    	        docked   : 'right',
        	    xtype    : 'button',
            	ui       : 'plain',
	            iconMask : true,
    	        iconCls  : 'delete',
        	    style    : 'color: red;'
        	},

	        dataMap : {
    	        getText : {
        	        setHtml : 'text'
            	},

	            getCheckbox : {
    	            setChecked : 'complete'
        	    }
        	},

	        layout : {
    	        type : 'hbox',
        	    align: 'stretch'
        	}
    	},

	    applyCheckbox : function(config) {
    	    return Ext.factory(config, Ext.field.Checkbox, this.getCheckbox());
    	},

	    updateCheckbox : function (cmp) {
    	    if (cmp) {
        	    this.add(cmp);
        	}
    	},

	    applyButton : function(config) {
    	    return Ext.factory(config, Ext.Button, this.getButton());
    	},

	    updateButton : function (cmp) {
    	    if (cmp) {
        	    this.add(cmp);
        	}
    	}

	});


###建立主要檢視

**檔名**：app/view/Main.js

既然我們已定義個別待辦事項清單項目的版面配置 (前文)，我們想要將定義實際清單項目、應用程式標題及新增工作之按鈕的完整使用者介面包覆在該清單之外。

	Ext.define('Basic.view.Main', {
    	extend : 'Ext.dataview.List',
    	xtype  : 'main',

	    requires : [
    	    'Ext.TitleBar',
        	'Ext.dataview.List',
        	'Ext.data.Store',
        	'Ext.plugin.PullRefresh',
        	'Ext.plugin.ListPaging',
        	'Basic.view.DataItem'
    	],

	    config : {
    	    store : 'TodoItems',

        	useSimpleItems : false,
        	defaultType    : 'basic-dataitem',

	        plugins : [
    	        {
        	        xclass          : 'Ext.plugin.PullRefresh',
            	    pullRefreshText : 'Pull down to refresh!'
            	},
            	{
                	xclass     : 'Ext.plugin.ListPaging',
                	autoPaging : true
            	}
        	],

	        scrollable : {
    	        direction     : 'vertical',
        	    directionLock : true
        	},

	        items : [
    	        {
        	        docked : 'top',
            	    xtype  : 'titlebar',
                	title  : 'Azure Mobile - Basic Data Example'
            	},
            	{
                	xtype  : 'toolbar',
                	docked : 'bottom',
                	items  : [
                    	{
                        	xtype       : 'textfield',
                        	placeHolder : 'Enter new task',
                        	flex        : 1
                    	},
                    	{
                        	xtype  : 'button',
                        	action : 'add',
                        	text   : 'Add'
                    	}
                	]
            	}
        	]
    	}
	});

###將所有要素串連在一起

**檔名：**app/controller/Main.js

組建應用程式的最後一個步驟是對按鈕動作 (刪除、儲存等) 產生回應，以及提供所有要求背後的邏輯。Sencha Touch 使用能接聽這些事件及據以回應的控制器。

	Ext.define('Basic.controller.Main', {
    	extend : 'Ext.app.Controller',

	    config : {
    	    refs : {
        	    todoField : 'main toolbar textfield',
            	main      : 'main'
        	},

	        control : {
    	        'button[action=add]'    : {
        	        tap : 'onAddItem'
            	},
            	'button[action=reload]' : {
                	tap : 'onReload'
            	},

	            main : {
    	            activate      : 'loadInitialData',
        	        itemdoubletap : 'onItemEdit'
            	},

	            'basic-dataitem checkboxfield' : {
    	            change : 'onItemCompleteTap'
        	    },

            	'basic-dataitem button' : {
                	tap : 'onItemDeleteTap'
            	}
        	}
    	},

	    loadInitialData : function () {
    	    Ext.getStore('TodoItems').load();
    	},

	    onItemDeleteTap : function (button, e, eOpts) {
    	    var store    = Ext.getStore('TodoItems'),
        	    dataItem = button.up('dataitem'),
            	rec      = dataItem.getRecord();

	        rec.erase({
    	        success: function (rec, operation) {
        	        store.remove(rec);
            	},
            	failure: function (rec, operation) {
                	Ext.Msg.alert(
                    	'Error',
                    	Ext.util.Format.format('There was an error deleting this task.<br/><br/>	Status Code: {0}<br/>Status Text: {1}',
                    	operation.error.status,
                    	operation.error.statusText)
                	);
            	}
        	});
    	},

	    onItemCompleteTap : function (checkbox, newVal, oldVal, eOpts) {
    	    var dataItem = checkbox.up('dataitem'),
        	    rec      = dataItem.getRecord(),
            	recVal   = rec.get('complete');

	        // this check is needed to prevent an issue where multiple creates get triggered from one create
        	if (newVal !== recVal) {
            	rec.set('complete', newVal);
            	rec.save({
                	success: function (rec, operation) {
                    	rec.commit();
                	},
                	failure: function (rec, operation) {
                    	// since there was a failure doing the update on the server then silently reject the change
	                    rec.reject(true);
    	                Ext.Msg.alert(
        	                'Error',
            	            Ext.util.Format.format('There was an error updating this task.<br/><br/>Status Code: {0}<br/>Status Text: {1}',
            	            operation.error.status,
            	            operation.error.statusText)
	                    );
    	            }
        	    });
        	}
    	},

	    onItemEdit : function (list, index, target, record, e, eOpts) {
    	    var rec = list.getSelection()[0];

        	Ext.Msg.prompt('Edit', 'Rename task',
            	function (buttonId, value) {
                	if (buttonId === 'ok') {
                    	rec.set('text', value);
                    	rec.save({
                        	success: function (rec, operation) {
                            	rec.commit();
                        	},
                        	failure: function (rec, operation) {
                            	// since there was a failure doing the update on the server then reject the change
                            	rec.reject();
                            	Ext.Msg.alert(
                                	'Error',
                                	Ext.util.Format.format('There was an error updating this task.<br/><br/>Status Code: {0}<br/>Status Text: {1}',
                                	operation.error.status,
                                	operation.error.statusText)
                            	);
                        	}
                    	});
                	}
            	},
            	null,
            	false,
            	record.get('text')
        	);
    	},

	    onReload : function () {
    	    Ext.getStore('TodoItems').load();
    	},

	    onAddItem : function () {
    	    var me = this,
        	    rec,
            	store = Ext.getStore('TodoItems'),
            	field = me.getTodoField(),
            	value = field.getValue();

	        if (value === '') {
    	        Ext.Msg.alert('Error', 'Please enter Task name', Ext.emptyFn);
        	}
        	else {
            	rec = Ext.create('Basic.model.TodoItem', {
                	complete : false,
                	text     : value
            	});
            	//store.insert(0, rec); //insert at the top
            	//store.sync();
            	rec.save({
                	success: function (rec, operation) {
                    	store.insert(0, rec); //insert at the top
                    	field.setValue('');
                	},
                	failure: function (rec, operation) {
                    	Ext.Msg.alert(
                        	'Error',
                        	Ext.util.Format.format('There was an error creating this task.<br/><br/>Status Code: {0}<br/>Status Text: {1}',
                        	operation.error.status,
                        	operation.error.statusText)
                    	);
                	}
            	});
        	}
    	}
	});

###組合在一起

**檔名**：app.js

最後一個步驟一完成主要應用程式檔案的編輯，以及提供已定義之模型、存放區、檢視及控制器的詳細資訊。系統會自動將這些資源的來源檔案載入應用程式。最後，系統會叫用能建立及顯示主要應用程式檢視 'Basic.main.View' 的 launch 方法。


	Ext.Loader.setConfig({
    	enabled : true,
    	paths   : {
        	'Ext'       : 'touch/src',
        	'Ext.azure' : 'packages/azure/src'
    	}
	});

	Ext.application({
    	name : 'Basic',

	    requires : [
    	    'Ext.MessageBox',
        	'Ext.azure.Azure'
    	],

	    views : [
    	    'Main'
    	],

	    controllers : [
    	    'Main'
    	],

	    stores : [
    	    'TodoItems'
    	],

	    azure : {
    	    appUrl : 'YOUR_APP_URL.azure-mobile.net',
        	appKey : 'YOUR_APP_KEY'
    	},

	    icon : {
    	    '57'  : 'resources/icons/Icon.png',
        	'72'  : 'resources/icons/Icon~ipad.png',
        	'114' : 'resources/icons/Icon@2x.png',
        	'144' : 'resources/icons/Icon~ipad@2x.png'
    	},

	    isIconPrecomposed : true,

	    startupImage : {
    	    '320x460'   : 'resources/startup/320x460.jpg',
        	'640x920'   : 'resources/startup/640x920.png',
        	'768x1004'  : 'resources/startup/768x1004.png',
        	'748x1024'  : 'resources/startup/748x1024.png',
        	'1536x2008' : 'resources/startup/1536x2008.png',
        	'1496x2048' : 'resources/startup/1496x2048.png'
    	},

	    launch : function () {
    	    // Destroy the #appLoadingIndicator element
        	Ext.fly('appLoadingIndicator').destroy();

	        // Initialize Azure
    	    Ext.Azure.init(this.config.azure);

	        // Initialize the main view
    	    Ext.Viewport.add(Ext.create('Basic.view.Main'));
	    },

	    onUpdated : function () {
    	    Ext.Msg.confirm(
        	    "Application Update",
            	"This application has just successfully been updated to the latest version. Reload now?",
	            function (buttonId) {
    	            if (buttonId === 'yes') {
        	            window.location.reload();
            	    }
            	}
        	);
   		}
	});

###裝載並執行 Sencha Touch 應用程式

本教學課程的最終階段是在本機電腦上裝載並執行新的應用程式。

  1. 在終端機中，瀏覽至解壓縮之應用程式的位置。

  2. 使用 Sencha Cmd 執行以下命令：

    * *sencha app refresh*：這會指示 Sencha Cmd 找出所有的應用程式相依項目，並下載任何需要的封裝 (例如 [Sencha Touch Extensions for Azure](https://market.sencha.com/extensions/sencha-touch-extensions-for-windows-azure))。

    * *sencha web start*：這會啟動本機 Web 伺服器以測試我們的應用程式。

    ![sencha web start](./media/partner-sencha-mobile-services-get-started/sencha-web-start.png)

  3. 在網頁瀏覽器中開啟終端機列示的 URL，以啟動應用程式 (如 http://localhost:1841)。

  4. 在應用程式中輸入有意義的文字 (例如「完成教學課程」)，然後按一下 [新增]。

    ![new todo item](./media/partner-sencha-mobile-services-get-started/new-todo-item.png)

    如此會傳送 POST 要求到 Azure 中代管的新行動服務。Data from the request is inserted into the TodoItem table.

  5. 回到管理入口網站中，按一下 [資料] 索引標籤，然後按一下 [TodoItems] 表格。

    ![Todo Items table](./media/partner-sencha-mobile-services-get-started/mobile-data-tab.png)

    如此可讓您瀏覽由應用程式插入資料表中的資料。

    ![browse todo table](./media/partner-sencha-mobile-services-get-started/mobile-data-browse.png)

##後續步驟
現在，您已完成入門指南，了解如何在行動服務中搭配 Sencha 執行其他重要工作。

[下載](https://github.com/arthurakay/sencha-touch-azure-example)含其他樣式和功能的完整範例應用程式，瞭解 Sencha Touch 的其他能力！

接著，深入瞭解 Sencha Touch Extensions for Azure 的詳細資訊：

  * 範例應用程式[逐步解說](http://docs.sencha.com/touch-azure/1.0.0/#!/guide/data_filters) (英文)
  * 到 [Sencha 論壇](http://www.sencha.com/forum)尋求協助
  * 瀏覽 [Sencha 文件](http://docs.sencha.com/) (英文)
  * 搭配 Azure 行動服務使用 Sencha：[(影片)](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-126-Using-Sencha-With-Windows-Azure-Mobile-Services)


##其他資源

  * [下載 Sencha Touch](http://pages.sencha.com/touch-for-azure.html)
  * [Sencha Touch Extensions for Azure](https://market.sencha.com/extensions/sencha-touch-extensions-for-windows-azure) (英文)


##摘要

Sencha Touch Extension for Azure 套件備有此處簡述的範例，其位於 Basic Data 範例所在的範例目錄中。除了詳細的註解和說明之外，套件還備有一些展示本延伸功能之其他功能特性的範例。

如需開始使用 Sencha Touch 的詳細資訊，請造訪完整的[指南](http://docs.sencha.com/touch/#!/guide) (英文) 集合。

<!-- images -->
[0]: ./media/partner-sencha-mobile-services-get-started/finished-app.png
 

<!---HONumber=July15_HO1-->