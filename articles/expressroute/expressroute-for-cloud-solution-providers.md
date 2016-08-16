<properties
   pageTitle="Azure ExpressRoute voor Cloud Solution Providers | Microsoft Azure"
   description="Dit artikel bevat informatie voor cloudserviceproviders die Azure-services en ExpressRoute in hun aanbod willen opnemen."
   documentationCenter="na"
   services="expressroute"
   authors="richcar"
   manager="josha"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/20/2016"
   ms.author="richcar"/>

# ExpressRoute voor Cloud Solution Providers (CSP)

Microsoft biedt grootschalige services waarmee traditionele leveranciers en distributeurs (CSP) snel nieuwe services en oplossingen voor uw klanten kunnen inrichten, zonder dat ze hoeven te investeren in de ontwikkeling van deze nieuwe services. Microsoft biedt programma's en API's waarmee de Cloud Solution Provider (CSP) Microsoft Azure-resources kan beheren namens uw klanten. Zo kan de Cloud Solution Provider (CSP) deze nieuwe services rechtstreeks beheren. Een van deze resources is ExpressRoute. Met ExpressRoute kan de CSP verbinding maken tussen de bestaande resources van de klant en de Azure-services. ExpressRoute is een zeer snelle persoonlijke communicatiekoppeling naar de services in Azure. 

ExpresRoute bestaat uit twee circuits voor hoge beschikbaarheid die zijn gekoppeld aan het abonnement of de abonnementen van één klant. Deze kunnen niet worden gedeeld door meerdere klanten. Elk circuit moet worden beëindigd in een andere router om de hoge beschikbaarheid te houden.

>[AZURE.NOTE] Er zijn limieten voor de bandbreedte en verbinding in ExpressRoute, wat betekent dat er bij grote/complexe implementaties meerdere ExpressRoute-circuits per klant zijn vereist.

Microsoft Azure biedt een toenemend aantal services dat u aan uw klanten kunt aanbieden.  Als u optimaal gebruik wilt maken van deze services, moet u ExpressRoute-verbindingen gebruiken om toegang met hoge snelheid en lage latentie te bieden tot de Microsoft Azure-omgeving.

## Microsoft Azure-beheer
Microsoft biedt CSP's met API's voor het beheren van de Azure-klantabonnementen aan de hand van programmatische integratie met uw eigen servicebeheersystemen. U vindt de ondersteunde beheermogelijkheden [hier](https://msdn.microsoft.com/library/partnercenter/dn974944.aspx).

## Beheer van Microsoft Azure-resources
Het contract dat u met uw klant hebt, bepaalt hoe het abonnement wordt beheerd. De CSP kan het maken en het onderhouden van resources rechtstreeks beheren, maar de klant kan ook zelf de controle behouden over het Microsoft Azure-abonnement en de Azure-resources naar behoefte maken. Als uw klant het maken van resources in het Microsoft Azure-abonnement zelf beheert, gebruikt hij of zij het model 'Doorverbinden' of het model 'Leiden naar'. Deze modellen worden gedetailleerd beschreven in de volgende gedeelten.  

### Het model 'Doorverbinden'

![alternatieve tekst](./media/expressroute-for-cloud-solution-providers/connect-through.png)  

In het model 'Doorverbinden' maakt de CSP een rechtstreekse verbinding tussen uw datacenter en het Azure-abonnement van uw klant. De rechtstreekse verbinding wordt gemaakt met behulp van ExpressRoute en verbindt uw netwerk met Azure. De klant maakt vervolgens verbinding met uw netwerk. Dit scenario vereist dat de klant het CSP-netwerk gebruikt voor toegang tot de Azure-services. 

Als de klant andere Azure-abonnementen heeft die niet door u worden beheerd, gebruiken deze het openbare internet of een eigen privéverbinding om verbinding te maken met de services die zijn ingericht met het abonnement dat niet door de CSP wordt beheerd. 

Voor CSP’s die Azure-services beheren, wordt ervan uitgegaan dat de CSP een eerder gemaakt archief voor de klantidentiteit heeft. Dit wordt gerepliceerd in Azure Active Directory voor het beheren van zijn of haar CSP-abonnement via AOBO (Administrate-On-Behalf-Of). Situaties waarvoor dit scenario geschikt is, zijn bijvoorbeeld: wanneer een partner of serviceprovider al een bestaande relatie met de klant heeft, de klant al gebruikmaakt van de services van de provider of de partner een combinatie van door de provider gehoste en door Azure gehoste oplossingen wil bieden voor meer flexibiliteit en om tegemoet te komen aan klantwensen waaraan niet alleen door de CSP kan worden voldaan. Dit model wordt weergegeven in onderstaande **afbeelding**.

![alternatieve tekst](./media/expressroute-for-cloud-solution-providers/connect-through-model.png)

### Het model 'Verbinden met'

![alternatieve tekst](./media/expressroute-for-cloud-solution-providers/connect-to.png)

In het model 'Verbinden met' maakt de serviceprovider een rechtstreekse verbinding tussen het datacenter van de klant en het door de CSP ingerichte Azure-abonnement. Hiervoor wordt ExpressRoute gebruikt via het (klant)netwerk van de klant.

>[AZURE.NOTE] Voor ExpressRoute moet de klant het ExpressRoute-circuit maken en onderhouden.  

In dit scenario moet de klant rechtstreeks verbinding maken via een klantnetwerk voor toegang tot het door de CSP beheerde Azure-abonnement. Hiervoor moet een rechtstreekse netwerkverbinding worden gebruikt die is gemaakt door, eigendom is van en geheel of gedeeltelijk wordt beheerd door de klant. Bij deze klanten wordt ervan uitgegaan dat de provider nog geen archief voor de klantidentiteit heeft gemaakt en dat de provider de klant helpt bij het repliceren van het huidige identiteitsarchief in Azure Active Directory voor het beheren van zijn/haar abonnement via AOBO. Situaties waarvoor dit scenario geschikt is, zijn bijvoorbeeld: wanneer een partner of serviceprovider al een bestaande relatie met de klant heeft, de klant al gebruikmaakt van de services van de provider of de partner services wil bieden die alleen zijn gebaseerd op in Azure gehoste oplossingen, zonder dat hiervoor een bestaand datacenter of een bestaande infrastructuur van de provider nodig is.

![alternatieve tekst](./media/expressroute-for-cloud-solution-providers/connect-to-model.png)

De keuze tussen deze twee opties is gebaseerd op de behoeften van uw klant en uw huidige behoefte om Azure-services te leveren. Voor gedetailleerde informatie over deze modellen en het bijbehorende op rollen gebaseerde toegangsbeheer, de netwerken en de ontwerppatronen voor de identiteit volgt u de volgende links:
-   **RBAC (op rollen gebaseerd toegangsbeheer)**: RBAC is gebaseerd op Azure Active Directory.  Klik [hier](../active-directory/role-based-access-control-configure.md) voor meer informatie over Azure RBAC. 
-   **Netwerken**: bevat informatie over de verschillende netwerkonderwerpen in Microsoft Azure.
-   **Azure Active Directory (AAD)**: AAD biedt het identiteitsbeheer voor Microsoft Azure en SaaS-toepassingen van derden. Klik [hier](https://azure.microsoft.com/documentation/services/active-directory/) voor meer informatie over Azure AD.  


## Netwerksnelheden
ExpressRoute ondersteunt netwerksnelheden van 50 Mb/s tot 10 Gb/s. Dit geeft klanten de mogelijkheid om de hoeveelheid bandbreedte aan te schaffen die nodig is voor hun specifieke omgeving.

>[AZURE.NOTE] De bandbreedte van het netwerk kan naar behoefte worden verhoogd zonder dat de communicatie wordt onderbroken. Als u de snelheid van het netwerk wilt verlagen, moet het circuit echter worden verbroken en opnieuw worden gemaakt met de lagere netwerksnelheid.  

ExpressRoute ondersteunt de verbinding van meerdere vNets met een enkel ExpressRoute-circuit voor beter gebruik van de verbindingen met hogere snelheid. Een enkel ExpressRoute-circuit kan worden gedeeld met meerdere Azure-abonnementen die eigendom zijn van dezelfde klant.

## ExpressRoute configureren
ExpressRoute kan worden geconfigureerd voor ondersteuning van drie typen verkeer ([routeringsdomeinen](#ExpressRoute-routing-domains)) via een enkel ExpressRoute-circuit. Dit verkeer is onderverdeeld in Microsoft-peering, openbare Azure-peering en privépeering. U kunt ervoor kiezen om één of alle soorten verkeer te verzenden via een enkel ExpressRoute-circuit of meerdere ExpressRoute-circuits, afhankelijk van de grootte van het ExpressRoute-circuit en de door uw klant vereiste isolatie. De beveiliging van uw klant mag niet toestaan dat openbaar verkeer en privéverkeer via hetzelfde circuit lopen.

### Het model 'Doorverbinden'
In een 'Doorverbinden'-configuratie bent u verantwoordelijk voor de volledige fundering van de netwerken. Zo kunt u de resources in het datacenter van uw klant verbinden met de abonnementen die in Azure worden gehost. Al uw klanten die de mogelijkheden van Azure willen benutten, hebben een eigen ExpressRoute-verbinding nodig. Deze wordt door u beheerd. U gebruikt de methodes die de klant zou gebruiken om het ExpressRoute-circuit aan te schaffen. U volgt de stappen die worden beschreven in het artikel [ExpressRoute-werkstromen](./expressroute-workflows.md), voor het inrichten van het circuit en de statussen van het circuit. Vervolgens configureert u de BGP-routes (Border Gateway Protocol) om het verkeer tussen het on-premises netwerk en Azure vNet te beheren.

### Het model 'Verbinden met'
In een 'Verbinden met'-configuratie heeft uw klant al een bestaande verbinding met Azure of wordt er verbinding gemaakt met de internetserviceprovider die ExpressRoute rechtstreeks vanuit het datacenter van de klant (in plaats van via uw datacenter) koppelt aan Azure. Uw klant moet de stappen zoals hiervoor beschreven voor het model 'Doorverbinden' volgen om het inrichtingsproces te starten. Wanneer het circuit tot stand is gebracht, moet uw klant de on-premises routers configureren, zodat deze toegang hebben tot uw netwerk en Azure vNets.

U kunt helpen bij het instellen van de verbinding en het configureren van de routes om de resources in uw datacenter(s) te laten communiceren met de clientresources in uw datacenter of met de resources die in Azure worden gehost.

## ExpressRoute-routeringsdomeinen
ExpressRoute biedt drie routeringsdomeinen: openbare peering, privépeering en Microsoft-peering. Alle routeringsdomeinen zijn geconfigureerd met identieke routers in de actief/actief-configuratie voor hoge beschikbaarheid. Klik [hier](./expressroute-circuit-peerings.md) voor meer informatie over de ExpressRoute-routeringsdomeinen.

U kunt aangepaste routefilters definiëren, zodat alleen route(s) die u wilt toestaan of nodig hebt, zijn toegestaan. Voor meer informatie of om te zien hoe u deze wijzigingen aanbrengt, raadpleegt u het artikel: [Routering voor een ExpressRoute-circuit maken en wijzigen met behulp van PowerShell ](./expressroute-howto-routing-classic.md) voor meer informatie over routeringsfilters.

>[AZURE.NOTE] Voor Microsoft-peering en openbare peering moet verbinding worden gemaakt via een openbaar IP-adres dat eigendom is van de klant of de CSP. Deze verbinding moet voldoen aan alle gedefinieerde regels. Zie voor meer informatie de pagina [Vereisten voor ExpressRoute](expressroute-prerequisites.md).  

## Routering
ExpressRoute maakt verbinding met de Azure-netwerken via de gateway van Azure Virtual Network. Netwerkgateways bieden routering voor virtuele netwerken in Azure.

Als u virtuele netwerken in Azure maakt, wordt er ook een standaardrouteringstabel voor het vNet gemaakt om verkeer van/naar de subnetten van het vNet te leiden. Als de standaardrouteringstabel onvoldoende is voor de oplossing, kunnen er aangepaste routes worden gemaakt om uitgaand verkeer naar aangepaste toestellen te routeren of om routes naar specifieke subnetten of externe netwerken te blokkeren.

### Standaardroutering
De standaardroutetabel bevat de volgende routes:

- Routering binnen een subnet
- Subnet-naar-subnet binnen het virtuele netwerk
- Naar internet
- Virtueel netwerk-naar-virtueel netwerk via VPN-gateway
- Virtueel netwerk-naar-on-premises netwerk via een VPN- of ExpressRoute-gateway

![alternatieve tekst](./media/expressroute-for-cloud-solution-providers/default-routing.png)  

### Door de gebruiker gedefinieerde routering
Door de gebruiker gedefinieerde routes geven u controle over het uitgaande verkeer vanuit het toegewezen subnet naar andere subnetten in het virtuele netwerk of via een van de andere vooraf gedefinieerde gateways (ExpressRoute; internet of VPN). De standaardrouteringstabel kan worden vervangen door een door de gebruiker gedefinieerde routeringstabel. Hiermee wordt de standaardrouteringstabel vervangen door aangepaste routes. Met door de gebruiker gedefinieerde routering kunnen klanten specifieke routes maken voor apparaten, zoals firewalls of inbraakdetectieapparaten. Daarnaast kunnen klanten de toegang tot specifieke subnetten blokkeren vanuit het subnet dat de door de gebruiker gedefinieerde route host. Klik [hier](../virtual-network/virtual-networks-udr-overview.md) voor een overzicht van door de gebruiker gedefinieerde routes. 

## Beveiliging
Afhankelijk van het model dat uw klant gebruikt ('Verbinden met' of 'Doorverbinden'), kan hij of zij de beveiligingsregels in het vNet definiëren of deze regels doorgeven aan de CSP, zodat deze de vNets kan definiëren. De volgende beveiligingscriteria kunnen worden gedefinieerd:

1.  **Klantisolatie**: het Azure-platform biedt klantisolatie door de klant-ID en vNet-gegevens op te slaan in een beveiligde database. Deze wordt gebruikt om het verkeer van elke klant in te kapselen in een GRE-tunnel.
2.  De regels voor de **netwerkbeveiligingsgroep (NSG)** worden gebruikt om te definiëren welk verkeer van en naar de subnetten binnen vNets in Azure mag worden geleid. Standaard bevat de NSG regels voor het blokkeren van verkeer van internet naar het vNet en regels voor het toestaan van verkeer binnen een vNet. Klik [hier](https://azure.microsoft.com/blog/network-security-groups/) voor meer informatie over netwerkbeveiligingsgroepen.
3.  **Geforceerde tunneling**: dit is een optie waarmee u verkeer vanuit Azure naar internet kunt omleiden via de ExpressRoute-verbinding met het on-premises datacenter. Klik [hier](./expressroute-routing.md#advertising-default-routes) voor meer informatie over geforceerde tunneling.  

4.  **Versleuteling**: hoewel de ExpressRoute-circuits zijn toegewezen aan een specifieke klant, is het mogelijk dat de netwerkprovider wordt geschonden, waardoor een indringer het pakketverkeer kan zien. Een klant of CSP kan dit potentiële probleem oplossen door het verkeer te versleutelen dat gebruikmaakt van de verbinding. Dit kan worden gedaan door een beleid voor de IPSec-tunnelmodus te definiëren voor al het verkeer tussen de on-premises resources en de Azure-resources (raadpleeg de optionele tunnelmodus IPSec voor klant 1 in bovenstaande afbeelding 5: ExpressRoute-beveiliging). De tweede optie is het gebruik van een firewallapparaat op elk eindpunt van het ExpressRoute-circuit. Hiervoor moeten aan beide uiteinden virtuele firewallmachines of firewallapparaten van derden worden geïnstalleerd om het verkeer te versleutelen dat gebruikmaakt van het ExpressRoute-circuit.


![alternatieve tekst](./media/expressroute-for-cloud-solution-providers/expressroute-security.png)  

## Volgende stappen
De Cloud Solution Provider-service biedt een manier om uw waarde voor uw klanten te verhogen, zonder dat u hiervoor dure infrastructuur en capaciteit hoeft aan te schaffen. U behoudt daarbij uw positie als de belangrijkste provider voor uitbesteding. Met de CSP-API kunt u zorgen voor naadloze integratie met Microsoft Azure, zodat u het beheer van Microsoft Azure binnen uw bestaande beheerframeworks kunt integreren.  

Voor meer informatie klikt u op de volgende koppelingen:

[Microsoft Cloud Solution Provider-programma](https://partner.microsoft.com/en-US/Solutions/cloud-reseller-overview).  
[Bereid u voor om zaken te doen als een Cloud Solution Provider](https://partner.microsoft.com/en-us/solutions/cloud-reseller-pre-launch).  
[Microsoft Cloud Solution Provider-resources](https://partner.microsoft.com/en-us/solutions/cloud-reseller-resources).



<!--HONumber=Jun16_HO2-->


