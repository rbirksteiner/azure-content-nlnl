<properties
    pageTitle="Een pool voor elastische database maken met C# | Microsoft Azure"
    description="Gebruik C# databaseontwikkelingstechnieken om een schaalbare pool voor elastische databases te maken in Azure SQL Database om resources te delen tussen meerdere databases."
    services="sql-database"
    documentationCenter=""
    authors="srinia"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="csharp"
    ms.workload="data-management"
    ms.date="05/27/2016"
    ms.author="srinia"/>

# Een nieuwe pool voor elastische database maken met C&#x23;

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)


Meer informatie over het maken van een [pool voor elastische database](sql-database-elastic-pool.md) met C&#x23;. 

Raadpleeg voor algemene foutcodes [SQL-foutcodes voor SQL Database-clienttoepassingen: Databaseverbindingsfout en andere problemen](sql-database-develop-error-messages.md).

Pools voor elastische database zijn momenteel in preview en alleen beschikbaar in SQL Database V12-servers. Als u een SQL Database V11-server hebt, kunt u [PowerShell gebruiken om te upgraden naar V12 en in één stap een pool te maken](sql-database-upgrade-server-portal.md).

De voorbeelden gebruiken de [SQL Database-bibliotheek voor .NET](https://msdn.microsoft.com/library/azure/mt349017.aspx), dus u dient de bibliotheek te installeren. U kunt deze installeren door de volgende opdracht in te voeren in de [Package Manager Console](http://docs.nuget.org/Consume/Package-Manager-Console) in Visual Studio (**Tools** > **NuGet Package Manager** > **Package Manager Console**):

    PM> Install-Package Microsoft.Azure.Management.Sql –Pre

## Een nieuwe pool maken

Maak een [SqlManagementClient](https://msdn.microsoft.com/library/microsoft.azure.management.sql.sqlmanagementclient)-instantie met waarden uit [Azure Active Directory](sql-database-client-id-keys.md). Maak een [ElasticPoolCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.sql.models.elasticpoolcreateorupdateparameters)-instantie en roep de [CreateOrUpdate](https://msdn.microsoft.com/library/microsoft.azure.management.sql.databaseoperationsextensions.createorupdate)-methode aan. De waarden voor eDTU per pool, min. en max. DTU’s zijn beperkt door de waarde van de servicecategorie (Basic, Standard of Premium). Zie [eDTU en opslaglimieten voor elastische pools en elastische databases](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases).


    ElasticPoolCreateOrUpdateParameters newPoolParameters = new ElasticPoolCreateOrUpdateParameters()
    {
        Location = "South Central US",
        Properties = new ElasticPoolCreateOrUpdateProperties()
        {
            Edition = "Standard",
            Dtu = 400,
            DatabaseDtuMin = 0,
            DatabaseDtuMax = 100
         }
    };

    // Create the pool
    var newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);

## Een nieuwe database in een pool maken

Maak een [DataBaseCreateorUpdateProperties](https://msdn.microsoft.com/library/microsoft.azure.management.sql.models.databasecreateorupdateproperties)-instantie en stel de eigenschappen van de nieuwe database in. Roep vervolgens de CreateOrUpdate-methode aan met de resourcegroep, servernaam en nieuwe databasenaam.

    // Create a database: configure create or update parameters and properties explicitly
    DatabaseCreateOrUpdateParameters newPooledDatabaseParameters = new DatabaseCreateOrUpdateParameters()
    {
        Location = currentServer.Location,
        Properties = new DatabaseCreateOrUpdateProperties()
        {
            Edition = "Standard",
            RequestedServiceObjectiveName = "ElasticPool",
            ElasticPoolName = "ElasticPool1",
            MaxSizeBytes = 268435456000, // 250 GB,
            Collation = "SQL_Latin1_General_CP1_CI_AS"
        }
    };

    var poolDbResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database2", newPooledDatabaseParameters);

Zie [Een database in een elastische pool plaatsen ](sql-database-elastic-pool-manage-csharp.md#Move-a-database-into-an-elastic-pool) om een bestaande database in een elastische pool te plaatsen.

## Voorbeeld: Een pool maken met C&#x23


In dit voorbeeld worden een nieuwe Azure-resourcegroep, een nieuwe Azure SQL Server-instantie en een nieuwe elastische pool gemaakt. 
 

Om dit voorbeeld uit te voeren, heeft u de volgende bibliotheken nodig. U kunt installeren door de volgende opdrachten uit te voeren in de [Package Manager Console](http://docs.nuget.org/Consume/Package-Manager-Console) in Visual Studio (**Tools** > **NuGet Package Manager** > **Package Manager Console**)

    Install-Package Microsoft.Azure.Management.Sql –Pre
    Install-Package Microsoft.Azure.Management.Resources –Pre
    Install-Package Microsoft.Azure.Common.Authentication –Pre

Maak een console-app en vervang de inhoud van Program.cs met het volgende. Om de vereiste client-id en bijbehorende waarden te verkrijgen, raadpleegt u [uw app registreren en de vereiste clientwaarden ophalen voor het verbinden van uw app met SQL Database](sql-database-client-id-keys.md). Gebruik de [Get-AzureRmSubscription](https://msdn.microsoft.com/library/mt619284.aspx)-cmdlet om de waarde van de abonnements-ID op te halen.

    using Microsoft.Azure;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    
    namespace SqlDbElasticPoolsSample
    {
    class Program
    {
        // authentication variables
        static string subscriptionId = "<your Azure subscription>";
        static string clientId = "<your client id>";
        static string redirectUri = "<your redirect URI>";
        static string domainName = "<i.e. microsoft.onmicrosoft.com>";
        static AuthenticationResult token;

        // resource group variables
        static string datacenterLocation = "<location>";
        static string resourceGroupName = "<resource group name>";

        // server variables
        static string serverName = "<server name>";
        static string adminLogin = "<server admin>";
        static string adminPassword = "<server password (store it securely!)>";
        static string serverVersion = "12.0";

        // pool variables
        static string elasticPoolName = "<pool name>";
        static string poolEdition = "Standard";
        static int poolDtus = 400;
        static int databaseMinDtus = 0;
        static int databaseMaxDtus = 100;


        static void Main(string[] args)
        {
            // Sign in to Azure
            token = GetAccessToken();
            Console.WriteLine("Logged in as: " + token.UserInfo.DisplayableId);

            // Create a resource group
            ResourceGroup rg = CreateResourceGroup();
            Console.WriteLine(rg.Name + " created.");

            // Create a server
            Console.WriteLine("Creating server... ");
            ServerGetResponse srvr = CreateServer();
            Console.WriteLine("Creation of server " + srvr.Server.Name + ": " + srvr.StatusCode.ToString());

            // Create a pool
            Console.WriteLine("Creating elastic database pool... ");
            ElasticPoolCreateOrUpdateResponse epool = CreateElasticDatabasePool();
            Console.WriteLine("Creation of pool " + epool.ElasticPool.Name + ": " + epool.Status.ToString());


            // keep the console open until Enter is pressed!
            Console.WriteLine("Press Enter to exit.");
            Console.ReadLine();
        }


        static ElasticPoolCreateOrUpdateResponse CreateElasticDatabasePool()
        {
            // Create a SQL Database management client
            SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            // Create elastic pool: configure create or update parameters and properties explicitly
            ElasticPoolCreateOrUpdateParameters newPoolParameters = new ElasticPoolCreateOrUpdateParameters()
            {
                Location = datacenterLocation,
                Properties = new ElasticPoolCreateOrUpdateProperties()
                {
                    Edition = poolEdition,
                    Dtu = poolDtus, 
                    DatabaseDtuMin = databaseMinDtus,
                    DatabaseDtuMax = databaseMaxDtus
                }
            };

            // Create the pool
            var newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate(resourceGroupName, serverName, elasticPoolName, newPoolParameters);
            return newPoolResponse;
        }



        static ServerGetResponse CreateServer()
        {

            // Create a SQL Database management client
            SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            // Create a server
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = datacenterLocation,
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = adminLogin,
                    AdministratorLoginPassword = adminPassword,
                    Version = serverVersion
                }
            };

            var serverResult = sqlClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
            return serverResult;

        }


        static ResourceGroup CreateResourceGroup()
        {
           
            Microsoft.Rest.TokenCredentials creds = new Microsoft.Rest.TokenCredentials(token.AccessToken);
            // Create a resource management client 
            ResourceManagementClient resourceClient = new ResourceManagementClient(creds);

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = datacenterLocation,
            };

            //Create a resource group
            resourceClient.SubscriptionId = subscriptionId;
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
            return resourceGroupResult;
        }


        private static AuthenticationResult GetAccessToken()
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" + domainName /* Tenant ID or AAD domain */);

            AuthenticationResult token = authContext.AcquireToken
                ("https://management.azure.com/"/* the Azure Resource Management endpoint */,
                    clientId,
            new Uri(redirectUri) /* redirect URI */,
            PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

            return token;
        }
    }
    }

  

## Volgende stappen

- [Uw pool beheren](sql-database-elastic-pool-manage-csharp.md)
- [Elastische taken maken](sql-database-elastic-jobs-overview.md): Met elastische taken kunt u de T-SQL-scripts uitvoeren op een willekeurig aantal databases in de pool.
- [Uitschalen met Azure SQL Database](sql-database-elastic-scale-introduction.md): Gebruik elastische database-hulpmiddelen om uit te schalen.

## Aanvullende bronnen

- [SQL Database](https://azure.microsoft.com/documentation/services/sql-database/)
- [Azure Resource Management API's](https://msdn.microsoft.com/library/azure/dn948464.aspx)


<!--HONumber=Jun16_HO2-->


