<properties
   pageTitle="Uw domein delegeren naar Azure DNS | Microsoft Azure"
   description="Lees hoe u de domeindelegering wijzigt en DNS-naamservers kunt gebruiken om domeinen te hosten."
   services="dns"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/10/2016"
   ms.author="cherylmc"/>


# Een domein delegeren naar Azure DNS

Met Azure DNS kunt u een DNS-zone hosten en de DNS-records voor een domein in Azure beheren. Het domein moet vanaf het bovenliggende domein worden gedelegeerd naar Azure DNS om ervoor te zorgen dat DNS-query's voor een domein Azure DNS bereiken. Houd er rekening mee Azure DNS is niet de domeinregistrar. In dit artikel wordt uitgelegd hoe domeindelegering werkt en hoe domeinen naar Azure DNS delegeert.




## Hoe DNS-delegering werkt

### Domeinen en zones

Het Domain Name System is een hiërarchie van domeinen. De hiërarchie start vanaf het hoofddomein. De naam van dit domein is eenvoudigweg '**.**'.  Hieronder komen de topleveldomeinen, zoals com, net, org, uk of nl.  Onder de topleveldomeinen komen de secondleveldomainen, zoals org.uk of co.jp.  Enzovoort. De domeinen in de DNS-hiërarchie worden gehost met behulp van afzonderlijke DNS-zones. Deze zones worden globaal gedistribueerd en gehost door DNS-naamservers over de hele wereld.

**DNS-zone**

Een domein is een unieke naam in het Domain Name System, bijvoorbeeld contoso.com. Een DNS-zone wordt gebruikt om de DNS-records voor een bepaald domein te hosten. Het domein 'contoso.com' kan bijvoorbeeld een aantal DNS-records bevatten, zoals 'mail.contoso.com' (voor een e-mailserver) en 'www.contoso.com' (voor een website).

**Domeinregistrar**

Een domeinregistrar is een bedrijf dat internetdomeinnamen kan leveren. Het bedrijf controleert of het internetdomein dat u wilt gebruiken, beschikbaar is en bieden u de mogelijkheid om de naam te kopen. Zodra de domeinnaam is geregistreerd, bent u de juridische eigenaar van de domeinnaam. Als u al een internetdomein hebt, gebruikt u de huidige domeinregistrar om te delegeren naar Azure DNS.

>[AZURE.NOTE] Zie [Internetdomeinbeheer in Azure AD](https://msdn.microsoft.com/library/azure/hh969248.aspx) voor meer informatie over wie de eigenaar is van een bepaald domein of voor informatie over het kopen van domein.

### Omzetting en delegering

Er zijn twee typen DNS-servers:

- Een _gezaghebbende_ DNS-server host DNS-zones. Deze beantwoordt DNS-query's voor records in deze zones.
- Een _recursieve_ DNS-server host geen DNS-zones. De server beantwoordt alle DNS-query's door gezaghebbende DNS-servers aan te roepen om de benodigde gegevens te verzamelen.

DNS-clients op pc's of mobiele apparaten roepen een recursieve DNS-server om de DNS-query's uit te voeren die de clienttoepassingen nodig hebben.

Wanneer een recursieve DNS-server een query voor een DNS-record zoals www.contoso.com ontvangt, moet eerst de naamserver worden gezocht die de zone voor het domein contoso.com host. Hiervoor wordt begonnen bij de basisnaamservers om de naamservers te zoeken die com-zone hosten. Vervolgens wordt er een query op de com-naamservers uitgevoerd om de naamservers te zoeken die de zone contoso.com hosten.  Tot slot wordt er op deze naamservers een query uitgevoerd om www.contoso.com te zoeken.  

Dit wordt het omzetten van de DNS-naam genoemd. Strikt genomen omvat de DNS-omzetting aanvullende stappen zoals het volgen van CNAMEs, maar dat is niet belangrijk om te begrijpen hoe de DNS-delegering werkt.

Hoe 'wijst' een bovenliggende zone naar de naamservers voor een onderliggende zone? Hiervoor wordt gebruikgemaakt van een speciaal type DNS-record, ook wel een NS-record genoemd (NS staat voor 'naamserver'). De hoofdzone bevat bijvoorbeeld NS-records voor com en toont u de naamservers voor de com-zone. De com-zone bevat op zijn beurt NS-records voor contoso.com, die u de naamservers toont voor de zone contoso.com. Het instellen van de NS-records voor een onderliggende zone in de bovenliggende zone wordt het delegeren van het domein genoemd.


![DNS-naamserver](./media/dns-domain-delegation/image1.png)

Elke delegering bevat twee kopieën van de NS-records. De ene kopie bevindt zich in de bovenliggende zone en wijst naar de onderliggende zone, terwijl de andere kopie zich in de onderliggende zone zelf bevindt. De zone contoso.com bevat de NS-records voor contoso.com (naast de NS-records in com). Deze records worden gezaghebbende NS-records genoemd en bevinden zich in de apex van de onderliggende zone.


## Een domein delegeren naar Azure DNS

Zodra u de DNS-zone in Azure DNS hebt gemaakt, moet u NS-records instellen in de bovenliggende zone om Azure DNS de gezaghebbende bron voor de naamomzetting voor uw zone te maken. Voor domeinen die bij een registrar worden gekocht, zal de registrar u de optie bieden om deze NS-records in te stellen.

>[AZURE.NOTE] U hoeft niet de eigenaar van een domein te zijn om een DNS-zone met die domeinnaam in Azure DNS te maken. U hoeft echter geen eigenaar van het domein te zijn om de delegering naar Azure DNS in te stellen voor de registrar.

Stel dat u het domein contoso.com koopt en een zone met de naam contoso.com in Azure DNS maakt. Als eigenaar van het domein zal uw registrar u de optie bieden om de adressen van de naamserver te configureren (oftewel de NS-records) voor uw domein. De registrar slaat deze NS-records op in het bovenliggende domein in dit geval '.com'. Clients over de hele wereld worden vervolgens omgeleid naar uw domein in de Azure DNS-zone wanneer ze DNS-records omzetten in contoso.com.

### De namen van de naamserver zoeken

Voordat u uw DNS-zone naar Azure DNS kunt delegeren, moet u eerst de servernamen voor uw zone weten. Telkens wanneer er een zone wordt gemaakt, wijst Azure DNS naamservers uit een groep toe.

De eenvoudigste manier om de toegewezen naamservers te bekijken, is via Azure Portal.  In dit voorbeeld zijn de naamservers ns1-01.azure-dns.com, ns2 01.azure dns.net, ns3-01.azure-dns.org en ns4-01.azure-dns.info aan de zone contoso.net toegewezen:

 ![DNS-naamserver](./media/dns-domain-delegation/viewzonens500.png)

Azure DNS maakt automatisch gezaghebbende NS-records in uw zone die de toegewezen naamservers bevatten.  Als u de namen van de naamservers wilt weergeven via Azure PowerShell of Azure CLI, hoeft u deze records alleen maar op te halen.

Met Azure PowerShell kunnen de gezaghebbende NS-records als volgt worden opgehaald. De recordnaam "@" wordt gebruikt om te verwijzen naar records in de apex van de zone. 

    PS> $zone = Get-AzureRmDnsZone –Name contoso.net –ResourceGroupName MyResourceGroup
    PS> Get-AzureRmDnsRecordSet –Name “@” –RecordType NS –Zone $zone

    Name              : @
    ZoneName          : contoso.net
    ResourceGroupName : MyResourceGroup
    Ttl               : 3600
    Etag              : 5fe92e48-cc76-4912-a78c-7652d362ca18
    RecordType        : NS
    Records           : {ns1-01.azure-dns.com, ns2-01.azure-dns.net, ns3-01.azure-dns.org,
                        ns4-01.azure-dns.info}
    Tags              : {}

U kunt ook de platformoverschrijdende CLI van Azure gebruiken om gezaghebbende NS-records op te halen en zodoende de naamservers achterhalen die zijn toegewezen aan uw zone:

    C:\> azure network dns record-set show MyResourceGroup contoso.net @ NS
    info:    Executing command network dns record-set show
        + Looking up the DNS Record Set "@" of type "NS"
    data:    Id                              : /subscriptions/.../resourceGroups/MyResourceGroup/providers/Microsoft.Network/dnszones/contoso.net/NS/@
    data:    Name                            : @
    data:    Type                            : Microsoft.Network/dnszones/NS
    data:    Location                        : global
    data:    TTL                             : 172800
    data:    NS records
    data:        Name server domain name     : ns1-01.azure-dns.com.
    data:        Name server domain name     : ns2-01.azure-dns.net.
    data:        Name server domain name     : ns3-01.azure-dns.org.
    data:        Name server domain name     : ns4-01.azure-dns.info.
    data:
    info:    network dns record-set show command OK

### Delegering instellen

Elke registrar heeft zijn eigen hulpprogramma's voor DNS-beheer om de naamserverrecords voor een domein te wijzigen. Ga naar DNS-beheerpagina van de registrar, bewerk de NS-records en vervang de NS-records door de records die door Azure DNS zijn gemaakt.

Wanneer u een domein naar Azure DNS delegeert, moet u de naamservernamen gebruiken die zijn verstrekt door Azure DNS. U moet niet glue records gebruiken om naar het IP-adres van de Azure DNS-naamserver te wijzen, aangezien deze IP-adressen in de toekomst kunnen worden gewijzigd. Delegeringen die gebruikmaken van de naamservernamen in uw eigen zone, ook wel vanity naamservers genoemd, worden momenteel niet ondersteund in Azure DNS.

### Controleren of de naamomzetting werkt

Nadat het delegeren is voltooid, kunt u controleren of de naamomzetting werkt door een hulpprogramma zoals nslookup te gebruiken om een query op de SOA-record voor uw zone uit te voeren (die ook automatisch wordt gemaakt wanneer de zone wordt gemaakt).

U hoeft de Azure DNS-naamservers niet op te geven, aangezien het normale DNS-omzettingproces automatisch de naamservers zoekt als delegering goed is ingesteld.

    nslookup –type=SOA contoso.com

    Server: ns1-04.azure-dns.com
    Address: 208.76.47.4

    contoso.com
    primary name server = ns1-04.azure-dns.com
    responsible mail addr = msnhst.microsoft.com
    serial = 1
    refresh = 900 (15 mins)
    retry = 300 (5 mins)
    expire = 604800 (7 days)
    default TTL = 300 (5 mins)

## Subdomeinen delegeren in Azure DNS

Als u een afzonderlijke onderliggende zone wilt instellen, kunt u een subdomein delegeren in Azure DNS. Stel dat u contoso.com hebt ingesteld en gedelegeerd in Azure DNS en vervolgens een afzonderlijke onderliggende zone, partners.contoso.com, wilt instellen. 

Het proces voor het instellen van een subdomein is vergelijkbaar met het proces voor een normale delegering. Het enige verschil is dat de NS-records in stap 3 moeten worden gemaakt in de bovenliggende zone in Azure DNS, contoso.com, moeten worden gemaakt en niet moeten worden ingesteld via een domeinregistrar.


1. Maak de onderliggende zone partners.contoso.com in Azure DNS.
2. Zoek de gezaghebbende NS-records in de onderliggende zone om de naamservers op te halen die de onderliggende zone in Azure DNS hosten.
3. Delegeer de onderliggende zone door NS-records in de bovenliggende te configureren die wijzen naar de onderliggende zone.


### Een subdomein delegeren

In het volgende PowerShell-voorbeeld kunt u zien hoe dit werkt. Dezelfde stappen kunnen ook worden uitgevoerd via Azure Portal of via de platformoverschrijdende CLI van Azure.

#### Step 1. De bovenliggende en onderliggende zones maken

Eerst moeten de bovenliggende en onderliggende zones worden gemaakt. Deze kunnen zich in dezelfde resourcegroep of in verschillende resourcegroepen bevinden.

    $parent = New-AzureRmDnsZone -Name contoso.com -ResourceGroupName RG1
    $child = New-AzureRmDnsZone -Name partners.contoso.com -ResourceGroupName RG1

#### Stap 2. NS-records ophalen

Vervolgens moeten de gezaghebbende NS-records uit de onderliggende zone worden opgehaald, zoals in het volgende voorbeeld.  Deze bevatten de naamservers die aan de onderliggende zone zijn toegewezen. 

    $child_ns_recordset = Get-AzureRmDnsRecordSet -Zone $child -Name "@" -RecordType NS

#### Stap 3. De onderliggende zone delegeren

Maak de bijbehorende NS-recordset in de bovenliggende zone om de delegering te voltooien. De naam van de recordset in de bovenliggende zone komt overeen met de naam van de onderliggende zone, in dit geval 'partners'.

    $parent_ns_recordset = New-AzureRmDnsRecordSet -Zone $parent -Name "partners" -RecordType NS -Ttl 3600
    $parent_ns_recordset.Records = $child_ns_recordset.Records
    Set-AzureRmDnsRecordSet -RecordSet $parent_ns_recordset

### Controleren of de naamomzetting werkt

U kunt controleren of alles juist is ingesteld door de SOA-record van de onderliggende zone te zoeken.

    nslookup –type=SOA partners.contoso.com

    Server: ns1-08.azure-dns.com
    Address: 208.76.47.8

    partners.contoso.com
        primary name server = ns1-08.azure-dns.com
        responsible mail addr = msnhst.microsoft.com
        serial = 1
        refresh = 900 (15 mins)
        retry = 300 (5 mins)
        expire = 604800 (7 days)
        default TTL = 300 (5 mins)

## Volgende stappen

[DNS-zones beheren](dns-operations-dnszones.md)

[DNS-records beheren](dns-operations-recordsets.md)




<!--HONumber=Jun16_HO2-->


