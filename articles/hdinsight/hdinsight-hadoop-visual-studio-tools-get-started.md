<properties
    pageTitle="Informatie over het gebruik van Visual Studio Hadoop-hulpprogramma's voor HDInsight | Microsoft Azure"
    description="Informatie over de installatie en het gebruik van Visual Studio Hadoop-hulpprogramma's voor HDInsight om verbinding te maken met een Hadoop-cluster en een Hive-query uit te voeren."
    keywords="hadoop tools,hive query,visual studio"
    services="HDInsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="05/25/2016"
    ms.author="jgao"/>

# Aan de slag met Visual Studio Hadoop-hulpprogramma’s voor HDInsight om een Hive-query uit te voeren

Informatie over het gebruik van HDInsight Tools voor Visual Studio om verbinding te maken met HDInsight-clusters en Hive-query's te verzenden. Zie [Inleiding tot HDInsight][hdinsight.introduction] en [Aan de slag met HDInsight][hdinsight.get.started] voor meer informatie over het gebruik van HDInsight. Zie [C#-topologieën met Visual Studio ontwikkelen voor Apache Storm op HDInsight][hdinsight.storm.visual.studio.tools] voor meer informatie over verbinding maken met een Storm-cluster.

**Vereisten**

Al u deze zelfstudie wilt voltooien en Hadoop-hulpprogramma's in Visual Studio wilt gebruiken, hebt u het volgende nodig:

- Een Azure HDInsight-cluster: een cluster op basis van Linux of op basis van Windows werkt met de stappen in dit document. Zie een van de volgende onderwerpen voor informatie over het maken van een cluster:

    - [Aan de slag met HDInsight op basis van Linux](hdinsight-hadoop-linux-tutorial-get-started.md)
    - [Aan de slag met HDInsight op basis van Windows](hdinsight-hadoop-tutorial-get-started-windows.md)

- Een werkstation met de volgende software:

    - Windows 8.1, Windows 8 of Windows 7
    - Visual Studio (een van de volgende versies):
        - Visual Studio 2013 Community/Professional/Premium/Ultimate met [Update 4](https://www.microsoft.com/download/details.aspx?id=44921)
        - Visual Studio 2015 (Community/Enterprise)

    >[AZURE.NOTE] Op dit moment wordt HDInsight Tools voor Visual Studio alleen geleverd bij de Engelse versie.


## HDInsight-tools voor Visual Studio

HDInsight-tools voor Visual Studio en het Microsoft Hive ODBC-stuurprogramma zijn verpakt met Microsoft Azure SDK voor .NET versie 2.5.1 of hoger. U kunt ze installeren met het [webplatforminstallatieprogramma](http://go.microsoft.com/fwlink/?LinkId=255386). U moet de optie kiezen die overeenkomt met uw versie van Visual Studio. Als u Visual Studio niet hebt geïnstalleerd, kunt u de meest recente Visual Studio Community en Azure SDK installeren met het [webplatforminstallatieprogramma](http://go.microsoft.com/fwlink/?LinkId=255386) of via de volgende koppelingen:

- [Visual Studio Community 2015 met Microsoft Azure SDK](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/VS2015CommunityAzurePack.appids)
- [Visual Studio Community 2013 met Microsoft Azure SDK](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/VS2013CommunityAzurePack.appids)
- [Microsoft Azure SDK voor .NET (VS 2015)](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/VWDOrVs2015AzurePack.appids)
- [Microsoft Azure SDK voor .NET (VS 2013)](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/VWDOrVs2013AzurePack.appids)

![Hadoop-hulpprogramma’s: webinstallatieprogramma voor HDinsight Tools voor Visual Studio.][1]

## Verbinding maken met Azure-abonnementen
Met de HDInsight Tools voor Visual Studio kunt u verbinding maken met uw HDInsight-clusters, enkele eenvoudige beheerbewerkingen uitvoeren en Hive-query's uitvoeren.

>[AZURE.NOTE] Zie [Write and submit Hive queries using Visual Studio](http://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx) (Hive-query’s schrijven en verzenden met Visual Studio) voor informatie over het maken van verbinding met een algemeen Hadoop-cluster.


**Verbinding maken met uw Azure-abonnement**

1.  Open Visual Studio.
2.  Klik in het menu **Weergeven** op **Server Explorer** om het venster Server Explorer te openen.
3.  Vouw **Azure** uit en vouw vervolgens **HDInsight** uit.

    >[AZURE.NOTE]U ziet dat het venster **Taakoverzicht HDInsight** is geopend. Als het venster niet wordt weergegeven, klikt u op **Other Windows** (Overige vensters) in het menu **View** (Beeld) en klikt u vervolgens op **HDInsight Task List Window** (Lijstvenster met HDInsight-taken).  
4.  Voer de referenties voor uw Azure-abonnement in en klik vervolgens op **Aanmelden**. Dit is alleen vereist als u op dit werkstation nooit verbinding hebt gemaakt met het Azure-abonnement vanuit Visual Studio.
5.  In Server Explorer wordt een lijst met bestaande HDInsight-clusters weergegeven. Als u geen clusters hebt, kunt u een cluster inrichten door gebruik te maken van Azure Portal, Azure PowerShell of de HDInsight SDK. Zie [HDInsight-clusters inrichten][hdinsight-provision] voor meer informatie.

    ![Hadoop-hulpprogramma's: HDInsight Tools voor Visual Studio Server Explorer-clusterlijst][5]
6.  Een HDInsight-cluster uitbreiden. U ziet **Hive-databases**, een standaardopslagaccount, gekoppelde opslagaccounts en een **Hadoop Service-logboekbestand**. U kunt de entiteiten verder uitbreiden.

Nadat u verbinding met uw Azure-abonnement hebt gemaakt, kunt u het volgende:

**Verbinding maken met Azure Portal vanuit Visual Studio**

- Vouw in Server Explorer **Azure** > **HDInsight** uit, klik met de rechtermuisknop op een HDInsight-cluster en klik vervolgens op **Cluster beheren in Azure Portal**.

**Vragen stellen en feedback geven vanuit Visual Studio**

- Klik in het menu **Extra** op **HDInsight** en klik vervolgens op **MSDN-forum** om vragen te stellen of klik op **Feedback geven**.

## Door de gekoppelde resources navigeren

In Server Explorer worden het standaardaccount voor opslag en alle gekoppelde opslagaccounts weergegeven. Als u het standaardopslagaccount uitvouwt, kunt u de containers op het opslagaccount zien. Het standaardopslagaccount en de standaardcontainer worden gemarkeerd. U kunt ook met de rechtermuisknop op een van de containers klikken om de inhoud weer te geven.

![De lijst met clusters in Server Explorer van HDInsight Tools voor Visual Studio][2]

## Een Hive-query uitvoeren
[Apache Hive][apache.hive] is een datawarehouse-infrastructuur die is gebouwd op Hadoop om overzichten, query's en analyses te leveren. HDInsight Tools voor Visual Studio biedt ondersteuning voor het uitvoeren van Hive-query's vanuit Visual Studio. Zie [Hive gebruiken met HDInsight][hdinsight.hive] voor meer informatie over Hive.

Het testen van een Hive-script op een HDInsight-cluster neemt vrij veel tijd in beslag. Dit kan enkele minuten of langer duren. HDInsight Tools voor Visual Studio kan het Hive-script lokaal valideren zonder dat er verbinding hoeft te worden gemaakt met een live cluster.

Met HDInsight Tools voor Visual Studio kunnen gebruikers ook in de Hive-taak kijken door de YARN-logboeken van bepaalde Hive-taken te verzamelen en op te halen.

### De **hivesampletable** bekijken
Alle HDInsight-clusters bevatten een voorbeeld van een Hive-tabel met de naam*hivesampletable*. Deze tabel wordt gebruikt om te laten zien hoe u een lijst met Hive-tabellen maakt, het tabelschema weergeeft en de rijen in de Hive-tabel vermeldt.



**Een lijst met Hive-tabellen maken en het Hive-tabelschema weergeven**

1.  Vouw in **Server Explorer** achtereenvolgens **Azure** > **HDInsight** > het gewenste cluster > **Hive-databases** > **Standaard** > **hivesampletable** uit om het tabelschema weer te geven.
4.  Klik met de rechtermuisknop op de **hivesampletable** en klik vervolgens op **View Top 100 Rows** (Top 100 rijen weergeven) om de rijen weer te geven. Dit is gelijk aan het uitvoeren van de volgende Hive-query met het Hive ODBC-stuurprogramma:

        SELECT * FROM hivesampletable LIMIT 100

    U kunt het aantal rijen aanpassen.

    ![Hadoop-hulpprogramma's: Visual Studio HDinsight Hive-schema-query][6]

### Hive-tabellen maken

U kunt de GUI of Hive-query's gebruiken om een Hive-tabel te maken. Zie [Hive-query's uitvoeren](#run.queries) voor meer informatie over het gebruik van Hive-query's.

**Een Hive-tabel maken**

1. Vouw in **Server Explorer** achtereenvolgens **Azure** > **HDInsight-clusters** een HDInsight-cluster > **Hive-databases** uit. Klik vervolgens met de rechtermuisknop op **standaard** en klik op **Tabel maken**.
2. Configureer de tabel.
3. Klik op **Tabel maken** om de taak voor het maken van de nieuwe Hive-tabel te verzenden.

    ![Hadoop-hulpprogramma's: hdinsight visual studio tool voor het maken van een Hive-tabel][7]

### <a name="run.queries"></a>Hive-query’s valideren en uitvoeren
Er zijn twee manieren om Hive-query's te maken en uit te voeren:

- Ad-hocquery's maken
- Een Hive-toepassing maken

**Ad-hocquery's maken, valideren en uitvoeren**

1. Vouw in **Server Explorer** achtereenvolgens **Azure** en **HDInsight-clusters** uit.
2. Klik met de rechtermuisknop op het cluster waar u de query wilt uitvoeren en klik vervolgens op **Write a Hive Query** (Een Hive-query schrijven).
3. Voer de Hive-query's in Zoals u kunt zien, biedt de Hive-editor ondersteuning voor IntelliSense. HDInsight Tools voor Visual Studio biedt ondersteuning voor het laden van de externe metagegevens wanneer u het Hive-script bewerkt. Wanneer u bijvoorbeeld SELECT * FROM typt, geeft IntelliSense alle voorgestelde tabelnamen weer. Wanneer een tabelnaam is opgegeven, worden de kolomnamen door de IntelliSense vermeld. Het hulpprogramma ondersteunt bijna alle DML-instructies, subquery's en ingebouwde UDF's van Hive.

    ![Hadoop-hulpprogramma's: HDInsight Visual Studio Tools IntelliSense][13]

    ![Hadoop-hulpprogramma's: HDInsight Visual Studio Tools IntelliSense][14]

    > [AZURE.NOTE] Alleen de metagegevens van de clusters die in HDInsight Toolbar zijn geselecteerd, worden voorgesteld.
4. (Optioneel): klik op **Script valideren** om het script op syntaxisfouten te controleren.

    ![Hadoop-hulpprogramma's: HDInsight Tools voor de lokale validatie van Visual Studio][10]

4. Klik op **Verzenden** of **Verzenden (geavanceerd)**. Met de optie voor geavanceerd verzenden, configureert u de **Taaknaam**, **Argumenten**, **Aanvullende configuraties** en **Statusmap** voor het script:

    ![hdinsight hadoop hive query][9]

    Nadat u de taak hebt verzonden, ziet u een venster met een **samenvatting van de Hive-taak**.

    ![Samenvatting van een HDInsight Hadoop Hive-query][8]
5. Gebruik de knop **Vernieuwen** om de status bij te werken totdat de status van de taak is gewijzigd in **Voltooid**.
6. Klik op de koppelingen onderaan om het volgende weer te geven: **Job Query** (Taakquery), **Job Output** (Taakuitvoer), **Job log** (Logboekbestand van taak) of **Yarn log** (Yarn-logboekbestand).



**Een Hive-oplossing maken en uitvoeren**

1. Klik in het menu **FILE** op **Nieuw** en klik vervolgens op **Project**.
2. Selecteer in het linkerdeelvenster **HDInsight** selecteer in het middelste venster **Hive-toepassing**, geef de eigenschappen op en klik vervolgens op **OK**.

    ![Hadoop-hulpprogramma's: hdinsight visual studio tools nieuw hive-project][11]
3. Dubbelklik in **Solution Explorer** op **Script.hql** om het script te openen.
4. Als u het Hive-script wilt valideren, klikt u op de knop **Script valideren** of klikt u met de rechtermuisknop in de Hive-editor en klikt u in het contextmenu vervolgens op **Script valideren**.


### Hive-taken weergeven
U kunt taakquery's, taakuitvoer, logboekbestanden van taken en Yarn-logboekbestanden voor Hive-taken weergeven. Zie de vorige schermafbeelding voor meer informatie.

Met de meest recente versie van het hulpprogramma kunt u in de Hive-taak kijken door de YARN-logboeken van bepaalde Hive-taken te verzamelen en op te halen. Een YARN-logboek kan u helpen om prestatieproblemen te onderzoeken. Zie [Programmatische toegang tot HDInsight-toepassingslogboeken][hdinsight.access.application.logs] voor informatie over hoe HDInsight YARN-logboeken verzamelt.

**Hive-taken weergeven**

1. Vouw in **Server Explorer** achtereenvolgens **Azure** en **HDInsight** uit.
2. Klik met de rechtermuisknop op een HDInsight-cluster en klik vervolgens op **Taken weergeven**. U ziet een lijst met de Hive-taken die zijn uitgevoerd op het cluster.
3. Klik op een taak in de lijst om de taak te selecteren en gebruik vervolgens het venster **Hive Job Summary** (Samenvatting Hive-taak) om **Job Query** (Taakquery), **Job Output** (Taakuitvoer), **Job Log** (Logboekbestand van taak) of **Yarn log** (Yarn-logboekbestand) te openen.

    ![Hadoop-hulpprogramma's: Hive-taken in HDInsight Visual Studio Tools weergeven][12]

### Sneller pad voor het uitvoeren van Hive via HiveServer2

>[AZURE.NOTE] Deze functie werkt alleen op HDInsight-cluster versie 3.2 en nieuwer.

De HDInsight Tools die worden gebruikt om Hive-taken via [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) te verzenden (ook wel bekend als Templeton). Het duurde lang om taakgegevens en informatie over de fout te retourneren.
De hulpprogramma's van HDInsight lossen dit probleem op door de Hive-taken rechtstreeks via HiveServer2 uit voeren in het cluster, zodat RDP/SSH wordt omzeild.
Naast betere prestaties kunnen gebruikers Hive ook weergeven op Tez-grafieken en de taakdetails weergeven.

Voor HDInsight-cluster versie 3.2 of hoger wordt de knop **Execute via HiveServer2** (Uitvoeren via HiveServer2) weergegeven:

![hdinsight visual studio tools uitvoeren via hiveserver2](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.execute.via.hiveserver2.png)

U kunt bovendien zien hoe de logboeken in realtime worden teruggestreamd. Daarnaast kunt u de taakgrafieken bekijken wanneer de Hive-query wordt uitgevoerd in Tez.

![hdinsight visual studio tools snel pad voor het uitvoeren van hive](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.fast.path.hive.execution.png)

**Het verschil tussen het uitvoeren van query's via HiveServer2 en het verzenden van query's via WebHCat**

Het uitvoeren van query's via HiveServer2 biedt verschillende prestatievoordelen biedt, maar heeft ook enkele beperkingen. Sommige beperkingen zijn niet geschikt voor productiegebruik. In de volgende tabel kunt u de verschillen zien:

| |Via HiveServer2 uitvoeren |Verzenden via WebHCat|
|---|---|---|
|Query's uitvoeren|Hiermee wordt de overhead geëlimineerd in WebHCat (die MapReduce-taak met de naam TempletonControllerJob start).|Zolang een query wordt uitgevoerd via WebHCat, start WebHCat een MapReduce-taak die resulteert in extra latentie.|
|Logboeken terugstreamen|Bijna in realtime.|De logboekbestanden voor het uitvoeren van de taak zijn alleen beschikbaar wanneer de taak is voltooid.|
|Taakgeschiedenis weergeven|Als een query wordt uitgevoerd via HiveServer2, blijft de taakgeschiedenis (taaklogboek, taakuitvoer) niet behouden. De toepassing kan worden weergegeven in de gebruikersinterface van YARN met beperkte informatie.|Als een query wordt uitgevoerd via WebHCat, blijft de taakgeschiedenis (taaklogboek, taakuitvoer) bewaard en kan deze worden weergegeven met Visual Studio/HDInsight SDK/PowerShell. |
|Venster sluiten|  Uitvoeren via HiveServer2 is een 'synchrone' methode. Dit betekent dat de vensters geopend moet blijven. Als de vensters worden gesloten, wordt de queryuitvoering geannuleerd.|Verzenden via WebHCat is een 'asynchrone' methode. Dit betekent dat u de query via WebHCat kunt verzenden en Visual Studio kunt sluiten. U kunt op elk gewenst moment terugkeren om de resultaten te bekijken.|


### Prestatiegrafiek Tez Hive-taak

HDInsight Visual Studio-hulpprogramma's bieden ondersteuning voor de weergave van de prestatiegrafieken voor de Hive-taken die worden uitgevoerd door de Tez-uitvoeringsengine. Zie [Hive in HDInsight gebruiken][hdinsight.hive] voor meer informatie over het inschakelen van Tez. Nadat u een Hive-taak in Visual Studio hebt ingediend, wordt in Visual Studio de grafiek weergegeven als de taak is voltooid.  Mogelijk moet u op de knop **Vernieuwen** klikken om de meest recente taakstatus op te halen.

> [AZURE.NOTE] Deze functie is alleen beschikbaar voor HDInsight-clusters van versie 3.2.4.593 of hoger en werkt alleen voor voltooide taken (als u uw werk via WebHCat hebt verzonden). Deze grafiek wordt weergegeven wanneer u uw query uitvoert via HiveServer2. Dit werkt zowel voor Windows- als Linux-clusters.

![prestatiegrafiek hadoop component tez](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.hive.tez.performance.graph.png)

De weergave Hive Operator is toegevoegd aan deze release voor meer inzicht in uw Hive-query. U hoeft alleen maar te dubbelklikken op de hoekpunten van de taakgrafiek om alle operators in het hoekpunt weer te geven. U kunt ook een bepaalde operator aanwijzen om meer informatie over deze operator weer te geven.

### Taakuitvoeringsweergave voor Hive op Tez-taken

De taakuitvoeringsweergave voor Hive op Tez-taken kan worden gebruikt om gestructureerde en gevisualiseerde informatie voor Hive-taken en meer taakdetails op te halen. Bij prestatieproblemen kunt u de weergave gebruiken om meer details op te vragen. Bijvoorbeeld hoe elke taak functioneert en de gedetailleerde informatie over elke taak (gegevens lezen/schrijven, start-/eindtijd van het schema enzovoort), zodat u de taakconfiguraties of de systeemarchitectuur op basis van de gevisualiseerde informatie kunt optimaliseren.

![hdinsight visual studio tools taakuitvoeringsweergave](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.task.execution.view.png)

## Pig-scripts uitvoeren

HDInsight Tools voor Visual Studio ondersteunt het maken en verzenden van Pig-scripts naar HDInsight-clusters. Gebruikers kunnen een Pig-project maken op basis van een sjabloon en het script vervolgens verzenden naar HDInsight-clusters.

## Feedback en bekende problemen

- Momenteel worden de HiveServer2-resultaten weergegeven als tekst. Dit is niet ideaal. Er wordt momenteel gewerkt aan een oplossing.

- Als de resultaten zijn gestart met NULL-waarden, worden de resultaten momenteel niet weergegeven. Dit probleem is opgelost en als u vastloopt op dit probleem, kunt u ons een e-mail sturen of contact opnemen met het ondersteuningsteam.

- Het HQL-script dat door Visual Studio wordt gemaakt, word gecodeerd op basis van de lokale regio-instellingen van de gebruiker. Het script wordt mogelijk niet correct uitgevoerd als de gebruiker het script als een binair bestand uploadt naar het cluster.

Als u suggesties of feedback wilt geven of als u problemen ondervindt bij het gebruik van dit hulpprogramma, kunt u een e-mail sturen naar hdivstool@microsoft.com.

## Volgende stappen
In dit artikel hebt u geleerd hoe u de Hadoop-hulpprogramma's kunt gebruiken om vanuit Visual Studio verbinding te maken met HDInsight-clusters en hoe u een Hive-query uitvoert. Ga voor meer informatie naar:

- [Hadoop Hive in HDInsight gebruiken][hdinsight.hive]
- [Aan de slag met Hadoop in HDInsight][hdinsight.get.started]
- [Hadoop-taken verzenden in HDInsight][hdinsight.submit.jobs]
- [Twitter-gegevens met Hadoop analyseren in HDInsight][hdinsight.analyze.twitter.data]


<!--Anchors-->
[Installatie]: #installation
[Verbinding maken met uw Azure-abonnement]: #connect-to-your-azure-subscription
[Door de gekoppelde resources navigeren]: #navigate-the-linked-resources
[Hive-query's uitvoeren]: #run-hive-queries
[Volgende stappen]: #next-steps

<!--Image references-->
[1]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.wpi.png
[2]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.linked.resources.png
[5]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.server.explorer.png
[6]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.hive.schema.png
[7]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.create.hive.table.png
[8]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.run.hive.job.summary.png
[9]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.submit.jobs.advanced.png
[10]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.validate.hive.script.png
[11]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.new.hive.project.png
[12]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.view.hive.jobs.png
[13]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.table.names.png
[14]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.column.names.png


<!--Link references-->
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight.introduction]: hdinsight-hadoop-introduction.md
[hdinsight.get.started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight.hive]: hdinsight-use-hive.md
[hdinsight.submit.jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight.analyze.twitter.data]: hdinsight-analyze-twitter-data.md
[hdinsight.storm.visual.studio.tools]: hdinsight-storm-develop-csharp-visual-studio-topology.md
[hdinsight.access.application.logs]: hdinsight-hadoop-access-yarn-app-logs.md

[apache.hive]: http://hive.apache.org



<!--HONumber=Jun16_HO2-->


