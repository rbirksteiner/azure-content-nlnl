<properties 
   pageTitle="Wat is een netwerkbeveiligingsgroep (NSG)"
   description="Meer informatie over de gedistribueerde firewall in Azure met netwerkbeveiligingsgroepen (NSG's) en hoe u NSGs gebruikt om verkeer binnen uw virtuele netwerken (VNET'S) te isoleren en beheren."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/11/2016"
   ms.author="telmos" />

# Wat is een netwerkbeveiligingsgroep (NSG)?

Een netwerkbeveiligingsgroep (NSG) bevat een lijst met ACL-regels (Access Control List, toegangsbeheerlijst) waarmee netwerkverkeer voor uw VM-exemplaren in een virtueel netwerk wordt toegestaan of geweigerd. NSG's kunnen worden gekoppeld aan subnetten of afzonderlijke VM-exemplaren in dat subnet. Als een NSG is gekoppeld aan een subnet, zijn de ACL-regels van toepassing op alle VM-exemplaren in dat subnet. U kunt het verkeer naar een afzonderlijke VM nog verder beperken door een NSG rechtstreeks aan die VM te koppelen.

## NSG-resource

NSG's bevatten de volgende eigenschappen.

|Eigenschap|Beschrijving|Beperkingen|Overwegingen|
|---|---|---|---|
|Naam|Naam voor de NSG|Moet uniek zijn binnen de regio<br/>Kan letters, cijfers, onderstrepingstekens, punten en afbreekstreepjes bevatten<br/>Moet beginnen met een letter of cijfer<br/>Moet eindigen met een letter, cijfer of onderstrepingsteken<br/>Is niet langer dan 80 tekens|Omdat u mogelijk verschillende NSG's moet maken, is het raadzaam namen te gebruiken die aangeven wat het doel van de NSG's is|
|Regio|Azure-regio waar de NSG wordt gehost|NSG's kunnen alleen worden toegepast op resources binnen de regio waarin deze zijn gemaakt|Zie [Limieten](#Limits) verderop in dit document voor informatie over het maximum aantal NSG's per regio|
|Resourcegroep|Resourcegroep waartoe de NSG behoort|Hoewel een NSG tot een resourcegroep behoort, kan deze worden gekoppeld aan resources in elke willekeurige resourcegroep, zolang de resouce maar tot dezelfde Azure-regio als de NSG behoort|Resourcegroepen worden gebruikt voor het beheren van meerdere resources tegelijk, als een implementatie-eenheid<br/>U kunt de NSG desgewenst groeperen met resources waaraan deze is gekoppeld|
|Regels|Regels die bepalen welk verkeer wordt toegestaan of geweigerd||Zie [NSG-regels](#Nsg-rules) verderop in document| 

>[AZURE.NOTE] ACL's voor eindpunten en netwerkbeveiligingsgroepen worden niet ondersteund op een en hetzelfde VM-exemplaar. Als u een NSG wilt gebruiken en er al een ACL voor eindpunten is geïmplementeerd, moet u eerst de ACL voor eindpunten verwijderen. Zie voor informatie over hoe u dit doet, [Toegangsbeheerlijsten (ACL's) voor eindpunten beheren met PowerShell](virtual-networks-acl-powershell.md).

### NSG-regels

NSG-regels bevatten de volgende eigenschappen.

|Eigenschap|Beschrijving|Beperkingen|Overwegingen|
|---|---|---|---|
|**Naam**|Naam voor de regel|Moet uniek zijn binnen de regio<br/>Kan letters, cijfers, onderstrepingstekens, punten en afbreekstreepjes bevatten<br/>Moet beginnen met een letter of cijfer<br/>Moet eindigen met een letter, cijfer of onderstrepingsteken<br/>Is niet langer dan 80 tekens|U kunt meerdere regels binnen een NSG hebben, dus zorg ervoor dat u duidelijk herkenbare namen gebruikt die het doel van de regel aangeven|
|**Protocol**|Te matchen protocol voor de regel|TCP, UDP of \*|Als u \* als protocol gebruikt, omvat dit zowel ICMP (alleen oost-west-verkeer), als UDP en TCP en hebt u wellicht minder regels nodig<br/>Tegelijkertijd is het gebruik van \* mogelijk niet specifiek genoeg, dus gebruik deze optie alleen als dit echt nodig is|
|**Poortbereik van bron**|Te matchen bronpoortbereik voor de regel|Een poortnummer tussen 1 en 65535, een poortbereik (bijvoorbeeld 1-65635) of \* (voor alle poorten)|Bronpoorten kunnen kortstondig zijn. Tenzij in uw clientprogramma een specifieke poort wordt gebruikt, gebruikt u in de meeste gevallen '*'.<br/>Gebruik zo veel mogelijk poortbereiken om te voorkomen dat u meerdere regels nodig hebt<br/>Meerdere poorten of poortbereiken kunnen niet worden gegroepeerd met komma's
|**Poortbereik van doel**|Te matchen doelpoortbereik voor de regel|Een poortnummer tussen 1 en 65535, een poortbereik (bijvoorbeeld 1-65535) of \* (voor alle poorten)|Gebruik zo veel mogelijk poortbereiken om te voorkomen dat u meerdere regels nodig hebt<br/>Meerdere poorten of poortbereiken kunnen niet worden gegroepeerd met komma's
|**Voorvoegsel voor bronadres**|Te matchen bronadresvoorvoegsel of tag voor de regel|Een IP-adres (bijvoorbeeld 10.10.10.10), IP-subnet (bijvoorbeeld 192.168.1.0/24), [standaardtag](#Default-Tags) of * (voor alle adressen)|Overweeg het gebruik van bereiken, standaardtags en * om het aantal regels te verminderen|
|**Voorvoegsel voor doeladres**|Te matchen doeladresvoorvoegsel of tag voor de regel|een IP-adres (bijvoorbeeld 10.10.10.10), IP-subnet (bijvoorbeeld 192.168.1.0/24), [standaardtag](#Default-Tags) of * (voor alle adressen)|Overweeg het gebruik van bereiken, standaardtags en * om het aantal regels te verminderen|
|**Richting**|Te matchen richting van verkeer voor de regel|binnenkomend of uitgaand|Regels voor binnenkomend en uitgaand verkeer worden afzonderlijk verwerkt op basis van de richting|
|**Prioriteit**|Regels worden gecontroleerd op volgorde van prioriteit en als een regel van toepassing is, worden verder geen regels meer getest voor matching|Getal tussen 100 en 4096|Overweeg prioriteitsnummers voor regels te laten verspringen met 100, om ruimte voor nieuwe regels over te laten tussen bestaande regels in|
|**Toegang**|Toe te passen type toegang als de regel matcht|toestaan of weigeren|Als er geen regel voor het toestaan van een pakket wordt gevonden, wordt het pakket verwijderd|

NSG's bevatten twee sets met regels: een set met regels voor binnenkomend verkeer en een set met regels voor uitgaand verkeer. De prioriteit voor een regel moet uniek zijn binnen elke set. 

![Verwerking van NSG-regels](./media/virtual-network-nsg-overview/figure3.png) 

In de bovenstaande afbeelding ziet u hoe NSG-regels worden verwerkt.

### Standaardtags

Standaardtags zijn systeem-id's voor een bepaalde categorie IP-adressen. U kunt standaardtags gebruiken in de eigenschappen voor het **voorvoegsel voor het bronadres** en het **voorvoegsel voor het doeladres** van een regel. Er zijn drie standaardtags die u kunt gebruiken.

- **VIRTUAL_NETWORK:** met deze standaardtag worden alle adresruimten van uw netwerk aangeduid. Dit omvat de adresruimte van het virtuele netwerk (CIDR-bereiken die in Azure zijn gedefinieerd) en alle verbonden on-premises adresruimten en verbonden Azure VNET'S (lokale netwerken).

- **AZURE_LOADBALANCER:** met deze standaardtag wordt de load balancer voor de infrastructuur van Azure aangeduid. Dit wordt vertaald naar het IP-adres van een datacenter van Azure vanwaar statuscontroles van Azure worden uitgevoerd.

- **INTERNET:** met deze standaardtag wordt de IP-adresruimte aangeduid die zich buiten het virtuele netwerk bevindt en bereikbaar is via internet. Dit bereik omvat ook [openbare IP-adresruimten van Azure](https://www.microsoft.com/download/details.aspx?id=41653).

### Standaardregels

Alle NSG's bevatten een set met standaardregels. De standaardregels kunnen niet worden verwijderd, maar omdat ze de laagste prioriteit hebben, kunnen ze worden overschreven door de regels die u maakt. 

Zoals u kunt zien in de onderstaande standaardregels, wordt verkeer van of naar een virtueel netwerk zowel toegestaan in binnenkomende als uitgaande richting. Hoewel connectiviteit met internet wordt toegestaan in uitgaande richting, wordt het standaard geblokkeerd in binnenkomende richting. Er is een standaardregel waarmee de load balancer van Azure wordt toegestaan de status van uw VM's en rolexemplaren te controleren. U kunt deze regel onderdrukken als u geen set met taakverdeling gebruikt.

**Standaardregels voor binnenkomende verkeer**

| Naam                              | Prioriteit | Bron-IP          | Bronpoort | Doel-IP  | Doelpoort | Protocol | Toegang |
|-----------------------------------|----------|--------------------|-------------|-----------------|------------------|----------|--------|
| ALLOW VNET INBOUND                | 65000    | VIRTUAL_NETWORK    | *           | VIRTUAL_NETWORK | *                | *        | ALLOW  |
| ALLOW AZURE LOAD BALANCER INBOUND | 65001    | AZURE_LOADBALANCER | *           | *               | *                | *        | ALLOW  |
| DENY ALL INBOUND                  | 65500    | *                  | *           | *               | *                | *        | DENY   |

**Standaardregels voor uitgaand verkeer**

| Naam                    | Prioriteit | Bron-IP       | Bronpoort | Doel-IP  | Doelpoort | Protocol | Toegang |
|-------------------------|----------|-----------------|-------------|-----------------|------------------|----------|--------|
| ALLOW VNET OUTBOUND     | 65000    | VIRTUAL_NETWORK | *           | VIRTUAL_NETWORK | *                | *        | ALLOW  |
| ALLOW INTERNET OUTBOUND | 65001    | *               | *           | INTERNET        | *                | *        | ALLOW  |
| DENY ALL OUTBOUND       | 65500    | *               | *           | *               | *                | *        | DENY   |

## NSG's koppelen

U kunt een NSG koppelen aan VM's, NIC's en subnetten, afhankelijk van het implementatiemodel dat u gebruikt.

[AZURE.INCLUDE [learn-about-deployment-models-both-include.md](../../includes/learn-about-deployment-models-both-include.md)]
 
- **Een NSG koppelen aan een VM (alleen voor klassieke implementaties).** Als u een NSG aan een VM koppelt, worden de netwerktoegangsregels toegepast op al het binnenkomende en uitgaande verkeer van de VM. 

- **Een NSG koppelen aan een NIC (alleen voor implementaties van Resource Manager).** Als u een NSG aan een NIC koppelt, worden de netwerktoegangsregels in de NSG alleen toegepast op die NIC. Als u een VM meerdere NIC's hebt en er een NSG wordt toegepast op één NIC, heeft dat geen invloed op verkeer dat is gebonden aan andere NIC's. 

- **Een NSG koppelen aan een subnet (alle implementaties)**. Als u een NSG aan een subnet koppelt, worden netwerktoegangsregels in de NSG toegepast op alle IaaS- en PaaS-resources in het subnet. 

U kunt verschillende NSG's koppelen aan een VM (of NIC, afhankelijk van het implementatiemodel) en het subnet dat met een NIC of VM is verbonden. In dat geval worden alle netwerktoegangsregels toegepast op het verkeer op basis van prioriteit in elke NSG, in de volgende volgorde:

- **Binnenkomend verkeer**
    1. NSG die is toegepast op het subnet. 
    
           If subnet NSG has a matching rule to deny traffic, packet will be dropped here.
    2. NSG die is toegepast op de NIC (Resource Manager) of VM (klassiek). 
       
           If VM\NIC NSG has a matching rule to deny traffic, packet will be dropped at VM\NIC, although subnet NSG has a matching rule to allow traffic.
- **Uitgaand verkeer**
    1. NSG die is toegepast op de NIC (Resource Manager) of VM (klassiek). 
      
           If VM\NIC NSG has a matching rule to deny traffic, packet will be dropped here.
    2. NSG die is toegepast op het subnet.
       
           If subnet NSG has a matching rule to deny traffic, packet will be dropped here, although VM\NIC NSG has a matching rule to allow traffic.

![ACL's voor NSG](./media/virtual-network-nsg-overview/figure2.png)

>[AZURE.NOTE] U kunt slechts één NSG aan een subnet, VM of NIC koppelen, maar u kunt wel dezelfde NSG aan zo veel resources koppelen als u wilt.

## Implementatie
U kunt NSG's implementeren in het klassieke implementatiemodel of het implementatiemodel van Resource Manager met behulp van de hieronder vermelde hulpprogramma's.

|Implementatieprogramma|Klassiek|Resource Manager|
|---|---|---|
|Klassieke portal|![Nee][red]|![Nee][red]|
|Azure Portal|![Ja][green]|<a href="https://azure.microsoft.com/documentation/articles/virtual-networks-create-nsg-arm-pportal">![Ja][green]</a>|
|PowerShell|<a href="https://azure.microsoft.com/documentation/articles/virtual-networks-create-nsg-classic-ps">![Ja][green]</a>|<a href="https://azure.microsoft.com/documentation/articles/virtual-networks-create-nsg-arm-ps">![Ja][green]</a>|
|Azure CLI|<a href="https://azure.microsoft.com/documentation/articles/virtual-networks-create-nsg-classic-cli">![Ja][green]</a>|<a href="https://azure.microsoft.com/documentation/articles/virtual-networks-create-nsg-arm-cli">![Ja][green]</a>|
|ARM-sjabloon|![Nee][red]|<a href="https://azure.microsoft.com/documentation/articles/virtual-networks-create-nsg-arm-template">![Ja][green]</a>|

|**Sleutel**|![Ja][green] Ondersteund. Klikken voor artikel.|![Nee][red] Niet ondersteund.|
|---|---|---|

## Planning

Voordat u NSG's implementeert, moet u de onderstaande vragen beantwoorden:   

1. Van welke typen resources wilt u het binnenkomende of uitgaande verkeer filteren (NIC's in dezelfde VM, VM's of andere resources zoals cloudservices of toepassingsserviceomgevingen die zijn verbonden met hetzelfde subnet, of tussen resources die zijn verbonden met verschillende subnetten)?

2. Zijn de resources waarvan u het binnenkomende/uitgaande verkeer wilt filteren verbonden met subnetten in bestaande VNET'S of worden ze verbonden met nieuwe VNs of subnetten?
 
Lees de [aanbevolen procedures voor cloudservices en netwerkbeveiliging](../best-practices-network-security.md) voor meer informatie over het plannen van netwerkbeveiliging in Azure. 

## Overwegingen bij het ontwerpen

Lees, als u de antwoorden op de vragen in de sectie [Planning](#Planning) weet, het volgende aandachtig door voordat u uw NSG's definieert.

### Limieten

Houd rekening met de volgende limieten bij het ontwerpen van uw NSG's.

|**Beschrijving**|**Standaardlimiet**|**Gevolgen**|
|---|---|---|
|Aantal NSG's dat u aan een subnet, VM of NIC kunt koppelen|1|Dit betekent dat u NSG's niet kunt combineren. Zorg ervoor dat alle benodigde regels voor een bepaalde set resources zijn opgenomen in één NSG.|
|NSG's per regio per abonnement|100|Standaard wordt een nieuwe NSG gemaakt voor elke VM die u in de Azure-portal maakt. Als u dit standaardgedrag toestaat, is het maximum aantal NSG's snel bereikt. Houd rekening met deze limiet in uw ontwerp en verdeel uw resources zo nodig in meerdere regio's of abonnementen. |
|NSG-regels per NSG|200|Gebruik een breed bereik van IP-adressen en poorten zodat u deze limiet niet overschrijdt. |

>[AZURE.IMPORTANT] Bekijk alle [limieten voor netwerkservices in Azure](../azure-subscription-service-limits.md#networking-limits) voordat u uw oplossing ontwerpt. Sommige limieten kunnen worden verhoogd door een ondersteuningsticket te openen.

### VNET's en subnetten ontwerpen

Omdat NSG's kunnen worden toegepast op subnetten, kunt u het aantal NSG's minimaliseren door uw resources te  groeperen per subnet, en NSG's op subnetten toe te passen.  Als u besluit om NSG's op subnetten toe te passen, merkt u mogelijk dat bij het definiëren van bestaande VNET's en subnetten niet altijd rekening is gehouden met NSG's. Mogelijk moet u nieuwe VNET's en subnetten definiëren ter ondersteuning van uw NSG-ontwerp. En de nieuwe resources implementeren in de nieuwe subnetten. U kunt dan een migratiestrategie definiëren om bestaande resources te verplaatsen naar de nieuwe subnetten. 

### Speciale regels

U moet rekening houden met de speciale regels die hieronder worden vermeld. Zorg ervoor dat u geen verkeer blokkeert dat wordt toegestaan door deze regels, anders is er in uw infrastructuur geen communicatie mogelijk met essentiële Azure-services.

- **Virtueel IP-adres van het hostknooppunt:** Basisinfrastructuurservices zoals DHCP, DNS en Statuscontrole worden geleverd via het gevirtualiseerde host-IP-adres 168.63.129.16. Dit openbare IP-adres is van Microsoft en is het enige gevirtualiseerde IP-adres dat in alle regio's wordt gebruikt voor dit doel. Dit IP-adres wordt toegewezen aan het fysieke IP-adres van de servermachine (hostknooppunt) die fungeert als host voor de virtuele machine. Het hostknooppunt fungeert als de DHCP-relay, de recursieve DNS-omzetter en de bron voor de statuscontrole van de load balancer en de machine. Communicatie met dit IP-adres mag niet worden beschouwd als een aanval.

- **Licentieverlening (Key Management Service):** voor alle Windows installatiekopieën die op de virtuele machines worden uitgevoerd, is een licentie vereist. Hiertoe wordt een licentieaanvraag verstuurd naar de Key Management Service-hostservers waarop dergelijke query's worden afgehandeld. Dit gebeurt altijd via de uitgaande poort 1688.

### ICMP-verkeer

In de huidige NSG-regels worden alleen de protocollen *TCP* en *UDP* ondersteund. Er is geen specifieke tag voor *ICMP*. ICMP-verkeer wordt echter wel standaard toegestaan binnen een virtueel netwerk via de regel voor binnenkomend verkeer in een VNET (standaardregel 65000 binnenkomend) waarmee verkeer van/naar elke poort en elk protocol in het VNET wordt toegestaan.

### Subnetten

- Houd rekening met het aantal benodigde lagen voor uw workload. Elke laag kan worden geïsoleerd met behulp van een subnet, waarbij een NSG wordt toegepast op het subnet. 
- Als u een subnet voor een VPN-gateway of ExpressRoute-circuit moet implementeren, moet u **GEEN** NSG op dat subnet toepassen. Als u dit wel doet, is er verbinding mogelijk tussen VNET’s of lokale netwerkonderdelen onderling.
- Als u een virtueel apparaat wilt implementeren, moet u het virtuele apparaat implementeren in een eigen subnet, anders werken uw door de gebruiker gedefinieerde routes (UDR's) mogelijk niet correct. U kunt een NSG op subnetniveau implementeren om binnenkomend en uitgaand verkeer in dit subnet te filteren. Meer informatie over [het beheren van verkeersstromen en het gebruik van virtuele apparaten](virtual-networks-udr-overview.md).

### Load balancers

- Overweeg de taakverdelingsregels en NAT-regels voor elke load balancer die wordt gebruikt door elk van de workloads. Deze regels zijn gebonden aan een back-end-pool met NIC's (implementaties van Resource Manager) of VM's/rolexemplaren (klassieke implementaties). Overweeg om een NSG voor elke back-end-pool te maken zodat alleen verkeer wordt toegestaan dat is gedefinieerd in de regels die zijn geïmplementeerd in de load balancers. Dit garandeert dat verkeer dat rechtstreeks afkomstig is van de back-end-pool, in plaats van via de load balancer, ook gefilterd is.
- In klassieke implementaties maakt u eindpunten waarop poorten van een load balancer worden afgestemd met poorten van uw VM's of rolexemplaren. U kunt ook uw eigen individuele openbare load balancer maken in een implementatie van Resource Manager. Als u verkeer beperkt tot VM's en rolexemplaren die deel uitmaken van een back-end-pool in een load balancer met behulp van NSGs, houd er dan rekening mee dat de doelpoort voor het binnenkomende verkeer de werkelijke poort van de VM of het rolexemplaar is, en niet de poort die beschikbaar is gemaakt door de load balancer. Houd er ook rekening mee dat de bronpoort en het bronadres voor de verbinding met de VM een poort en adres zijn op de externe computer op internet, en niet de poort die en het adres dat beschikbaar is gemaakt door de load balancer.
- Net als bij openbare load balancers geldt: wanneer u NSG's maakt voor het filteren van verkeer dat afkomstig is van een interne load balancer (ILB), worden de bronpoort en het bronadresbereik gebruikt van de computer waarvan de oproep afkomstig is, en niet die van de load balancer. Bovendien zijn de doelpoort en het doeladresbereik gerelateerd aan de computer waarop het verkeer binnenkomt, niet aan de load balancer.

### Overige

- ACL's voor eindpunten en NSG's worden niet ondersteund op een en hetzelfde VM-exemplaar. Als u een NSG wilt gebruiken en er al een ACL voor eindpunten is geïmplementeerd, moet u eerst de ACL voor eindpunten verwijderen. Zie [ACL's voor eindpunten beheren](virtual-networks-acl-powershell.md) voor meer informatie over hoe u dit doet.
- In het implementatiemodel van Resource Manager kunt u een NSG gebruiken die is gekoppeld aan een NIC voor VM's met meerdere NIC's om beheer (externe toegang) via NIC in te schakelen, en zo verkeer te scheiden.
- Net als bij het gebruik van load balancers geldt dat u bij het filteren van verkeer van andere VNET's, het adresbereik van de bron van de externe computer moet gebruiken, en niet dat van de gateway waarmee de VNET's onderling worden verbonden.
- Veel Azure-services kunnen niet worden verbonden met virtuele Azure-netwerken en daarom kan verkeer van en naar deze netwerken niet met NSG's worden gefilterd.  Raadpleeg de documentatie bij de services die u gebruikt om te bepalen of deze kunnen worden gebruikt in combinatie met VNET's.

## Voorbeeldimplementatie

Ter illustratie van de toepassing van de informatie in dit artikel, definiëren we NSG's voor het filteren van het netwerkverkeer naar een 2-laags workloadoplossing met de volgende vereisten:

1. Scheiding van verkeer tussen front-end (Windows-webservers) en back-end (SQL Database-servers).
2. Taakverdelingsregels waarmee verkeer naar de load balancer wordt doorgestuurd naar alle webservers op poort 80.
3. NAT-regels waarmee binnenkomend verkeer op poort 50001 van de load balancer wordt doorgestuurd op poort 3389 van één VM in de front-end.
4. Geen toegang tot de front-end- of back-end-VM's via internet, met uitzondering van vereiste nummer 1.
5. Geen toegang tot internet via de front-end of back-end.
6. Toegang tot poort 3389 voor elke webserver in de front-end, voor verkeer dat afkomstig is van het front-end-subnet zelf.
7. Toegang tot poort 3389 voor alle SQL Server-VM's in de back-end van alleen het front-end-subnet.
8. Toegang tot poort 1433 voor alle SQL Server-VM's in de back-end van alleen het front-end-subnet.
9. Scheiding van beheerverkeer (poort 3389) en databaseverkeer (poort 1433) op verschillende NIC's in de back-end-VM's.

![NSG's](./media/virtual-network-nsg-overview/figure1.png)

Zoals u ziet in het bovenstaande diagram, zijn de VM's *Web1* en *Web2* verbonden met het subnet *FrontEnd*, en zijn de VM's *DB1* en *DB2* verbonden met het subnet *BackEnd*.  Beide subnetten behoren tot het VNET *TestVNET*. Alle resources zijn toegewezen aan de Azure-regio *VS-west*.

De vereisten 1-6 (met uitzondering van 3) hierboven zijn allemaal beperkt tot subnetruimten. Om het benodigde aantal regels voor elke NSG te beperken en om gemakkelijk extra VM's te kunnen toevoegen aan de subnetten met hetzelfde type workload als de bestaande VM's, kunnen we de volgende NSG's op subnetniveau implementeren.

### NSG voor subnet FrontEnd

**Regels voor binnenkomend verkeer**

|Regel|Toegang|Prioriteit|Bronadresbereik|Bronpoort|Doeladresbereik|Doelpoort|Protocol|
|---|---|---|---|---|---|---|---|
|HTTP toestaan|Toestaan|100|INTERNET|\*|\*|80|TCP|
|RDP via FrontEnd toestaan|Toestaan|200|192.168.1.0/24|\*|\*|3389|TCP|
|alles via internet weigeren|Weigeren|300|INTERNET|\*|\*|\*|TCP|

**Regels voor uitgaand verkeer**

|Regel|Toegang|Prioriteit|Bronadresbereik|Bronpoort|Doeladresbereik|Doelpoort|Protocol|
|---|---|---|---|---|---|---|---|
|internet weigeren|Weigeren|100|\*|\*|INTERNET|\*|\*|

### NSG voor subnet BackEnd

**Regels voor binnenkomend verkeer**

|Regel|Toegang|Prioriteit|Bronadresbereik|Bronpoort|Doeladresbereik|Doelpoort|Protocol|
|---|---|---|---|---|---|---|---|
|internet weigeren|Weigeren|100|INTERNET|\*|\*|\*|\*|

**Regels voor uitgaand verkeer**

|Regel|Toegang|Prioriteit|Bronadresbereik|Bronpoort|Doeladresbereik|Doelpoort|Protocol|
|---|---|---|---|---|---|---|---|
|internet weigeren|Weigeren|100|\*|\*|INTERNET|\*|\*|

### NSG voor één VM (NIC) in FrontEnd voor RDP via internet

**Regels voor binnenkomend verkeer**

|Regel|Toegang|Prioriteit|Bronadresbereik|Bronpoort|Doeladresbereik|Doelpoort|Protocol|
|---|---|---|---|---|---|---|---|
|RDP via internet toestaan|Toestaan|100|INTERNET|*|\*|3389|TCP|

>[AZURE.NOTE] Merk op dat het bronadresbereik voor deze regel **Internet** is, en niet het VIP voor de load balancer. De bronpoort **\***, niet 500001. Zorg ervoor dat u NAT-regels, taakverdelingsregels en NSG-regels niet met elkaar verwart. NSG-regels hebben altijd betrekking op de bron van herkomst en de bestemming van het verkeer, **NIET** op de load balancer ertussen. 

### NSG voor beheer-NIC's in BackEnd

**Regels voor binnenkomend verkeer**

|Regel|Toegang|Prioriteit|Bronadresbereik|Bronpoort|Doeladresbereik|Doelpoort|Protocol|
|---|---|---|---|---|---|---|---|
|RDP via front-end toestaan|Toestaan|100|192.168.1.0/24|*|\*|3389|TCP|

### NSG voor databasetoegang-NIC's in back-end

**Regels voor binnenkomend verkeer**

|Regel|Toegang|Prioriteit|Bronadresbereik|Bronpoort|Doeladresbereik|Doelpoort|Protocol|
|---|---|---|---|---|---|---|---|
|SQL via front-end toestaan|Toestaan|100|192.168.1.0/24|*|\*|1433|TCP|

Omdat sommige van de bovenstaande NSG's moeten worden gekoppeld aan afzonderlijke NIC's, moet u dit scenario implementeren als een implementatie van Resource Manager. Merk op hoe regels worden gecombineerd voor het subnet en NIC-niveau, afhankelijk van hoe ze moeten worden toegepast. 

## Volgende stappen

- [NSG's implementeren in het klassieke implementatiemodel](virtual-networks-create-nsg-classic-ps.md).
- [NSG's implementeren in Resource Manager](virtual-networks-create-nsg-arm-pportal.md).
- [NSG-logboeken beheren](virtual-network-nsg-manage-log.md).

[groen]: ./media/virtual-network-nsg-overview/green.png
[geel]: ./media/virtual-network-nsg-overview/yellow.png
[rood]: ./media/virtual-network-nsg-overview/red.png



<!--HONumber=Jun16_HO2-->


