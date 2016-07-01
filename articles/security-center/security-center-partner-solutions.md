<properties
   pageTitle="Partneroplossingen beheren in Azure Security Center | Microsoft Azure"
   description="In dit document wordt Azure Security Center geïntroduceerd. U kunt in Azure Security Center in één oogopslag zien wat de integriteitsstatus is van de partneroplossingen die zijn geïntegreerd met uw Azure-abonnement."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/22/2016"
   ms.author="terrylan"/>

# Partneroplossingen bewaken met Azure Security Center

Dit document helpt u in Azure Security Center de integriteitsstatus van uw partneroplossingen te bewaken.

> [AZURE.NOTE] De informatie in dit document is van toepassing op de preview-versie van Azure Security Center. In dit document wordt de service geïntroduceerd aan de hand van een voorbeeldimplementatie. Dit is geen stapsgewijze handleiding.

## Wat is Security Center?
 Azure Security Center helpt u bij het detecteren, voorkomen van en reageren op bedreigingen dankzij een verhoogde zichtbaarheid van en controle over de beveiliging van uw Azure-resources. Het biedt geïntegreerde beveiligingsbewaking en beleidsbeheer voor uw abonnementen, helpt bedreigingen te detecteren die anders onopgemerkt zouden blijven, en werkt met een uitgebreid ecosysteem van beveiligingsoplossingen.

## Partneroplossingen bewaken

U kunt met de tegel **Partneroplossingen** op de blade **Security Center** in één oogopslag zien wat de integriteitsstatus is van de partneroplossingen die zijn geïntegreerd met uw Azure-abonnement.
![Tegel Partneroplossingen][1]

De tegel **Partneroplossingen** bevat het aantal partneroplossingen en een statusoverzicht voor deze oplossingen.

De **STATUS** van een partneroplossing kan zijn:

- Beveiligd (groen): er is geen probleem met de status.
- Niet in orde (rood): er is een probleem met de status waarvoor onmiddellijke aandacht is vereist.
- Rapportage gestopt (oranje): de oplossing is met het melden van de status gestopt.
- Onbekende beveiligingsstatus (oranje): de status van de oplossing is op dit moment onbekend omdat een nieuwe resource niet kan worden toegevoegd aan de bestaande oplossing.
- Niet gerapporteerd (grijs): de oplossing heeft nog niets gerapporteerd. Misschien is de status van een oplossing nog niet gerapporteerd, omdat de oplossing zojuist is verbonden en de implementatie nog niet is voltooid.

Als er geen oplossingen met uw abonnement worden geïntegreerd, wordt in de tegel vermeld dat er geen oplossingen zijn. Als u de tegel **Partneroplossingen** selecteert, kunt u de blade **Aanbevelingen** openen om beveiligingsoplossingen van partners te implementeren.
![Geen partneroplossingen][2]

De status van uw partneroplossingen weergeven:

1. Selecteer de tegel **Partneroplossingen**. Er wordt een blade geopend met een lijst van alle partneroplossingen die zijn verbonden met Security Center.
![Partneroplossingen][3]

2. Selecteer een partneroplossing. In dit voorbeeld wordt de oplossing **F5-WAF2** geselecteerd.  Er wordt een blade geopend waarin u de status van de partneroplossing en de gekoppelde resources van de oplossingen kunt zien. Selecteer **Oplossingenconsole** om de partnerbeheermogelijkheden voor deze oplossing te openen.
![Details van partneroplossingen][4]

3. Ga terug naar de blade **F5-WAF2** en selecteer **App koppelen**. De blade **Toepassingen koppelen** wordt geopend. Hier kunt u resources verbinden met de partneroplossing.
![Resources aan een partneroplossing koppelen][5]

## Volgende stappen
In dit document is de tegel **Partneroplossingen** in Security Center geïntroduceerd. Zie de volgende onderwerpen voor meer informatie over Security Center:

- [Beveiligingsbeleid instellen in Azure Security Center](security-center-policies.md): leer hoe u beveiligingsbeleid voor uw Azure-abonnementen en -resourcegroepen configureert.
- [Aanbevelingen voor beveiliging in Azure Security Center beheren](security-center-recommendations.md): leer hoe aanbevelingen u helpen uw Azure-resources te beveiligen.
- [Beveiligingsstatus bewaken in Azure Security Center](security-center-monitoring.md): meer informatie over het bewaken van de status van uw Azure-resources.
- [Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center](security-center-managing-and-responding-alerts.md): leer hoe u beveiligingswaarschuwingen kunt beheren en erop kunt reageren.
- [Azure Security Center FAQ](security-center-faq.md) (Veelgestelde vragen over Azure Security Center): raadpleeg veelgestelde vragen over het gebruik van de service.
- [Azure Security Blog](http://blogs.msdn.com/b/azuresecurity/) (Azure-beveiligingsblog): hier vindt u het laatste nieuws over Azure-beveiliging en andere informatie.

<!--Image references-->
[1]: ./media/security-center-partner-solutions/partner-solutions-tile.png
[2]: ./media/security-center-partner-solutions/no-partner-solutions-to-display.png
[3]: ./media/security-center-partner-solutions/partner-solutions.png
[4]: ./media/security-center-partner-solutions/partner-solutions-detail.png
[5]: ./media/security-center-partner-solutions/link-applications.png



<!--HONumber=Jun16_HO2-->


