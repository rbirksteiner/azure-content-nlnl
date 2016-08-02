<properties
    pageTitle="Voorbeeld van het toevoegen van aanmelding aan een Node.js-web-app voor Azure B2C | Microsoft Azure"
    description="Een Node.js-web-app maken waarmee gebruikers worden aangemeld via een B2C-tenant."
    services="active-directory-b2c"
    documentationCenter=""
    authors="brandwe"
    manager="msmbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
  ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="hero-article"
    ms.date="05/31/2016"
    ms.author="brandwe"/>


# B2C-voorbeeld: aanmelden toevoegen aan een Node.js-web-app

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

**Passport** is verificatiemiddleware voor Node.js. Passport is zeer flexibel en modulair en kan onopvallend worden geïnstalleerd in een Express- of Restify-webtoepassing. Een uitgebreide set strategieën ondersteunt verificatie met een gebruikersnaam en wachtwoord, Facebook, Twitter en meer. 

> [AZURE.NOTE] In dit artikel wordt niet beschreven hoe u aanmelding, registratie en profielbeheer implementeert met behulp van Azure Active Directory B2C. Dit artikel gaat over het aanroepen van web-API's nadat de gebruiker is geverifieerd. Als u dit nog niet hebt gedaan, leest u eerst de [zelfstudie Aan de slag met .NET-web-app](active-directory-b2c-devquickstarts-web-dotnet.md) voor meer informatie over de basisprincipes van Azure AD B2C.

We hebben een strategie ontwikkeld voor Azure Active Directory (Azure AD). U installeert deze module en voegt de vervolgens de `passport-azure-ad`-invoegtoepassing van Azure AD toe.

Hiervoor doet u het volgende:

1. U registreert een toepassing met Azure AD.
2. U stelt de app in op het gebruik van de `passport-azure-ad`-invoegtoepassing.
3. U gebruikt Passport om aan- en afmeldingsaanvragen te verzenden naar Azure AD.
4. U drukt de gebruikersgegevens af.

De code voor deze zelfstudie [wordt bewaard in GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS). Als u het voorbeeld wilt uitvoeren, kunt u [de basis van de app downloaden als zip-bestand](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip). U kunt de basis ook klonen:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS.git```

De voltooide toepassing wordt verstrekt aan het einde van deze zelfstudie.

> [AZURE.WARNING]   Voor dit B2C-voorbeeld moet u dezelfde **client-id**/**toepassings-id** en beleidsregels gebruiken voor de web-API-taakserver en de client die hiermee verbinding maakt. Dit geldt ook voor de zelfstudies voor iOS en Android. Als u al eerder een toepassing hebt gemaakt in een van deze snelstartgidsen, gebruikt u deze waarden. Maak geen nieuwe waarden.

## Een Azure AD B2C-directory maken

Voordat u Azure AD B2C kunt gebruiken, moet u een directory, of tenant, maken.  Een directory is een container voor alle gebruikers, apps, groepen en meer. Als u nog geen directory hebt, [maakt u een B2C-directory](active-directory-b2c-get-started.md) voordat u doorgaat in deze handleiding.

## Een app maken

Vervolgens maakt u een app in uw B2C-directory. Hiermee geeft u informatie door aan Azure AD die nodig is om veilig te communiceren met uw app. De client-app en web-API worden aangegeven met één **toepassings-id** omdat deze beide één logische app vormen. Volg [deze instructies](active-directory-b2c-app-registration.md) om een app te maken. Zorg ervoor dat:

- U een **web-app**/**web-API** in de toepassing opneemt.
- U `http://localhost/TodoListService` invoert als **antwoord-URL**. Dit is de standaard-URL voor dit codevoorbeeld.
- U een **toepassingsgeheim** maakt voor uw toepassing en dit kopieert. U hebt dit later nodig. Merk op dat deze waarde [een escape-teken voor XML](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) moet bevatten voordat u deze kunt gebruiken.
- U de **toepassings-id** kopieert die is toegewezen aan uw app. Deze hebt u ook later nodig.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Het beleid maken

In Azure AD B2C wordt elke gebruikerservaring gedefinieerd door een [beleid](active-directory-b2c-reference-policies.md). Deze app bevat drie identiteitservaringen: registreren, aanmelden en aanmelden via Facebook. U moet één beleid maken voor elk type, zoals wordt beschreven in het [naslagartikel voor beleid](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Wanneer u uw drie beleidsregels maakt:

- Kiest u **Weergavenaam** en andere registratiekenmerken in het registratiebeleid.
- Kiest u **Weergavenaam**- en **Object-id**-toepassingsclaims voor elk beleid. U kunt ook andere claims kiezen.
- Kopieert u de **naam** van elk beleid nadat u dit hebt gemaakt. Deze moet het voorvoegsel `b2c_1_` bevatten.  U hebt deze beleidsnamen later nodig.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Nadat u de drie beleidsregels hebt gemaakt, kunt u uw app maken.

In dit artikel wordt niet beschreven hoe u de gemaakte beleidsregels gebruikt. Voor meer informatie over de werking van beleid in Azure AD B2C, leest u eerst de [zelfstudie Aan de slag met .NET-web-app](active-directory-b2c-devquickstarts-web-dotnet.md).

## Vereisten aan de directory toevoegen

Wijzig vanaf de opdrachtregel de directory's in de hoofdmap, als u hier nog niet bent. Voer de volgende opdrachten uit:

- `npm install express`
- `npm install ejs`
- `npm install ejs-locals`
- `npm install restify`
- `npm install mongoose`
- `npm install bunyan`
- `npm install assert-plus`
- `npm install passport`
- `npm install webfinger`
- `npm install body-parser`
- `npm install express-session`
- `npm install cookie-parser`

Daarnaast hebben we `passport-azure-ad` gebruikt voor het voorbeeld in de basis van de snelstartgids.

- `npm install passport-azure-ad`

Hiermee worden de bibliotheken geïnstalleerd waarvan `passport-azure-ad` afhankelijk is.

## Uw app instellen voor gebruik van de strategie Passport-Node.js
Configureer de Express-middleware voor gebruik van het OpenID Connect-verificatieprotocol. Passport wordt onder andere gebruikt om aan- en afmeldingsaanvragen te verzenden, gebruikerssessies te beheren en informatie over gebruikers op te halen.

Open het bestand `config.js` in de hoofdmap van het project en geef de configuratiewaarden van de app op in de sectie `exports.creds`.
- `clientID`: de **toepassings-id** die is toegewezen aan uw app in de registratieportal.
- `returnURL`: de **omleidings-URI** die u hebt ingevoerd in de portal.
- `tenantName`: de tenantnaam van de app, bijvoorbeeld **contoso.onmicrosoft.com**.

[AZURE.INCLUDE [active-directory-b2c-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Open het bestand `app.js` in de hoofdmap van het project. Voeg de volgende aanroep toe om de `OIDCStrategy`-strategie aan te roepen die beschikbaar is in `passport-azure-ad`.


```JavaScript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

// Add some logging
var log = bunyan.createLogger({
    name: 'Microsoft OIDC Example Web Application'
});
```

Gebruik de strategie waarnaar u net hebt verwezen om aanmeldingsaanvragen te verwerken.

```JavaScript
// Use the OIDCStrategy in Passport (Section 2).
//
//   Strategies in Passport require a "validate" function that accepts
//   credentials (in this case, an OpenID identifier), and invokes a callback
//   by using a user object.
passport.use(new OIDCStrategy({
    callbackURL: config.creds.returnURL,
    realm: config.creds.realm,
    clientID: config.creds.clientID,
    oidcIssuer: config.creds.issuer,
    identityMetadata: config.creds.identityMetadata,
    skipUserProfile: config.creds.skipUserProfile,
    responseType: config.creds.responseType,
    responseMode: config.creds.responseMode,
    tenantName: config.creds.tenantName
  },
  function(iss, sub, profile, accessToken, refreshToken, done) {
    log.info('Example: Email address we received was: ', profile.email);
    // asynchronous verification, for effect...
    process.nextTick(function () {
      findByEmail(profile.email, function(err, user) {
        if (err) {
          return done(err);
        }
        if (!user) {
          // "Auto-registration"
          users.push(profile);
          return done(null, profile);
        }
        return done(null, user);
      });
    });
  }
));
```
In Passport wordt een vergelijkbaar patroon gebruikt voor alle strategieën (inclusief Twitter en Facebook). Alle schrijvers van strategieën gebruiken dit patroon. Wanneer u de strategie bekijkt, ziet u dat u een `function()` met een token hieraan doorgeeft en `done` doorgeeft als parameters. De strategie komt weer naar u terug nadat al het werk is uitgevoerd. Sla de gebruiker en het token op, zodat u hier niet opnieuw naar hoeft te vragen.

> [AZURE.IMPORTANT]
De voorafgaande code geldt voor alle gebruikers die worden geverifieerd door de server. Dit is automatische registratie. Wanneer u productieservers gebruikt, wilt u gebruikers alleen toelaten als ze een registratieproces hebben doorlopen dat u hebt ingesteld. U ziet dit patroon vaak in consumenten-apps. U kunt u hiermee registreren met Facebook, maar vervolgens wordt u om aanvullende informatie gevraagd. Als de toepassing geen voorbeeld zou zijn, zouden we een e-mailadres kunnen extraheren uit het tokenobject dat wordt geretourneerd en vervolgens de gebruiker vragen om aanvullende informatie in te vullen. Omdat dit een testserver is, voegen we gebruikers toe aan de database in het geheugen.

Voeg de methoden toe waarmee u gebruikers kunt bijhouden die zich hebben aangemeld, zoals wordt vereist door Passport. Dit omvat het serialiseren en deserialiseren van gebruikersgegevens:

```JavaScript

// Passport session setup. (Section 2)

//   To support persistent sign-in sessions, Passport needs to be able to
//   serialize users into and deserialize users out of sessions. Typically,
//   this is as simple as storing the user ID when Passport serializes a user
//   and finding the user by ID when Passport deserializes that user.
passport.serializeUser(function(user, done) {
  done(null, user.email);
});

passport.deserializeUser(function(id, done) {
  findByEmail(id, function (err, user) {
    done(err, user);
  });
});

// Array to hold users who have signed in
var users = [];

var findByEmail = function(email, fn) {
  for (var i = 0, len = users.length; i < len; i++) {
    var user = users[i];
    log.info('we are using user: ', user);
    if (user.email === email) {
      return fn(null, user);
    }
  }
  return fn(null, null);
};

```

Voeg de code voor het laden van de Express-engine toe. Hierna kunt u zien dat we het standaardpatroon voor `/views` en `/routes` van Express gebruiken.

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
  // Initialize Passport!  Also use passport.session() middleware to support
  // persistent sign-in sessions (recommended).
  app.use(passport.initialize());
  app.use(passport.session());
  app.use(app.router);
  app.use(express.static(__dirname + '/../../public'));
});

```

Voeg de `POST`-routes toe waarmee de daadwerkelijke aanmeldingsaanvragen worden afgeleverd bij de `passport-azure-ad`-engine:

```JavaScript

// Our Auth routes (Section 3)

// GET /auth/openid
//   Use passport.authenticate() as route middleware to authenticate the
//   request. The first step in OpenID authentication involves redirecting
//   the user to an OpenID provider. After the user is authenticated,
//   the OpenID provider redirects the user back to this application at
//   /auth/openid/return

app.get('/auth/openid',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Authentication was called in the Sample');
    res.redirect('/');
  });

// GET /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request. If authentication fails, the user will be redirected back to the
//   sign-in page. Otherwise, the primary route function will be called.
//   In this example, it redirects the user to the home page.
app.get('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {

    res.redirect('/');
  });

// POST /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request. If authentication fails, the user will be redirected back to the
//   sign-in page. Otherwise, the primary route function will be called.
//   In this example, it will redirect the user to the home page.

app.post('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {

    res.redirect('/');
  });
```

## Passport gebruiken om aan- en afmeldingsaanvragen te verzenden naar Azure AD

Uw app is nu geconfigureerd voor communicatie met het v2.0-eindpunt met behulp van het OpenID Connect-verificatieprotocol. `passport-azure-ad` heeft gezorgd voor de details van het samenstellen van verificatieberichten, het valideren van tokens van Azure AD en het beheren van gebruikerssessies. U hoeft alleen nog ervoor te zorgen dat uw gebruikers zich kunnen aan- en afmelden en aanvullende informatie te verzamelen over gebruikers die zich hebben aangemeld.

Voeg eerst de standaardmethode en de aanmeldings-, account- en afmeldingsmethoden toe aan uw `app.js`-bestand:

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

Meer informatie over deze methoden:
- Met de route `/` wordt u omgeleid naar de weergave `index.ejs` door de gebruiker in de aanvraag door te geven (indien aanwezig).
- Met de route `/account` wordt eerst gecontroleerd of u bent geverifieerd (de implementatie hiervoor vindt u hieronder). Vervolgens wordt de gebruiker in de aanvraag doorgegeven, zodat u aanvullende informatie over de gebruiker kunt ophalen.
- Met de route `/login` wordt de `azuread-openidconnect`-verificator aangeroepen vanuit `passport-azure-ad`. Als dit niet lukt, wordt de gebruiker omgeleid naar `/login`.
- `/logout` roept `logout.ejs` aan (en de bijbehorende route). Hiermee worden cookies gewist en keert de gebruiker terug naar `index.ejs`.


Voor het laatste deel van `app.js` voegt u de `EnsureAuthenticated`-methode toe die wordt gebruikt in de route `/account`.

```JavaScript

// Simple route middleware to ensure that the user is authenticated. (Section 4)

//   Use this route middleware on any resource that needs to be protected. If
//   the request is authenticated (typically via a persistent sign-in session),
//   then the request will proceed. Otherwise, the user will be redirected to the
//   sign-in page.
function ensureAuthenticated(req, res, next) {
  if (req.isAuthenticated()) { return next(); }
  res.redirect('/login')
}

```

Maak ten slotte de server zelf in `app.js`.

```JavaScript

app.listen(3000);

```


## De weergaven en routes in Express maken om uw beleid aan te roepen

Uw `app.js` is nu voltooid. U hoeft alleen nog de routes en weergaven toe te voegen waarmee u het aanmeldings- en registratiebeleid kunt aanroepen. Hiermee worden ook de routes voor `/logout` en `/login` verwerkt die u hebt gemaakt.

Maak de route `/routes/index.js` onder de hoofddirectory.

```JavaScript

/*
 * GET home page.
 */

exports.index = function(req, res){
  res.render('index', { title: 'Express' });
};
```

Maak de route `/routes/user.js` onder de hoofddirectory.

```JavaScript

/*
 * GET users listing.
 */

exports.list = function(req, res){
  res.send("respond with a resource");
};
```

Met deze eenvoudige routes worden aanvragen doorgegeven aan uw weergaven. Deze omvatten de gebruiker, indien aanwezig.

Maak de weergave `/views/index.ejs` onder de hoofddirectory. Dit is een eenvoudige pagina waarmee beleid voor aanmelden en afmelden wordt aangeroepen. U kunt dit ook gebruiken om accountgegevens op te halen. U kunt de voorwaardelijke `if (!user)` gebruiken als de gebruiker wordt doorgegeven in de aanvraag om te bewijzen dat deze is aangemeld.

```JavaScript
<% if (!user) { %>
    <h2>Welcome! Please sign in.</h2>
    <a href="/login/?p=your facebook policy">Sign in with Facebook</a>
    <a href="/login/?p=your email sign-in policy">Sign in with email</a>
    <a href="/login/?p=your email sign-up policy">Sign up with email</a>
<% } else { %>
    <h2>Hello, <%= user.displayName %>.</h2>
    <a href="/account">Account info</a></br>
    <a href="/logout">Log out</a>
<% } %>
```

Maak de weergave `/views/account.ejs` onder de hoofddirectory, zodat u aanvullende informatie kunt weergeven die `passport-azure-ad` in de gebruikersaanvraag heeft geplaatst.

```Javascript
<% if (!user) { %>
    <h2>Welcome! Please sign in.</h2>
    <a href="/login">Sign in</a>
<% } else { %>
<p>displayName: <%= user.displayName %></p>
<p>givenName: <%= user.name.givenName %></p>
<p>familyName: <%= user.name.familyName %></p>
<p>UPN: <%= user._json.upn %></p>
<p>Profile ID: <%= user.id %></p>
<p>Full Claims</p>
<%- JSON.stringify(user) %>
<p></p>
<a href="/logout">Log Out</a>
<% } %>
```

U kunt nu uw app bouwen en uitvoeren.

Voer `node app.js` uit en navigeer naar `http://localhost:3000`


Registreer u of meld u aan bij de app via e-mail of Facebook. Meld u af en meld u weer aan als een andere gebruiker.

##Volgende stappen

Voor naslag is het voltooide voorbeeld (zonder uw configuratiewaarden) [geleverd als zip-bestand](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/complete.zip). U kunt dit ook klonen van GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-nodejs.git```

U kunt nu verder met geavanceerdere onderwerpen. U kunt het volgende proberen:

[Een web-API beveiligen met het B2C-model in Node.js](active-directory-b2c-devquickstarts-api-node.md)

<!--

For additional resources, check out:
You can now move on to more advanced B2C topics. You might try:

[Call a Node.js web API from a Node.js web app]()

[Customizing the your B2C App's UX >>]()

-->



<!--HONumber=Jun16_HO2-->


