<properties 
    pageTitle="Aan de slag met Azure Search | Microsoft Azure | Azure Search - Aan de slag | DocumentDB | Service voor zoeken in de cloud" 
    description="Met deze zelfstudie kunt u stapsgewijs uw eerste Azure Search-oplossing maken. U leert hoe u een Azure Search-index maakt met DocumentDB-gegevens. Dit is een portal-gebaseerde oefening zonder code, waarin gebruik wordt gemaakt van de wizard Gegevens importeren." 
    services="search" 
    documentationCenter="" 
    authors="HeidiSteen" 
    manager="paulettm" 
    editor=""
    tags="azure-portal"/>

<tags 
    ms.service="search" 
    ms.devlang="na" 
    ms.workload="search" 
    ms.topic="hero-article" 
    ms.tgt_pltfrm="na" 
    ms.date="05/17/2016" 
    ms.author="heidist"/>

# Aan de slag met Azure Search in de portal

Met deze inleiding zonder code kunt u aan de slag met Microsoft Azure Search en maakt u gebruik van de ingebouwde mogelijkheden in de portal. 

In deze zelfstudie wordt ervan uitgegaan dat u over een [Azure DocumentDB-voorbeelddatabase](#apdx-sampledata) beschikt. U kunt deze database heel eenvoudig maken aan de hand van onze gegevens en instructies. U kunt de stappen in deze zelfstudie ook aanpassen aan uw bestaande gegevens in een DocumentDB-database of SQL Database.

> [AZURE.NOTE] Voor deze zelfstudie moet u beschikken over een [Azure-abonnement](../../includes/free-trial-note.md) en een [Azure Search-service](search-create-service-portal.md). 
 
## Uw service vinden

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Open het servicedashboard van uw Azure Search-service. U kunt het dashboard op verschillende manieren vinden.
    - Klik in de snelbalk op **Services zoeken**. Elke ingerichte service in uw abonnement wordt in de snelbalk weergegeven. Als er een zoekservice is gedefinieerd, ziet u **Services zoeken** in de lijst.
    - Klik in de snelbalk op **Bladeren** en typ "zoeken" in het zoekvak om een lijst weer te geven met alle zoekservices die in uw abonnementen zijn gemaakt.

## Controleren of er voldoende ruimte is

Veel klanten beginnen met de gratis service. Deze versie is beperkt tot drie indexen drie gegevensbronnen en drie indexeerfuncties. Zorg ervoor dat er voldoende ruimte is voor extra items voordat u begint. In deze zelfstudie maakt u er van elk object één.

## Een index laden en gegevens laden

Zoekopdrachten worden over een *index* herhaald met doorzoekbare gegevens, metagegevens en constructies, die worden gebruikt om bepaald zoekgedrag te optimaliseren. Als eerste definieert u een index en vult u deze index.

U kunt op verschillende manieren een index maken. Als de gegevens in een archief staan waar u met Azure Search bij kunt, zoals Azure SQL Database, SQL Server op een virtuele machine van Azure of DocumentDB, kunt u heel eenvoudig een index maken en vullen met behulp van de *indexeerfunctie*.

Omdat deze taak portal-gebaseerd is, gebruiken we gegevens uit DocumentDB die met behulp van een indexeerfunctie kunnen worden benaderd via de wizard **Gegevens importeren**. 

Voordat u doorgaat, maakt u een [DocumentDB-voorbeelddatabase](#apdx-sampledata) die u kunt gebruiken voor deze zelfstudie. Vervolgens kunt u de onderstaande stappen uitvoeren.

<a id="defineDS"></a>
#### Stap 1: De gegevensbron definiëren

1. Klik op het dashboard van uw Azure Search-service op **Gegevens importeren** in de opdrachtbalk om een wizard te starten waarmee u een index maakt en deze vervolgens vult.

  ![][7]

2. Klik in de wizard op **Gegevensbron** > **DocumentDB** > **Naam** en typ een naam voor de gegevensbron. Een gegevensbron is een verbindingsobject in Azure Search dat kan worden gebruikt met andere indexeerfuncties. Zodra u de gegevensbron hebt gemaakt, wordt deze beschikbaar gesteld in de "bestaande gegevensbron" in uw service.

3. Kies uw bestaande DocumentDB-account, de database en de verzameling. Als u de voorbeeldgegevens gebruikt, ziet de definitie van de gegevensbron er als volgt uit:

  ![][2]

Zoals u ziet, wordt de query overgeslagen. Dit is omdat we deze keer het bijhouden van gegevens in onze gegevensset niet gebruiken. Als uw gegevensset een veld bevat waarin wordt bijgehouden wanneer een record wordt bijgewerkt, kunt u een Azure Search-indexeerfunctie configureren zodat u deze bijwerkfunctie voor bepaalde updates van uw index kunt gebruiken.

Klik op **OK** om deze stap van de wizard te voltooien.

#### Stap 2: De index definiëren

Klik in de wizard op **Index** en bekijk het ontwerpoppervlak dat wordt gebruikt om een Azure Search-index te maken. In een index moeten minimaal een naam en een verzameling van velden zijn opgenomen, waarbij één veld is gemarkeerd als de documentsleutel. Omdat we werken met een DocumentDB-gegevensset, worden de velden automatisch door de wizard herkend en wordt de index vooraf geladen met velden en toewijzingen voor gegevenstypen. 

  ![][3]

De velden en gegevenstypen worden dus voor u geconfigureerd. U moet echter zelf kenmerken toewijzen. De selectievakjes boven in de lijst met velden zijn *indexkenmerken* die bepalen hoe het veld wordt gebruikt. 

- **Ophalen mogelijk** betekent dat dit veld wordt weergegeven in de lijst met zoekresultaten. U kunt afzonderlijke velden markeren als ontoegankelijk voor zoekresultaten door dit selectievakje uit te schakelen, bijvoorbeeld voor velden die alleen in filterexpressies worden gebruikt. 
- De kenmerken **Filterbaar**, **Sorteerbaar** en **Geschikt voor facetten** bepalen of een veld in een filter, een sorteervolgorde of een facetnavigatiestructuur kan worden gebruikt. 
- **Doorzoekbaar** betekent dat een veld is opgenomen in een zoekopdracht in volledige tekst. Tekenreeksen zijn meestal doorzoekbaar. Numerieke velden en Booleaanse waarden zijn vaak gemarkeerd als niet doorzoekbaar. 

Voordat u deze pagina verlaat, markeert u welke opties u voor de velden in uw index wilt gebruiken (ophalen mogelijk, doorzoekbaar, enzovoort). De meeste velden kunnen worden opgehaald. De meeste tekenreeksvelden zijn doorzoekbaar (u hoeft de sleutel dan niet meer doorzoekbaar te maken). Sommigen velden, zoals genre, orderableOnline, rating en tags zijn ook filterbaar, sorteerbaar en geschikt voor facetten. 
    
Veld | Type | Opties |
------|------|---------|
id | Edm.String | |
albumTitle | Edm.String | Ophalen mogelijk, Doorzoekbaar |
albumUrl | Edm.String | Ophalen mogelijk, Doorzoekbaar |
genre | Edm.String | Ophalen mogelijk, Doorzoekbaar Filterbaar, Sorteerbaar, Geschikt voor facetten |
genreDescription | Edm.String | Ophalen mogelijk, Doorzoekbaar |
artistName | Edm.String | Ophalen mogelijk, Doorzoekbaar |
orderableOnline | Edm.Boolean | Ophalen mogelijk, Filterbaar, Sorteerbaar, Geschikt voor facetten |
tags | Collection(EDM.String) | Ophalen mogelijk, Filterbaar, Geschikt voor facetten |
price | Edm.Double | Ophalen mogelijk, Filterbaar, Geschikt voor facetten |
margin | Edm.Int32 | |
rating | Edm.Int32 | Ophalen mogelijk, Filterbaar, Sorteerbaar, Geschikt voor facetten |
inventory | Edm.Int32 | Ophalen mogelijk |
lastUpdated | Edm.DateTimeOffset | |

De volgende schermafbeelding is een illustratie van een index die is gebouwd volgens de specificatie in de vorige tabel.

 ![][4]

Klik op **OK** om deze stap van de wizard te voltooien.

#### Stap 3: De indexeerfunctie definiëren

Klik in de wizard**Gegevens importeren** op **Indexeerfunctie** > **Naam**, typ een naam voor de indexeerfunctie en gebruik de standaardinstellingen voor alle andere waarden. Dit object definieert een uitvoerbaar proces. Als u dit hebt gemaakt, kunt u een terugkerend schema instellen, maar in dit geval gebruikt u de standaardoptie om de indexeerfunctie een keer uit te voeren zodra u op **OK** klikt. 

De invoer van al uw importgegevens moet helemaal zijn ingevuld en klaar voor gebruik zijn.

  ![][5]

Om de wizard uit te voeren, klikt u op **OK** om het importeren te starten en de wizard te sluiten.

## Voortgang controleren

Om de voortgang te bekijken, gaat u terug naar het servicedashboard. Schuif omlaag en dubbelklik op de tegel **Indexeerfuncties** om de lijst met indexeerfuncties te openen. Als het goed is, wordt de indexeerfunctie die u zojuist hebt gemaakt weergegeven in de lijst en wordt de status 'wordt uitgevoerd' of voltooid weergegeven, samen met het aantal geïndexeerde documenten in Azure Search.

  ![][6]

## Een query op de index uitvoeren

U hebt nu een zoekindex die gereed is om op te vragen. 

**Search explorer** is een queryprogramma dat is ingebouwd in de portal. Het biedt een zoekvak zodat u kunt controleren of een zoekopdracht de invoer retourneert van de gegevens die u verwacht. 

1. Klik op **Search explorer** in de werkbalk.
2. Kijk welke index actief is. Als het niet de index is die u zojuist hebt gemaakt, klikt u op **Index wijzigen** in de opdrachtbalk om de juiste index te selecteren.
2. Laat het zoekvak leeg en klik vervolgens op de knop **Zoeken** om een zoekopdracht uit te voeren op jokertekens die alle documenten retourneert.
3. Voer een aantal query's voor zoekopdrachten in volledige tekst uit. U kunt de resultaten van uw zoekopdrachten met jokertekens bekijken om vertrouwd te raken met artiesten, fotoalbums en genres die u gebruikt in uw zoekopdracht.
4. Probeer andere zoekopdrachten met de [voorbeelden aan het einde van dit artikel](https://msdn.microsoft.com/library/azure/dn798927.aspx) om ideeën op te doen en uw zoekopdracht te wijzigen om zoekreeksen te gebruiken die kunnen worden gevonden in uw index.

## Volgende stappen

Nadat u de wizard één keer hebt uitgevoerd, kunt u teruggaan en de afzonderlijke onderdelen weergeven of wijzigen, zoals de index, indexeerfunctie of de gegevensbron. Bepaalde wijzigingen die u aanbrengt, zoals het wijzigen van het gegevensveldtype, zijn niet toegestaan in de index. De meeste eigenschappen en instellingen kunnen echter wel worden gewijzigd. Als u afzonderlijke onderdelen wilt weergeven, klikt u op **Index**, **Indexeerfunctie** of op de tegel **Gegevensbronnen** op uw dashboard om een lijst met bestaande objecten weer te geven.

Voor meer informatie over andere functies die in dit artikel worden vermeld, gaat u naar deze koppelingen:

- [Indexeerfuncties](search-indexer-overview.md)
- [Een index maken (inclusief een gedetailleerde beschrijving van de indexkenmerken)](https://msdn.microsoft.com/library/azure/dn798941.aspx)
- [Search Explorer](search-explorer.md)
- [Documenten zoeken (inclusief voorbeelden van een querysyntaxis)](https://msdn.microsoft.com/library/azure/dn798927.aspx)

U kunt deze dezelfde werkstroom met de wizard importeren voor andere gegevensbronnen, zoals Azure SQL Database of SQL Server op Azure virtuele machines.

> [AZURE.NOTE] De indexeerfunctie voor het verkennen van Azure Blob-opslag is nieuw, maar deze functie is alleen beschikbaar in de voorbeeldversie en is nog geen optie in de portal. Als u de indexeerfunctie wilt uitproberen, moet u code schrijven. Zie [Azure Blob-opslag in Azure Search indexeren](search-howto-indexing-azure-blob-storage.md) voor meer informatie.
<a id="apdx-sampledata"></a>


## Bijlage: Voorbeeldgegevens in DocumentDB maken

In deze sectie maken we een kleine database in DocumentDB die kan worden gebruikt voor het uitvoeren van de taken in deze zelfstudie.

De volgende instructies bevatten algemene richtlijnen. Deze zijn echter niet volledig. Als u meer hulp nodig met DocumentDB-navigatie in de portal of taken, raadpleegt u de DocumentDB-documentatie. De meeste opdrachten die u nodig hebt vindt u in de opdrachtbalk van de service boven in het dashboard of in de databaseblade. 

  ![][1]

### musisctoredb voor deze zelfstudie maken

1. [Klik hier](https://github.com/HeidiSteen/azure-search-get-started-sample-data) om een ZIP-bestand met de JSON-muziekgegevensbestanden te downloaden. We bieden 246 JSON-documenten voor deze gegevensset.
2. Voeg DocumentDB aan uw abonnement toe en open vervolgens het servicedashboard.
2. Klik op **Database toevoegen** om een nieuwe database te maken met een id van `musicstoredb`. Deze wordt weergegeven in de databasetegel verderop in de pagina nadat deze is gemaakt.
2. Klik op de naam van de database om de databaseblade te openen.
3. Klik op **Verzameling toevoegen** om een verzameling te maken met een id van `musicstorecoll`.
3. Klik op **Documentverkenner**.
4. Klik op **Uploaden**.
5. Navigeer in **Document uploaden** naar de lokale map met de JSON-bestanden die u eerder hebt gedownload. Selecteer JSON-bestanden in batches van 100 of minder.
    - 386. json
    - 387. json
    - . . .
    - 486. json
6. Herhaal dit om de volgende batch bestanden op te halen, totdat u het laatste bestand, 669.json hebt geüpload.
7. Klik op **Queryverkenner** om te controleren of de gegevens zijn geüpload om te voldoen aan de vereisten van de Documentverkenner.

Een eenvoudige manier om dit te doen is met de standaardquery, maar u kunt de standaardquery ook aanpassen zodat hiermee de bovenste 300 items worden geselecteerd (er zijn minder dan 300 items in deze gegevensset).

Als het goed is, ontvangt u JSON-uitvoer, beginnend met documentnummer 386 en eindigend met document 669. Nadat de gegevens zijn geladen, kunt u [teruggaan naar de stappen in deze zelfstudie](#defineDS) voor het bouwen van een index te maken met behulp van de **wizard Gegevens importeren**.


<!--Image references-->
[1]: ./media/search-get-started-portal/AzureSearch-GetStart-Docdbmenu1.png
[2]: ./media/search-get-started-portal/AzureSearch-GetStart-DataSource.png
[3]: ./media/search-get-started-portal/AzureSearch-GetStart-DefaultIndex.png
[4]: ./media/search-get-started-portal/AzureSearch-GetStart-FinishedIndex.png
[5]: ./media/search-get-started-portal/AzureSearch-GetStart-ImportReady.png
[6]: ./media/search-get-started-portal/AzureSearch-GetStart-IndexerList.png
[7]: ./media/search-get-started-portal/search-data-import-wiz-btn.png



<!--HONumber=Jun16_HO2-->


