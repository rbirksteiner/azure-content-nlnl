<properties
    pageTitle="Inleiding tot Apache Storm op HDInsight | Microsoft Azure"
    description="Maak kennis met Apache Storm en leer hoe u Storm op HDInsight kunt gebruiken om oplossingen voor realtime gegevensanalyse in de cloud te bouwen."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="paulettm"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/18/2016"
   ms.author="larryfr"/>

#Inleiding tot Apache Storm op HDInsight: realtime analyses voor Hadoop

Met Apache Storm op HDInsight kunt u gedistribueerde, realtime analyseoplossingen in de Azure-omgeving maken met [Apache Hadoop](http://hadoop.apache.org).

##Wat is Apache Storm?

Apache Storm is een gedistribueerd, fouttolerant, open source berekeningssysteem waarmee u in realtime gegevens met Hadoop kunt verwerken. Dankzij de mogelijkheid om gegevens die de eerste keer niet zijn verwerkt, opnieuw te verwerken, bieden Storm-oplossingen u de garantie dat de gegevens worden verwerkt.

##Waarom Storm op HDInsight gebruiken?

Apache Storm op HDInsight is een beheerd cluster dat is geïntegreerd in de Azure-omgeving. Apache Storm op HDInsight biedt de volgende voordelen:

* Wordt uitgevoerd als een beheerde service met een SLA met 99,9% beschikbaarheid

* De taal van uw keuze: biedt ondersteuning voor Storm-onderdelen die zijn geschreven in **Java**, **C#** en **Python**

    * Ondersteunt verschillende programmeertalen: gegevens lezen met Java en vervolgens verwerken met C.#
    
        > [AZURE.NOTE] C#-topologieën worden alleen ondersteund op HDInsight-clusters die zijn gebaseerd op Windows.

    * Gebruik de **Trident** Java-interface om Storm-topologieën te maken die ondersteuning bieden voor een eenmalige verwerking van berichten, transactionele DataStore-persistentie en een aantal algemene Stream Analytics-bewerkingen.

* Bevat ingebouwde functies voor het aanpassen van de schaal: een HDInsight-cluster schalen zonder gevolgen voor de actieve Storm-topologieën.

* Kan worden geïntegreerd met andere Azure-services, waaronder Event Hub, Azure Virtual Network, SQL Database, Blob Storage en DocumentDB.

    * De mogelijkheden van meerdere HDInsight-clusters combineren door gebruik te maken van Azure Virtual Network: analytische pijplijnen maken die HDInsight-, HBase- of Hadoop-clusters gebruiken

Zie [Companies Using Apache Storm](https://storm.apache.org/documentation/Powered-By.html) (Bedrijven die Apache Storm gebruiken) voor een lijst met bedrijven die gebruikmaken van Apache Storm.

Zie [Aan de slag met Storm op HDInsight][gettingstarted] om aan de slag te gaan met Storm.

###Eenvoudig inrichten

U kunt in enkele minuten een nieuw Storm op HDInsight-cluster inrichten. Geef de clusternaam, de grootte, het beheerdersaccount en het opslagaccount op. Azure maakt het cluster, inclusief voorbeeldtopologieën en een dashboard voor beheer via het web.

> [AZURE.NOTE] U kunt ook Storm-clusters inrichten met de [Azure CLI](../xplat-cli-install.md) of [Azure PowerShell](../powershell-install-configure.md).

Binnen 15 minuten nadat u een aanvraag hebt verzonden, beschikt u over een nieuw actief Storm-cluster en bent u klaar voor uw eerste realtime analysepijplijn.

###Gebruiksgemak

__Voor Storm op HDInsight-clusters die zijn gebaseerd op Linux__ kunt u verbinding maken met het cluster via SSH en kunt u de opdracht `storm` gebruiken om topologieën te starten en te beheren. Daarnaast kunt u Ambari gebruiken om de Storm-service te bewaken en kunt u met de gebruikersinterface van Storm de topologieën bewaken en beheren die worden uitgevoerd.

Zie [Aan de slag met Apache Storm op HDInsight op basis van Linux](hdinsight-apache-storm-tutorial-get-started-linux.md) voor meer informatie over het werken met op Linux gebaseerde Storm-clusters.

__Voor Storm op HDInsight-clusters die zijn gebaseerd op Windows__ kunt u met HDInsight Tools for Visual Studio C#- en hybride C#/Java-topologieën maken en deze vervolgens verzenden naar uw Storm op HDInsight-cluster.  

![Een Storm-project maken](./media/hdinsight-storm-overview/createproject.png)

HDInsight Tools voor Visual Studio biedt tevens een interface waarmee u Storm-topologieën op een cluster kunt bewaken en beheren.

![Storm-beheer](./media/hdinsight-storm-overview/stormview.png)

Zie [C# Storm-topologieën ontwikkelen met HDInsight Tools voor Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md) voor een voorbeeld van het gebruik van HDInsight Tools om een Storm-toepassing te maken.

Zie [Aan de slag met HDInsight Tools voor Visual Studio](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md) voor meer informatie over de HDInsight Tools voor Visual Studio.

Elk Storm op HDInsight-cluster biedt ook een online Storm-dashboard waarmee u de Storm topologieën die op het cluster worden uitgevoerd, kunt bewaken en beheren.

![Storm-dashboard](./media/hdinsight-storm-overview/dashboard.png)

Zie [Apache Storm-topologieën op HDInsight implementeren en beheren](hdinsight-storm-deploy-monitor-topology.md) voor meer informatie over het gebruik van het Storm-dashboard.

Storm op HDInsight biedt ook eenvoudige integratie met Azure Event Hubs via de **Event Hub Spout**. De nieuwste versie van dit onderdeel is beschikbaar op [https://github.com/hdinsight/hdinsight-storm-examples/tree/master/lib/eventhubs](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/lib/eventhubs). Zie de volgende documenten voor meer informatie over het gebruik van dit onderdeel.

* [Een C#-topologie ontwikkelen die gebruikmaakt van Azure Event Hubs](hdinsight-storm-develop-csharp-event-hub-topology.md)

* [Een Java-topologie ontwikkelen die gebruikmaakt van Azure Event Hubs](hdinsight-storm-develop-java-event-hub-topology.md)

###Betrouwbaarheid

Apache Storm zorgt ervoor dat elk binnenkomend bericht altijd volledig wordt verwerkt, zelfs wanneer de gegevensanalyse is verspreid over honderden knooppunten.

Het **Nimbus-knooppunt** biedt een vergelijkbare functionaliteit aan de Hadoop JobTracker en wijst taken toe aan andere knooppunten in het cluster via **Zookeeper**. Zookeeper-knooppunten bieden coördinatie voor het cluster en faciliteren de communicatie tussen Nimbus en het **Supervisor**-proces op de werkrolknooppunten. Als een verwerkingsknooppunt wordt uitgeschakeld, wordt het Nimbus-knooppunt hiervan op de hoogte gesteld en worden de taak en de bijbehorende gegevens toegewezen aan een ander knooppunt.

Apache Storm wordt standaard geconfigureerd met slechts één Nimbus knooppunt. Storm op HDInsight voert twee Nimbus-knooppunten uit. Als het primaire knooppunt mislukt, schakelt het HDInsight-cluster over naar het secundaire knooppunt en wordt het primaire knooppunt hersteld.

![Diagram van nimbus, zookeeper en supervisor](./media/hdinsight-storm-overview/nimbus.png)

###Schalen

U kunt tijdens het maken het aantal knooppunten in het cluster opgeven, maar mogelijk wilt u het cluster vergroten of verkleinen zodat het overeenkomt met de workload. Alle HDInsight-clusters bieden u de mogelijkheid het aantal knooppunten in het cluster te wijzigen, zelfs terwijl de gegevens worden verwerkt.

> [AZURE.NOTE] Als u wilt profiteren van nieuwe knooppunten die zijn toegevoegd door te schalen, moet u de topologieën die zijn gestart voordat de clustergrootte werd uitgebreid, opnieuw verdelen.

###Ondersteuning

Storm op HDInsight wordt geleverd met een 24-uurs ondersteuning op ondernemingsniveau. Storm op HDInsight beschikt over een SLA van 99,9%. Dit betekent dat het cluster gegarandeerd minimaal 99,9% van de tijd extern verbinding heeft.

##Algemene gebruiksvoorbeelden voor realtime analyses

Hier volgen enkele algemene scenario's waarvoor u Apache Storm op HDInsight kunt gebruiken. Lees [How companies are using Storm](https://storm.incubator.apache.org/documentation/Powered-By.html) (Hoe bedrijven Storm gebruiken) voor informatie over praktijkscenario's.

* Internet der dingen (IoT)
* Fraudedetectie
* Sociale analyses
* Extraction, Transformation, Loading (ETL)
* Netwerkbewaking
* Search
* Mobile Engagement

##Hoe worden gegevens in HDInsight Storm verwerkt?

Apache Storm voert **topologieën** uit in plaats van de MapReduce-taken die u mogelijk kent uit HDInsight of Hadoop. Een Storm op HDInsight-cluster bevat twee soorten knooppunten: hoofdknooppunten met **Nimbus** en werkrolknooppunten met **Supervisor**.

* **Nimbus**: net als bij de JobTracker in Hadoop is Nimbus verantwoordelijk voor het distribueren van de code in het cluster, het toewijzen van taken aan virtuele machines en het controleren op fouten. HDInsight biedt twee Nimbus-knooppunten. Er is dus geen Single Point of Failure voor Storm op HDInsight.

* **Supervisor**: de supervisor voor elk werkrolknooppunt is verantwoordelijk voor het starten en stoppen van de **werkprocessen** op het knooppunt.

* **Werkproces**: voert een subset van een **topologie** uit. Een actieve topologie wordt gedistribueerd over verschillende werkprocessen in het cluster.

* **Topologie**: definieert een grafiek van de berekening die **stromen** gegevens verwerkt. In tegenstelling tot MapReduce-taken worden topologieën uitgevoerd totdat u ze stopt.

* **Stream**: een niet-afhankelijke verzameling **tuples**. Streams worden geproduceerd door **spouts** en **bolts** en ze worden verbruikt door **bolts**.

* **Tuple**: een benoemde lijst met dynamische getypeerde waarden.

* **Spout**: gebruikt gegevens van een gegevensbron en verzendt een of meer **streams**.

    > [AZURE.NOTE] In veel gevallen worden gegevens uit een wachtrij zoals Kafka, Azure Service Bus-wachtrijen of Event Hubs gelezen. De wachtrij zorgt ervoor dat gegevens bij een storing worden bewaard.

* **Bolt**: verbruikt **streams**, voert verwerkingstaken op **tuples** uit en verzendt **streams**. Bolts zijn ook verantwoordelijk voor het schrijven van gegevens naar externe opslaglocaties, zoals een wachtrij, HDInsight, HBase, een blob of een andere gegevensopslag.

* **Apache Thrift**: een softwareframework voor schaalbare meertalige serviceontwikkeling. Hiermee kunt u services bouwen die zowel voor C++, Java, Python, PHP, Ruby, Erlang, Perl, Haskell, C#, Cocoa, JavaScript, Node.js, Smalltalk als andere talen werken.

    * **Nimbus** is een Thrift-service en een **topologie** is een Thrift-definitie. Het is dus mogelijk om topologieën te ontwikkelen met verschillende programmeertalen.

Zie [Storm-zelfstudie][apachetutorial] op apache.org voor meer informatie over Storm-onderdelen.


##Welke programmeertalen kan ik gebruiken?

Het Storm op HDInsight-cluster biedt ondersteuning voor C# en Java en Python.

### C&#35;

Dankzij de HDInsight Tools voor Visual Studio kunnen .NET-ontwikkelaars een topologie in C# ontwerpen en implementeren. U kunt ook hybride topologieën maken die gebruikmaken van Java- en C#-onderdelen.

Zie [C#-topologieën met Visual Studio ontwikkelen voor Apache Storm op HDInsight](hdinsight-storm-develop-csharp-visual-studio-topology.md) voor meer informatie.

###Java

De meeste Java-voorbeelden die u tegenkomt, zijn gewoon Java of Trident. Trident is een abstractie op hoog niveau waarmee u gemakkelijker bepaalde bewerkingen kunt uitvoeren, zoals samenvoegen, verzamelen, groeperen en filteren. Trident voert echter bewerkingen uit op batches tuples, terwijl een onbewerkte Java-oplossing een stream tuple voor tuple verwerkt.

Zie de [Trident-zelfstudie](https://storm.incubator.apache.org/documentation/Trident-tutorial.html) op apache.org voor meer informatie over Trident.

Zie de [lijst met voorbeelden van Storm-topologieën](hdinsight-storm-example-topology.md) of de Storm Starter-voorbeelden op uw HDInsight-cluster voor voorbeelden van Java- en Trident-topologieën.

De Storm Starter-voorbeelden vindt u in de map __ /usr/hdp/current/storm-client/contrib/storm-starter__ op Linux-clusters en in de map **%storm_home%\contrib\storm-starter** op Windows-clusters.

##Wat zijn enkele algemene ontwikkelingspatronen?

###Gegarandeerde berichtverwerking

Storm kan verschillende niveaus van gegarandeerde berichtverwerking bieden. Een eenvoudige Storm-toepassing biedt u bijvoorbeeld de garantie dat de gegevens minimaal één keer worden verwerkt. Trident kan u de garantie bieden dat gegevens exact één keer worden verwerkt.

Zie [Guarantees on data processing](https://storm.apache.org/about/guarantees-data-processing.html) (Garanties met betrekking tot de gegevensverwerking) op apache.org voor meer informatie.

###IBasicBolt

Het patroon voor het lezen van een invoer-tuple, het verzenden van nul of meer tuples om de invoer-tuple vervolgens onmiddellijk aan het einde van de uitvoeringsmethode te bevestigen, is een veelgebruikte methode en Storm biedt de [IBasicBolt](https://storm.apache.org/apidocs/backtype/storm/topology/IBasicBolt.html)-interface om dit patroon te automatiseren.

###Samenvoegingen

Het samenvoegen van twee streams gegevens varieert per toepassing. U kunt bijvoorbeeld elke tuple uit meerdere streams samenvoegen in één nieuwe stream, of u kunt alleen batches tuples voor een specifiek venster samenvoegen. In beide gevallen kunt u samenvoeging realiseren met [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29), wat een manier is om te definiëren hoe tuples worden gerouteerd naar bolts.

In het volgende Java-voorbeeld wordt fieldsGrouping gebruikt om tuples die afkomstig zijn uit de onderdelen 1, 2 en 3 te routeren naar de **MyJoiner**-bolt.

    builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));

###Batchverwerking

Batchverwerking kan op verschillende manieren worden gerealiseerd. Met een eenvoudige Storm Java-topologie kunt een eenvoudige prestatiemeter gebruiken om X tuples in batch te verwerken voordat u ze verzendt. U kunt ook een intern tijdmechanisme gebruiken, ook wel een tick tuple genoemd, om elke X seconden een batch te verzenden.

Zie [Sensorgegevens met Storm en HBase op HDInsight analyseren](hdinsight-storm-sensor-data-analysis.md) voor een voorbeeld van het gebruik van tick tuples.

Als u Trident gebruikt, is dit gebaseerd op het verwerken van batches tuples.

###Caching

In-memory caching wordt vaak gebruikt als mechanisme om de verwerking te versnellen, omdat de veelgebruikte assets in het geheugen blijven staan. Aangezien een topologie wordt verdeeld over meerdere knooppunten en meerdere processen binnen elk knooppunt, moet u het gebruik van [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) overwegen om ervoor te zorgen dat tuples met de velden die worden gebruikt om te zoeken in het cachegeheugen, altijd worden omgeleid naar hetzelfde proces. Hiermee voorkomt u dubbele cachevermeldingen voor verschillende processen.

###Top N streamen

Wanneer uw topologie afhankelijk is van de berekening van een top N-waarde, zoals de top 5 van trends op Twitter, moet u de top N-waarde parallel berekenen en de uitvoer van deze berekeningen vervolgens samenvoegen in een algemene waarde. Dit kan worden gedaan door [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) te gebruiken om per veld naar de parallelle bolts te routeren (die de gegevens partitioneert op basis van de veldwaarde) en vervolgens te routeren naar een bolt die globaal de top N-waarde bepaalt.

Zie het [RollingTopWords](https://github.com/nathanmarz/storm-starter/blob/master/src/jvm/storm/starter/RollingTopWords.java)-voorbeeld voor een voorbeeld hiervan.

##Volgende stappen

Meer informatie over realtime analyseoplossingen met Apache Storm in HDInsight:

* [Aan de slag met Storm op HDInsight][gettingstarted]

* [Voorbeeldtopologieën van Storm op HDInsight](hdinsight-storm-example-topology.md)

[stormtrident]: https://storm.incubator.apache.org/documentation/Trident-API-Overview.html
[samoa]: http://yahooeng.tumblr.com/post/65453012905/introducing-samoa-an-open-source-platform-for-mining
[apachetutorial]: https://storm.incubator.apache.org/documentation/Tutorial.html
[gettingstarted]: hdinsight-apache-storm-tutorial-get-started-linux.md



<!--HONumber=Jun16_HO2-->


