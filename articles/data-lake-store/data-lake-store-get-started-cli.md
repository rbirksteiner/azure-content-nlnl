<properties
   pageTitle="Aan de slag met Azure Data Lake Store met de platformoverschrijdende opdrachtregelinterface | Microsoft Azure"
   description="De platformoverschrijdende opdrachtregel van Azure gebruiken voor het maken van een Data Lake Store-account en uitvoeren van basisbewerkingen"
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
   ms.date="04/07/2016"
   ms.author="nitinme"/>

# Aan de slag met Azure Data Lake Store met de Azure-opdrachtregelinterface

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [REST API](data-lake-store-get-started-rest-api.md)
- [Azure CLI](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

Informatie over gebruik van de Azure-opdrachtregelinterface voor het maken van een Azure Data Lake Store-account en het uitvoeren van basisbewerkingen, zoals het maken van mappen, uploaden en downloaden van gegevensbestanden, verwijderen van uw account, enzovoort. Zie [Overzicht van Data Lake Store](data-lake-store-overview.md) voor meer informatie over Data Lake Store.

De Azure CLI is geïmplementeerd in Node.js en kan worden gebruikt op elk platform dat ondersteuning biedt voor Node.js, zoals Windows, Mac en Linux. De Azure CLI is open-source. De broncode wordt beheerd in GitHub op <a href= "https://github.com/azure/azure-xplat-cli">https://github.com/azure/azure-xplat-cli</a>. Dit artikel behandelt alleen het gebruik van de Azure CLI met Data Lake Store. Raadpleeg voor algemene richtlijnen voor het gebruik van Azure CLI [De Azure CLI gebruiken] [azure-command-line-tools].


##Vereisten

Voordat u dit artikel gaat lezen, moet u beschikken over het volgende:

- **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
- **Uw Azure-abonnement inschakelen** voor de openbare preview van Data Lake Store. Zie [Instructies](data-lake-store-get-started-portal.md#signup).
- **Azure CLI**: zie [De Azure CLI installeren en configureren](../xplat-cli-install.md) voor informatie over de installatie en configuratie. Start de computer opnieuw op nadat u de CLI hebt geïnstalleerd.

##Meld u aan bij uw Azure-abonnement

Volg de stappen die zijn beschreven in [Verbinding maken met een Azure-abonnement met de Azure-opdrachtregelinterface (Azure CLI)](../xplat-cli-connect.md) en maak verbinding met uw abonnement met behulp van de methode __login__.


## Een Azure Data Lake Store-account maken

Open een opdrachtprompt, shell of terminalvenster en voer de volgende opdrachten uit.

1. Meld u aan bij uw Azure-abonnement:

        azure login

    U wordt gevraagd om een webpagina te openen en een verificatiecode in te voeren. Volg de instructies op de pagina om u aan te melden bij uw Azure-abonnement.

2. Schakel over naar modus Azure Resource Manager met de volgende opdracht:

        azure config mode arm


3. Geef de Azure-abonnementen voor uw account weer.

        azure account list


4. Als u meerdere Azure-abonnementen hebt, gebruikt u de volgende opdracht om in te stellen welk abonnement door de Azure CLI-opdrachten wordt gebruikt:

        azure account set <subscriptionname>

5. Maak een nieuwe resourcegroep. Geef in de volgende opdracht de parameterwaarden op die u wilt gebruiken.

        azure group create <resourceGroup> <location>

    Als de locatienaam spaties bevat, moet deze tussen dubbele aanhalingstekens worden geplaatst. Bijvoorbeeld “VS-oost 2”.

5. Maak de Data Lake Store-account.

        azure datalake store account create <dataLakeStoreAccountName> <location> <resourceGroup>

## Mappen maken in uw Data Lake Store

U kunt mappen maken onder uw Azure Data Lake Store-account voor het beheren en opslaan van gegevens. Gebruik de volgende opdracht om een map te maken met de naam ‘mynewfolder’ in de hoofdmap van de Data Lake Store.

    azure datalake store filesystem create <dataLakeStoreAccountName> <path> --folder

Bijvoorbeeld:

    azure datalake store filesystem create mynewdatalakestore /mynewfolder --folder

## Gegevens uploaden naar uw Data Lake Store

Als u gegevens uploadt naar Data Lake Store, kunt u dat direct naar het hoogste niveau doen of naar een map die u in het account hebt gemaakt. De codefragmenten hieronder laten zien hoe u voorbeeldgegevens uploadt naar de map (**mynewfolder**) die u in de voorgaande sectie hebt gemaakt.

Als u nog geen voorbeeldgegevens hebt om te uploaden, kunt u de map **Ambulance Data** uit de [Azure Data Lake Git-opslagplaats](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData) gebruiken. Download het bestand en sla het in een lokale map op uw computer op, bijvoorbeeld C:\sampledata\.

    azure datalake store filesystem import <dataLakeStoreAccountName> "<source path>" "<destination path>"

Bijvoorbeeld:

    azure datalake store filesystem import mynewdatalakestore "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" "/mynewfolder/vehicle1_09142014.csv"


## Bestanden in Data Lake Store weergeven

Gebruik de volgende opdracht om de bestanden in een Data Lake Store-account weer te geven.

    azure datalake store filesystem list <dataLakeStoreAccountName> <path>

Bijvoorbeeld:

    azure datalake store filesystem list mynewdatalakestore /mynewfolder

De uitvoer ziet er ongeveer als volgt uit:

    info:    Executing command datalake store filesystem list
    data:    accessTime: 1446245025257
    data:    blockSize: 268435456
    data:    group: NotSupportYet
    data:    length: 1589881
    data:    modificationTime: 1446245105763
    data:    owner: NotSupportYet
    data:    pathSuffix: vehicle1_09142014.csv
    data:    permission: 777
    data:    replication: 0
    data:    type: FILE
    data:    ------------------------------------------------------------------------------------
    info:    datalake store filesystem list command OK

## Gegevens in uw Data Lake Store een nieuwe naam geven, downloaden en verwijderen

* **Als u de naam van een bestand wilt wijzigen**, gebruikt u de volgende opdracht:

        azure datalake store filesystem move <dataLakeStoreAccountName> <path/old_file_name> <path/new_file_name>

    Bijvoorbeeld:

        azure datalake store filesystem move mynewdatalakestore /mynewfolder/vehicle1_09142014.csv /mynewfolder/vehicle1_09142014_copy.csv

* **Als u een bestand wilt downloaden**, gebruikt u de volgende opdracht: Zorg ervoor dat het doelpad dat u opgeeft al bestaat.

        azure datalake store filesystem export <dataLakeStoreAccountName> <source_path> <destination_path>

    Bijvoorbeeld:

        azure datalake store filesystem export mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv "C:\mysampledata\vehicle1_09142014_copy.csv"

* **Als u een bestand wilt verwijderen**, gebruikt u de volgende opdracht:

        azure datalake store filesystem delete <dataLakeStoreAccountName> <path>

    Bijvoorbeeld:

        azure datalake store filesystem delete mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv

    Wanneer dit wordt gevraagd, typt u **Y** om het item te verwijderen.

## De toegangsbeheerlijst voor een map in Data Lake Store weergeven

Gebruik de volgende opdracht om de ACL's van een Data Lake Store-map weer te geven. In de huidige release kunnen ACL's alleen worden ingesteld voor de hoofdmap van de Data Lake Store. De onderstaande padparameter wordt dus altijd hoofdmap (/).

    azure datalake store permissions show <dataLakeStoreName> <path>

Bijvoorbeeld:

    azure datalake store permissions show mynewdatalakestore /


## Uw Data Lake Store-account verwijderen

Gebruik de volgende opdracht om een Data Lake Store-account te verwijderen.

    azure datalake store account delete <dataLakeStoreAccountName>

Bijvoorbeeld:

    azure datalake store account delete mynewdatalakestore

Wanneer dit wordt gevraagd, typt u **Y** om het account te verwijderen.


## Volgende stappen

- [Gegevens in Data Lake Store beveiligen](data-lake-store-secure-data.md)
- [Azure Data Lake Analytics gebruiken met Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Azure HDInsight gebruiken met Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)


[azure-command-line-tools]: ../xplat-cli-install.md



<!--HONumber=Jun16_HO2-->


