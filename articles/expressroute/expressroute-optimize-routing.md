<properties
   pageTitle="ExpressRoute-routering optimaliseren | Microsoft Azure"
   description="Deze pagina bevat gedetailleerde informatie over het optimaliseren van routering wanneer een klant meerdere ExpressRoute-circuits heeft die Microsoft verbinden met het bedrijfsnetwerk van de klant."
   documentationCenter="na"
   services="expressroute"
   authors="charwen"
   manager="carmonm"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/03/2016"
   ms.author="charwen"/>

# ExpressRoute-routering optimaliseren
Als u meerdere ExpressRoute-circuits hebt, hebt u meer dan één pad om verbinding te maken met Microsoft. Dat betekent dat suboptimale routering kan plaatsvinden, met andere woorden, dat verkeer soms een langer pad aflegt om Microsoft te bereiken en Microsoft om uw netwerk te bereiken. Hoe langer het netwerkpad, hoe groter de latentie. Latentie heeft een directe invloed op toepassingsprestaties en gebruikerservaring. In dit artikel wordt dit probleem geïllustreerd en wordt uitgelegd hoe u routering optimaliseert met behulp van de standaardrouteringstechnologieën.

## Voorbeeld 1 van suboptimale routering
We gaan het routeringsprobleem bekijken aan de hand van een voorbeeld. Stel, u hebt twee kantoren in de VS, één in Los Angeles en één in New York. Uw kantoren zijn aangesloten op een Wide Area Network (WAN). Dit kan uw eigen backbone-netwerk zijn of het IP VPN van uw serviceprovider. U hebt twee ExpressRoute-circuits, één in VS - west en één in VS - oost. Deze zijn ook aangesloten op het WAN. U hebt uiteraard twee paden om verbinding te maken met het Microsoft-netwerk. Stel nu dat u zowel in VS - west als in VS - oost een Azure-implementatie hebt (bijvoorbeeld Azure App Service). Het is uw bedoeling om uw gebruikers in Los Angeles te verbinden met Azure VS - west en uw gebruikers in New York met Azure VS - oost, omdat uw servicebeheerder adverteert dat gebruikers in elk kantoor voor optimale ervaringen gebruik kunnen maken van de Azure-services die zich zo dichtbij mogelijk bevinden. Helaas werkt dit plan prima voor de gebruikers aan de oostkust maar niet voor de gebruikers aan de westkust. Dit heeft de volgende oorzaak. In elk ExpressRoute-circuit adverteren we aan u zowel het voorvoegsel in Azure VS - oost (23.100.0.0/16) als het voorvoegsel in Azure VS - west (13.100.0.0/16). Als u niet weet welk voorvoegsel bij welke regio hoort, kunt u ze niet op verschillende manieren behandelen. Misschien 'denkt' uw WAN-netwerk dat beide voorvoegsels zich dichter bij VS - oost bevinden dan bij VS - west, en worden de gebruikers van beide kantoren daarom omgeleid naar het ExpressRoute-circuit in VS - oost. Dit leidt tot veel ontevreden gebruikers in het kantoor in Los Angeles.

![](./media/expressroute-optimize-routing/expressroute-case1-problem.png)

### Oplossing: gebruik BGP-community's
Om routering te optimaliseren voor de gebruikers op beide kantoren, moet u weten welk voorvoegsel van Azure VS - west is en welk van Azure VS - oost. Deze informatie wordt versleuteld met [BGP-communitywaarden](expressroute-routing.md). We hebben aan elke Azure-regio een unieke BGP-communitywaarde toegewezen, bijvoorbeeld '12076:51004' voor VS - oost en '12076:51006' voor VS - west. Nu u weet welk voorvoegsel bij welke Azure-regio hoort, kunt u configureren welk ExpressRoute-circuit de voorkeur heeft. Omdat we het BGP gebruiken om routeringsinformatie uit te wisselen, kunt u de routering beïnvloeden met de lokale voorkeur van het BGP. In ons voorbeeld kunt u in VS - west aan 13.100.0.0/16 een hogere lokale-voorkeurswaarde toekennen dan in VS - oost, en in VS - oost kunt u aan 23.100.0.0/16 een hogere lokale-voorkeurswaarde toekennen dan in VS - west. Als beide paden naar Microsoft beschikbaar zijn, zorgt deze configuratie ervoor dat uw gebruikers in Los Angeles het ExpressRoute-circuit in VS - west gebruiken om verbinding te maken met Azure VS - west, en uw gebruikers in New York de ExpressRoute in VS - oost nemen naar Azure VS - oost. Routering is nu aan beide zijden geoptimaliseerd. 

![](./media/expressroute-optimize-routing/expressroute-case1-solution.png)

## Voorbeeld 2 van suboptimale routering
Hier volgt nog een voorbeeld waarin verbindingen van Microsoft een langer pad afleggen om uw netwerk te bereiken. In dit geval maakt u gebruik van on-premises Exchange-servers en Exchange Online in een [hybride omgeving](https://technet.microsoft.com/library/jj200581%28v=exchg.150%29.aspx). Uw kantoren zijn verbonden met een WAN. U adverteert de voorvoegsels van uw on-premises servers in beide kantoren aan Microsoft door middel van de twee ExpressRoute-circuits. Voor bijvoorbeeld postvakmigratie start Exchange Online verbindingen met de on-premises servers. Helaas wordt de verbinding met uw kantoor in Los Angeles omgeleid naar het ExpressRoute-circuit in VS - oost, waarna deze het gehele continent doorkruist om weer aan de westkust uit te komen. De oorzaak van het probleem is vergelijkbaar met die in het eerste voorbeeld. Zonder aanwijzingen weet het Microsoft-netwerk niet welk klantvoorvoegsel zich dichter bij VS - oost bevindt en welk dichter bij VS - west. Dus soms wordt het verkeerde pad naar uw kantoor in Los Angeles gekozen.

![](./media/expressroute-optimize-routing/expressroute-case2-problem.png)

### Oplossing: AS-padtoevoeging
Er zijn twee oplossingen voor het probleem. Voor de eerste oplossing adverteert u gewoon het on-premises voorvoegsel voor uw kantoor in Los Angeles, 177.2.0.0/31, op het ExpressRoute-circuit in VS - west en het on-premises voorvoegsel voor uw kantoor in New York, 177.2.0.2/31, op het ExpressRoute-circuit in VS - oost. Als gevolg hiervan is er slechts één pad waarlangs Microsoft verbinding maakt met elk kantoor. Er is geen dubbelzinnigheid en routering is geoptimaliseerd. Bij dit ontwerp moet u echter nadenken over een failoverstrategie. Als het pad naar Microsoft via ExpressRoute wordt verbroken, moet u ervoor zorgen dat Exchange Online nog steeds verbinding kan maken met uw on-premises servers. 

Voor de tweede oplossing blijft u beide voorvoegsels op beide ExpressRoute-circuits adverteren en geeft u daarnaast aan welk voorvoegsel zich het dichtst bij welk kantoor bevindt. Omdat we BGP AS-padtoevoeging ondersteunen, kunt u het AS-pad voor uw voorvoegsel configureren om routering te beïnvloeden. In dit voorbeeld kunt u het AS-pad voor 172.2.0.0/31 in VS - oost verlengen zodat het ExpressRoute-circuit in VS - west de voorkeur krijgt voor verkeer dat is bestemd voor dit voorvoegsel (omdat ons netwerk 'denkt' dat het pad naar dit voorvoegsel korter is in het westen). En zo verlengt u ook het AS-pad voor 172.2.0.2/31 in VS - west, zodat het ExpressRoute-circuit in VS - oost de voorkeur krijgt. Routering is geoptimaliseerd voor beide kantoren. Als bij dit ontwerp één ExpressRoute-circuit wordt verbroken, kan Exchange Online u nog steeds bereiken via een ander ExpressRoute-circuit en uw WAN. 

>[AZURE.IMPORTANT] We verwijderen persoonlijke AS-nummers in het AS-pad voor voorvoegsels die binnenkomen op Microsoft-peering. U moet openbare AS-nummers aan het AS-pad toevoegen om routering voor Microsoft-peering te beïnvloeden.

![](./media/expressroute-optimize-routing/expressroute-case2-solution.png)



<!--HONumber=Jun16_HO2-->


