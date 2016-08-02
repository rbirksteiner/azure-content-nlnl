<properties
    pageTitle="Aan de slag door de Enable Database voor Stretch-wizard uit te voeren| Microsoft Azure"
    description="Ontdek hoe u een database kunt configureren voor Stretch Database door de Enable Database  voor Stretch-wizard uit te voeren."
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
    ms.topic="hero-article"
    ms.date="05/17/2016"
    ms.author="douglasl"/>

# Ga aan de slag door de Enable Database voor Stretch-wizard uit te voeren

Voer de Enable Database  voor Stretch-wizard uit om een database te configureren voor Stretch Database.  In dit onderwerp wordt uitgelegd welke gegevens u moet invoeren en welke keuzes u in de wizard moet maken.

Zie [Stretch Database](sql-server-stretch-database-overview.md) voor meer informatie over Stretch Database.

## De wizard starten

1.  In SQL Server Management Studio, in Object Explorer, selecteert u de database waarvoor u Stretch wilt inschakelen.

2.  Klik hier met de rechtermuisknop\-op en selecteer **Taken**. Selecteer vervolgens **Stretch** en daarna  **Inschakelen** om de wizard te starten.

## <a name="Intro"></a>Inleiding
Controleer het doel van de wizard en de vereisten.

![Introductiepagina van de Stretch Database-wizard][StretchWizardImage1]

## <a name="Tables"></a>Selecteer tabellen
Selecteer de tabellen die u wilt inschakelen voor Stretch.

![Selecteer de tabellenpagina van de Stretch Database-wizard][StretchWizardImage2]

|Kolom|Beschrijving|
|----------|---------------|
|(geen titel)|Vink het selectievakje in deze kolom aan om de geselecteerde tabel voor Stretch in te schakelen.|
|**Naam**|De naam van de kolom in de tabel.|
|(geen titel)|Een symbool in deze kolom geeft meestal aan u de geselecteerde tabel voor Stretch niet kunt inschakelen vanwege een probleem met blokkeringen. Dit komt mogelijk doordat de tabel een niet-ondersteund gegevenstype gebruikt. Houd de cursor boven  het symbool om meer informatie in de knopinfo weer te geven. Zie [Surface area limitations and blocking issues for Stretch Database](sql-server-stretch-database-limitations.md) voor meer informatie.|
|**Stretched**|Geeft aan of de tabel al is ingeschakeld.|
|**Migreren**|U kunt een hele tabel migreren (**Gehele tabel**) of u kunt een filter op basis van datum-predicaat opgeven in de wizard. Als u een ander filterpredicaat wilt gebruiken om rijen te selecteren die u wilt migreren, voert u de instructie ALTER TABLE uit om het filterpredicaat te specificeren nadat u de wizard hebt afgesloten. Zie [Een filterpredicaat gebruiken om rijen te selecteren om te migreren (Stretch Database)](sql-server-stretch-database-predicate-function.md) voor meer informatie over het filterpredicaat. Zie [Enable Stretch Database for a table](sql-server-stretch-database-enable-table.md) of [ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)voor meer informatie over het toepassen van het predicaat.|
|**Rijen**|Aantal rijen in de tabel.|
|**Grootte (KB)**|Grootte van de tabel in KB.|

## <a name="Filter"></a>Geef optioneel een filterpredicaat op basis van datum

Als u een filterpredicaat op basis van datum wilt geven om de rijen die u wilt migreren te selecteren, doe dan het volgende op de pagina **Tabellen selecteren**.

1.  In de lijst **Selecteer de tabellen die u wilt uitrekken**, klikt u op **Gehele tabel** in de rij voor de tabel. Het dialoogvenster **Selecteer rijen om uit te rekken** wordt geopend.

    ![Geef een filterpredicaat op op basis van datum][StretchWizardImage2a]

2.  In het dialoogvenster **Selecteer rijen om uit te rekken** selecteert u **Rijen kiezen**.

3.  In het **naamveld** vult u een naam in voor het filterpredicaat.

4.  Voor de **waar**-clausule kiest u een datumkolom uit de tabel, een operator en een datumwaarde.

5. Klik op **Controleren** om het predicaat te testen. Als het predicaat resultaten uit de tabel geeft, dat wil zeggen, als er te migreren rijen zijn die voldoen aan de voorwaarde, geeft de test **geslaagd** aan.

6.  Klik op Gereed om terug te keren naar de pagina **Tabellen selecteren**.

    ![Selecteer de pagina Tabellen nadat u een filterpredicaat heeft gedefinieerd][StretchWizardImage2b]

## <a name="Configure"></a>Azure-implementatie configureren

1.  Inloggen bij Microsoft Azure met een Microsoft-account.

    ![Inloggen bij de Azure - Stretch Database-wizard][StretchWizardImage3]

2.  Selecteer het Azure-abonnement dat moet worden gebruikt voor de Stretch Database.

3.  Selecteer een Azure-regio. Als u een nieuwe server maakt, wordt de server gemaakt in deze regio.

    Om latentie te beperken, kiest u de Azure-regio waarin uw SQL Server zich bevindt. Zie [Azure-gebieden](https://azure.microsoft.com/regions/) voor meer informatie over de regio's.

4.  Geef op of u een bestaande server wilt gebruiken of een nieuwe Azure-server wilt maken.

    Als de Active Directory op uw SQL Server is bij Azure Active Directory is gefedereerd, kunt u eventueel een federatieve serviceaccount voor SQL Server gebruiken om te communiceren met de externe Azure-server. Zie [ALTER DATABASE SET Options (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx) voor meer informatie over de vereisten voor deze optie.

    -   **Nieuwe server maken**

        1.  Maak een gebruikersnaam en wachtwoord voor de serverbeheerder.

        2.  Gebruik eventueel een federatieve serviceaccount voor SQL Server om te communiceren met de externe Azure-server.

        ![Nieuwe Azure-server maken - Stretch Database-wizard][StretchWizardImage4]

    -   **Bestaande server**

        1.  Selecteer of typ de naam van de bestaande Azure-server.

        2.  Selecteer de verificatiemethode.

            -   Als u **SQL Server-verificatie** selecteert, maakt u een nieuwe gebruikersnaam en nieuw wachtwoord.

            -   Selecteer **Active Directory Integrated Authentication** om een federatieve serviceaccount voor SQL Server te gebruiken om te communiceren met de externe Azure-server.

        ![Bestaande Azure-server selecteren - Stretch Database-wizard][StretchWizardImage5]

## <a name="Credentials"></a>Beveiligde referenties
Voer een sterk wachtwoord in om een databasehoofdsleutel te maken, of voer het wachtwoord in als een databasehoofdsleutel al bestaat.

U hebt een databasehoofdsleutel nodig om de referenties te beschermen die de Stretch Database gebruikt om verbinding te maken met de externe database.

![Beveilig de referentiepagina van de Stretch Database-wizard][StretchWizardImage6]

Zie [CREATE MASTER KEY (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx) en [Create a Database Master Key](https://msdn.microsoft.com/library/aa337551.aspx) voor meer informatie over de databasehoofdsleutel. Zie [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx) voor meer informatie over de referentie die de wizard maakt.

## <a name="Network"></a>Selecteer IP-adres
Gebruik het openbare IP-adres van uw SQL Server of voer een bereik met IP-adressen in om een firewallregel op Azure te creëren waarmee SQL Server kan communiceren met de externe Azure-server invoeren.

Het IP-adressen/de IP-adressen die u op deze pagina opgeeft, laten de Azure-server weten dat deze binnenkomende gegevens, query's en beheerbewerkingen gestart door SQL Server moet doorlaten via de Azure-firewall. De wizard brengt geen wijzigingen aan in de firewall-instellingen op de SQL Server.

![Selecteer de IP-adrespagina van de Stretch Database-wizard][StretchWizardImage7]

## <a name="Summary"></a>Samenvatting
Bekijk de waarden die u hebt ingevoerd en de opties die u hebt geselecteerd in de wizard. Selecteer vervolgens **Voltooien** om Stretch in te schakelen.

![Overzichtspagina van de Stretch Database-wizard][StretchWizardImage8]

## <a name="Results"></a>Resultaten
Bekijk de resultaten.

Selecteer eventueel **Monitor** om het monitoren van de status van de gegevensmigratie in de Stretch Database Monitor te starten. Zie [Monitor and troubleshoot data migration (Stretch Database)](sql-server-stretch-database-monitor.md) voor meer informatie.

## <a name="KnownIssues"></a>Problemen met de wizard oplossen
**De Stretch Database-wizard is mislukt.**
Als Stretch Database nog niet is ingeschakeld op serverniveau en u voert de wizard uit zonder de systeembeheerdersmachtigingen om deze in te schakelen, mislukt de wizard. Vraag de systeembeheerder om Stretch Database in te schakelen op het lokale serverexemplaar en voer de wizard opnieuw uit. Zie [Vereiste: toestemming om Stretch Database in te schakelen op de server](sql-server-stretch-database-enable-database.md#EnableTSQLServer) voor meer informatie.

## Volgende stappen
Aanvullende tabellen voor Stretch Database inschakelen. Controleer gegevensmigratie en beheer\-databases en tabellen waarvoor Stretch is ingeschakeld.

-   [Stretch  Database  inschakelen voor een tabel](sql-server-stretch-database-enable-table.md) om aanvullende tabellen in te schakelen.

-   [Stretch  Database bewaken](sql-server-stretch-database-monitor.md) om de status van de gegevensmigratie te bekijken.

-   [Stretch Database pauzeren en  hervatten](sql-server-stretch-database-pause.md)

-   [Stretch Database beheren en problemen oplossen](sql-server-stretch-database-manage.md)

-   [Back-up en herstel voor Stretch geschikte databases](sql-server-stretch-database-backup.md)

## Zie ook

[Stretch Database inschakelen voor een database](sql-server-stretch-database-enable-database.md)

[Stretch Database inschakelen voor een tabel](sql-server-stretch-database-enable-table.md)

[StretchWizardImage1]: ./media/sql-server-stretch-database-wizard/stretchwiz1.png
[StretchWizardImage2]: ./media/sql-server-stretch-database-wizard/stretchwiz2.png
[StretchWizardImage2a]: ./media/sql-server-stretch-database-wizard/stretchwiz2a.png
[StretchWizardImage2b]: ./media/sql-server-stretch-database-wizard/stretchwiz2b.png
[StretchWizardImage3]: ./media/sql-server-stretch-database-wizard/stretchwiz3.png
[StretchWizardImage4]: ./media/sql-server-stretch-database-wizard/stretchwiz4.png
[StretchWizardImage5]: ./media/sql-server-stretch-database-wizard/stretchwiz5.png
[StretchWizardImage6]: ./media/sql-server-stretch-database-wizard/stretchwiz6.png
[StretchWizardImage7]: ./media/sql-server-stretch-database-wizard/stretchwiz7.png
[StretchWizardImage8]: ./media/sql-server-stretch-database-wizard/stretchwiz8.png



<!--HONumber=Jun16_HO2-->


