<properties
    pageTitle="Uw strategie voor Mobile Engagement definiëren | Microsoft Azure"
    description="Informatie over het gebruik en de optimalisatie van Mobile Engagement met analyses en pushmeldingen."
    services="mobile-engagement"
    documentationCenter="Mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="03/01/2016"
    ms.author="piyushjo" />

# Uw strategie voor Mobile Engagement definiëren

*U hebt uw app niet zomaar geschreven: u wilt wel dat uw gebruikers deze ook gebruiken.*

We zijn ervan overtuigd dat u veel moeite hebt gedaan om er een geweldige app van te maken. U hebt waarschijnlijk ook een aanzienlijk marketingbudget geïnvesteerd om gebruikers aan te trekken. Maar na de eerste opwindende piek, ziet u dat uw app langzamerhand steeds minder wordt gebruikt. *Daar gaat het bij Azure Mobile Engagement om*: dat gebruikers blijven hangen en dat u uw app stapsgewijs kunt verbeteren via testen en leermomenten.

Onze aanpak voor het verbeteren van de retentie en het gebruik is gebaseerd op het benaderen van app-gebruikers via pushmeldingen en in-app-berichten. Dit gebeurt op een zeer speciale manier, namelijk met berichten en communicatie die op hun gedrag in uw app zijn afgestemd. Ons doel is dat u op het juiste moment en op de juiste locatie met de juiste doelgroep communiceert.

Maar hiervoor moet u eerst *inzicht krijgen in uw gebruikers*. Daarna moet u groepen maken op basis van wat ze hebben gedaan of op basis van hun kenmerken (we noemen dat segmenten). Vervolgens moet u relevante communicatie voor elk segment maken.

## Mobile Engagement helpt u om uw doelstellingen te verwezenlijken

*We hebben het gehad over retentie en gebruik, maar waarvoor?*

Wanneer u uw Mobile Engagement-strategie bouwt, moet u eerst kijken naar de doelstellingen en de Key Performance Indicators (KPI's) van uw app.

Bepaal eerst de doelstellingen en KPI's waarmee u zicht krijgt op uw use cases voor betrokkenheid.

Uw use cases bestaan uit een eenvoudige lijst met campagnes die u wilt maken om met uw gebruikers te communiceren. Deze variëren van het eenvoudige welkomstbericht tot de zeer geavanceerde melding voor het hulpprogramma die door uw IT-systeem wordt geactiveerd. Een goed opgebouwde use case moet ten minste bestaan uit de drie w's: *wat, wie en wanneer*:

1. Een zeer korte benaming (bijvoorbeeld een 'welkomstcampagne').
2. **Wat**: een voorbeeld van een bericht (bijvoorbeeld 'Fijn dat u hier bent. Vergeet niet om u aan te melden, zodat u de eerste maand gratis krijgt.'). Dit is geen permanente keuze. U kunt het bericht op elk gewenst moment wijzigen, maar het kan handig zijn om alvast te bedenken wat u wilt zeggen.
3. **Wie**: het segment dat dit bericht ontvangt (bijvoorbeeld 'alle gebruikers die de app 3 dagen geleden voor het eerst hebben gestart, de aanmeldingspagina hebben bezocht, maar zich niet hebben aangemeld').
    - Dit kunt u heel eenvoudig doen met Azure Mobile Engagement.
    - Ook dit hoeft niet definitief te zijn, aangezien u de segmenten op elk gewenst moment kunt aanpassen. Het is echter wel belangrijk dat u uw segmentatiecriteria in een vroeg stadium definieert, zodat u de juiste gegevens verzamelt.
4. **Wanneer**: de timing van uw campagne. Dit kan op een bepaalde datum of na een specifieke actie zijn, op basis van een trigger. Mobile Engagement biedt tal van belangrijke mogelijkheden om uw communicatie juist te timen.

Als u uw use cases en segment hebt bepaald, kunt u aan de hand hiervan de gegevens definiëren die in een toepassing moeten worden verzameld. Dit is de rol van een *'tagplan'*. Met een tagplan kunt u ervoor zorgen dat de gegevensverzameling aan de ontwikkelaars wordt opgegeven. Daardoor kunnen ontwikkelaars Mobile Engagement insluiten met de juiste instellingen, zodat u met de juiste gegevens aan uw campagnes kunt werken. Het is ook heel belangrijk dat u testen uitvoert om te controleren of de integratie juist is en verzamelt wat u nodig hebt.

Op basis van de integratie kunt u, wanneer toepassingen zijn gepubliceerd, als marketeer uw analyses in realtime zien, uw doelgroep segmenteren en vervolgens starten met het verzenden van slimme gerichte pushmeldingen om eindgebruikers in of buiten de app te benaderen.

### Use cases om mee aan de slag te gaan
1. Welkomststrategie: maak verschillende campagnes met pushmeldingen op basis van het gedrag van de eindgebruiker tijdens de start van de toepassing. Zo kunt u de interactie opnieuw starten op D+2/5/10/15 na de eerste sessie en de retentie na de eerste uitvoering verhogen.
2. Promoot nieuwe inhoud (functie, artikel/video of product) op basis van het gedrag van de eindgebruiker. Verzend de informatie alleen naar eindgebruikers die eerder zijn geneigd om de interactie aan te gaan.
3. Beoordeel de toepassing: richt u op minder dan 1 procent van uw gebruikersgroep, die de app in de winkel hoogst waarschijnlijk met 5 sterren beoordeelt.
4. Stimuleer abonnementen: promoot waardevolle inhoud aan eindgebruikers die deze nog niet hebben gezien om het aantal abonnementen te verhogen.
5. Zelfstudie: geen verplichte zelfstudie voor iedereen meer. Waarom geen geweldige in-app-zelfstudies bouwen en deze vervolgens alleen via in-app-berichten activeren als de gebruiker de app niet lijkt te gebruiken of problemen heeft met het gebruik van een functie?

## Waarom hebt u analyses nodig om interactie aan te gaan?

Momenteel beseft u waarschijnlijk dat het maken van een pushmelding alleen niet voldoende is. Het kernconcept van Mobile Engagement is om marketeers en ontwikkelaars te helpen de juiste eindgebruiker op het juiste moment en op de juiste plaats te benaderen. Als u inzicht wilt krijgen in deze drie belangrijkste concepten, is het essentieel om analyses van uw toepassing te verzamelen en deze vervolgens te gebruiken bij het segmenteren van uw doelgroep. Deze worden nog krachtiger wanneer gedragssegmenten gegevens uit uw andere database of CRM of uit een ander kanaal aanvullen. Mobile Engagement haalt gegevens op vanaf elke locatie en gebruikt deze om de juiste doelgroep te benaderen.

Als u zo contextueel mogelijk wilt zijn wanneer u de interactie met uw doelgroep aangaat, is het cruciaal om kennis te hebben van het gedrag van eindgebruikers en hun status in realtime te weten. Door het verzamelen van gegevens kunnen marketeers zich richten op wat echt belangrijk is in het gebruik van use cases en kunnen ze hun strategiedoelstellingen voor Mobile Engagement bereiken. Om de eerder gestelde doelstellingen te bereiken, moet u ook niet lukraak zo veel mogelijk gegevens verzamelen. De aanbevolen procedure is om alleen die gegevens te verzamelen waarmee u zich kunt richten op uw use cases en op wat u te weten wilt komen. Dit is een uitstekende manier om de oplossing te starten, te proberen, te testen en te leren hoe u deze kunt gebruiken, slimme pushmeldingen te maken en de retentie van een toepassing te verhogen om deze tot een succes te maken.

>[AZURE.NOTE] Let op: een teveel aan gegevens is funest.

### Use cases en aanbevolen procedures

In de volgende secties worden een aantal belangrijke use cases die we bij onze klanten zijn tegengekomen in het kort behandeld, zodat u aan de slag kunt gaan.

#### Media

Verzamel het type inhoud dat wordt gebruikt door de eindgebruiker. Segmenteer vervolgens de doelgroep op basis van dit gedrag, zodat u specifieke typen inhoud alleen kunt tonen aan een doelgroep die meer geneigd is deze te gebruiken. Dit voorkomt het spammen van een gehele gebruikersgroep en zorgt voor een betere retentie.

#### M-commerce

Verzamel de productcategorieën die binnen de toepassing en doelgroep het meest worden bezocht en promoot een korting of een nieuw product uit die categorie waarvan de eindgebruiker meer is geneigd deze aan te schaffen. Richt u op het verhogen van uw opbrengsten. Het doel is weer om niet te spammen.

#### Gaming

Verzamel de levels van een game van een eindgebruiker en de tijd die in een bepaalde periode is besteed, zodat u zich kunt richten op de doelgroep die wellicht is vastgelopen en meer geneigd is om naar het volgende level te gaan met een bonusaanbieding.

Communiceer met gebruikers die enige tijd niet hebben gespeeld over specifieke gebeurtenissen met een stimulans om ze aan te moedigen terug te keren.

#### Retail

Verzamel de producten of merken die een doelgroep eerder is geneigd te gebruiken op basis van favorieten of gedrag. Lok de doelgroep naar uw winkel om inkoopopbrengsten te verhogen.

#### Bankieren

Verzamel gegevens van eindgebruikers die een account hebben gemaakt tijdens de eerste keer dat zij de toepassing openden. Richt u op de implementatie van een welkomststrategie met een gerichte pushmelding en verhoog het aantal abonnementen.

### Een geweldig tagplan maken

Een tagplan moet als een beschrijving van het gebruikerspad of een soort werkstroom van de toepassing zijn. Alle vereiste tags (gegevens) die moeten worden verzameld, moeten worden opgegeven om voldoende datapunten te hebben om gebruikersgedrag te begrijpen en de gebruikersgroep goed te segmenteren. Dit is geen technisch proces. Marketeers kunnen daarom de gegevens opgeven die ze willen verzamelen op basis van hun Mobile Engagement-strategie.

U moet ten minste alle schermen van een toepassing taggen (de schermen worden in Mobile Engagement *activiteiten* genoemd). Dit helpt u het gebruikerspad te bepalen.

Een activiteit kan *gebeurtenissen* insluiten die informatie over acties verzamelen, zoals het klikken op een knop. Hiermee kunnen interacties in de toepassing worden verzameld. Zo kunnen marketeers te weten komen wat schermgebruikers bezoeken en wat ze doen.

`Jobs` zijn acties met een duur. Dit is zeer nuttig voor marketeers. Het geeft ze inzicht in hoe lang het duurt voordat een gebruiker bijvoorbeeld een account heeft gemaakt of zich heeft aangemeld. Dit kan ook handig zijn voor ontwikkelaars. Zo kunnen ze in de gaten houden hoe lang het duurt om een webservice aan te roepen.

`Errors` kunnen ook worden gecontroleerd. Hiermee komt u erachter of gebruikers problemen ondervinden in uw app. Bijvoorbeeld als ze regelmatig verbindingsproblemen hebben.

Al dit soort gegevens kunnen worden uitgebreid met parameters (*extra gegevens* in Mobile Engagement), zodat u dynamische gegevens van de toepassing kunt verzamelen. Dit is belangrijk voor het toestaan van verfijnde segmentering. Marketeers kunnen bijvoorbeeld een gebruiker segmenteren op basis van het type inhoud dat ze hebben gebruikt. Het type inhoud bestaat uit de dynamische gegevens van een activiteit of een gebeurtenis.

*Informatie over de app* bestaat uit gegevens waarmee u de status van de toepassing of de gebruiker in realtime kunt bevestigen. Dit helpt u ook een doelgroep te categoriseren en deze snel te benaderen. Er kan bijvoorbeeld gebruik worden gemaakt van een status waar/onwaar om te zien of de gebruiker zich wel of niet aanmeldt of van de vervaldatum van het abonnement van de gebruiker.

#### Voorbeeld van tags

*Use case: segmenteer het doelgroepgedrag om de juiste eindgebruiker met de juiste pushmeldinginhoud te benaderen*

1.  Een pushmelding verzenden om een categorie van een product te promoten: verzamel gedragsgegevens om de doelgroep te segmenteren op basis van de categorie van een product die de gebruikers in een bepaalde periode x keer hebben bezocht of een specifiek item dat ze in een winkelwagen hebben toegevoegd. Met de verzamelde gegevens kunt u segmenteren en vervolgens een pushmelding naar de juiste doelgroep verzenden.
2.  De app beoordelen: verzamel gegevens op basis van de inhoud die door de doelgroep op een sociaal netwerk is gedeeld. Is erop gericht de doelgroep te segmenteren door de *ambassadeurs* van uw app te bepalen. Als u een in-app-pushmelding gebruikt, zijn de ambassadeurs de beste doelgroep van uw app om te vragen uw app in de winkel met 5 sterren te beoordelen.

    ![][1]

*Use case: declaratieve gegevens*
1.  Segmenteer op basis van nieuwsnotificaties: verzamel declaratieve gegevens om de doelgroep op basis van de voorkeuren van deze gebruikers te segmenteren. Deze instelling staat het verzenden van pushmeldingen toe over een bepaald onderwerp waarin een specifieke doelgroep echt geïnteresseerd is.
2.  Segmenteer de doelgroep op basis van de aanmeldingsstatus van de gebruikers. Verzamel gegevens om erachter te komen of een gebruiker is verbonden of een account heeft gemaakt. Dit helpt u bij het benaderen van eindgebruikers die zich nog niet hebben aangemeld en verzendt een pushmelding om eindgebruikers aan te moedigen dit toch te doen.
    ![][2]

### Volgende stappen

- Ga naar [Mobile Engagement-concepten] voor meer informatie over basisconcepten van Mobile Engagement.
- Ga naar [Een Mobile Engagement-app maken](mobile-engagement-create.md) om een nieuwe Mobile Engagement-app-verzameling in Azure te maken en begin met het beheren van uw apps met de Mobile Engagement-portal.
- Ga naar [Aanbevolen procedures](mobile-engagement-getting-started-best-practices.md) voor gedetailleerde informatie.
- Ga naar [Gaming-app-scenario](mobile-engagement-gaming-scenario.md) voor meer informatie over de implementatie van Mobile Engagement met een gaming-voorbeeld-app. 
- Ga naar [Media-app-scenario](mobile-engagement-media-scenario.md) voor meer informatie over de implementatie van Mobile Engagement met een media-voorbeeld-app. 
- Ga naar [Zelfstudies] voor meer informatie over de implementatie.

<!-- Images. -->
[1]: ./media/mobile-engagement-define-your-mobile-engagement-strategy/use-case1.png
[2]: ./media/mobile-engagement-define-your-mobile-engagement-strategy/use-case2.png

<!-- URLs. -->
[Mobile Engagement-concepten]: http://azure.microsoft.com/documentation/articles/mobile-engagement-concepts/
[Zelfstudies]: http://azure.microsoft.com/documentation/articles/mobile-engagement-ios-get-started/




<!--HONumber=Jun16_HO2-->


