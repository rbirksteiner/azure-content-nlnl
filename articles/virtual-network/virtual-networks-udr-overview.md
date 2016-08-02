<properties 
   pageTitle="Wat zijn door de gebruiker gedefinieerde routes en Doorsturen via IP?"
   description="Meer informatie over het gebruik van door de gebruiker gedefinieerde routes (UDR) en Doorsturen via IP om verkeer door te sturen naar virtuele apparaten in Azure."
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
   ms.date="03/15/2016"
   ms.author="telmos" />

# Wat zijn door de gebruiker gedefinieerde routes en Doorsturen via IP?
Als u virtuele machines (VM's) aan een virtueel netwerk (VNET) in Azure toevoegt, ziet u dat de virtuele machines automatisch met elkaar kunnen communiceren via het netwerk. U hoeft geen gateway op te geven, ook niet als de virtuele machines tot verschillende subnetten behoren. Hetzelfde geldt voor de communicatie tussen VM's en internet. Als er een hybride verbinding is tussen Azure en uw eigen datacentrum, is er zelfs communicatie met uw on-premises netwerk mogelijk.

Deze communicatiestroom is mogelijk omdat de IP-verkeersstromen in Azure wordt gedefinieerd met behulp van een reeks systeemroutes. Systeemroutes bepalen de communicatiestroom in de volgende scenario's:

- Binnen een en hetzelfde subnet.
- Van het ene naar het andere subnet binnen een VNET.
- Van virtuele machines naar internet.
- Van het ene naar het andere VNET via een VPN-gateway.
- Van een VNET naar uw on-premises netwerk via een VPN-gateway.

In de afbeelding hieronder ziet u eenvoudige configuratie met een VNET, twee subnetten en enkele virtuele machines, plus de systeemroutes voor het toestaan van IP-verkeer.

![Systeemroutes in Azure](./media/virtual-networks-udr-overview/Figure1.png)

Hoewel bij het gebruik van systeemroutes verkeer automatisch wordt gefaciliteerd voor uw implementatie, kunnen er gevallen zijn waarin u pakketten liever wilt routeren via een virtueel apparaat. U kunt hiertoe door de gebruiker gedefinieerde routes maken waarin u de volgende hop voor pakketstromen naar een specifiek subnet opgeeft, zodat de pakketten worden doorgestuurd naar uw virtuele apparaat. Daarvoor schakelt u Doorsturen via IP in voor de virtuele machine die u gebruikt als virtueel apparaat.

In de volgende afbeelding ziet u een voorbeeld van door de gebruiker gedefinieerde routes en doorsturen via IP om af te dwingen dat pakketten die van het ene naar het andere subnet worden verzonden, naar een virtueel apparaat op een derde subnet worden doorgestuurd.

![Systeemroutes in Azure](./media/virtual-networks-udr-overview/Figure2.png)

>[AZURE.IMPORTANT] Door de gebruiker gedefinieerde routes worden alleen toegepast op uitgaand verkeer van een subnet. U kunt bijvoorbeeld geen routes maken om op te geven hoe verkeer binnenkomt in een subnet vanaf internet. Het apparaat waarnaar u verkeer doorstuurt, mag zich niet bevinden in hetzelfde subnet als het subnet waarvan het verkeer afkomstig is. Maak altijd een apart subnet voor uw apparaten. 

## Bron routeren
Pakketten worden gerouteerd via een TCP/IP-netwerk op basis van een routetabel die op elk knooppunt van het fysieke netwerk is gedefinieerd. Een routetabel is een verzameling van afzonderlijke routes waarmee wordt bepaald waarnaar pakketten worden doorgestuurd op basis van het doel-IP-adres. Een route bestaat uit het volgende:

|Eigenschap|Beschrijving|Beperkingen|Overwegingen|
|---|---|---|---|
| Adresvoorvoegsel | De doel-CIDR waarop de route van toepassing is, zoals 10.1.0.0/16.|Dit moet een geldig CIDR-bereik zijn van adressen op internet, in een virtueel Azure-netwerk of in een on-premises datacentrum.|Zorg ervoor dat het **adresvoorvoegsel** niet het adres voor de **Nexthop-waarde** bevat, anders raken de pakketten in een lus van de bron naar de volgende hop zonder ooit hun doel te bereiken. |
| Volgend hoptype | Het type hop in Azure waarnaar het pakket moet worden doorgestuurd. | Dit moet een van de volgende waarden zijn: <br/> **Lokaal**. Hiermee geeft u het lokale virtuele netwerk op. Als u bijvoorbeeld twee subnetten, 10.1.0.0/16 en 10.2.0.0/16, in hetzelfde virtuele netwerk hebt, heeft de route voor elk subnet in de routetabel de waarde *Lokaal* voor de volgende hop. <br/> **VPN-gateway**. Hiermee geeft u een Azure S2S VPN-gateway op. <br/> **Internet**. Hiermee geeft u de standaard-internetgateway van de Azure-infrastructuur op. <br/> **Virtueel apparaat**. Hiermee geeft u een virtueel apparaat op dat u hebt toegevoegd aan uw virtuele Azure-netwerk. <br/> **NULL**. Hiermee geeft u een black hole op. Pakketten die worden doorgestuurd naar een black hole, worden helemaal niet doorgestuurd.| Overweeg het gebruik van een **NULL**-type als u het doorsturen van pakketten naar een bepaalde bestemming wilt stoppen. | 
| Waarde voor volgende hop | De waarde voor de volgende hop bevat het IP-adres waarnaar pakketten moeten worden doorgestuurd. Waarden voor de volgende hop zijn alleen toegestaan in routes waar het volgende hoptype *Virtueel apparaat* is.| Het IP-adres moet een bereikbaar IP-adres zijn. | Als het IP-adres een VM vertegenwoordigt, moet u [Doorsturen via IP](#IP-forwarding) in Azure voor de VM inschakelen. |

### Systeemroutes
Elk subnet dat in een virtueel netwerk wordt gemaakt, wordt automatisch gekoppeld aan een routetabel met de volgende systeemrouteregels:

- **Lokale VNnet-regel**: deze regel wordt automatisch gemaakt voor elk subnet in een virtueel netwerk. Hiermee wordt aangegeven dat er een directe koppeling tussen de VM's in het VNET is zonder tussenliggende volgende hop.
- **On-premises regel**: deze regel wordt toegepast op al het verkeer naar het on-premises adresbereik en maakt gebruik van VPN-gateway als bestemming voor de volgende hop.
- **Regel voor Internet**: deze regel wordt toegepast op al het verkeer naar internet en maakt gebruik van de internetgateway van de infrastructuur als bestemming voor de volgende hop voor al het verkeer naar internet.

### Door de gebruiker gedefinieerde routes
Voor de meeste omgevingen hebt u alleen de systeemroutes nodig die al zijn gedefinieerd door Azure. In bepaalde gevallen moet u echter een routetabel maken en een of meer routes toevoegen, zoals bij:

- Geforceerde tunneling naar internet via uw on-premises netwerk.
- Gebruik van virtuele apparaten in uw Azure-omgeving.

In de bovenstaande gevallen moet u een routetabel maken en er door de gebruiker gedefinieerde routes aan toevoegen. U kunt meerdere routetabellen hebben en dezelfde routetabel kan worden gekoppeld aan een of meer subnetten. Elk subnet kan slechts worden gekoppeld aan één routetabel. Voor alle VM's en cloudservices in een subnet wordt de routetabel gebruikt die is gekoppeld aan dat subnet.

Subnetten zijn afhankelijk van systeemroutes totdat er een routetabel aan het subnet gekoppeld is. Zodra een dergelijke koppeling bestaat, vindt routering plaats op basis van het LPM (Longest Prefix Match, langste overeenkomende voorvoegsel) van door de gebruiker gedefinieerde routes en systeemroutes. Als er meer dan één route met dezelfde overeenkomende LPM is, wordt een route geselecteerd op basis van de oorsprong in de volgende volgorde:

1. Door de gebruiker gedefinieerde route
1. BGP-route (wanneer u ExpressRoute gebruikt)
1. Systeemroute

Zie [Routes maken en Doorsturen via IP inschakelen in Azure](virtual-networks-udr-how-to.md#How-to-manage-routes) voor meer informatie over het maken van door de gebruiker gedefinieerde routes.

>[AZURE.IMPORTANT] Door de gebruiker gedefinieerde routes worden alleen toegepast op Azure VM's en cloudservices. Als u bijvoorbeeld een virtueel apparaat voor een firewall tussen uw on-premises netwerk en Azure wilt toevoegen, moet u een door de gebruiker gedefinieerde route voor uw Azure-routetabellen maken waarmee al het verkeer wordt doorgestuurd naar de on-premises adresruimte op het virtuele apparaat. Binnenkomend verkeer van de on-premises adresruimte wordt echter rechtstreeks via uw VPN-gateway of ExpressRoute-circuit doorgestuurd naar de Azure-omgeving, buiten het virtuele apparaat om.

### BGP-routes
Als u een ExpressRoute-verbinding tussen uw on-premises netwerk en Azure hebt, kunt u BGP configureren voor het propageren van routes van uw on-premises netwerk naar Azure. Deze BGP-routes worden op dezelfde manier gebruikt als systeemroutes en door de gebruiker gedefinieerde routes in elk Azure-subnet. Zie [Inleiding tot ExpressRoute](../expressroute/expressroute-introduction.md) voor meer informatie.

>[AZURE.IMPORTANT] U kunt uw Azure-omgeving configureren voor het gebruik van geforceerde tunneling via uw on-premises netwerk door een door de gebruiker gedefinieerde route voor subnet 0.0.0.0/0 te maken waarin de VPN-gateway wordt gebruikt als de volgende hop. Dit werkt alleen als u een VPN-gateway gebruikt en niet ExpressRoute. Voor ExpressRoute wordt geforceerde tunneling geconfigureerd via BGP.

## Doorsturen via IP
Zoals hierboven beschreven, worden door de gebruiker gedefinieerde routes meestal gemaakt om verkeer door te sturen naar een virtueel apparaat. Een virtueel apparaat is niets meer dan een virtuele machine waarop een toepassing wordt uitgevoerd om netwerkverkeer op een bepaalde manier af te handelen, zoals een firewall of een NAT-apparaat.

Deze VM op het virtuele apparaat moet in staat zijn om binnenkomend verkeer te ontvangen dat niet is geadresseerd aan zichzelf. Om met een VM verkeer te kunnen ontvangen dat is geadresseerd aan andere bestemmingen, moet u Doorsturen via IP inschakelen voor die VM. Dit is een Azure-instelling, niet een instelling in het gastbesturingssysteem.

## Volgende stappen

- Ontdek hoe u [routes maakt in het implementatiemodel van Resource Manager](virtual-network-create-udr-arm-template.md) en deze koppelt aan subnetten. 
- Ontdek hoe u [routes maakt in het klassieke implementatiemodel](virtual-network-create-udr-classic-ps.md) en deze koppelt aan subnetten.



<!--HONumber=Jun16_HO2-->


