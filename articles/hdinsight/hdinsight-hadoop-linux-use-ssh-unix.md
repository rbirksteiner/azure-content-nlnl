<properties
   pageTitle="SSH-sleutels gebruiken voor Hadoop (gebaseerd op Linux) in Linux, Unix of OS X | Microsoft Azure"
   description=" U kunt met Secure Shell (SSH) HDInsight (gebaseerd op Linux) openen. Dit document bevat informatie over het gebruik van SSH met HDInsight op Linux-, Unix- of OS X-clients."
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

#SSH gebruiken met Hadoop op basis van Linux in HDInsight via Linux, Unix of OS X

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
- [Linux, Unix, OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

Met [Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) kunt u via de opdrachtregelinterface op afstand bewerkingen uitvoeren op uw HDInsight-clusters op basis van Linux. Dit document bevat informatie over het gebruik van SSH met HDInsight op Linux-, Unix- of OS X-clients.

> [AZURE.NOTE] In de stappen in dit artikel wordt ervan uitgegaan dat u een Linux-, Unix- of OS X-client gebruikt. Hoewel deze stappen kunnen worden uitgevoerd op een Windows-client als u een pakket hebt geïnstalleerd dat `ssh` en `ssh-keygen` biedt (zoals Git voor Windows), kunt u voor Windows-clients het beste de stappen in [SSH gebruiken met HDInsight (Hadoop) op basis van Linux via Windows](hdinsight-hadoop-linux-use-ssh-windows.md) volgen.

##Vereisten

* **ssh-keygen** en **ssh** voor Linux-, Unix- en OS X-clients. Deze hulpprogramma's worden doorgaans bij uw besturingssysteem geleverd of zijn beschikbaar via het pakketbeheersysteem.

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

1. Open een terminalsessie en gebruik de volgende opdracht om te controleren of u bestaande SSH-sleutels hebt:

        ls -al ~/.ssh

    Zoek naar de volgende bestanden in de lijst met mappen. Dit zijn algemene namen voor openbare SSH-sleutels.

    * id\_dsa.pub
    * id\_ecdsa.pub
    * id\_ed25519.pub
    * id\_rsa.pub

2. Als u geen bestaand bestand wilt gebruiken of als u geen bestaande SSH-sleutels hebt, gebruikt u de volgende opdracht om een nieuw bestand te genereren:

        ssh-keygen -t rsa

    U wordt gevraagd naar de volgende informatie:

    * De bestandslocatie: de standaardlocatie is ~/.ssh/id\_rsa.
    * Een wachtwoordzin: u wordt gevraagd deze opnieuw op te geven.

        > [AZURE.NOTE] U kunt het best een beveiligde wachtwoordzin voor de sleutel gebruiken. Als u de wachtwoordzin vergeet, is er echter geen manier om deze te herstellen.

    Zodra de opdracht is uitgevoerd, beschikt u over twee nieuwe bestanden: de persoonlijke sleutel (bijvoorbeeld **id\_rsa**) en de openbare sleutel (bijvoorbeeld **id\_rsa.pub**).

##Een HDInsight-cluster op basis van Linux maken

Wanneer u een op Linux gebaseerd HDInsight-cluster maakt, moet u de openbare sleutel opgeven die eerder is gemaakt. Er zijn twee manieren om een HDInsight-cluster te maken via Linux-, Unix- of OS X-clients:

* **Azure Portal**: maakt gebruik van een webportal om het cluster te maken.

* **Azure CLI voor Mac-, Linux- en Windows**: maakt gebruik van opdrachtregelopdrachten om het cluster te maken.

Voor elk van deze methoden is een wachtwoord of een openbare sleutel vereist. Zie [Op Linux gebaseerde HDInsight-clusters inrichten](hdinsight-hadoop-provision-linux-clusters.md) voor de volledige informatie over het maken van een HDInsight-cluster op basis van Linux.

###Azure Portal

Wanneer u de [Azure Portal][Preview Portal] gebruikt om een HDInsight-cluster op basis van Linux te maken, moet u een **SSH-GEBRUIKERSNAAM** opgeven en kiest u ervoor om een **WACHTWOORD** of **OPENBARE SSH-SLEUTEL** op te geven.

Als u **OPENBARE SSH-SLEUTEL** selecteert, kunt u de openbare sleutel (in het bestand met de extensie **.pub**) in het veld __Openbare SSH-sleutel__ plakken of __Selecteer een bestand__ selecteren om te bladeren en het bestand met de openbare sleutel te selecteren.

![Afbeelding van een formulier waarin naar de openbare sleutel wordt gevraagd.](./media/hdinsight-hadoop-linux-use-ssh-unix/ssh-key.png)

> [AZURE.NOTE] Het sleutelbestand is een tekstbestand. De inhoud ziet er ongeveer als volgt uit:
> ```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCelfkjrpYHYiks4TM+r1LVsTYQ4jAXXGeOAF9Vv/KGz90pgMk3VRJk4PEUSELfXKxP3NtsVwLVPN1l09utI/tKHQ6WL3qy89WVVVLiwzL7tfJ2B08Gmcw8mC/YoieT/YG+4I4oAgPEmim+6/F9S0lU2I2CuFBX9JzauX8n1Y9kWzTARST+ERx2hysyA5ObLv97Xe4C2CQvGE01LGAXkw2ffP9vI+emUM+VeYrf0q3w/b1o/COKbFVZ2IpEcJ8G2SLlNsHWXofWhOKQRi64TMxT7LLoohD61q2aWNKdaE4oQdiuo8TGnt4zWLEPjzjIYIEIZGk00HiQD+KCB5pxoVtp user@system
> ```

Hiermee maakt u een aanmelding voor de opgegeven gebruiker door gebruik te maken van het wachtwoord of de openbare sleutel die u opgeeft.

###Azure-opdrachtregelinterface voor Mac, Linux en Windows

U kunt de [Azure CLI voor Mac, Linux en Windows](../xplat-cli-install.md) gebruiken om een nieuw cluster met de opdracht `azure hdinsight cluster create` te maken.

Zie [Hadoop Linux-clusters in HDInsight inrichten met aangepaste opties](hdinsight-hadoop-provision-linux-clusters.md) voor meer informatie over het gebruik van deze opdracht.

##Verbinding maken met een HDInsight-cluster dat is gebaseerd op Linux

Open een terminalsessie en gebruik de SSH-opdracht om verbinding met het hoofdknooppunt van het cluster te maken door het adres en de gebruikersnaam op te geven:

* **SSH-adres**: - Er zijn twee adressen die kunnen worden gebruikt om verbinding met een cluster te maken via SSH:

    * **Verbinding maken met het hoofdknooppunt**: de naam van het cluster gevolgd door **-ssh.azurehdinsight.net**. Bijvoorbeeld **mijncluster-ssh.azurehdinsight.net**.
    
    * **Verbinding maken met het edge-knooppunt**: als uw cluster zich op HDInsight R Server bevindt, bevat het cluster ook een edge-knooppunt dat toegankelijk is met **RServer.CLUSTERNAME.ssh.azurehdinsight.net**, waarbij __CLUSTERNAME__ de naam van het cluster is.

* **Gebruikersnaam**: de SSH-gebruikersnaam die u hebt opgegeven toen u het cluster hebt gemaakt.

In het volgende voorbeeld maakt de gebruiker **me** verbinding met het hoofdknooppunt 0 van **mijncluster**:

    ssh me@mycluster-ssh.azurehdinsight.net

Als u een wachtwoord voor het gebruikersaccount hebt gebruikt, wordt u gevraagd het wachtwoord in te voeren.

Als u een SSH-sleutel hebt gebruikt die is beveiligd met een wachtwoordzin, wordt u gevraagd de wachtwoordzin op te geven. Anders probeert SSH automatisch te verifiëren met een van de lokale persoonlijke sleutels op de client.

> [AZURE.NOTE] Als SSH niet automatisch een verificatie met de juiste persoonlijk sleutel uitvoert, gebruikt u de parameter **-i** en geeft u het pad voor de persoonlijke sleutel op. In het volgende voorbeeld wordt de persoonlijke sleutel uit `~/.ssh/id_rsa` geladen:
>
> `ssh -i ~/.ssh/id_rsa me@mycluster-ssh.azurehdinsight.net`

Als u verbinding maakt met het adres voor het hoofdknooppunt en er geen poort is opgegeven, gebruikt SSH standaard poort 22, waarmee verbinding wordt gemaakt met het hoofdknooppunt 0 op het HDInsight-cluster. Als u poort 23 gebruikt, maakt u verbinding met hoofdknooppunt 1. Zie [Beschikbaarheid en betrouwbaarheid van Hadoop-clusters in HDInsight](hdinsight-high-availability-linux.md) voor meer informatie over de hoofdknooppunten.

###Verbinding maken met werkrolknooppunten

De werkrolknooppunten zijn niet rechtstreeks toegankelijk van buiten het Azure-datacentrum, maar ze zijn wel via SSH toegankelijk vanaf het hoofdknooppunt van het cluster.

Als u een SSH-sleutel gebruikt om uw gebruikersaccount te verifiëren, moet u de volgende stappen uitvoeren op de client:

1. Start een teksteditor en open `~/.ssh/config`. Als dit bestand niet bestaat, kunt u dit maken door `touch ~/.ssh/config` in de terminal in te voeren.

2. Voeg het volgende toe aan het bestand. Vervang *CLUSTERNAME* door de naam van uw HDInsight-cluster.

        Host CLUSTERNAME-ssh.azurehdinsight.net
          ForwardAgent yes

    Hiermee configureert u het doorsturen van de SSH-agent voor uw HDInsight-cluster.

3. Test het doorsturen van de SSH-agent met de volgende opdracht vanaf de terminal:

        echo "$SSH_AUTH_SOCK"

    De geretourneerde informatie moet er ongeveer als volgt uitzien:

        /tmp/ssh-rfSUL1ldCldQ/agent.1792

    Als er niets wordt geretourneerd, betekent dit dat de **ssh-agent** niet wordt uitgevoerd. Raadpleeg de documentatie van uw besturingssysteem voor specifieke stappen over het installeren en configureren van **ssh-agent** of raadpleeg [Ssh-agent met SSH gebruiken](http://mah.everybody.org/docs/ssh).

4. Zodra u hebt gecontroleerd of **ssh-agent** wordt uitgevoerd, gebruikt u de volgende opdracht om uw persoonlijke SSH-sleutel toe te voegen aan de agent:

        ssh-add ~/.ssh/id_rsa

    Als uw persoonlijke sleutel wordt opgeslagen in een ander bestand, vervangt u `~/.ssh/id_rsa` door het pad naar het bestand.

Gebruik de volgende stappen om verbinding te maken met de werkrolknooppunten voor uw cluster.

> [AZURE.IMPORTANT] Als u een SSH-sleutel gebruikt om uw account te verifiëren, moet u de vorige stappen voltooien om te controleren of agentdoorsturing werkt.

1. Maak verbinding met het HDInsight-cluster door SSH te gebruiken zoals hierboven wordt beschreven.

2. Zodra u verbinding hebt, gebruikt u de volgende opdracht om een lijst met knooppunten in uw cluster op te halen. Vervang *ADMINPASSWORD* door het wachtwoord voor uw cluster-beheeraccount. Vervang *CLUSTERNAME* door de naam van uw cluster.

        curl --user admin:ADMINPASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/hosts

    Zodoende wordt de informatie voor de knooppunten in het cluster geretourneerd in een JSON-indeling, inclusief `host_name`, die de fully qualified domain name (FQDN) voor elk knooppunt bevat. Hieronder volgt een voorbeeld van een `host_name`-item dat is geretourneerd met de opdracht **curl**:

        "host_name" : "workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net"

3. Zodra u een lijst hebt opgehaald met de werkrolknooppunten waarmee u verbinding wilt maken, gebruikt u de volgende opdracht in de SSH-sessie op de server om een verbinding met een werkrolknooppunt te openen:

        ssh USERNAME@FQDN

    Vervang *USERNAME* door uw SSH-gebruikersnaam en *FQDN* door de FQDN voor het werkrolknooppunt. Bijvoorbeeld `workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net`.

    > [AZURE.NOTE] Als u een wachtwoord gebruikt voor de verificatie van uw SSH-sessie, wordt u gevraagd het wachtwoord opnieuw invoeren. Als u een SSH-sleutel gebruikt, wordt de verbinding voltooid zonder prompts.

4. Zodra de sessie actief is, verandert de terminalprompt van `username@hn0-clustername` in `username@wk0-clustername` om aan te geven dat verbonden bent met het werkrolknooppunt. Alle opdrachten die u op dit moment uitvoert, worden uitgevoerd onder het werkrolknooppunt.

4. Zodra u de acties op het werkrolknooppunt hebt uitgevoerd, gebruikt u de opdracht `exit` om de sessie op het werkrolknooppunt te sluiten. Hiermee keert u terug naar de prompt `username@hn0-clustername`.

##Meer accounts toevoegen

1. Genereer een nieuwe openbare en persoonlijke sleutel voor het nieuwe gebruikersaccount, zoals beschreven in de sectie [Een SSH-sleutel maken](#create-an-ssh-key-optional).

    > [AZURE.NOTE] De persoonlijke sleutel moet worden gegenereerd op een client die de gebruiker gebruikt om verbinding met het cluster te maken of, nadat de sleutel is gemaakt, veilig worden overgedragen naar een dergelijke client.

1. Gebruik de volgende opdracht om tijdens een SSH-sessie op het cluster de nieuwe gebruiker toe te voegen:

        sudo adduser --disabled-password <username>

    Hiermee maakt u een nieuw gebruikersaccount, maar wordt wachtwoordverificatie uitgeschakeld.

2. Gebruik de volgende opdracht om de map en bestanden te maken waarin de sleutel moet worden bewaard:

        sudo mkdir -p /home/<username>/.ssh
        sudo touch /home/<username>/.ssh/authorized_keys
        sudo nano /home/<username>/.ssh/authorized_keys

3. Wanneer de nano-editor wordt geopend, kopieert en plakt u de inhoud van de openbare sleutel voor het nieuwe gebruikersaccount. Gebruik tot slot **Ctrl X** om het bestand op te slaan en de editor te sluiten.

    ![afbeelding van de nano-editor met een voorbeeldsleutel](./media/hdinsight-hadoop-linux-use-ssh-unix/nano.png)

4. Gebruik de volgende opdracht om het eigendom van de SSH-map en de inhoud ervan te wijzigen zodat het nieuwe gebruikersaccount de eigenaar wordt:

        sudo chown -hR <username>:<username> /home/<username>/.ssh

5. U kunt zich nu met het nieuwe gebruikersaccount en de nieuwe persoonlijke sleutel verifiëren bij de server.

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


