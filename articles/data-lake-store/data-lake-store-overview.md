<properties
   pageTitle="Overzicht van Azure Data Lake Store | Azure"
   description="Weten wat Azure Data Lake Store is en wat de meerwaarde is ten opzicht van andere gegevensarchieven"
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
   ms.date="05/11/2016"
   ms.author="nitinme"/>

# Overzicht van Azure Data Lake Store

Azure Data Lake Store is een ondernemingsbrede opslagplaats op hyperschaal voor analytische workloads van big data. Met Azure Data Lake kunt u gegevens van elke grootte, type en opnamesnelheid vastleggen op één enkele locatie voor operationele en experimentele analyses.

> [AZURE.TIP] Gebruik het [leertraject van Data Lake Store](https://azure.microsoft.com/documentation/learning-paths/data-lake-store-self-guided-training/) om de service Azure Data Lake Store te verkennen.

Azure Data Lake Store kan worden geopend via Hadoop (beschikbaar met HDInsight-cluster) met behulp van de met WebHDFS compatibele REST-API's. Het is speciaal ontworpen om de opgeslagen gegevens te kunnen analyseren en het is afgestemd op prestaties voor scenario's met gegevensanalyses. Het bevat out-of-the-box alle mogelijkheden op bedrijfsniveau: beveiliging, beheerbaarheid, schaalbaarheid, betrouwbaarheid en beschikbaarheid. Dit zijn allemaal essentiële factoren voor zakelijke gebruiksvoorbeelden.


![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

De belangrijkste mogelijkheden van Azure Data Lake zijn onder andere de volgende.

### Gebouwd voor Hadoop

Het Azure Data Lake-archief is een Apache Hadoop-bestandssysteem dat compatibel is met Hadoop Distributed File System (HDFS) en samenwerkt met het Hadoop-ecosysteem.  Uw bestaande toepassingen of services van HDInsight die gebruikmaken van de API WebHDFS kunnen eenvoudig worden geïntegreerd met Data Lake Store. Data Lake Store bevat ook een met WebHDFS compatibele REST-interface voor toepassingen

Gegevens die zijn opgeslagen in Data Lake Store kunnen eenvoudig worden geanalyseerd met analytische frameworks van Hadoop zoals MapReduce of Hive. Microsoft Azure HDInsight-clusters kunnen worden ingericht en geconfigureerd voor directe toegang tot gegevens die zijn opgeslagen in Data Lake Store.

### Onbeperkte opslag, bestanden ter grootte van petabytes

Azure Data Lake Store biedt onbeperkte opslag en is geschikt voor het opslaan van een verscheidenheid aan gegevens voor analyses. Het legt geen limieten op voor de grootte van accounts of bestanden, of de hoeveelheid gegevens die kunnen worden opgeslagen in een Data Lake. De grootte van afzonderlijke bestanden kan uiteenlopen van een kilobyte tot petabytes, waardoor het een uitstekende keuze is voor het opslaan van elk type gegevens. Gegevens worden blijvend opgeslagen door meerdere kopieën te maken en er is geen limiet voor de tijdsduur waarin de gegevens kunnen worden opgeslagen in de Data Lake.

### Prestaties zijn afgestemd op big data-analyses

Azure Data Lake Store is gebouwd voor het uitvoeren van grootschalige analytische systemen waarvoor grote doorvoer vereist is om grote hoeveelheden gegevens op te vragen en te analyseren. De Data Lake verspreidt delen van een bestand over een aantal afzonderlijke opslagservers. Hiermee verbetert u de doorvoer wanneer het bestand in parallel wordt gelezen voor het uitvoeren van gegevensanalyse.


### Enterprise-ready: hoge beschikbaarheid en veiligheid

Azure Data Lake Store biedt industriestandaard beschikbaarheid en betrouwbaarheid. Uw gegevensassets worden blijvend opgeslagen door het maken van redundante exemplaren ter bescherming tegen onverwachte fouten. Ondernemingen kunnen Azure Data Lake bij hun oplossingen gebruiken als een belangrijk onderdeel van hun bestaande gegevensplatform.

Data Lake Store biedt ook beveiliging op bedrijfsniveau voor de opgeslagen gegevens. Zie voor meer informatie [Gegevens beveiligen in Azure Data Lake Store](#DataLakeStoreSecurity).


### Alle gegevens

In Azure Data Lake Store kunnen alle gegevens worden opgeslagen in de oorspronkelijke indeling, zoals ze gemaakt zijn, en het is niet nodig om de gegevens eerst om te zetten. In Data Lake Store hoeft geen schema te worden gedefinieerd voordat de gegevens worden geladen. Daardoor kan het afzonderlijke analytische framework de gegevens interpreteren en een schema definiëren op het moment van de analyse. Doordat bestanden van verschillende groottes en indelingen kunnen worden opgeslagen, kan Data Lake Store gestructureerde, semi-gestructureerde en ongestructureerde gegevens verwerken.

Containers voor gegevens van Azure Data Lake Store zijn eigenlijk mappen en bestanden. U werkt met de opgeslagen gegevens met behulp van SDK's, Azure Portal en Azure Powershell. Als u uw gegevens in het archief plaats via deze interfaces en de juiste containers gebruikt, kunt u allerlei soorten gegevens opslaan. Data Lake Store voert geen speciale verwerking van gegevens uit op basis van het type gegevens dat wordt opgeslagen.


## <a name="DataLakeStoreSecurity"></a>Gegevens beveiligen in Azure Data Lake Store

Azure Data Lake Store maakt gebruik van Azure Active Directory voor verificatie en toegangsbeheerlijsten (ACL’s) om toegang tot uw gegevens te beheren.

| Functie                                 | Beschrijving                              |
|-----------------------------------------|------------------------------------------|
| Verificatie | Azure Data Lake Store integreert met Azure Active Directory (AAD) voor identiteits- en toegangsbeheer voor alle gegevens die zijn opgeslagen in Azure Data Lake Store. Als gevolg van de integratie profiteert Azure Data Lake van alle AAD-functies, zoals Multi-Factor Authentication, voorwaardelijke toegang, op rollen gebaseerd toegangsbeheer, bewaking van het gebruik van toepassingen, beveiligingsbewaking en waarschuwingen, enzovoort. Azure Data Lake Store ondersteunt het OAuth 2.0-protocol voor verificatie in de REST-interface. |
| Toegangsbeheer                          | Azure Data Lake Store biedt toegangsbeheer door ondersteuning te bieden voor POSIX-machtigingen die beschikbaar worden gemaakt door het protocol WebHDFS. In de huidige release kunnen machtigingen worden opgegeven op het niveau van Data Lakes en gelden de machtigingen voor alle bestanden en mappen in de Data Lake. In toekomstige updates wordt verfijnd toegangsbeheer mogelijk door het instellen van machtigingen voor afzonderlijke bestanden en mappen.|

Wilt u meer informatie over het beveiligen van gegevens in Data Lake Store? Volg dan de onderstaande links.

* Zie voor instructies over het beveiligen van gegevens in Data Lake Store [Gegevens beveiligen in Azure Data Lake Store](data-lake-store-secure-data.md).
* Kijkt u liever naar video’s? [Bekijk deze video](https://mix.office.com/watch/1q2mgzh9nn5lx) over het beveiligen van gegevens die zijn opgeslagen in Data Lake Store.

## Toepassingen die compatibel zijn met Azure Data Lake Store

Azure Data Lake Store is compatibel met de meeste open source-onderdelen van het Hadoop-ecosysteem. Het kan ook goed worden geïntegreerd in andere Azure-services. Hierdoor is Data Lake Store een perfecte optie voor uw opslagbehoeften. Volg de onderstaande links voor meer informatie over hoe Data Lake Store kan worden gebruikt met open source-onderdelen en met andere Azure-services.

* Zie [Toepassingen en services die compatibel zijn met Azure Data Lake Store](data-lake-store-compatible-oss-other-applications.md) voor een lijst met open-sourcetoepassingen die compatibel zijn met Data Lake Store.
* Zie [Integreren met andere Azure-services](data-lake-store-integrate-with-other-services.md) om te begrijpen hoe Data Lake Store samen met andere Azure-services kan worden gebruikt in een breed scala aan scenario's.
* Zie [Scenario's voor het gebruik van Data Lake Store](data-lake-store-data-scenarios.md) voor informatie over het gebruik van Data Lake Store in scenario's zoals het opnemen, verwerken, downloaden en visualiseren van gegevens.

## Wat is het bestandssysteem van Azure Data Lake Store (adl://)?

Data Lake Store is toegankelijk via het nieuwe bestandssysteem, het AzureDataLakeFilesystem (adl://), in Hadoop-omgevingen (beschikbaar met HDInsight-cluster). Toepassingen en services die gebruikmaken van adl:// kunnen profiteren van verdere optimalisatie van prestaties die op dit moment niet beschikbaar is in WebHDFS. Als gevolg hiervan geeft Data Lake Store u de flexibiliteit om de beste prestaties te behalen met de aanbevolen optie adl:// of bestaande code te beheren door de API van WebHDFS rechtstreeks te blijven gebruiken. Azure HDInsight maakt volledig gebruik van AzureDataLakeFilesystem om de beste prestaties te leveren op Data Lake Store.

U kunt toegang krijgen tot uw gegevens in Data Lake Store met behulp van `adl://<data_lake_store_name>.azuredatalakestore.net`. Zie voor meer informatie over toegang krijgen tot de gegevens in Data Lake Store [Eigenschappen van de opgeslagen gegevens weergeven](data-lake-store-get-started-portal.md#properties)

## Hoe begin ik met het gebruiken van Azure Data Lake Store?

Zie [Aan de slag met Data Lake Store via de Azure Portal](data-lake-store-get-started-portal.md) voor informatie over het inrichten van een Data Lake Store via de Azure Portal. Als u Azure Data Lake hebt ingericht, kunt u leren hoe u big data-oplossingen zoals Azure Data Lake Analytics of Azure HDInsight kunt gebruiken met Data Lake Store. Ook kunt u een .NET-toepassing maken om een Azure Data Lake Store-account te maken en bewerkingen uit te voeren, zoals het uploaden en downloaden van gegevens, enzovoort.

- [Aan de slag met Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Azure HDInsight gebruiken met Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Aan de slag met Azure Data Lake Store met .NET SDK](data-lake-store-get-started-net-sdk.md)


## Video’s over Data Lake Store

Als u liever video's bekijkt voor meer informatie, biedt Data Lake Store video's voor een breed scala aan functies.

* [Een Azure Data Lake Store-account maken](https://mix.office.com/watch/1k1cycy4l4gen)
* [De Data Explorer gebruiken om gegevens te beheren in Azure Data Lake Store](https://mix.office.com/watch/icletrxrh6pc)
* [Azure Data Lake Analytics verbinden met Azure Data Lake Store](https://mix.office.com/watch/qwji0dc9rx9k)
* [Toegang tot Azure Data Lake Store via Data Lake Analytics](https://mix.office.com/watch/1n0s45up381a8)
* [Azure HDInsight verbinden met Azure Data Lake Store](https://mix.office.com/watch/l93xri2yhtp2)
* [Toegang tot Azure Data Lake Store via Hive en Pig](https://mix.office.com/watch/1n9g5w0fiqv1q)
* [DistCp (Hadoop Distributed Copy) gebruiken om gegevens van en naar Azure Data Lake Store te kopiëren](https://mix.office.com/watch/1liuojvdx6sie)
* [Apache Sqoop gebruiken om gegevens te verplaatsen tussen relationele bronnen en Azure Data Lake Store](https://mix.office.com/watch/1butcdjxmu114)
* [Organisatie van gegevens met behulp van Azure Data Factory voor Azure Data Lake Store](https://mix.office.com/watch/1oa7le7t2u4ka)
* [Gegevens beveiligen in Azure Data Lake Store](https://mix.office.com/watch/1q2mgzh9nn5lx)






<!--HONumber=Jun16_HO2-->


