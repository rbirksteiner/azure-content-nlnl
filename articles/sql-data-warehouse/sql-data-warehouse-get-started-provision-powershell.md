<properties
   pageTitle="SQL Data Warehouse maken met behulp van Powershell | Microsoft Azure"
   description="SQL Data Warehouse maken met behulp van Powershell"
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
   ms.date="06/04/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# SQL Data Warehouse maken met Powershell

> [AZURE.SELECTOR]
- [Azure Portal](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

## Vereisten
Zorg, voordat u begint, dat u beschikt over het volgende.

- **Azure-account**: zie [Gratis proefversie van Azure][] of [Azure-tegoed met MSDN][] om een account te maken.
- **V12 Azure SQL Server**: zie [Een logische Azure SQL Database-server maken met de Azure Portal][] of [Een logische Azure SQL Database-server maken met PowerShell][].
- **Naam van resourcegroep**: gebruik dezelfde resourcegroep als de V12 Azure SQL Server of zie [Resourcegroepen][] als u een nieuwe resourcegroep wilt maken.
- **PowerShell-versie 1.0.3 of hoger**: u kunt uw versie controleren door **Get-Module - ListAvailable-Name Azure** uit te voeren.  De meest recente versie kan worden geïnstalleerd met het [Webplatforminstallatieprogramma voor Microsoft][].  Zie [Azure PowerShell installeren en configureren][] voor meer informatie over het installeren van de meest recente versie.

> [AZURE.NOTE] Het maken van een nieuwe SQL Data Warehouse kan leiden tot een nieuwe factureerbare service.  Zie [Prijzen van SQL Data Warehouse][] voor meer informatie over prijzen.

## Een SQL Data Warehouse-database maken
1. Open Windows PowerShell.
2. Voer deze cmdlet uit om u aan te melden bij Azure Resource Manager.

    ```Powershell
    Login-AzureRmAccount
    ```
    
3. Selecteer het abonnement dat u voor de huidige sessie wilt gebruiken.

    ```Powershell
    Get-AzureRmSubscription -SubscriptionName "MySubscription" | Select-AzureRmSubscription
    ```

4.  Maak database. In dit voorbeeld wordt een nieuwe database met de naam 'mynewsqldw' met servicedoelstellingsniveau 'DW400' gemaakt op een server met de naam 'sqldwserver1', die zich bevindt in de resourcegroep 'mywesteuroperesgp1'.

    ```Powershell
    New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse"
    ```

De vereiste parameters voor deze cmdlet zijn:

- **RequestedServiceObjectiveName**: de hoeveelheid DWU die u aanvraagt, in de notatie 'DWXXX'. DWU vertegenwoordigt een toewijzing van CPU en geheugen.  Elke DWU-waarde vertegenwoordigt een lineaire toename van deze resources.  Op dit moment worden de volgende waarden ondersteund: 100, 200, 300, 400, 500, 600, 1000, 1200, 1500, 2000.
- **DatabaseName**: de naam van de SQL Data Warehouse die u maakt.
- **ServerName**: de naam van de server die u bij het maken gebruikt (moet V12 zijn).
- **ResourceGroupName**: de resourcegroep die u gebruikt.  Gebruik Get-AzureResource om beschikbare resourcegroepen in uw abonnement te zoeken.
- **Edition**: u moet Edition instellen op 'DataWarehouse' als u een SQL Data Warehouse wilt maken.

Zie [Create Database (Azure SQL Data Warehouse)][] voor meer informatie over de parameteropties.
Zie [New-AzureRmSqlDatabase][] voor de naslaginformatie bij de opdracht.

## Volgende stappen
Wanneer de inrichting van uw SQL Data Warehouse is voltooid, kunt u proberen om [voorbeeldgegevens te laden][] of meer informatie te zoeken over [ontwikkelen][], [laden][] of [migreren][].

Als u wilt weten hoe u SQL Data Warehouse programmatisch kunt beheren, lees dan het artikel over het gebruik van [PowerShell cmdlets and REST APIs][] (PowerShell-cmdlets en REST-API's).

<!--Image references-->

<!--Article references-->
[migreren]: ./sql-data-warehouse-overview-migrate.md
[ontwikkelen]: ./sql-data-warehouse-overview-develop.md
[laden]: ./sql-data-warehouse-load-with-bcp.md
[voorbeeldgegevens te laden]: ./sql-data-warehouse-get-started-manually-load-samples.md
[PowerShell cmdlets and REST APIs]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[firewall-regels]: ./sql-database-configure-firewall-settings.md
[Azure PowerShell installeren en configureren]: ../powershell/powershell-install-configure.md
[een SQL Data Warehouse maken vanuit de Azure Portal]: ./sql-data-warehouse-get-started-provision.md
[Een logische Azure SQL Database-server maken met de Azure Portal]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Een logische Azure SQL Database-server maken met PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[Resourcegroepen]: ../azure-portal/resource-group-portal.md

<!--MSDN references--> 
[MSDN]:https://msdn.microsoft.com/library/azure/dn546722.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Create Database (Azure SQL Data Warehouse)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[Webplatforminstallatieprogramma voor Microsoft]: https://aka.ms/webpi-azps
[Prijzen van SQL Data Warehouse]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Gratis proefversie van Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[Azure-tegoed met MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F



<!--HONumber=Jun16_HO2-->


