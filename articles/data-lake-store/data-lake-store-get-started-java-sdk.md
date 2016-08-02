<properties
   pageTitle="Data Lake Store Java SDK gebruiken om toepassingen te ontwikkelen | Azure"
   description="Data Lake Store Java SDK gebruiken om toepassingen te ontwikkelen"
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
   ms.date="05/12/2016"
   ms.author="nitinme"/>

# Aan de slag met Azure Data Lake Store met Java

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [REST API](data-lake-store-get-started-rest-api.md)
- [Azure CLI](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

Lees hoe u met de Azure Data Lake Store Java SDK een Azure Data Lake-account maakt en basisbewerkingen uitvoert. U kunt onder andere mappen maken, gegevensbestanden uploaden en downloaden en uw account verwijderen. Zie [Azure Data Lake Store](data-lake-store-overview.md) voor meer informatie over Data Lake.

## Azure Data Lake Store Java SDK

Volg deze koppelingen voor de downloadlocatie voor de Java SDK voor Data Lake Store en naslaginformatie over de Java SDK. Voor deze zelfstudie hoeft u de SDK niet te downloaden en het referentiedocument niet te volgen. Deze koppelingen zijn uitsluitend ter informatie.

* De broncode voor de Java SDK voor Data Lake Store is beschikbaar op [GitHub](https://github.com/Azure/azure-sdk-for-java).
* Naslaginformatie over Java SDK voor Data Lake Store is beschikbaar op [https://azure.github.io/azure-sdk-for-java/](https://azure.github.io/azure-sdk-for-java/).

## Vereisten

* Java Development Kit (JDK) 8 (met Java-versie 1.8).
* IntelliJ of een andere geschikte Java-ontwikkelomgeving. Dit is optioneel, maar wordt wel aanbevolen. In onderstaande instructies wordt IntelliJ gebruikt.
* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
* **Uw Azure-abonnement inschakelen** voor de openbare preview van Data Lake Store. Zie [Instructies](data-lake-store-get-started-portal.md#signup).
* **Een Azure Active Directory-toepassing maken**. Er zijn twee manieren waarop u kunt verifiëren met behulp van Azure Active Directory: **interactief** en **niet-interactief**. De vereisten zijn afhankelijk van de manier waarop u wilt verifiëren.
    * **Voor interactieve verificatie** moet u in Azure Active Directory een **Native Client-toepassing** maken. Wanneer u de toepassing hebt gemaakt, haalt u de volgende waarden op met betrekking tot de toepassing.
        - **Client-id** en **omleidings-URI** voor de toepassing ophalen
        - Gedelegeerde machtigingen instellen

    * **Voor niet-interactieve verificatie** (dat in dit artikel wordt gebruikt) moet u in Azure Active Directory een **webtoepassing** maken. Wanneer u de toepassing hebt gemaakt, haalt u de volgende waarden op met betrekking tot de toepassing.
        - **Client-id**, **clientgeheim** en **omleidings-URI** voor de toepassing ophalen
        - Gedelegeerde machtigingen instellen
        - Wijs de Azure Active Directory-toepassing toe aan een rol. De rol kan zich bevinden op het niveau van het bereik waarmee u toegang wilt geven aan de Azure Active Directory-toepassing. U kunt de toepassing bijvoorbeeld toewijzen op abonnementsniveau of op het niveau van een resourcegroep. 

    Zie [Een Active Directory-toepassing en service-principal maken met portal](../resource-group-create-service-principal-portal.md) als u wilt weten hoe u deze waarden ophaalt, de machtigingen instelt en rollen toewijst.

## Hoe verifieer ik met Azure Active Directory?

In onderstaand codefragment vindt u code voor **niet-interactieve** verificatie, waarbij de toepassing zijn eigen referenties verstrekt.

Deze zelfstudie werkt alleen wanneer u uw toepassing machtigt om resources te maken in Azure. Het wordt **ten zeerste aangeraden** dat u deze toepassing voor het doel van deze zelfstudie alleen Inzender-rechten geeft voor een nieuwe, ongebruikte en lege resourcegroep in uw Azure-abonnement.

## Een Java-toepassing maken

1. Open IntelliJ en maak een nieuw Java-project met de **Command Line Application**-sjabloon. Voltooi de wizard om het project te maken.

2. Klik met de rechtermuisknop op het project aan de linkerkant van het scherm en klik op **Add Framework Support** (Framework-ondersteuning toevoegen). Kies **Maven** en klik op **OK**.

3. Open het zojuist gemaakte bestand **pom.xml** en voeg het volgende tekstfragment toe tussen de tag **\</version>** en de tag **\</project>**:

    >[AZURE.NOTE] Deze stap is tijdelijk totdat de Azure Data Lake Store SDK beschikbaar is in Maven. Dit artikel wordt bijgewerkt wanneer de SDK beschikbaar in Maven is. Alle toekomstige updates voor deze SDK komen beschikbaar via Maven.

        <repositories>
            <repository>
                <id>adx-snapshots</id>
                <name>Azure ADX Snapshots</name>
                <url>http://adxsnapshots.azurewebsites.net/</url>
                <layout>default</layout>
                <snapshots>
                    <enabled>true</enabled>
                </snapshots>
            </repository>
            <repository>
                <id>oss-snapshots</id>
                <name>Open Source Snapshots</name>
                <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
                <layout>default</layout>
                <snapshots>
                    <enabled>true</enabled>
                    <updatePolicy>always</updatePolicy>
                </snapshots>
            </repository>
        </repositories>
        <dependencies>
            <dependency>
                <groupId>com.microsoft.azure</groupId>
                <artifactId>azure-client-authentication</artifactId>
                <version>1.0.0-SNAPSHOT</version>
            </dependency>
            <dependency>
                <groupId>com.microsoft.azure</groupId>
                <artifactId>azure-mgmt-datalake-store</artifactId>
                <version>1.0.0-SNAPSHOT</version>
            </dependency>
        </dependencies>


4. Ga naar **File**, **Settings** en **Build, Execution, and Deployment** (Bestand > Instellingen > Samenstellen, uitvoeren en implementeren). Vouw **Build Tools**, **Maven** en vervolgens **Importing** uit (Opbouwhulpmiddelen, Maven, Importeren). Schakel het selectievakje **Import Maven projects automatically** (Maven-projecten automatisch importeren) in. Klik op **Apply** (Toepassen) en op **OK**.

5. Ga vanuit het linkerdeelvenster naar **src**, **main**, **java**, **\<pakketnaam>** en open **Main.java**. Vervang het bestaande codeblok door de volgende code. Geef ook de waarden op voor parameters die in het codefragment worden genoemd, zoals **localFolderPath**, **_adlsAccountName**, **_resourceGroupName**, en vervang de tijdelijke aanduidingen voor **CLIENT-ID**, **CLIENT-SECRET**, **TENANT-ID** en **SUBSCRIPTION-ID**.

    Deze code doorloopt het proces waarin een Data Lake Store-account wordt gemaakt, bestanden in het archief worden gemaakt, bestanden worden samengevoegd, een bestand wordt gedownload en het account tot slot wordt verwijderd.

        package com.company;
        
        import com.microsoft.azure.CloudException;
        import com.microsoft.azure.credentials.ApplicationTokenCredentials;
        import com.microsoft.azure.management.datalake.store.*;
        import com.microsoft.azure.management.datalake.store.models.*;
        import com.microsoft.rest.credentials.ServiceClientCredentials;
        import java.io.*;
        import java.nio.charset.Charset;
        import java.util.ArrayList;
        import java.util.List;
        
        public class Main {
            private static String _adlsAccountName;
            private static String _resourceGroupName;
            private static String _location;
        
            private static String _tenantId;
            private static String _subId;
            private static String _clientId;
            private static String _clientSecret;
        
            private static DataLakeStoreAccountManagementClient _adlsClient;
            private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
        
            public static void main(String[] args) throws Exception {
                _adlsAccountName = "<DATA-LAKE-STORE-NAME>";
                _resourceGroupName = "<RESOURCE-GROUP-NAME>";
                _location = "East US 2";
        
                _tenantId = "<TENANT-ID>";
                _subId =  "<SUBSCRIPTION-ID>";
                _clientId = "<CLIENT-ID>";
        
                _clientSecret = "<CLIENT-SECRET>"; // TODO: For production scenarios, we recommend that you replace this line with a more secure way of acquiring the application client secret, rather than hard-coding it in the source code.
        
                String localFolderPath = "C:\\local_path\\"; // TODO: Change this to any unused, new, empty folder on your local machine.
        
                // Authenticate
                ApplicationTokenCredentials creds = new ApplicationTokenCredentials(_clientId, _tenantId, _clientSecret, null);
                SetupClients(creds);
        
                // Create Data Lake Store account
                WaitForNewline("Authenticated.", "Creating NEW account.");
                CreateAccount();
                WaitForNewline("Account created.", "Displaying account(s).");
        
                // List Data Lake Store accounts that this app can access
                System.out.println(String.format("All ADL Store accounts that this app can access in subscription %s:", _subId));
                List<DataLakeStoreAccount> adlsListResult = _adlsClient.getAccountOperations().list().getBody();
                for (DataLakeStoreAccount acct : adlsListResult) {
                    System.out.println(acct.getName());
                }
                WaitForNewline("Account(s) displayed.", "Creating files.");
        
                // Create two files in Data Lake Store: file1.csv and file2.csv
                CreateFile("/file1.csv", "123,abc", true);
                CreateFile("/file2.csv", "456,def", true);
                WaitForNewline("Files created.", "Concatenating files.");
        
                // Concatenate two files in Data Lake Store
                List<String> srcFilePaths = new ArrayList<String>();
                srcFilePaths.add("/file1.csv");
                srcFilePaths.add("/file2.csv");
                ConcatenateFiles(srcFilePaths, "/input.csv");
                WaitForNewline("Files concatenated.", "Downloading file.");
        
                // Download file from Data Lake Store
                DownloadFile("/input.csv", localFolderPath + "input.csv");
                WaitForNewline("File downloaded.", "Deleting file.");
        
                // Delete file from Data Lake Store
                DeleteFile("/input.csv");
                WaitForNewline("File deleted.", "Deleting account.");
        
                // Delete account
                DeleteAccount();
                WaitForNewline("Account deleted.", "DONE.");
            }
        
            //Set up clients
            public static void SetupClients(ServiceClientCredentials creds)
            {
                _adlsClient = new DataLakeStoreAccountManagementClientImpl(creds);
                _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClientImpl(creds);
        
                _adlsClient.setSubscriptionId(_subId);
            }
        
            // Helper function to show status and wait for user input
            public static void WaitForNewline(String reason, String nextAction)
            {
                if (nextAction == null)
                    nextAction = "";
                if (!nextAction.isEmpty())
                {
                    System.out.println(reason + "\r\nPress ENTER to continue...");
                    try{System.in.read();}
                    catch(Exception e){}
                    System.out.println(nextAction);
                }
                else
                {
                    System.out.println(reason + "\r\nPress ENTER to continue...");
                    try{System.in.read();}
                    catch(Exception e){}
                }
            }
        
            // Create Data Lake Store account
            public static void CreateAccount() throws InterruptedException, CloudException, IOException {
                DataLakeStoreAccount adlsParameters = new DataLakeStoreAccount();
                adlsParameters.setLocation(_location);
        
                _adlsClient.getAccountOperations().create(_resourceGroupName, _adlsAccountName, adlsParameters);
            }
        
            // Create file
            public static void CreateFile(String path) throws IOException, CloudException {
                _adlsFileSystemClient.getFileSystemOperations().create(_adlsAccountName, path);
            }
        
            // Create file with contents
            public static void CreateFile(String path, String contents, boolean force) throws IOException, CloudException {
                byte[] bytesContents = contents.getBytes();
        
                _adlsFileSystemClient.getFileSystemOperations().create(_adlsAccountName, path, bytesContents, force);
            }
        
            // Append to file
            public static void AppendToFile(String path, String contents) throws IOException, CloudException {
                byte[] bytesContents = contents.getBytes();
        
                _adlsFileSystemClient.getFileSystemOperations().append(_adlsAccountName, path, bytesContents);
            }
        
            // Concatenate files
            public static void ConcatenateFiles(List<String> srcFilePaths, String destFilePath) throws IOException, CloudException {
                _adlsFileSystemClient.getFileSystemOperations().concat(_adlsAccountName, destFilePath, srcFilePaths);
            }
        
            // Delete concatenated file
            public static void DeleteFile(String filePath) throws IOException, CloudException {
                _adlsFileSystemClient.getFileSystemOperations().delete(_adlsAccountName, filePath);
            }
        
            // Get file or directory info
            public static FileStatusProperties GetItemInfo(String path) throws IOException, CloudException {
                return _adlsFileSystemClient.getFileSystemOperations().getFileStatus(_adlsAccountName, path).getBody().getFileStatus();
            }
        
            // List files and directories
            public static List<FileStatusProperties> ListItems(String directoryPath) throws IOException, CloudException {
                return _adlsFileSystemClient.getFileSystemOperations().listFileStatus(_adlsAccountName, directoryPath).getBody().getFileStatuses().getFileStatus();
            }
        
            // Download file
            public static void DownloadFile(String srcPath, String destPath) throws IOException, CloudException {
                InputStream stream = _adlsFileSystemClient.getFileSystemOperations().open(_adlsAccountName, srcPath).getBody();
        
                PrintWriter pWriter = new PrintWriter(destPath, Charset.defaultCharset().name());
        
                String fileContents = "";
                if (stream != null) {
                    Writer writer = new StringWriter();
        
                    char[] buffer = new char[1024];
                    try {
                        Reader reader = new BufferedReader(
                                new InputStreamReader(stream, "UTF-8"));
                        int n;
                        while ((n = reader.read(buffer)) != -1) {
                            writer.write(buffer, 0, n);
                        }
                    } finally {
                        stream.close();
                    }
                    fileContents =  writer.toString();
                }
        
                pWriter.println(fileContents);
                pWriter.close();
            }
        
            // Delete account
            public static void DeleteAccount() throws InterruptedException, CloudException, IOException {
                _adlsClient.getAccountOperations().delete(_resourceGroupName, _adlsAccountName);
            }
        }


6. Voer de toepassing uit. Volg de aanwijzingen om de toepassing uit te voeren en te voltooien.

## Volgende stappen

- [Gegevens in Data Lake Store beveiligen](data-lake-store-secure-data.md)
- [Azure Data Lake Analytics gebruiken met Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Azure HDInsight gebruiken met Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)



<!--HONumber=Jun16_HO2-->


