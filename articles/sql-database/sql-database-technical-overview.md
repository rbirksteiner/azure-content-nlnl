<properties
    pageTitle="Wat is SQL Database? Inleiding tot SQL Database | Microsoft Azure"
    description="Maak kennis met SQL-Database: technische informatie en mogelijkheden van het relationele database beheersysteem (RDBMS) in de cloud van Microsoft."
    keywords="introduction to sql,intro to sql,what is sql database"
    services="sql-database"
    documentationCenter=""
    authors="shontnew"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="05/23/2016"
   ms.author="shkurhek"/>

# Wat is SQL Database? Inleiding tot SQL Database

SQL Database is een relationele database-service in de cloud op basis van de toonaangevende Microsoft SQL Server-engine met bedrijfskritieke mogelijkheden. SQL Database biedt voorspelbare prestaties, schaalbaarheid zonder uitvaltijd, bedrijfscontinuïteit en gegevensbeveiliging, allemaal praktisch zonder beheer. U kunt zich richten op het sneller ontwikkelen en op de markt brengen van apps, in plaats van het beheer van virtuele machines en infrastructuur. Omdat SQL Database is gebaseerd op de [SQL Server](https://msdn.microsoft.com/library/bb545450.aspx)-engine, worden bestaande SQL Server-hulpprogramma's, -bibliotheken en -API's ondersteund voor een soepele verplaatsing en uitbreiding naar de cloud.

Dit artikel bevat een inleiding tot de belangrijkste concepten en functies van SQL Database met betrekking tot prestaties, schaalbaarheid en beheerbaarheid, met koppelingen naar meer gedetailleerde informatie. Als u zover bent, kunt u in een paar minuten [uw eerste SQL-database maken](sql-database-get-started.md) of [een pool voor een elastische database maken](sql-database-elastic-pool-create-portal.md). Zie voor meer informatie deze video van 30 minuten.

> [AZURE.VIDEO azurecon-2015-get-started-with-azure-sql-database]

## Prestaties en schaal aanpassen zonder uitvaltijd

SQL-databases zijn beschikbaar in verschillende *servicelagen*: Basic, Standard en Premium. Elke servicelaag biedt [verschillende niveaus qua prestaties en mogelijkheden](sql-database-service-tiers.md) voor lichte tot zware workloads van databases. U kunt uw eerste app ontwikkelen op een kleine database voor slechts enkele euro's per maand. Vervolgens kunt u handmatig of programmatisch [de servicelaag wijzigen](sql-database-scale-up.md) als uw app wereldwijd een succes wordt, zonder uitvaltijd voor uw app of uw klanten.

Voor veel bedrijven en apps is het kunnen maken van databases en het naar wens omhoog of omlaag schalen van de prestaties van één database al voldoende, vooral als de gebruikspatronen redelijk voorspelbaar zijn. Bij onvoorspelbare gebruikspatronen kan het echter lastig zijn uw kosten en bedrijfsmodel effectief te beheren.

[Elastische pools](sql-database-elastic-pool.md) in SQL Database vormen de oplossing voor dit probleem. Het concept is eenvoudig. U wijst prestaties aan een pool toe en betaalt voor de collectieve prestaties van de groep in plaats van voor de prestaties van één database. U hoeft de prestaties van de database niet omhoog of omlaag te schalen. De databases in de pool, de *elastische databases*, worden automatisch omhoog of omlaag geschaald om aan de vraag te voldoen. Elastische databases tellen mee voor het verbruik tot het maximum voor de pool is bereikt. Zo blijven uw kosten voorspelbaar, ook al is uw databasegebruik dat niet. Bovendien kunt u [databases aan de pool toevoegen of eruit verwijderen](sql-database-elastic-pool-manage-portal.md). Zo kan uw app kan worden opgeschaald van een handjevol databases naar duizenden databases, allemaal binnen het budget dat u zelf bepaalt. Zie [Ontwerppatronen voor SaaS-toepassingen met meerdere tenants met behulp van Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md) voor meer informatie over ontwerppatronen voor SaaS-toepassingen met elastische pools.

Welke oplossing u ook kiest, individuele of elastische databases, u kunt altijd nog switchen. U kunt individuele databases combineren met pools voor elastische databases, en de servicelagen van individuele databases en pools wijzigen om innovatieve ontwerpen te maken. Bovendien kunt u, dankzij de kracht en het bereik van Azure, Azure-services combineren en integreren met SQL Database om te voldoen aan de behoeften voor uw unieke moderne app-ontwerp, kosten besparen en resources efficiënt beheren, en nieuwe zakelijke verkoopkansen creëren.

Maar hoe kunt u de relatieve prestaties van databases en databasepools vergelijken? Hoe weet u wanneer u moet stoppen met omhoog of omlaag schalen? Het antwoord is de DTU (Database Transaction Unit, databasetransactie-eenheid) voor individuele databases en de elastische DTU (eDTU) voor elastische databases en databasepools. Zie [SQL Database-opties en prestaties: wat is er beschikbaar in elke servicelaag](sql-database-service-tiers.md) voor meer informatie.

## Continuïteit van uw app en uw bedrijf

De toonaangevende serviceovereenkomst [(SLA)](http://azure.microsoft.com/support/legal/sla/) van Azure met 99,99% beschikbaarheid dankzij een wereldwijd netwerk van door Microsoft beheerde datacenters, zorgt u ervoor dat uw app continu (24 uur per dag, 7 dagen per week) in de lucht blijft. Met elke SQL-database profiteert u van ingebouwde functionaliteit voor fouttolerantie en gegevensbeveiliging die u anders zelf zou moeten ontwerpen, kopen, implementeren en beheren. Afhankelijk van de eisen van uw bedrijf kunt u daarnaast aanvullende beveiligingslagen aanvragen, zodat uw app en uw bedrijfsvoering snel weer kunnen worden hersteld na een noodgeval, fout of iets anders. Elke servicelaag van SQL Database heeft zo zijn eigen set met functies voor het aanleggen en permanent in de lucht houden van uw database. Met behulp van herstelpunten kunt u een database terugzetten naar een eerdere toestand, tot 35 dagen geleden. Als er een storing optreedt in het datacenter dat als host fungeert voor uw databases, kunt u bovendien een failover naar databasereplica's in een andere regio uitvoeren. U kunt replica's ook gebruiken voor upgrades of verplaatsingen naar verschillende regio's.

![Geo-replicatie in SQL Database](./media/sql-database-technical-overview/azure_sqldb_map.png)


Zie [Bedrijfscontinuïteit](sql-database-business-continuity.md) voor meer informatie over de verschillende functies voor bedrijfscontinuïteit die beschikbaar zijn per servicelaag.

## Uw gegevens beveiligen
SQL Server heeft traditiegetrouw een uitstekende gegevensbeveiliging. Deze traditie wordt voortgezet in SQL Database, met functies voor toegangsbeperking, gegevensbeveiliging en activiteitsbewaking. Zie [Beveiliging van uw SQL-database](sql-database-security.md) voor een kort overzicht van beveiligingsopties in SQL Database. Zie het [Beveiligingscentrum voor SQL Server Database-engine en SQL Database](https://msdn.microsoft.com/library/bb510589) voor een uitgebreid overzicht van beveiligingsfuncties. Bezoek ook eens het [Vertrouwenscentrum van Azure](https://azure.microsoft.com/support/trust-center/security/) voor informatie over de beveiliging van het Azure-platform.

## Volgende stappen
Nu u de inleiding tot SQL Database hebt gelezen en weet wat SQL Database is, bent u klaar voor de volgende stappen:

- Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/sql-database/) voor hulpprogramma's voor berekening en een vergelijking van de kosten voor individuele en elastische databases.
- Lees meer over [elastische pools](sql-database-elastic-pool.md).
- Ga aan de slag met het [maken van uw eerste database](sql-database-get-started.md).
- [Verbinding maken en query's uitvoeren met SSMS](sql-database-connect-query-ssms.md)
- Bouw uw eerste app in C#, Java, Node.js, PHP, Python of Ruby: [Verbindingsbibliotheken voor SQL-Database en SQL Server](sql-database-libraries.md)
- Zie een index van de titels en beschrijvingen van [alle onderwerpen voor Azure SQL Database-service](sql-database-index-all-articles.md).



<!--HONumber=Jun16_HO2-->


