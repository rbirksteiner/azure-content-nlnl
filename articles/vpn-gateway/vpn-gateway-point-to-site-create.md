<properties
   pageTitle="Een punt-naar-site-VPN-verbinding met een Azure-netwerk configureren met behulp van de klassieke portal | Microsoft Azure"
   description="U maakt een beveiligde verbinding met uw Azure Virtual Network door een punt-naar-site-VPN-verbinding te maken. Instructies voor VNets die zijn gemaakt met het Service Management-implementatiemodel (klassiek)."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/30/2016"
   ms.author="cherylmc"/>

# Een punt-naar-site-VPN-verbinding met een VNet configureren met behulp van de klassieke portal

> [AZURE.SELECTOR]
- [PowerShell - Resource Manager](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Portal - klassiek](vpn-gateway-point-to-site-create.md)

Met een punt-naar-site-configuratie kunt u vanaf elke clientcomputer afzonderlijk een beveiligde verbinding met het virtuele netwerk maken. Er wordt een VPN-verbinding tot stand gebracht door de verbinding te starten vanaf de clientcomputer. Punt-naar-site is een uitstekende oplossing als u uw VNet vanaf een externe locatie, zoals vanaf thuis of op een conferentie, wilt verbinden, of wanneer u slechts enkele clients hebt die verbinding moeten maken met een virtueel netwerk. 

Voor punt-naar-site-verbindingen hebt u geen VPN-apparaat of openbaar IP-adres nodig. Voor meer informatie over punt-naar-site-verbindingen leest u de [VPN Gateway FAQ](vpn-gateway-vpn-faq.md#point-to-site-connections) (Veelgestelde vragen over VPN-gateways) en [About cross-premises connections](vpn-gateway-cross-premises-options.md) (Over cross-premises verbindingen).

Dit artikel is van toepassing op punt-naar-site-VPN-gatewayverbindingen met een virtueel netwerk dat is gemaakt met het **klassieke implementatiemodel** (Service Management) en de klassieke portal. Wanneer we stappen hebben voor de Azure Portal, wordt er op deze pagina een koppeling naar dat artikel opgenomen.

**Implementatiemodellen en hulpmiddelen voor punt-naar-site-verbindingen**

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)] 


**Over Azure-implementatiemodellen**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

![Punt-naar-site-diagram](./media/vpn-gateway-point-to-site-create/point2site.png "point-to-site")



## Over het maken van een punt-naar-site-verbinding
 
De volgende stappen helpen u bij het maken van een beveiligde punt-naar-site-verbinding met een virtueel netwerk. Hoewel de configuratie van een punt-naar-site-verbinding uit meerdere stappen bestaat, is het een goede manier om een beveiligde verbinding tussen uw computer en het virtuele netwerk te verkrijgen zonder een VPN-apparaat te moeten aanschaffen en configureren. 

De configuratie van een punt-naar-site-verbinding is onderverdeeld in vier secties. De volgorde waarin u deze uitvoert is belangrijk. Sla dus geen stappen over en houd de volgorde aan.


- **Sectie 1** helpt u stapsgewijs bij het maken van een virtueel netwerk en de VPN-gateway.
- **Sectie 2** helpt u bij het maken van de certificaten die worden gebruikt voor verificatie en bij het uploaden ervan.
- **Sectie 3** helpt u bij de stappen voor het exporteren en installeren van de clientcertificaten.
- **Sectie 4** helpt u bij de stappen voor het configureren van uw VPN-client.

## Sectie 1: Een virtueel netwerk en een VPN-gateway maken


### Stap 1: Een virtueel netwerk maken

1. Meld u aan bij de [klassieke Azure Portal](https://manage.windowsazure.com/). In deze stappen wordt gebruik gemaakt van de klassieke portal, niet de Azure Portal.

2. Klik linksonder in het scherm op **Nieuw**. Klik in het navigatievenster op **Netwerkservices** en vervolgens op **Virtueel netwerk**. Klik op **Aangepast maken** om de configuratiewizard te starten.

3. Op de pagina **Details van virtueel netwerk** voert u de volgende informatie in en klikt u op de pijl Volgende in de rechterbenedenhoek.
    - **Naam**: geef het virtuele netwerk een naam. Bijvoorbeeld VNetEast. Dit is de naam die u gebruikt wanneer u virtuele machines en PaaS-instanties in dit VNet implementeert.
    - **Locatie**: de locatie is direct gerelateerd aan de fysieke locatie (regio) waar u wilt dat de resources (VM's) zich bevinden. Selecteer bijvoorbeeld VS - oost als u wilt dat de virtuele machines die u in dit virtuele netwerk implementeert zich fysiek bevinden in VS - oost. U kunt de regio die aan het virtuele netwerk is gekoppeld niet meer wijzigen wanneer het netwerk is gemaakt.

4. Op de pagina **DNS-servers en VPN-connectiviteit** voert u de volgende informatie in en klikt u op de pijl Volgende in de rechterbenedenhoek.
    - **DNS-servers**: geef de naam en het IP-adres van de DNS-server op of selecteer een eerder geregistreerde DNS-server in het snelmenu. Met deze instelling wordt geen DNS-server gemaakt. U kunt geeft hiermee alleen de DNS-servers op die u wilt gebruiken voor de naamomzetting voor dit virtuele netwerk. Laat deze sectie leeg als u de standaardservice voor naamomzetting van Azure wilt gebruiken.
    - **Punt-naar-site-VPN configureren**: schakel het selectievakje in.

5. Geef op de pagina **Punt-naar-site-connectiviteit** op uit welk IP-adresbereik uw VPN-clients een IP-adres ontvangen wanneer ze worden verbonden. Er zijn een paar regels met betrekking tot de adresbereiken die u kunt opgeven. Het is belangrijk dat u controleert of het bereik dat u opgeeft geen bereiken in het on-premises netwerk overlapt.

6. Voer de volgende informatie in en klik op de pijl Volgende.
 - **Adresruimte**: neem het IP-beginadres en de CIDR (aantal adressen) op.
 - **Adresruimte toevoegen**: voeg alleen een adresruimte toe als dit is vereist voor uw netwerkontwerp.

7. Op de pagina **Adresruimten voor virtueel netwerk** geeft u het adresbereik op dat u voor het virtuele netwerk wilt gebruiken. Dit zijn de dynamische IP-adressen (DIPS) die worden toegewezen aan de virtuele machines en andere rolinstanties die u in dit virtuele netwerk implementeert. Het is vooral van belang dat u een bereik selecteert dat niet overlapt met een van de bereiken die voor uw on-premises netwerk worden gebruikt. U moet hiervoor afstemmen met de netwerkbeheerder, die mogelijk een bereik van IP-adressen in de adresruimte van uw on-premises netwerk moet reserveren voor het virtuele netwerk.

8. Voer de volgende informatie in en klik op het vinkje om het virtuele netwerk te gaan maken.
 - **Adresruimte**: voeg het interne IP-adresbereik toe dat u voor dit virtuele netwerk wilt gebruiken, inclusief IP-beginadres en Aantal. Het is van belang dat u een bereik selecteert dat niet overlapt met een van de bereiken die voor uw on-premises netwerk worden gebruikt. U moet hiervoor afstemmen met de netwerkbeheerder, die mogelijk een bereik van IP-adressen in de adresruimte van uw on-premises netwerk moet reserveren voor het virtuele netwerk.
 - **Subnet toevoegen**: u hebt geen aanvullende subnetten nodig, maar misschien wilt u een apart subnet maken voor virtuele machines die statische DIPS krijgen. Of misschien wilt u uw virtuele machines in een subnet plaatsen dat is gescheiden van uw andere rolinstanties.
 - **Gatewaysubnet toevoegen**: het gatewaysubnet is vereist voor een punt-naar-site-VPN-verbinding. Klik om het gatewaysubnet toe te voegen. Het gatewaysubnet wordt alleen gebruikt voor de gateway van het virtueel netwerk.

9. Wanneer het virtuele netwerk is gemaakt, wordt op de pagina met netwerken in de klassieke Azure Portal **Gemaakt** vermeld onder **Status**. Wanneer het virtuele netwerk is gemaakt, kunt u de gateway voor dynamische routering maken.

### Deel 2: Een gateway voor dynamische routering maken

Het gatewaytype moet worden geconfigureerd als Dynamisch. Statische routeringsgateways werken niet met deze functie.

1. Ga in de klassieke Azure Portal naar de pagina **Netwerken** en klik op het virtuele netwerk dat u zojuist hebt gemaakt. Navigeer dan naar de **Dashboard**-pagina.

2. Klik in de rechterbenedenhoek van de **Dashboard**pagina op **Gateway maken**. Er verschijnt een bericht met de vraag **Wilt u een gateway maken voor virtueel netwerk 'uwnetwerk'**. Klik op **Ja** om de gateway te gaan maken. Het kan ongeveer vijftien minuten duren voordat de gateway is gemaakt.

## Sectie 2: Certificaten genereren en uploaden

Certificaten worden gebruikt om VPN-clients voor punt-naar-site-VPN's te verifiëren. U kunt certificaten gebruiken die zijn gegenereerd door een commerciële certificeringsoplossing, maar u kunt ook zelfondertekende certificaten gebruiken. U kunt maximaal twintig basiscertificaten uploaden naar Azure.

Voer de volgende stappen uit als u een zelfondertekend certificaat wilt gebruiken. Als u een commerciële certificeringsoplossing wilt gebruiken, zijn de stappen in elke sectie anders. De volgende stappen dient u echter alsnog uit te voeren:

### Deel 1: Een basiscertificaat identificeren of genereren

Als u geen commerciële certificeringsoplossing gebruikt, moet u een zelfondertekend basiscertificaat genereren. De stappen in deze sectie zijn geschreven voor Windows 8. Voor de stappen in Windows 10 raadpleegt u [Working with self-signed root certificates for Point-to-Site configurations](vpn-gateway-certificates-point-to-site.md) (Werken met zelfondertekende basiscertificaten voor punt-naar-site-configuraties).

Eén manier om een X.509-certificaat te maken, is met het hulpprogramma voor het maken van certificaten (makecert.exe). Als u makecert wilt gebruiken, downloadt en installeert u [Microsoft Visual Studio Express](https://www.visualstudio.com/products/visual-studio-express-vs.aspx). Dit is gratis.

1. Navigeer naar de map Visual Studio Tools en start de opdrachtprompt als beheerder.

2. Met de opdracht in het volgende voorbeeld wordt een basiscertificaat in het persoonlijke certificaatarchief op uw computer gemaakt en geïnstalleerd en wordt ook een bijbehorend *CER*-bestand gemaakt dat u later naar de klassieke Azure Portal gaat uploaden.

3. Ga naar de map waarin u het CER-bestand wilt plaatsen en voer de volgende opdracht uit, waarbij *RootCertificateName* de naam is die u voor het certificaat wilt gebruiken. Als u het volgende voorbeeld zonder wijzigingen uitvoert, is het resultaat een basiscertificaat en het bijbehorende bestand *RootCertificateName.cer*.

>[AZURE.NOTE] Omdat u een basiscertificaat hebt gemaakt op basis waarvan clientcertificaten worden gegenereerd, wilt u dit certificaat misschien samen met de persoonlijke sleutel exporteren en opslaan op een veilige locatie vanaf waar deze kunnen worden hersteld.

    makecert -sky exchange -r -n "CN=RootCertificateName" -pe -a sha1 -len 2048 -ss My "RootCertificateName.cer"

### Deel 2: Het CER-bestand van het basiscertificaat uploaden naar de klassieke Azure Portal

U moet het CER-bestand van elk basiscertificaat uploaden naar Azure. U kunt maximaal twintig certificaten uploaden.

1. Als u in een vorige procedure een basiscertificaat hebt gemaakt, hebt u ook een *CER*-bestand gemaakt. U gaat dat bestand nu uploaden naar de klassieke Azure Portal. Het CER-bestand bevat niet de persoonlijke sleutel van het basiscertificaat. U kunt maximaal twintig basiscertificaten uploaden.

2. Ga in de klassieke Azure Portal naar de pagina **Certificaten** voor het virtuele netwerk en klik op **Een basiscertificaat uploaden**.

3. Blader op de pagina **Certificaat uploaden** naar het CER-basiscertificaat en klik op het vinkje.

### Deel 3: Een clientcertificaat genereren

Met de stappen hieronder gaat u een clientcertificaat genereren uit het zelfondertekende basiscertificaat. De stappen in deze sectie zijn geschreven voor Windows 8. Voor de stappen in Windows 10 raadpleegt u [Working with self-signed root certificates for Point-to-Site configurations](vpn-gateway-certificates-point-to-site.md) (Werken met zelfondertekende basiscertificaten voor punt-naar-site-configuraties). Als u een commerciële certificeringsoplossing gebruikt, volg dan de richtlijnen voor de betreffende oplossing. 

1. Open als beheerder een Visual Studio-opdrachtpromptvenster op de computer waarmee u ook het zelfondertekende basiscertificaat hebt gemaakt.

2. Ga naar de map waarin u het clientcertificaatbestand wilt opslaan. *RootCertificateName* verwijst naar het zelfondertekende basiscertificaat dat u hebt gegenereerd. Als u het volgende voorbeeld uitvoert (waarbij u RootCertificateName hebt vervangen door de naam van uw basiscertificaat), is het resultaat een certificaat met de naam 'ClientCertificateName' in uw persoonlijke certificaatarchief.

3. Typ de volgende opdracht:

        makecert.exe -n "CN=ClientCertificateName" -pe -sky exchange -m 96 -ss My -in "RootCertificateName" -is my -a sha1

4. Alle certificaten worden opgeslagen in het persoonlijke certificaatarchief op uw computer. Controleer *certmgr* om dit te controleren. Met deze procedure kunt u net zoveel certificaten genereren als nodig is. U wordt geadviseerd om unieke clientcertificaten te maken voor elke computer die u wilt verbinden met het virtuele netwerk.

## Sectie 3: Het clientcertificaat exporteren en installeren

Het installeren van een certificaat op elke computer die u wilt verbinden met het virtueel netwerk is een verplichte stap. Hieronder vindt u de stappen voor het handmatig installeren van het clientcertificaat.

1. U moet een clientcertificaat installeren op elke computer die u met het virtueel netwerk wilt verbinden. Dat betekent dat u waarschijnlijk meerdere clientcertificaten moet maken en deze vervolgens moet exporteren. U gebruikt *certmgr.msc* om de clientcertificaten te exporteren. Klik met de rechtermuisknop op het clientcertificaat dat u wilt exporteren, klik op **Alle taken** en vervolgens op **Exporteren**.
2. Exporteer het *clientcertificaat* met de persoonlijke sleutel. Dit is een *PFX*-bestand. Zorg dat u het wachtwoord (sleutel) dat u voor dit certificaat instelt, ergens noteert of onthoudt.
3. Kopieer het *PFX*-bestand naar de clientcomputer. Dubbelklik op de clientcomputer op het *PFX*-bestand om het te installeren. Voer het wachtwoord in wanneer dit wordt aangevraagd. Wijzig de installatielocatie niet.

## Sectie 4: De VPN-client configureren

U moet uw VPN-client ook configureren om verbinding te maken met het virtuele netwerk. De client vereist zowel een clientcertificaat als een correcte configuratie van de VPN-client om verbinding te kunnen maken. Voer de volgende stappen in de gegeven volgorde uit om de VPN-client te configureren.

### Deel 1: Het configuratiepakket voor de VPN-client maken

1. Ga in de klassieke Azure Portal naar de **Dashboard**-pagina voor het virtuele netwerk, navigeer naar het menu Snelle weergave in de rechterbovenhoek en klik op het VPN-pakket voor de client die u met het virtuele netwerk wilt verbinden.

2. 
De volgende clientbesturingssystemen worden ondersteund:
 - Windows 7 (32-bits en 64-bits)
 - Windows Server 2008 R2 (alleen 64-bits)
 - Windows 8 (32-bits en 64-bits)
 - Windows 8.1 (32-bits en 64-bits)
 - Windows Server 2012 (alleen 64-bits)
 - Windows Server 2012 R2 (alleen 64-bits)
 - Windows 10

3. Selecteer het downloadpakket dat overeenkomt met het besturingssysteem van de client waarop het wordt geïnstalleerd:
 - Voor 32-bits clients selecteert u **Het 32-bits VPN-clientpakket downloaden**.
 - Voor 64-bits clients selecteert u **Het 64-bits VPN-clientpakket downloaden**.

4. Het kan enkele minuten duren voordat het clientpakket is gemaakt. Wanneer het pakket is voltooid, kunt u het bestand downloaden. Het *EXE*-bestand dat u hebt gedownload kan veilig worden opgeslagen op de lokale computer.

5. Nadat u het VPN-clientpakket via de klassieke Azure Portal hebt gegenereerd en geïnstalleerd, kunt u het installeren op de clientcomputer van waaruit u verbinding wilt maken met het virtuele netwerk. Als u het VPN-clientpakket op meerdere clientcomputers wilt installeren, moet op elke computer een clientcertificaat worden geïnstalleerd. Het VPN-clientpakket bevat configuratiegegevens voor de configuratie van de VPN-clientsoftware die is ingebouwd in Windows. Met het pakket wordt geen andere software geïnstalleerd.

### Deel 2: Het VPN-configuratiepakket installeren op de client en de verbinding starten

1. Kopieer het configuratiebestand lokaal op de computer die u wilt verbinden met het virtuele netwerk en dubbelklik op het EXE-bestand. Wanneer het pakket is geïnstalleerd, kunt u de VPN-verbinding starten.
Het configuratiepakket is niet ondertekend door Microsoft. U kunt het pakket desgewenst laten ondertekenen door de ondertekeningsservice van uw organisatie of het zelf ondertekenen met behulp van [SignTool]( http://go.microsoft.com/fwlink/p/?LinkId=699327). U kunt het pakket echter gebruiken zonder het te ondertekenen. Als het pakket niet is ondertekend, wordt echter een waarschuwing weergegeven wanneer u het pakket installeert.
2. Ga op de clientcomputer naar de VPN-verbindingen en zoek de VPN-verbinding die u zojuist hebt gemaakt. Deze heeft dezelfde naam als het virtuele netwerk. Klik op **Verbinden**.
3. Er verschijnt een pop-upbericht dat wordt gebruikt voor het maken van een zelfondertekend certificaat voor het eindpunt van de gateway. Klik op **Doorgaan** om verhoogde bevoegdheden te gebruiken.
4. Klik op de pagina **Verbindingsstatus** op **Verbinden** om de verbinding te starten.
5. Als het scherm **Certificaat selecteren** wordt geopend, controleert u of het weergegeven clientcertificaat het certificaat is dat u voor de verbinding wilt gebruiken. Als dat niet het geval is, gebruikt u de pijl-omlaag om het juiste certificaat te selecteren en klikt u op **OK**.
6. U hebt nu verbinding met het virtuele netwerk en u hebt volledige toegang tot elke service en virtuele machine die in het virtuele netwerk wordt gehost.

### Deel 3: De VPN-verbinding controleren

1. Als u wilt controleren of uw VPN-verbinding actief is, opent u een opdrachtprompt met verhoogde bevoegdheid en voert u *ipconfig/all* in.
2. Bekijk de resultaten. Het IP-adres dat u hebt ontvangen is een van de adressen binnen het adresbereik van de punt-naar-site-verbinding dat u hebt opgegeven tijdens het maken van het VNet. De resultaten moeten er ongeveer als volgt uitzien:

Voorbeeld:



    PPP adapter VNetEast:
        Connection-specific DNS Suffix .:
        Description.....................: VNetEast
        Physical Address................:
        DHCP Enabled....................: No
        Autoconfiguration Enabled.......: Yes
        IPv4 Address....................: 192.168.130.2(Preferred)
        Subnet Mask.....................: 255.255.255.255
        Default Gateway.................:
        NetBIOS over Tcpip..............: Enabled

## Volgende stappen

U kunt virtuele machines toevoegen aan het virtuele netwerk. Zie [How to create a custom virtual machine](../virtual-machines/virtual-machines-windows-classic-createportal.md) (Een aangepaste virtuele machine maken).

Bekijk de pagina [Virtual Network Documentation](https://azure.microsoft.com/documentation/services/virtual-network/) (Virtual Network-documentatie) voor meer informatie over Virtual Networks.



<!--HONumber=Jun16_HO2-->


