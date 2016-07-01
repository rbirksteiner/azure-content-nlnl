<properties
    pageTitle="Aan de slag met Azure Mobile Services en Sencha"
    description="Volg deze zelfstudie, zodat u kunt gaan ontwikkelen met Mobile Services en het mobiele app-framework Sencha HTML5."
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
    ms.topic="get-started-article"
    ms.date="02/10/2016"
    ms.author="glenga"/>

# <a name="getting-started"> </a>Aan de slag met Mobile Services en Sencha Touch

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]
&nbsp;

[AZURE.INCLUDE [mobile-services-hero-slug](../../includes/mobile-services-hero-slug.md)]

##Overzicht

Deze zelfstudie laat zien hoe u Azure Mobile Services in uw Sencha Touch-toepassing kunt gebruiken. U maakt een eenvoudige *takenlijst*-app met Sencha Touch die gebruikmaakt van een mobiele service die u via de klassieke Azure Portal definieert. Deze zelfstudie is bedoeld voor gemiddelde tot gevorderde ontwikkelaars van web-apps die een goede kennis hebben van JavaScript en bekend zijn met het Sencha Touch-framework.

Als u liever een video bekijkt, gebruik dan deze clip. De video toont dezelfde stappen als deze zelfstudie. In de video legt Arthur Kay uit hoe u een Sencha Touch-toepassing maakt met een Azure Mobile Services-back-end.

> [AZURE.VIDEO getting-started-with-sencha-touch]


Hieronder ziet u een schermafbeelding van de voltooide app:

![][0]

##Vereisten

- Download en installeer [Sencha Touch](http://wwww.sencha.com/products/touch/download" target="_blank").

- Download en installeer [Sencha Cmd Tool](http://www.sencha.com/products/sencha-cmd/download" target="_blank").

- Java Runtime Environment (JRE) of Java Development Kit (als u Android-apps maakt).
- Ruby en SASS gem.

## <a name="create-new-service"> </a>Een nieuwe mobiele service maken

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

##Een takentabel maken

Nadat u uw mobiele service hebt gemaakt, kunt u in de klassieke Azure Portal een eenvoudige Quick Start volgen om een nieuwe databasetabel te maken voor gebruik in uw mobiele service.

1. Klik in de [klassieke Azure Portal] op **Mobile Services** en klik vervolgens op de mobiele service die u zojuist hebt gemaakt.

2. Klik op het tabblad Quick Start op **HTML** onder **Platform kiezen** en vouw **Een nieuwe HTML-app maken** uit.

    ![Mobiele Quick Start voor HTML](./media/partner-sencha-mobile-services-get-started/mobile-portal-quickstart-html.png)

    U ziet nu de drie eenvoudige stappen om een HTML-app te maken en te hosten die is verbonden met uw mobiele service.

    ![Mobiele Quick Start voor HTML](./media/partner-sencha-mobile-services-get-started/mobile-quickstart-steps-html.png)

3. Klik op **Takentabel maken** om een tabel te maken voor het opslaan van app-gegevens.

    > [AZURE.NOTE] Download de HTML-app NIET van de klassieke Azure Portal. In plaats daarvan gaat u in onderstaande sectie handmatig een Sencha Touch-toepassing maken.


1. Houd rekening met de **app-sleutel** en de **app-URL** in de klassieke Azure Portal. U gebruikt deze in andere gedeelten van deze zelfstudie.

    ![App-sleutel](./media/partner-sencha-mobile-services-get-started/mobile-app-key-portal.png)

1. Controleer op het tabblad **Configureren** of `localhost` al wordt vermeld in de lijst **Aanvragen van hostnamen toestaan** onder **Cross-origin-resource delen (CORS)**. Als dat niet het geval is, typt u `localhost` in het veld **Hostnaam** en klikt u vervolgens op **Opslaan**.

    ![CORS voor lokale host instellen](./media/partner-sencha-mobile-services-get-started/mobile-services-set-cors-localhost.png)

##Uw Touch-toepassing genereren

Met Sencha Cmd kunt u eenvoudig een Sencha Touch-sjabloontoepassing genereren. Dit is een uitstekende manier om een toepassing zeer snel gebruiksklaar te hebben.

In de map waar u het Touch-framework hebt geïnstalleerd, voert u de volgende opdracht uit:

    $ sencha generate app Basic /path/to/application

Hiermee wordt een sjabloon voor de Touch-toepassing met de toepassingsnaam Basic gegenereerd. Als u uw toepassing wilt starten, navigeert u in uw browser naar de map /pad/naar/toepassing. De standaard Touch-voorbeeldtoepassing moet worden weergegeven.

##De Sencha Touch-extensies voor Azure installeren

De extensie voor Azure wordt handmatig of als Sencha-pakket geïnstalleerd. U bepaalt zelf welke methode u wilt gebruiken.

###Handmatige installatie

In de meeste Touch-toepassingen kunt u een externe bibliotheek van klassen toevoegen. Dit doet u door het pakket te downloaden, dit in de toepassingsmap uit te pakken en het Touch-laadprogramma met de locatie van de bibliotheek te configureren.

U kunt de Azure-extensies handmatig aan uw toepassing toevoegen door de volgende stappen te volgen:

1. Download het pakket met de Azure-extensies [hier](https://market.sencha.com/extensions/sencha-extensions-for-microsoft-azure). (U kunt uw id voor de Sencha Forums gebruiken om toegang tot dit gedeelte te krijgen.)

2. Kopieer het pakket met de Azure-extensies uit de downloadmap naar de plek waar u het wilt opslaan en pak het uit:

        $ cd /path/to/application
        $ mv /download-location/azure.zip .
        $ unzip azure.zip

    Hiermee maakt u een **Azure**-map met de gehele pakketbron, voorbeelden en documentatie. De bron wordt opgeslagen in de map **azure/src**.


###Installatie als Sencha-pakket

> [AZURE.NOTE] U kunt deze methode alleen gebruiken als u uw toepassing hebt gegenereerd met behulp van de opdracht <code>sencha generate app</code>.

Alle toepassingen die door Sencha Cmd worden gegenereerd, hebben in de hoofdmap een map 'packages'. De locatie van deze map kan worden geconfigureerd. Maar ongeacht de locatie dient de map 'packages' als opslag voor alle pakketten die door uw toepassing worden gebruikt (of door meerdere toepassingen als u een Sencha-werkruimte hebt gemaakt).

Aangezien Ext.Azure een Sencha Cmd-pakket is, kan de broncode eenvoudig via Sencha Cmd worden geïnstalleerd en opgenomen in uw toepassing. (Zie [Sencha Cmd-pakketten](http://docs.sencha.com/cmd/6.x/cmd_packages/cmd_packages.html) voor meer informatie.)

Als u het pakket met de Azure-extensies vanuit de opslagplaats met Sencha-pakketten wilt downloaden en installeren, moet u de pakketnaam aan het bestand **app.json** toevoegen en uw toepassing bouwen:

1. Voeg het Azure-pakket toe aan de sectie met vereisten van het bestand app.json:

        {
            "name": "Basic",
            "requires": [
                "touch-azure"
            ]
        }

2. Bouw uw toepassing opnieuw door gebruik te maken van **Sencha Cmd** om het pakket op te halen en te installeren:

        $ sencha app build

Zowel de **Sencha-app voor bouwen** als de **Sencha-app voor vernieuwen** voert nu de stappen uit die nodig zijn voor de integratie van het pakket in uw toepassing. Na het wijzigen van de pakketvereisten moet u doorgaans **Sencha-app vernieuwen** uitvoeren, zodat de metagegevens die ter ondersteuning van de dev-modus zijn vereist, worden bijgewerkt.

Welke opdracht u ook uitvoert, het pakket wordt door Sencha Cmd gedownload en uitgevouwen in de map 'packages'. Hierna wordt er in uw werkruimte een map 'packages/touch-azure' weergegeven.

##Azure opnemen en configureren

**Bestandsnaam**: app.js

De Azure-extensie is nu gedownload en geïnstalleerd in uw toepassingsmap. De volgende stap is dat uw toepassing weet waar de bronbestanden te vinden zijn en deze bestanden vereist:

1. Configureer het Sencha-laadprogramma met de locatie van de broncode:

        Ext.Loader.setConfig({
            enabled : true,
            paths   : {
                'Ext'       : 'touch/src',
                'Ext.azure' : '/path-to/azure-for-touch/azure/src'
            }
        });


2. De Azure-klassenbestanden vereisen:

        Ext.application({

            requires: [ 'Ext.azure.Azure' ],

            // ...

        });


3. Azure configureren

    Het Azure-pakket wordt geïnitialiseerd door de methode **Ext.Azure.init** aan te roepen in de startsectie van uw toepassing. Met deze methode wordt er een configuratieobject doorgegeven met mobiele servicereferenties, evenals andere referenties en functies die u wilt gebruiken.

    Hoewel u het configuratieobject rechtstreeks aan de init-methode kunt doorgeven, raden we u aan een configuratie-eigenschap van een Sencha-toepassing met de naam **azure** te maken en alle relevante informatie daarin te plaatsen. Vervolgens kunt u deze eigenschapswaarde aan de methode Ext.Azure.init doorgeven.

    Wanneer u in Azure een mobiele service maakt (zie [Aan de slag met Azure](http://senchaazuredocs.azurewebsites.net/#!/guide/getting_started)), worden aan die service een toepassingssleutel en een URL toegewezen. Deze informatie moet aan uw Azure-pakket worden opgegeven, zodat dit verbinding kan maken met uw service.

    In dit voorbeeld wordt een zeer eenvoudige Azure-configuratie en -initialisatie weergegeven met alleen de toepassingssleutel en de URL:

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

    Raadpleeg de Ext.Azure API-documentatie voor meer informatie over de configuratieopties van Azure.


Gefeliciteerd! Uw toepassing heeft nu toegang tot uw mobiele service.

##De taken-app bouwen

U hebt nu uw toepassing geconfigureerd en de Azure-extensie opgenomen. Ook hebt u deze voorzien van uw mobiele-servicereferenties. Nu kunt u verdergaan met het maken van een Touch-toepassing die gebruikmaakt van uw mobiele service voor het weergeven en bewerken van uw takenlijstgegevens die in de service zijn opgeslagen.

###De gegevensproxy van Azure configureren

**Bestandsnaam:** app/model/TodoItem.js

Uw Touch-toepassing communiceert met uw mobiele service via een gegevensproxy. De proxy voert al het werk uit. Deze verzendt aanvragen naar de mobiele service en ontvangt gegevens van de mobiele service. Als de proxy in combinatie met een Touch-gegevensmodel en -archief wordt gebruikt, hoeft u de externe gegevens niet meer te verwerken en in uw toepassing op te nemen. Dit wordt door Touch zelf gedaan.

Sencha Touch-modellen bieden de definitie voor de gegevensrecords die u in uw toepassing gaat gebruiken. Hiermee kunt u niet alleen de gegevensvelden definiëren, maar ook een configuratie opgeven voor de proxy die de communicatie tussen de toepassing en de mobiele service van Azure verwerkt.

In onderstaande code ziet u dat we de velden (en de typen hiervan) voor het model definiëren en ook een proxyconfiguratie bieden. Wanneer u uw proxy configureert, moet u hieraan een type (in dit geval 'Azure') toewijzen, en de tabelnaam van de mobiele service (taak) en andere optionele parameters opgeven. In dit voorbeeld schakelen we proxy-paginering in, zodat we naadloos voor- en achteruit door de lijstitems kunnen bladeren.

De Azure-proxy stelt automatisch alle HTTP-headers in met de juiste CRUD-bewerkingen die door de Azure-API worden verwacht (inclusief verificatiereferenties, indien aanwezig).

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


###Uw taken opslaan

**Bestandsnaam**: app/store/TodoItems.js

Sencha Touch-archieven worden gebruikt om verzamelingen van gegevensrecords (modellen) op te slaan. Deze kunnen worden gebruikt als bronnen voor Touch-onderdelen, zodat u de records op verschillende manieren kunt weergeven. U kunt bijvoorbeeld gebruikmaken van rasters, grafieken en lijsten.

Hieronder definiëren we een archief dat wordt gebruikt om al uw taken uit het archief in op te slaan die uit de mobiele service van Azure zijn opgehaald. Zoals u kunt zien, bevat de configuratie van het archief de naam van het modeltype (Basic.model.TodoItem, hierboven gedefinieerd). Hiermee definieert u de structuur van de records die in het archief worden opgenomen.

Er zijn ook enkele extra configuratieopties voor het archief. Zo kunt u de paginagrootte opgeven (8 records), maar u kunt bijvoorbeeld ook instellen dat het sorteren van de records voor dit archief op afstand moet worden uitgevoerd door de mobiele service van Azure (er wordt dan niet lokaal gesorteerd in het archief zelf).

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


###Uw taken bekijken en bewerken

**Bestandsnaam**: app/view/DataItem.js

Nu u de structuur van elke taak hebt gedefinieerd en een archief hebt gemaakt om alle records in te plaatsen, moet u bedenken hoe u deze informatie aan de gebruiker van de app wilt weergegeven. Normaal gesproken wordt de informatie aan de gebruiker weergegeven met behulp van **Weergaven**. Een weergave kan bestaan uit een willekeurig aantal Touch-onderdelen die afzonderlijk of in combinatie met andere onderdelen worden gebruikt.

Onderstaande weergave bestaat uit een lijstitem waarmee wordt gedefinieerd hoe elke record wordt weergegeven, gecombineerd met enkele knoppen waarmee acties mogelijk worden gemaakt om elk item te verwijderen.

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


###Uw primaire weergave maken

**Bestandsnaam**: app/view/Main.js

Nu u de indeling van een afzonderlijk takenitem (hierboven) hebt gedefinieerd, gaan we een volledige gebruikersinterface rond de lijst maken. In deze interface worden de daadwerkelijke lijst met items, de toepassingsnaam en een knop om een nieuwe taak toe te voegen, gedefinieerd.

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

###Ervoor zorgen dat alles met elkaar werkt

**Bestandsnaam**: app/controller/Main.js

De laatste stap in het maken van onze toepassing heeft betrekking op hoe er wordt gereageerd op klikken/tikken op knoppen (Verwijderen, Opslaan, enzovoort) en op de logica achter al deze aanvragen. Sencha Touch maakt gebruik van controllers die naar deze gebeurtenissen luisteren en dienovereenkomstig reageren.

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
                        Ext.util.Format.format('There was an error deleting this task.<br/><br/>    Status Code: {0}<br/>Status Text: {1}',
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

###Alles samenvoegen

**Bestandsnaam**: app.js

Tijdens de laatste stap voltooit u de bewerking van het hoofdtoepassingsbestand en geeft u informatie op over de gedefinieerde modellen, archieven, weergaven en controllers. De bronbestanden voor deze resources worden automatisch in de toepassing geladen. Ten slotte wordt de startmethode aangeroepen. Deze maakt de primaire toepassingsweergave 'Basic.main.View' en geeft deze weer.


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

###Uw Sencha Touch-app hosten en uitvoeren

De laatste fase van deze zelfstudie is het hosten en uitvoeren van uw nieuwe app op uw lokale computer.

  1. Blader in de terminal naar de locatie van uw uitgepakte toepassing.

  2. Als u Sencha Cmd wilt gebruiken, voert u de volgende opdrachten uit:

    * *Sencha-app vernieuwen*: hiermee zoekt Sencha Cmd naar alle app-afhankelijkheden en worden eventuele benodigde pakketten gedownload (bijvoorbeeld [Sencha Touch-extensies voor Azure](https://market.sencha.com/extensions/sencha-extensions-for-microsoft-azure)).

    * *Sencha web starten*: hiermee start een lokale webserver met het testen van de toepassing.

    ![Sencha web starten](./media/partner-sencha-mobile-services-get-started/sencha-web-start.png)

  3. Open de URL die in uw terminal in een webbrowser wordt vermeld, om de app te starten (bijvoorbeeld http://localhost:1841).

  4. Typ een stukje zinvolle tekst in de app, zoals 'Voltooi de zelfstudie', en klik vervolgens op **Toevoegen**.

    ![Nieuwe taak](./media/partner-sencha-mobile-services-get-started/new-todo-item.png)

    Er wordt nu een POST-aanvraag verzonden naar de nieuwe mobiele service die wordt gehost in Azure. De gegevens van de aanvraag worden opgenomen in de takentabel.

  5. Klik in de [klassieke Azure Portal] op het tabblad **Gegevens** en klik vervolgens op de takentabel.

    ![Takentabel](./media/partner-sencha-mobile-services-get-started/mobile-data-tab.png)

    U kunt nu door de gegevens bladeren die door de app in de tabel zijn ingevoegd.

    ![In de takentabel bladeren](./media/partner-sencha-mobile-services-get-started/mobile-data-browse.png)

##Volgende stappen
Nu u de instructiehandleiding hebt voltooid, kunt u nagaan hoe u met Sencha aanvullende belangrijke taken uitvoert in Mobile Services.

[Download](https://github.com/arthurakay/sencha-touch-azure-example) een voltooide voorbeeld-app met extra stijlen en functies om te zien wat u nog meer met Sencha Touch kunt doen.

Hier vindt u meer informatie over de Sencha Touch-extensies voor Azure:

  * [Overzicht](http://docs.sencha.com/touch-azure/1.0.0/#!/guide/data_filters) van een voorbeeld-app
  * Hulp via de [Sencha Forums](http://www.sencha.com/forum)
  * Door de [Sencha-documentatie](http://docs.sencha.com/) bladeren
  * Sencha gebruiken met Azure Mobile Services: [(video)](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-126-Using-Sencha-With-Windows-Azure-Mobile-Services)


##Aanvullende bronnen

  * [Sencha Touch downloaden](http://pages.sencha.com/touch-for-azure.html)
  * [Sencha Touch-extensies voor Azure](https://market.sencha.com/extensions/sencha-extensions-for-microsoft-azure)


##Samenvatting

Het voorbeeld dat hier wordt beschreven, vindt u in de Sencha Touch-extensie voor een Azure-pakket. Het is het basisgegevensvoorbeeld in de map met voorbeelden. U vindt hier nog enkele voorbeelden die andere functies van deze extensie belichten. De voorbeelden zijn voorzien van gedetailleerde opmerkingen en uitleg.

Voor meer informatie over hoe u aan de slag kunt gaan met Sencha Touch, raadpleegt u de volledige set [handleidingen](http://docs.sencha.com/touch/#!/guide)


[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- images -->
[0]: ./media/partner-sencha-mobile-services-get-started/finished-app.png

[klassieke Azure Portal]: https://manage.windowsazure.com/


<!--HONumber=Jun16_HO2-->


