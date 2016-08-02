<properties
   pageTitle="Inleiding tot Azure Security Center | Microsoft Azure"
   description="Meer informatie over Azure Security Center, de belangrijkste mogelijkheden van Azure Security Center en hoe Azure Security Center werkt."
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

# Inleiding tot Azure Security Center

Meer informatie over Azure Security Center, de belangrijkste mogelijkheden van Azure Security Center en hoe Azure Security Center werkt.

> [AZURE.NOTE] De informatie in dit document is van toepassing op de preview-versie van Azure Security Center. In dit document wordt de service geïntroduceerd aan de hand van een voorbeeldimplementatie.

## Wat is Azure Security Center?
 Azure Security Center helpt u bij het detecteren, voorkomen van en reageren op bedreigingen dankzij een verhoogde zichtbaarheid van en controle over de beveiliging van uw Azure-resources. Het biedt geïntegreerde beveiligingsbewaking en beleidsbeheer voor uw Azure-abonnementen, helpt bedreigingen te detecteren die anders onopgemerkt zouden blijven, en werkt met een uitgebreid ecosysteem van beveiligingsoplossingen.

##  Belangrijkste mogelijkheden
 Security Center biedt eenvoudige en effectieve preventie en detectie van bedreigingen en reactiemogelijkheden die zijn ingebouwd in Azure. De belangrijkste mogelijkheden zijn:

| | |
|----- |-----|
| Voorkomen | De beveiligingsstatus van uw Azure-resources wordt gecontroleerd |
| | Er wordt beleid gedefinieerd voor uw Azure-abonnementen en -resourcegroepen op basis van de beveiligingsvereisten van uw bedrijf, de typen toepassingen die u gebruikt, en de vertrouwelijkheid van uw gegevens |
| | Er wordt gebruikgemaakt van beveiligingsaanbevelingen van het beleid om service-eigenaren te begeleiden bij de implementatie van benodigde besturingselementen |
| | Beveiligingsservices en -toepassingen van Microsoft en partners worden snel geïmplementeerd |
| Detecteren |Beveiligingsgegevens van uw Azure-resources, het netwerk en partneroplossingen zoals antimalwareprogramma's en firewalls worden automatisch verzameld en geanalyseerd |
| | Er wordt gebruikgemaakt van wereldwijde dreigingskennis van Microsoft-producten en -services, Microsoft Digital Crimes Unit (DCU), Microsoft Security Response Center (MSRC) en externe feeds |
| | Er worden geavanceerde analyses toegepast, waaronder machine learning en gedragsanalyse |
| Reageren | Beveiligingsincidenten/-waarschuwingen worden met prioriteit geleverd |
| | Er wordt inzicht geboden in de bron van de aanval en betrokken resources |
| | Er worden manieren voorgesteld om de huidige aanval te stoppen en toekomstige aanvallen te voorkomen |

## Introductie
 Security Center is toegankelijk via [Azure Portal](https://azure.microsoft.com/features/azure-portal/). [Meld u aan bij de portal](https://portal.azure.com), selecteer **Bladeren** en schuif vervolgens naar de optie **Security Center** of selecteer de tegel **Security Center** die u eerder aan het portaldashboard hebt vastgemaakt.

![Beveiligingstegel in Azure Portal][1]

Vanuit Security Center kunt u beveiligingsbeleid instellen, beveiligingsconfiguraties bewaken en beveiligingswaarschuwingen weergeven.

### Beveiligingsbeleid

U kunt beleid voor uw Azure-abonnementen en -resourcegroepen definiëren overeenkomstig de beveiligingsvereisten van uw bedrijf. Vervolgens kunt u het beleid aanpassen aan de typen toepassingen die u gebruikt, of de vertrouwelijkheid van de gegevens in elk abonnement. Zo kunnen er voor resources die worden gebruikt voor ontwikkeling of tests, andere beveiligingsvereisten zijn dan voor resources die worden gebruikt voor productietoepassingen. Ook kan voor toepassingen met gereglementeerde gegevens, zoals PII, een hoger beveiligingsniveau vereist zijn.

> [AZURE.NOTE] Als u het beveiligingsbeleid op abonnementsniveau of het niveau van de resourcegroep wilt wijzigen, moet u de eigenaar van het abonnement of een bijdrager voor het abonnement zijn.

Selecteer op de blade **Security Center** de tegel **Beleid** voor een lijst met uw abonnementen en resourcegroepen.   

![Blade Security Center][2]

Selecteer op de blade **Beveiligingsbeleid** een abonnement om de details van het beleid weer te geven.

![Blade Beveiligingsbeleid van het abonnement][3]

Met **Gegevensverzameling** (zie hierboven) schakelt u gegevensverzameling voor een beveiligingsbeleid in. Het inschakelen biedt:

- Dagelijks scannen van alle ondersteunde virtuele machines voor het bewaken van de beveiliging en aanbevelingen.
- Verzameling van beveiligingsgebeurtenissen voor analyse en detectie van bedreigingen.

Door **een opslagaccount per regio te kiezen** (zie hierboven) kunt u voor elke regio waarin u virtuele machines uitvoert, het opslagaccount kiezen waar de gegevens worden opgeslagen die worden verzameld bij deze virtuele machines. Als u geen opslagaccount voor elke regio kiest, wordt er een voor u gemaakt. De gegevens die worden verzameld, worden uit veiligheidsoverwegingen logisch geïsoleerd van de gegevens van andere klanten.

> [AZURE.NOTE] Gegevens verzamelen en een opslagaccount per regio kiezen wordt geconfigureerd op abonnementsniveau.

Met **Aanbevelingen weergeven voor** (zie hierboven) kunt u de beveiligingsbesturingselementen kiezen die u wilt bewaken en aanbevelen op basis van de beveiligingsvereisten van de resources in het abonnement.

Selecteer vervolgens een resourcegroep om de details van het beleid weer te geven.

![Resourcegroep van de blade Beveiligingsbeleid][4]

Met **Overname** (zie hierboven) kunt u de resourcegroep definiëren als:

- Overgenomen (standaard) wat betekent dat alle beveiligingsbeleidsregels voor deze resourcegroep worden overgenomen van het abonnementsniveau.
- Uniek wat betekent dat de resourcegroep een aangepast beveiligingsbeleid heeft. U moet wijzigingen aanbrengen onder **Aanbevelingen weergeven voor**.

> [AZURE.NOTE] Als er een conflict tussen het beleid op abonnementsniveau en het beleid op het niveau van de resourcegroep is, heeft het beleid op het niveau van de resourcegroep voorrang.

### Aanbevelingen voor beveiliging

 Security Center analyseert de beveiligingsstatus van uw Azure-resources om mogelijke beveiligingsproblemen op te sporen. Een lijst met aanbevelingen begeleidt u bij het configureren van benodigde besturingselementen. Voorbeelden zijn:

- Inrichting van antimalware om schadelijke software te identificeren en te verwijderen
- Configuratie van netwerkbeveiligingsgroepen en -regels om gegevensverkeer naar virtuele machines te beheren
- Inrichten van Web Application Firewalls om te beschermen tegen aanvallen die zijn gericht op uw webtoepassingen
- Implementatie van ontbrekende systeemupdates
- Aanpassing van besturingssysteemconfiguraties die niet overeenkomen met de aanbevolen basislijnen

Klik op de tegel **Aanbevelingen** voor een lijst met aanbevelingen. Klik op elke aanbeveling om extra informatie weer te geven of om actie te ondernemen om het probleem te verhelpen.

![Aanbevelingen voor beveiliging in Azure Security Center][5]

### Status van resources

Op de tegel **Beveiligingsstatus van de resource** ziet u de algehele beveiligingsstatus van de omgeving per resourcetype, met inbegrip van virtuele machines, webtoepassingen en andere resources.   

Selecteer een resourcetype op de tegel **Beveiligingsstatus van de resource** voor meer informatie, waaronder een lijst met alle mogelijke beveiligingsproblemen die zijn geïdentificeerd. (In het onderstaande voorbeeld is **Virtuele machines** geselecteerd.)

![Tegel Status van resources][6]

### Beveiligingswaarschuwingen

 Security Center verzamelt, analyseert en integreert automatisch logboekgegevens van uw Azure-resources, het netwerk en partneroplossingen zoals antimalwareprogramma's en firewalls. Wanneer er dreigingen worden gedetecteerd, wordt een beveiligingswaarschuwing gemaakt. Voorbeelden zijn detectie van:

- Geïnfecteerde virtuele machines die communiceren met bekende schadelijke IP-adressen
- Geavanceerde malware die is gedetecteerd met Windows Foutrapportage
- Beveiligingsaanvallen op virtuele machines
- Beveiligingswaarschuwingen van geïntegreerde antimalwareprogramma's en firewalls

Als u op de tegel **Beveiligingswaarschuwingen** klikt, wordt een lijst waarschuwingen met prioriteit weergegeven.

![Beveiligingswaarschuwingen][7]

Als u een waarschuwing selecteert, ziet u meer informatie over de aanval en suggesties voor herstel.

![Details van beveiligingswaarschuwing][8]

### Partneroplossingen

U kunt met de tegel **Partneroplossing** in één oogopslag zien wat de integriteitsstatus is van de partneroplossingen die zijn geïntegreerd met uw Azure-abonnement. Security Center bevat waarschuwingen die van de oplossingen afkomstig zijn.

Selecteer de tegel **Partneroplossingen**. Er wordt een blade met een lijst van alle verbonden partneroplossingen geopend.

![Partneroplossingen][9]

## Aan de slag
U moet over een Microsoft Azure-abonnement beschikken om met Security Center aan de slag te gaan. Security Center wordt met uw Azure-abonnement ingeschakeld. Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis proefversie](https://azure.microsoft.com/pricing/free-trial/).

 Security Center is toegankelijk via [Azure Portal](https://azure.microsoft.com/features/azure-portal/). Zie de [portaldocumentatie](https://azure.microsoft.com/documentation/services/azure-portal/) voor meer informatie.

In [Aan de slag met Azure Security Center](security-center-get-started.md) leert u snel de onderdelen van Security Center voor de bewaking van de beveiliging en het beheer van het beleid kennen.

## Volgende stappen
Dit document is een inleiding tot Security Center, de belangrijkste mogelijkheden hiervan en hoe u met Security Center aan de slag gaat. Zie het volgende voor meer informatie:

- [Beveiligingsbeleid instellen in Azure Security Center](security-center-policies.md): leer hoe u beveiligingsbeleid voor uw Azure-abonnementen en -resourcegroepen configureert.
- [Aanbevelingen voor beveiliging in Azure Security Center beheren](security-center-recommendations.md): leer hoe aanbevelingen u helpen uw Azure-resources te beveiligen.
- [Beveiligingsstatus bewaken in Azure Security Center](security-center-monitoring.md): meer informatie over het bewaken van de status van uw Azure-resources.
- [Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center](security-center-managing-and-responding-alerts.md): leer hoe u beveiligingswaarschuwingen kunt beheren en erop kunt reageren.
- [Partneroplossingen bewaken met Azure Security Center](security-center-partner-solutions.md): leer hoe u de integriteitsstatus van uw partneroplossingen kunt bewaken.
- [Azure Security Center FAQ](security-center-faq.md) (Veelgestelde vragen over Azure Security Center): raadpleeg veelgestelde vragen over het gebruik van de service.
- [Azure Security Blog](http://blogs.msdn.com/b/azuresecurity/) (Azure-beveiligingsblog): hier vindt u het laatste nieuws over Azure-beveiliging en andere informatie.

<!--Image references-->
[1]: ./media/security-center-intro/security-tile.PNG
[2]: ./media/security-center-intro/security-center.png
[3]: ./media/security-center-intro/security-policy.png
[4]: ./media/security-center-intro/security-policy-blade.png
[5]: ./media/security-center-intro/recommendations.png
[6]: ./media/security-center-intro/resources-health.png
[7]: ./media/security-center-intro/security-alert.png
[8]: ./media/security-center-intro/security-alert-detail.png
[9]: ./media/security-center-intro/partner-solutions.png



<!--HONumber=Jun16_HO2-->


