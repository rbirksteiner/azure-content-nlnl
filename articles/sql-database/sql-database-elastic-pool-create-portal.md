<properties
    pageTitle="Een nieuwe elastische pool maken met de Azure Portal | Microsoft Azure"
    description="Een schaalbare pool voor een elastische database toevoegen aan uw SQL Databaseconfiguratie om deze eenvoudiger te kunnen beheren en resources te delen met een groot aantal databases."
    keywords="scalable database,database configuration"
    services="sql-database"
    documentationCenter=""
    authors="ninarn"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="05/06/2016"
    ms.author="ninarn"
    ms.workload="data-management"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="NA"/>


# Een nieuwe pool voor een elastische database maken met de Azure portal

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)

In dit artikel wordt uitgelegd hoe u een schaalbare [pool voor een elastische database](sql-database-elastic-pool.md) kunt maken met de [Azure portal](https://portal.azure.com/). Er zijn twee manieren om een pool te maken. U kunt dit zelf doen, als u weet welke instellingen u voor uw pool wilt gebruiken, of u kunt gebruikmaken van wat de service u aanraadt. SQL Database beschikt over ingebouwde intelligentie die bepaalde kostenefficiënte instellingen voor een pool aanraadt, op basis van de gebruikstelemetrie van uw databases.

U kunt meerdere pools toevoegen aan een server, maar u kunt geen databases van verschillende servers toevoegen aan dezelfde pool. Om een pool te maken hebt u ten minsten één database in een V12-server nodig. Als u die niet heeft, raadpleeg dan [Uw eerste Azure SQL database maken](sql-database-get-started.md). U kunt een pool maken met slechts één database, maar pools zijn alleen kostenefficiënt wanneer zich meerdere databases in één pool bevinden. Zie [Prijs- en prestatieoverwegingen voor een pool voor elastische databases](sql-database-elastic-pool-guidance.md).

> [AZURE.NOTE] Pools zijn alleen beschikbaar met SQL Database V12-servers. Als u databases heeft op een V11-server, kunt u een [PowerShell-script gebruiken om te bepalen of deze in een pool op een V12-server kunnen worden geplaatst](sql-database-elastic-pool-database-assessment-powershell.md) en vervolgens [PowerShell gebruiken om tegelijk een upgrade uit te voeren naar V12 en een pool te maken](sql-database-upgrade-server-powershell.md).

## Stap 1: een nieuwe pool maken

Maak een pool voor een elastische database door een nieuwe pool aan een server toe te voegen. U kunt meerdere pools toevoegen aan een server, maar u kunt geen databases van verschillende servers toevoegen aan dezelfde pool.

1. Klik in de [Azure portal](http://portal.azure.com/) op **SQL-servers** en klik vervolgens op de server met de databases die u aan een pool wilt toevoegen.
2. Klik op **Nieuwe pool**.

    ![Voeg de pool toe aan een server](./media/sql-database-elastic-pool-create-portal/new-pool.png)

    **OF**

    Er wordt een bericht weergegeven dat er aanbevolen pools voor een elastische database zijn voor de server (alleen V12). Klik op het bericht om de pools die worden aanbevolen op basis van de telemetrie van het databasegebruik te bekijken. Klik vervolgens op de categorie voor meer informatie en om de pool aan te passen. Zie [Aanbevelingen voor pools begrijpen](#understand-pool-recommendations) verderop in dit onderwerp om te ontdekken hoe de aanbeveling tot stand is gekomen.

    ![aanbevolen pool](./media/sql-database-elastic-pool-create-portal/recommended-pool.png)

    De blade **Pool voor elastische database** wordt weergegeven. Hier kunt u uw pool instellen. Als u bij de vorige stap op **Nieuwe pool** heeft geklikt, kiest de portal een **Standaardpool** onder **Prijscategorie**, een unieke **Naam** en een standaardconfiguratie voor de pool. Als u een aanbevolen pool kiest, zijn de aanbevolen categorie en configuratie van de pool al gekozen. U kunt deze als u wilt bewerken.

    ![Elastische pool configureren](./media/sql-database-elastic-pool-create-portal/configure-elastic-pool.png)

3. Kies een naam voor de elastische pool, of behoud de standaardnaam.

## Stap 2: kies een prijscategorie

De prijscategorie van de pool bepaalt welke functies beschikbaar zijn voor de elastische databases in de pool en het maximumaantal eDTU’s (eDTU MAX) en de opslag (in GB’s) die beschikbaar is in elke database. Zie voor meer informatie de Servicecategorieën.

Om de prijscategorie van de pool te wijzigen, klikt u op **Prijscategorie**. Klik op de gewenste prijscategorie en vervolgens op **Selecteren**.

> [AZURE.IMPORTANT] Nadat u de prijscategorie gekozen heeft en uw wijzigingen heeft opgeslagen door in de laatste stap op **OK** te klikken, kunt u de prijscategorie van de pool niet meer wijzigen. Om de prijscategorie van een bestaande elastische pool te wijzigen, maakt u een nieuwe elastische pool in de gewenste prijscategorie en migreert u vervolgens de elastische databases naar deze nieuwe pool.

![Een prijscategorie selecteren](./media/sql-database-elastic-pool-create-portal/pricing-tier.png)

## Stap 3: configureer de pool

Nadat u de prijscategorie heeft ingesteld, klikt u op Pool configureren. Hier voegt u databases toe en stelt u de eDTU’s en opslag van de pool (in GB’s) in, evenals het minimum- en maximumaantal eDTU’s voor de elastische databases in de pool.

1. Klik op **Pool configureren**
2. Selecteer de databases die u aan de pool wilt toevoegen. Deze stap is optioneel bij het maken van de pool. Databases kunnen ook worden toegevoegd wanneer de pool is gemaakt.
    Om databases toe te voegen klikt u op **Database toevoegen** en vervolgens op de databases die u wilt toevoegen. Klik als laatste op de knop **Selecteren**.

    ![Databases toevoegen](./media/sql-database-elastic-pool-create-portal/add-databases.png)

    Als de gebruikstelemetrie van de databases waar u mee werkt genoeg is, worden de grafiek **Geschat eDTU- en GB-gebruik** en het staafdiagram **Werkelijk eDTU-gebruik** bijgewerkt zodat u betere beslissingen kunt nemen met betrekking tot de configuratie. De service kan ook een bericht weergeven met een aanbeveling zodat u de pool het juiste formaat kunt geven. Zie [Dynamische aanbevelingen](#dynamic-recommendations).

3. Gebruik de bedieningselementen op de pagina **Pool configureren** om de instellingen te verkennen en uw pool te configureren. Zie [Limieten elastische pools](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools-and-elastic-databases) voor meer informatie over de limieten van elke servicecategorie en zie [Prijs- en prestatieoverwegingen voor een pool voor elastische databases](sql-database-elastic-pool-guidance.md) voor gedetailleerde richtlijnen voor het juiste formaat van een pool. Zie [Eigenschappen pool voor elastische database](sql-database-elastic-pool.md#elastic-database-pool-properties)voor meer informatie over de instellingen van een pool.

    ![Elastische pool configureren](./media/sql-database-elastic-pool-create-portal/configure-performance.png)

4. Klik op **Selectere** in de blade **Pool configureren** nadat u de instellingen heeft gewijzigd.
5. Klik op **OK** om de pool te maken.


## Aanbevelingen voor pools begrijpen

De SQL Database-service beoordeelt de gebruiksgeschiedenis en beveelt een of meerdere pools aan wanneer deze kosteneffectiever zijn dan het gebruik van individuele databases. Elke aanbeveling wordt geconfigureerd met een unieke subset van de databases van de server die het meest geschikt zijn voor de pool.

![aanbevolen pool](./media/sql-database-elastic-pool-create-portal/recommended-pool.png)  

De aanbevelingen voor de pool bestaan uit:

- Een prijscategorie voor de pool (Basic, Standard en Premium)
- De juiste **eDTU’s voor de pool** (ook wel Max eDTU's per pool)
- De **eDTU MAX** en **eDTU Min** per database
- De lijst met aanbevolen databases voor de pool

De service houdt rekening met de laatste 30 dagen telemetrie bij het aanbevelen van pools. Een database moet tenminste 7 dagen bestaan voordat deze in aanmerking komt voor een pool voor een elastische database. Databases die zich al in een pool voor een elastische database bevinden, worden niet aanbevolen voor een pool voor een elastische database.

De service beoordeelt wat de resource nodig heeft en hoe kosteneffectief het is om de individuele databases in elke servicecategorie te verplaatsen naar pools van dezelfde categorie. Alle Standard databases op een server worden bijvoorbeeld beoordeeld op hoe ze in een Standard elastische pool passen. Dit betekent dat de service geen aanbevelingen doet voor het verplaatsen van databases naar een andere categorie, zoals het verplaatsen van een Standard database naar een Premium pool.

### Dynamische aanbevelingen.

Nadat databases aan de pool zijn toegevoegd, worden dynamische aanbevelingen gegenereerd op basis van het gebruik van de databases die u hebt geselecteerd. Deze aanbevelingen worden weergegeven in de eDTU- en GB-gebruiksgrafiek, evenals in een banner met aanbevelingen bovenaan de blade  **Pool configureren**. Deze aanbevelingen zijn bedoeld om u te helpen bij het maken van een pool die geoptimaliseerd is voor uw specifieke databases.

![dynamische aanbevelingen](./media/sql-database-elastic-pool-create-portal/dynamic-recommendation.png)

## Aanvullende bronnen

- [Een elastische pool in de SQL Database beheren met de portal](sql-database-elastic-pool-manage-portal.md)
- [Een elastische pool in de SQL Database beheren met PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [Een elastische pool in de SQL Database beheren met C#](sql-database-elastic-pool-manage-csharp.md)
- [Uitbreiden met Azure SQL Database](sql-database-elastic-scale-introduction.md) 




<!--HONumber=Jun16_HO2-->


