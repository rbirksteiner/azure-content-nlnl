<properties
   pageTitle="Een DNS-zone maken met CLI| Microsoft Azure"
   description="Leer stapsgewijs hoe u met CLI DNS-zones voor Azure DNS maakt om uw DNS-domein te hosten."
   services="dns"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/09/2016"
   ms.author="cherylmc"/>

# Een Azure DNS-zone maken met CLI


> [AZURE.SELECTOR]
- [Azure Portal](dns-getstarted-create-dnszone-portal.md)
- [PowerShell](dns-getstarted-create-dnszone.md)
- [Azure CLI](dns-getstarted-create-dnszone-cli.md)


In dit artikel wordt uitgelegd welke stappen u moet uitvoeren om een DNS-zone met CLI te maken. U kunt ook een DNS-zone met PowerShell of Azure Portal maken. 

[AZURE.INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)] 


## Voordat u begint

Deze instructies hebben betrekking op Microsoft Azure CLI. Zorg ervoor dat u bijwerkt naar de nieuwste Azure CLI (0.9.8 of later) om de Azure DNS-opdrachten te kunnen gebruiken. Typ `azure -v` om te controleren welke versie van Azure CLI momenteel op uw computer is geïnstalleerd.

## Stap 1: Azure CLI instellen

### 1. Azure CLI installeren

U kunt Azure CLI installeren voor Windows, Linux of MAC. De volgende stappen moeten worden voltooid voordat u Azure DNS met Azure CLI kunt beheren. Lees [Azure CLI installeren](../xplat-cli-install.md) voor meer informatie. Voor de DNS-opdrachten hebt u Azure CLI versie 0.9.8 of later nodig.

Alle netwerkprovideropdrachten voor CLI kunt u weergeven met de volgende opdracht:

    Azure network

### 2. Naar de CLI-modus schakelen

Azure DNS maakt gebruik van Azure Resource Manager. Zorg ervoor dat u overschakelt naar de CLI-modus om de ARM-opdrachten te kunnen gebruiken.

    Azure config mode arm

### 3. Aanmelden bij uw Azure-account

U wordt gevraagd om u te verifiëren met u referenties. Houd er rekening mee dat u alleen ORGID-accounts kunt gebruiken.

    Azure login -u "username"

### 4. Het abonnement selecteren

Kies welk Azure-abonnement u wilt gebruiken.

    Azure account set "subscription name"

### 5. Een resourcegroep maken

Azure Resource Manager vereist dat er voor alle resourcegroepen een locatie wordt opgegeven. Deze locatie wordt gebruikt als de standaardlocatie voor resources in die resourcegroep. Aangezien alle DNS-resources globaal en niet regionaal zijn, is de keuze van de locatie voor de resourcegroep niet van invloed op Azure DNS.

U kunt deze stap overslaan als u een bestaande resourcegroep gebruikt. 

    Azure group create -n myresourcegroup --location "West US"


### 6. Registreren

De Azure DNS-service wordt beheerd door de Microsoft.Network-resourceprovider. Uw Azure-abonnement moet worden geregistreerd voor het gebruik van deze resourceprovider voordat u Azure DNS kunt gebruiken. Dit is een eenmalige bewerking voor elk abonnement.

    Azure provider register --namespace Microsoft.Network


## Stap 2: een DNS-zone maken

Een DNS-zone wordt gemaakt met de opdracht `azure network dns zone create`. U kunt eventueel ook een DNS-zone en tags maken. Tags bevatten een lijst met naam/waarde-paren en worden door Azure Resource Manager gebruikt om resources te labelen voor facturerings- of groeperingsdoeleinden. Zie [Tags gebruiken om uw Azure-resources te organiseren](../resource-group-using-tags.md) voor meer informatie over tags. 

Zonenamen moeten in Azure DNS worden opgegeven zonder een afsluitende **'.'**. Bijvoorbeeld als '**contoso.com**' in plaats van '**contoso.com.**'.


### Een DNS-zone maken

In het volgende voorbeeld maakt u een DNS-zone met de naam *contoso.com* in de resourcegroep *MyResourceGroup*. 

Gebruik het voorbeeld om uw DNS-zone te maken door de waarden te vervangen voor uw eigen DNS-zone.

    Azure network dns zone create myresourcegroup contoso.com

### Een DNS-zone en tags maken

Azure DNS CLI ondersteunt tags van DNS-zones die zijn opgegeven met de optionele parameter *-Tag*. in het volgende voorbeeld kunt u zien hoe u DNS-zone met twee tags maakt, project = demo en env = test.

Gebruik het voorbeeld hieronder om een DNS-zone en tags te maken, door de waarden te vervangen door uw eigen waarden.

    Azure network dns zone create myresourcegroup contoso.com -t "project=demo";"env=test"

## Records weergeven

Wanneer u een DNS-zone maakt, wordt de volgende DNS-records gemaakt:

- De SOA-record (Start of Authority). Deze is aanwezig in de hoofdmap van elke DNS-zone.

- De gezaghebbende naamserverrecords (NS). Deze laten zien welke servers de zone hosten. Azure DNS maakt gebruik van een groep naamservers, waardoor er verschillende naamservers kunnen worden toegewezen aan verschillende zones in Azure DNS. Zie [Een domein aan Azure DNS overdragen](dns-domain-delegation.md) voor meer informatie.

Gebruik `azure network dns-record-set show` om deze records weer te geven.<BR>
*Gebruik: network dns record-set show <resource-group> <dns-zone-name> <name> <type>*


Als u in het onderstaande voorbeeld de opdracht uitvoert met de resourcegroep *myresourcegroup*, de recordsetnaam*"@"* (voor een hoofdmaprecord) en het type *SOA*, leidt dit tot de volgende uitvoer:


    azure network dns record-set show myresourcegroup "contoso.com" "@" SOA
    info:    Executing command network dns-record-set show
    + Looking up the DNS record set "@"
    data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/SOA/@
    data:    Name                            : @
    data:    Type                            : Microsoft.Network/dnszones/SOA
    data:    Location                        : global
    data:    TTL                             : 3600
    data:    SOA record:
    data:      Email                         : msnhst.microsoft.com
    data:      Expire time                   : 604800
    data:      Host                          : edge1.azuredns-cloud.net
    data:      Minimum TTL                   : 300
    data:      Refresh time                  : 900
    data:      Retry time                    : 300
    data:                                    :
<BR>
Als u de NS-records wilt weergeven die u met deze zone hebt gemaakt, gebruikt u de volgende opdracht:

    azure network dns record-set show myresourcegroup "contoso.com" "@" NS
    info:    Executing command network dns-record-set show
    + Looking up the DNS record set "@"
    data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@
    data:    Name                            : @
    data:    Type                            : Microsoft.Network/dnszones/NS
    data:    Location                        : global
    data:    TTL                             : 3600
    data:    NS records
    data:        Name server domain name     : ns1-05.azure-dns.com
    data:        Name server domain name     : ns2-05.azure-dns.net
    data:        Name server domain name     : ns3-05.azure-dns.org
    data:        Name server domain name     : ns4-05.azure-dns.info
    data:
    info:    network dns-record-set show command OK

>[AZURE.NOTE] Recordsets in de hoofdmap (of *apex*) van een DNS-zone gebruiken **@** als recordsetnaam.

## Testen

U kunt uw DNS-zone testen door gebruik te maken van DNS-hulpprogramma's, zoals nslookup, DIG of de PowerShell-cmdlet `Resolve-DnsName`.

Als u uw domein nog niet hebt gedelegeerd voor het gebruik van de nieuwe zone in Azure DNS, moet u de DNS-query rechtstreeks op een van de naamservers voor uw zone richten. De naamservers voor uw zone worden gegeven in de NS-records, zoals vermeld door 'azure network dns record-set show' hierboven. Zorg ervoor dat u in de onderstaande opdracht de juiste waarden voor uw zone vervangt.

In volgende voorbeeld wordt met DIG een query op het domein contoso.com uitgevoerd, waarbij gebruik wordt gemaakt van de naamservers die zijn toegewezen aan de DNS-zone. De query moet naar een naamserver wijzen waarvoor *@<name server for the zone>* is gebruikt en waarvan de zonenaam DIG bevat.

     <<>> DiG 9.10.2-P2 <<>> @ns1-05.azure-dns.com contoso.com
    (1 server found)
    global options: +cmd
    Got answer:
    ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 60963
    flags: qr aa rd; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1
    WARNING: recursion requested but not available

    OPT PSEUDOSECTION:
    EDNS: version: 0, flags:; udp: 4000
    QUESTION SECTION:
    contoso.com.                        IN      A

    AUTHORITY SECTION:
    contoso.com.         300     IN      SOA     edge1.azuredns-cloud.net.
    msnhst.microsoft.com. 6 900 300 604800 300

    Query time: 93 msec
    SERVER: 208.76.47.5#53(208.76.47.5)
    WHEN: Tue Jul 21 16:04:51 Pacific Daylight Time 2015
    MSG SIZE  rcvd: 120

## Volgende stappen

Nadat u een DNS-zone hebt gemaakt, maakt u [recordsets en records](dns-getstarted-create-recordset-cli.md) om namen om te zetten voor uw internetdomein.



<!--HONumber=Jun16_HO2-->


