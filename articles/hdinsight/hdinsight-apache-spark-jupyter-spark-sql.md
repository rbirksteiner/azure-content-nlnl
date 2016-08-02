<properties
    pageTitle="Een Spark-cluster maken in HDInsight Linux en Spark SQL van Jupyter gebruiken voor interactieve analyses | Microsoft Azure"
    description="Stapsgewijze instructies voor het snel maken van een Apache Spark-cluster in HDInsight en vervolgens Spark SQL gebruiken via Jupyter-notebooks om interactieve query's uit te voeren."
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


# Aan de slag: een Apache Spark-cluster in HDInsight maken en interactieve query's uitvoeren met Spark SQL

Leer hoe u een Apache Spark-cluster in HDInsight maakt en vervolgens een [Jupyter](https://jupyter.org)-notebook gebruikt om interactieve Spark SQL-query's uit te voeren in het Spark-cluster.

   ![Aan de slag met Apache Spark in HDInsight](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.getstartedflow.png  "Get started using Apache Spark in HDInsight tutorial. Steps illustrated: create a storage account; create a cluster; run Spark SQL statements")

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

**Vereisten:**

- **Een Azure-abonnement**. Voordat u met deze zelfstudie begint, moet u een Azure-abonnement hebben. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Een SSH-client (Secure Shell)**: Linux-, Unix- en OS X-systemen leveren een SSH client via de opdracht `ssh`. Voor Windows-systemen raden we [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) aan.
    
- **SSH-sleutels (Secure Shell) (optioneel)**: u kunt het SSH-account dat wordt gebruikt om verbinding te maken met het cluster, beveiligen met behulp van een wachtwoord of een openbare sleutel. Als u een wachtwoord instelt, kunt u snel aan de slag. Gebruik deze optie als u snel een cluster wilt maken en bepaalde testtaken wilt uitvoeren. Gebruik van een sleutel is veiliger, maar er zijn aanvullende instellingen vereist. Deze procedure is bijvoorbeeld aan te bevelen bij het maken van een productiecluster. In dit artikel gebruiken we de wachtwoordprocedure. Zie voor instructies over het maken en gebruiken van SSH-sleutels met HDInsight de volgende artikelen:

    -  Voor een Linux-computer: [SSH gebruiken met HDInsight (Hadoop) op basis van Linux via Linux, Unix of OS X](hdinsight-hadoop-linux-use-ssh-unix.md).
    
    -  Voor een Windows-computer: [SSH gebruiken met HDInsight (Hadoop) op basis van Linux via Windows](hdinsight-hadoop-linux-use-ssh-windows.md).


## Een Spark-cluster maken

In deze sectie maakt u een HDInsight versie 3.4-cluster (Spark versie 1.6.1) met behulp van een Azure ARM-sjabloon. Zie [Versiebeheer van HDInsight-onderdelen](hdinsight-component-versioning.md) voor meer informatie over de versies van HDInsight en de bijbehorende SLA’s. Zie [HDInsight-clusters maken](hdinsight-hadoop-provision-linux-clusters.md) voor andere methoden voor het maken van clusters.

1. Klik op de volgende afbeelding om in de Azure Portal een ARM-sjabloon te openen.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-spark-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    De ARM-sjabloon bevindt zich in een openbare blobcontainer, *https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-spark-cluster-in-hdinsight.json*. 
   
2. Voer op de blade Parameters het volgende in:

    - **Clusternaam**: voer een naam in voor het Hadoop-cluster dat u maakt.
    - **Clusteraanmeldgegevens**: de standaardaanmeldnaam is admin.
    - **SSH-aanmeldgegevens**.
    
    Noteer deze waarden.  U hebt ze later in de zelfstudie nodig.

    > [AZURE.NOTE] SSH wordt gebruikt voor externe toegang tot het HDInsight-cluster via een opdrachtregel. De gebruikersnaam en het wachtwoord die u hier opgeeft, worden gebruikt om via SSH verbinding te maken met het cluster. De SSH-gebruikersnaam moet bovendien uniek zijn, omdat hiermee een gebruikersaccount wordt gemaakt op alle knooppunten van het HDInsight-cluster. Hieronder volgt een aantal van de accountnamen die zijn gereserveerd voor gebruik door de services op het cluster en niet als SSH-gebruikersnaam kunnen worden gebruikt:
    >
    > root, hdiuser, storm, hbase, ubuntu, zookeeper, hdfs, yarn, mapred, hbase, hive, oozie, falcon, sqoop, admin, tez, hcat, hdinsight-zookeeper.

    > Raadpleeg een van de volgende artikelen voor meer informatie over het gebruik van SSH met HDInsight:

    > * [SSH gebruiken met Hadoop op basis van Linux in HDInsight via Linux, Unix of OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
    > * [SSH gebruiken met Hadoop op basis van Linux in HDInsight via Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

    
3. Klik op **OK** om de parameters op te slaan.

4. Klik vanuit de blade **Aangepaste implementatie** op de vervolgkeuzelijst **Resourcegroep**. Klik vervolgens op **Nieuw** om een nieuwe resourcegroep te maken. De resourcegroep is een container waarin het cluster, het afhankelijke opslagaccount en andere gekoppelde resources zijn gegroepeerd.

5. Klik op **Juridische voorwaarden** en vervolgens op **Maken**.

6. Klik op **Maken**. U ziet een nieuwe tegel met de titel Implementatie indienen voor Sjabloonimplementatie. Het duurt ongeveer 20 minuten om het cluster en de SQL-database te maken.



## Spark SQL-query's uitvoeren met behulp van een Jupyter-notebook

In deze sectie gebruikt u een Jupyter-notebook om Spark SQL-query's uit te voeren op het Spark-cluster. HDInsight Spark-clusters bieden twee kernels die u met de Jupyter-notebook kunt gebruiken. Dit zijn:

* **PySpark** (voor toepassingen die zijn geschreven in Python)
* **Spark** (voor toepassingen die zijn geschreven in Scala)

In dit artikel gebruikt u de PySpark-kernel. In het artikel [Beschikbare kernels op Jupyter-notebooks met HDInsight Spark-clusters](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels) vindt u meer informatie over de voordelen van het gebruik van de PySpark-kernel. Enkele van de belangrijkste voordelen van het gebruik van de PySpark-kernel zijn echter:

* Het is niet nodig om de context voor Spark en die voor Hive in te stellen. Deze worden automatisch voor u ingesteld.
* U kunt cel-magics, zoals `%%sql`, gebruiken om uw SQL- of Hive-query's direct uit te voeren, zonder eventuele voorgaande codefragmenten.
* De uitvoer voor de SQL- of Hive-query's wordt automatisch weergegeven.

### Een Jupyter-notebook maken met PySpark-kernel 

1. Klik vanuit de [Azure Portal](https://portal.azure.com/), vanaf het startboard, op de tegel voor uw Spark-cluster (als u deze aan het startboard hebt vastgemaakt). U kunt ook naar uw cluster navigeren onder **Bladeren** > **HDInsight-clusters**.   

2. Klik vanuit de blade Spark-cluster op **Snelkoppelingen**. Klik vervolgens vanuit het **Cluster-dashboard** op **Jupyter Notebook**. Voer de beheerdersreferenties voor het cluster in als u daarom wordt gevraagd.

    > [AZURE.NOTE] Mogelijk bereikt u de Jupyter-notebook voor uw cluster ook door de volgende URL in uw browser te openen. Vervang __CLUSTERNAME__ door de naam van uw cluster.
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Maak een nieuwe notebook. Klik op **Nieuw** en klik vervolgens op **PySpark**.

    ![Een nieuwe Jupyter-notebook maken](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.createnotebook.png "Create a new Jupyter notebook")

3. Er wordt een nieuwe notebook gemaakt en geopend met de naam Untitled.pynb. Klik bovenaan op de naam van de notebook en wijzig deze in een beschrijvende naam.

    ![Een naam opgeven voor de notebook](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.notebook.name.png "Provide a name for the notebook")

4. Omdat u de notebook met behulp van de PySpark-kernel hebt gemaakt, hoeft u niet expliciet contexten te maken. De Spark- en Hive-contexten worden automatisch voor u gemaakt tijdens het uitvoeren van de eerste codecel. Als eerste stap importeert u de typen die voor dit scenario zijn vereist. Plak hiertoe het volgende codefragment in een cel en druk op **SHIFT + ENTER**.

        from pyspark.sql.types import *
        
    Telkens wanneer u een taak in Jupyter uitvoert, toont de venstertitel van uw webbrowser de status **(Bezet)** samen met de notebooktitel. Ook ziet u een gevulde cirkel naast de **PySpark**-tekst in de rechterbovenhoek. Nadat de taak is voltooid, verandert deze in een lege cirkel.

     ![Status van een Jupyter-notebooktaak](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.jupyter.job.status.png "Status of a Jupyter notebook job")

4. Laad voorbeeldgegevens in een tijdelijke tabel. Wanneer u een Spark-cluster in HDInsight maakt, wordt het voorbeeldgegevensbestand, **hvac.csv**, gekopieerd naar het bijbehorende opslagaccount onder **\HdiSamples\HdiSamples\SensorSampleData\hvac**.

    Plak het volgende codevoorbeeld in een lege cel en druk op **SHIFT + ENTER**. Dit codevoorbeeld registreert de gegevens in een tijdelijke tabel genaamd **hvac**.

        # Load the data
        hvacText = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
        
        # Create the schema
        hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])
        
        # Parse the data in hvacText
        hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))
        
        # Create a data frame
        hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)
        
        # Register the data fram as a table to run queries against
        hvacdf.registerTempTable("hvac")

5. Omdat u een PySpark-kernel gebruikt, kunt u nu rechtstreeks een SQL-query uitvoeren op de tijdelijke tabel **hvac**, die u zojuist hebt gemaakt met behulp van de `%%sql`-magic. Voor meer informatie over de `%%sql`-magic, evenals over andere magics die voor de PySpark-kernel beschikbaar zijn, raadpleegt u [Beschikbare kernels op Jupyter-notebooks met HDInsight Spark-clusters](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels).
        
        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

5. Nadat de taak is voltooid, wordt standaard de volgende uitvoer in tabelvorm weergegeven.

    ![Tabeluitvoer van het queryresultaat](./media/hdinsight-apache-spark-jupyter-spark-sql/tabular.output.png "Table output of query result")

    U kunt de resultaten ook in andere visualisaties bekijken. Zo ziet een gebiedsgrafiek voor dezelfde uitvoer er als volgt uit.

    ![Gebiedsgrafiek van het queryresultaat](./media/hdinsight-apache-spark-jupyter-spark-sql/area.output.png "Area graph of query result")


6. Wanneer u klaar bent met het uitvoeren van de toepassing, moet u de notebook afsluiten om de resources vrij te geven. Dit doet u door in het menu **Bestand** in de notebook te klikken op **Sluiten en stoppen**. Hiermee wordt de notebook afgesloten.

##Het cluster verwijderen

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## Zie ook


* [Overzicht: Apache Spark in Azure HDInsight](hdinsight-apache-spark-overview.md)

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


[hdinsight-versies]: hdinsight-component-versioning.md
[hdinsight-uploaden-gegevens]: hdinsight-upload-data.md
[hdinsight-opslag]: hdinsight-hadoop-use-blob-storage.md

[azure-aankoop-opties]: http://azure.microsoft.com/pricing/purchase-options/
[azure-lid-aanbiedingen]: http://azure.microsoft.com/pricing/member-offers/
[Azure-gratis-proefversie]: http://azure.microsoft.com/pricing/free-trial/
[Azure-beheerportal]: https://manage.windowsazure.com/
[Azure-maken-opslagaccount]: storage-create-storage-account.md



<!--HONumber=Jun16_HO2-->


