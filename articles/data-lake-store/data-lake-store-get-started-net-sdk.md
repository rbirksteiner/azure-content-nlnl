<properties
   pageTitle="Data Lake Store .NET SDK gebruiken om toepassingen te ontwikkelen | Azure"
   description="Azure Data Lake Store .NET SDK gebruiken om toepassingen te ontwikkelen"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="04/27/2016"
   ms.author="nitinme"/>

# Aan de slag met Azure Data Lake Store met .NET SDK

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [REST API](data-lake-store-get-started-rest-api.md)
- [Azure CLI](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

Lees hoe u met de Azure Data Lake Store .NET SDK een Azure Data Lake-account maakt en basisbewerkingen uitvoert, zoals het maken van mappen, uploaden en downloaden van gegevensbestanden, verwijderen van uw account enzovoort. Zie [Azure Data Lake Store](data-lake-store-overview.md) voor meer informatie over Data Lake.

## Vereisten

* Visual Studio 2013 of 2015 In onderstaande instructies wordt Visual Studio 2015 gebruikt.
* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
* **Uw Azure-abonnement inschakelen** voor de openbare preview van Data Lake Store. Zie [Instructies](data-lake-store-get-started-portal.md#signup).
* **Een Azure Active Directory-toepassing maken**. Er zijn twee manieren waarop u kunt verifiëren met behulp van Azure Active Directory: **interactief** en **niet-interactief**. De vereisten zijn afhankelijk van de manier waarop u wilt verifiëren.
    * **Voor interactieve verificatie** (dat in dit artikel wordt gebruikt) moet u in Azure Active Directory een **Native Client-toepassing** maken. Wanneer u de toepassing hebt gemaakt, haalt u de volgende waarden op met betrekking tot de toepassing.
        - **Client-id** en **omleidings-URI** voor de toepassing ophalen
        - Gedelegeerde machtigingen instellen

    * **Voor niet-interactieve verificatie** moet u in Azure Active Directory een **webtoepassing** maken. Wanneer u de toepassing hebt gemaakt, haalt u de volgende waarden op met betrekking tot de toepassing.
        - **Client-id**, **clientgeheim** en **omleidings-URI** voor de toepassing ophalen
        - Gedelegeerde machtigingen instellen
        - Wijs de Azure Active Directory-toepassing toe aan een rol. De rol kan zich bevinden op het niveau van het bereik waarmee u toegang wilt geven aan de Azure Active Directory-toepassing. U kunt de toepassing bijvoorbeeld toewijzen op abonnementsniveau of op het niveau van een resourcegroep. 

    Zie [Een Active Directory-toepassing en service-principal maken met portal](../resource-group-create-service-principal-portal.md) als u wilt weten hoe u deze waarden ophaalt, de machtigingen instelt en rollen toewijst.

## Een .NET-toepassing maken

1. Open Visual Studio en maak een consoletoepassing.

2. Klik in het menu **File** op **New** en klik vervolgens op **Project**.

3. In **New Project** typt of selecteert u de volgende waarden:

  	| Eigenschap | Waarde                       |
  	|----------|-----------------------------|
  	| Category | Templates/Visual C#/Windows |
  	| Template | Console Application         |
  	| Name     | CreateADLApplication        |

4. Klik op **OK** om het project aan te maken.

5. Voeg de Nuget-pakketten toe aan het project.

    1. Klik in Solution Explorer met de rechtermuisknop op de projectnaam en klik op **Manage NuGet Packages**.
    2. Controleer op het tabblad **Nuget Package Manager** of **Package source** is ingesteld op **nuget.org** en of het selectievakje **Include prerelease** is ingeschakeld.
    3. Zoek en installeer de volgende Data Lake Store-pakketten:

        * `Microsoft.Azure.Management.DataLake.Store`
        * `Microsoft.Azure.Management.DataLake.StoreUploader`

        ![Een Nuget-bron toevoegen](./media/data-lake-store-get-started-net-sdk/ADL.Install.Nuget.Package.png "Create a new Azure Data Lake account")

    4. Installeer ook het pakket `Microsoft.IdentityModel.Clients.ActiveDirectory` voor Azure Active Directory-verificatie. Zorg dat u het selectievakje **Include prerelease** *uitschakelt*, zodat u een stabiele versie van dit pakket installeert.

        ![Een Nuget-bron toevoegen](./media/data-lake-store-get-started-net-sdk/adl.install.azure.auth.png "Create a new Azure Data Lake account")


    5. Sluit de **Nuget Package Manager**.

7. Open **Program.cs**, verwijder de bestaande code en neem de volgende instructies op om verwijzingen naar naamruimten toe te voegen.

        using System;
        using System.IO;
        using System.Security;
        using System.Text;
        using System.Collections.Generic;
        using System.Linq;

        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.Azure.Management.DataLake.StoreUploader;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Rest;

8. Declareer de variabelen zoals hieronder wordt weergegeven, en geef de waarden op voor de naam van de Data Lake Store en de naam van de resourcegroep. De naam van de Data Lake Store die u opgeeft wordt door de toepassing gemaakt. De resourcegroep die u hier opgeeft, moet al bestaan. Ook het lokale pad en de bestandsnaam die u hier opgeeft, moeten al bestaan op de computer. Voeg het volgende codefragment toe na de naamruimtedeclaraties.

        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreAccountManagementClient _adlsClient;
                private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;

                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;

                private static void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name for a NEW Store account.
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; // TODO: Replace this value. This resource group should already exist.
                    _location = "East US 2";

                    string localFolderPath = @"C:\local_path\"; // TODO: Make sure this exists and can be overwritten.
                    string localFilePath = localFolderPath + "file.txt"; // TODO: Make sure this exists and can be overwritten.
                    string remoteFolderPath = "/data_lake_path/";
                    string remoteFilePath = remoteFolderPath + "file.txt";
                }
            }
        }

In de rest van het artikel ziet u het gebruik van de beschikbare .NET-methoden voor het uitvoeren van bewerkingen, zoals het verifiëren van gebruikers, het maken van een Data Lake Store-account, het uploaden van bestanden, enzovoort. Zie de [Bijlage](#appendix-sample-code) onder aan dit artikel voor een compleet codevoorbeeld voor het werken met Data Lake Store.

## De gebruiker verifiëren

Azure Active Directory heeft twee verificatiemethoden:

* **Interactief**, waarbij een gebruiker zich aanmeldt met de toepassing. Dit is geïmplementeerd in de methode `AuthenticateUser` in het onderstaande codefragment.

* **Niet-interactief**, waarbij de toepassing eigen referenties gebruikt. Dit is geïmplementeerd in de methode `AuthenticateAppliaction` in het onderstaande codefragment.

### Interactieve verificatie

Het volgende codefragment bevat de methode `AuthenticateUser`, die u kunt gebruiken voor interactief aanmelden.

    // Authenticate the user with AAD through an interactive popup.
    // You need to have an application registered with AAD in order to authenticate.
    //   For more information and instructions on how to register your application with AAD, see:
    //   https://azure.microsoft.com/en-us/documentation/articles/resource-group-create-service-principal-portal/
    public static TokenCredentials AuthenticateUser(string tenantId, string resource, string appClientId, Uri appRedirectUri, string userId = "")
    {
        var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenantId);
    
        var tokenAuthResult = authContext.AcquireToken(resource, appClientId, appRedirectUri,
            PromptBehavior.Auto, UserIdentifier.AnyUser);
    
        return new TokenCredentials(tokenAuthResult.AccessToken);
    }

### Niet-interactieve verificatie

Het volgende codefragment bevat de methode `AuthenticateApplication`, die u kunt gebruiken voor niet-interactief aanmelden.

    // Authenticate the application with AAD through the application's secret key.
    // You need to have an application registered with AAD in order to authenticate.
    //   For more information and instructions on how to register your application with AAD, see:
    //   https://azure.microsoft.com/en-us/documentation/articles/resource-group-create-service-principal-portal/
    public static TokenCredentials AuthenticateApplication(string tenantId, string resource, string appClientId, Uri appRedirectUri, SecureString clientSecret)
    {
        var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenantId);
        var credential = new ClientCredential(appClientId, clientSecret);
    
        var tokenAuthResult = authContext.AcquireToken(resource, credential);
    
        return new TokenCredentials(tokenAuthResult.AccessToken);
    }
    
## Een Data Lake Store-account maken

Het volgende codefragment bevat de methode `CreateAccount`, die u kunt gebruiken voor het maken van een Data Lake Store-account.

    // Create Data Lake Store account
    public static void CreateAccount()
    {
        var adlsParameters = new DataLakeStoreAccount(location: _location);
        _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);
    } 

## Alle Data Lake Store-accounts binnen een abonnement weergeven

Het volgende codefragment bevat de methode `ListAdlStoreAccounts`, die u kunt gebruiken voor het weergeven van alle Data Lake Store-accounts binnen een bepaald Azure-abonnement.

    // List all ADLS accounts within the subscription
    public static List<DataLakeStoreAccount> ListAdlStoreAccounts()
    {
        var response = _adlsClient.Account.List(_adlsAccountName);
        var accounts = new List<DataLakeStoreAccount>(response);
    
        while (response.NextPageLink != null)
        {
            response = _adlsClient.Account.ListNext(response.NextPageLink);
            accounts.AddRange(response);
        }
    
        return accounts;
    }

## Een map maken

Het volgende codefragment bevat de methode `CreateDirectory`, die u kunt gebruiken voor het maken van een map in een Date Lake Store-account.

    // Create a directory
    public static void CreateDirectory(string path)
    {
        _adlsFileSystemClient.FileSystem.Mkdirs(_adlsAccountName, path);
    }

## Bestand uploaden naar Data Lake Store

Het volgende codefragment bevat de methode `UploadFile`, die u kunt gebruiken voor het uploaden van bestanden naar een Date Lake Store-account.

    // Upload a file
    public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
    {
        var parameters = new UploadParameters(srcFilePath, destFilePath, _adlsAccountName, isOverwrite: force);
        var frontend = new DataLakeStoreFrontEndAdapter(_adlsAccountName, _adlsFileSystemClient);
        var uploader = new DataLakeStoreUploader(parameters, frontend);
        uploader.Execute();
    }

## Bestands- of mapinformatie ophalen

Het volgende codefragment bevat de methode `GetItemInfo`, die u kunt gebruiken voor het ophalen van bestands- of mapinformatie die beschikbaar is in Data Lake Store. 

    // Get file or directory info
    public static FileStatusProperties GetItemInfo(string path)
    {
        return _adlsFileSystemClient.FileSystem.GetFileStatus(_adlsAccountName, path).FileStatus;
    }

## Bestanden of mappen weergeven

Het volgende codefragment bevat de methode `ListItem`, die u kunt gebruiken voor het weergeven van de bestanden en mappen in een Date Lake Store-account.
    
    // List files and directories
    public static List<FileStatusProperties> ListItems(string directoryPath)
    {
        return _adlsFileSystemClient.FileSystem.ListFileStatus(_adlsAccountName, directoryPath).FileStatuses.FileStatus.ToList();
    }

## Bestanden samenvoegen

Het volgende codefragment bevat de methode `ConcatenateFiles`, die u kunt gebruiken voor het samenvoegen van bestanden. 

    // Concatenate files
    public static void ConcatenateFiles(string[] srcFilePaths, string destFilePath)
    {
        _adlsFileSystemClient.FileSystem.Concat(_adlsAccountName, destFilePath, srcFilePaths);
    }

## Toevoegen aan een bestand

Het volgende codefragment bevat de methode `AppendToFile`, die u kunt gebruiken voor het toevoegen van gegevens aan een bestand dat al is opgeslagen in een Date Lake Store-account.

    // Append to file
    public static void AppendToFile(string path, string content)
    {
        var stream = new MemoryStream(Encoding.UTF8.GetBytes(content));

        _adlsFileSystemClient.FileSystem.Append(_adlsAccountName, path, stream);
    }

## Bestand downloaden

Het volgende codefragment bevat de methode `DownloadFile`, die u kunt gebruiken voor downloaden van een bestand in een Date Lake Store-account.

    // Download file
    public static void DownloadFile(string srcPath, string destPath)
    {
        var stream = _adlsFileSystemClient.FileSystem.Open(_adlsAccountName, srcPath);
        var fileStream = new FileStream(destPath, FileMode.Create);

        stream.CopyTo(fileStream);
        fileStream.Close();
        stream.Close();
    }

## Een Data Lake Store-account verwijderen

Het volgende codefragment bevat de methode `DeleteAccount`, die u kunt gebruiken voor het verwijderen van een Data Lake Store-account.

    // Delete account
    public static void DeleteAccount()
    {
        _adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);
    }

## Bijlage: voorbeeldcode

Het volgende fragment bevat uitgebreide voorbeeldcode die u kunt kopiëren en plakken in uw toepassing om een complete bewerking met Data Lake Store te zien. Voordat u het codefragment uitvoert, moet u de vereiste waarden opgeven, zoals de naam van de Data Lake Store, de naam van de resourcegroep, enzovoort. Ook moet u de vereiste waarden voor Azure Active Directory-verificatie opgeven, zoals **\<APPLICATION-CLIENT-ID>**, **\<APPLICATION-REPLY-URI>** en **\<SUBSCRIPTION-ID>**.

Hoewel de onderstaande code zowel de interactieve als niet-interactieve methode bevat, is de niet-interactieve code opgenomen als opmerking. Voor de interactieve methode moet u de client-ID van de AAD-toepassing en de omleidings-URI opgeven. Volg de koppeling die wordt vermeld in de vereisten voor instructies voor het verkrijgen van deze informatie.

>[AZURE.NOTE] Als u het codefragment aanpast om de niet-interactieve methode (`AuthenticateApplication`) te gebruiken, moet u ook de client-verificatiesleutel, client-ID en de antwoord-URI voor de client opgeven als invoer voor de methode. Het artikel [Een Active Directory-toepassing en service-principal maken met Portal](../resource-group-create-service-principal-portal.md) biedt ook informatie over het genereren en ophalen van de client-verificatiesleutel.
    
Als laatste moet u ervoor zorgen dat het lokale pad en de bestandsnaam die u hier opgeeft, al bestaan op de computer. Als u nog geen voorbeeldgegevens hebt om te uploaden, kunt u de map **Ambulance Data** uit de [Azure Data Lake Git-opslagplaats](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData) gebruiken.



    using System;
    using System.IO;
    using System.Security;
    using System.Text;
    using System.Collections.Generic;
    using System.Linq;

    using Microsoft.Azure.Management.DataLake.Store;
    using Microsoft.Azure.Management.DataLake.Store.Models;
    using Microsoft.Azure.Management.DataLake.StoreUploader;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;

    namespace SdkSample
    {
        class Program
        {
            private static DataLakeStoreAccountManagementClient _adlsClient;
            private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;

            private static string _adlsAccountName;
            private static string _resourceGroupName;
            private static string _location;

            private static void Main(string[] args)
            {
                _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name for a NEW Store account.
                _resourceGroupName = "<RESOURCE-GROUP-NAME>"; // TODO: Replace this value. This resource group should already exist.
                _location = "East US 2";

                string localFolderPath = @"C:\local_path\"; // TODO: Make sure this exists and can be overwritten.
                string localFilePath = localFolderPath + "file.txt"; // TODO: Make sure this exists and can be overwritten.
                string remoteFolderPath = "/data_lake_path/";
                string remoteFilePath = remoteFolderPath + "file.txt";

                // Authenticate the user
                var tokenCreds = AuthenticateUser("common", "https://management.core.windows.net/",
                    "<APPLICATION-CLIENT-ID>", new Uri("<APPLICATION-REPLY-URI>")); // TODO: Replace bracketed values.

                SetupClients(tokenCreds, "<SUBSCRIPTION-ID>"); // TODO: Replace bracketed value.

                // Run sample scenarios
                WaitForNewline("Authenticated.", "Creating NEW account.");
                CreateAccount();
                WaitForNewline("Account created.", "Creating a directory.");

                // Create a directory in the Data Lake Store
                CreateDirectory(remoteFolderPath);
                WaitForNewline("Directory created.", "Showing directory info.");

                // Get info about the directory in the Data Lake Store
                var itemInfo = GetItemInfo(remoteFolderPath);
                Console.WriteLine("Type: " + itemInfo.Type);
                Console.WriteLine("Last modified (UTC): " +
                                  new DateTime(1970, 1, 1, 0, 0, 0, 0, DateTimeKind.Utc).AddMilliseconds(
                                      itemInfo.ModificationTime.Value));
                WaitForNewline("Directory info shown.", "Uploading a file.");

                // Upload a file to the Data Lake Store
                UploadFile(localFilePath, remoteFilePath);
                WaitForNewline("File uploaded.", "Listing files and directories.");

                // List the files in the Data Lake Store
                var itemList = ListItems(remoteFolderPath);
                var fileMenuItems = itemList.Select(a => String.Format("{0,15} {1}", a.Type, a.PathSuffix));
                Console.WriteLine(String.Join("\r\n", fileMenuItems));
                WaitForNewline("Files and directories listed.", "Appending content to a file.");

                // Append to a file in the Data Lake Store
                AppendToFile(remoteFilePath, "123");
                WaitForNewline("Content appended.", "Downloading a file.");

                // Download a file from the Data Lake Store
                DownloadFile(remoteFilePath, localFilePath);
                WaitForNewline("File downloaded.", "Deleting account.");

                // Delete account
                DeleteAccount();
                WaitForNewline("Account deleted. You can now exit.");
            }

            // Helper function to show status and wait for user input
            public static void WaitForNewline(string reason, string nextAction = "")
            {
                if (!String.IsNullOrWhiteSpace(nextAction))
                {
                    Console.WriteLine(reason + "\r\nPress ENTER to continue...");
                    Console.ReadLine();
                    Console.WriteLine(nextAction);
                }
                else
                {
                    Console.WriteLine(reason + "\r\nPress ENTER to continue...");
                    Console.ReadLine();
                }
            }

            // Authenticate the user with AAD through an interactive popup.
            // You need to have an application registered with AAD in order to authenticate.
            //   For more information and instructions on how to register your application with AAD, see:
            //   https://azure.microsoft.com/en-us/documentation/articles/resource-group-create-service-principal-portal/
            public static TokenCredentials AuthenticateUser(string tenantId, string resource, string appClientId, Uri appRedirectUri, string userId = "")
            {
                var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenantId);

                var tokenAuthResult = authContext.AcquireToken(resource, appClientId, appRedirectUri,
                    PromptBehavior.Auto, UserIdentifier.AnyUser);

                return new TokenCredentials(tokenAuthResult.AccessToken);
            }
            
            /*
            // Authenticate the application with AAD through the application's secret key.
            // You need to have an application registered with AAD in order to authenticate.
            //   For more information and instructions on how to register your application with AAD, see:
            //   https://azure.microsoft.com/en-us/documentation/articles/resource-group-create-service-principal-portal/
            public static TokenCredentials AuthenticateApplication(string tenantId, string resource, string appClientId, Uri appRedirectUri, SecureString clientSecret)
            {
                var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenantId);
                var credential = new ClientCredential(appClientId, clientSecret);

                var tokenAuthResult = authContext.AcquireToken(resource, credential);

                return new TokenCredentials(tokenAuthResult.AccessToken);
            }
            */

            //Set up clients
            public static void SetupClients(TokenCredentials tokenCreds, string subscriptionId)
            {
                _adlsClient = new DataLakeStoreAccountManagementClient(tokenCreds);
                _adlsClient.SubscriptionId = subscriptionId;

                _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(tokenCreds);
            }

            // Create account
            public static void CreateAccount()
            {
                // Create ADLS account
                var adlsParameters = new DataLakeStoreAccount(location: _location);
                _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);
            }

            // Delete account
            public static void DeleteAccount()
            {
                _adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);
            }

            // List all ADLS accounts within the subscription
            public static List<DataLakeStoreAccount> ListAdlStoreAccounts()
            {
                var response = _adlsClient.Account.List(_adlsAccountName);
                var accounts = new List<DataLakeStoreAccount>(response);

                while (response.NextPageLink != null)
                {
                    response = _adlsClient.Account.ListNext(response.NextPageLink);
                    accounts.AddRange(response);
                }

                return accounts;
            }

            // Upload a file
            public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
            {
                var parameters = new UploadParameters(srcFilePath, destFilePath, _adlsAccountName, isOverwrite: force);
                var frontend = new DataLakeStoreFrontEndAdapter(_adlsAccountName, _adlsFileSystemClient);
                var uploader = new DataLakeStoreUploader(parameters, frontend);
                uploader.Execute();
            }

            // Concatenate files
            public static void ConcatenateFiles(string[] srcFilePaths, string destFilePath)
            {
                _adlsFileSystemClient.FileSystem.Concat(_adlsAccountName, destFilePath, srcFilePaths);
            }

            // Get file or directory info
            public static FileStatusProperties GetItemInfo(string path)
            {
                return _adlsFileSystemClient.FileSystem.GetFileStatus(_adlsAccountName, path).FileStatus;
            }

            // List files and directories
            public static List<FileStatusProperties> ListItems(string directoryPath)
            {
                return _adlsFileSystemClient.FileSystem.ListFileStatus(_adlsAccountName, directoryPath).FileStatuses.FileStatus.ToList();
            }

            // Download file
            public static void DownloadFile(string srcPath, string destPath)
            {
                var stream = _adlsFileSystemClient.FileSystem.Open(_adlsAccountName, srcPath);
                var fileStream = new FileStream(destPath, FileMode.Create);

                stream.CopyTo(fileStream);
                fileStream.Close();
                stream.Close();
            }

            // Append to file
            public static void AppendToFile(string path, string content)
            {
                var stream = new MemoryStream(Encoding.UTF8.GetBytes(content));

                _adlsFileSystemClient.FileSystem.Append(_adlsAccountName, path, stream);
            }

            // Create a directory
            public static void CreateDirectory(string path)
            {
                _adlsFileSystemClient.FileSystem.Mkdirs(_adlsAccountName, path);
            }
        }
    }


## Volgende stappen

- [Gegevens in Data Lake Store beveiligen](data-lake-store-secure-data.md)
- [Azure Data Lake Analytics gebruiken met Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Azure HDInsight gebruiken met Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)



<!--HONumber=Jun16_HO2-->


