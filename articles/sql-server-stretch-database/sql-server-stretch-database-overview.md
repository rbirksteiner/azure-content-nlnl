<properties
    pageTitle="Overzicht Stretch Database | Microsoft Azure"
    description="Informatie over hoe Stretch Database uw historische gegevens transparant en veilig naar de Microsoft Azure-cloud migreert."
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager=""
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/17/2016"
    ms.author="douglasl"/>

# Overzicht Stretch Database

Stretch Database migreert uw historische gegevens transparant en veilig naar de Microsoft Azure-cloud.

Als u meteen met Stretch Database aan de slag wilt, bekijk dan [Get started by running the Enable Database for Stretch Wizard](sql-server-stretch-database-wizard.md).

## Wat zijn de voordelen van Stretch Database?
Stretch Database biedt de volgende voordelen:

**Kosten\-efficiënte beschikbaarheid van koude gegevens** Breid warme en koude transactionele gegevens dynamisch van de SQL Server naar Microsoft Azure uit met SQL Server Stretch Database. In tegenstelling tot traditionele opslag van koude gegevens zijn uw gegevens altijd online en beschikbaar om op te vragen. U kunt gegevens langer bewaren zonder dat u door grote tabellen, zoals de ordergeschiedenis van klanten, uw budget overschrijdt. Profiteer van de lage kosten van Azure in plaats van dure opslag on\--premises op te schalen. U kiest in de Azure Portal de prijscategorie en configureert de instellingen om de kosten onder controle te houden. Schaal naar behoefte omhoog of omlaag. Ga naar de pagina [SQL Server Stretch Database Pricing](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/) voor meer informatie.

**Geen wijzigingen in query's of toepassingen vereist** Naadloos toegang tot uw SQL Server-gegevens, ongeacht of deze zich on\--premises bevinden of naar de cloud zijn uitgebreid.  U stelt u het beleid vast dat bepaalt waar de gegevens worden opgeslagen, en SQL Server verplaatst op de achtergrond de gegevens. De volledige tabel is altijd online en opvraagbaar. Bovendien vereist Stretch Database geen wijzigingen in bestaande query's of toepassingen – de locatie van de gegevens is volledig transparant voor de toepassing.

**Stroomlijnt het on\--premises gegevensonderhoud** Beperk on\--premises onderhoud en opslag van uw gegevens. Backups voor uw gegevens in de cloud worden automatisch uitgevoerd. Backups voor uw on\--premises gegevens worden sneller uitgevoerd en tijdens de onderhoudssessie voltooid. Uw on\--premises opslagbehoeften worden aanzienlijk beperkt. Azure-opslag is 80% goedkoper dan een on\--premises SSD toevoegen.

**Beveiligt uw gegevens zelfs tijdens migratie** Breid uw belangrijkste toepassingen met een gerust hart veilig naar de cloud uit. Always Encrypted van SQL-Server versleutelt uw gegevens tijdens de migratie. Beveiliging op rijniveau (Row Level Security - RLS) en andere geavanceerde functies van de SQL Server-beveiliging werken ook met Stretch Database samen om uw gegevens te beveiligen.

## Wat doet Stretch Database?
Nadat u Stretch Database voor een SQL Server-exemplaar, een database en ten minste één tabel heeft ingeschakeld, begint deze op de achtergrond uw historische gegevens naar Azure te migreren.

-   Als u historische gegevens in een afzonderlijke tabel heeft opgeslagen, kunt u de gehele tabel migreren.

-   Als de tabel zowel historische als huidige gegevens bevat, kunt u een filterpredikaat opgeven om de rijen die u wilt migreren te selecteren.

Stretch Database zorgt ervoor dat er geen gegevens verloren gaan als er een fout optreedt tijdens de migratie. Het bevat ook pogingslogica om verbindingsproblemen op te lossen die tijdens de migratie kunnen optreden. In een dynamische beheerweergave wordt de status van de migratie weergegeven

U kunt de gegevensmigratie onderbreken om problemen op de lokale server op te lossen of om de beschikbare netwerkbandbreedte te maximaliseren.

U hoeft bestaande query's en klantenapps niet te wijzigen. Zelfs tijdens de gegevensmigratie heeft u naadloos toegang tot lokale en externe gegevens. Bij externe query's treedt een korte latentietijd op, maar deze latentie merkt u alleen wanneer u historische gegevens opvraagt.

![Overzicht Stretch Database][StretchOverviewImage1]

## Is Stretch Database iets voor u?
Als u het volgende kunt beamen, kan Stretch Database wellicht aan uw wensen voldoen en uw problemen oplossen.

|Als u een zakelijke besluitvormer bent|Als u een DBA bent|
|------------------------------|-------------------|
|Ik moet transactionele gegevens lange tijd bewaren.|Mijn tabellen worden te groot.|
|Ik moet soms historische gegevens opvragen.|Mijn gebruikers zeggen dat ze toegang tot historische gegevens willen, maar ze gebruiken deze zelden.|
|Ik heb apps, waaronder oudere apps, die ik niet wil bijwerken.|Ik moet steeds meer extra opslag kopen.|
|Ik wil geld besparen op opslag.|Ik kan in de SLA geen back-up maken van dergelijke grote tabellen of ze herstellen.|

## Welke databases en tabellen zijn geschikt voor Stretch Database?
Stretch Database is bedoeld voor transactionele databases met een grote hoeveelheid historische gegevens, die doorgaans in een klein aantal tabellen zijn opgeslagen. Deze tabellen kunnen meer dan een miljard rijen bevatten.

Als u de tijdelijke-tabelfunctie van SQL Server 2016 gebruikt, gebruik dan Stretch Database om de gehele bijbehorende geschiedenistabel of een deel daarvan naar de kosten\-efficiënte opslag in Azure te migreren. Zie voor meer informatie [Manage Retention of Historical Data in System-Versioned Temporal Tables](https://msdn.microsoft.com/library/mt637341.aspx).

Gebruik Stretch Database Advisor, een functie van SQL Server 2016 Upgrade Advisor, om databases en tabellen voor Stretch Database te identificeren. Zie voor meer informatie [Identify databases and tables for Stretch Database](sql-server-stretch-database-identify-databases.md). Zie [Surface area limitations and blocking issues for Stretch Database](sql-server-stretch-database-limitations.md) voor meer informatie over mogelijke problemen met blokkeringen.

## <a name="FAQ"></a>Veelgestelde vragen over Stretch Database
**Werkt Stretch Database met &lt;naam van de SQL Server-functie&gt;?**
-   Voor een lijst van SQL Server-functies die zorgen dat een tabel niet met Stretch gebruikt kan worden, zie [Surface area limitations and blocking issues for Stretch Database](sql-server-stretch-database-limitations.md).

-   Eventueel kunt u SQL Server 2016 Upgrade Advisor downloaden en de Stretch Database Advisor uitvoeren om databases en tabellen te identificeren die geschikt zijn voor Stretch Database. Zie voor meer informatie [Identify databases and tables for Stretch Database](sql-server-stretch-database-identify-databases.md).

**Kan ik een ander lokaal SQL Server-exemplaar als doel voor Stretch Database aanwijzen?**
Nee. Stretch Database biedt geen ondersteuning voor een ander lokaal SQL Server-exemplaar als het externe eindpunt.

**Kan ik Stretch uitschakelen en de gemigreerde gegevens weer naar de lokale tabel verplaatsen?**
Ja. Zie voor meer informatie [Disable Stretch Database and bring back remote data](sql-server-stretch-database-disable.md).

## Voorwaarden
**Lokale database**. De on\--premises SQL Server-database.

**Extern eindpunt**. De locatie in Microsoft Azure die de externe gegevens van de database bevat.

**Lokale gegevens**. Gegevens in een database waarvoor Stretch Database is ingeschakeld, die niet naar Azure worden verplaatst op basis van de Stretch Database-configuratie voor de tabellen in de database.

**In aanmerking komende gegevens**. Gegevens in een database waarvoor Stretch Database is ingeschakeld, die nog niet zijn verplaatst, maar wel naar Azure verplaatst gaan worden op basis van de Stretch Database-configuratie voor de tabellen in de database.

**Externe gegevens**. Gegevens in een database waarvoor Stretch Database is ingeschakeld, die al naar Azure zijn verplaatst.

## Architectuur
Stretch Database maakt gebruik van de resources in Microsoft Azure om de verwerking van opslag van en query's op gearchiveerde gegevens te ontlasten.

Wanneer u Stretch Database voor een database inschakelt, wordt een beveiligde definitie voor de gekoppelde server aangemaakt in de on\--premises SQL Server. Het externe eindpunt is het doel van deze definitie voor de gekoppelde server. Wanneer u Stretch Database voor een tabel in de database inschakelt, richt het externe resources in en begint het in aanmerking komende gegevens te migreren, mits migratie is ingeschakeld.

Query's op tabellen waarvoor Stretch Database is ingeschakeld, worden automatisch uitgevoerd op zowel de lokale database als het externe eindpunt. Stretch Database maakt gebruik van de verwerkingscapaciteit van Azure voor het uitvoeren van query's op externe gegevens door de query te herschrijven. U kunt dit herschrijven beschouwen als een 'externe query'-operator in het nieuwe queryplan.

![Architectuur Stretch Database][StretchOverviewImage2]

## Beveiliging en machtigingen

### Stretch Database in- en uitschakelen voor een SQL Server-exemplaar
Om te beginnen met het configureren van databases voor Stretch Database, moet u eerst de configuratieoptie voor het niveau van het ‘externe gegevensarchief’-exemplaar\- wijzigen met sp\_configuratie. Voor deze bewerking heeft u de bevoegdheden van een systeembeheerder of serverbeheerder nodig. Wanneer deze optie is ingeschakeld, kunt u databases voor Stretch Database configureren, gegevens migreren en gegevens bij het externe eindpunt opvragen.

### In- en uitschakelen van Stretch Database voor een database of tabel
Om een database voor Stretch Database te configureren moet u de machtiging BEHEER DATABASE hebben. Bovendien moet u beheerdersreferenties opgeven voor het externe eindpunt.

Om een tabel voor Stretch Database te configureren moet u de bevoegdheid WIJZIGEN voor de tabel hebben en de database moet al zijn geconfigureerd voor Stretch Database.

### Toegang tot gegevens
Alleen systeemprocessen hebben toegang tot de definitie voor de gekoppelde server achter Stretch Database. Gebruikersaanmeldingen kunnen geen query's naar het externe eindpunt uitgeven via de definitie voor de gekoppelde server.

Stretch Database verandert het machtigingsmodel van een bestaande database niet. Gebruikersaanmeldingen kunnen via de lokale database de gegevens opvragen in een tabel waarvoor Stretch Database is ingeschakeld. De lokale database controleert de machtigingen voor alle acties die de gebruiker heeft gestart op dezelfde manier als bij andere objecten. Als u bent gemachtigd om de tabel in te zien waarvoor Stretch Database is ingeschakeld, hebt u toegang tot alle inhoud waarvoor u gemachtigd bent, ongeacht waar de gegevens zich fysiek bevinden.

![Gegevenstoegangsmodel Stretch Database][StretchOverviewImage3]

## Testen Stretch Database
**Test Stretch Database met de AdventureWorks voorbeelddatabase.** Om de AdventureWorks voorbeelddatabase te verkrijgen downloadt u [hier](https://www.microsoft.com/download/details.aspx?id=49502) ten minste het databasebestand en het bestand met voorbeelden en scripts. Decomprimeer, nadat u de database naar een exemplaar van SQL Server 2016 heeft hersteld, het voorbeeldbestand en open het Stretch DB voorbeeldbestand uit de map Stretch DB. Voer de scripts in dit bestand uit om de ruimte te controleren die door uw gegevens wordt gebruikt voor- en nadat u Stretch Database heeft ingeschakeld, om de voortgang van de gegevensmigratie bij te houden en om te bevestigen dat u zowel tijdens als na de gegevensmigratie bestaande gegevens kunt blijven opvragen en nieuwe gegevens kunt invoegen.

## Volgende stap
**Identificeer de databases en tabellen die geschikt zijn voor Stretch Database.** Download SQL Server 2016 Upgrade Advisor en voer de Stretch Database Advisor uit om databases en tabellen te identificeren die geschikt zijn voor Stretch Database. Stretch Database Advisor identificeert ook problemen met blokkeringen. Zie voor meer informatie [Identify databases and tables for Stretch Database](sql-server-stretch-database-identify-databases.md).

<!--Image references-->
[StretchOverviewImage1]: ./media/sql-server-stretch-database-overview/StretchDBOverview.png
[StretchOverviewImage2]: ./media/sql-server-stretch-database-overview/StretchDBOverview1.png
[StretchOverviewImage3]: ./media/sql-server-stretch-database-overview/StretchDBOverview2.png



<!--HONumber=Jun16_HO2-->


