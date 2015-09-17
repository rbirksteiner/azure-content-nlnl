<properties
	pageTitle="開始使用 Azure AD NodeJS | Microsoft Azure"
	description="如何建立可整合 Azure AD 以進行驗證的 Node.js Web API。"
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
	ms.date="07/17/2015"
	ms.author="brandwe"/>

# 開始使用節點的 WEB API

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

本逐步解說將提供您一個快速又輕鬆的方式來設定 REST API 服務與 Azure Active Directory 的整合，以獲得使用 OAuth2 通訊協定的 API 保護。下載中所包含的範例伺服器是為了能在任何平台上 (但目標是 OSX 和 Linux) 執行所設計的。

在本逐步解說結束時，您應該能夠建置包含下列功能的執行中 REST API 伺服器：

* 搭配 JSON 執行 REST API 介面，且使用 MongoDB 作為永久儲存體的 node.js 伺服器
* 運用 OAuth2 API 保護，並搭配使用 Azure Active Directory 的承載者權杖的 REST API


我們已在 Apache 2.0 授權底下的 GitHub 中發行這個執行範例的原始程式碼，因此您可以隨意複製 (或更棒的是您可以分散出去！) 和提供意見反應及提取要求。

## 關於 Node.js 模組

我們將在本逐步解說中使用 Node.js 模組。模組是指可載入的 JavaScript 封裝，可為您的應用程式提供特定功能。您通常可以使用 NPM 安裝目錄中的 Node.js NPM 命令列工具來安裝模組，但核心 Node.js 封裝中已隨附了一些模組 (例如 HTTP 模組)。已安裝的模組會儲存在 Node.js 安裝目錄的根目錄下的 node\_modules 目錄。node\_modules 目錄下的每個模組都會維護它自己的 node\_modules 目錄 (其中包含它所依賴的任何模組)，且每個必要模組都會有一個 node\_modules 目錄。這個遞迴目錄結構表示相依性鏈結。

此相依性鏈結結構會導致較高的應用程式使用量，但它可以保證已符合所有相依性，而且用於開發的模組版本也會用於生產環境中。這可讓實際執行的應用程式行為更容易預測，並防止可能會影響使用者的版本控制問題。

## 步驟 1：註冊 Azure AD 租用戶

若要使用此範例，您需要 Azure Active Directory 租用戶。如果您不確定什麼是租用戶或如何取得租用戶，請參閱[如何取得 Azure AD 租用戶](active-directory-howto-tenant.md)。

## 步驟 2：將 Web API 加入至您的租用戶

取得您的 Azure Active Directory 租用戶之後，請將這個範例應用程式加入您的租用戶，您才可以用它來保護 API。

若要啟用應用程式來驗證使用者，您必須先要在您的租用戶中註冊這個新的應用程式。

- 登入 Azure 管理入口網站。
- 在左側導覽中按一下 **Active Directory**。
- 選取您要註冊應用程式的租用戶。
- 按一下 [**應用程式**] 索引標籤，然後按一下最下面抽屜的 [新增]。
- 遵照提示進行，並建立新的 **Web 應用程式和/或 WebAPI**。
    - 應用程式的 [**名稱**] 將對使用者說明您的應用程式
    -	[**登入 URL**] 是指應用程式的基底 URL。基本架構的預設值是 `https://localhost:8888`。
    - [**應用程式識別碼 URI**] 是指應用程式的唯一識別碼。慣例會使用 `https://<tenant-domain>/<app-name>`，例如：`https://contoso.onmicrosoft.com/my-first-aad-app`
- 完成註冊後，AAD 會為您的應用程式指派一個唯一用戶端識別碼。您在後續章節中將會用到這個值，所以請從 [設定] 索引標籤中複製此值。

## 步驟 3：下載適用於您平台的 node.js
若要成功使用此範例，您必須具備已成功安裝的 Node.js。

從 [http://nodejs.org](http://nodejs.org) 安裝 Node.js。

## 步驟 4：在您的平台上安裝 MongoDB

若要成功使用此範例，您必須具備已成功安裝的 MongoDB。我們將會使用 MongoDB 讓 REST API 得以在不同伺服器執行個體之間持續使用。

從 [http://mongodb.org](http://www.mongodb.org) 安裝 MongoDB。

**附註：**本逐步解說假設您會使用 MongoDB 的預設安裝和伺服器端點，在撰寫本文時為：mongodb://localhost


## 步驟 5：在您的 Web API 上安裝 Restify 模組

我們將會使用 Resitfy 來建置 REST API。Restify 是衍生自 Express 的最小且具彈性的 Node.js 應用程式架構，其中包含一組強大功能除了可用來建立連線外，還可以用來建置 REST API。

### 安裝 Restify

從命令列將目錄變更至 azuread 目錄。如果 **azuread** 目錄不存在，請予以建立。

`cd azuread - or- mkdir azuread; cd azuread`

輸入以下命令：

`npm install restify`

此命令會安裝 Restify。

#### 您有收到錯誤訊息嗎？

在某些作業系統上使用 npm 時，您可能會收到 [錯誤：EPERM, chmod '/usr/local/bin/..'] 的錯誤訊息，並收到以系統管理員身分執行該帳戶的要求。若發生這個情況，使用 sudo 命令以更高的權限層級執行 npm。

#### 您有收到有關 DTRACE 的錯誤訊息嗎？

安裝 Restify 時，您可能會看到類似下面的內容：

```Shell
clang: error: no such file or directory: 'HD/azuread/node_modules/restify/node_modules/dtrace-provider/libusdt'
make: *** [Release/DTraceProviderBindings.node] Error 1
gyp ERR! build error
gyp ERR! stack Error: `make` failed with exit code: 2
gyp ERR! stack     at ChildProcess.onExit (/usr/local/lib/node_modules/npm/node_modules/node-gyp/lib/build.js:267:23)
gyp ERR! stack     at ChildProcess.EventEmitter.emit (events.js:98:17)
gyp ERR! stack     at Process.ChildProcess._handle.onexit (child_process.js:789:12)
gyp ERR! System Darwin 13.1.0
gyp ERR! command "node" "/usr/local/lib/node_modules/npm/node_modules/node-gyp/bin/node-gyp.js" "rebuild"
gyp ERR! cwd /Volumes/Development HD/azuread/node_modules/restify/node_modules/dtrace-provider
gyp ERR! node -v v0.10.11
gyp ERR! node-gyp -v v0.10.0
gyp ERR! not ok
npm WARN optional dep failed, continuing dtrace-provider@0.2.8
```


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


## 步驟 6：在您的 Web API 上安裝 Passport.js

[Passport](http://passportjs.org/) 是 Node.js 的驗證中介軟體。您可以暗中將極具彈性且模組化的 Passport 放入任何 Express 或 Resitify Web 應用程式。一組完整的策略可支援使用使用者名稱和密碼、Facebook、Twitter 及其他等驗證。我們已為 Azure Active Directory 開發一個策略。我們將會安裝此模組，然後加入 Azure Active Directory 的策略外掛程式。

從命令列將目錄變更至 azuread 目錄。

輸入以下命令以安裝 passport.js

`npm install passport`

此命令的輸出應類似這樣：

	passport@0.1.17 node_modules\passport
	├── pause@0.0.1
	└── pkginfo@0.2.3

## 步驟 7：在您的 Web API 中加入 Passport.js 持有者權杖支援

接下來，我們要使用 passport-bearer-http ([Passport](http://passportjs.org/) 的 Bearner 處理常式) 來加入持有者策略。我們也會加入透過使用 node-jwt 支援的 JWT 權杖處理常式。

**附註：**雖然 OAuth2 提供可發行任何已知權杖類型的架構，但只有特定的權杖類型獲得普遍的使用。在保護端點中，這會是持有者權杖。持有者權杖是在 OAuth2 中最普遍發行的權杖類型，而且許多實作假設持有者權杖會是唯一發行的權杖類型。

從命令列將目錄變更至 **azuread** 目錄。

輸入下列命令以安裝 Passport.js 模組：

- `npm install passport-oauth`
- `npm install passport-http-bearer`
- `npm install node-jwt`

此命令的輸出應類似這樣：

	ms-passport-wsfed-saml2@0.3.8 node_modules\passport-oauth  
	├── xtend@2.0.3
	├── xml-crypto@0.0.9
	├── xmldom@0.1.13
	└── xml2js@0.1.14 (sax@0.5.2)


## 步驟 8：將 MongoDB 模組加入 Web API

我們將使用 MongoDB 作為資料存放區。基於這個理由，我們必須安裝兩個廣泛使用的外掛程式才能管理名為 Mongoose 的模型與結構描述，以及同樣稱為 MongoDB 的 MongoDB 的資料庫驅動程式。


* `npm install mongoose`
* `npm install mongodb`

## 步驟 9：安裝其他模組

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


## 步驟 10：使用您的相依性建立 server.js

server.js 檔案可提供我們 Web API 伺服器的大部分功能。我們將在此檔案中加入大部分的程式碼。基於生產目的，您會將功能重整成較小的檔案，例如單獨分開的路徑和控制器。基於此示範的目的，我們將在這項功能中使用 server.js。

從命令列將目錄變更至 **azuread** 資料夾 (如果您尚未在此目錄下)：

`cd azuread`

在偏好的編輯器中建立 `server.js` 檔案，並加入下列資訊：

```Javascript
	'use strict';

	/**
 	* Module dependencies.
 	*/

	var fs = require('fs');
	var path = require('path');
	var util = require('util');
	var assert = require('assert-plus');
	var bunyan = require('bunyan');
	var getopt = require('posix-getopt');
	var mongoose = require('mongoose/');
	var restify = require('restify');
```

儲存檔案。我們稍後會再回到此檔案。

## 步驟 11：建立可儲存您的 Azure AD 設定的組態檔

這個程式碼檔會將設定參數從您的 Azure Active Directory 入口網站傳遞到 Passport.js。您會在將 Web API 加入入口網站 (本逐步解說的第一個部分) 時建立這些設定值。在您完成複製程式碼之後，我們將說明要放入這些參數值的內容。


從命令列將目錄變更至 **azuread** 資料夾 (如果您尚未在此目錄下)：

`cd azuread`

在偏好的編輯器中建立 `config.js` 檔案，並加入下列資訊：

```Javascript
// Don't commit this file to your public repos
    exports.creds = {
    mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
    openid_configuration: 'https://login.microsoftonline.com/common/.well-known/openid-configuration', // For using Microsoft you should never need to change this.
    openid_keys: 'https://login.microsoftonline.com/common/discovery/keys', // For using Microsoft you should never need to change this. If absent will attempt to get from openid_configuration
}

```



**附註：**您可能永遠不需要變更這些值。

**附註：**我們會經常性地變更金鑰。請確定您總是從 "openid\_keys" URL 中進行提取，而且應用程式可以存取網際網路。


## 步驟 12：將設定加入 server.js 檔案

我們必須從您剛才跨應用程式建立的組態檔中讀取這些值。若要這樣做，我們只需在應用程式中將 .config 檔案作為必要資源加入，然後將全域變數設定為 config.js 文件中的那些值即可

從命令列將目錄變更至 **azuread** 資料夾 (如果您尚未在此目錄下)：

`cd azuread`

在偏好的編輯器中開啟 `server.js` 檔案，並加入下列資訊：

```Javascript
var config = require('./config');
```
然後，使用下列程式碼在 `server.js` 中加入新的區段：

```Javascript
/**
* Setup some configuration
*/
var mongoose = require('mongoose/');
var serverPort = process.env.PORT || 8888;
var serverURI = ( process.env.PORT ) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;

```
## 步驟 13：建立可協助剖析中繼資料/權杖的 metadata.js 協助程式檔案

由於目標是為了在 server.js 檔案中僅保留應用程式邏輯，因此將一些協助程式方法放在不同檔案中會是合理的作法。這些方法只會協助我們剖析 OpenID Connect 中繼資料，而且與核心案例無關。最好是將它們放到別處。隨著逐步進行本逐步解說，我們將會在此檔案中新增更多內容。

***附註：***您會注意到這個 metadata.js 檔案會在 SAML 和 WS-Fed 中剖析 XML，以及在 OpenID Connect 中剖析 JSON。這是預設的行為，您也將會在其他範例中使用這個檔案。您可以放心地將其忽略。

從命令列將目錄變更至 **azuread** 資料夾 (如果您尚未在此目錄下)：

`cd azuread`

在偏好的編輯器中建立 `metadata.js` 檔案，並加入下列資訊：

```Javascript

'use strict';

var xml2js = require('xml2js');
var request = require('request');
var aadutils = require('./aadutils');
var async = require('async');

// Logging

var bunyan = require('bunyan');
var log = bunyan.createLogger({name: 'Microsoft OpenID Connect Passport Strategy'});

var Metadata = function (url, authtype) {


  if(!url) {
    throw new Error("Metadata: url is a required argument");
  }
  if(!authtype) {
    throw new Error('OIDCBearerStrategy requires an authentication type specified to metadata parser. Valid types are saml, wsfed, or odic"');
  }

  this.url = url;
  this.metadata = null;
  this.authtype = authtype;
  log.info(authtype, 'Metadata requested for authentication type');
};

Object.defineProperty(Metadata, 'url', {
  get: function () {
    return this.url;
  }
});

Object.defineProperty(Metadata, 'saml', {
  get: function () {
    return this.saml;
  }
});

Object.defineProperty(Metadata, 'wsfed', {
  get: function () {
    return this.wsfed;
  }
});

Object.defineProperty(Metadata, 'oidc', {
  get: function () {
    return this.oidc;
  }
});


Object.defineProperty(Metadata, 'metadata', {
  get: function () {
    return this.metadata;
  }
});

Metadata.prototype.updateSamlMetadata = function(doc, next) {
  log.info('Request to update the SAML Metadata');
  try {

    this.saml = {};

    var entity = aadutils.getElement(doc, 'EntityDescriptor');
    var idp = aadutils.getElement(entity, 'IDPSSODescriptor');
    var signOn = aadutils.getElement(idp[0], 'SingleSignOnService');
    var signOff = aadutils.getElement(idp[0], 'SingleLogoutService');
    var keyDescriptor = aadutils.getElement(idp[0], 'KeyDescriptor');
    this.saml.loginEndpoint = signOn[0].$.Location;
    this.saml.logoutEndpoint = signOff[0].$.Location;

    // copy the x509 certs from the metadata
    this.saml.certs = [];
    for (var j=0;j<keyDescriptor.length;j++) {
      this.saml.certs.push(keyDescriptor[j].KeyInfo[0].X509Data[0].X509Certificate[0]);
    }
    next(null);
  } catch (e) {
    next(new Error('Invalid SAMLP Federation Metadata ' + e.message));
  }
};

Metadata.prototype.updateOidcMetadata = function(doc, next) {
  log.info('Request to update the Open ID Connect Metadata');
  try {
    this.oidc = {};

    var issuer = doc['issuer'];
    var keyDescriptor = aadutils.getElement(idp[0], 'keys');

    // copy the x509 certs from the metadata
    this.oidc.certs = [];
    for (var j=0;j<keyDescriptor.length;j++) {
      this.oidc.certs.push(keyDescriptor[j].KeyInfo[0].X509Data[0].X509Certificate[0]);
    }
    next(null);
  } catch (e) {
    next(new Error('Invalid Open ID Connect Federation Metadata ' + e.message));
  }
};


Metadata.prototype.updateWsfedMetadata = function(doc, next) {
  log.info('Request to update the WS Federation Metadata');
  try {
    this.wsfed = {};
    var entity = aadutils.getElement(doc, 'EntityDescriptor');
    var roles = aadutils.getElement(entity, 'RoleDescriptor');
    for(var i = 0; i < roles.length; i++) {
      var role = roles[i];
      if(role['fed:SecurityTokenServiceEndpoint']) {
        var endpoint = role['fed:SecurityTokenServiceEndpoint'];
        var endPointReference = aadutils.getFirstElement(endpoint[0],'EndpointReference');
        this.wsfed.loginEndpoint = aadutils.getFirstElement(endPointReference,'Address');

        var keyDescriptor = aadutils.getElement(role, 'KeyDescriptor');
        // copy the x509 certs from the metadata
        this.wsfed.certs = [];
        for (var j=0;j<keyDescriptor.length;j++) {
          this.wsfed.certs.push(keyDescriptor[j].KeyInfo[0].X509Data[0].X509Certificate[0]);
        }
        break;
      }
    }

    return next(null);
  } catch (e) {
    next(new Error('Invalid WSFED Federation Metadata ' + e.message));
  }
};

Metadata.prototype.fetch = function(callback) {
  var self = this;
  log.info("Fetching metadata from the provided metadata URL: " + self.url);
  async.waterfall([
    // fetch the Federation metadata for the AAD tenant
    function(next){
      request(self.url, function (err, response, body) {
        if(err) {
          next(err);
        } else if(response.statusCode !== 200) {
          next(new Error("Error:" + response.statusCode +  " Cannot get AAD Federation metadata from " + self.url));
        } else {
          log.info(body, "retreived");
          next(null, body);
        }
      });
    },
    function(body, next){
      // parse the AAD Federation metadata xml

      if(self.authtype == "saml" || self.authtype == "wsfed") {
      log.info(body, "Parsing XML retreived from the endpoint");
      var parser = new xml2js.Parser({explicitRoot:true});
      // Note: xml responses from Azure AAD have a leading \ufeff which breaks xml2js parser!
      parser.parseString(body.replace("\ufeff", ""), function (err, data) {
        self.metatdata = data;
        next(err);

      });
    } else if(self.authtype == "oidc") {
      log.info(body, "Parsing JSON retreived from the endpoint");
      JSON.parse(body, function (err, data) {
        self.metatdata = data;
        next(err);
      });

    } else {

       next(new Error("Error: No Authentication type specified to metadata parser. Valid types are saml, wsfed, or odic"));
    }

    },

    function(next){
      if(self.authtype = "saml") {
      // update the SAML SSO endpoints and certs from the metadata
      self.updateSamlMetadata(self.metatdata, next);
    }},
    function(next){
      if(self.authtype = "wsfed") {
      // update the SAML SSO endpoints and certs from the metadata
      self.updateWsfedMetadata(self.metatdata, next);
    }},
    function(next){
      if(self.authtype = "oidc") {
      self.updateOidcMetadata(self.metadata, next);
    }},
  ], function (err) {
    // return err or success (err === null) to callback
    callback(err);
  });
};

exports.Metadata = Metadata;
```
從程式碼中可以看到，它只接受您在 `config.js` 中傳遞的 openid URL，然後將它進行剖析，以取得我們要在 `server.js` 檔案中使用的資訊。我們非常歡迎您調查這段程式碼，並視需要加以開發。

### 在您的 server.js 中載入 metadata.js 檔案

我們必須告訴伺服器哪裡可以找到您剛才撰寫的方法。

從命令列將目錄變更至 **azuread** 資料夾 (如果您尚未在此目錄下)：

`cd azuread`

在偏好的編輯器中開啟 `server.js` 檔案，並加入下列資訊：

```Javascript
var metadata = require('./metadata);
```
接下來，將這個呼叫加入 `Configuration` 區段的結尾處，以便將 `config.js` 中的中繼資料文件傳送到我們剛剛撰寫的剖析器：

```Javascript
this.aadutils = new var Metadata = require('./metadata').Metadata;
```

## 步驟 14：使用 Moongoose 新增 MongoDB 模型和結構描述資訊

現在，當我們將這三個檔案整合一起提供給 REST API 服務時，您便會開始看到所有準備工作的成效。

在此逐步解說中，我們將使用 MongoDB 來儲存工作，如***步驟 4*** 中所述。

如果您還記得我們在***步驟 11*** 中建立的 `config.js` 檔案，我們會呼叫資料庫 `tasklist`，因為那是我們放在 mogoose\_auth\_local 連線 URL 結尾處的內容。您不需要在 MongoDB 中事先建立此資料庫，它會在您第一次執行伺服器應用程式時為您建立 (假設此資料庫不存在)。

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
/**
*
* Connect to MongoDB
*/

global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;  
```
這會連線到 MongoDB 伺服器，並將結構描述物件傳回給我們。

#### 使用這個結構描述，在程式碼中建立模型

以下是您之前撰寫的程式碼，請加入下列程式碼：

```Javascript
/**
/ Here we create a schema to store our tasks. Pretty simple schema for now.
*/

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

## 步驟 15：在工作 REST API 伺服器中新增路由

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
 * APIs
 */

function createTask(req, res, next) {

	// Resitify currently has a bug which doesn't allow you to set default headers
  	// This headers comply with CORS and allow us to mongodbServer our response to any origin

  res.header("Access-Control-Allow-Origin", "*");
  res.header("Access-Control-Allow-Headers", "X-Requested-With");

    // Create a new task model, fill it up and save it to Mongodb
  var _task = new Task();

        if (!req.params.task) {
                req.log.warn('createTask: missing task');
                next(new MissingTaskError());
                return;
        }


  _task.owner = req.params.owner;
   _task.task = req.params.task;
   _task.date = new Date();

  _task.save(function (err) {
  	if (err) {
        req.log.warn(err, 'createTask: unable to save');
        next(err);
    } else {
    res.send(201, _task);

			}
  });

  return next();

}


/**
 * Deletes a Task by name
 */
function removeTask(req, res, next) {

        Task.remove( { task:req.params.task }, function (err) {
                if (err) {
                        req.log.warn(err,
                                     'removeTask: unable to delete %s',
                                     req.params.task);
                        next(err);
                } else {
                        res.send(204);
                        next();
                }
        });
}

/**
 * Deletes all Tasks. A wipe
 */
function removeAll(req, res, next) {
        Task.remove();
        res.send(204);
        return next();
}    });
}


/**
 *
 *
 *
 */
function getTask(req, res, next) {


        Task.find(req.params.name, function (err, data) {
                if (err) {
                        req.log.warn(err, 'get: unable to read %s', req.params.name);
                        next(err);
                        return;
                }

                res.json(data);
        });

        return next();
}


/**
 * Simple returns the list of TODOs that were loaded.
 *
 */

function listTasks(req, res, next) {
  // Resitify currently has a bug which doesn't allow you to set default headers
  // This headers comply with CORS and allow us to mongodbServer our response to any origin

  res.header("Access-Control-Allow-Origin", "*");
  res.header("Access-Control-Allow-Headers", "X-Requested-With");

  console.log("server getTasks");

  Task.find().limit(20).sort('date').exec(function (err,data) {

    if (err)
      return next(err);

    if (data.length > 0) {
            console.log(data);
        }

    if (!data.length) {
            console.log('there was a problem');
            console.log(err);
            console.log("There is no tasks in the database. Did you initalize the database as stated in the README?");
        }

    else {

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


function TaskExistsError(name) {
        assert.string(name, 'name');

        restify.RestError.call(this, {
                statusCode: 409,
                restCode: 'TaskExists',
                message: name + ' already exists',
                constructorOpt: TaskExistsError
        });

        this.name = 'TaskExistsError';
}
util.inherits(TaskExistsError, restify.RestError);


function TaskNotFoundError(name) {
        assert.string(name, 'name');

        restify.RestError.call(this, {
                statusCode: 404,
                restCode: 'TaskNotFound',
                message: name + ' was not found',
                constructorOpt: TaskNotFoundError
        });

        this.name = 'TaskNotFoundError';
}

util.inherits(TaskNotFoundError, restify.RestError);
```


## 步驟 16：建立伺服器！

我們已經定義好資料庫，也準備好路由，要做的最後一件事是加入將會管理呼叫的伺服器執行個體。

Restify (及 Express) 有很多進階自訂項目可讓您在 REST API 伺服器上執行，但再重申一次，基於本文的目的，我們將使用最基本的設定。

```Javascript
/**
 * Our Server
 */


var server = restify.createServer({
        name: "Azure Active Directroy TODO Server",
    version: "1.0.0",
    formatters: {
        'application/json': function(req, res, body){
            if(req.params.callback){
                var callbackFunctionName = req.params.callback.replace(/[^A-Za-z0-9_\.]/g, '');
                return callbackFunctionName + "(" + JSON.stringify(body) + ");";
            } else {
                return JSON.stringify(body);
            }
        },
        'text/html': function(req, res, body){
            if (body instanceof Error)
                        return body.stack;

                      if (Buffer.isBuffer(body))
                        return body.toString('base64');

                return util.inspect(body);
        },
        'application/x-www-form-urlencoded': function(req, res, body){
            if (body instanceof Error) {
                    res.statusCode = body.statusCode || 500;
                    body = body.message;
            } else if (typeof (body) === 'object') {
                body = body.task || JSON.stringify(body);
            } else {
                body = body.toString();
            }

        res.setHeader('Content-Length', Buffer.byteLength(body));
        return (body);
        }
    }
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

        // This lets us push JSON to the REST API endpoint as well. Maps x: y as /name:value

        server.use(restify.bodyParser({ mapParams: false }));

        /// Now the real handlers. Here we just CRUD

        server.get('/tasks', listTasks);
        server.head('/tasks', listTasks);
        server.get('/tasks/:name', getTask);
        server.head('/tasks/:name', getTask);
        server.post('/tasks/:name/:task', createTask);
        server.del('/tasks/:name/:task', removeTask);
        server.del('/tasks/:name', removeTask);
        server.del('/tasks', removeAll, function respond(req, res, next) { res.send(204); next(); });


        // Register a default '/' handler

        server.get('/', function root(req, res, next) {
                var routes = [
                        'GET     /',
                        'POST    /tasks/:name/:task',
                        'GET     /tasks',
                        'PUT     /tasks/:name',
                        'GET     /tasks/:name',
                        'DELETE  /tasks/:name/:task'
                ];
                res.send(200, routes);
                next();
        });

  server.listen(serverPort, function() {

  var consoleMessage = '\n Azure Active Directory Tutorial'
  consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++'
  consoleMessage += '\n %s server is listening at %s';
  consoleMessage += '\n Open your browser to %s/tasks\n';
  consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n'
  consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n'
  consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n'  

  console.log(consoleMessage, server.name, server.url, server.url, server.url);

});
```

## 步驟 17：加入 OAuth 支援之前，請先執行伺服器。

在繼續執行 OAuth 部分的逐步解說之前，最好先確定沒有任何問題。

若要這樣做的最簡單方法是使用命令列中的 `curl`。在執行此動作之前，我們需要一個可讓我們將輸出剖析為 JSON 的簡單公用程式。若要這樣做，請安裝 [json](https://github.com/trentm/json) 工具，以供下面所有範例使用。

	$npm install -g jsontool

這會全域安裝 JSON 工具。既然我們已經完成此動作，我們現在可以開始使用伺服器：

首先，請確定 monogoDB 執行個體正在執行中...

	$sudo mongod

然後，變更目錄並啟動 curling...

	$ cd azuread
	$ node server.js

	$ curl -isS http://127.0.0.1:8888 | json
	HTTP/1.1 200 OK
	Connection: close
	Content-Type: application/x-www-form-urlencoded
	Content-Length: 145
	Date: Wed, 29 Jan 2014 03:41:24 GMT

	[
  	"GET     /",
  	"POST    /tasks/:owner/:task",
  	"GET     /tasks",
  	"DELETE  /tasks",
  	"PUT     /tasks/:owner",
  	"GET     /tasks/:owner",
  	"DELETE  /tasks/:task"
	]

接著，我們可以透過以下方式新增工作：

	$ curl -isS -X POST http://127.0.0.1:8888/tasks/brandon/Hello

回應應為：

	HTTP/1.1 201 Created
	Connection: close
	Access-Control-Allow-Origin: *
	Access-Control-Allow-Headers: X-Requested-With
	Content-Type: application/x-www-form-urlencoded
	Content-Length: 5
	Date: Tue, 04 Feb 2014 01:02:26 GMT

	Hello

而且我們可以透過以下方式列出 Brandon 的工作：

	$ curl -isS http://127.0.0.1:8888/tasks/brandon/

如果所有項目都沒問題，我們便可以開始將 OAuth 加入 REST API 伺服器。

## 步驟 18：將 Passport.js 程式碼加入 REST API 伺服器

既然我們已經擁有執行中的 REST API (順道恭喜您！)，我們可以開始讓它在 Azure AD 中發揮其價值。

從命令列將目錄變更至 **azuread** 資料夾 (如果您尚未在此目錄下)：

`cd azuread`

### 步驟 1：新增 Passport 模組

在偏好的編輯器中開啟 `server.js` 檔案，並在您先前陳述要載入模組的位置下面加入下列資訊：這個位置很接近檔案頂端，應該是緊接在 `var aadutils = require('./aadutils');` 匯入後面的位置。

```Javascript
/*
*
* Load our old friend Passport for OAuth2 flows
*/

var passport = require('passport')
  , OAuth2Strategy = require('passport-oauth').OAuth2Strategy;
```

### 2\.告訴伺服器我們正在使用驗證

在偏好的編輯器中開啟 `server.js` 檔案，並在您先前定義路由的 **server.get() 下方**，但在 **server.listen()** 方法上方加入下列資訊：


我們必須告知 Restify 開始使用其 `authorizationParser()`並查看授權標頭的內容。

```Javascript
        server.use(restify.authorizationParser());


```


### 3\.將 Passport OAuth2 模組加入程式碼

在此我們使用加入 config.js 檔案的特定 OAuth2 參數。如果 `aadutils.js` 檔案執行其剖析同盟中繼資料文件的工作，則即使這些值在 config.js 檔案中是空白值，系統仍會替我們填入這些值。

```Javascript
// Now our own handlers for authentication/authorization
// Here we only use Oauth2 from Passport.js

passport.use('provider', new OAuth2Strategy({
    authorizationURL: authEndpoint,
    tokenURL: tokenEndpoint,
    clientID: clientID,
    clientSecret: clientSecret,
    callbackURL: callbackURL
  },
  function(accessToken, refreshToken, profile, done) {
    User.findOrCreate({ UserId: profile.id }, function(err, user) {
      done(err, user);
    });
  }
));

// Let's start using Passport.js

server.use(passport.initialize());

```
### 步驟 4：在 OAuth 驗證中新增路由

```Javascript
// Redirect the user to the OAuth 2.0 provider for authentication.  When
// complete, the provider will redirect the user back to the application at
//     /auth/provider/callback

server.get('/auth/provider', passport.authenticate('provider'));

// The OAuth 2.0 provider has redirected the user back to the application.
// Finish the authentication process by attempting to obtain an access
// token.  If authorization was granted, the user will be logged in.
// Otherwise, authentication has failed.

server.get('/auth/provider/callback',
  passport.authenticate('provider', { successRedirect: '/',
                                      failureRedirect: '/login' }));
```

### 步驟 5：在路由中加入 IsAuthenticated() 協助程式方法

```Javascript
// Simple route middleware to ensure user is authenticated.
//   Use this route middleware on any resource that needs to be protected.  If
//   the request is authenticated (typically via a persistent login session),
//   the request will proceed.  Otherwise, the user will be redirected to the
//   login page.

var ensureAuthenticated = function(req, res, next) {
  if (req.isAuthenticated()) {
    return next();
  }
  res.redirect('/login');
};

```

### 步驟 6：新增 Cookie 的快取機制

```Javascript
// Passport session setup.
//   To support persistent login sessions, Passport needs to be able to
//   serialize users into and deserialize users out of the session.  Typically,
//   this will be as simple as storing the user ID when serializing, and finding
//   the user by ID when deserializing.
passport.serializeUser(function(user, done) {
  done(null, user.email);
});

passport.deserializeUser(function(id, done) {
  findByEmail(id, function (err, user) {
    done(err, user);
  });
});
```
### 步驟 7：最後，保護某些端點

您可以保護端點，方法是透過您想要使用的通訊協定來指定 passport.authenticate() 呼叫。

讓我們編輯伺服端程式碼中的路由，以便執行更有趣的作業：

```Javascript
server.get('/tasks', passport.authenticate('provider', { session: false }), listTasks);
```


## 步驟 19：再次執行應用程式伺服器，並確保它會拒絕您的要求

讓我們再次使用 `curl`，以查看我們現在是否有針對端點的 OAuth2 保護。我們會在針對這個端點執行任何用戶端 SDK 之前，執行此動作。傳回的標頭應該足以說明我們執行的作業步驟正確無誤。

首先，請確定 monogoDB 執行個體正在執行中...

	$sudo mongod

然後，變更目錄並啟動 curling...

	$ cd azuread
	$ node server.js

嘗試基本的 GET 方法：

	$ curl -isS http://127.0.0.1:8888/tasks/
	HTTP/1.1 302 Moved Temporarily
	Connection: close
	Location: https://login.windows.net/468a75f4-f9a7-4dc4-a527-4f4522734790/oauth2/authorize?response_type=code&redirect_uri=&client_id=123
	Content-Length: 0
	Date: Tue, 04 Feb 2014 02:15:14 GMT


此時 302 會是您期待的回應，指出 Passport 層嘗試重新導向至授權的端點，這也正是您想要的結果。

## 恭喜！ 您現在擁有一項使用 OAuth2 的 REST API 服務！

在未使用 OAuth2 相容用戶端的情況下，您已經儘可能地使用此伺服器的所有功能。您還必須完成其他逐步解說。

如果您只需有關如何使用 Restify 和 OAuth2 實作 REST API 的相關資訊，則您已經有足夠的程式碼可以繼續開發服務，並學習如何以此範例為基礎進行建置。

如果您對執行 ADAL 的後續步驟感興趣，以下是一些我們建議的支援 ADAL 用戶端，可供您繼續處理：

您只需複製到您的開發人員機器，並如本逐步解說所述進行設定即可。

[ADAL for iOS](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios)

[ADAL for Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android)

[ADAL for .Net](http://msdn.microsoft.com/library/windowsazure/jj573266.aspx)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
 

<!---HONumber=August15_HO6-->