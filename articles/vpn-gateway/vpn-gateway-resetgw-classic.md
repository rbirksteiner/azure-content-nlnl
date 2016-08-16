<properties
   pageTitle="Een Azure VPN-gateway opnieuw instellen | Microsoft Azure"
   description="Dit artikel helpt u bij het opnieuw instellen van uw Azure VPN-gateway. Het artikel is van toepassing op VPN-gateways die zijn gemaakt met het klassieke implementatiemodel."
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
   ms.date="04/26/2016"
   ms.author="cherylmc"/>

# Een Azure VPN-gateway opnieuw instellen met PowerShell


Dit artikel helpt u bij het opnieuw instellen van uw Azure VPN-gateway met PowerShell-cmdlets. Deze instructies zijn van toepassing op het klassieke implementatiemodel.  We hebben nog geen cmdlet of REST-API om een VPN-gateway opnieuw in te stellen voor virtuele netwerken die zijn gemaakt met het Resource Manager-implementatiemodel. Deze zijn in de maak. Wanneer u het virtuele netwerk in de Azure Portal weergeeft, kunt u zien of de VPN-gateway is gemaakt met het klassieke implementatiemodel. Virtuele netwerken die zijn gemaakt met het klassieke implementatiemodel worden weergegeven in het gedeelte Virtueel netwerk (klassiek) van de Azure Portal.

Het opnieuw instellen van de Azure VPN-gateway is handig als u cross-premises VPN-connectiviteit verliest in een of meer S2S-VPN-tunnels. In een dergelijke situatie functioneren al uw on-premises VPN-apparaten naar behoren, maar kunnen ze geen IPSec-tunnels tot stand brengen met de Azure VPN-gateways. Als u de cmdlet *Reset-AzureVNetGateway* gebruikt, wordt de gateway opnieuw opgestart en worden de cross-premises configuraties opnieuw toegepast. De gateway blijft hetzelfde openbare IP-adres gebruiken. Dat betekent dat u de VPN-routerconfiguratie niet hoeft bij te werken met een nieuw openbaar IP-adres voor de Azure VPN-gateway.  


Controleer voordat u de gateway opnieuw instelt de hieronder vermelde essentiële items voor elke S2S-VPN-tunnel (site-naar-site) met IPsec. Als items niet overeenkomen, wordt de verbinding met S2SVPN-tunnels verbroken. Door de configuraties voor uw on-premises en Azure VPN-gateways te controleren en te corrigeren, voorkomt u dat u onnodig opnieuw moet opstarten en dat de andere werkende verbindingen via de gateways worden verbroken.

Controleer de volgende items voordat u de gateway opnieuw instelt.

- Controleer of de internet-IP-adressen (VIP's) voor zowel de Azure VPN-gateway als de on-premises VPN-gateway correct zijn geconfigureerd in zowel het Azure- als het lokale VPN-beleid.
- De vooraf gedeelde sleutel moet hetzelfde zijn in de Azure- en de on-premises VPN-gateway.
- Als u een specifieke IPsec/IKE-configuratie, zoals versleuteling, hash-algoritmen en PFS (Perfect Forward Secrecy), toepast, controleer dan of de Azure- en on-premises VPN-gateway dezelfde configuratie hebben.


## Een VPN-gateway opnieuw instellen met PowerShell

De PowerShell-cmdlet voor het opnieuw instellen van Azure VPN-gateway is *Reset-AzureVNetGateway*. Elke Azure VPN-gateway bestaat uit twee VM-instanties die worden uitgevoerd in een actief/stand-byconfiguratie. Wanneer de opdracht is uitgevoerd, wordt de huidige instantie van de Azure VPN-gateway onmiddellijk opnieuw opgestart. Er is een korte onderbreking tijdens de failover van de actieve instantie (die opnieuw wordt opgestart) naar de stand-by-instantie. Deze onderbreking zou niet langer dan een minuut moeten duren. 

In het volgende voorbeeld wordt de Azure VPN-gateway voor het virtuele netwerk, ContosoVNet, opnieuw ingesteld.
 
        Reset-AzureVNetGateway –VnetName “ContosoVNet” 

        Error          :
        HttpStatusCode : OK
        Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
        Status         : Successful
        RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
        StatusCode     : OK


Als de verbinding na de eerste keer opnieuw opstarten niet is hersteld, voert u deze opdracht opnieuw uit om de tweede VM-instantie (de nieuwe actieve gateway) opnieuw op te starten. Als de twee keer opnieuw opstarten na elkaar worden aangevraagd, duurt het iets langer omdat beide VM-instanties (actieve en stand-by) opnieuw worden opgestart. In dat geval zal de VPN-verbinding twee tot vier minuten worden onderbroken, totdat de virtuele machines opnieuw zijn opgestart.

Als u na twee keer opnieuw opstarten nog steeds problemen hebt met de cross-premises connectiviteit, open dan een ondersteuningsticket vanuit de klassieke Azure Portal om contact op te nemen met de ondersteuning van Microsoft Azure.

## Volgende stappen
    
Zie de [PowerShell reference](https://msdn.microsoft.com/library/azure/mt270366.aspx) (Naslaginformatie over PowerShell) voor meer informatie over deze cmdlet.









<!--HONumber=Jun16_HO2-->


