<properties
    pageTitle="HBase-zelfstudie: aan de slag met HBase-clusters op basis van Linux in Hadoop | Microsoft Azure"
    description="Volg deze HBase-zelfstudie om Apache HBase met Hadoop te gebruiken in HDInsight. Maak tabellen vanuit de HBase-shell en gebruik Hive om query's uit te voeren op de tabellen."
    keywords="apache hbase,hbase,hbase shell,hbase tutorial"
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/04/2016"
    ms.author="jgao"/>



# HBase-zelfstudie: aan de slag met het gebruik van Apache HBase met Hadoop gebaseerd op Linux in HDInsight 

[AZURE.INCLUDE [hbase-selector](../../includes/hdinsight-hbase-selector.md)]

Informatie over het maken van een HBase-cluster in HDInsight, het maken van HBase-tabellen en het uitvoeren van query's op tabellen met Hive. Zie [Overzicht van HDInsight HBase][hdinsight-hbase-overview] voor algemene informatie over HBase.

De informatie in dit document is specifiek voor HDInsight-clusters die zijn gebaseerd op Linux. Voor informatie over Windows-clusters gebruikt u de tabselector boven aan de pagina om te schakelen.

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

###Vereisten

Voordat u met deze HBase-zelfstudie begint, moet u beschikken over het volgende:

- **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- [Secure Shell (SSU)](hdinsight-hadoop-linux-use-ssh-unix.md). 
- [curl](http://curl.haxx.se/download.html).

## Een HBase-cluster maken

Voor de volgende procedure wordt een Azure ARM-sjabloon gebruikt om een HBase-cluster te maken. Zie [Op Linux gebaseerde Hadoop-clusters maken in HDInsight](hdinsight-hadoop-provision-linux-clusters.md) voor meer inzicht in de parameters die voor deze procedure worden gebruikt en andere methoden voor het maken van clusters.

1. Klik op de volgende afbeelding om een ARM-sjabloon in Azure Portal te openen. De ARM-sjabloon bevindt zich in een openbare blob-container. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Voer op de blade **Parameters** het volgende in:

    - **Clusternaam**: voer een naam in voor het HBase-cluster dat u maakt.
    - **Aanmeldingsgegevens voor het cluster**: de standaardaanmeldingsnaam is **admin**.
    - **SSH-gebruikersnaam en - wachtwoord**: de standaardgebruikersnaam is **sshuser**.  U kunt de naam wijzigen.
     
    Andere parameters zijn optioneel.  
    
    Elk cluster is afhankelijk van een Azure Blob Storage-account. Nadat u een cluster hebt verwijderd, blijven de gegevens in het opslagaccount staan. De naam van het standaardopslagaccount voor het cluster is de naam waaraan 'store' is toegevoegd. Deze is vastgelegd in de sectie met sjabloonvariabelen.
        
3. Klik op **OK** om de parameters op te slaan.
4. Klik vanuit de blade **Aangepaste implementatie** op de vervolgkeuzelijst **Resourcegroep** en klik op **Nieuw** om een nieuwe resourcegroep te maken.  De resourcegroep is een container waarin het cluster, het afhankelijke opslagaccount en andere gekoppelde resources zijn gegroepeerd.
5. Klik op **Juridische voorwaarden** en klik vervolgens op **Maken**.
6. Klik op **Maken**. Het duurt ongeveer 20 minuten om een cluster te maken.


>[AZURE.NOTE] Nadat een HBase-cluster is verwijderd, kunt u een ander HBase-cluster maken met de dezelfde standaard blob-container. Het nieuwe cluster haalt de HBase-tabellen op die u hebt gemaakt in het oorspronkelijke cluster.

## Tabellen maken en gegevens invoegen

U kunt SSH gebruiken om verbinding te maken met HBase-clusters. U kunt HBase Shell gebruiken om HBase-tabellen te maken, gegevens in te voegen en een query voor gegevens uit te voeren. Zie [SSH gebruiken met Hadoop op basis van Linux in HDInsight via Linux, Unix of OS X](hdinsight-hadoop-linux-use-ssh-unix.md) en [SSH gebruiken met Hadoop op basis van Linux in HDInsight via Windows](hdinsight-hadoop-linux-use-ssh-windows.md) voor informatie over het gebruik van SSH in Linux, Unix, OS X en Windows.
 

Voor de meeste mensen worden de gegevens weergegeven in een tabelindeling:

![tabelgegevens in hdinsight hbase][img-hbase-sample-data-tabular]

In HBase, wat een implementatie van BigTable is, zien dezelfde gegevens er als volgt uit:

![bigtable-gegevens in hdinsight hbase][img-hbase-sample-data-bigtable]

Alles wordt waarschijnlijk duidelijker zodra u de volgende procedure hebt voltooid.  


**De HBase-shell gebruiken**

1. Voer de volgende opdracht uit in SSH:

        hbase shell

4. Maak een HBase met twee kolomfamilies:

        create 'Contacts', 'Personal', 'Office'
        list
5. Voeg enkele gegeven in:

        put 'Contacts', '1000', 'Personal:Name', 'John Dole'
        put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
        put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
        put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
        scan 'Contacts'

    ![hdinsight hadoop hbase shell][img-hbase-shell]

6. Een enkel rij ophalen

        get 'Contacts', '1000'

    Aangezien er maar één rij is, zijn de resultaten hetzelfde als voor de scanopdracht.

    Zie [Inleiding tot het HBase-schemaontwerp][hbase-schema] voor meer informatie over het HBase-tabelschema. Raadpleeg de [Snelzoekgids voor Apache HBase][hbase-quick-start] voor meer HBase-opdrachten.

6. De shell afsluiten

        exit

**Gegevens bulksgewijs laden in de HBase-tabel met contacten**

U kunt in HBase verschillende methoden gebruiken om gegevens in tabellen te laden.  Zie [Bulk loading](http://hbase.apache.org/book.html#arch.bulk.load) (Bulkgsgewijs laden) voor meer informatie.


Er is een voorbeeld van een gegevensbestanden geüpload naar een openbare blob-container, *wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt*.  De inhoud van het gegevensbestand is:

    8396    Calvin Raji     230-555-0191    230-555-0191    5415 San Gabriel Dr.
    16600   Karen Wu        646-555-0113    230-555-0192    9265 La Paz
    4324    Karl Xie        508-555-0163    230-555-0193    4912 La Vuelta
    16891   Jonn Jackson    674-555-0110    230-555-0194    40 Ellis St.
    3273    Miguel Miller   397-555-0155    230-555-0195    6696 Anchor Drive
    3588    Osa Agbonile    592-555-0152    230-555-0196    1873 Lion Circle
    10272   Julia Lee       870-555-0110    230-555-0197    3148 Rose Street
    4868    Jose Hayes      599-555-0171    230-555-0198    793 Crawford Street
    4761    Caleb Alexander 670-555-0141    230-555-0199    4775 Kentucky Dr.
    16443   Terry Chander   998-555-0171    230-555-0200    771 Northridge Drive

U kunt een tekstbestand maken en het bestand desgewenst uploaden naar uw eigen opslagaccount. Zie [Gegevens voor Hadoop-taken in HDInsight uploaden][hdinsight-upload-data] voor instructies.

> [AZURE.NOTE] In deze procedure wordt gebruikgemaakt van de HBase-tabel met contacten die u in de laatste procedure hebt gemaakt.

1. Voer de volgende opdracht vanuit SSH uit om het gegevensbestand te transformeren naar StoreFiles en op te slaan naar een relatief pad dat is opgegeven door Dimporttsv.bulk.output:.  Als u zich in HBase Shell bevindt, gebruikt u de afsluitopdracht om af te sluiten.

        hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:Phone, Office:Phone, Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

4. Voer de volgende opdracht uit om de gegevens uit /example/data/storeDataFileOutput naar de HBase-tabel te uploaden:

        hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts

5. U kunt de HBase-shell openen en de scanopdracht gebruiken om de tabelinhoud weer te geven.



## Hive gebruiken om een query op HBase uit te voeren

Met Hive kunt u een query uitvoeren op de gegevens in HBase-tabellen. In deze sectie maakt u een Hive-tabel die is toegewezen aan de HBase-tabel en deze gebruikt om een query voor de gegevens in uw HBase-tabel uit te voeren.

1. Open **PuTTY** en maak verbinding met het cluster.  Zie de instructies in de vorige procedure.
2. Open de Hive-shell.

       hive
3. Voer het volgende HiveQL-script uit om een Hive-tabel te maken die is toegewezen aan de HBase-tabel. Zorg ervoor dat u met de HBase-shell de voorbeeldtabel hebt gemaakt waarnaar eerder in deze zelfstudie is verwezen voordat u deze instructie uitvoert.

        CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
        STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
        WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
        TBLPROPERTIES ('hbase.table.name' = 'Contacts');

2. Voer het volgende HiveQL-script uit. Met de Hive-query wordt een query uitgevoerd op de gegevens in de HBase-tabel:

        SELECT count(*) FROM hbasecontacts;

## HBase REST API's gebruiken met Curl

> [AZURE.NOTE] Wanneer u Curl of een andere REST-communicatie gebruikt met WebHCat, moet u de aanvragen verifiëren door de gebruikersnaam en het wachtwoord voor de beheerder van het HDInsight-cluster op te geven. U moet ook de clusternaam gebruiken als onderdeel van de URI (Uniform Resource Identifier) die wordt gebruikt om de aanvragen naar de server te verzenden.
>
> Voor de opdrachten in deze sectie vervangt u **USERNAME** door de gebruiker die moet worden geverifieerd bij het cluster en vervangt u **PASSWORD** door het wachtwoord voor het gebruikersaccount. Vervang **CLUSTERNAME** door de naam van uw cluster.
>
> De REST API is beveiligd via [basisverificatie](http://en.wikipedia.org/wiki/Basic_access_authentication). U moet aanvragen altijd uitvoeren via een beveiligde HTTP-verbinding (HTTPS). Zo zorgt u ervoor dat uw referenties veilig worden verzonden naar de server.

1. Gebruik een opdrachtregel met de volgende opdracht om te controleren of u verbinding met uw HDInsight-cluster kunt maken:

        curl -u <UserName>:<Password> -G https://<ClusterName>.azurehdinsight.net/templeton/v1/status

    Het antwoord dat u ontvangt, is vergelijkbaar met het volgende antwoord:

    {"status":"ok","version":"v1"}

  In deze opdracht worden de volgende parameters gebruikt:

    * **-u**: de gebruikersnaam en het wachtwoord voor het verifiëren van de aanvraag.
    * **-G**: hiermee wordt aangegeven dat dit een GET-aanvraag is.

2. Gebruik de volgende opdracht om een lijst met bestaande HBase-tabellen weer te geven:

        curl -u <UserName>:<Password> -G https://<ClusterName>.azurehdinsight.net/hbaserest/

3. Gebruik de volgende opdracht om een nieuwe HBase-tabel met twee kolomfamilies te maken:

        curl -u <UserName>:<Password> -v -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/schema" -H "Accept: application/json" -H "Content-Type: application/json" -d "{\"@name\":\"test\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}"

    Het schema wordt opgegeven in de JSON-indeling.

4. Gebruik de volgende opdracht om enkele gegevens in te voegen:

        curl -u <UserName>:<Password> -v -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/schema" -H "Accept: application/json" -H "Content-Type: application/json" -d "{\"Row\":{\"key\":\"1000\",\"Cell\":{\"column\":\"Personal:Name\", \"$\":\"John Dole\"}}}"

5. Gebruik de volgende opdracht om een rij te verkrijgen:

        curl -u <UserName>:<Password> -v -X GET "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/1000" -H "Accept: application/json"

## De clusterstatus controleren

HBase in HDInsight wordt geleverd met een webgebruikersinterface voor het bewaken van clusters. Met de webgebruikersinterface kunt u statistieken of informatie over regio's aanvragen.

SSH kan ook worden gebruikt voor tunneling van lokale aanvragen, zoals webaanvragen, naar het HDInsight-cluster. De aanvraag wordt vervolgens naar de aangevraagde resource gerouteerd, alsof deze afkomstig is van het hoofdknooppunt van het HDInsight-cluster. Zie [SSH gebruiken met Hadoop op basis van Linux in HDInsight via Windows](hdinsight-hadoop-linux-use-ssh-windows.md#tunnel) voor meer informatie.

**Een SSH-tunnelingsessie tot stand brengen**

1. Open **PuTTY**.  
2. Als u een SSH-sleutel hebt opgegeven tijdens het maken van uw gebruikersaccount, moet u de volgende stap uitvoeren om de persoonlijke sleutel te selecteren die u wilt gebruiken voor de verificatie bij het cluster:

    Vouw onder **Categorie** de optie **Verbinding** en vervolgens **SSH** uit en selecteer **Auth**. Klik tot slot op **Bladeren** en selecteer het PPK-bestand dat uw persoonlijke sleutel bevat.

3. Klik in **Categorie** op **Sessie**.
4. Voer in het scherm met basisopties voor uw PuTTY-sessie de volgende waarden in:

    - **Hostnaam**: het SSH-adres van uw HDInsight-server in het veld Hostnaam (of IP-adres). Het SSH-adres is de naam van uw cluster, gevolgd door **-ssh.azurehdinsight.net**. Bijvoorbeeld *mijncluster-ssh.azurehdinsight.net*.
    - **Poort**: 22. De ssh-poort op het hoofdknooppunt 0 is 22.  
5. Vouw in de sectie **Categorie** aan de linkerkant van het dialoogvenster achtereenvolgens **Verbinding** en **SSH** uit en klik vervolgens op **Tunnels**.
6. Geef op het formulier met de opties voor SSH-port fowarding de volgende informatie op:

    - **Bronpoort**: de poort op de client die u wilt doorsturen. Bijvoorbeeld 9876.
    - **Dynamisch**: hiermee schakelt u de dynamische routering van SOCKS-proxy's in.
7. Klik op **Toevoegen** om de instellingen toe te voegen.
8. Klik op **Openen** onder aan het dialoogvenster om een SSH-verbinding te openen.
9. Meld u desgevraagd met een SSH-account aan bij de server. Zo wordt er een SSH-sessie tot stand gebracht en wordt de tunnel ingeschakeld.

**De FQDN van de zookeepers met Ambari zoeken**

1. Blader naar https://<ClusterName>.azurehdinsight.net/.
2. Voer twee keer de referenties voor uw clusteraccount in.
3. Klik in het linkermenu op **zookeeper**.
4. Klik op een van de drie **ZooKeeper Server**-koppelingen in het overzicht.
5. Kopieer **Hostnaam**. Bijvoorbeeld zk0-CLUSTERNAME.xxxxxxxxxxxxxxxxxxxx.cx.internal.cloudapp.net.

**Een clientprogramma (Firefox) configureren en de clusterstatus controleren**

1. Open Firefox.
2. Klik op de knop **Menu openen**.
3. Klik op **Opties**.
4. Klik achtereenvolgens op **Geavanceerd**, **Netwerk** en **Instellingen**.
5. Selecteer **Handmatige proxyconfiguratie**.
6. Voer de volgende waarden in:

    - **Socks-host**: localhost
    - **Poort**: gebruik dezelfde poort als de poort die u hebt geconfigureerd in de Putty SSH-tunneling.  Bijvoorbeeld 9876.
    - **SOCKS v5**: (geselecteerd)
    - **Externe DNS**: (geselecteerd)
7. Klik op **OK** om de wijzigingen op te slaan.
8. Blader naar http://<TheFQDN of a ZooKeeper>: 60010/master-status.

In een cluster met hoge beschikbaarheid ziet u een koppeling naar het huidige actieve HBase-hoofdknooppunt dat de webgebruikersinterface host.

##Het cluster verwijderen

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## Volgende stappen
In deze HBase-zelfstudie voor HDInsight hebt u geleerd hoe u een HBase-cluster maakt en hoe u tabellen maakt en de gegevens in deze tabellen vanuit de HBase-shell weergeeft. U hebt ook geleerd hoe u een Hive-query op gegevens in HBase-tabellen uitvoert en hoe u de HBase C# REST API's gebruikt om een HBase-tabel te maken en gegevens op te halen uit de tabel.

Voor meer informatie zie:

- [Overzicht van HDInsight HBase][hdinsight-hbase-overview]: HBase is een Apache, open-source NoSQL-database op basis van Hadoop. HBase biedt willekeurige toegang en een sterke consistentie voor grote hoeveelheden ongestructureerde en semigestructureerde gegevens.


[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hbase-reference]: http://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: http://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-portal]: https://portal.azure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-file-browser.png
[img-hbase-shell]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png



<!--HONumber=Jun16_HO2-->


