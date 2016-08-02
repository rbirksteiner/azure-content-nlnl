<properties
    pageTitle="Prestaties en opties van SQL Database: servicelagen | Microsoft Azure"
    description="Vergelijk de prestatie- en bedrijfscontinuïteitsfuncties op de verschillende servicelagen van SQL Database met balanskosten en capaciteit terwijl u schaalt."
    keywords="database options,database performance"
    services="sql-database"
    documentationCenter=""
    authors="carlrabeler"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="05/13/2016"
    ms.author="carlrab"/>

# Opties en prestaties van SQL Database: inzicht in wat er beschikbaar is in elke servicelaag

[Azure SQL Database](sql-database-technical-overview.md) heeft meerdere servicelagen voor de afhandeling van verschillende workloads. U kunt servicelagen op elk moment wijzigen zonder uitvaltijd voor uw toepassing. U kunt ook [een individuele database maken](sql-database-get-started.md) met gedefinieerde kenmerken en prijzen. Of u kunt meerdere databases beheren door [een pool voor elastische database te maken](sql-database-elastic-pool-create-portal.md). In beide gevallen is er een **Basic**-, **Standard**- en **Premium**-laag. Databaseopties in deze lagen zijn vergelijkbaar voor individuele databases en elastische pools, maar voor elastische pools zijn er aanvullende overwegingen. In dit artikel vindt u details over servicelagen voor individuele databases en elastische databases.

## Servicelagen en database-opties
Basic-, Standard- en Premium-servicelagen hebben allemaal een SLA van 99,99% beschikbaarheid en bieden voorspelbare prestaties, flexibele opties voor bedrijfscontinuïteit, beveiligingsfuncties en facturering per uur. In de volgende tabel vindt u voorbeelden van de lagen die het meest geschikt zijn voor verschillende toepassingsworkloads.

| Servicelaag | Beoogde workloads |
|---|---|
| **Basic** | Het meest geschikt voor een kleine database, die doorgaans één actieve bewerking tegelijk ondersteunt. Voorbeelden zijn onder meer databases die worden gebruikt voor ontwikkeling of testen, of kleinschalige, onregelmatig gebruikte toepassingen. |
| **Standard** | De optie die wordt gebruikt door de meeste cloudtoepassingen, met ondersteuning voor meerdere gelijktijdige query's. Voorbeelden zijn onder meer werkgroep- of webtoepassingen. |
| **Premium** | Ontworpen voor toepassingen met veel transacties, die een groot aantal gelijktijdige gebruikers ondersteunen en optimale mogelijkheden voor bedrijfscontinuïteit vereisen. Voorbeelden zijn databases die bedrijfskritieke toepassingen ondersteunen. |

>[AZURE.NOTE] De Web- en Business-edities worden niet meer gebruikt. Lees de [Veelgestelde vragen over Sunset](https://azure.microsoft.com/pricing/details/sql-database/web-business/) als u de Web- of Business-editie wilt blijven gebruiken.

## Servicelagen en prestatieniveaus van individuele databases
Voor individuele databases zijn er binnen elke servicelaag meerdere prestatieniveaus. U hebt de flexibiliteit om het niveau te kiezen dat het best voldoet aan de eisen van uw workload. Als u omhoog of omlaag wilt schalen, kunt u de lagen van de database eenvoudig wijzigen **zonder uitvaltijd voor uw toepassing**. Zie [Servicelagen en prestatieniveaus van databases wijzigen](sql-database-scale-up.md) voor meer informatie.

De hieronder vermelde prestatiekenmerken zijn van toepassing op databases die zijn gemaakt met [SQL Database V12](sql-database-v12-whats-new.md). Als de onderliggende hardware in Azure als host fungeert voor meerdere databases, krijgt de database nog steeds een gegarandeerd aantal resources en heeft dit geen invloed op de verwachte prestatiekenmerken van de database.

[AZURE.INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

Raadpleeg de sectie [DTU](#understanding-dtus) in dit onderwerp voor meer inzicht in DTU's.

>[AZURE.NOTE] Voor een gedetailleerde uitleg van de overige rijen in deze tabel met servicelagen raadpleegt u [Mogelijkheden en beperkingen van servicelagen](sql-database-performance-guidance.md#service-tier-capabilities-and-limits).

## Servicelagen en prestaties in eDTU's van elastische pools
Behalve dat u een individuele database kunt maken en schalen, hebt u ook de mogelijkheid om meerdere databases te beheren in een [elastische pool](sql-database-elastic-pool.md). Alle databases in een elastische pool delen een gemeenschappelijke set bronnen. De prestatiekenmerken worden gemeten met behulp van *eDTU's* (elastic Database Transaction Units). Net als bij individuele databases zijn er drie soorten servicelagen: **Basic**, **Standard** en **Premium**. Voor pools bepalen deze drie servicelagen nog steeds de algehele prestatielimieten en een aantal functies.

Dankzij pools kunnen elastische databases DTU-bronnen delen en verbruiken zonder dat er aan de databases in de pool een specifiek prestatieniveau hoeft te worden toegewezen. Zo kan een individuele database in een Standard-pool van 0 eDTU's tot het maximumaantal eDTU's voor de database gaan dat u hebt ingesteld bij de configuratie van de pool. Op die manier kunnen meerdere databases met verschillende workloads efficiënt gebruikmaken van de eDTU-resources die voor de hele pool beschikbaar zijn. Zie [Prijs- en prestatieoverwegingen voor een elastische pool](sql-database-elastic-pool-guidance.md) voor meer informatie.

In de volgende tabel vindt u een beschrijving van de kenmerken van de servicelagen van de pool.

[AZURE.INCLUDE [SQL DB service tiers table for elastic databases](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Elke database in een pool heeft dezelfde kenmerken als die van een individuele database voor die laag. Zo heeft de Basic-pool een limiet voor het maximumaantal sessies per groep van 4800 tot 28800, maar één database binnen die groep heeft een databaselimiet van 300 sessies (de limiet voor een individuele Basic-database zoals aangegeven in de vorige sectie).

## DTU's

[AZURE.INCLUDE [SQL DB DTU description](../../includes/sql-database-understanding-dtus.md)]

## Volgende stappen
- Meer informatie over prijzen voor deze lagen vindt u in [Prijzen van SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).
- Lees meer over [Pools voor elastische databases](sql-database-elastic-pool-guidance.md) en [Prijs- en prestatieoverwegingen voor pools voor elastische databases](sql-database-elastic-pool-guidance.md).
- Lees [Elastische pools controleren, beheren en van formaat veranderen](sql-database-elastic-pool-manage-portal.md) en [De prestaties van individuele databases bewaken](sql-database-single-database-monitor.md).
- Nu u bekend bent met de lagen van SQL Database, kunt u ze uitproberen met een [gratis account](https://azure.microsoft.com/pricing/free-trial/) en leren [hoe u uw eerste SQL-database maakt](sql-database-get-started.md).



<!--HONumber=Jun16_HO2-->


