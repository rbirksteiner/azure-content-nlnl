<properties
    pageTitle="Een nieuwe pool voor elastische database maken met PowerShell | Microsoft Azure"
    description="Ontdek hoe u PowerShell gebruikt om Azure SQL Database-resources uit te schalen door een schaalbare pool voor elastische database te maken voor het beheer van meerdere databases."
    services="sql-database"
    documentationCenter=""
    authors="srinia"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="05/27/2016"
    ms.author="srinia"/>

# Een nieuwe pool voor elastische database maken met PowerShell

> [AZURE.SELECTOR]
- [Azure Portal](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)


Ontdek hoe u een [pool voor elastische database](sql-database-elastic-pool.md) maakt met PowerShell-cmdlets. 

Raadpleeg voor algemene foutcodes [SQL-foutcodes voor SQL Database-clienttoepassingen: Databaseverbindingsfout en andere problemen](sql-database-develop-error-messages.md).

> [AZURE.NOTE] Pools voor elastische database zijn momenteel in preview en alleen beschikbaar in SQL Database V12-servers. Als u een SQL Database V11-server hebt, kunt u [PowerShell gebruiken om te upgraden naar V12 en in één stap een pool te maken](sql-database-upgrade-server-portal.md).


U moet Azure PowerShell 1.0 of hoger gebruiken. Zie voor gedetailleerde informatie [Installeren en configureren van Azure PowerShell](../powershell-install-configure.md).

## Een nieuwe pool maken

De [New-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378.aspx)-cmdlet maakt een nieuwe pool. De waarden voor eDTU per pool, min. en max. DTU’s zijn beperkt door de waarde van de servicecategorie (Basic, Standard of Premium). Zie [eDTU en opslaglimieten voor elastische pools en elastische databases](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases).

    New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


## Een nieuwe elastische database in een pool maken

Gebruik de [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx)-cmdlet en stel de **ElasticPoolName**-parameter in voor de doelpool. Zie [Een database in een elastische pool plaatsen ](sql-database-elastic-pool-manage-powershell.md#Move-a-database-into-an-elastic-pool) om een bestaande database in een elastische pool te plaatsen.

    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## Een pool maken en deze vullen met meerdere nieuwe databases 

Het maken van een groot aantal databases in een pool kan tijd kosten wanneer het wordt gedaan via de portal of PowerShell-cmdlets die slechts één database tegelijkertijd maken. Als u het maken voor een nieuwe pool wilt automatiseren, raadpleegt u [CreateOrUpdateElasticPoolAndPopulate ](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).   

## Voorbeeld: een pool maken met PowerShell
 

Dit script maakt een nieuwe Azure-resourcegroep en een nieuwe server. Als dit wordt gevraagd, geeft u een gebruikersnaam voor de beheerder op en een wachtwoord voor de nieuwe server (niet uw Azure-referenties).

    $subscriptionId = '<your Azure subscription id>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $serverName = '<server name>'
    $poolName = '<pool name>'
    $databaseName = '<database name>'

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $location -ServerVersion "12.0"
    New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

    New-AzureRmSqlElasticPool -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100

    New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -ElasticPoolName $poolName -MaxSizeBytes 10GB



## Volgende stappen

- [Uw pool beheren](sql-database-elastic-pool-manage-powershell.md)
- [Elastische taken maken](sql-database-elastic-jobs-overview.md) Met elastische taken kunt u de T-SQL-scripts uitvoeren op een willekeurig aantal databases in de pool.
- [Uitschalen met Azure SQL Database](sql-database-elastic-scale-introduction.md): Gebruik elastische database-hulpmiddelen om uit te schalen.




<!--HONumber=Jun16_HO2-->


