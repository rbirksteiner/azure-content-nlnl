<properties 
    pageTitle="Nieuwe SQL Database instellen met PowerShell | Microsoft Azure" 
    description="Ontdek hoe u een nieuwe SQL Database maakt met PowerShell. U kunt algemene database-instellingstaken beheren met PowerShell-cmdlets." 
    keywords="create new sql database,database setup"
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="hero-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="05/09/2016"
    ms.author="sstein"/>

# Maak een nieuwe SQL Database aan en voer algemene database-instellingstaken uit met PowerShell-cmdlets 


> [AZURE.SELECTOR]
- [Azure Portal](sql-database-get-started.md)
- [PowerShell](sql-database-get-started-powershell.md)
- [C#](sql-database-get-started-csharp.md)



Ontdek hoe u een nieuwe SQL Database maakt met PowerShell-cmdlets. (Voor het maken van de elastische databases raadpleegt u [Een nieuwe pool voor elastische database maken met PowerShell](sql-database-elastic-pool-create-powershell.md).)


[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## Database instellen: een resourcegroep, server en firewallregel maken

U hebt nu de mogelijkheid om cmdlets uit te voeren voor uw geselecteerde Azure-abonnement. De volgende stap is om de resourcegroep tot stand te brengen met de server waarin u de database gaat maken. U kunt de volgende opdracht bewerken zodat u welke geldige locatie dan ook kunt gebruiken. Voer **(Get-AzureRmLocation | Where-Object { $_.Providers -eq "Microsoft.Sql" }).Location** uit om een lijst geldige locaties op te halen.

Voer de volgende opdracht uit om een nieuwe resourcegroep te maken:

    New-AzureRmResourceGroup -Name "resourcegroupsqlgsps" -Location "West US"

Wanneer u de nieuwe resourcegroep hebt gemaakt, krijgt u informatie te zien, waaronder **ProvisioningState: voltooid**.


### Een server maken 

SQL Databases worden in Azure SQL Database-servers gemaakt. Voer **New-AzureRmSqlServer** uit om een nieuwe server te maken. Vervang ServerName door de naam voor uw server. De servernaam moet uniek binnen alle Azure SQL-servers. Er wordt hier dus een foutmelding weergegeven als de servernaam al in gebruik is. Het is ook handig te weten dat deze opdracht enkele minuten in beslag kan nemen. U kunt de opdracht wijzigen om een geldige locatie naar keuze te gebruiken, maar u moet dezelfde locatie gebruiken als de locatie die u hebt gebruikt voor de resourcegroep die u tijdens de vorige stap hebt gemaakt.

    New-AzureRmSqlServer -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -Location "West US" -ServerVersion "12.0"

Wanneer u deze opdracht uitvoert, wordt er een venster weergegeven waarin u een **gebruikersnaam** en **wachtwoord** moet opgeven. Dit zijn niet uw Azure-referenties; geef een gebruikersnaam en wachtwoord op voor de beheerder die u wilt aanmaken voor de nieuwe server.

De servergegevens worden weergegeven nadat de server is gemaakt.

### Een serverfirewallregel configureren om toegang tot de server toe te staan

Stel een firewallregel in voor toegang tot de server. Voer de volgende opdracht uit, maar vervang de begin- en eind-IP-adressen door waarden die geldig zijn voor uw computer.

    New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.0" -EndIpAddress "192.168.0.0"

De firewallregelgegevens worden weergegeven nadat de regel is gemaakt.

Als u wilt dat andere Azure-services toegang hebben tot de server, voegt u een firewallregel toe en stelt u zowel het StartIpAddress als het EndIpAddress in op 0.0.0.0. Hiermee staat u Azure-verkeer toe tussen Azure-abonnementen en de server.

Zie [Azure SQL Database-firewall](sql-database-firewall-configure.md) voor meer informatie.


## Een nieuwe SQL-database maken

Nu u een resourcegroep, een server en een firewallregel hebt geconfigureerd, kunt u de server benaderen.

Met de volgende opdracht maakt u een nieuwe (lege) SQL Database op het serviceniveau Standard, met het prestatieniveau S1:


    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -DatabaseName "database1" -Edition "Standard" -RequestedServiceObjectiveName "S1"


De databasegegevens worden weergegeven nadat de database is gemaakt.

## Een nieuwe SQL-database maken met een PowerShell-script

    $SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    $ResourceGroupName = "resourcegroupname"
    $Location = "Japan West"
    
    $ServerName = "uniqueservername"
    
    $FirewallRuleName = "rule1"
    $FirewallStartIP = "192.168.0.0"
    $FirewallEndIp = "192.168.0.0"
    
    $DatabaseName = "database1"
    $DatabaseEdition = "Standard"
    $DatabasePerfomanceLevel = "S1"
    
    
    Add-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId $SubscriptionId
    
    $ResourceGroup = New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
    
    $Server = New-AzureRmSqlServer -ResourceGroupName $ResourceGroupName -ServerName $ServerName -Location $Location -ServerVersion "12.0"
    
    $FirewallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $ServerName -FirewallRuleName $FirewallRuleName -StartIpAddress $FirewallStartIP -EndIpAddress $FirewallEndIp
    
    $SqlDatabase = New-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -Edition $DatabaseEdition -RequestedServiceObjectiveName $DatabasePerfomanceLevel
    
    $SqlDatabase
    


## Volgende stappen
Wanneer u een nieuwe SQL-database hebt gemaakt en u de algemene installatietaken voor de database hebt uitgevoerd, bent u klaar voor het volgende:

- [Verbinding maken met SQL Database met SQL Server Management Studio en een voorbeeld-T-SQL-query uitvoeren](sql-database-connect-query-ssms.md)


## Aanvullende bronnen

- [Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/)



<!--HONumber=Jun16_HO2-->


