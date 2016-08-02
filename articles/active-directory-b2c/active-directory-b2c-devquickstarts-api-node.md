<properties
    pageTitle="B2C Preview: Een web-API beveiligen met behulp van Node.js | Microsoft Azure"
    description="Een Node.js web-API ontwikkelen die tokens accepteert van een B2C-tenant"
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

# B2C Preview: Een web-API ontwikkelen met behulp van Node.js

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]


> [AZURE.NOTE] In dit artikel wordt niet beschreven hoe u aanmelding, registratie en profielbeheer implementeert met behulp van Azure AD B2C. Dit artikel gaat over het aanroepen van web-API's nadat de gebruiker is geverifieerd. Lees eerst de [zelfstudie Aan de slag met .NET-web-app](active-directory-b2c-devquickstarts-web-dotnet.md) voor meer informatie over de basisprincipes van Azure Active Directory B2C, als u dat nog niet hebt gedaan.


> [AZURE.NOTE]  Dit voorbeeld is bedoeld voor gebruik met de [iOS B2C-voorbeeldtoepassing](active-directory-b2c-devquickstarts-ios.md). Voer eerst deze procedure uit en volg daarna dat voorbeeld.

**Passport** is verificatiemiddleware voor Node.js. Passport is zeer flexibel en modulair, en kan onopvallend worden geïnstalleerd in een Express- of Restify-webtoepassing. Een uitgebreide set strategieën ondersteunt verificatie met een gebruikersnaam en wachtwoord, Facebook, Twitter en meer. We hebben een strategie ontwikkeld voor Azure Active Directory (Azure AD). U installeert deze module en voegt vervolgens de `passport-azure-ad`-invoegtoepassing van Azure AD toe.

Hiervoor moet u het volgende doen:

1. U registreert een toepassing met Azure AD.
2. U stelt de app in voor het gebruik van de Passport-invoegtoepassing `azure-ad-passport`.
3. U configureert een client om de web-API 'takenlijst' aan te roepen.

De code voor deze zelfstudie [wordt bewaard in GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs). Als u het voorbeeld wilt uitvoeren, kunt u [de basis van de app downloaden als zip-bestand](https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs/archive/skeleton.zip) of het geraamte kopiëren:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs.git```

De voltooide toepassing wordt verstrekt aan het einde van deze zelfstudie.

> [AZURE.WARNING]   Voor dit B2C-voorbeeld moet u dezelfde **client-id**/**toepassings-id** en beleidsregels gebruiken voor de web-API-taakserver en voor de client die hiermee verbinding maakt. Dit geldt ook voor de zelfstudies voor iOS en Android. Als u al eerder een toepassing hebt gemaakt in een van deze snelstartgidsen, gebruikt u deze waarden. Maak geen nieuwe waarden.


## Een Azure AD B2C-directory maken

Voordat u Azure AD B2C kunt gebruiken, moet u een directory, of tenant, maken.  Een directory is een container voor alle gebruikers, apps, groepen en meer.  Als u nog geen directory hebt, [maakt u een B2C-directory](active-directory-b2c-get-started.md) voordat u verdergaat.

## Een app maken

Vervolgens moet u in de B2C-directory een app maken die Azure AD de informatie geeft die nodig is om veilig te communiceren met uw app. In dit geval worden zowel de client-app als de web-API aangegeven met één **toepassings-id** omdat ze samen één logische app vormen. Volg [deze instructies](active-directory-b2c-app-registration.md) om een app te maken. Zorg ervoor dat:

- U een **web-app of web-API** in de toepassing opneemt.
- U `http://localhost/TodoListService` invoert als **antwoord-URL**. Dit is de standaard-URL voor dit codevoorbeeld.
- U een **toepassingsgeheim** maakt voor uw toepassing en dit kopieert. U hebt dit later nodig. U hebt dit zo direct nodig. Deze waarde moet [een escape-teken voor XML](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) bevatten voordat u deze kunt gebruiken.
- U de **toepassings-id** kopieert die is toegewezen aan uw app. Deze hebt u ook later nodig.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Het beleid maken

In Azure AD B2C wordt elke gebruikerservaring gedefinieerd door [beleid](active-directory-b2c-reference-policies.md). Deze app bevat drie identiteitservaringen: registreren, aanmelden en aanmelden met Facebook. U moet één beleidsregel maken voor elk type, zoals wordt beschreven in het [naslagartikel voor beleid](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy).  Wanneer u uw drie beleidsregels maakt:

- Kiest u **Weergavenaam** en andere registratiekenmerken in het registratiebeleid.
- Kiest u **Weergavenaam**- en **Object-id**-toepassingsclaims voor elk beleid.  U kunt ook andere claims kiezen.
- Noteert u de **naam** van elk beleid nadat u dit hebt gemaakt. Deze moet het voorvoegsel `b2c_1_` bevatten.  U hebt deze beleidsnamen later nodig.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Nadat u de drie beleidsregels hebt gemaakt, kunt u uw app maken.

In dit artikel wordt niet beschreven hoe u de zojuist gemaakte beleidsregels gebruikt. Voor meer informatie over de werking van beleid in Azure AD B2C, leest u eerst de [zelfstudie Aan de slag met .NET-web-app](active-directory-b2c-devquickstarts-web-dotnet.md).

## Node.js voor uw platform downloaden

U moet een werkende implementatie van Node.js hebben om dit voorbeeld te kunnen gebruiken.

Installeer Node.js vanuit [nodejs.org](http://nodejs.org).

## MongoDB installeren voor uw platform

U moet ook een werkende implementatie van MongoDB hebben om dit voorbeeld te kunnen gebruiken. U gebruikt MongoDB om uw REST-API persistent te maken in alle serverexemplaren.

Installeer MongoDB vanuit [mongodb.org](http://www.mongodb.org).

> [AZURE.NOTE] In deze procedure wordt ervan uitgegaan dat u gebruikmaakt van de standaardinstallatie- en -servereindpunten voor MongoDB. Op het moment waarop dit artikel is geschreven, zijn dat `mongodb://localhost`.

## De Restify-modules installeren in uw web-API

U gebruikt Restify om de REST-API te ontwikkelen. Restify is een minimaal en flexibel Node.js-toepassingsframework dat is afgeleid van Express. Het bevat een set krachtige functies voor het ontwikkelen van REST-API's op Connect.

### Restify installeren

Wijzig de directory vanaf de opdrachtregel in `azuread`. Als de directory `azuread` niet bestaat, maakt u deze.

`cd azuread` of `mkdir azuread;`

Voer de volgende opdracht in:

`npm install restify`

Met deze opdracht wordt Restify geïnstalleerd.

#### Krijgt u een foutmelding?

Wanneer u `npm` in sommige besturingssystemen gebruikt, wordt het foutbericht `Error: EPERM, chmod '/usr/local/bin/..'` weergegeven, plus het verzoek om het account uit te voeren als beheerder. In dat geval gebruikt u de opdracht `sudo` om `npm` uit te voeren op een hoger niveau van bevoegdheden.

#### Wordt er een DTrace-fout weergegeven?

Misschien wordt er iets in de trant van het volgende weergegeven wanneer u Restify installeert:

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

Restify biedt een krachtig mechanisme om REST-aanroepen te traceren met behulp van DTrace. Veel besturingssystemen beschikken echter niet over DTrace. U kunt deze fouten negeren.

De uitvoer van de opdracht ziet er ongeveer als volgt uit:

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

## Passport installeren in uw web-API

[Passport](http://passportjs.org/) is verificatiemiddleware voor Node.js. Passport is zeer flexibel en modulair, en kan onopvallend worden geïnstalleerd in een Express- of Restify-webtoepassing. Een uitgebreide set strategieën ondersteunt verificatie met een gebruikersnaam en wachtwoord, Facebook, Twitter en meer. We hebben een strategie ontwikkeld voor Azure AD. U installeert deze module en voegt vervolgens de strategie-invoegtoepassing van Azure AD toe.

Wijzig de directory vanaf de opdrachtregel in `azuread`, als dat nog niet is gebeurd.

Voer de volgende opdracht in om Passport te installeren:

`npm install passport`

De uitvoer van de opdracht ziet er ongeveer als volgt uit:

    passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3

## Passport-azuread toevoegen aan uw web-API

Voeg vervolgens de OAuth-strategie toe met behulp van `passport-azuread`. Dit is een reeks strategieën die Azure AD verbinden met Passport. Gebruik deze strategie voor bearer-tokens in het REST-API-voorbeeld.

> [AZURE.NOTE] Hoewel OAuth2 een kader biedt waarin elk onbekend type token kan worden uitgegeven, worden alleen bepaalde typen tokens wijdverbreid gebruikt. De tokens voor het beveiligen van eindpunten zijn bearer-tokens. Dit is het type token dat het meest wordt uitgegeven in OAuth2. In veel implementaties wordt ervan uitgegaan dat bearer-tokens het enige type token zijn dat wordt uitgegeven.

Wijzig de directory vanaf de opdrachtregel in `azuread`, als dat nog niet is gebeurd.

Voer de volgende opdracht in om de Passport-module `passport-azure-ad` te installeren:

`npm install passport-azure-ad`

De uitvoer van de opdracht ziet er ongeveer als volgt uit:

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

## MongoDB-modules toevoegen aan uw web-API

U gebruikt MongoDB als uw gegevensarchief. Daarom moet u zowel Mongoose, een veelgebruikte invoegtoepassing voor het beheren van modellen en schema's, als het databasestuurprogramma voor MongoDB, ook MongoDB genoemd, installeren.

* `npm install mongoose`
* `npm install mongodb`

## Aanvullende modules installeren

Vervolgens installeert u de overige vereiste modules.

Wijzig de directory vanaf de opdrachtregel in `azuread`, als dat nog niet is gebeurd:

`cd azuread`

Voer de volgende opdrachten in om de modules in de directory `node_modules` te installeren:

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


## Een bestand server.js met de afhankelijkheden maken

Het bestand `server.js` verstrekt het merendeel van de functionaliteit voor uw Web-API-server. U gaat de meeste code toevoegen aan dit bestand. Voor productiedoeleinden moet u de functionaliteit in kleinere bestanden opsplitsen, zoals afzonderlijke routes en controllers. In deze zelfstudie gebruiken we server.js voor deze functionaliteit.

Wijzig de directory vanaf de opdrachtregel in `azuread`, als dat nog niet is gebeurd:

`cd azuread`

Maak een bestand `server.js` in een teksteditor. Voeg de volgende informatie toe:

```Javascript
'use strict';
/**
* Module dependencies.
*/
var fs = require('fs');
var path = require('path');
var util = require('util');
var assert = require('assert-plus');
var mongoose = require('mongoose/');
var bunyan = require('bunyan');
var restify = require('restify');
var config = require('./config');
var passport = require('passport');
var OIDCBearerStrategy = require('passport-azure-ad').BearerStrategy;
```

Sla het bestand op. U hebt dit bestand later nodig.

## Een bestand config.js maken om de Azure AD-instellingen op te slaan

Dit codebestand geeft de configuratieparameters van de Azure AD Portal door aan het bestand `Passport.js`. U hebt deze configuratiewaarden gemaakt toen u de web-API aan de portal toevoegde in het eerste deel van de procedure. Wanneer u de code hebt gekopieerd, wordt uitgelegd wat u in de waarden van deze parameters moet zetten.

Wijzig de directory vanaf de opdrachtregel in `azuread`, als dat nog niet is gebeurd:

`cd azuread`

Maak een bestand `config.js` in een teksteditor. Voeg de volgende informatie toe:

```Javascript
// Don't commit this file to your public repos. This config is for first-run
exports.creds = {
mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
audience: '<your audience URI>',
identityMetadata: 'https://login.microsoftonline.com/common/.well-known/openid-configuration', // For using Microsoft you should never need to change this.
tenantName:'<tenant name>',
policyName:'b2c_1_<sign in policy name>',
};

```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

### Vereiste waarden

`IdentityMetadata`: dit is de locatie waar `passport-azure-ad` de configuratiegegevens voor de id-provider zoekt. Hier wordt ook gezocht naar de sleutels om de JSON-webtokens te valideren. Als u Azure AD gebruikt, hoeft u deze waarde waarschijnlijk niet te wijzigen.

`audience`: de uniform resource identifier (URI) van de portal die de service identificeert. In ons voorbeeld wordt `http://localhost/TodoListService` gebruikt.

`tenantName`: de tenantnaam, bijvoorbeeld **contoso.onmicrosoft.com**.

`policyName`: het beleid waarmee u de tokens wilt valideren die bij de server binnenkomen. Dit moet hetzelfde beleid zijn als het beleid dat u op de clienttoepassing gebruikt om u aan te melden.

> [AZURE.NOTE] Gebruik voor deze B2C-preview hetzelfde beleid in de client- en serverconfiguratie. Als u al een procedure hebt doorlopen waarin u deze beleidsregels hebt gemaakt, hoeft u dit niet opnieuw te doen. Omdat u de procedure hebt voltooid, hoeft u op de site geen nieuw beleid voor clientprocedures te maken.

## Configuratie toevoegen aan het bestand server.js

U moet de waarden uit het bestand `config.js` dat u zojuist hebt gemaakt, inlezen in uw toepassing. Daartoe voegt u het bestand `.config` als een vereiste bron in uw toepassing toe en stelt u de globale variabelen in op de variabelen in het document `config.js`.

Wijzig de directory vanaf de opdrachtregel in `azuread`, als dat nog niet is gebeurd:

`cd azuread`

Open het bestand `server.js` in een teksteditor. Voeg de volgende informatie toe:

```Javascript
var config = require('./config');
```
Voeg een nieuwe sectie aan `server.js` toe die de volgende code bevat:

```Javascript
// We pass these options in to the ODICBearerStrategy.
var options = {
// The URL of the metadata document for your app. We will put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
    identityMetadata: config.creds.identityMetadata,
    clientID: config.creds.clientID,
    tenantName: config.creds.tenantName,
    policyName: config.creds.policyName,
    validateIssuer: config.creds.validateIssuer,
    audience: config.creds.audience
};
// array to hold logged-in users and the current logged-in user (owner)
var users = [];
var owner = null;
// Our logger
var log = bunyan.createLogger({
name: 'Microsoft Azure Active Directory Sample'
});
```

## Het MongoDB-model en de schemagegevens toevoegen met behulp van Mongoose

De eerdere voorbereiding loont de moeite wanneer u deze drie bestanden samenbrengt in een REST-API-service.

Voor deze procedure gebruikt u MongoDB om uw taken op te slaan, zoals eerder is besproken.

In het bestand `config.js` dat u hebt gemaakt, hebt u de **takenlijst** van uw database aangeroepen. Dit is ook wat u aan het einde van de verbindings-URL van `mongoose_auth_local` hebt geplaatst. U hoeft deze database niet vooraf in MongoDB te maken. Als de database nog niet bestaat, wordt deze voor u gemaakt wanneer u de servertoepassing voor de eerste keer uitvoert.

Nadat u de server hebt laten weten welke MongoDB-database u wilt gebruiken, moet u aanvullende code schrijven om het model en het schema voor de servertaken te maken.

### Het model uitbreiden

Dit schemamodel is zeer eenvoudig. U kunt het naar behoefte uitbreiden.

`name`: de persoon die aan de taak is toegewezen. Dit is een **tekenreeks**.

`task`: de taak zelf. Dit is een **tekenreeks**.

`date`: de datum waarop de taak moet zijn voltooid. Dit is een **datum/tijd**.

`completed`: of de taak is voltooid of niet. Dit is een **booleaanse waarde**.

### Het schema in de code maken

Wijzig de directory vanaf de opdrachtregel in `azuread`, als dat nog niet is gebeurd:

`cd azuread`

Open het bestand `server.js` in een teksteditor. Voeg de volgende informatie toe onder het configuratie-item:

```Javascript
// MongoDB setup
// Set up some configuration
var serverPort = process.env.PORT || 8080;
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');
```
Hiermee wordt verbinding gemaakt met de MongoDB-server en wordt een schemaobject geretourneerd.

### Het schema gebruiken om het model in de code te maken

Voeg de volgende code toe onder de code die u hierboven hebt geschreven:

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
Eerst maakt u het schema en vervolgens maakt u een modelobject dat u gebruikt om uw gegevens in de code op te slaan wanneer u de **routes** definieert.

## Routes toevoegen voor de REST-API-taakserver

Nu u een databasemodel hebt om mee te werken, voegt u de routes toe die u voor de REST-API-server wilt toevoegen.

### Routes in Restify

Routes werken in Restify op precies dezelfde manier als wanneer ze de Express-stack gebruiken. U definieert routes met behulp van de URI die de clienttoepassingen aanroepen. In de meeste gevallen definieert u uw routes in een afzonderlijk bestand. Voor deze zelfstudie plaatst u de routes in het bestand `server.js`. Voor productiegebruik is het raadzaam om deze in een eigen bestand te houden.

Een doorsnee patroon voor een Restify-route is:

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

Dit is het patroon op het meest eenvoudige niveau. U kunt Restify en Express gebruiken voor een veel diepere functionaliteit, zoals voor het definiëren van toepassingstypen en de uitvoering van complexe routering tussen verschillende eindpunten. Voor deze zelfstudie houden we de routes eenvoudig.

#### Standaardroutes toevoegen aan een server

Nu gaat u de basis-CRUD-routes voor **maken**, **ophalen**, **bijwerken** en **verwijderen** toevoegen.

Wijzig de directory vanaf de opdrachtregel in `azuread`, als dat nog niet is gebeurd:

`cd azuread`

Open het bestand `server.js` in een teksteditor. Voeg de volgende informatie toe onder de databasevermeldingen die u hierboven hebt gemaakt:

```Javascript
/**
*
* APIs for our REST task server
*/
// Create a task
function createTask(req, res, next) {
// Restify currently has a bug that doesn't allow you to set default headers
// The headers comply with CORS and allow us to mongodbServer our response to any origin
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
// Restify currently has a bug that doesn't allow you to set default headers
// The headers comply with CORS and allow us to mongodbServer our response to any origin
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

#### Foutafhandeling voor de routes toevoegen

U moet een vorm van foutafhandeling toevoegen, zodat u eventuele problemen op een begrijpelijke manier kunt terugkoppelen naar de client.

Voeg de volgende code toe onder de code die u hierboven hebt geschreven:

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


## De server maken

U hebt nu een database gedefinieerd en de routes geconfigureerd. Nu hoeft u alleen nog het serverexemplaar toe te voegen waarmee uw aanroepen worden beheerd.

Met Restify en Express kunt u de REST-API-server in grote mate aanpassen, maar hier gebruikt u de meest eenvoudige configuratie.

```Javascript
/**
* Our server
*/
var server = restify.createServer({
name: "Microsoft Azure Active Directory TODO Server",
version: "2.0.1"
});
// Ensure that we don't drop data on uploads
server.pre(restify.pre.pause());
// Clean up sloppy paths like //todo//////1//
server.pre(restify.pre.sanitizePath());
// Handle annoying user agents (curl)
server.pre(restify.pre.userAgentConnection());
// Set a per request bunyan logger (with requestid filled in)
server.use(restify.requestLogger());
// Allow five requests/second by IP, and burst to 10
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
## De routes toevoegen aan de server (zonder verificatie)

```Javascript
/// Now the real handlers. Here we just CRUD
/**
/*
/* Each of these handlers is protected by our OIDCBearerStrategy by invoking 'oidc-bearer'
/* in the passport.authenticate() method. We set 'session: false' as REST is stateless and
/* we don't need to maintain session state. You can experiment with removing API protection
/* by removing the passport.authenticate() method like this:
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
## De server uitvoeren voordat u OAuth-ondersteuning toevoegt

Voordat u verificatie toevoegt, moet u de server testen.

De eenvoudigste manier om dit te doen, is door `curl` in een opdrachtregel te gebruiken. Voordat u dat doet, hebt u echter een eenvoudig hulpprogramma nodig waarmee u uitvoer kunt parseren als JSON. Installeer eerst het JSON-hulpprogramma.

`$npm install -g jsontool`

Hiermee wordt het JSON-hulpprogramma op alle vereiste locaties geïnstalleerd. Test de server nadat u het JSON-hulpprogramma hebt geïnstalleerd:

Zorg ervoor dat het MongoDB-exemplaar is geactiveerd.

`$sudo mongodb`

Schakel naar de directory `azuread` en gebruik `curl`.

`$ cd azuread`
`$ node server.js`

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

Voeg een taak toe:

`$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

Het antwoord moet zijn:

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
Ga als volgt te werk als u taken voor de gebruiker 'Brandon' wilt weergeven:

`$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

Als dit werkt, kunt u OAuth toevoegen aan de REST-API-server.

U hebt een REST-API-server met MongoDB.

## Verificatie toevoegen aan een REST-API-app

Nu u een actieve REST-API-server hebt, kunt u deze gaan gebruiken met Azure AD.

Wijzig de directory vanaf de opdrachtregel in `azuread`, als dat nog niet is gebeurd:

`cd azuread`

### De OIDCBearerStrategy gebruiken die is opgenomen in passport-azure-ad

Tot nu toe hebt u een typische REST-ToDo-server gemaakt zonder enige vorm van autorisatie. U kunt nu de autorisatie gaan samenstellen.

Eerst moet u aangeven dat u Passport wilt gebruiken. Voeg dit direct onder de andere serverconfiguratie toe:

```Javascript
// Let's start using Passport.js

server.use(passport.initialize()); // Starts Passport
server.use(passport.session()); // Provides session support
```

> [AZURE.TIP]
Wanneer u API's schrijft, moet u de gegevens altijd koppelen aan iets unieks in het token, iets dat de gebruiker niet kan vervalsen. Wanneer de server ToDo-items opslaat, gebeurt dit op basis van de **Object-id** van de gebruiker in het token (aangeroepen via token.oid); deze komt in het eigenaarsveld te staan. Op die manier heeft alleen de gebruiker toegang tot zijn of haar ToDo-items. In de API wordt de 'eigenaar' niet weergegeven. Dat betekent dat een externe gebruiker ToDo-items van anderen kan aanvragen, ook al zijn ze geverifieerd.

Vervolgens gebruikt u de bearer-strategie die bij `passport-azure-ad` is geleverd. (We bekijken de code hier alleen.) Plaats deze achter hetgeen u hierboven hebt ingevoerd:

```Javascript
/**
/*
/* Calling the OIDCBearerStrategy and managing users
/*
/* Passport pattern provides the need to manage users and info tokens
/* with a FindorCreate() method that must be provided by the implementer.
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
log.info(token, 'was the token retrieved');
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

In Passport wordt een patroon gebruikt voor alle strategieën (waaronder Twitter en Facebook); deze komen overeen met de strategieën waaraan alle schrijvers van strategieën zich houden. U geeft hieraan een `function()` door die `token` en `done` als parameters heeft. U krijgt deze strategie terug nadat deze zijn werk heeft gedaan. Sla de gebruiker op en sla ook het token op, zodat u het niet opnieuw hoeft op te vragen.

> [AZURE.IMPORTANT]
In bovenstaande code wordt elke gebruiker gebruikt die zich bij de server kan verifiëren. Dit wordt automatische registratie genoemd. In productieservers wilt u waarschijnlijk geen gebruikers toelaten zonder dat zij eerst een registratieproces hebben doorlopen dat u hebt vastgelegd. Dit is doorgaans het patroon dat u ziet in consumenten-apps waarbij u zich kunt registreren via Facebook, maar waarvoor u vervolgens aanvullende informatie moet invullen. Als dit geen opdrachtregelprogramma was, had u het e-mailadres niet kunnen verkrijgen uit het tokenobject dat wordt geretourneerd en had u gebruikers daarna niet kunnen vragen aanvullende gegevens in te vullen. Omdat dit een testserver is, voegen we deze gewoon toe aan de database in het geheugen.

### Eindpunten beveiligen

U beveiligt de eindpunten door de `passport.authenticate()`-aanroep op te geven via het protocol dat u wilt gebruiken.

U kunt de route in uw servercode bewerken om iets interessanters te doen:

```Javascript
server.get('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), listTasks);
server.get('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), listTasks);
server.get('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), getTask);
server.head('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), getTask);
server.post('/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
session: false
}), createTask);
server.post('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), createTask);
server.del('/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), removeAll, function respond(req, res, next) {
res.send(204);
next();
});
```

## De servertoepassing opnieuw uitvoeren om te verifiëren dat u wordt geweigerd

U kunt `curl` opnieuw gebruiken om na te gaan of u nu OAuth2-bescherming voor uw eindpunten hebt. Doe dit voordat u een van onze client-SDK's met dit eindpunt uitvoert. De geretourneerde headers moeten voldoende zijn om aan te geven dat u op de juiste weg bent.

Controleer of het MongoDB-exemplaar is geactiveerd:

    $sudo mongodb

Schakel naar de directory en gebruik `curl`:

    $ cd azuread
    $ node server.js

Probeer een basic POST:

`$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

```Shell
HTTP/1.1 401 Unauthorized
Connection: close
WWW-Authenticate: Bearer realm="Users"
Date: Tue, 14 Jul 2015 05:45:03 GMT
Transfer-Encoding: chunked
```

Een 401-fout is het gewenste antwoord. Hiermee wordt aangegeven dat de Passport-laag probeert om te leiden naar het geautoriseerde eindpunt.


## U hebt nu een REST-API-service die gebruikmaakt van OAuth2

U bent nu met deze server zo ver mogelijk gegaan zonder gebruik te maken van een OAuth2-compatibele client. Daarvoor hebt u een extra procedure nodig.

Als u alleen wilt weten hoe u een REST-API implementeert met Restify en OAuth2, hebt u nu voldoende code om verder te gaan met het ontwikkelen van uw service. U kunt zich baseren op dit voorbeeld.

Als naslaginformatie wordt het volledige voorbeeld (zonder uw configuratiewaarden) [geleverd als een zip-bestand](https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs/archive/complete.zip). U kunt dit ook klonen vanuit GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs.git```


## Volgende stappen

U kunt nu verdergaan met geavanceerdere onderwerpen, te weten:

[Een web-API verbinden met behulp van iOS met B2C](active-directory-b2c-devquickstarts-ios.md)



<!--HONumber=Jun16_HO2-->


