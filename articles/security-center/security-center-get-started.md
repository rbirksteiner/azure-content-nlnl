<properties
   pageTitle="Aan de slag met Azure Security Center | Microsoft Azure"
   description="Dit document helpt u snel aan de slag met Azure Security Center, door u wegwijs te maken in de onderdelen voor bewaking van veiligheid en beleidsbeheer, en u door te verwijzen naar de volgende stappen."
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
   ms.date="04/26/2016"
   ms.author="terrylan"/>

# Aan de slag met Azure Security Center

Dit document helpt u snel aan de slag met Azure Security Center, door u wegwijs te maken in de onderdelen voor bewaking van veiligheid en beleidsbeheer, en u door te verwijzen naar de volgende stappen.

> [AZURE.NOTE] De informatie in dit document is van toepassing op de preview-versie van Azure Security Center. In dit document wordt de service geïntroduceerd aan de hand van een voorbeeldimplementatie. Dit is geen stapsgewijze handleiding.

## Wat is Azure Security Center?
 Azure Security Center helpt u bij het detecteren, voorkomen van en reageren op bedreigingen dankzij een verhoogde zichtbaarheid van en controle over de beveiliging van uw Azure-resources. Het biedt geïntegreerde beveiligingsbewaking en beleidsbeheer voor uw abonnementen, helpt bedreigingen te detecteren die anders onopgemerkt zouden blijven, en werkt met een uitgebreid ecosysteem van beveiligingsoplossingen.

## Vereisten

U moet over een abonnement op Microsoft Azure beschikken om met Security Center aan de slag te gaan. Security Center wordt met uw abonnement ingeschakeld. Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis proefversie](https://azure.microsoft.com/pricing/free-trial/).

 Security Center is toegankelijk via de [Azure-portal](https://azure.microsoft.com/features/azure-portal/). Zie de [portaldocumentatie](https://azure.microsoft.com/documentation/services/azure-portal/) voor meer informatie.


## Security Center openen

Voer in de portal de volgende stappen uit om Security Center te openen:

1. Selecteer **Bladeren** en blader naar de optie **Security Center**.
![Azure Security Center openen in de portal][1]

2. Selecteer **Security Center**. Hiermee opent u de blade **Security Center**.
3. Als u de blade **Security Center** in de toekomst snel wilt kunnen openen, selecteert u (rechtsboven) **Blade vastmaken aan dashboard**.
![Optie Blade vastmaken aan dashboard][2]

## Security Center gebruiken

U kunt beveiligingsbeleidsregels voor uw Azure-abonnementen en resourcegroepen configureren. We gaan een beveiligings**beleid** configureren voor uw abonnement:

1. Selecteer de tegel **Beleid** op de blade **Security Center**.
![Security Center][3]

2. Selecteer een abonnement op de blade **Beveiligingsbeleid: Het beleid per abonnement of resourcegroep definiëren**.
![De blade Beveiligingsbeleid in Azure Security Center][4]

3. Schakel op de blade **Beveiligingsbeleid** de optie **Gegevensverzameling** in als u automatisch logboeken wilt verzamelen. Door **Gegevensverzameling** in te schakelen, wordt ook de controle-extensie op alle huidige en de nieuwe VM's in het abonnement ingericht.
4. Selecteer **Kies per regio een opslagaccount	**. Voor elke regio waarin u virtuele machines uitvoert, kiest u de opslagaccount waarin de gegevens worden opgeslagen die zijn verzameld bij deze virtuele machines. Als u geen opslagaccount voor elke regio kiest, wordt er een voor u gemaakt. De gegevens die worden verzameld, worden uit veiligheidsoverwegingen logisch geïsoleerd van de gegevens van andere klanten.

     > [AZURE.NOTE] U wordt aangeraden om gegevensverzameling in te schakelen en eerst een opslagaccount op abonnementsniveau te kiezen.  Beveiligingsbeleidsregels kunnen worden ingesteld op het niveau van het Azure-abonnement en op dat van de resourcegroep, maar configuratie van gegevensselectie en opslagaccount kan alleen plaatsvinden op abonnementsniveau.

5. Schakel de **aanbevelingen** in die u wilt opnemen in uw beveiligingsbeleid. Voorbeelden:

 - Door **Systeemupdates** in te schakelen, worden alle ondersteunde virtuele machines gecontroleerd op ontbrekende OS-updates.
 - Door **Basislijnregels** in te schakelen, worden alle ondersteunde virtuele machines gescand om eventuele OS-configuraties te identificeren die de virtuele machine kwetsbaarder maken voor aanvallen.

Ga naar **Aanbevelingen**:

1. Ga terug naar de blade **Security Center** en selecteer de tegel **Aanbevelingen**. De beveiligingsstatus van uw Azure-bronnen worden regelmatig door Security Center gecontroleerd. Als mogelijke beveiligingsproblemen worden geïdentificeerd, wordt hier een aanbeveling weergegeven.
2.  Selecteer elke aanbeveling om meer informatie weer te geven en/of om actie te ondernemen om het probleem te verhelpen.
![Aanbevelingen in Azure Security Center][5]

Bekijk de beveiligingsstatus van uw resources via **Beveiligingsstatus van de resource**:

1.  Ga terug naar de blade **Security Center**.
2.  De tegel **Beveiligingsstatus van de resource** bevat indicatoren van de beveiligingsstatus voor **Virtuele machines**, **Netwerken**, **SQL** en **Toepassingen**.
3.  Selecteer **Virtuele machines** als u meer informatie wilt weergeven.
4.  De blade **Virtuele machines** geeft een statusoverzicht met de status van antimalwareprogramma's, systeemupdates, het opnieuw opstarten van het systeem en de basislijnregels van uw virtuele machines.
5.  Selecteer een item onder **Aanbevelingen voor virtuele machine** als u meer informatie wilt weergeven en/of actie wilt ondernemen om noodzakelijke besturingselementen te configureren.
6.  Geef meer details meer om aanvullende informatie weer te geven voor specifieke virtuele machines.
![De tegel Status van resources in Azure Security Center][6]

Ga naar **Beveiligingswaarschuwingen**:

1.  Ga terug naar de blade **Security Center** en selecteer de tegel **Beveiligingswaarschuwingen**. Op de blade **Beveiligingswaarschuwingen** wordt een lijst met waarschuwingen weergegeven. De waarschuwingen worden gegenereerd door de beveiligingscentrumanalyse van uw beveiligingslogboeken en netwerkactiviteit. Waarschuwingen van geïntegreerde partneroplossingen zijn ook opgenomen.
![Beveiligingswaarschuwingen in Azure Security Center][7]

2.  Selecteer een waarschuwing als u extra informatie wilt weergeven.
![Gegevens van beveiligingswaarschuwingen in Azure Security Center][8]

Bekijk de status van uw **partneroplossingen**:

1. Ga terug naar de blade **Security Center**. U kunt met de tegel **Partneroplossing** in één oogopslag zien wat de integriteitsstatus is van de partneroplossingen die zijn geïntegreerd met uw Azure-abonnement.
2. Selecteer de tegel **Partneroplossingen**. Er wordt een blade geopend met een lijst van alle partneroplossingen die zijn verbonden met Security Center.
![Partneroplossingen][9]

3. Selecteer een partneroplossing. In dit voorbeeld wordt de oplossing **F5-WAF2** geselecteerd.  Er wordt een blade geopend met de status van de partneroplossing en de gekoppelde resources van de oplossingen. Selecteer **Oplossingenconsole** om de partnerbeheermogelijkheden voor deze oplossing te openen.
![Details van partneroplossingen][10]

## Volgende stappen
In dit document hebt u kennisgemaakt met de onderdelen van beveiligingsbewaking en beleidsbeheer in Security Center. Raadpleeg de volgende onderwerpen voor meer informatie:

- [Beveiligingsbeleid instellen in Azure Security Center](security-center-policies.md): leer hoe u beveiligingsbeleid voor uw Azure-abonnementen en -resourcegroepen configureert.
- [Aanbevelingen voor beveiliging in Azure Security Center beheren](security-center-recommendations.md): leer hoe aanbevelingen u helpen uw Azure-resources te beveiligen.
- [Beveiligingsstatus bewaken in Azure Security Center](security-center-monitoring.md): ontdek hoe u de status van uw Azure-resources kunt controleren.
- [Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center](security-center-managing-and-responding-alerts.md): leer hoe u beveiligingswaarschuwingen kunt beheren en erop kunt reageren.
- [Partneroplossingen bewaken met Azure Security Center](security-center-partner-solutions.md): meer informatie over het bewaken van de integriteitsstatus van uw partneroplossingen.
- [Azure Security Center FAQ](security-center-faq.md): raadpleeg veelgestelde vragen over het gebruik van de service.
- [Azure Security Blog](http://blogs.msdn.com/b/azuresecurity/) (Azure-beveiligingsblog): hier vindt u het laatste nieuws over Azure-beveiliging en andere informatie.

<!--Image references-->
[1]: ./media/security-center-get-started/security-tile.png
[2]: ./media/security-center-get-started/pin-blade.png
[3]: ./media/security-center-get-started/security-center.png
[4]: ./media/security-center-get-started/security-policy.png
[5]: ./media/security-center-get-started/recommendations.png
[6]: ./media/security-center-get-started/resources-health.png
[7]: ./media/security-center-get-started/security-alert.png
[8]: ./media/security-center-get-started/security-alert-detail.png
[9]: ./media/security-center-get-started/partner-solutions.png
[10]: ./media/security-center-get-started/partner-solutions-detail.png



<!--HONumber=Jun16_HO2-->


