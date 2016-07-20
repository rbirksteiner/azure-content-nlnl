


## Voordelen van de integratie van Compute, netwerk en opslag in het Azure Resource Manager-implementatiemodel

Met het Azure Resource Manager-implementatiemodel kunt u eenvoudig gebruikmaken van vooraf samengestelde toepassingssjablonen of zelf een toepassingssjabloon maken om reken-, netwerk- en opslagresources te implementeren en te beheren op Azure. In deze sectie behandelen we de voordelen van de implementatie van resources via het Azure Resource Manager-implementatiemodel.

-   Moeilijk wordt makkelijk -- U kunt bouwen, integreren en samenwerken in complexe toepassingen waarin u het volledige assortiment van Azure-resources (zoals Websites, SQL Databases, Virtual Machines of Virtual Networks) uit een deelbaar sjabloonbestand kunt opnemen
-   Flexibiliteit met herhaalbare implementaties voor ontwikkeling, devOps en systeembeheerders wanneer u hetzelfde sjabloonbestand gebruikt
-   Diepe integratie van VM-extensies (aangepaste scripts, DSC, Chef, Puppet enzovoort) met de Azure Resource Manager in een sjabloonbestand voor eenvoudige indeling van installatieconfiguraties in de VM
-   Labels en de doorgifte van facturering van deze labels voor reken-, netwerk- en opslagresources definiëren
-   Eenvoudig en nauwkeurig beheer van toegang tot resources met op rollen gebaseerd toegangsbeheer (RBAC) van Azure
-   Vereenvoudigde upgrade-/updatemogelijkheden door het wijzigen van de oorspronkelijke sjabloon en deze vervolgens opnieuw te implementeren


## Verbeteringen van de Azure Resource Manager-API's voor reken-, netwerk- en opslagfunctionaliteit

Naast de hierboven genoemde voordelen zijn er een aantal belangrijke prestatieverbeteringen in de API's vrijgegeven:

-   Brede en parallelle implementatie van virtuele machines
-   Ondersteuning voor 3 foutdomeinen in beschikbaarheidssets
-   Verbeterde extensie voor aangepaste scripts, waarmee scripts van elke openbaar toegankelijke aangepaste URL kunnen worden gespecificeerd
- Integratie van virtuele machines met de Azure Sleutelkluis voor uiterst veilige opslag en persoonlijke implementatie van geheimen van [FIPS-gevalideerde](http://wikipedia.org/wiki/FIPS_140-2) [hardwarebeveiligingsmodules](http://wikipedia.org/wiki/Hardware_security_module)
-   De bouwstenen van netwerken via API's, waarmee klanten gecompliceerde toepassingen met netwerkinterfaces, load balancers en virtuele netwerken kunnen maken
-   De nieuwe netwerkinterfaces zorgen ervoor dat een gecompliceerde netwerkconfiguratie kan worden ondersteund en hergebruikt voor virtuele machines
-   Load balancers als een eerste-klas resource zorgen ervoor dat IP-adressen kunnen worden toegewezen
-   Gedetailleerde virtuele netwerk-API's maken het beheer van afzonderlijke virtuele netwerken eenvoudiger

## Conceptuele verschillen dankzij de introductie van nieuwe API's

In deze sectie bekijken we enkele van de belangrijkste conceptuele verschillen tussen de huidige op XML gebaseerde API's en op JSON gebaseerde API's voor reken-, netwerk- en opslagfunctionaliteit die beschikbaar zijn via Azure Resource Manager.

 Item | Azure Service Management (XML-indeling)    | Reken-, netwerk- en opslagproviders (JSON-indeling)
 ---|---|---
| Cloudservice voor virtuele machines |  Cloudservice was een container voor de opslag van de virtuele machines waarvoor de beschikbaarheid van het platform en taakverdeling vereist was. | Cloudservice is niet langer een object dat is vereist voor het maken van een virtuele machine met het nieuwe model. |
| Beschikbaarheidssets | De beschikbaarheid van het platform werd aangegeven door de configuratie van dezelfde "AvailabilitySetName" op de virtuele machines. Het maximumaantal foutdomeinen was 2. | Beschikbaarheidsset is een resource die beschikbaar wordt gesteld door Microsoft.Compute-provider. Virtuele machines die uiterst beschikbaar moeten zijn, worden opgenomen in de beschikbaarheidsset. Het maximumaantal foutdomeinen is nu 3. |
| Affiniteitsgroepen | Voor het maken van virtuele netwerken waren affiniteitsgroepen vereist. Door de introductie van regionale virtuele netwerken is dit echter niet meer nodig. |Het concept van affiniteitsgroepen bestaat niet meer in de API's die via Azure Resource Manager beschikbaar worden gesteld. |
| Taakverdeling    | Bij het maken van een cloudservice wordt een impliciete load balancer voor de geïmplementeerde virtuele machines aangemaakt. | De load balancer is een resource die beschikbaar wordt gesteld door de Microsoft.Compute-provider. De primaire netwerkinterface van de virtuele machines waarvoor de taken moeten worden verdeeld moet verwijzen naar de load balancer. Load balancers kunnen intern of extern zijn. [Meer informatie.](../articles/resource-groups-networking.md) |
|Virtueel IP-adres | Cloud Services krijgt een standaard VIP (virtueel IP-adres) wanneer een VM aan een cloudservice wordt toegevoegd. Het virtuele IP-adres is het adres dat is gekoppeld aan de impliciete load balancer.   | Het openbare IP-adres is een resource die beschikbaar wordt gesteld door de Microsoft.Compute-provider. Een openbaar IP-adres kan statisch (gereserveerd) of dynamisch zijn. Dynamische openbare IP-adressen kunnen worden toegewezen aan een load balancer. Openbare IP-adressen kunnen worden beveiligd met beveiligingsgroepen. |
|Gereserveerd IP-adres|   U kunt een IP-adres in Azure reserveren en dit koppelen aan een cloudservice om ervoor te zorgen dat het IP-adres is vergrendeld.   | Een openbaar IP-adres kan in de statische modus worden gemaakt en biedt dezelfde mogelijkheden als een gereserveerd IP-adres. Statische openbare IP-adressen kunnen momenteel alleen worden toegewezen aan een load balancer. |
|Openbaar IP-adres (PIP) per VM | Openbare IP-adressen kunnen ook rechtstreeks aan een VM worden gekoppeld. | Het openbare IP-adres is een resource die beschikbaar wordt gesteld door de Microsoft.Compute-provider. Een openbaar IP-adres kan statisch (gereserveerd) of dynamisch zijn. Echter, momenteel worden alleen dynamische openbare IP-adressen toegewezen aan een netwerkinterface om een openbaar IP-adres per VM op te halen. |
|Eindpunten| Invoereindpunten moesten eerder op een virtuele machine worden geconfigureerd voor open connectiviteit voor bepaalde poorten. Een van de algemene modi voor het maken van verbinding met virtuele machines wordt gerealiseerd door het instellen van invoereindpunten. | Inkomende NAT-regels kunnen op load balancers worden geconfigureerd om eindpunten in te schakelen op bepaalde poorten voor verbinding met de VM's. |
|DNS-naam| Een cloudservice krijgt een impliciete, globaal unieke DNS-naam. Bijvoorbeeld: `mycoffeeshop.cloudapp.net`. | DNS-namen zijn optionele parameters die voor de resource van een openbaar IP-adres kunnen worden opgegeven. De FQDN wordt weergegeven in de volgende indeling - `<domainlabel>.<region>.cloudapp.azure.com`. |
|Netwerkinterfaces | Primaire en secundaire netwerkinterface en de bijbehorende eigenschappen werden gedefinieerd als de netwerkconfiguratie van een virtuele machine. | De netwerkinterface is een resource die beschikbaar wordt gesteld door de Microsoft.Compute-provider. De levenscyclus van de netwerkinterface is niet gekoppeld aan een virtuele machine. |

## Aan de slag met Azure-sjablonen voor virtuele machines

U kunt aan de slag met de Azure-sjablonen door gebruik te maken van de verschillende beschikbare hulpprogramma's voor de ontwikkeling en implementatie naar het platform.

### Azure Portal

Azure Portal biedt nog altijd de optie om virtuele machines te implementeren met het klassieke implementatiemodel en tegelijkertijd virtuele machines te implementeren met het Resource Manager-implementatiemodel. De Azure Portal biedt ook de mogelijkheid om aangepaste sjabloonimplementaties te maken.

### Azure PowerShell

Azure PowerShell heeft twee modi van implementatie – de **AzureServiceManagement**-modus en de **AzureResourceManager**-modus.  De AzureResourceManager-modus bevat nu ook de cmdlets voor het beheer van virtuele machines, virtuele netwerken en opslagaccounts. [Hier](../articles/powershell-azure-resource-manager.md) vindt u meer informatie.

### Azure CLI

De Azure-opdrachtregelinterface (Azure CLI) heeft twee implementatiemodi – de **AzureServiceManagement**-modus en de **AzureResourceManager**-modus. De AzureResourceManager-modus bevat nu ook opdrachten om virtuele machines, virtuele netwerken en opslagaccounts te beheren. [Hier](../articles/xplat-cli-azure-resource-manager.md) vindt u meer informatie.

### Visual Studio

Met de meest recente release van Azure SDK voor Visual Studio kunt u virtuele machines en complexe toepassingen rechtstreeks vanuit Visual Studio ontwerpen en implementeren. Visual Studio biedt de mogelijkheid voor het implementeren van een vooraf samengestelde lijst met sjablonen. Maar u kunt ook met een lege sjabloon beginnen.

### REST-API’s

[Hier](https://msdn.microsoft.com/library/azure/dn790568.aspx) vindt u de gedetailleerde documentatie over REST API voor providers van reken-, netwerk- en opslagresources.

## Veelgestelde vragen

**Kan ik met de nieuwe Azure Resource Manager een virtuele machine maken voor implementatie in een virtueel netwerk of een opslagaccount dat is gemaakt met behulp van de Azure Service Management-API's?**

Dit wordt momenteel niet ondersteund. U kunt de nieuwe Azure Resource Manager-API's niet gebruiken om een virtuele machine te implementeren in een virtueel netwerk dat is gemaakt met de Service Management-API's.

**Kan ik met de nieuwe Azure Resource Manager-API's een virtuele machine maken van een gebruikersinstallatiekopie die is gemaakt met de Azure Service Management-API's?**

Dit wordt momenteel niet ondersteund. U kunt wel de VHD-bestanden kopiëren vanaf een opslagaccount dat is gemaakt met de Service Management-API's en deze naar een nieuw account kopiëren dat is gemaakt met behulp van de nieuwe Azure Resource Manager-API's.

**Wat zijn de gevolgen voor het quotum voor mijn abonnement?**

De quota voor de virtuele machines, virtuele netwerken en opslagaccounts die zijn gemaakt met behulp van de nieuwe Azure Resource Manager-API's zijn gescheiden van de quota die u momenteel hebt. Elk abonnement krijgt nieuwe quota voor het maken van de resources met behulp van de nieuwe API's. U vindt [hier](../articles/azure-subscription-service-limits.md) meer informatie over de extra quota.

**Kan ik mijn geautomatiseerde scripts voor het inrichten van virtuele machines, virtuele netwerken, opslagaccounts enzovoort via de nieuwe Azure Resource Manager-API's blijven gebruiken?**

De automatisering en scripts die u hebt gemaakt blijven werken voor de bestaande virtuele machines en virtuele netwerken die zijn gemaakt in de Azure Service Management-modus. De scripts moeten echter worden bijgewerkt voor het gebruik van het nieuwe schema voor het maken van dezelfde resources via de nieuwe Azure Resource Manager-modus.

**Kunnen de virtuele netwerken die zijn gemaakt met behulp van de nieuwe Azure Resource Manager-API's worden verbonden met mijn Express Route-circuit?**

Dit wordt momenteel niet ondersteund. U kunt de virtuele netwerken die zijn gemaakt met behulp van de nieuwe Azure Resource Manager-API's niet verbinden met een Express Route-circuit. Dit wordt in de toekomst wel ondersteund.

**Waar vind ik voorbeelden van Azure Resource Manager-sjablonen?**

U vindt een uitgebreide set startsjablonen voor Azure Resource Manager op [Azure-snelstartsjablonen](https://azure.microsoft.com/documentation/templates/).



<!--HONumber=Jun16_HO2-->


