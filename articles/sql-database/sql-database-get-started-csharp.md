<properties
    pageTitle="SQL Database uitproberen: een SQL-database maken met C# | Microsoft Azure"
    description="Probeer SQL Database om SQL- en C#-apps te ontwikkelen en om een Azure SQL Database te maken met C# met behulp van de SQL Database-bibliotheek voor .NET."
    keywords="try sql, sql c#"   
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="csharp"
   ms.workload="data-management"
   ms.date="05/26/2016"
   ms.author="sstein"/>

# SQL Database uitproberen: een SQL-database maken met C# met de SQL Database-bibliotheek voor .NET


> [AZURE.SELECTOR]
- [Azure Portal](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)

Lees hoe u C#-opdrachten gebruikt om een Azure SQL-database te maken met de [Azure SQL Database-bibliotheek voor .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql). U gaat SQL Database uitproberen door een individuele database te maken met SQL en C#. Zie [Een pool voor elastische database maken](sql-database-elastic-pool-create-portal.md) als u een pool voor elastische database wilt maken. Voor de duidelijkheid zijn afzonderlijke codefragmenten uitgelicht. In een voorbeeld van een console-toepassing in de sectie onder aan dit artikel worden alle opdrachten samengevoegd.

De Azure SQL Database-bibliotheek voor .NET biedt een op [Azure Resource Manager](../resource-group-overview.md) gebaseerde API die de op [Resource Manager gebaseerde SQL Database-REST-API](https://msdn.microsoft.com/library/azure/mt163571.aspx) bevat. Deze clientbibliotheek volgt het algemene patroon voor op Resource Manager gebaseerde clientbibliotheken. Resource Manager vereist resourcegroepen en verificatie met [Azure Active Directory](https://msdn.microsoft.com/library/azure/mt168838.aspx) (AAD).

<br>

> [AZURE.NOTE] De SQL Database-bibliotheek voor .NET is momenteel als preview-versie beschikbaar.

<br>

U hebt het volgende nodig om de stappen in dit artikel uit te voeren:

- Een Azure-abonnement. Als u een Azure-abonnement nodig hebt, klikt u gewoon op **Gratis proefversie** boven aan deze pagina. Keer daarna hier terug om dit artikel te voltooien.
- Visual Studio. Ga naar de pagina [Visual Studio-downloads](https://www.visualstudio.com/downloads/download-visual-studio-vs) voor een gratis exemplaar van Visual Studio.


## De vereiste bibliotheken installeren

Als u een SQL-database wilt instellen met C#, zorgt u eerst voor de vereiste managementbibliotheken door de volgende pakketten te installeren met behulp van de [pakketbeheerconsole](http://docs.nuget.org/Consume/Package-Manager-Console) in Visual Studio (**Tools** > **NuGet Package Manager** > **Package Manager Console**):

    Install-Package Microsoft.Azure.Management.Sql –Pre
    Install-Package Microsoft.Azure.Management.ResourceManager –Pre
    Install-Package Microsoft.Azure.Common.Authentication –Pre


## Verificatie met Azure Active Directory configureren

U moet eerst mogelijk maken dat de clienttoepassing toegang krijgt tot de SQL Database-service door de vereiste verificatie in te schakelen.

Als u de clienttoepassing wilt verifiëren op basis van de huidige gebruiker, moet u de toepassing eerst registreren in het AAD-domein dat is gekoppeld aan het abonnement waaronder de Azure-resources zijn gemaakt. Als uw Azure-abonnement is gemaakt met een Microsoft-account in plaats van een werk- of schoolaccount, dan hebt u al een standaard-AAD-domein.

Ga als volgt te werk om een nieuwe toepassing te maken en deze te registreren in de juiste Active Directory:

1. Ga naar de [klassieke Azure Portal](https://manage.windowsazure.com/).
1. Selecteer links de **Active Directory**-service en selecteer vervolgens de directory voor de verificatie van uw toepassing en klik op de **naam** ervan.

    ![SQL Database uitproberen: Azure Active Directory (AAD) instellen.][1]

2. Klik op de directory-pagina op **Toepassingen**.

    ![De directory-pagina met Toepassingen.][5]

4. Klik op **Toevoegen** om een nieuwe toepassing te maken.

5. Selecteer **Add an application my organization is developing** (Een toepassing toevoegen die door mijn organisatie wordt ontwikkeld).

5. Geef een **naam** op voor de app en selecteer **Native Client Application** (Systeemeigen clienttoepassing).

    ![Verstrek informatie over uw SQL C#-toepassing.][7]

6. Geef een **Redirect URI** (Omleidings-URI) op. Dit hoeft geen echt eindpunt te zijn, alleen een geldige URI.

    ![Voeg een omleidings-URL toe voor de SQL C#-toepassing.][8]

7. Voltooi de app, klik op **Configureren** en kopieer de **Client-id** (die hebt u later in uw code nodig).

    ![Haal client-id op voor de SQL C#-toepassing.][9]


1. Klik onder aan de pagina op **Toepassing toevoegen**.
1. Selecteer **Microsoft-apps**.
1. Selecteer **Azure Service Management-API** en voltooi de wizard.
2. Als de API is geselecteerd, moet u de specifieke machtigingen toekennen die zijn vereist voor toegang tot deze API. Daartoe selecteert u **Access Azure Service Management (preview)** (Azure Service Management openen (preview)).

    ![Stel machtigingen in.][2]

2. Klik op **OPSLAAN**.



### De domeinnaam identificeren

De domeinnaam is vereist voor uw code. Er is een eenvoudige manier om de juiste domeinnaam te identificeren:

1. Ga naar de [Azure Portal](http://portal.azure.com).
2. Houd de muisaanwijzer boven uw naam in de rechterbovenhoek en noteer het domein dat in het pop-upvenster wordt weergegeven.

    ![Identificeer de domeinnaam.][3]





**Aanvullende informatie over AAD**  

Meer informatie over het gebruik van Azure Active Directory voor verificatie vindt u in [dit interessante blogbericht](http://www.cloudidentity.com/blog/2013/09/12/active-directory-authentication-library-adal-v1-for-net-general-availability/).


### Het toegangstoken voor de huidige gebruiker ophalen

De clienttoepassing moet het toegangstoken voor de toepassing ophalen voor de huidige gebruiker. Wanneer een gebruiker deze code de eerste keer uitvoert, wordt de gebruiker gevraagd zijn of haar gebruikersreferenties in te voeren, waarna het resulterende token lokaal in de cache wordt opgeslagen. Bij een volgende uitvoering wordt het token opgehaald uit de cache en hoeft de gebruiker zich alleen nog aan te melden als het token is verlopen.

Deze code retourneert een Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationResult dat u nodig hebt in de andere codefragmenten hieronder.

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



> [AZURE.NOTE] De voorbeelden in dit artikel gebruiken een synchrone vorm van elke API-aanvraag en blokkeren totdat de REST-aanroep van de onderliggende service is voltooid. Er zijn asynchrone methoden beschikbaar.



## Een resourcegroep maken

Alle resources moeten met Resource Manager worden gemaakt in een resourcegroep. Een resourcegroep is een container met verwante resources voor een toepassing. Met Azure SQL Database moet de databaseserver worden gemaakt in een bestaande resourcegroep.

        static void CreateResourceGroup()
        {
            creds = new Microsoft.Rest.TokenCredentials(token.AccessToken);

            // Create a resource management client
            ResourceManagementClient resourceClient = new ResourceManagementClient(creds);

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = location,
            };

            //Create a resource group
            resourceClient.SubscriptionId = subscriptionId;
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
        }


## Een server maken

SQL-databases zijn opgenomen in servers. De servernaam moet globaal uniek zijn in alle Azure SQL-servers. Er wordt hier dus een foutmelding weergegeven als de servernaam al in gebruik is. Het is ook handig te weten dat deze opdracht enkele minuten in beslag kan nemen.

    static void CreateServer()
    {
        // Create a SQL Database management client
        SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

        // Create a server
        ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
        {
            Location = location,
            Properties = new ServerCreateOrUpdateProperties()
            {
                AdministratorLogin = administratorLogin,
                AdministratorLoginPassword = administratorPassword,
                Version = serverVersion
            }
        };
            var serverResult = sqlClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
    }


### Een externe serverbeheerder maken


    // Create a server external admin
    ServerAdministratorCreateOrUpdateParameters aadAdminParameters = new ServerAdministratorCreateOrUpdateParameters()
    {
        Properties = new ServerAdministratorCreateOrUpdateProperties()
        {
            AdministratorType = "ActiveDirectory",
            Login = "<login>",
            Sid = new Guid("<Azure AD admin sid>"),
            TenantId = new Guid("<Azure AD tenant id>")
        }
    };

    var adminResult = sqlClient.ServerAdministrators.CreateOrUpdate(resourceGroupName, serverName, "ActiveDirectory", aadAdminParameters);
    var adminGetResult = sqlClient.ServerAdministrators.Get(resourceGroupName, serverName, "ActiveDirectory");




## Een serverfirewallregel maken om toegang tot de server toe te staan

Standaard kan niet vanaf elke locatie verbinding worden gemaakt met een server. Om verbinding te maken met een server of een database op de server, moet een [firewallregel](https://msdn.microsoft.com/library/azure/ee621782.aspx) worden gedefinieerd die toegang toestaat vanuit het IP-adres van de client.

In het volgende voorbeeld wordt een regel gemaakt die toegang tot de server toestaat vanuit elk IP-adres. Het wordt aanbevolen om uw database te beveiligen met de juiste SQL-aanmeldingen en wachtwoorden, en firewallregels niet als primaire beveiliging tegen indringers te gebruiken.


        static void CreateFirewallRule()
        {
            // Create a firewall rule on the server
            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    StartIpAddress = "0.0.0.0",
                    EndIpAddress = "255.255.255.255"
                }
            };
            var firewallResult = sqlClient.FirewallRules.CreateOrUpdate(resourceGroupName, serverName, firewallRuleName, firewallParameters);
        }




Als u wilt dat andere Azure-services toegang hebben tot een server, voegt u een firewallregel toe en stelt u zowel het StartIpAddress als het EndIpAddress in op 0.0.0.0. Hiermee geeft u Azure-verkeer vanuit *elk* Azure-abonnement toegang tot de server.


## Een SQL-database maken met C&#x23;

Met de volgende C#-opdracht wordt een nieuwe SQL-database gemaakt als er nog geen database met dezelfde naam op de server bestaat. Als er wel een database met dezelfde naam bestaat, wordt deze bijgewerkt.


        static void CreateDatabase()
        {
            // Create a database

            // Retrieve the server on which the database will be created
            Server currentServer = sqlClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = databaseEdition,
                    RequestedServiceObjectiveName = databasePerfLevel,
                }
            };
            var dbResponse = sqlClient.Databases.CreateOrUpdate(resourceGroupName, serverName, databaseName, newDatabaseParameters);
        }



## Voorbeeld van C&#x23;-consoletoepassing

In het volgende voorbeeld worden een resourcegroep, een server, een firewallregel en een SQL-database gemaakt. In de sectie *Verificatie met Azure Active Directory configureren*, boven in dit artikel, wordt uitgelegd waar u de waarden ophaalt voor de variabelen clientId, redirectUri en domainName.


    using Microsoft.Azure;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;

    namespace SqlDbConsoleApp
    {
    class Program
    {
        // Get these values from the Azure portal
        static string subscriptionId = "<Azure subscription id>";
        static string clientId = "<Azure App clientId>";
        static string redirectUri = "<Azure App redirectURI>";
        static string domainName = "<domain>";

        // You create these values
        static string resourceGroupName = "<your resource group name>";
        static string location = "<Azure data center location>";

        static string serverName = "<your server name>";
        static string administratorLogin = "<your server admin>";

        // store your password securely!
        static string administratorPassword = "<your server admin password>";
        static string serverVersion = "12.0";

        static string firewallRuleName = "<your firewall rule name>";

        static string databaseName = "dbfromcsharparticle";
        static string databaseEdition = "Basic"; // Basic, Standard, or Premium
        static string databasePerfLevel = "";

        static AuthenticationResult token;
        static Microsoft.Rest.TokenCredentials creds;

        static SqlManagementClient sqlClient;


        static void Main(string[] args)
        {
            Console.WriteLine("Logging in...");

            token = GetAccessToken();

            Console.WriteLine("Logged in as: " + token.UserInfo.DisplayableId);

            Console.WriteLine("Creating resource group...");
            CreateResourceGroup();

            sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            Console.WriteLine("Creating server...");
            CreateServer();

            Console.WriteLine("Creating firewall rule...");
            CreateFirewallRule();

            Console.WriteLine("Creating database...");

            DatabaseCreateOrUpdateResponse dbResponse = CreateDatabase();
            Console.WriteLine("Status: " + dbResponse.Status.ToString()
                + " Code: " + dbResponse.StatusCode.ToString());

            Console.WriteLine("Press enter to exit...");
            Console.ReadLine();
        }

        static void CreateResourceGroup()
        {
            creds = new Microsoft.Rest.TokenCredentials(token.AccessToken);

            // Create a resource management client
            ResourceManagementClient resourceClient = new ResourceManagementClient(creds);

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = location,
            };

            //Create a resource group
            resourceClient.SubscriptionId = subscriptionId;
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
        }

        static void CreateServer()
        {
            // Create a SQL Database management client
            //SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            // Create a server
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = location,
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = administratorLogin,
                    AdministratorLoginPassword = administratorPassword,
                    Version = serverVersion
                }
            };
            var serverResult = sqlClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
        }

        static void CreateFirewallRule()
        {
            // Create a firewall rule on the server
            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    // replace with your client IP address
                    StartIpAddress = "0.0.0.0",
                    EndIpAddress = "255.255.255.255"
                }
            };
            var firewallResult = sqlClient.FirewallRules.CreateOrUpdate(resourceGroupName, serverName, firewallRuleName, firewallParameters);
        }

        static DatabaseCreateOrUpdateResponse CreateDatabase()
        {
            // Create a database

            // Retrieve the server on which the database will be created
            Server currentServer = sqlClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = databaseEdition,
                    RequestedServiceObjectiveName = databasePerfLevel,
                }
            };
            var dbResponse = sqlClient.Databases.CreateOrUpdate(resourceGroupName, serverName, databaseName, newDatabaseParameters);
            return dbResponse;
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
Nu u SQL Database hebt uitgeprobeerd en een database hebt ingesteld met C#, bent u klaar voor de volgende artikelen:

- [Verbinding maken met SQL Database met SQL Server Management Studio en een voorbeeld-T-SQL-query uitvoeren](sql-database-connect-query-ssms.md)

## Aanvullende bronnen

- [SQL Database](https://azure.microsoft.com/documentation/services/sql-database/)





<!--Image references-->
[1]: ./media/sql-database-get-started-csharp/aad.png
[2]: ./media/sql-database-get-started-csharp/permissions.png
[3]: ./media/sql-database-get-started-csharp/getdomain.png
[4]: ./media/sql-database-get-started-csharp/aad2.png
[5]: ./media/sql-database-get-started-csharp/aad-applications.png
[6]: ./media/sql-database-get-started-csharp/add.png
[7]: ./media/sql-database-get-started-csharp/add-application.png
[8]: ./media/sql-database-get-started-csharp/add-application2.png
[9]: ./media/sql-database-get-started-csharp/clientid.png



<!--HONumber=Jun16_HO2-->


