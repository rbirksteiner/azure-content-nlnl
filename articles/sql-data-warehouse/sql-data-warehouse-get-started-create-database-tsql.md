<properties
   pageTitle="Een SQL Data Warehouse maken met TSQL | Microsoft Azure"
   description="Meer informatie over het maken van een Azure SQL Data Warehouse met TSQL"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/04/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Een SQL Data Warehouse-database maken met behulp van Transact-SQL (TSQL)

> [AZURE.SELECTOR]
- [Azure Portal](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

In dit artikel wordt beschreven hoe u een SQL Data Warehouse-database maakt met Transact-SQL (TSQL).

## Vereisten
Zorg, voordat u begint, dat u beschikt over het volgende.

- **Azure-account**: zie [Gratis proefversie van Azure][] of [Azure-tegoed met MSDN][] om een account te maken.
- **V12 Azure SQL Server**: zie [Een logische Azure SQL Database-server maken met de Azure Portal][] of [Een logische Azure SQL Database-server maken met PowerShell][].
- **Naam van resourcegroep**: gebruik dezelfde resourcegroep als de V12 Azure SQL Server of zie [Resourcegroepen][] als u een nieuwe resourcegroep wilt maken.
- **Visual Studio met SQL Server Data Tools**: zie [Visual Studio en SSDT installeren][] voor installatie-instructies.

> [AZURE.NOTE] Het maken van een nieuwe SQL Data Warehouse kan leiden tot een nieuwe factureerbare service.  Zie [Prijzen van SQL Data Warehouse][] voor meer informatie over prijzen.

## Een database maken met Visual Studio

Als u niet bekend bent met Visual Studio, raadpleeg dan het artikel [Verbinding maken met SQL Data Warehouse met Visual Studio][].  Om te beginnen, opent u SQL Server-objectverkenner in Visual Studio en maakt u verbinding met de server die uw SQL Data Warehouse-database gaat hosten.  Als de verbinding is gemaakt, kunt u een SQL Data Warehouse maken door de volgende SQL-opdracht uit te voeren voor **hoofd**database.  Met deze opdracht wordt de database MySqlDwDb gemaakt met een serviceniveaudoelstelling van DW400 en kan de database uitbreiden tot een maximale grootte van 10 TB.

```sql
CREATE DATABASE MySqlDwDb (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## Een database maken met sqlcmd

U kunt dezelfde opdracht ook met sqlcmd uitvoeren door het volgende bij de opdrachtprompt uit te voeren.

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

De parameters **MAXSIZE** en **SERVICE_OBJECTIVE** geven de maximale ruimte op die de database op de schijf mag innemen en de rekenresources die aan uw Data Warehouse-exemplaar zijn toegewezen.  De servicedoelstelling is in wezen een toewijzing van CPU en geheugen die lineair is geschaald met de grootte van DWU.  

MAXSIZE kan een waarde tussen 250 GB en 240 TB zijn.  De servicedoelstelling kan een waarde tussen DW100 en DW2000 zijn.  Voor een volledige lijst van alle geldige waarden voor MAXSIZE en SERVICE_OBJECTIVE raadpleegt u de MSDN-documentatie voor [CREATE DATABASE][].  MAXSIZE en SERVICE_OBJECTIVE kunnen beide worden gewijzigd met de TSQL-opdracht [ALTER DATABASE][].  Wees voorzichtig wanneer u SERVICE_OBJECTIVE verandert. Hierdoor worden services opnieuw gestart en daarbij worden ook alle lopende query's geannuleerd.  Voor een verandering van MAXSIZE hoeven services niet opnieuw te worden gestart, omdat dit een eenvoudige metagegevensbewerking is.

## Volgende stappen
Wanneer de inrichting van uw SQL Data Warehouse is voltooid, kunt u [voorbeeldgegevens laden][] of meer informatie zoeken over [ontwikkelen][], [laden][] of [migreren][].

<!--Article references-->
[een SQL Data Warehouse maken vanuit de Azure Portal]: ./sql-data-warehouse-get-started-provision.md
[Verbinding maken met SQL Data Warehouse met Visual Studio]: ./sql-data-warehouse-get-started-connect.md
[migreren]: ./sql-data-warehouse-overview-migrate.md
[ontwikkelen]: ./sql-data-warehouse-overview-develop.md
[laden]: ./sql-data-warehouse-overview-load.md
[voorbeeldgegevens laden]: ./sql-data-warehouse-get-started-manually-load-samples.md
[Een logische Azure SQL Database-server maken met de Azure Portal]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Een logische Azure SQL Database-server maken met PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[Resourcegroepen]: ../azure-portal/resource-group-portal.md
[Visual Studio en SSDT installeren]: ./sql-data-warehouse-install-visual-studio.md

<!--MSDN references--> 
[CREATE DATABASE]: https://msdn.microsoft.com/library/mt204021.aspx
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx

<!--Other Web references-->
[Prijzen van SQL Data Warehouse]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Gratis proefversie van Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[Azure-tegoed met MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F



<!--HONumber=Jun16_HO2-->


