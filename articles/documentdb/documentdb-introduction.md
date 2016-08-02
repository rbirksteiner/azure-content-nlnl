<properties 
    pageTitle="Inleiding tot DocumentDB, een JSON-database | Microsoft Azure" 
    description="Meer informatie over Azure DocumentDB, een NoSQL JSON-database. Deze documentdatabase is gebouwd voor big data, een elastische schaalbaarheid en een hoge beschikbaarheid." 
    keywords="json database, document database"
    services="documentdb" 
    authors="mimig1" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="03/30/2016" 
    ms.author="mimig"/>

# Inleiding tot DocumentDB: een NoSQL JSON-database

Azure DocumentDB is een volledig beheerde NoSQL-databaseservice die is gebouwd voor snelle en voorspelbare prestaties, een hoge beschikbaarheid, automatisch schalen en een gebruiksvriendelijke manier van ontwikkelen. Dankzij het flexibele gegevensmodel, de consistente lage latenties en geavanceerde querymogelijkheden is DocumentDB uitermate geschikt voor webtoepassingen, mobiele toepassingen, IoT-toepassingen en vele andere toepassingen die u probleemloos moet kunnen schalen.

Als u snel meer over deze JSON-database wilt leren en een demonstratie wilt bekijken, volgt u de volgende drie stappen: 

1. Bekijk de video [What is DocumentDB?](https://azure.microsoft.com/documentation/videos/what-is-azure-documentdb/) (Wat is DocumentDB?) waarin u in twee minuten kennismaakt met de voordelen van het gebruik van DocumentDB.
2. Bekijk de drie minuten durende video [Create DocumentDB on Azure](https://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/) (DocumentDB op Azure maken), waarin wordt uitgelegd hoe u aan de slag kunt met DocumentDB door gebruik te maken van Azure Portal.
3. Bezoek de [Query Playground](http://www.documentdb.com/sql/demo)(Queryspeelplaats) waar u verschillende activiteiten kunt doorlopen om meer te leren over de geavanceerde queryfunctionaliteit die beschikbaar is in DocumentDB. Ga vervolgens naar het tabblad Sandbox en voer uw eigen SQL-query's uit en experimenteer met DocumentDB.

Keer vervolgens terug naar dit artikel om uw kennis te verdiepen en waarin de volgende vragen worden beantwoord:  

-   [Wat is DocumentDB en wat is de waarde ervan voor moderne toepassingen?](#what-is-azure-documentdb)
-   [Hoe worden mijn gegevens in DocumentDB beheerd en hoe zijn ze toegankelijk?](#data-management)
-   [Hoe ontwikkel ik toepassingen met DocumentDB?](#develop)
-   [Wat zijn de volgende stappen die ik moet uitvoeren om een DocumentDB-toepassing te bouwen?](#next-steps)  

## Wat is Azure DocumentDB?  

Moderne toepassingen kunnen snel zeer grote hoeveelheden gegevens produceren, gebruiken en verwerken. De ontwikkeling van deze toepassingen verloopt razendsnel en dat geldt ook voor het onderliggende gegevensschema. Als reactie hierop kiezen ontwikkelaars steeds vaker voor NoSQL-documentdatabases zonder schema. Hiermee beschikken ze over een eenvoudige, snelle, schaalbare oplossing voor de opslag en verwerking van gegevens, terwijl ze tegelijkertijd snel herhalingen voor toepassingsgegevensmodellen en ongestructureerde gegevensfeeds kunnen uitvoeren. Veel van de databases zonder schema bieden echter geen ondersteuning voor complexe query's en transactionele verwerking, waardoor het lastig is om geavanceeerd gegevensbeheer te realiseren. Dit is waar DocumentDB van pas komt. Microsoft heeft DocumentDB ontwikkeld om aan deze vereisten te voldoen met betrekking tot het beheren van gegevens voor moderne toepassingen.

DocumentDB is een echte NoSQL-databaseservice zonder schema die is ontwikkeld voor moderne mobiele toepassingen en web-, gaming- en IoT-toepassingen. DocumentDB biedt consistent snelle lees- en schrijfbewerkingen, schemaflexibiliteit en de mogelijkheid om een database naar wens eenvoudig omhoog of omlaag te schalen. Voor DocumentDB is geen schema vereist voor de JSON-documenten die worden geïndexeerd. Alle documenten in de database worden standaard automatisch geïndexeerd, er wordt geen schema verwacht of vereist en er hoeven geen secundaire indexen te worden gemaakt. DocumentDB is geschikt voor complexe ad-hocquery's die gebruikmaken van een SQL-taal, ondersteunt goed gedefinieerde consistentieniveaus, heeft de JavaScript-taal geïntegreerd en kan meerdere documenttransacties verwerken door gebruik te maken van het vertrouwde programmeermodel met opgeslagen procedures, triggers en UDF's. 

Als JSON-database biedt DocumentDB automatisch ondersteuning voor JSON-documenten, waardoor een toepassingsschema eenvoudig kan worden herhaald. Daarnaast biedt DocumentDB ondersteuning voor toepassingen die gegevensmodellen met sleutel-waardeparen, documenten of tabellen nodig hebben. DocumentDB omarmt de alomtegenwoordigheid van JSON en JavaScript, waardoor onjuiste overeenkomsten tussen de door de toepassing gedefinieerde objecten en de databaseschema worden geëlimineerd. Door de diepe integratie van JavaScript kunnen ontwikkelaars de toepassingslogica op efficiënte wijze en rechtstreeks binnen de database-engine in een databasetransactie uitvoeren. 

Azure DocumentDB biedt de volgende belangrijke mogelijkheden en voordelen:

-   **Elastische schaalbare doorvoer en opslag:** U kunt uw DocumentDB JSON-database eenvoudig omhoog of omlaag schalen om te voldoen aan de behoeften van uw toepassing. Uw gegevens worden opgeslagen op SSD-schijven (Solid State Disks) voor lage voorspelbare latenties. DocumentDB ondersteunt containers voor het opslaan van de JSON-gegevens die verzamelingen worden genoemd, waarvan de opslaggrootte en ingerichte doorvoer vrijwel onbeperkt kan worden geschaald. Wanneer uw toepassing groeit, kunt u DocumentDB probleemloos schalen met voorspelbare prestaties. 

-   **Ad-hocquery's met bekende SQL-syntaxis:** U kunt heterogene JSON-documenten in DocumentDB opslaan en query's op deze documenten uitvoeren via een bekende SQL-syntaxis. DocumentDB maakt gebruik van een uiterst gelijktijdige, vergrendelingsvrije, indexeringstechnologie op basis van een logboekenstructuur om alle documentinhoud automatisch te indexeren. Zodoende kunt u realtime query's uitvoeren zonder dat u schemahints, secundaire indexen of weergaven hoeft op te geven. Zie [Query's in DocumentDB uitvoeren](documentdb-sql-query.md) voor meer informatie. 

-   **Uitvoeren van JavaScript in de database:** Express-toepassingslogica als opgeslagen procedures en triggers en door de gebruiker gedefinieerde functies (UDF's) met standaard-JavaScript. Zodoende kan uw toepassingslogica worden toegepast op gegevens zonder dat u zich zorgen hoeft te maken over onjuiste overeenkomsten tussen de toepassing en het databaseschema. Met DocumentDB kan de JavaScript-toepassingslogica volledig transactioneel en rechtstreeks worden uitgevoerd in de database-engine. Dankzij de diepe integratie van JavaScript kunnen de bewerkingen INSERT, REPLACE, DELETE en SELECT als geïsoleerde transactie worden uitgevoerd vanuit een JavaScript-programma. Zie [Programmeren op de DocumentDB-server](documentdb-programming.md) voor meer informatie.

-   **Instelbare consistentieniveaus:** Selecteer een van de vier goed gedefinieerde consistentieniveaus voor een optimale balans tussen de consistentie en prestaties. Voor query's en leesbewerkingen biedt DocumentDB vier verschillende consistentieniveaus: sterk, gebonden-verouderd, sessie en mogelijk. Op basis van deze gedetailleerde, goed gedefinieerde consistentieniveaus kunt u een goede balans vinden tussen de consistentie, beschikbaarheid en latentie. Zie [Consistentieniveaus gebruiken om de beschikbaarheid en prestaties in DocumentDB te maximaliseren](documentdb-consistency-levels.md) voor meer informatie.

-   **Volledig beheerd:** Elimineer de noodzaak om database- en machineresources te beheren. U hoeft voor een volledig beheerde Microsoft Azure-service geen virtuele machines te beheren, software te implementeren en te configureren, schaalwijzigingen te beheren of complexe upgrades voor gegevenslagen uit te voeren. Er wordt automatisch een back-up van elke database gemaakt en de databases worden automatisch beveiligd tegen regionale fouten. U kunt gemakkelijk een DocumentDB-account toevoegen en capaciteit inrichten als u dit nodig hebt, waardoor u zich kunt richten op uw toepassing in plaats van het besturingssysteem en het beheren van uw database. 

-   **Open ontwerp:** U kunt snel aan de slag door gebruik te maken van bestaande vaardigheden en hulpprogramma's. U kunt op een eenvoudige en gebruiksvriendelijke manier programmeren voor DocumentDB. Bovendien hebt u geen nieuwe hulpprogramma's nodig en hoeft u geen aangepaste uitbreidingen voor JSON of JavaScript te gebruiken. De volledige databasefunctionaliteit, inclusief CRUD-, query- en JavaScript-verwerking, is toegankelijk via een eenvoudige RESTful HTTP-interface. DocumentDB is geschikt voor bestaande indelingen, talen en standaarden, terwijl daarnaast waardevolle databasemogelijkheden worden geboden.

U kunt DocumentDB gebruiken voor het opslaan van flexibele gegevenssets waarvoor query's moeten worden opgehaald en die transactioneel moeten worden verwerkt. Toepassingsscenario's kunnen gebruikersgegevens voor interactieve webtoepassingen, mobiele toepassingen en gamingtoepassing omvatten, maar ook het opslaan, ophalen en verwerken van JSON-gegevens die zijn gegenereerd met een IoT-apparaat. U kunt elk volume aan JSON-documenten opslaan in een database, aangezien DocumentDB uitermate geschikt is voor toepassingen die op grote schaal op internet worden uitgevoerd.

##<a name="data-management"></a>Azure DocumentDB-resources
De gegevens in Azure DocumentDB worden beheerd via goed gedefinieerde databaseresources. Deze resources worden gerepliceerd voor hoge beschikbaarheid en zijn uniek adresseerbaar op basis van hun logische URI. DocumentDB biedt voor alle resources een eenvoudig RESTful-programmeermodel op basis van HTTP. 

Het DocumentDB-databaseaccount is een unieke naamruimte die u toegang biedt tot Azure DocumentDB. Voordat u een databaseaccount kunt maken, moet u een Azure-abonnement hebben. Dit abonnement geeft u toegang tot diverse Azure-services. 

Alle resources binnen DocumentDB worden gemodelleerd en opgeslagen als JSON-documenten. Resources worden beheerd als items, oftewel JSON-documenten met metagegevens, en als feeds, oftewel verzamelingen items. Sets met items bevinden zich in hun respectieve feeds.

De onderstaande afbeelding toont u relaties tussen de DocumentDB-resources:

![De hiërarchische relatie tussen resources in DocumentDB, een NoSQL JSON-database][1] 

Een databaseaccount bestaat uit een set databases die elk meerdere verzamelingen bevatten. Elk verzameling kan opgeslagen procedures, triggers, UDF's, documenten en verwante bijlagen bevatten. Er zijn ook gebruikers aan een database gekoppeld. Elke gebruiker beschikt over een set machtigingen voor toegang tot verschillende andere verzamelingen, opgeslagen procedures, triggers, UDF's, documenten of bijlagen. Databases, gebruikers, machtigingen en verzamelingen zijn door het systeem gedefinieerde resources met bekende schema's. Documenten, opgeslagen procedures, triggers, UDF's en bijlagen bevatten echter willekeurige, door de gebruiker gedefinieerde JSON-inhoud.  

##<a name="develop"></a> Ontwikkelen met Azure DocumentDB
Azure DocumentDB ontsluit resources via een REST API die kan worden aangeroepen via elke taal waarmee HTTP-/HTTPS-aanvragen kunnen worden gemaakt. Daarnaast biedt DocumentDB programmeringsbibliotheken voor verschillende veelgebruikte talen. Dankzij deze bibliotheken kunt u in vele opzichten eenvoudiger met Azure DocumentDB werken, doordat ze de gegevens voor adrescaching, uitzonderingsbeheer, automatische nieuwe pogingen enzovoort verwerken. Bibliotheken zijn momenteel beschikbaar voor de volgende talen en platformen:  

Downloaden | Documentatie
--- | ---
[.NET SDK](http://go.microsoft.com/fwlink/?LinkID=402989) | [.NET-bibliotheek](https://msdn.microsoft.com/library/azure/dn948556.aspx)
[Node.js SDK](http://go.microsoft.com/fwlink/?LinkID=402990) | [Node.js-bibliotheek](http://azure.github.io/azure-documentdb-node/)
[Java SDK](http://go.microsoft.com/fwlink/?LinkID=402380) | [Java-bibliotheek](http://azure.github.io/azure-documentdb-java/)
[JavaScript SDK](http://go.microsoft.com/fwlink/?LinkID=402991) | [JavaScript-bibliotheek](http://azure.github.io/azure-documentdb-js/)
N.v.t. | [JavaScript SDK op de server](http://azure.github.io/azure-documentdb-js-server/)
[Python SDK](https://pypi.python.org/pypi/pydocumentdb) | [Python-bibliotheek](http://azure.github.io/azure-documentdb-python/)

DocumentDB biedt naast de eenvoudige maak-, lees-, bijwerk- en verwijderbewerkingen een geavanceerde SQL-queryinterface voor het ophalen van JSON-documenten en ondersteuning aan de serverzijde voor de transactionele uitvoering van JavaScript-toepassingslogica. De interfaces voor het uitvoeren van query's en scripts zijn beschikbaar via alle platformbibliotheken en REST API's. 

### SQL-query
Azure DocumentDB biedt ondersteuning voor documentquery's die gebruikmaken van een SQL-taal, die verankerd ligt in het JavaScript-typesysteem, en voor expressies met ondersteuning voor relationele, hiërarchische en ruimtelijke query's. De quertytaal van DocumentDB is een eenvoudige maar krachtige interface om query's op JSON-documenten uit te voeren. De taal ondersteunt een subset van de ANSI SQL-grammatica en zorgt voor een diepe integratie van JavaScript-object-, -matrix-, -objectconstructie- en functieaanroepen. DocumentDB levert het querymodel zonder een expliciet schema of indexeringshints van de ontwikkelaar.

Door de gebruiker gedefinieerde functies (UDF's) kunnen worden geregistreerd met DocumentDB en er kan als onderdeel van een SQL-query naar worden verwezen, waarmee de grammatica ter ondersteuning van aangepaste toepassingslogica wordt uitgebreid. Deze UDF zijn geschreven als JavaScript-programma's en worden uitgevoerd binnen de database. 

Voor .NET-ontwikkelaars bevat DocumentDB een LINQ-queryprovider als onderdeel van de [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.aspx). 

### Transacties en JavaScript uitvoeren
Met DocumentDB kunt u toepassingslogica schrijven als benoemde programma's die volledig zijn geschreven in JavaScript. Deze programma's worden geregistreerd voor een verzameling en kunnen databasebewerkingen uitvoeren op de documenten binnen een bepaalde verzameling. JavaScript kan worden geregistreerd voor uitvoering als een trigger, opgeslagen procedure of als een door de gebruiker gedefinieerde functie. Met triggers en opgeslagen procedures kunt u documenten maken, lezen, bijwerken en verwijderen, terwijl de functies die door de gebruiker zijn gedefinieerd, worden uitgevoerd als onderdeel van de logica voor het uitvoeren van een query, zonder schrijftoegang tot de verzameling.

De JavaScript-uitvoering in DocumentDB is gemodelleerd naar de concepten die worden ondersteund door relationele databasesystemen, met JavaScript als een moderne vervanger van Transact-SQL. Alle JavaScript-logica wordt uitgevoerd binnen een ambient ACID-transactie met het isolatieniveau Snapshot. Als er tijdens de uitvoering een JavaScript-uitzondering optreedt, wordt de volledige transactie afgebroken.

## Volgende stappen
Als u al een Azure-account hebt, kunt u aan de slag met DocumentDB in [Azure Portal](https://portal.azure.com/#gallery/Microsoft.DocumentDB) door [een DocumentDB-databaseaccount te maken](documentdb-create-account.md).

Als u geen Azure-account hebt, kunt u het volgende doen:

- Aanmelden voor een [gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/). U hebt dan 30 dagen en € 170 om alle Azure-services te proberen. 
- Als u een MSDN-abonnement hebt, komt u in aanmerking voor [$ 150 aan gratis Azure-tegoed per maand](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Dit tegoed kunt u gebruiken voor elke willekeurige Azure-service. 

Als u vervolgens meer wilt leren, kunt u ons [leertraject](https://azure.microsoft.com/documentation/learning-paths/documentdb/) bezoeken om door alle beschikbare leerresources te bladeren. 


[1]: ./media/documentdb-introduction/json-database-resources1.png
 



<!--HONumber=Jun16_HO2-->


