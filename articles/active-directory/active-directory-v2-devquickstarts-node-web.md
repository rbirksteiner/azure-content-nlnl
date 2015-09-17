<properties
	pageTitle="應用程式模型 v2.0 | Microsoft Azure"
	description="如何建置可使用個人 Microsoft 帳戶及工作或學校帳戶登入使用者的 Node JS Web 應用程式。"
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

# 應用程式模型 v2.0 預覽：將登入加入 nodeJS Web App


  >[AZURE.NOTE]此資訊適用於 v2.0 應用程式模型公開預覽。如需如何與正式運作之 Azure AD 服務整合的相關指示，請參閱 [Azure Active Directory 開發人員指南](active-directory-developers-guide.md)。


我們將在此處使用 Passport 來執行下列動作：

- 讓使用者使用 Azure AD 和 v2.0 App 模型來登入 App。
- 顯示一些使用者的相關資訊。
- 讓使用者登出 App。

**Passport** 是 Node.js 的驗證中介軟體。您可以暗中將極具彈性且模組化的 Passport 放入任何 Express 或 Resitify Web 應用程式。一組完整的策略可支援使用使用者名稱和密碼、Facebook、Twitter 及其他等驗證。我們已為 Microsoft Azure Active Directory 開發一項策略。我們將安裝此模組，然後加入 Microsoft Azure Active Directory `passport-azure-ad` 外掛程式。

為執行此作業，您必須執行下列動作：

1. 註冊應用程式。
2. 設定您的 App 以使用 Passport-azure-ad 策略。
3. 使用 Passport，向 Azure AD 發出登入和登出要求。
4. 列印出使用者的相關資料。

本教學課程的程式碼保留在 [GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs) 上。若要遵循執行，您可以[利用 .zip 格式下載 App 的基本架構](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/skeleton.zip)，或複製基本架構：

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

The completed application is provided at the end of this tutorial as well.

## 1. Register an App
Create a new app at [apps.dev.microsoft.com](https://apps.dev.microsoft.com), or follow these [detailed steps](active-directory-v2-app-registration.md).  Make sure to:

- Copy down the **Application Id** assigned to your app, you'll need it soon.
- Add the **Web** platform for your app.
- Enter the correct **Redirect URI**. The redirect URI indicates to Azure AD where authentication responses should be directed - the default for this tutorial is `http://localhost:3000/auth/openid/return`.

## 2. Add pre-requisities to your directory

From the command-line, change directories to your root folder if not already there and run the following commands:

- `npm install express`
- `npm install ejs`
- `npm install ejs-locals`
- `npm install restify`
- `npm install mongoose`
- `npm install bunyan`
- `npm install assert-plus`
- `npm install passport`

- In addition, we've included a special `passport-azure-ad` for our Preview in the skeleton of the quickstart. You'll need to install the pre-requisites in there as well.

- `cd /lib/passport-azure-ad`
- `npm install`

This will install the libraries that passport-azure-ad depend on.

## 3. Set up your app to use the passport-node-js strategy
Here, we'll configure the Express middleware to use the OpenID Connect authentication protocol.  Passport will be used to issue sign-in and sign-out requests, manage the user's session, and get information about the user, amongst other things.

-	To begin, open the `config.js` file in the root of the project, and enter your app's configuration values in the `exports.creds` section.
    -	The `clientID:` is the **Application Id** assigned to your app in the registration portal.
    -	The `returnURL` is the **Redirect URI** you entered in the portal.
    - The `clientSecret` is the secret you generated in the portal
    - The `realm` is the **Redirect URI** you entered in the portal without the route. (example: http//localhost:3000)
    - The `issuer` is where you append the **Application Id** after, as an example: https://sts.windows.net/96702724-991f-4576-bc90-be9862749ac5/

- Next open `app.js` file in the root of the proejct and add the follwing call to invoke the `OIDStrategy` strategy that comes with `passport-azure-ad`


```JavaScript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy; ```

- 之後，請使用我們僅供參考的策略來處理登入要求

```JavaScript
// Use the OIDCStrategy within Passport. (Section 2) 

//   Strategies in passport require a `validate` function, which accept
//   credentials (in this case, an OpenID identifier), and invoke a callback
//   with a user object.
passport.use(new OIDCStrategy({
    callbackURL: config.creds.returnURL,
    realm: config.creds.realm,
    clientID: config.creds.clientID,
    clientSecret: config.creds.clientSecret,
    oidcIssuer: config.creds.issuer,
    identityMetadata: config.creds.identityMetadata,
    skipUserProfile: true // doesn't fetch user profile
  },
  function(iss, sub, email, claims, profile, accessToken, refreshToken, done) {
    log.info('We received claims of: ', claims);
    log.info('Example: Email address we received was: ', claims.preferred_username);
    // asynchronous verification, for effect...
    process.nextTick(function () {
      findByEmail(claims.preferred_username, function(err, user) {
        if (err) {
          return done(err);
        }
        if (!user) {
          // "Auto-registration"
          users.push(claims);
          return done(null, claims);
        }
        return done(null, user);
      });
    });
  }
));
```
Passport 會使用適用於它的所有策略 (Twitter、Facebook 等) 且所有策略寫入器都依循的類似模式。查看此策略，您會看見我們將它當成 function() 來傳遞，其中含有一個 token 和一個 done 做為參數。一旦策略完成所有工作之後，便會盡責地返回。當它完成之後，我們會想要儲存使用者並隱藏權杖，因此我們不需再次要求它。

> [AZURE.IMPORTANT]上述程式碼會讓所有使用者經歷伺服器的驗證。這就是所謂的自動註冊。在生產伺服器中，您想要讓所有人都必須先經歷您所決定的註冊過程。這通常是您在取用者 App 中看到的模式，可讓您向 Facebook 註冊，但接著會要求您填寫其他資訊。如果這不是範例應用程式，我們就只能從傳回的權杖物件中擷取電子郵件，然後要求他們填寫其他資訊。由於這是測試伺服器，因此，我們只會將它們直接加入記憶體中的資料庫。

- 接下來，我們會新增方法，在 Passport 提出要求時，允許我們持續追蹤已登入的使用者。這包括將使用者資訊序列化和還原序列化：

```JavaScript

// Passport session setup. (Section 2)

//   To support persistent login sessions, Passport needs to be able to
//   serialize users into and deserialize users out of the session.  Typically,
//   this will be as simple as storing the user ID when serializing, and finding
//   the user by ID when deserializing.
passport.serializeUser(function(user, done) {
  done(null, user.preferred_username);
});

passport.deserializeUser(function(id, done) {
  findByEmail(id, function (err, user) {
    done(err, user);
  });
});

// array to hold logged in users
var users = [];

var findByEmail = function(email, fn) {
  for (var i = 0, len = users.length; i < len; i++) {
    var user = users[i];
    if (user.preferred_username === email) {
      return fn(null, user);
    }
  }
  return fn(null, null);
};

```

- 接下來，加入可載入 express 引擎的程式碼。您會在此處看到我們使用 Express 所提供的預設 /views 和 /routes 模式。

```JavaScript

// configure Express (Section 2)

var app = express();


app.configure(function() {
  app.set('views', __dirname + '/views');
  app.set('view engine', 'ejs');
  app.use(express.logger());
  app.use(express.methodOverride());
  app.use(cookieParser());
  app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
  app.use(bodyParser.urlencoded({ extended : true }));
  // Initialize Passport!  Also use passport.session() middleware, to support
  // persistent login sessions (recommended).
  app.use(passport.initialize());
  app.use(passport.session());
  app.use(app.router);
  app.use(express.static(__dirname + '/../../public'));
});

```

- 最後，加入 POST 路由，這會將實際的登入要求遞交到 `passport-azure-ad` 引擎：

```JavaScript

// Our POST routes (Section 3)

// POST /auth/openid
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  The first step in OpenID authentication will involve redirecting
//   the user to their OpenID provider.  After authenticating, the OpenID
//   provider will redirect the user back to this application at
//   /auth/openid/return
app.post('/auth/openid',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Authenitcation was called in the Sample');
    res.redirect('/');
  });

// GET /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  If authentication fails, the user will be redirected back to the
//   login page.  Otherwise, the primary route function function will be called,
//   which, in this example, will redirect the user to the home page.
app.get('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('We received a return from AzureAD.');
    res.redirect('/');
  });

app.get('/logout', function(req, res){
  req.logout();
  res.redirect('/');
});
```

## 4\.使用 Passport，向 Azure AD 發出登入和登出要求

您的 App 現在已正確設定，將使用 OpenID Connect 驗證通訊協定來與 v2.0 端點進行通訊。`passport-azure-ad` 已經處理所有製作驗證訊息、驗證 Azure AD 的權杖和維護使用者工作階段的瑣碎詳細資料。所有剩餘的部分就是為使用者提供一種方式來登入、登出，以及收集關於已登入使用者的其他資訊。

- 首先，將預設、登入、帳戶及登出方法加入 `app.js` 檔案：

```JavaScript

//Routes (Section 4)

app.get('/', function(req, res){
  res.render('index', { user: req.user });
});

app.get('/account', ensureAuthenticated, function(req, res){
  res.render('account', { user: req.user });
});

app.get('/login', 
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Login was called in the Sample');
    res.redirect('/');
});

app.get('/logout', function(req, res){
  req.logout();
  res.redirect('/');
});

```

-	讓我們詳細檢閱這些方法：
    -	`/` 路由將重新導向到 index.ejs 檢視，其會在要求中傳遞使用者 (如果有的話)
    - `/account` 路由將先***確保我們已通過驗證*** (我們將在下面實作)，然後在要求中傳遞使用者，讓我們能夠取得關於該使用者的其他資訊。
    - `/login` 路由將從 `passport-azuread` 呼叫 azuread-openidconnect 驗證器，如果失敗，即會再次將使用者重新導向到 /login
    - `/logout` 會直接呼叫 logout.ejs (以及路由)，其會清除 cookie，然後讓使用者返回 index.ejs


- 針對 `app.js` 的最後一個部分，加入可在上述 `/account` 中使用的 EnsureAuthenticated 方法。

```JavaScript

// Simple route middleware to ensure user is authenticated. (Section 4)
//   Use this route middleware on any resource that needs to be protected.  If
//   the request is authenticated (typically via a persistent login session),
//   the request will proceed.  Otherwise, the user will be redirected to the
//   login page.
function ensureAuthenticated(req, res, next) {
  if (req.isAuthenticated()) { return next(); }
  res.redirect('/login')
}
```

- 最後，在 `app.js` 中實際建立伺服器本身：

```JavaScript

app.listen(3000);

```


## 5\.快速建立檢視與路由以在網站中顯示使用者

我們已完成 `app.js`。現在只需新增路由和檢視即可，這兩者將會向使用者顯示我們取得的資訊，以及控制我們建立的 `/logout` 和 `/login` 路由。

- 在根目錄底下建立 `/routes/index.js` 路由。

```JavaScript
/*
 * GET home page.
 */

exports.index = function(req, res){
  res.render('index', { title: 'Express' });
};
```

- 在根目錄底下建立 `/routes/user.js` 路由

```JavaScript
/*
 * GET users listing.
 */

exports.list = function(req, res){
  res.send("respond with a resource");
};
```

這些簡單路由只會將要求傳遞到我們的檢視，包括使用者 (如果有的話)。

- 在根目錄底下建立 `/views/index.ejs` 檢視。這是簡單網頁，將呼叫我們的登入和登出方法，且讓我們能夠抓取帳戶資訊。請注意，若透過要求傳遞的使用者已證實我們擁有已登入的使用者，就能使用條件式 `if (!user)`。

```JavaScript
<% if (!user) { %>
	<h2>Welcome! Please log in.</h2>
	<a href="/login">Log In</a>
<% } else { %>
	<h2>Hello, <%= user.name %>.</h2>
	<a href="/account">Account Info</a></br>
	<a href="/logout">Log Out</a>
<% } %>
```

- 在根目錄底下建立 `/views/account.ejs` 檢視，如此一來，就能檢視 `passport-azuread` 放置於使用者要求中的其他資料。

```Javascript
<% if (!user) { %>
	<h2>Welcome! Please log in.</h2>
	<a href="/login">Log In</a>
<% } else { %>
<p>displayName: <%= user.name %></p>
<p>Profile ID: <%= user.sub %></p>
<a href="/logout">Log Out</a>
<% } %>
```

- 最後，可藉由新增版面配置，使它看起來很美觀。在根目錄底下建立 '/views/layout.ejs' 檢視

```JavaScript

<!DOCTYPE html>
<html>
	<head>
		<title>Passport-OpenID Example</title>
	</head>
	<body>
		<% if (!user) { %>
			<p>
			<a href="/">Home</a> | 
			<a href="/login">Log In</a>
			</p>
		<% } else { %>
			<p>
			<a href="/">Home</a> | 
			<a href="/account">Account</a> | 
			<a href="/logout">Log Out</a>
			</p>
		<% } %>
		<%- body %>
	</body>
</html>
```

最後，建置並執行您的應用程式！

執行 `node app.js` 並瀏覽到 `http://localhost:3000`


使用個人的 Microsoft 帳戶或工作或學校帳戶登入，並注意 /account 清單中使用者身分識別的反映狀態。您的 Web 應用程式現在使用業界標準的通訊協定保護，可以使用個人與工作/學校帳戶來驗證使用者。

##後續步驟

如需參考，[此處以 .zip 格式提供](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/complete.zip)完整範例 (不含您的設定值)，或者，您也可以從 GitHub 予以複製：

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

您現在可以進入更進階的主題。您可以嘗試：

[在 node.js 中使用 v2.0 應用程式模型保護 Web API >>](active-directory-v2-devquickstarts-webapi-nodejs.md)

如需其他資源，請查看：- [應用程式模型 v2.0 預覽 >>](active-directory-appmodel-v2-overview.md) - [StackOverflow "azure-active directory" 標記 >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

<!---HONumber=August15_HO9-->