<properties 
    pageTitle="Vragen over DocumentDB-Database vragen - Veelgestelde vragen | Microsoft Azure" 
    description="Vind antwoorden op veelgestelde vragen over Azure DocumentDB, een NoSQL-documentdatabaseservice voor JSON. Antwoorden op vragen over de capaciteit, prestatieniveaus en schaalmogelijkheden van de database." 
    keywords="Database questions, frequently asked questions, documentdb, azure, Microsoft azure"
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


#Veelgestelde vragen over DocumentDB

## Databasevragen over de basisprincipes van Microsoft Azure DocumentDB

### Wat is Microsoft Azure DocumentDB? 
Microsoft Azure DocumentDB is een uiterst schaalbare NoSQL-documentdatabase-as-a-service die geavanceerde mogelijkheden biedt om query's op schemavrije gegevens uit te voeren, die garant staat voor configureerbare en betrouwbare prestaties en waarmee u snel toepassingen kunt ontwikkelen via een beheerd platform dat wordt ondersteund door de kracht en het bereik van Microsoft Azure. DocumentDB is de juiste oplossing voor web-, gaming-, IoT-toepassingen en mobiele toepassingen wanneer een voorspelbare doorvoer, lage latentie en een gegevensmodel zonder schema belangrijke vereisten zijn. DocumentDB biedt schemaflexibiliteit en geavanceerde indexeermogelijkheden via een systeemeigen JSON-gegevensmodel. Daarnaast biedt het transactionele ondersteuning voor meerdere documenten met geïntegreerde JavaScript.  
  
Zie de pagina [DocumentDB documentatie](https://azure.microsoft.com/documentation/services/documentdb/) voor meer vragen en antwoorden over databases en instructies om deze service te implementeren en te gebruiken.

### Wat voor soort database is DocumentDB?
DocumentDB is een documentgeoriënteerde NoSQL-database waarin gegevens worden opgeslagen in de JSON-indeling.  DocumentDB biedt ondersteuning voor geneste, onafhankelijke gegevensstructuren waarop query's kunnen worden uitgevoerd via een geavanceerde [SQL-querygrammatica](documentdb-sql-query.md) voor DocumentDB. DocumentDB biedt een high-performance transactionele verwerking van JavaScript aan de serverzijde via [opgeslagen procedures, triggers en door de gebruiker gedefinieerde functies](documentdb-programming.md). De database biedt ook ondersteuning voor consistentieniveaus die kunnen worden ingesteld door de ontwikkelaar en de daaraan gekoppelde [prestatieniveaus](documentdb-performance-levels.md).
 
### Bevatten DocumentDB-databases tabellen zoals relationele database (RDBMS)?
Nee, DocumentDB slaat gegevens op in verzamelingen JSON-documenten.  Zie [DocumentDB-resourcemodel en -concepten](documentdb-resources.md) voor meer informatie over DocumentDB-resources. 

### Bieden DocumentDB-databases ondersteuning voor gegevens zonder schema?
Ja, DocumentDB staat toepassingen toe willekeurige JSON-documenten op te slaan zonder schemadefinities of hints. De gegevens zijn onmiddellijk beschikbaar voor het uitvoeren van query's via de DocumentDB SQL-queryinterface.   

### Biedt DocumentDB ondersteuning voor ACID-transactions?
Ja, DocumentDB biedt ondersteuning voor transacties tussen meerder documenten uitgedrukt als in JavaScript opgeslagen procedures en triggers. Transacties worden toegewezen aan één partitie binnen elke verzameling en uitgevoerd met ACID-semantiek als alles of niets, geïsoleerd van andere code en gebruikersaanvragen die gelijktijdig worden uitgevoerd.  Als er uitzonderingen worden veroorzaakt wanneer de JavaScript-toepassingscode wordt uitgevoerd aan de serverkant, wordt de hele transactie teruggedraaid. 

### Wat zijn typische gebruiksvoorbeelden voor DocumentDB?  
DocumentDB is een goede keuze voor nieuwe web-, gaming- en IoT-toepassingen en mobiele toepassingen waarbij automatisch schalen, voorspelbare prestaties, snelle orderresponstijden in milliseconden en de mogelijkheid om query's op schemavrije gegevens uit te voeren van groot belang zijn DocumentDB is uitermate geschikt wanneer u snel toepassingen wilt ontwikkelen en de toepassingsgegevensmodellen wilt herhalen. [DocumentDB wordt doorgaans veel gebruikt](documentdb-use-cases.md) voor toepassingen waarmee door de gebruiker gegenereerde inhoud en gegevens worden beheerd.  

### Hoe biedt DocumentDB voorspelbare prestaties?
Een aanvraageenheid is de maateenheid voor de doorvoer in DocumentDB. Eén RU komt overeen met de doorvoer die nodig is om een document van 1 kB op te halen. Alle bewerkingen in DocumentDB, inclusief lees-en schrijfbewerkingen, SQL-query's en opgeslagen procedure-uitvoeringen, hebben een deterministische aanvraageenheidwaarde op basis van de doorvoer die is vereist om de bewerking te voltooien. In plaats van na te denken over uw CPU, IO en geheugen en wat hun precieze impact op de doorvoer van uw toepassing is, kunt u ook denken in termen van één aanvraageenheid.

Elke DocumentDB-verzameling kan worden gereserveerd met ingerichte doorvoer in termen van aanvraageenheden aan doorvoer per seconde. U kunt een benchmark-test uitvoeren voor afzonderlijke aanvragen, ongeacht de grootte van de toepassing, om de aanvraageenheidwaarden te meten en verzamelingen in te richten om het totale aantal aanvraageenheden voor alle aanvragen te verwerken. U kunt de doorvoer van uw verzameling omhoog of omlaag schalen naargelang de behoeften van uw toepassing. Lees het artikel [Prestaties en capaciteit beheren](documentdb-manage.md) voor meer informatie over aanvraageenheden en het bepalen van de behoeften van uw verzameling.

### Is DocumentDB compatibel met HIPAA?
Ja, DocumentDB is compatibel met HIPAA? HIPAA stelt vereisten vast voor het gebruik, de openbaarmaking en de beveiliging van individueel identificeerbare gezondheidsinformatie. Zie [Microsoft Trust Center](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA) voor meer informatie.

### Wat zijn de opslaglimieten van DocumentDB? 
Er is geen theoretische limiet voor de totale hoeveelheid gegevens die in een verzameling in DocumentDB kan worden opgeslagen. Als u meer dan 250 GB aan gegevens in één verzameling wilt opslaan, kunt u [contact opnemen met de ondersteuning](documentdb-increase-limits.md) om uw accountquotum te verhogen. 

### Wat zijn de doorvoerlimieten van DocumentDB? 
Er is geen theoretische limiet voor de totale hoeveelheid doorvoer die een verzameling in DocumentDB kan ondersteunen als uw workload redelijk gelijkmatig kan worden verdeeld over een voldoende aantal partitiesleutels. Als u behoefte hebt aan meer dan 250.000 aanvraageenheden per seconde per verzameling of account, kunt u [contact opnemen met de ondersteuning](documentdb-increase-limits.md) om uw accountquotum te verhogen. 

### Wat kost Microsoft Azure DocumentDB?
Zie [Prijzen van DocumentDB](https://azure.microsoft.com/pricing/details/documentdb/) voor meer informatie. De kosten voor DocumentDB worden bepaald door het aantal gebruikte verzamelingen, het aantal uur dat de verzamelingen online zijn en de verbruikte opslag en ingerichte doorvoer voor elke verzameling. 

### Is er een gratis account beschikbaar?
Als u nog niet bekend bent met Azure kunt u zich aanmelden voor een [gratis Azure-account](https://azure.microsoft.com/pricing/free-trial/). U hebt dan 30 dagen en € 170 om alle Azure-services te proberen. Als u een Visual Studio-abonnement hebt, komt u in aanmerking voor [$ 150 aan gratis Azure-tegoed per maand](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), die u kunt gebruiken voor elke willekeurige Azure-service.  

### Hoe kan ik aanvullende ondersteuning voor DocumentDB krijgen?
Als u hulp nodig hebt, kunt contact met ons opnemen via [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-documentdb), de [Azure DocumentDB MSDN Developer-forums](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureDocumentDB), of een [persoonlijk chatgesprek met het technische team van DocumentDB](http://www.askdocdb.com/) plannen. Volg ons op [Twitter](https://twitter.com/DocumentDB) om op de hoogte te blijven van het laatste nieuws en de nieuwste functies met betrekking tot DocumentDB.

## Microsoft Azure DocumentDB instellen?

### Hoe meld ik mij aan voor Microsoft Azure DocumentDB?
Microsoft Azure DocumentDB is beschikbaar in [Azure Portal][azure-portal].  U moet zich eerst aanmelden voor een Microsoft Azure-abonnement.  Zodra u zich hebt aangemeld voor een Microsoft Azure-abonnement, kunt u een DocumentDB-account toevoegen aan uw Azure-abonnement. Zie [Een DocumentDB-databaseaccount maken](documentdb-create-account.md) voor instructies om een DocumentDB-account toe te voegen.   

### Wat is een hoofdsleutel?
Een hoofdsleutel is een beveiligingstoken voor toegang tot alle resources voor een account. Personen met de sleutel hebben lees- en schrijftoegang tot alle resources in het databaseaccount. Wees voorzichtig met het distribueren van hoofdsleutels. De primaire en secundaire hoofdsleutel zijn beschikbaar in de blade **Sleutels ** van [Azure Portal][azure-portal]. Zie [Toegangssleutels weergeven, kopiëren en opnieuw genereren](documentdb-manage-account.md#keys) voor meer informatie over sleutels.

### Hoe maak ik een database?
U kunt databases maken met [Azure Portal](), zoals wordt beschreven in [Een DocumentDB-database maken](documentdb-create-database.md), een van de [DocumentDB SDK's](documentdb-sdk-dotnet.md) of via de [REST API's](https://msdn.microsoft.com/library/azure/dn781481.aspx).  

### Wat is een verzameling?
Een verzameling is een container van JSON-documenten en de bijbehorende JavaScript-toepassingslogica. Een verzameling is een factureerbare entiteit waarvan de [kosten](documentdb-performance-levels.md) worden bepaald door het prestatieniveau dat aan de verzameling is gekoppeld. Verzamelingen kunnen meerdere partities/servers omvatten en kunnen worden geschaald voor een vrijwel onbeperkte hoeveelheid opslag of doorvoer.

Verzamelingen zijn ook de factureringsentiteiten voor DocumentDB. Elke verzameling wordt per uur gefactureerd op basis van de ingerichte doorvoer en de gebruikte opslagruimte. Zie [Prijzen van DocumentDB](https://azure.microsoft.com/pricing/details/documentdb/) voor meer informatie.  

### Hoe stel ik gebruikers en machtigingen in?
U kunt gebruikers en machtigingen maken met een van de [DocumentDB SDK's](documentdb-sdk-dotnet.md) of via de [REST API's](https://msdn.microsoft.com/library/azure/dn781481.aspx).   

## Databasevragen over het ontwikkelen van toepassingen met Microsoft Azure DocumentDB

### Hoe start ik met het ontwikkelen van toepassingen met DocumentDB?
Er zijn [SDK's](documentdb-sdk-dotnet.md) beschikbaar voor .NET, Python, Node.js, JavaScript en Java.  Ontwikkelaars kunnen ook gebruikmaken van de [RESTful HTTP API's](https://msdn.microsoft.com/library/azure/dn781481.aspx) om te communiceren met DocumentDB-resources van verschillende platformen en met verschillende talen. 

Voorbeelden voor de DocumentDB [.NET](documentdb-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](documentdb-nodejs-samples.md) en [Python](documentdb-python-samples.md) SDK's zijn beschikbaar op GitHub.

### Biedt DocumentDB ondersteuning voor SQL?
De DocumentDB SQL-querytaal is een uitgebreide subset van de queryfunctionaliteit die wordt ondersteund door SQL. De DocumentDB SQL-querytaal biedt geavanceerde hiërarchische en relationele operators en uitbreidingsmogelijkheden via door de gebruiker gedefinieerde functies (UDF's) op basis van JavaScript. De JSON-grammatica biedt de mogelijkheid om de JSON-documenten te modelleren als structuren met labels als structuurknooppunten, die zowel door de automatische indexeringstechnieken als het SQL-querydialect van DocumentDB worden gebruikt.  Zie het artikel [Query op DocumentDB uitvoeren][query] voor meer informatie over het gebruik van de SQL-grammatica.

### Welke gegevenstypen worden ondersteund door DocumentDB?
De primitieve gegevenstypen die in DocumentDB worden ondersteund, zijn hetzelfde als voor JSON. JSON heeft een eenvoudig typesysteem dat bestaat uit tekenreeksen, cijfers (IEEE754 dubbele precisie) en booleaanse waarden - waar, onwaar en null-waarden.  Complexe gegevenstypen zoals DateTime, Guid Int64 en Geometry kunnen worden gebruikt, zowel in JSON als in DocumentDB, door geneste objecten met de operator { } en matrices met de operator [ ] te maken. 

### Hoe biedt DocumentDB gelijktijdigheid?
DocumentDB biedt ondersteuning voor optimistisch gelijktijdigheidbeheer (OCC) via HTTP-entity-tags of ETags. Elke DocumentDB-resource heeft een ETag en DocumentDB-clients nemen in hun nieuwste leesversie in schrijfaanvragen op. Als de ETag actueel is, wordt de wijziging doorgevoerd. Als de waarde extern is gewijzigd, wordt de schrijfbewerking door de server geweigerd en wordt de responscode 'HTTP 412 Precondition failure' weergegeven. Clients moeten de nieuwste versie van de resource lezen en de aanvraag opnieuw uitvoeren. 

### Hoe voer ik transacties in DocumentDB uit?
DocumentDB biedt ondersteuning voor taalgeïntegreerde transacties via in JavaScript opgeslagen procedures en triggers. Alle databasebewerkingen in scripts worden uitgevoerd met het isolatieniveau Snapshot binnen het bereik van de verzameling als dit een verzameling van één partitie is, of binnen het bereik van documenten met dezelfde waarde voor de partitiesleutel binnen een verzameling, als de verzameling is gepartitioneerd. Er wordt aan het begin van de transactie een schermopname van de documentversies (ETags) gemaakt en de transactie wordt alleen uitgevoerd als het script kan worden uitgevoerd. Als JavaScript een fout genereert, wordt de transactie teruggedraaid. Zie [Programmeren op de DocumentDB-server](documentdb-programming.md) voor meer informatie.

### Hoe kan ik bulksgewijs documenten in DocumentDB invoegen? 
Er zijn drie manieren om bulksgewijs documenten in DocumentDB in te voegen:

- Het hulpprogramma voor gegevensmigratie, zoals beschreven in [Gegevens importeren in DocumentDB](documentdb-import-data.md).
- Documentverkenner in Azure Portal, zoals beschreven in [Bulksgewijs documenten toevoegen met Documentverkenner](documentdb-view-json-document-explorer.md#BulkAdd).
- Opgeslagen procedures, zoals beschreven in [Programmeren op de DocumentDB-server](documentdb-programming.md).

### Biedt DocumentDB ondersteuning voor het opslaan in de cache van resourcekoppelingen?
Ja, omdat DocumentDB een RESTful-service is, zijn de resourcekoppelingen onveranderbaar en kunnen ze worden opgeslagen in de cache. DocumentDB-clients kunnen de header 'If-None-Match' opgegeven voor leesbewerkingen voor alle resources, zoals documenten of verzamelingen, en hun lokale kopieën alleen bijwerken wanneer de serverversie is gewijzigd. 




[azure-portal]: https://portal.azure.com
[query]: documentdb-sql-query.md
 


<!--HONumber=Jun16_HO2-->


