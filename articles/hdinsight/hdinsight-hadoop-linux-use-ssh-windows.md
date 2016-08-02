<properties
   pageTitle="SSH-sleutels met Hadoop gebruiken op Linux-clusters van Windows | Microsoft Azure"
   description="Informatie over het maken en gebruiken van SSH-sleutels voor de verificatie bij HDInsight-clusters op basis van Linux. Verbinding maken met clusters van Windows-clients via de PuTTY SSH-client."
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
   ms.date="03/25/2016"
   ms.author="larryfr"/>

#SSH gebruiken met Hadoop op basis van Linux in HDInsight via Windows

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
- [Linux, Unix, OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

Met [Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) kunt u een opdrachtregelinterface gebruiken om op afstand bewerkingen uit te voeren op uw HDInsight-clusters die zijn gebaseerd op Linux. Dit document bevat informatie over het gebruik van de PuTTY SSH-client om verbinding met HDInsight te maken vanaf Windows-clients.

> [AZURE.NOTE] Voor de stappen in dit artikel wordt ervan uitgegaan dat u een Windows-client gebruikt. Zie [SSH gebruiken met Hadoop op basis van Linux in HDInsight via Linux, Unix of OS X](hdinsight-hadoop-linux-use-ssh-unix.md) als u een Linux-, Unix- of OS X-client gebruikt.

##Vereisten

* **PuTTY** en **PuTTYGen** voor Windows-clients. Deze hulpprogramma's zijn beschikbaar via [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

* Een moderne webbrowser met ondersteuning voor HTML5.

OF

* [Azure CLI](../xplat-cli-install.md).

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 

##Wat is SSH?

SSH is een hulpprogramma waarmee u zich kun aanmelden bij een externe server en op afstand opdrachten kunt uitvoeren. Met HDInsight op basis van Linux brengt SSH een versleutelde verbinding met het hoofdknooppunt van het cluster tot stand en wordt er een opdrachtregel weergegeven die u kunt gebruiken om opdrachten in te voeren. Opdrachten worden dan rechtstreeks uitgevoerd op de server.

###SSH-gebruikersnaam

Een SSH-gebruikersnaam is de naam die u gebruikt voor de verificatie bij het HDInsight-cluster. Wanneer u tijdens het maken van het cluster een SSH-gebruikersnaam opgeeft, wordt deze gebruiker gemaakt op alle knooppunten in het cluster. Zodra het cluster is gemaakt, kunt u deze gebruikersnaam gebruiken om verbinding te maken met de hoofdknooppunten van het HDInsight-cluster. Vanaf de hoofdknooppunten kunt u vervolgens verbinding maken met de afzonderlijke werkrolknooppunten.

###SSH-wachtwoord of openbare sleutel

Een SSH-gebruiker kan een wachtwoord of een openbare sleutel voor verificatie gebruiken. Een wachtwoord is gewoon een verzonnen reeks tekens, terwijl een openbare sleutel deel uitmaakt van een cryptografisch sleutelpaar dat wordt gegenereerd om u op unieke wijze te identificeren.

Een sleutel is veiliger dan een wachtwoord, maar om een sleutel te genereren moet u extra stappen uitvoeren. Daarnaast moet u de bestanden bewaren op een beveiligde locatie. Als iemand zich toegang tot uw sleutelbestanden weet te verschaffen, heeft diegene ook toegang tot uw account. Of als u de sleutelbestanden verliest, kunt u zich niet aanmelden bij uw account.

Een sleutelpaar bestaat uit een openbare sleutel (die wordt verzonden naar de HDInsight-server) en een persoonlijke sleutel (die wordt bewaard op de clientcomputer). Als u via SSH verbinding met de HDInsight-server maakt, gebruikt de SSH-client de persoonlijke sleutel op uw computer voor de verificatie bij de server.

##Een SSH-sleutel maken

Gebruik de volgende informatie als u van plan bent SSH-sleutels voor uw cluster te gebruiken. Als u van plan bent een wachtwoord te gebruiken, kunt u deze sectie overslaan.

1. Open PuTTYGen.

2. Selecteer voor **Type sleutel dat moet worden gegenereerd** de optie **SSH-2 RSA** en klik vervolgens op **Genereren**.

    ![PuTTYGen-interface](./media/hdinsight-hadoop-linux-use-ssh-windows/puttygen.png)

3. Beweeg de muis in het gebied onder de voortgangsbalk totdat de balk wordt gevuld. Door de muis te bewegen, worden er willekeurige gegevens gegenereerd die worden gebruikt om de sleutel te genereren.

    ![de muis bewegen](./media/hdinsight-hadoop-linux-use-ssh-windows/movingmouse.png)

    Zodra de sleutel is gegenereerd, wordt de openbare sleutel weergegeven.

4. Voor extra beveiliging kunt u een wachtwoordzin in het veld **Sleutelwachtwoordzin** opgeven en vervolgens dezelfde waarde in het veld **Wachtwoordzin bevestigen** typen.

    ![wachtwoordzin](./media/hdinsight-hadoop-linux-use-ssh-windows/key.png)

    > [AZURE.NOTE] U kunt het best een beveiligde wachtwoordzin voor de sleutel gebruiken. Als u de wachtwoordzin vergeet, is er echter geen manier om deze te herstellen.

5. Klik op **Persoonlijke sleutel opslaan** om de sleutel op te slaan als een **PPK**-bestand. Deze sleutel wordt gebruikt voor de verificatie bij uw HDInsight-cluster dat is gebaseerd op Linux.

    > [AZURE.NOTE] U moet deze sleutel opslaan op een veilige locatie, aangezien deze kan worden gebruikt voor de toegang tot uw HDInsight-cluster dat is gebaseerd op Linux.

6. Klik op **Openbare sleutel opslaan** om de sleutel op te slaan als een **TXT**-bestand. Hiermee kunt u de openbare sleutel in de toekomst opnieuw gebruiken bij het maken van aanvullende HDInsight-clusters die zijn gebaseerd op Linux.

    > [AZURE.NOTE] De openbare sleutel wordt ook boven aan PuTTYGen weergegeven. U kunt met de rechtermuisknop op dit veld klikken, de waarde kopiëren en deze in een formulier plakken wanneer u een cluster met Azure Portal maakt.

##Een HDInsight-cluster op basis van Linux maken

Wanneer u een op Linux gebaseerd HDInsight-cluster maakt, moet u de openbare sleutel opgeven die eerder is gemaakt. Er zijn twee manieren om een HDInsight-cluster op basis van Linux te maken vanaf Windows-clients:

* **Azure Portal**: maakt gebruik van een webportal om het cluster te maken.

* **Azure CLI voor Mac-, Linux- en Windows**: maakt gebruik van opdrachtregelopdrachten om het cluster te maken.

Voor elk van deze methoden is de openbare sleutel vereist. Zie [Op Linux gebaseerde HDInsight-clusters inrichten](hdinsight-hadoop-provision-linux-clusters.md) voor de volledige informatie over het maken van een HDInsight-cluster op basis van Linux.

###Azure Portal

Wanneer u [Azure Portal][Preview Portal] gebruikt om een HDInsight-cluster op basis van Linux te maken, moet u een **SSH-gebruikersnaam** opgeven en kiest u ervoor om een **WACHTWOORD** of **OPENBARE SSH-SLEUTEL** op te geven.

Als u **OPENBARE SSH-SLEUTEL** selecteert, kunt u de openbare sleutel (weergegeven in het veld voor het plakken van de __openbare sleutel in het geautoriseerde OpenSSH-sleutelbestand\___ in PuttyGen,) in het veld __Openbare SSH-sleutel__ plakken of __Een bestand selecteren__ selecteren om naar het bestand met de openbare sleutel te bladeren en dit te selecteren.

![Afbeelding van een formulier waarin naar de openbare sleutel wordt gevraagd.](./media/hdinsight-hadoop-linux-use-ssh-windows/ssh-key.png)

Zo maakt u een aanmelding voor de opgegeven gebruiker en kan deze zich verifiëren middels een wachtwoord of SSH-sleutel.

###Azure-opdrachtregelinterface voor Mac, Linux en Windows

U kunt de [Azure CLI voor Mac, Linux en Windows](../xplat-cli-install.md) gebruiken om een nieuw cluster met de opdracht `azure hdinsight cluster create` te maken.

Zie [Hadoop Linux-clusters in HDInsight inrichten met aangepaste opties](hdinsight-hadoop-provision-linux-clusters.md) voor meer informatie over het gebruik van deze opdracht.

##Verbinding maken met een HDInsight-cluster dat is gebaseerd op Linux

1. Open PuTTY.

    ![Putty-interface](./media/hdinsight-hadoop-linux-use-ssh-windows/putty.png)

2. Als u een SSH-sleutel hebt opgegeven tijdens het maken van uw gebruikersaccount, moet u de volgende stap uitvoeren om de persoonlijke sleutel te selecteren die u wilt gebruiken voor de verificatie bij het cluster:

    Vouw onder **Categorie** de optie **Verbinding** en vervolgens **SSH** uit en selecteer **Auth**. Klik tot slot op **Bladeren** en selecteer het PPK-bestand dat uw persoonlijke sleutel bevat.

    ![De persoonlijke sleutel selecteren in de putty-interface](./media/hdinsight-hadoop-linux-use-ssh-windows/puttykey.png)

3. Selecteer onder **Categorie** de optie **Sessie**. Geef in het scherm **Basisopties voor uw PuTTY-sessie** in het veld **Hostnaam (of IP-adres)** het SSH-adres van uw HDInsight-server op. Er zijn twee mogelijke SSH-adressen die kunt gebruiken om verbinding met een cluster te maken:

    * __Adres van het hoofdknooppunt__: gebruik uw clusternaam en vervolgens **-ssh.azurehdinsight.net** om verbinding met het hoofdknooppunt te maken. Bijvoorbeeld **mijncluster-ssh.azurehdinsight.net**.
    
    * __Adres van het edge-knooppunt__: als u verbinding maakt met een R Server op een HDInsight-cluster, kunt u het adres __RServer.CLUSTERNAME.ssh.azurehdinsight.net__ gebruiken om verbinding met het edge-knooppunt op R Server te maken, waarbij CLUSTERNAME de naam van uw cluster is. Bijvoorbeeld __RServer.mycluster.ssh.azurehdinsight.net__.

    ![Putty-interface met een ingevoerd ssh-adres](./media/hdinsight-hadoop-linux-use-ssh-windows/puttyaddress.png)

4. Als u de verbindingsgegevens wilt opslaan voor toekomstig gebruik, voert u een naam voor deze verbinding in onder **Opgeslagen sessies** en klikt u vervolgens op **Opslaan**. De verbinding wordt toegevoegd aan de lijst met opgeslagen sessies.

5. Klik op **Openen** om verbinding te maken met het cluster.

    > [AZURE.NOTE] Als dit de eerste keer is dat u verbinding met het cluster hebt gemaakt, ontvangt u een beveiligingswaarschuwing. Dit is normaal. Selecteer **Ja** om de RSA2-sleutel van de server op te slaan in het cachegeheugen.

6. Desgevraagd geeft u de gebruiker op die u hebt opgegeven toen u het cluster hebt gemaakt. Als u een wachtwoord voor de gebruiker hebt opgegeven, wordt u gevraagd dit ook in te voeren.

> [AZURE.NOTE] In de bovenstaande stappen wordt ervan uitgegaan dat u poort 22 gebruikt, die verbinding maakt met hoofdknooppunt 0 op het HDInsight-cluster. Als u poort 23 gebruikt, maakt u verbinding met hoofdknooppunt 1. Zie [Beschikbaarheid en betrouwbaarheid van Hadoop-clusters in HDInsight](hdinsight-high-availability-linux.md) voor meer informatie over de hoofdknooppunten.

###Verbinding maken met werkrolknooppunten

De werkrolknooppunten zijn niet rechtstreeks toegankelijk van buiten het Azure-datacentrum, maar ze zijn wel via SSH toegankelijk vanaf het hoofdknooppunt van het cluster.

Als u een SSH-sleutel hebt opgegeven toen u uw gebruikersaccount hebt gemaakt, moet u de volgende stappen uitvoeren om de persoonlijke sleutel te gebruiken voor de verificatie bij het cluster als u verbinding met de werkrolknooppunten wilt maken:

1. Installeer Pageant via [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html). Dit hulpprogramma wordt gebruikt om de SSH-sleutels voor PuTTY op te slaan in het cachegeheugen.

2. Voer Pageant uit. Deze toepassing wordt geminimaliseerd tot een pictogram in het systeemvak. Klik met de rechtermuisknop op het pictogram en selecteer **Sleutel toevoegen**.

    ![sleutel toevoegen](./media/hdinsight-hadoop-linux-use-ssh-windows/addkey.png)

3. Wanneer het bladervenster wordt weergegeven, selecteert u het PPK-bestand dat de sleutel bevat en klikt u vervolgens op **Openen**. De sleutel wordt toegevoegd aan Pageant, die het weer doorgeeft aan PuTTY wanneer er verbinding met het cluster wordt gemaakt.

    > [AZURE.IMPORTANT] Als u een SSH-sleutel gebruikt om uw account te beveiligen, moet u de vorige stappen voltooien voordat u verbinding met de werkrolknooppunten kunt maken.

4. Open PuTTY.

5. Als u voor de verificatie een SSH-sleutel gebruikt, vouwt u in de sectie **Categorie** achtereenvolgens **Verbinding** en **SSH** uit en selecteert u **Auth**.

    Schakel in de sectie **Parameters voor verificatie** het selectievakje **Agentdoorsturing toestaan** in. Zodoende kan PuTTY automatisch de certificaatverificatie via de verbinding doorsturen naar het hoofdknooppunt van het cluster wanneer verbinding wordt gemaakt met de werkrolknooppunten.

    ![agentdoorsturing toestaan](./media/hdinsight-hadoop-linux-use-ssh-windows/allowforwarding.png)

6. Maak verbinding met het cluster zoals eerder beschreven. Als u een SSH-sleutel voor de verificatie gebruikt, hoeft u de sleutel niet te selecteren. De SSH-sleutel die is toegevoegd aan Pageant wordt gebruikt om het cluster te verifiëren.

7. Zodra er een verbinding tot stand is gebracht, gebruikt u de volgende opdracht om een lijst met knooppunten in uw cluster op te halen. Vervang *ADMINPASSWORD* door het wachtwoord voor uw cluster-beheeraccount. Vervang *CLUSTERNAME* door de naam van uw cluster.

        curl --user admin:ADMINPASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/hosts

    Zodoende wordt de informatie voor de knooppunten in het cluster geretourneerd in een JSON-indeling, inclusief `host_name`, die de fully qualified domain name (FQDN) voor elk knooppunt bevat. Hieronder volgt een voorbeeld van een `host_name`-item dat is geretourneerd met de opdracht **curl**:

        "host_name" : "workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net"

8. Zodra u een lijst hebt opgehaald met de werkrolknooppunten waarmee u verbinding wilt maken, gebruikt u de volgende opdracht in de PuTTY-sessie om een verbinding met een werkrolknooppunt te openen:

        ssh USERNAME@FQDN

    Vervang *USERNAME* door uw SSH-gebruikersnaam en *FQDN* door de FQDN voor het werkrolknooppunt. Bijvoorbeeld `workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net`.

    > [AZURE.NOTE] Als u een wachtwoord gebruikt voor de verificatie van uw SSH-sessie, wordt u gevraagd het wachtwoord opnieuw invoeren. Als u een SSH-sleutel gebruikt, wordt de verbinding voltooid zonder prompts.

9. Zodra de sessie actief is, verandert de prompt voor uw PuTTY-sessie van `username@hn0-clustername` in `username@wn0-clustername` om aan te geven dat u verbonden bent met het werkrolknooppunt. Alle opdrachten die u op dit moment uitvoert, worden uitgevoerd onder het werkrolknooppunt.

10. Zodra u de acties op het werkrolknooppunt hebt uitgevoerd, gebruikt u de opdracht `exit` om de sessie op het werkrolknooppunt te sluiten. Hiermee keert u terug naar de prompt `username@hn0-clustername`.

##Meer accounts toevoegen

Als u meer accounts aan uw cluster moet toevoegen, voert u de volgende stappen uit:

1. Genereer een nieuwe openbare en persoonlijke sleutel voor het nieuwe gebruikersaccount, zoals eerder beschreven.

2. Gebruik de volgende opdracht om tijdens een SSH-sessie op het cluster de nieuwe gebruiker toe te voegen:

        sudo adduser --disabled-password <username>

    Hiermee maakt u een nieuw gebruikersaccount, maar wordt wachtwoordverificatie uitgeschakeld.

3. Gebruik de volgende opdracht om de map en bestanden te maken waarin de sleutel moet worden bewaard:

        sudo mkdir -p /home/<username>/.ssh
        sudo touch /home/<username>/.ssh/authorized_keys
        sudo nano /home/<username>/.ssh/authorized_keys

4. Wanneer de nano-editor wordt geopend, kopieert en plakt u de inhoud van de openbare sleutel voor het nieuwe gebruikersaccount. Gebruik tot slot **Ctrl X** om het bestand op te slaan en de editor te sluiten.

    ![afbeelding van de nano-editor met een voorbeeldsleutel](./media/hdinsight-hadoop-linux-use-ssh-windows/nano.png)

5. Gebruik de volgende opdracht om het eigendom van de SSH-map en de inhoud ervan te wijzigen zodat het nieuwe gebruikersaccount de eigenaar wordt:

        sudo chown -hR <username>:<username> /home/<username>/.ssh

6. U kunt zich nu met het nieuwe gebruikersaccount en de nieuwe persoonlijke sleutel verifiëren bij de server.

##<a id="tunnel"></a>SSH-tunneling

SSH kan worden gebruikt voor de tunneling van lokale aanvragen, zoals webaanvragen, naar het HDInsight-cluster. De aanvraag wordt vervolgens naar de aangevraagde resource gerouteerd, alsof deze afkomstig is van het hoofdknooppunt van het HDInsight-cluster.

> [AZURE.IMPORTANT] Een SSH-tunnel is vereist voor toegang tot de webgebruikersinterface voor bepaalde Hadoop-services. De gebruikersinterface van bijvoorbeeld Taakgeschiedenis of Resource Manager is alleen toegankelijk via een SSH-tunnel.

Zie [SSH-tunneling gebruiken voor toegang tot de Ambari-webgebruikersinterface, ResourceManager JobHistory, NameNode, Oozie en andere webgebruikersinterfaces van ](hdinsight-linux-ambari-ssh-tunnel.md) voor meer informatie over het maken en gebruiken van een SSH-tunnel.

##Volgende stappen

Nu u weet hoe zich kunt verifiëren met een SSH-sleutel, wordt uitgelegd hoe u MapReduce gebruikt met Hadoop op HDInsight.

* [Hive gebruiken met HDInsight](hdinsight-use-hive.md)

* [Pig gebruiken met HDInsight](hdinsight-use-pig.md)

* [MapReduce-taken gebruiken met HDInsight](hdinsight-use-mapreduce.md)

[Preview Portal]: https://portal.azure.com/



<!--HONumber=Jun16_HO2-->


