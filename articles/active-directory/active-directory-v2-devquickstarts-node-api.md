<properties
	pageTitle="應用程式模型 v2.0 | Microsoft Azure"
	description="如何建置可接受來自個人 Microsoft 帳戶及工作或學校帳戶之權杖的 NodeJS Web API。"
	services="active-directory"
	documentationCenter="nodejs"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="08/25/2015"
	ms.author="brandwe"/>

# 應用程式模型 v2.0 預覽：使用 node.js 保護 Web API

> [AZURE.NOTE]此資訊適用於 v2.0 應用程式模型公開預覽。如需如何與正式運作之 Azure AD 服務整合的指示，請參閱 [Azure Active Directory 開發人員指南](active-directory-developers-guide.md)。

有了 v2.0 應用程式模型，您就能使用 [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) 存取權杖來保護 Web API，讓使用者能夠使用個人的 Microsoft 帳戶以及公司或學校帳戶，安全存取您的 Web API。

**Passport** 是 Node.js 的驗證中介軟體。您可以暗中將極具彈性且模組化的 Passport 放入任何 Express 或 Resitify Web 應用程式。一組完整的策略可支援使用使用者名稱和密碼、Facebook、Twitter 及其他等驗證。我們已為 Microsoft Azure Active Directory 開發一項策略。我們將安裝此模組，然後加入 Microsoft Azure Active Directory `passport-azure-ad` 外掛程式。

若要執行此作業，您需要執行下列動作：

1. 向 Azure AD 註冊應用程式
2. 設定您的 App 來使用 Passport 的 azure-ad-passport 外掛程式。
3. 設定用戶端應用程式呼叫待辦事項清單 Web API

本教學課程的程式碼保留在 [GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs) 上。若要遵循執行，您可以[利用 .zip 格式下載 App 的基本架構](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs/archive/skeleton.zip)，或複製基本架構：

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs.git```

The completed application is provided at the end of this tutorial as well.


## 1. Register an App
Create a new app at [apps.dev.microsoft.com](https://apps.dev.microsoft.com), or follow these [detailed steps](active-directory-v2-app-registration.md).  Make sure to:

- Copy down the **Application Id** assigned to your app, you'll need it soon.
- Add the **Mobile** platform for your app.
- Copy down the **Redirect URI** from the portal. You must use the default value of `urn:ietf:wg:oauth:2.0:oob`.


## 2: Download node.js for your platform
To successfully use this sample, you must have a working installation of Node.js.

Install Node.js from [http://nodejs.org](http://nodejs.org).

## 3: Install MongoDB on to your platform

To successfully use this sample, you must have a working installation of MongoDB. We will use MongoDB to make our REST API persistant across server instances.

Install MongoDB from [http://mongodb.org](http://www.mongodb.org).

> [AZURE.NOTE] This walkthrough assumes that you use the default installation and server endpoints for MongoDB, which at the time of this writing is: mongodb://localhost

## 4: Install the Restify modules in to your Web API

We will be using Resitfy to build our REST API. Restify is a minimal and flexible Node.js application framework derived from Express that has a robust set of features for building REST APIs on top of Connect.

### Install Restify

From the command-line, change directories to the azuread directory. If the **azuread** directory does not exist, create it.

`cd azuread` - or- `mkdir azuread;`

Type the following command:

`npm install restify`

This command installs Restify.

#### Did you get an error?

When using npm on some operating systems, you may receive an error of Error: EPERM, chmod '/usr/local/bin/..' and a request to try running the account as an administrator. If this occurs, use the sudo command to run npm at a higher privilege level.

#### Did you get an error regarding DTrace?

You may see something like this when installing Restify:

```Shell
clang: error: no such file or directory: 'HD/azuread/node\_modules/restify/node\_modules/dtrace-provider/libusdt' make: *** [Release/DTraceProviderBindings.node] Error 1 gyp ERR! build error gyp ERR! stack Error: `make` failed with exit code: 2 gyp ERR! stack at ChildProcess.onExit (/usr/local/lib/node\_modules/npm/node\_modules/node-gyp/lib/build.js:267:23) gyp ERR! stack at ChildProcess.EventEmitter.emit (events.js:98:17) gyp ERR! stack at Process.ChildProcess.\_handle.onexit (child\_process.js:789:12) gyp ERR! System Darwin 13.1.0 gyp ERR! command "node" "/usr/local/lib/node\_modules/npm/node\_modules/node-gyp/bin/node-gyp.js" "rebuild" gyp ERR! cwd /Volumes/Development HD/azuread/node\_modules/restify/node\_modules/dtrace-provider gyp ERR! node -v v0.10.11 gyp ERR! node-gyp -v v0.10.0 gyp ERR! not ok npm WARN optional dep failed, continuing dtrace-provider@0.2.8 ```


Restify 提供使用 DTrace 追蹤 REST 呼叫的強大機制。不過，許多作業系統沒有 DTrace 可以使用。您可以放心地忽略這些錯誤。


此命令的輸出應類似這樣：


	restify@2.6.1 node_modules/restify
	├── assert-plus@0.1.4
	├── once@1.3.0
	├── deep-equal@0.0.0
	├── escape-regexp-component@1.0.2
	├── qs@0.6.5
	├── tunnel-agent@0.3.0
	├── keep-alive-agent@0.0.1
	├── lru-cache@2.3.1
	├── node-uuid@1.4.0
	├── negotiator@0.3.0
	├── mime@1.2.11
	├── semver@2.2.1
	├── spdy@1.14.12
	├── backoff@2.3.0
	├── formidable@1.0.14
	├── verror@1.3.6 (extsprintf@1.0.2)
	├── csv@0.3.6
	├── http-signature@0.10.0 (assert-plus@0.1.2, asn1@0.1.11, ctype@0.5.2)
	└── bunyan@0.22.0 (mv@0.0.5)


## 5：在您的 Web API 上安裝 Passport.js

[Passport](http://passportjs.org/) 是 Node.js 的驗證中介軟體。您可以暗中將極具彈性且模組化的 Passport 放入任何 Express 或 Resitify Web 應用程式。一組完整的策略可支援使用使用者名稱和密碼、Facebook、Twitter 及其他等驗證。我們已為 Azure Active Directory 開發一個策略。我們將會安裝此模組，然後加入 Azure Active Directory 的策略外掛程式。

從命令列將目錄變更至 azuread 目錄。

輸入以下命令以安裝 passport.js

`npm install passport`

此命令的輸出應類似這樣：

	passport@0.1.17 node_modules\passport
	├── pause@0.0.1
	└── pkginfo@0.2.3

## 6：將 Passport-Azure-AD 加入您的 Web API

接下來，我們將使用 passport-azuread 來加入 OAuth 策略，這是一套將 Azure Active Directory 連接到 Passport 的策略。在這個 Rest API 範例中，我們將針對 Bearer Tokens 使用此策略。

> [AZURE.NOTE]雖然 OAuth2 提供可發行任何已知權杖類型的架構，但只會普遍使用特定的權杖類型。在保護端點中，這會是持有者權杖。持有者權杖是在 OAuth2 中最普遍發行的權杖類型，而且許多實作假設持有者權杖會是唯一發行的權杖類型。

從命令列中，將目錄變更到 azuread 目錄

輸入下列命令來安裝 Passport.js passport-azure-ad 模組：

`npm install passport-azure-ad`

此命令的輸出應類似這樣：

``
passport-azure-ad@1.0.0 node_modules/passport-azure-ad
├── xtend@4.0.0
├── xmldom@0.1.19
├── passport-http-bearer@1.0.1 (passport-strategy@1.0.0)
├── underscore@1.8.3
├── async@1.3.0
├── jsonwebtoken@5.0.2
├── xml-crypto@0.5.27 (xpath.js@1.0.6)
├── ursa@0.8.5 (bindings@1.2.1, nan@1.8.4)
├── jws@3.0.0 (jwa@1.0.1, base64url@1.0.4)
├── request@2.58.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, tunnel-agent@0.4.1, oauth-sign@0.8.0, isstream@0.1.2, extend@2.0.1, json-stringify-safe@5.0.1, node-uuid@1.4.3, qs@3.1.0, combined-stream@1.0.5, mime-types@2.0.14, form-data@1.0.0-rc1, http-signature@0.11.0, bl@0.9.4, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
└── xml2js@0.4.9 (sax@0.6.1, xmlbuilder@2.6.4)
``

## 7：將 MongoDB 模組加入您的 Web API

我們將使用 MongoDB 作為資料存放區。基於這個理由，我們必須安裝兩個廣泛使用的外掛程式才能管理名為 Mongoose 的模型與結構描述，以及同樣稱為 MongoDB 的 MongoDB 的資料庫驅動程式。


* `npm install mongoose`
* `npm install mongodb`

## 8：安裝其他模組

接下來，我們將會安裝其餘所需的模組。


從命令列將目錄變更至 **azuread** 資料夾 (如果您尚未在此目錄下)：

`cd azuread`


請輸入下列命令，在您的 node\_modules 目錄中安裝下列模組：

* `npm install crypto`
* `npm install assert-plus`
* `npm install posix-getopt`
* `npm install util`
* `npm install path`
* `npm install connect`
* `npm install xml-crypto`
* `npm install xml2js`
* `npm install xmldom`
* `npm install async`
* `npm install request`
* `npm install underscore`
* `npm install grunt-contrib-jshint@0.1.1`
* `npm install grunt-contrib-nodeunit@0.1.2`
* `npm install grunt-contrib-watch@0.2.0`
* `npm install grunt@0.4.1`
* `npm install xtend@2.0.3`
* `npm install bunyan`
* `npm update`


## 9：使用您的相依性建立 server.js

server.js 檔案可提供我們 Web API 伺服器的大部分功能。我們將在此檔案中加入大部分的程式碼。基於生產目的，您會將功能重整成較小的檔案，例如單獨分開的路徑和控制器。基於此示範的目的，我們將在這項功能中使用 server.js。

從命令列將目錄變更至 **azuread** 資料夾 (如果您尚未在此目錄下)：

`cd azuread`

在偏好的編輯器中建立 `server.js` 檔案，並加入下列資訊：

```Javascript
'use strict';
/**
* Module dependencies.
*/
var util = require('util');
var assert = require('assert-plus');
var mongoose = require('mongoose/');
var bunyan = require('bunyan');
var restify = require('restify');
var config = require('./config');
var passport = require('passport');
var OIDCBearerStrategy = require('passport-azure-ad').OIDCStrategy;
```

儲存檔案。我們稍後會再回到此檔案。

## 10：建立可儲存 Azure AD 設定的設定檔

這個程式碼檔會將設定參數從您的 Azure Active Directory 入口網站傳遞到 Passport.js。您會在將 Web API 加入入口網站 (本逐步解說的第一個部分) 時建立這些設定值。在您完成複製程式碼之後，我們將說明要放入這些參數值的內容。


從命令列將目錄變更至 **azuread** 資料夾 (如果您尚未在此目錄下)：

`cd azuread`

在偏好的編輯器中建立 `config.js` 檔案，並加入下列資訊：

```Javascript
// Don't commit this file to your public repos. This config is for first-run
exports.creds = {
mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
issuer: 'https://sts.windows.net/**<your application id>**/',
audience: '<your redirect URI>',
identityMetadata: 'https://login.microsoftonline.com/common/.well-known/openid-configuration' // For using Microsoft you should never need to change this.
};

```



### 必要值

*IdentityMetadata*：passport-azure-ad 將在此處尋找適用於 IdP 的設定資料以及用來驗證 JWT 權杖的金鑰。如果使用 Azure Active Directory，您可能不想變更此項目。

*audience*：來自入口網站的重新導向 URI。

> [AZURE.NOTE]我們會經常變更金鑰。請確定您總是從 "openid\_keys" URL 中進行提取，而且應用程式可以存取網際網路。


## 11：將設定加入 server.js 檔案

我們必須從您剛才跨應用程式建立的組態檔中讀取這些值。若要這樣做，我們只需在應用程式中將 .config 檔案作為必要資源加入，然後將全域變數設定為 config.js 文件中的那些值即可

從命令列將目錄變更至 **azuread** 資料夾 (如果您尚未在此目錄下)：

`cd azuread`

在偏好的編輯器中開啟 `server.js` 檔案，並加入下列資訊：

```Javascript
var config = require('./config');
```
然後，使用下列程式碼在 `server.js` 中加入新的區段：

```Javascript
// We pass these options in to the ODICBearerStrategy.
var options = {
// The URL of the metadata document for your app. We will put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
identityMetadata: config.creds.identityMetadata,
issuer: config.creds.issuer,
audience: config.creds.audience
};
// array to hold logged in users and the current logged in user (owner)
var users = [];
var owner = null;
// Our logger
var log = bunyan.createLogger({
name: 'Microsoft Azure Active Directory Sample'
});
```

## 12：使用 Moongoose 加入 MongoDB 模型和結構描述資訊

現在，當我們將這三個檔案整合一起提供給 REST API 服務時，您便會開始看到所有準備工作的成效。

在此逐步解說中，我們將使用 MongoDB 來儲存工作，如***步驟 4*** 中所述。

如果您還記得我們在步驟 11 中建立的 config.js 檔案，我們呼叫了資料庫 *tasklist*，因為那是我們放在 mogoose\_auth\_local 連線 URL 結尾處的內容。您不需要在 MongoDB 中事先建立此資料庫，它會在您第一次執行伺服器應用程式時為您建立 (假設此資料庫不存在)。

既然我們已經告訴伺服器想要使用哪個 MongoDB 資料庫，我們必須撰寫一些額外程式碼，以建立伺服器工作的模型和結構描述。

#### 模型的討論

我們的結構描述模型十分簡單，而且您可以視需要加以開發。

名稱 - 指派給工作的人員名稱。***字串***

工作 - 工作本身。***字串***

日期 - 工作到期的日期。***DATETIME***

已完成 - 工作是否已完成。***布林***

#### 在程式碼中建立結構描述


從命令列將目錄變更至 **azuread** 資料夾 (如果您尚未在此目錄下)：

`cd azuread`

在偏好的編輯器中開啟 `server.js` 檔案，並在組態項目下方加入下列資訊：

```Javascript
// MongoDB setup
// Setup some configuration
var serverPort = process.env.PORT || 8080;
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');
```
這會連線到 MongoDB 伺服器，並將結構描述物件傳回給我們。

#### 使用這個結構描述，在程式碼中建立模型

以下是您之前撰寫的程式碼，請加入下列程式碼：

```Javascript
// Here we create a schema to store our tasks and users. Pretty simple schema for now.
var TaskSchema = new Schema({
owner: String,
task: String,
completed: Boolean,
date: Date
});
// Use the schema to register a model
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```
從程式碼可以得知，當我們在定義***路由***時，我們會建立結構描述，然後建立將用來儲存整個程式碼資料的模型物件。

## 13：在工作 REST API 伺服器中加入路由

既然我們已經擁有可以使用的資料庫模型，讓我們新增將用於 REST API 伺服器的路由。

### 關於 Restify 中的路由

在 Restify 中的路由工作與使用 Express 堆疊的路由工作方式完全相同。您可以使用您預期用戶端應用程式呼叫的 URI 來定義路由。通常，您會在個別的檔案中定義您的路由。基於本文的目的，我們會將路由放在 server.js 檔案中。在實際執行環境中，我們建議您將這些路由分到他們自己的檔案。

Restify 路由的典型模式是：

```Javascript
function createObject(req, res, next) {
// do work on Object
_object.name = req.params.object; // passed value is in req.params under object
///...
return next(); // keep the server going
}
....
server.post('/service/:add/:object', createObject); // calls createObject on routes that match this.
```


這是最基本層級的模式。Resitfy (及 Express) 提供更深入的功能，例如定義應用程式類型和執行不同端點之間的複雜路由。基於本文的目的，我們會將這些路由儘可能的保持簡單。

#### 將預設路由加入伺服器

我們現在可以新增建立、擷取、更新和刪除的基本 CRUD 路由。

從命令列將目錄變更至 **azuread** 資料夾 (如果您尚未在此目錄下)：

`cd azuread`

在偏好的編輯器中開啟 `server.js` 檔案，並在您先前製作的資料庫項目底下加入下列資訊：

```Javascript
/**
*
* APIs for our REST Task server
*/
// Create a task
function createTask(req, res, next) {
// Resitify currently has a bug which doesn't allow you to set default headers
// This headers comply with CORS and allow us to mongodbServer our response to any origin
res.header("Access-Control-Allow-Origin", "*");
res.header("Access-Control-Allow-Headers", "X-Requested-With");
// Create a new task model, fill it up and save it to Mongodb
var _task = new Task();
if (!req.params.task) {
req.log.warn({
params: p
}, 'createTodo: missing task');
next(new MissingTaskError());
return;
}
_task.owner = owner;
_task.task = req.params.task;
_task.date = new Date();
_task.save(function(err) {
if (err) {
req.log.warn(err, 'createTask: unable to save');
next(err);
} else {
res.send(201, _task);
}
});
return next();
}
// Delete a task by name
function removeTask(req, res, next) {
Task.remove({
task: req.params.task,
owner: owner
}, function(err) {
if (err) {
req.log.warn(err,
'removeTask: unable to delete %s',
req.params.task);
next(err);
} else {
log.info('Deleted task:', req.params.task);
res.send(204);
next();
}
});
}
// Delete all tasks
function removeAll(req, res, next) {
Task.remove();
res.send(204);
return next();
}
// Get a specific task based on name
function getTask(req, res, next) {
log.info('getTask was called for: ', owner);
Task.find({
owner: owner
}, function(err, data) {
if (err) {
req.log.warn(err, 'get: unable to read %s', owner);
next(err);
return;
}
res.json(data);
});
return next();
}
/// Simple returns the list of TODOs that were loaded.
function listTasks(req, res, next) {
// Resitify currently has a bug which doesn't allow you to set default headers
// This headers comply with CORS and allow us to mongodbServer our response to any origin
res.header("Access-Control-Allow-Origin", "*");
res.header("Access-Control-Allow-Headers", "X-Requested-With");
log.info("listTasks was called for: ", owner);
Task.find({
owner: owner
}).limit(20).sort('date').exec(function(err, data) {
if (err)
return next(err);
if (data.length > 0) {
log.info(data);
}
if (!data.length) {
log.warn(err, "There is no tasks in the database. Add one!");
}
if (!owner) {
log.warn(err, "You did not pass an owner when listing tasks.");
} else {
res.json(data);
}
});
return next();
}
```

### 新增路由的一些錯誤處理

加入一些錯誤處理是個合理的做法，可讓我們將遇到的問題，採用方便了解的方式反向通訊傳給用戶端。

在您之前撰寫的程式碼底下加入下列程式碼：

```Javascript
///--- Errors for communicating something interesting back to the client
function MissingTaskError() {
restify.RestError.call(this, {
statusCode: 409,
restCode: 'MissingTask',
message: '"task" is a required parameter',
constructorOpt: MissingTaskError
});
this.name = 'MissingTaskError';
}
util.inherits(MissingTaskError, restify.RestError);
function TaskExistsError(owner) {
assert.string(owner, 'owner');
restify.RestError.call(this, {
statusCode: 409,
restCode: 'TaskExists',
message: owner + ' already exists',
constructorOpt: TaskExistsError
});
this.name = 'TaskExistsError';
}
util.inherits(TaskExistsError, restify.RestError);
function TaskNotFoundError(owner) {
assert.string(owner, 'owner');
restify.RestError.call(this, {
statusCode: 404,
restCode: 'TaskNotFound',
message: owner + ' was not found',
constructorOpt: TaskNotFoundError
});
this.name = 'TaskNotFoundError';
}
util.inherits(TaskNotFoundError, restify.RestError);
```


## 14：建立伺服器！

我們已經定義好資料庫，也準備好路由，要做的最後一件事是加入將會管理呼叫的伺服器執行個體。

Restify (及 Express) 有很多進階自訂項目可讓您在 REST API 伺服器上執行，但再重申一次，基於本文的目的，我們將使用最基本的設定。

```Javascript
/**
* Our Server
*/
var server = restify.createServer({
name: "Microsoft Azure Active Directroy TODO Server",
version: "2.0.1"
});
// Ensure we don't drop data on uploads
server.pre(restify.pre.pause());
// Clean up sloppy paths like //todo//////1//
server.pre(restify.pre.sanitizePath());
// Handles annoying user agents (curl)
server.pre(restify.pre.userAgentConnection());
// Set a per request bunyan logger (with requestid filled in)
server.use(restify.requestLogger());
// Allow 5 requests/second by IP, and burst to 10
server.use(restify.throttle({
burst: 10,
rate: 5,
ip: true,
}));
// Use the common stuff you probably want
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
mapParams: true
}));
```
## 15：加入路由 (目前不含驗證)

```Javascript
/// Now the real handlers. Here we just CRUD
/**
/*
/* Each of these handlers are protected by our OIDCBearerStrategy by invoking 'oidc-bearer'
/* in the pasport.authenticate() method. We set 'session: false' as REST is stateless and
/* we don't need to maintain session state. You can experiement removing API protection
/* by removing the passport.authenticate() method like so:
/*
/* server.get('/tasks', listTasks);
/*
**/
server.get('/tasks', listTasks);
server.get('/tasks', listTasks);
server.get('/tasks/:owner', getTask);
server.head('/tasks/:owner', getTask);
server.post('/tasks/:owner/:task', createTask);
server.post('/tasks', createTask);
server.del('/tasks/:owner/:task', removeTask);
server.del('/tasks/:owner', removeTask);
server.del('/tasks', removeTask);
server.del('/tasks', removeAll, function respond(req, res, next) {
res.send(204);
next();
});
// Register a default '/' handler
server.get('/', function root(req, res, next) {
var routes = [
'GET /',
'POST /tasks/:owner/:task',
'POST /tasks (for JSON body)',
'GET /tasks',
'PUT /tasks/:owner',
'GET /tasks/:owner',
'DELETE /tasks/:owner/:task'
];
res.send(200, routes);
next();
});
server.listen(serverPort, function() {
var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
consoleMessage += '\n %s server is listening at %s';
consoleMessage += '\n Open your browser to %s/tasks\n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';
});
```
## 16：加入 OAuth 支援之前，請先執行伺服器。

加入驗證之前，請先測試您的伺服器

這樣做的最簡單方式是在命令列中使用 curl。在執行此動作之前，我們需要一個可讓我們將輸出剖析為 JSON 的簡單公用程式。若要這樣做，請安裝 json 工具，以供下面所有範例使用。

`$npm install -g jsontool`

這會全域安裝 JSON 工具。現在已完成此動作，讓我們開始使用伺服器：

首先，請確定 monogoDB 執行個體正在執行中...

`$sudo mongod`

然後，變更目錄並啟動 curling...

`$ cd azuread` `$ node server.js`

`$ curl -isS http://127.0.0.1:8080 | json`

```Shell
HTTP/1.1 200 OK
Connection: close
Content-Type: application/json
Content-Length: 171
Date: Tue, 14 Jul 2015 05:43:38 GMT
[
"GET /",
"POST /tasks/:owner/:task",
"POST /tasks (for JSON body)",
"GET /tasks",
"PUT /tasks/:owner",
"GET /tasks/:owner",
"DELETE /tasks/:owner/:task"
]
```

接著，我們可以透過以下方式新增工作：

`$ curl -isS -X POST http://127.0.0.1:8888/tasks/brandon/Hello`

回應應為：

```Shell
HTTP/1.1 201 Created
Connection: close
Access-Control-Allow-Origin: *
Access-Control-Allow-Headers: X-Requested-With
Content-Type: application/x-www-form-urlencoded
Content-Length: 5
Date: Tue, 04 Feb 2014 01:02:26 GMT
Hello
```
而且我們可以透過以下方式列出 Brandon 的工作：

`$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

如果所有項目都沒問題，我們便可以開始將 OAuth 加入 REST API 伺服器。

**您必須將 REST API 伺服器搭配 MongoDB 使用！**

## 17：將驗證加入 REST API 伺服器

既然我們已經擁有執行中的 REST API (順道恭喜您！)，我們可以開始讓它在 Azure AD 中發揮其價值。

從命令列將目錄變更至 **azuread** 資料夾 (如果您尚未在此目錄下)：

`cd azuread`

### 1：使用 passport-azure-ad 包含的 oidcbearerstrategy

到目前為止，我們已經建置典型的 REST TODO 伺服器，且其中不含任何授權種類。這是我們將其結合在一起的起點。

首先，需指出要使用 Passport。在其他伺服器設定之後緊接著執行此動作：

```Javascript
// Let's start using Passport.js

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support
```

> [AZURE.TIP]撰寫 API 時，您應一律將資料連結到使用者無法證明其在權杖中是唯一的項目。當此伺服器儲存 TODO 項目時，會根據我們放在 [擁有者] 欄位的權杖 (透過 token.sub 呼叫) 中的使用者訂用帳戶識別碼來儲存它們。這確保只有該使用者可以存取他的 TODO，而且沒有其他人可存取輸入的 TODO。不會在「擁有者」API 中公開，因此，外部使用者可以要求其他的 TODO，即使它們已經過驗證也一樣。

接下來，我們將使用隨附於 passport-azure-ad 的 Open ID Connect Bearer 策略。目前只需看一下此程式碼，我很快就會討論到它。將這段程式碼放在您貼上上述內容的後方：

```Javascript
/**
/*
/* Calling the OIDCBearerStrategy and managing users
/*
/* Passport pattern provides the need to manage users and info tokens
/* with a FindorCreate() method that must be provided by the implementor.
/* Here we just autoregister any user and implement a FindById().
/* You'll want to do something smarter.
**/
var findById = function(id, fn) {
for (var i = 0, len = users.length; i < len; i++) {
var user = users[i];
if (user.sub === id) {
log.info('Found user: ', user);
return fn(null, user);
}
}
return fn(null, null);
};
var oidcStrategy = new OIDCBearerStrategy(options,
function(token, done) {
log.info('verifying the user');
log.info(token, 'was the token retreived');
findById(token.sub, function(err, user) {
if (err) {
return done(err);
}
if (!user) {
// "Auto-registration"
log.info('User was added automatically as they were new. Their sub is: ', token.sub);
users.push(token);
owner = token.sub;
return done(null, token);
}
owner = token.sub;
return done(null, user, token);
});
}
);
passport.use(oidcStrategy);
```

Passport 會使用適用於它的所有策略 (Twitter、Facebook 等) 且所有策略寫入器都依循的類似模式。查看此策略，您會看見我們將它當成 function() 來傳遞，其中含有一個 token 和一個 done 做為參數。一旦策略完成所有工作之後，便會盡責地返回。當它完成之後，我們會想要儲存使用者並隱藏權杖，因此我們不需再次要求它。

> [AZURE.IMPORTANT]上述程式碼會讓所有使用者經歷伺服器的驗證。這就是所謂的自動註冊。在生產伺服器中，您想要讓所有人都必須先經歷您所決定的註冊過程。這通常是您在取用者 App 中看到的模式，可讓您向 Facebook 註冊，但接著會要求您填寫其他資訊。如果這不是命令列程式，我們就只能從傳回的權杖物件中擷取電子郵件，然後要求他們填寫其他資訊。由於這是測試伺服器，因此，我們只會將它們直接加入記憶體中的資料庫。

### 2\.最後，保護某些端點

您可以保護端點，方法是透過您想要使用的通訊協定來指定 passport.authenticate() 呼叫。

讓我們在伺服器程式碼中編輯路由，以執行更有趣的作業：

```Javascript
server.get('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), listTasks);
server.get('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), listTasks);
server.get('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), getTask);
server.head('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), getTask);
server.post('/tasks/:owner/:task', passport.authenticate('oidc-bearer', {
session: false
}), createTask);
server.post('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), createTask);
server.del('/tasks/:owner/:task', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), removeAll, function respond(req, res, next) {
res.send(204);
next();
});
```

## 18：再次執行伺服器應用程式，並確保它會拒絕您的要求

讓我們再次使用 `curl`，以查看我們現在是否有針對端點的 OAuth2 保護。我們會在針對這個端點執行任何用戶端 SDK 之前，執行此動作。傳回的標頭應該足以說明我們執行的作業步驟正確無誤。

首先，請確定 monogoDB 執行個體正在執行中...

	$sudo mongod

然後，變更目錄並啟動 curling...

	$ cd azuread
	$ node server.js

嘗試基本的 POST 方法：

`$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

```Shell
HTTP/1.1 401 Unauthorized
Connection: close
WWW-Authenticate: Bearer realm="Users"
Date: Tue, 14 Jul 2015 05:45:03 GMT
Transfer-Encoding: chunked
```

此時 401 是您期待的回應，其會指出 Passport 層正嘗試重新導向到授權端點，這也正是您想要的結果。


## 恭喜！ 您現在擁有一項使用 OAuth2 的 REST API 服務！

在未使用 OAuth2 相容用戶端的情況下，您已經儘可能地使用此伺服器的所有功能。您還必須完成其他逐步解說。

如果您只需有關如何使用 Restify 和 OAuth2 實作 REST API 的相關資訊，則您已經有足夠的程式碼可以繼續開發服務，並學習如何以此範例為基礎進行建置。

## 後續步驟

如需參考，[此處以 .zip 格式提供](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs/archive/complete.zip)完整範例 (不含您的設定值)，或者，您也可以從 GitHub 予以複製：

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs.git```

您現在可以進入更進階的主題。您可以嘗試：

[在 Node.js 中使用 v2.0 應用程式模型保護 Web APP >>](active-directory-v2-devquickstarts-node-web.md)

如需其他資源，請查看：- [應用程式模型 v2.0 預覽 >>](active-directory-appmodel-v2-overview.md) - [StackOverflow "azure-active directory" 標記 >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

<!---HONumber=August15_HO9-->