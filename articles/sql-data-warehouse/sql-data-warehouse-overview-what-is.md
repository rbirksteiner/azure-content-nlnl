<properties
   pageTitle="Wat is Azure SQL Data Warehouse | Microsoft Azure"
   description="Gedistribueerde database op ondernemingsniveau die geschikt is voor het verwerken van petabytes aan relationele en niet-relationele gegevens. Het is het eerste geavanceerde clouddatawarehouse in de branche dat in enkele seconden kan worden vergroot, verkleind en onderbroken."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/01/2016"
   ms.author="lodipalm;barbkess;mausher;jrj;sonyama;kevin"/>


# Wat is Azure SQL Data Warehouse?

Azure SQL Data Warehouse is een schaalbare clouddatabase die geschikt is voor het verwerken van grote hoeveelheden relationele en/of niet-relationele gegevens. Dankzij de MPP-architectuur (Massively Parallelle Processing) is SQL Data Warehouse berekend op de workload van elke onderneming. 

SQL Data Warehouse:

- Combineert onze bewezen relationele SQL Server-database met onze schaalbare Azure-cloudfuncties. U kunt de rekencapaciteit vergroten, verkleinen, onderbreken of hervatten in enkele seconden.  Hiermee kunt u kosten besparen door de CPU-capaciteit te vergroten of te verkleinen tijdens piek- of daluren.
- Maakt gebruik van ons Azure-platform. Eenvoudig te implementeren en te onderhouden. Volledig fouttolerant dankzij automatische back-ups. 
- Vormt een aanvulling op de SQL Server-omgeving.  Te ontwikkelen met de vertrouwde TSQL en hulpprogramma's van SQL Server.

Lees verder voor meer informatie over de belangrijkste functies van SQL Data Warehouse.

## Geoptimaliseerd

### MPP-architectuur (Massively Parallel Processing)

SQL Data Warehouse maakt gebruik van de MPP-architectuur van Microsoft en is ontworpen voor 's werelds grootste lokale datawarehouses.

Momenteel worden gegevens in de MPP-architectuur verspreid over 60 opslag- en verwerkingseenheden, waarbij niets wordt gedeeld. De gegevens worden opgeslagen in Azure Storage-blobs in Premium-opslag en gekoppeld aan rekenknooppunten voor het uitvoeren van query's. Deze architectuur is bij uitstek geschikt voor het uitvoeren van complexe TSQL-query's volgens het verdeel-en-heersprincipe. Bij het verwerken wordt de query ontleed door het beheerknooppunt, waarna alle porties gegevens parallel worden verwerkt op verschillende rekenknooppunten. 

Door de MPP-architectuur te combineren met de opslagmogelijkheden van Azure, kunt u in SQL Data Warehouse:

- De opslag vergroten of verkleinen, onafhankelijk van de rekencapaciteit.
- De rekencapaciteit vergroten of verkleinen zonder gegevens te verplaatsen. 
- De rekencapaciteit onderbreken zonder gegevensverlies.
- De rekencapaciteit op elk gewenst moment hervatten.

De architectuur wordt hieronder in detail beschreven. 

![Architectuur van SQL Data Warehouse][1]


- **Beheerknooppunt:** het beheerknooppunt 'regelt' het systeem. Het is de front-end met interactie met alle toepassingen en verbindingen. In SQL Data Warehouse werkt het beheerknooppunt op basis van SQL Database en het maken van een verbinding gaat ongeveer hetzelfde. Op de achtergrond coördineert het beheerknooppunt alle benodigde verplaatsingen van gegevens en berekeningen voor het uitvoeren van parallelle query's op uw gedistribueerde gegevens. Wanneer u een TSQL-query naar SQL Data Warehouse verzendt, wordt deze door het beheerknooppunt getransformeerd tot afzonderlijke query's die parallel worden uitgevoerd op de afzonderlijke rekenknooppunten.

- **Rekenknooppunten:** de rekenknooppunten vormen de kracht achter SQL Data Warehouse. Dit zijn SQL-databases waarmee de querystappen worden verwerkt en de gegevens worden beheerd. Wanneer u gegevens toevoegt, worden de rijen door SQL Data Warehouse gedistribueerd met behulp van uw rekenknooppunten. De rekenknooppunten zijn ook de workers waarop de parallelle query's op uw gegevens worden uitgevoerd. Na de verwerking worden de resultaten teruggestuurd naar het beheerknooppunt. Om de query te voltooien, worden de resultaten door het beheerknooppunt geaggregeerd en het definitieve resultaat geretourneerd.


- **Opslag:** uw gegevens worden opgeslagen in Azure Storage-blobs. Bij interacties tussen de rekenknooppunten en uw gegevens worden de gegevens rechtstreeks vanuit de blob-opslag gelezen of ernaar weggeschreven. Omdat opslag in Azure transparant en onbeperkt kan worden uitgebreid, kan dat ook in SQL Data Warehouse. Omdat reken- en opslagcapaciteit niet van elkaar afhankelijk zijn, kan de opslagcapaciteit in SQL Data Warehouse automatisch los van de rekencapaciteit worden geschaald en omgekeerd.  Azure-opslag is ook volledig fouttolerant. Dit vergemakkelijkt het back-up- en herstelproces.
   

- **Data Movement Service:** Data Movement Service (DMS) is de Microsoft-technologie die wordt gebruikt voor het verplaatsen van gegevens van het ene naar het andere knooppunt. DMS geeft de rekenknooppunten toegang tot de benodigde gegevens voor samenvoegingen en aggregaties. DMS is geen Azure-service. Het is een Windows-service die naast SQL Database wordt uitgevoerd op alle knooppunten. Omdat DMS op de achtergrond wordt uitgevoerd, werkt u er niet rechtstreeks mee. Maar wanneer u de queryplannen bekijkt, ziet u dat deze enkele DMS-bewerkingen bevatten omdat er toch op een of andere manier gegevensverplaatsing nodig is om elke query parallel te kunnen uitvoeren.


### Geoptimaliseerde queryprestaties

Naast de verdeel-en-heersstrategie wordt de MPP-methode ondersteund door een aantal specifieke optimalisaties voor datawarehouseprestaties, waaronder:

- een gedistribueerde queryoptimalisatie en een set complexe statistieken voor alle gegevens. Met behulp van informatie over de omvang en distributie van gegevens kunnen query's in de service worden geoptimaliseerd door de kosten van specifieke gedistribueerde querybewerkingen te berekenen.

- In het gegevensverplaatsingsproces zijn geavanceerde algoritmen en technieken geïntegreerd om de gegevens efficiënt te verplaatsen tussen de computerbronnen die nodig zijn voor het uitvoeren van de query. Deze gegevensverplaatsingsbewerkingen zijn ingebouwd en alle optimalisaties voor Data Movement Service vinden automatisch plaats.

- Er wordt standaard gebruikgemaakt van geclusterde kolomopslagindexen. Dankzij kolomopslag is de compressie in SQL Data Warehouse tot wel vijf keer beter dan bij traditionele rij-opslag en zijn de prestaties tot wel tien keer beter. De prestaties van analysequery's waarbij een groot aantal rijen moeten worden gescand, zijn uitstekend bij kolomopslagindexen. 

## Schaalbaar

In de architectuur van SQL Data Warehouse zijn opslag- en rekencapaciteit gescheiden, zodat deze afzonderlijk kunnen worden geschaald. SQL Database is snel en eenvoudig te implementeren. Hierdoor kan de rekencapaciteit in een handomdraai worden uitgebreid. Een bijkomend voordeel is het gebruik van Azure Storage-blobs. Een infrastructuur met blobs zorgt niet alleen voor een stabiele, gerepliceerde opslag, maar biedt ook mogelijkheden voor probleemloze uitbreiding tegen lage kosten.  Door deze combinatie van schaalbare cloudopslag en Azure-rekencapaciteit, kunt u met SQL Data Warehouse zelf kiezen wanneer u meer opslagcapaciteit voor hogere queryprestaties nodig hebt. De rekencapaciteit is eenvoudig te wijzigen met een schuifregelaar in de Azure-portal, maar kan ook worden gepland met TSQL en PowerShell.

Naast de mogelijkheid om de reken- en opslagcapaciteit onafhankelijk van elkaar in te stellen, kunt u uw datawarehouse in SQL Data Warehouse ook volledig onderbreken. Uw opslagcapaciteit blijft behouden, maar alle rekencapaciteit wordt vrijgegeven in de hoofdpool van Azure, waardoor u onmiddellijk geld bespaart. U kunt de berekeningen hervatten wanneer u wilt en direct weer over uw gegevens en rekencapaciteit beschikken voor uw workload.

Het gebruik van rekencapaciteit wordt in SQL Data Warehouse gemeten met behulp van DWU's (SQL Data Warehouse Units). DWU's zijn een maatstaf voor de capaciteit van uw datawarehouse en zijn ontworpen om ervoor te zorgen dat u altijd kunt rekenen op bepaalde prestaties voor uw datawarehouse.  DWU's worden met name gebruikt voor het volgende:

- U kunt uw datawarehouse effectief schalen zonder dat u zich zorgen hoeft te maken over de onderliggende hardware of software.

- U kunt de grootte van uw datawarehouse wijzigen op basis van uw inzicht in de prestaties op DWU-niveau.

- De onderliggende hardware en software van uw exemplaar kunnen worden gewijzigd of verplaatst zonder negatieve gevolgen voor de workload

- De onderliggende architectuur van de service kan worden aangepast zonder de prestaties van uw workload te beïnvloeden.

- Omdat we de prestaties van SQL Data Warehouse continu verbeteren, kunnen we dat zo doen dat de workload schaalbaar is en evenredig wordt verdeeld over het systeem.

### Data Warehouse Units

In dit gedeelte wordt nader ingegaan op Data Warehouse Units (DWU's) als een maatstaf voor drie metrieken die nauw verband houden met de prestaties van datawarehouses bij een bepaalde workload. In het kader van algemene beschikbaarheid streven we ernaar dat deze workloadmetrieken lineair kunnen worden geschaald met het gekozen aantal DWU's voor uw datawarehouse.

**Scan/aggregatie:** deze workloadmetriek is gebaseerd op een standaard-datawarehouse-query waarmee een groot aantal rijen wordt gescand waarop vervolgens een complexe aggregatie wordt uitgevoerd. Dit is een I/O-bewerking waarbij de CPU intensief wordt belast.

**Belasting:** met deze metriek wordt de capaciteit voor het opnemen van gegevens in de service gemeten. Hiertoe wordt met PolyBase een representatieve gegevensset uit een Azure Storage-blob geladen. Deze metriek fungeert als stresstest voor de netwerk- en CPU-aspecten van de service.

**CREATE TABLE AS SELECT (CTAS):** met CTAS wordt de capaciteit voor het kopiëren van een tabel gemeten. Dit omvat het lezen van gegevens uit de opslag, het verdelen van de gegevens over de knooppunten van het systeem en het weer terugschrijven van de gegevens naar de opslag. Dit is een bewerking waarbij de CPU en het netwerk intensief worden belast.

### Wanneer schalen

Over het algemeen willen we het werken met DWU's zo eenvoudig mogelijk houden. Wanneer u snellere resultaten wilt, verhoogt u het aantal DWU's en betaalt u voor betere prestaties.  Wanneer u minder rekencapaciteit nodig hebt, verlaagt u het aantal DWU's en betaalt u alleen voor wat u nodig hebt. Hier volgen enkele voorbeelden van momenten waarop u kunt overwegen het aantal DWU's te wijzigen:

- Wanneer u geen query's hoeft uit te voeren, bijvoorbeeld 's avonds of in het weekend, kunt u de Compute-bronnen onderbreken om alle actieve query's te annuleren en alle DWU's die aan uw datawarehouse zijn toegewezen, te verwijderen.

- Wanneer u een zware laad- of transformatiebewerking wilt uitvoeren, kunt u de capaciteit vergroten (omhoog schalen) zodat uw gegevens sneller beschikbaar zijn.

- Om te bepalen wat voor u de ideale DWU-waarde is, kunt u omhoog en omlaag schalen en na het laden van uw gegevens enkele query's uitvoeren. Omdat schalen zo snel kan, kunt u een aantal verschillende prestatieniveaus uitproberen zonder een verplichting van meer dan een uur aan te gaan.

> [AZURE.NOTE] Houd er rekening mee dat het systeem als gevolg van de architectuur van SQL Data Warehouse mogelijk niet de verwachte prestaties levert bij lagere gegevensvolumes.  Voor een goede indicatie van de prestatievoordelen wordt u aangeraden te beginnen met een gegevensvolume van 1 TB of meer.

## Geïntegreerd

SQL Data Warehouse is gebaseerd op de beproefde relationele database-engine van SQL Server en bevat veel functies die u van een datawarehouse voor ondernemingen verwacht. Als u al bekend bent met Transact-SQL, zult u weinig moeite hebben met de overgang naar SQL Data Warehouse. Of u nu een ervaren of beginnende gebruiker bent, met de voorbeelden in de documentatie kunt u snel aan de slag. Over het algemeen kunt u nadenken over de manier waarop de elementen van de taal van SQL Data Warehouse zijn opgebouwd:

- In SQL Data Warehouse wordt voor veel bewerkingen de syntaxis van Transact-SQL (TSQL) van SQL Server gebruikt. Deze biedt ondersteuning voor diverse traditionele SQL-constructs, zoals opgeslagen procedures, door de gebruiker gedefinieerde functies, tabelpartities, indexen en sorteringen.

- SQL Data Warehouse bevat ook een aantal geavanceerde functies van SQL Server, zoals geclusterde kolomopslagindexen, PolyBase-integratie en gegevensauditing (inclusief beoordeling van bedreigingen).

- Omdat SQL Data Warehouse nog volop in ontwikkeling is, zijn bepaalde elementen van de taal van TSQL die minder gangbaar zijn voor datawarehouse-bewerkingen, of die nog relatief nieuw zijn in SQL Server, mogelijk niet beschikbaar. Zie onze migratiedocumentatie voor meer informatie hierover.

Doordat SQL Server, SQL Data Warehouse, SQL Database en Analytics Platform System de Transact-SQL en diverse functies met elkaar gemeen hebben, kunt u een passende oplossing ontwikkelen voor uw gegevensbehoeften. U kunt besluiten waar u uw gegevens wilt bewaren, op basis van de gewenste prestaties, beveiliging en schaal, en uw gegevens vervolgens van het ene naar het andere systeem verplaatsen.

Naast de vaststelling van de TSQL surface area van SQL Server is SQL Data Warehouse ook compatibel met veel van de hulpprogramma's waar SQL Server-gebruikers wellicht al bekend mee zijn. We hebben ons met name gericht op het integreren van bepaalde categorieën hulpprogramma's voor SQL Data Warehouse, waaronder:

**Traditionele SQL Server-hulpprogramma's:** volledige integratie met SQL Server Analysis Services, Integration Services en Reporting Services is beschikbaar in SQL Data Warehouse.

**Cloud-hulpprogramma's:** SQL Data Warehouse kan worden gebruikt in combinatie met een aantal nieuwe hulpprogramma's in Azure, en is diep geïntegreerd met Azure Data Factory, Stream Analytics, Machine Learning en Power BI.

**Hulpprogramma's van derden:** veel externe leveranciers hebben de integratie van hun hulpprogramma's met SQL Data Warehouse laten certificeren. Bekijk de volledige lijst.

## Hybride

Gecombineerd gebruik van SQL Data Warehouse en PolyBase biedt gebruikers ongekende mogelijkheden om gegevens te verplaatsen binnen hun systeemomgeving, inclusief de mogelijkheid voor geavanceerde hybride scenario's met niet-relationele en lokale gegevensbronnen.

Polybase is eenvoudig te gebruiken en stelt u in staat gebruik te maken van verschillende gegevensbronnen met dezelfde vertrouwde TSQL-opdrachten. Met Polybase kunt u niet-relationele gegevens opvragen uit Azure Storage-blobs, net zoals u gegevens kunt opvragen uit gewone tabellen. Met Polybase kunt u ook niet-relationele gegevens opvragen of niet-relationele gegevens importeren in SQL Data Warehouse.

- In PolyBase worden externe tabellen gebruikt voor toegang tot niet-relationele gegevens. De tabeldefinities worden opgeslagen in SQL Data Warehouse en zijn toegankelijk via SQL en hulpprogramma's zoals normale relationele gegevens.

- Polybase is agnostisch qua integratie. De functies en functionaliteit ervan zijn hetzelfde voor alle bronnen die door Polybase worden ondersteund. In Polybase kunnen gegevensbestanden in diverse indelingen worden gelezen, waaronder bestanden met scheidingstekens of ORC-bestanden.

- PolyBase kan worden gebruikt voor toegang tot blob-opslag die ook wordt gebruikt als opslag voor een HDInsight-cluster. Zo hebt u geavanceerde toegang tot dezelfde gegevens met relationele en niet-relationele hulpprogramma's.

## Volgende stappen

Nu u meer over SQL Data Warehouse weet, kunt u zich gaan verdiepen in de [datawarehouse-workload], het [inrichten] van SQL Data Warehouse en het [laden van voorbeeldgegevens].  Zie desgewenst ook deze informatiebronnen voor SQL Data Warehouse.  

- [Blogs] 
- [Functieverzoeken]
- [Video's]
- [CAT-teamblogs]
- [Ondersteuningsticket maken]
- [MSDN-forum]
- [Stack Overflow-forum]
- [Twitter]


<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Ondersteuningsticket maken]: ./sql-data-warehouse-get-started-create-support-ticket.md
[datawarehouse-workload]: ./sql-data-warehouse-overview-workload.md
[voorbeeldgegevens downloaden]: ./sql-data-warehouse-get-started-manually-load-samples.md
[inrichten]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other Web references-->
[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[CAT-teamblogs]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Functieverzoeken]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN-forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSQLDataWarehouse
[Stack Overflow-forum]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Video's]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse



<!--HONumber=Jun16_HO2-->


