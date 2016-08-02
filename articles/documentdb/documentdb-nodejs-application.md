<properties 
    pageTitle="Node.js leren - Zelfstudie over DocumentDB Node.js | Microsoft Azure" 
    description="Node.js leren In deze zelfstudie wordt uitgelegd hoe u Microsoft Azure DocumentDB gebruikt voor het opslaan van en de toegang tot gegevens van een Node.js Express-webtoepassing die wordt gehost Azure Websites." 
    keywords="Application development, database tutorial, learn node.js, node.js tutorial, documentdb, azure, Microsoft azure"
    services="documentdb" 
    documentationCenter="nodejs" 
    authors="aliuy" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="hero-article" 
    ms.date="04/18/2016" 
    ms.author="andrl"/>

# <a name="_Toc395783175"></a>Een Node.js-webtoepassing bouwen met DocumentDB

> [AZURE.SELECTOR]
- [.NET](documentdb-dotnet-application.md)
- [Node.js](documentdb-nodejs-application.md)
- [Java](documentdb-java-application.md)
- [Python](documentdb-python-application.md)

In deze zelfstudie over Node.js wordt uitgelegd hoe u de Azure DocumentDB-service gebruikt voor het opslaan van en de toegang tot een Node.js Express-toepassing die wordt gehost op Azure Websites.

U kunt het beste eerste de volgende video bekijken, waarin u leert hoe een Azure DocumentDB-databaseaccount inricht en JSON-documenten opslaat in uw Node.js-toepassing. 

> [AZURE.VIDEO azure-demo-getting-started-with-azure-documentdb-on-nodejs-in-linux]

Keer vervolgens terug naar deze zelfstudie over Node.js waarin de volgende vragen worden beantwoord:

- Hoe gebruik ik de npm-module om met DocumentDB te werken?
- Hoe implementeer ik de webtoepassing naar Azure Websites?

Door deze zelfstudie te volgen, bouwt u een eenvoudige webtoepassing voor taakbeheer waarmee u taken kunt maken, ophalen en voltooien. De taken worden opgeslagen als JSON-documenten in Azure DocumentDB.

![Schermopname van de toepassing My Todo List die in deze zelfstudie voor Node.js wordt gemaakt](./media/documentdb-nodejs-application/image1.png)

Hebt u geen tijd om de zelfstudie te voltooien en wilt u gewoon de volledige oplossing downloaden? Geen probleem, u kunt de complete voorbeeldoplossing downloaden van [GitHub][].

## <a name="_Toc395783176"></a>Vereisten

> [AZURE.TIP] Voor deze zelfstudie wordt ervan uitgegaan dat u ervaring hebt met Node.js en Azure Websites

Voordat u de instructies in dit artikel uitvoert, moet u beschikken over het volgende:

- Een actief Azure-account. Als u geen account hebt, kunt u binnen een paar minuten een gratis proefaccount maken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie.
- [Node.js][] versie v0.10.29 of hoger.
- [Express generator](http://www.expressjs.com/starter/generator.html) (te installeren via `npm install express-generator -g`)
- [Git][].

## <a name="_Toc395637761"></a>Stap 1: een DocumentDB-databaseaccount maken

Begin met het maken van een DocumentDB-account. Als u al een account hebt, gaat u verder met [Stap 2: een nieuwe Node.js-toepassing maken](#_Toc395783178).

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

[AZURE.INCLUDE [documentdb-keys](../../includes/documentdb-keys.md)]

## <a name="_Toc395783178"></a>Stap 2: een nieuwe Node.js-toepassing maken

Laten we eens kijken hoe u het [Express](http://expressjs.com/)-framework gebruikt om een eenvoudig Hello World Node.js-project te maken.

1. Open uw favoriete terminal.

2. Gebruik de express-generator om een toepassing met de naam **todo** te maken.

        express todo

3. Open de nieuwe map **todo** en installeer afhankelijkheden.

        cd todo
        npm install

4. Voer uw nieuwe toepassing uit.

        npm start

5. U kunt uw nieuwe toepassing weergeven door in uw browser naar [http://localhost:3000](http://localhost:3000) te navigeren.

    ![Node.js leren - Schermopname van de toepassing Hello World in een browservenster](./media/documentdb-nodejs-application/image12.png)

## <a name="_Toc395783179"></a>Stap 3: aanvullende modules installeren

Het bestand **package.json** is een van de bestanden die zijn gemaakt in de hoofdmap van het project. Dit bestand bevat een lijst met aanvullende modules die u nodig hebt voor uw Node.js-toepassing. Later, wanneer u deze toepassing implementeert naar Azure Websites, wordt dit bestand gebruikt om te bepalen welke modules ter ondersteuning van uw toepassing moeten worden geïnstalleerd op Azure. Voor deze zelfstudie moeten u nog twee pakketten installeren.

1. Keer terug naar de terminal en installeer de **async**-module via npm.

        npm install async --save

1. Installeer de **DocumentDB**-module via NPM. Dit is de module waar alle DocumentDB-magie plaatsvindt.

        npm install documentdb --save

3. Een snelle controle van het bestand **package.json** van de toepassing toont u de aanvullende modules. Dit bestand vertelt Azure welke pakketten moeten worden gedownload en geïnstalleerd wanneer uw toepassing wordt uitgevoerd. Het bestand is vergelijkbaar met het onderstaande voorbeeld.

    ![Schermopname van het tabblad package.json](./media/documentdb-nodejs-application/image17.png)

       This tells Node (and Azure later) that your application depends on these additional modules.

## <a name="_Toc395783180"></a>Stap 4: de DocumentDB-service in een knooppunttoepassing gebruiken

Hiermee is de installatie en eerste configuratie voltooid en kunnen we aan het echte werk beginnen, namelijk het schrijven van code met Azure DocumentDB.

### Het model maken

1. Maak in de projectmap een nieuwe map met de naam **models**.
2. Maak in de map **models** een nieuw bestand met de naam **taskDao.js**. Dit bestand bevat het model voor de taken die met onze toepassing worden gemaakt.
3. Maak in dezelfde map **models** nog een bestand met de naam **docdbUtils.js**. Dit bestand bevat enkele handige, opnieuw bruikbare codefragmenten die we voor de hele toepassing zullen gebruiken. 
4. Kopieer de volgende code naar het bestand **docdbUtils.js**.

        var DocumentDBClient = require('documentdb').DocumentClient;
            
        var DocDBUtils = {
            getOrCreateDatabase: function (client, databaseId, callback) {
                var querySpec = {
                    query: 'SELECT * FROM root r WHERE r.id= @id',
                    parameters: [{
                        name: '@id',
                        value: databaseId
                    }]
                };
        
                client.queryDatabases(querySpec).toArray(function (err, results) {
                    if (err) {
                        callback(err);
        
                    } else {
                        if (results.length === 0) {
                            var databaseSpec = {
                                id: databaseId
                            };
        
                            client.createDatabase(databaseSpec, function (err, created) {
                                callback(null, created);
                            });
        
                        } else {
                            callback(null, results[0]);
                        }
                    }
                });
            },
        
            getOrCreateCollection: function (client, databaseLink, collectionId, callback) {
                var querySpec = {
                    query: 'SELECT * FROM root r WHERE r.id=@id',
                    parameters: [{
                        name: '@id',
                        value: collectionId
                    }]
                };             
                
                client.queryCollections(databaseLink, querySpec).toArray(function (err, results) {
                    if (err) {
                        callback(err);
        
                    } else {        
                        if (results.length === 0) {
                            var collectionSpec = {
                                id: collectionId
                            };
                            
                            var requestOptions = {
                                offerType: 'S1'
                            };
                            
                            client.createCollection(databaseLink, collectionSpec, requestOptions, function (err, created) {
                                callback(null, created);
                            });
        
                        } else {
                            callback(null, results[0]);
                        }
                    }
                });
            }
        };
                
        module.exports = DocDBUtils;

> [AZURE.TIP] Voor createCollection is een optionele requestOptions-parameter gebruikt die kan worden gebruikt om het aanbiedingstype (OfferType) voor de verzameling (Collection) te specificeren. Als er geen waarde voor requestOptions.offerType is opgegeven, wordt het standaard-OfferType gebruikt om de verzameling te maken.
> Raadpleeg [Prestatieniveaus in DocumentDB](documentdb-performance-levels.md) voor meer informatie over DocumentDB-OfferTypes 
        
3. Sla het bestand **docdbUtils.js** op en sluit het bestand.

4. Voeg aan het begin van het bestand **taskDao.js** de volgende code toe om te verwijzen naar de **DocumentDBClient** en **docdbUtils.js** die eerder is gemaakt:

        var DocumentDBClient = require('documentdb').DocumentClient;
        var docdbUtils = require('./docdbUtils');

4. Vervolgens voegt u code toe om het taakobject te definiëren en te exporteren. Hiermee wordt het taakobject geïnitialiseerd en wordt de database- en documentverzameling ingesteld die we zullen gebruiken.

        function TaskDao(documentDBClient, databaseId, collectionId) {
          this.client = documentDBClient;
          this.databaseId = databaseId;
          this.collectionId = collectionId;
        
          this.database = null;
          this.collection = null;
        }
        
        module.exports = TaskDao;

5. Voeg vervolgens de volgende code toe om de aanvullende methoden voor het taakobject te definiëren, waardoor er interactie mogelijk is met gegevens die zijn opgeslagen in DocumentDB.

        TaskDao.prototype = {
            init: function (callback) {
                var self = this;
        
                docdbUtils.getOrCreateDatabase(self.client, self.databaseId, function (err, db) {
                    if (err) {
                        callback(err);
                    } else {
                        self.database = db;
                        docdbUtils.getOrCreateCollection(self.client, self.database._self, self.collectionId, function (err, coll) {
                            if (err) {
                                callback(err);
        
                            } else {
                                self.collection = coll;
                            }
                        });
                    }
                });
            },
        
            find: function (querySpec, callback) {
                var self = this;
        
                self.client.queryDocuments(self.collection._self, querySpec).toArray(function (err, results) {
                    if (err) {
                        callback(err);
        
                    } else {
                        callback(null, results);
                    }
                });
            },
        
            addItem: function (item, callback) {
                var self = this;
        
                item.date = Date.now();
                item.completed = false;
        
                self.client.createDocument(self.collection._self, item, function (err, doc) {
                    if (err) {
                        callback(err);
        
                    } else {
                        callback(null, doc);
                    }
                });
            },
        
            updateItem: function (itemId, callback) {
                var self = this;
        
                self.getItem(itemId, function (err, doc) {
                    if (err) {
                        callback(err);
        
                    } else {
                        doc.completed = true;
        
                        self.client.replaceDocument(doc._self, doc, function (err, replaced) {
                            if (err) {
                                callback(err);
        
                            } else {
                                callback(null, replaced);
                            }
                        });
                    }
                });
            },
        
            getItem: function (itemId, callback) {
                var self = this;
        
                var querySpec = {
                    query: 'SELECT * FROM root r WHERE r.id = @id',
                    parameters: [{
                        name: '@id',
                        value: itemId
                    }]
                };
        
                self.client.queryDocuments(self.collection._self, querySpec).toArray(function (err, results) {
                    if (err) {
                        callback(err);
        
                    } else {
                        callback(null, results[0]);
                    }
                });
            }
        };

6. Sla het bestand **taskDao.js** op en sluit het bestand. 

### De controller maken

1. Maak in de map **routes** van uw project een nieuw bestand met de naam **tasklist.js**. 
2. Voeg de volgende code toe aan het bestand **tasklist.js**. Hiermee worden de DocumentDBClient- en async-modules geladen die door het bestand **tasklist.js** worden gebruikt. Met deze code wordt ook de functie **TaskList** gedefinieerd, waarnaar een exemplaar van het eerder gemaakte object **Task** wordt doorgegeven:

        var DocumentDBClient = require('documentdb').DocumentClient;
        var async = require('async');
        
        function TaskList(taskDao) {
          this.taskDao = taskDao;
        }
        
        module.exports = TaskList;

3. Voeg vervolgens methoden toe aan het bestand **tasklist.js** door de methoden voor **showTasks, addTask** en **completeTasks** toe te voegen:
        
        TaskList.prototype = {
            showTasks: function (req, res) {
                var self = this;
        
                var querySpec = {
                    query: 'SELECT * FROM root r WHERE r.completed=@completed',
                    parameters: [{
                        name: '@completed',
                        value: false
                    }]
                };
        
                self.taskDao.find(querySpec, function (err, items) {
                    if (err) {
                        throw (err);
                    }
        
                    res.render('index', {
                        title: 'My ToDo List ',
                        tasks: items
                    });
                });
            },
        
            addTask: function (req, res) {
                var self = this;
                var item = req.body;
        
                self.taskDao.addItem(item, function (err) {
                    if (err) {
                        throw (err);
                    }
        
                    res.redirect('/');
                });
            },
        
            completeTask: function (req, res) {
                var self = this;
                var completedTasks = Object.keys(req.body);
        
                async.forEach(completedTasks, function taskIterator(completedTask, callback) {
                    self.taskDao.updateItem(completedTask, function (err) {
                        if (err) {
                            callback(err);
                        } else {
                            callback(null);
                        }
                    });
                }, function goHome(err) {
                    if (err) {
                        throw err;
                    } else {
                        res.redirect('/');
                    }
                });
            }
        };


4. Sla het bestand **tasklist.js** op en sluit het bestand.
 
### Config.js toevoegen

1. Maak een nieuw bestand met de naam **config.js** in uw projectmap.
2. Voeg het volgende toe aan het bestand **config.js**. Hiermee definieert u configuratie-instellingen en waarden die nodig zijn voor de toepassing.

        var config = {}
        
        config.host = process.env.HOST || "[the URI value from the DocumentDB Keys blade on http://portal.azure.com]";
        config.authKey = process.env.AUTH_KEY || "[the PRIMARY KEY value from the DocumentDB Keys blade on http://portal.azure.com]";
        config.databaseId = "ToDoList";
        config.collectionId = "Items";
        
        module.exports = config;

3. Werk in het bestand **config.js** de waarden voor HOST en AUTH_KEY bij door gebruik te maken van de waarden op de blade Sleutels van uw DocumentDB-account in [Microsoft Azure Portal](https://portal.azure.com):

4. Sla het bestand **config.js** op en sluit het bestand.
 
### App.js wijzigen

1. Ga naar de projectmap en open het bestand **app.js**. Dit bestand is gemaakt toen de Express-webtoepassing werd gemaakt.
2. Voeg de volgende code toe boven aan het bestand **app.js**.
    
        var DocumentDBClient = require('documentdb').DocumentClient;
        var config = require('./config');
        var TaskList = require('./routes/tasklist');
        var TaskDao = require('./models/taskDao');

3. Deze code definieert het configuratiebestand dat moet worden gebruikt en leest waarden uit dit bestand naar enkele variabelen die we zodra zullen gebruiken.
4. Vervang de volgende twee regels in het bestand **app.js**:

        app.use('/', routes);
        app.use('/users', users); 

      door het volgende codefragment:

        var docDbClient = new DocumentDBClient(config.host, {
            masterKey: config.authKey
        });
        var taskDao = new TaskDao(docDbClient, config.databaseId, config.collectionId);
        var taskList = new TaskList(taskDao);
        taskDao.init();
        
        app.get('/', taskList.showTasks.bind(taskList));
        app.post('/addtask', taskList.addTask.bind(taskList));
        app.post('/completetask', taskList.completeTask.bind(taskList));
        app.set('view engine', 'jade');



6. Deze regels definiëren een nieuw exemplaar van het object **TaskDao** met een nieuwe verbinding met DocumentDB (door gebruik te maken van de gelezen waarden uit het bestand **config.js**), initialiseren het taakobject en bindt de formulieracties aan de methoden op onze **TaskList**-controller. 

7. Tot slot slaat u het bestand **app.js** op en sluit u het bestand.
 
## <a name="_Toc395783181"></a>Stap 5: een gebruikersinterface maken

U kunt zich nu concentreren op het bouwen van de gebruikersinterface, zodat gebruikers kunnen communiceren met de toepassing. De Express-toepassing die is gemaakt, gebruikt **Jade** als weergave-engine. Raadpleeg [http://jade-lang.com/](http://jade-lang.com/) voor meer informatie over Jade.

1. Het bestand **layout.jade** in de map **views** wordt gebruikt als een algemeen sjabloon voor andere **.jade**-bestanden. In deze stap wordt het bestand aangepast voor het gebruik van [Twitter Bootstrap](https://github.com/twbs/bootstrap). Dit is een werkset waarmee u eenvoudig een aantrekkelijk ogende website kunt maken. 
2. Open het bestand **layout.jade** in de map **views** en vervang de inhoud door het volgende:
    
        doctype html
        html
          head
            title= title
            link(rel='stylesheet', href='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/css/bootstrap.min.css')
            link(rel='stylesheet', href='/stylesheets/style.css')
          body
            nav.navbar.navbar-inverse.navbar-fixed-top
              div.navbar-header
                a.navbar-brand(href='#') My Tasks
            block content
            script(src='//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.2.min.js')
            script(src='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/bootstrap.min.js')



    Zodoende weet de **Jade**-engine dat er bepaalde HTML-code voor onze toepassing moet worden weergegeven en wordt er een **blok** met de naam **content** gemaakt waar we lay-out kunnen opgeven voor onze inhoudspagina's.
    Sla het bestand **layout.jade** op en sluit het bestand.

4. Open nu het bestand **index.jade**, de weergave die door de toepassing wordt gebruikt, en vervang de inhoud van het bestand door het volgende:

        extends layout
        
        block content
          h1 #{title}
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
                    td #{task.name}
                    td #{task.category}
                    - var date  = new Date(task.date);
                    - var day   = date.getDate();
                    - var month = date.getMonth() + 1;
                    - var year  = date.getFullYear();
                    td #{month + "/" + day + "/" + year}
                    td
                      input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
            button.btn(type="submit") Update tasks
          hr
          form.well(action="/addtask", method="post")
            label Item Name:
            input(name="name", type="textbox")
            label Item Category:
            input(name="category", type="textbox")
            br
            button.btn(type="submit") Add item

    Hiermee breidt u de lay-out uit en levert u inhoud voor de tijdelijke aanduiding **content** die we eerder in het bestand **layout.jade** zijn tegengekomen.
    
    In deze lay-out hebben we twee HTML-formulieren gemaakt. 
    Het eerste formulier bevat een tabel voor onze gegevens en een knop waarmee items kunnen worden bijgewerkt door ze naar de methode **/completetask** van de controller te verzenden.
    Het tweede formulier bevat twee invoervelden en een knop waarmee een nieuw item kan worden gemaakt door ze naar de methode **/addtask** van de controller te verzenden.
    
    Dit is alles wat we nodig hebben voor een goed werkende toepassing.

5. Open het bestand **style.css** in de map **public\stylesheets** en vervang de code door de volgende code:

        body {
          padding: 50px;
          font: 14px "Lucida Grande", Helvetica, Arial, sans-serif;
        }
        a {
          color: #00B7FF;
        }
        .well label {
          display: block;
        }
        .well input {
          margin-bottom: 5px;
        }
        .btn {
          margin-top: 5px;
          border: outset 1px #C8C8C8;
        }

    Sla het bestand **style.css** op en sluit het.

## <a name="_Toc395783181"></a>Stap 6: de toepassing lokaal uitvoeren

1. Als u de toepassing wilt testen op uw lokale machine, voert u `npm start` uit in een terminal om uw toepassing te starten en start u een browser met een pagina die er ongeveer uitziet als de onderstaande afbeelding:

    ![Schermopname van de toepassing MyTodo List in een browservenster](./media/documentdb-nodejs-application/image18.png)


2. Gebruik de velden Item, Item Name (Itemnaam) en Category (Categorie) om informatie op te geven en klik vervolgens op **Add Item** (Item toevoegen).

3. De pagina moet worden bijgewerkt met het nieuwe item in de takenlijst.

    ![Schermopname van de toepassing met een nieuw item in de takenlijst](./media/documentdb-nodejs-application/image19.png)

4. Als u een taak wilt voltooien, schakelt u het selectievakje in de kolom Complete (Voltooid) in en klikt u vervolgens op **Update tasks** (Taken bijwerken).

## <a name="_Toc395783182"></a>Stap 7: uw project voor de ontwikkeling van een toepassing implementeren op Azure Websites

1. Als dit nog niet hebt gedaan, schakelt u een git-opslagplaats voor uw Azure-website in. In het onderwerp [Continue implementatie met Git in Azure App Service](../app-service-web/web-sites-publish-source-control.md) vindt u hiervoor de instructies.

2. Voeg uw Azure-website toe als een externe git.

        git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git

3. Implementeer door naar de externe git te pushen.

        git push azure master

4. Over een paar seconden zal GIT de publicatie van uw webtoepassing voltooien en een browser starten waarin u kunt zien hoe uw werk in Azure wordt uitgevoerd.

## <a name="_Toc395637775"></a>Volgende stappen

Gefeliciteerd. U hebt zojuist uw eerste Node.js Express-webtoepassing met Azure DocumentDB gemaakt en gepubliceerd naar Azure Websites.

De broncode voor de volledige referentietoepassing kan worden gedownload van [GitHub][].

Zie het [Node.js-ontwikkelaarscentrum](https://azure.microsoft.com/develop/nodejs/) voor meer informatie.

[Node.js]: http://nodejs.org/
[Git]: http://git-scm.com/
[GitHub]: https://github.com/Azure-Samples/documentdb-node-todo-app
 



<!--HONumber=Jun16_HO2-->


