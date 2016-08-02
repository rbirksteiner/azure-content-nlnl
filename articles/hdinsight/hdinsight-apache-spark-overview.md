<properties 
    pageTitle="Een overzicht van Apache Spark in HDInsight | Microsoft Azure" 
    description="Een inleiding tot Spark in HDInsight en scenario's waarin u Apache Spark in HDInsight kunt gebruiken in uw toepassingen." 
    services="hdinsight" 
    documentationCenter="" 
    authors="nitinme" 
    manager="paulettm" 
    editor="cgronlun"
    tags="azure-portal"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="06/06/2016" 
    ms.author="nitinme"/>

# Overzicht: Apache Spark in Azure HDInsight Linux
 
<a href="http://spark.apache.org/" target="_blank">Apache Spark</a> is een open-source framework voor parallelle verwerking dat ondersteuning biedt voor in-memory verwerking om de prestaties van toepassingen voor de analyse van big data te verbeteren. De Spark-verwerkingsengine is gebouwd voor snelheid, gebruiksgemak en geavanceerde analyses. De in-memory rekencapaciteiten van Spark maken dit tot een goede keuze voor zich herhalende algoritmen in machine-learning- en grafiekberekeningen. Spark is ook compatibel met Azure Blob Storage (WASB), zodat bestaande gegevens die zijn opgeslagen in Azure, gemakkelijk kunnen worden verwerkt via Spark.

Wanneer u in HDInsight een Spark-cluster maakt, maakt u rekenresources in Azure, waarbij Spark is geïnstalleerd en geconfigureerd. Het maken van een Spark-cluster in HDInsight duurt niet meer dan ongeveer tien minuten. De gegevens die moeten worden verwerkt, worden opgeslagen in Azure Blob Storage. Zie [Azure Blob Storage gebruiken met HDInsight][hdinsight-opslag].

![Apache Spark in Azure HDInsight](./media/hdinsight-apache-spark-overview/hdispark.architecture.png  "Apache Spark on Azure HDInsight")


**Wilt u aan de slag met Apache Spark in Azure HDInsight?** Zie [Snelstartgids: een Spark-cluster maken in HDInsight Linux en voorbeeldtoepassingen uitvoeren met Jupyter](hdinsight-apache-spark-jupyter-spark-sql.md).

>[AZURE.NOTE] Zie [Bekende problemen van Apache Spark in Azure HDInsight (Linux)](hdinsight-apache-spark-jupyter-spark-sql.md) voor een lijst met bekende problemen met en beperkingen van de huidige release.


## Waarom Spark in Azure HDInsight gebruiken? 

Azure HDInsight biedt een volledig beheerde Spark-service. Voordelen van het gebruik van Spark in HDInsight:

| Functie                             | Beschrijving       |
|-------------------------------------|-------------------|
| Het gemak van het maken van clusters            | U kunt binnen enkele minuten een nieuw Spark-cluster in HDInsight maken met de Azure-beheerportal, Azure PowerShell of de HDInsight-SDK voor .NET. Zie [Aan de slag met Spark-clusters in HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md) |
| Gebruiksgemak                     | Spark-clusters in HDInsight bevatten vooraf geconfigureerde Jupyter-notebooks. U kunt deze voor de interactieve gegevensverwerking en -visualisatie gebruiken. De URL is https://CLUSTERNAAM.azurehdinsight.net/jupyter. Vervang __CLUSTERNAAM__ door de naam van uw Spark HDInsight-cluster.|
| REST-API’s                       | Spark in HDInsight omvat [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server), een Spark-taakserver op basis van een REST-API om op afstand taken in te dienen en taken die worden uitgevoerd, te controleren. |
| Ondersteuning voor Azure Data Lake Store | Spark in HDInsight kan worden geconfigureerd voor het gebruik van Azure Data Lake Store als extra opslag. Zie [Overzicht van Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) voor meer informatie over Data Lake Store.
| Integratie met Azure-services | Spark in HDInsight wordt geleverd met een connector voor Azure Event Hubs. Met behulp van de Event Hubs kunnen klanten ook andere streamingtoepassingen bouwen, naast [Kafka](http://kafka.apache.org/), dat al beschikbaar is als onderdeel van Spark. |
| Ondersteuning voor R Server  | U kunt in een HDInsight Spark-cluster een R Server instellen voor het uitvoeren van gedistribueerde R-berekeningen met de snelheden die worden toegezegd voor een Spark-cluster. Zie [Aan de slag met R Server in HDInsight](hdinsight-hadoop-r-server-get-started.md) voor meer informatie.   |
| Integratie met IntelliJ IDEA  | U kunt de HDInsight-invoegtoepassing voor IntelliJ gebruiken voor het maken en verzenden van toepassingen naar een HDInsight Spark-cluster. Zie voor meer informatie [De invoegtoepassing HDInsight Tools for IntelliJ IDEA gebruiken om Spark-toepassingen te maken voor een HDInsight Spark Linux-cluster](hdinsight-apache-spark-intellij-tool-plugin.md). |
| Gelijktijdige query's              | Spark in HDInsight ondersteunt gelijktijdige query's. Hierdoor kunnen meerdere query's van één gebruiker of meerdere query's van verschillende gebruikers en toepassingen dezelfde clusterresources delen. |
| Opslaan in cache in SSD's                 | U kunt gegevens in het geheugen of in aan de clusterknooppunten gekoppelde SSD's cachen. Cachen in het geheugen biedt de beste queryprestaties, maar kan duur zijn. Cachen in SSD's biedt een goede optie voor het verbeteren van de prestaties van query's zonder de noodzaak tot het maken van een cluster dat zo groot is dat de volledige gegevensset in het geheugen past.|
| Integratie met BI-tools       | Spark voor HDInsight biedt connectors voor BI-tools zoals [Power BI](http://www.powerbi.com/) en [Tableau](http://www.tableau.com/products/desktop) voor gegevensanalyse.|
| Vooraf geladen Anaconda-bibliotheken        | Spark-clusters in HDInsight worden geleverd met Anaconda-bibliotheken die vooraf zijn geïnstalleerd. [Anaconda](http://docs.continuum.io/anaconda/) voorziet in bijna 200 bibliotheken voor machine learning, data-analyse, visualisatie, enzovoort.|
| Schaalbaarheid                     | Tijdens het maken kunt u het aantal knooppunten in het cluster opgeven, maar mogelijk wilt u het cluster vergroten of verkleinen, zodat dit aansluit bij de werkbelasting. Alle HDInsight-clusters bieden u de mogelijkheid het aantal knooppunten in het cluster te wijzigen. Bovendien kunnen Spark-clusters zonder gegevensverlies worden verwijderd, omdat alle gegevens worden opgeslagen in Azure Blob Storage. |
| 24/7 ondersteuning                    | Spark in HDInsight wordt geleverd met 24/7 ondersteuning op bedrijfsniveau en een SLA met 99,9% beschikbaarheid.|



## Wat zijn de gebruiksvoorbeelden voor Spark in HDInsight?

Apache Spark in HDInsight maakt de volgende belangrijke scenario's mogelijk.

### Interactieve gegevensanalyse en BI

[Bekijk een zelfstudie](hdinsight-apache-spark-use-bi-tools.md)

Apache Spark in HDInsight slaat gegevens op in Azure Blobs. Zakelijke deskundigen en besluitvormers kunnen die gegevens analyseren, erover rapporteren en Microsoft Power BI gebruiken voor het maken van interactieve rapporten van de geanalyseerde gegevens. Analisten kunnen beginnen met niet-gestructureerde/semi-gestructureerde gegevens in Azure Storage, een schema voor de gegevens definiëren met behulp van notebooks en vervolgens gegevensmodellen bouwen met behulp van Microsoft Power BI. Spark in HDInsight biedt ook ondersteuning voor een aantal BI-tools van derden, zoals Tableau, Qlikview en SAP Lumira, wat het tot een ideaal platform maakt voor gegevensanalisten, zakelijke deskundigen en besluitvormers.

### Iteratieve Machine Learning

[Bekijk een zelfstudie: Gebouwtemperaturen voorspellen met behulp van HVAC-gegevens](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

[Bekijk een zelfstudie: Voedselinspectieresultaten voorspellen](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

Apache Spark wordt geleverd met [MLlib](http://spark.apache.org/mllib/), een bibliotheek voor machine learning die boven op Spark is gebouwd. Hiernaast omvat Spark in HDInsight ook Anaconda, een Python-distributie met tal van andere pakketten voor machine learning. Combineer dit met een ingebouwde ondersteuning voor Jupyter-notebooks en u hebt een eersteklas omgeving voor het maken van machine learning-toepassingen.  

### Streaming en realtime gegevensanalyse

[Bekijk een zelfstudie](hdinsight-apache-spark-eventhub-streaming.md)

Analyse van realtime gegevens wordt gebruikt voor diverse scenario's. Denk aan het reduceren van de benodigde tijd of aan het verkrijgen van inzicht in gegevens door deze te verwerken zodra ze beschikbaar komen, maar ook aan het bouwen van een echte streamingoplossing. Spark in HDInsight biedt uitgebreide ondersteuning voor het bouwen van realtime analyseoplossingen. Spark omvat al connectors om gegevens op te halen uit diverse bronnen, zoals Kafka-, Flume-, Twitter-, ZeroMQ- en TCP-sockets, en HDInsight Spark voegt eersteklas ondersteuning toe voor het ophalen van gegevens uit Azure Event Hubs. Event Hubs zijn de meest gebruikte wachtrijservices in Azure. Dankzij out-of-the-box-ondersteuning voor Event Hubs is HDInsight een ideaal platform voor het bouwen van een realtime analysepijplijn.

##<a name="next-steps"></a>Welke onderdelen zijn er opgenomen als onderdeel van een Spark-cluster?

Spark in HDInsight omvat de volgende onderdelen die standaard beschikbaar zijn in de clusters.

- [Spark Core](https://spark.apache.org/docs/1.5.1/). Omvat Spark Core, Spark SQL, Spark-streaming-API's, GraphX en MLlib.
- [Anaconda](http://docs.continuum.io/anaconda/)
- [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
- [Jupyter Notebook](https://jupyter.org)

Spark in HDInsight biedt ook een [ODBC-stuurprogramma](http://go.microsoft.com/fwlink/?LinkId=616229) voor verbinding met Spark-clusters in HDInsight vanuit BI-tools, zoals Microsoft Power BI en Tableau.

## Waar moet ik beginnen?

Begin met het maken van een Spark-cluster in HDInsight Linux. Zie [Snelstartgids: een Spark-cluster maken in HDInsight Linux en voorbeeldtoepassingen uitvoeren met Jupyter](hdinsight-apache-spark-jupyter-spark-sql.md). 

## Volgende stappen

### Scenario 's

* [Spark met BI: interactieve gegevensanalyses uitvoeren met behulp van Spark in HDInsight met BI-tools](hdinsight-apache-spark-use-bi-tools.md)

* [Spark met Machine Learning: Spark in HDInsight gebruiken voor het analyseren van de gebouwtemperatuur met behulp van HVAC-gegevens](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark met Machine Learning: Spark in HDInsight gebruiken om voedselinspectieresultaten te voorspellen](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Spark-streaming: Spark in HDInsight gebruiken voor het bouwen van realtime streamingtoepassingen](hdinsight-apache-spark-eventhub-streaming.md)

* [Websitelogboekanalyse met Spark in HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### Toepassingen maken en uitvoeren

* [Een zelfstandige toepassing maken met behulp van Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Taken op afstand uitvoeren in een Spark-cluster met behulp van Livy](hdinsight-apache-spark-livy-rest-interface.md)

### Tools en uitbreidingen

* [De invoegtoepassing HDInsight Tools for IntelliJ IDEA gebruiken om Spark Scala-toepassingen te maken en in te dienen](hdinsight-apache-spark-intellij-tool-plugin.md)

* [De invoegtoepassing HDInsight Tools for IntelliJ IDEA gebruiken om op afstand fouten in Spark Scala-toepassingen op te lossen](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Zeppelin-notebooks gebruiken met een Spark-cluster in HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Beschikbare kernels voor Jupyter-notebook in Spark-cluster voor HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Externe pakketten gebruiken met Jupyter-notebooks](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Jupyter op uw computer installeren en verbinding maken met een HDInsight Spark-cluster](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### Resources beheren

* [Resources beheren voor het Apache Spark-cluster in Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Taken die worden uitgevoerd in een Apache Spark-cluster in HDInsight, traceren en er fouten in oplossen](hdinsight-apache-spark-job-debugging.md)


[hdinsight-opslag]: hdinsight-hadoop-use-blob-storage.md



<!--HONumber=Jun16_HO2-->


