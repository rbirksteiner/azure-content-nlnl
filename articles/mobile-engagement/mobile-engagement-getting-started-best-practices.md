<properties 
    pageTitle="Instructiehandleiding Azure Mobile Engagement met aanbevolen procedures"
    description="Instructiehandleiding voor Azure Mobile Engagement en aanbevolen procedures voor onboarding" 
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="wesmc7777"
    manager="erikre"
    editor=""/>

<tags
    ms.service="mobile-engagement"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="mobile-multiple"
    ms.workload="mobile" 
    ms.date="03/28/2016"
    ms.author="wesmc"/>

# Azure Mobile Engagement - Instructiehandleiding met aanbevolen procedures

## Overzicht

**Het mobiele scherm staat vol:** uit een in 2013 uitgevoerde studie bleek dat op het gemiddelde mobiele apparaat 27 toepassingen zijn geïnstalleerd. Gebruikers brachten doorgaans 30 uur per maand door in hun apps. De meeste tijd werd doorgebracht op sociale netwerken en met gamen (ongeveer 20 uur). Begin 2014 waren er op de Android-markt circa 1,5 miljoen toepassingen beschikbaar waar gebruikers uit konden kiezen. De Apple Store bevatte ongeveer 1,2 miljoen apps. Het gebruik van mobiele apps neemt nog steeds toe en ontwikkelaars blijven concurreren op deze groeiende markt. 

De gemiddelde mobiele gebruiker installeert en verwijdert apps vaak, afhankelijk van veranderende interesses en in-app-ervaringen. Als u het succes van uw app wilt bepalen, is het van essentieel belang méér te weten dan alleen het aantal gebruikers dat uw app installeert. U moet ook weten hoe nuttig uw app wordt gevonden en of de mening hierover verandert. De volgende vragen worden steeds belangrijker:

- Verliezen uw gebruikers interesse in uw app of vinden ze dat de app verouderd is? 
- Hoeveel gebruikers zijn al gestopt met het gebruik van uw app? 
- Neemt het aantal in-app-aankopen toe of af?
- Komt het voor dat gebruikers bepaalde procedures niet voltooien vanwege problemen met de app of gebrek aan interesse? 
- Ziet u mogelijkheden om uw app nuttig en relevant te houden door uw gebruikers nieuwe inhoud te bieden? 
- Is deze nieuwe inhoud dan identiek voor alle gebruikers of moet er onderscheid worden gemaakt tussen bepaalde gebruikerssegmenten op basis van gedrag in uw app? 
 
De antwoorden op deze en soortgelijke vragen kunnen u helpen de levensduur van uw app te verlengen en uw inkomsten te verhogen. Daarnaast kunnen ze u helpen bij het definiëren en behouden van uw gebruikersgroep. 

Mediagerelateerde apps worden door gebruikers doorgaans het langst bewaard. Een reden hiervoor is dat deze apps voortdurend nieuwe inhoud leveren aan gebruikers. Het verzenden van nuttige pushmeldingen naar een bepaald gebruikerssegment heeft vaak een grote invloed op app-retentie, zeker als dit vanaf het begin consistent wordt gedaan. 

Het programma Azure Mobile Engagement is bedoeld om de levensduur en retentie van uw app te vergroten. Hiertoe biedt Azure Mobile Engagement u een methode voor het verzamelen en analyseren van gedetailleerde informatie over het gebruik van uw app. Met deze methode kunt u uw gebruikers op basis van gedrag classificeren en gerichte campagnes maken voor het sturen van pushmeldingen en in-app-berichten aan geïdentificeerde gebruikerssegmenten. Key Performance Indicators (KPI's) meten hoe actief uw gebruikers zijn met verschillende aspecten van uw app. Azure Mobile Engagement biedt de methoden die u nodig hebt om deze KPI's te bepalen. Dit helpt het rendement te verhogen door de infrastructuur te bieden die u nodig hebt om het gebruik van uw mobiele app te verhogen. 

Wilt u optimaal gebruikmaken van Azure Mobile Engagement, dan moet u beginnen met een goed ontworpen gebruiksplan. Hiermee kunt u de gedetailleerde gegevens identificeren die u nodig hebt om uw gebruikersgroep in segmenten te verdelen. U kunt segmenten maken op basis van gedrag en op basis van in-app-ervaringen. Voor een succesvol plan is het heel belangrijk om de KPI waarmee u de doelstellingen van uw app wilt meten, duidelijk te definiëren. Met duidelijk gedefinieerde prestatie-indicatoren kunt u in uw app eenvoudig de logica opnemen voor het verzamelen van gedetailleerde gegevens. Deze gebruikt u vervolgens om uw KPI's te analyseren en te evalueren. Dit onderwerp is een handleiding met aanbevolen procedures voor het definiëren van de KPI's die u in uw plan gaat gebruiken. 


## Stap 1: de KPI's definiëren zodat deze bij het BET-model passen


Het correct definiëren van KPI's kan moeilijk zijn. Apps die zijn bestemd voor verschillende branches, hebben hun eigen specificaties en doelstellingen. Dit kan verwarrend zijn voor het kiezen van een benadering. Om dit te voorkomen, moeten doelstellingen en KPI's worden ingedeeld in drie hoofdcategorieën: **Zakelijk**, **Betrokkenheid** en **Technisch**. In dit verband spreken we ook wel van het **BET-model** (BET = Business, Engagement, Technical).

Een goed plan bevat doorgaans doelstellingen voor de KPI's waarmee het succes wordt gemeten in elk van de volgende categorieën van het BET-model.


#### Zakelijke KPI's

Zakelijke KPI's zijn het eenvoudigst te maken. U hebt deze waarschijnlijk al op een bepaalde manier gedefinieerd toen u uw mobiele app plande. Deze KPI's zijn in het algemeen nuttig bij het meten van de omzet en het rendement van uw app. In de volgende lijst ziet u een aantal voorbeelden van zakelijke KPI's die u kunnen helpen bij het definiëren van de prestatie-indicatoren:

- Zakelijke KPI's voor media
    - Aantal advertenties waarop is geklikt
    - Aantal bezoeken per gebruiker
    - Aantal bestaande abonnementen
- Zakelijke KPI's voor gaming
    - Aantal in-app-aankopen
    - Gemiddelde omzet per gebruiker (ARPU)
    - Tijd besteed per sessie
    - Aantal dagen gespeeld en huidige niveau in de game
- Zakelijke KPI's voor e-commerce
    - Aantal dagen dat de app is gebruikt
    - Gemiddelde omzet per gebruiker (ARPU)
    - Gemiddeld bedrag in winkelwagen bij afrekenen
    - Productcategorie voor de meeste weergaven en aankopen
- Zakelijke KPI's voor de bank- en verzekeringssector
    - Aantal accounts
    - Geactiveerde functies
    - Bezochte aanbiedingenpagina's
    - Meldingen waarop is geklikt of die zijn geactiveerd      



#### Betrokkenheids-KPI's

Een betrokkenheids-KPI is een prestatie-indicator die de betrokkenheid van uw gebruikers meet. Aan de hand van de trends op dit gebied kunt u de retentie van uw app bepalen. Hier volgen enkele voorbeelden van prestatie-indicatoren voor dit type KPI:

- Actieve gebruikers in de afgelopen 7 dagen
- Aantal inactieve gebruikers in de afgelopen 7 dagen
- Aantal gebruikers dat de app al 30 dagen niet heeft gebruikt  

Op deze betrokkenheidsindicatoren kunnen ook bepaalde voor de hand liggende externe factoren van invloed zijn. U kunt bijvoorbeeld aannemen dat een gebruiker altijd een mobiel apparaat bij zich heeft. Dit kan wel of niet het geval zijn. Een gaming-app heeft rond de feestdagen vaak een hoger gebruik, omdat de gebruiker dan vrij heeft van werk of school.   

Met goed gedefinieerde KPI's in deze categorie kunt u de relatie tussen uw app en uw klanten meten.



#### Technische KPI's

Aan de hand van de prestatie-indicatoren in deze categorie kunt u bepalen of uw app goed werkt, of juist vaak vastloopt of crasht. Deze indicatoren stellen u in staat de status van uw app te meten en problemen met de functionaliteit bloot te leggen die gebruik van de app in de weg staan. De in deze categorie verzamelde gegevens kunnen bovendien prestatie-informatie bevatten die relevant is voor uw marketingteams. Daarnaast kunnen de gegevens nuttig zijn voor het oplossen van problemen door de IT-afdeling en ondersteuningsteams. Denk bijvoorbeeld aan het detecteren van nog niet door gebruikers gemelde fouten. 
 
Hier volgen enkele voorbeelden van technische KPI's:

- Informatie over onverwerkte of verwerkte uitzonderingen en over het aantal uitzonderingen 
- Tijdstempel voor laatste crash
- Knop waarop het laatst is geklikt of de laatst bezochte webpagina
- Geheugengebruik van de app
- Framesnelheid van app
- Versie van het besturingssysteem waarop de app wordt uitgevoerd
- App-versie

Definieer deze KPI's voor het meten van app-prestaties en het detecteren van mogelijke fouten. Deze indicatoren kunnen ervoor zorgen dat u minder tijd nodig hebt om uw klanten een oplossing te bieden. Daarnaast kunnen ze u helpen bij het afbakenen van gebruikerssegmenten die bepaalde problemen ondervinden. Deze gebruikerssegmenten kunt u vervolgens via gerichte campagnes benaderen met meldingen over beschikbare oplossingen en promoties. Zo bent u in staat de klanttevredenheid te herstellen. 


#### Draaiboekoefening 1: uw KPI-dashboard maken

In uw marketingstrategie moeten uw KPI's elk van uw hoofddoelen inzichtelijk maken. Hiervoor hebt u duidelijk gedefinieerde gegevenspunten nodig, waarmee u essentiële informatie kunt verzamelen om uw app en het gedrag van eindgebruikers te monitoren.

Een KPI-dashboard maken dat onderstaande informatie bevat

1.  Wat zijn de KPI's voor de app?
2.  Welke gegevenspunten gaat u voor elke KPI gebruiken?
3.  Waar bevinden zich deze gegevens voor de toepassing (scherm, instellingen, systeem, enz.)?
4.  Kan ik voor deze KPI een betrokkenheidsreeks afspelen?

Gebruik het werkblad **KPI Builder** in onze [Media Playbook-sjabloon][link naar Media Playbook] voor voorbeelden en richtlijnen.



## Stap 2: uw betrokkenheidsprogramma


Een doordacht programma voor mobiele betrokkenheid vormt een belangrijk onderdeel van uw app. Essentieel hierin is een aantrekkelijk welkomstprogramma dat voor de gebruiker wordt uitgevoerd tijdens de eerste dagen van het app-gebruik. Dit heeft vaak een zeer positieve invloed op de betrokkenheid bij en retentie van uw app. Uit onderzoek is gebleken dat de meeste gebruikers al in de eerste paar dagen na de installatie stoppen met het gebruik van een app. Het is dus zaak om aan de verwachting van de gebruiker te voldoen of deze zelfs te overtreffen wanneer de gebruiker nog aandacht heeft voor uw app. Zorg er daarom voor dat u de belangrijkste waarde en voordelen van uw app benadrukt. 


![](./media/mobile-engagement-getting-started-best-practices/unsegmented-push-notifications.png)

Pushmeldingen zijn de aanbevolen aanpak om in een vroeg stadium betrokkenheid te genereren onder gebruikers van mobiele apparaten. Maar u moet uiterst voorzichtig zijn met het indelen van gebruikers in segmenten voor pushmeldingen. Wanneer een gebruiker het idee heeft spam of niet-interessante meldingen te ontvangen, kan dit zeer nadelige gevolgen hebben. In enkele klikken kan een gebruiker uw toepassing verwijderen en nooit meer installeren. Bied gebruikers daarom sterk gepersonaliseerde in-app-waarde in plaats van algemene spam.

Wanneer gebruikers eenmaal actief betrokken zijn, kunt u met uw betrokkenheidsprogramma andere aspecten van de app laten groeien.

U kunt bijvoorbeeld een campagne instellen waarin u actieve gebruikers vraagt om uw app te beoordelen. Aangezien dit segment gebruikers het meest actief is en de meeste ervaring heeft met uw app, mag u verwachten dat zij de meest nauwkeurige beoordeling geven. Als uw app een hoge beoordeling weet te bereiken, kan dit het aantal organische downloads van uw app stimuleren en uw kosten voor het werven van nieuwe klanten verlagen.



#### Betrokkenheidsreeks


Een universeel betrokkenheidsprogramma bevat verschillende betrokkenheidsreeksen. Elke reeks probeert verschillende doelstellingen te behalen.


###### Levenscyclusgerelateerde pushreeks


De doelstellingen van een levenscyclusgerelateerde pushreeks verschillen, afhankelijk van de levenscyclus van de betrokkenheid van de gebruiker bij de app. Een bepaalde gebruiker kan nieuw zijn, inactief of zeer actief. Gebruikers kunnen in verschillende stadia van de betrokkenheidslevenscyclus profiteren van uw nieuwe inhoud in de vorm van tips of koppelingen naar documentatie. 

Nieuwe gebruikers kunnen bijvoorbeeld hulp nodig hebben om zich te oriënteren in een app of profiteren van een incentive voor nieuwe gebruikers (zoals hieronder) wanneer zij de app voor het eerst starten...

*'Fijn dat u er bent! Als u zich aanmeldt, kunt u de app de eerste maand gratis gebruiken!'*


###### Gedragsgerelateerde pushreeks

Een gedragsgerelateerde pushreeks is erop gericht het gebruik te verhogen op basis van gebruikersgedrag dat voor de app is verzameld.  

Een zeer actieve gebruiker van een fantasy football-app kan bijvoorbeeld voordeel hebben bij een reactie op de volgende pushmelding:

*'John, je bent een echte fan! Meld je aan bij onze NFL-sectie en win kaartjes voor de SuperBowl!'*


###### Pushreeks voor meldingen

Gebruikers waarderen relevant nieuws dat te maken heeft met hun interesses. Een pushreeks voor meldingen verhoogt de betrokkenheid door meldingen te sturen op basis van duidelijk afgebakende interesses van de gebruiker. Interesses kunnen expliciet worden vastgesteld door gebruikers hun interesses in de app te laten selecteren. Interesses kunnen echter ook impliciet worden vastgesteld op basis van gegevens die tijdens gebruikersinteractie met de app zijn verzameld.

De gebruiker van een e-commerce-app kan bijvoorbeeld regelmatig koffie van een specifiek merk kopen dat u hebt vastgelegd met een zakelijke KPI. De volgende melding kan de betrokkenheid bij de app van deze gebruiker verbeteren.
 
*'Hallo Frank, in de eerste week van september 2015 krijg je 25% korting op je favoriete merk koffie. We vinden het fijn dat je voor ons kiest en willen je hier op deze manier voor bedanken.'*

###### Retentiegerelateerde pushreeks

Deze reeks is erop gericht gebruikers te behouden door middel van terugkerende pushmeldingcampagnes die gebruikers aanmoedigen regelmatig de app te gebruiken. Dergelijke campagnes kunnen helpen de app-retentie te verhogen als de gebruiker de interacties leuk vindt. 

De gebruiker van een sport-app ontvangt bijvoorbeeld wekelijks de volgende pushmeldingen op basis van zijn of haar favoriete teams:

*'Maak kans op 200 punten en voorspel of de New York Yankees deze week de wedstrijd tegen de Toronto Blue Jays gaan winnen!'*


#### De 3W-methode

Als u weet hoe u de verschillende pushreeksen gebruikt, vergroot u gemakkelijker de betrokkenheid van eindgebruikers. U moet echter nog steeds de 3W-methode gebruiken voor het personaliseren van uw meldingen. De 3W-methode heeft betrekking op het 'Wie?', 'Wat?' en 'Wanneer?' van elke melding. Als u hier op de juiste wijze invulling aan geeft, zijn uw meldingen optimaal gericht op betrokkenheid.

![](./media/mobile-engagement-getting-started-best-practices/who-what-when.png)



###### Wie: het gebruikerssegment dat berichten ontvangt

Pushmeldingen aan uw gebruikers moeten worden gezien als een zeer vertrouwelijk communicatiekanaal. Zorg ervoor dat de meldingen die u aan een gebruikerssegment wilt verzenden, perfect aansluiten bij de interesses van dat gebruikerssegment. Onjuist gerouteerde meldingen kunnen gemakkelijk resulteren in een negatieve gebruikerservaring. Gebruikers kunnen uw berichten gaan zien als spam en dat kan ertoe leiden dat ze uw app verwijderen. 

Gebruik een combinatie van specifieke technische en gedragscriteria bij het definiëren van de gebruikerssegmenten die meldingen ontvangen. Dit is een eenvoudig voorbeeld van een nauwkeurig afgebakend gebruikerssegment:

'Alle gebruikers die drie dagen geleden voor het laatst een mobiele toepassing hebben gestart en de aanmeldingspagina tweemaal hebben bezocht, maar zich vervolgens niet daadwerkelijk hebben aangemeld.'
 
Een dergelijke definitie helpt u bij het identificeren van de gegevens die u moet verzamelen om een specifiek scenario te ondersteunen.


###### Wat: het bericht dat u verzendt

**Toon**

Gebruik in uw communicatie een toon die past bij uw gebruikerssegment. Dit is een uitstekende manier om contact te maken met uw eindgebruikers en de interesse van gebruikers in uw app te vergroten. 

**Omleiding**

Een pushmelding kan niet alleen voor het openen van de toepassing worden gebruikt. Als de melding is gerelateerd aan een specifieke context, zoals een nieuwsbericht of een productpromotie, kan er een rechtstreekse dieptekoppeling naar de juiste inhoud in de toepassing tot stand worden gebracht. Als u deze functionaliteit wilt ondersteunen, moet u een URL-schema maken, zodat de toepassing de omleiding kan beheren. Tijdens het maken van uw betrokkenheidsreeksen is dit een belangrijke stap die u niet moet vergeten.

Er kunnen ook omleidingen worden beheerd voor andere systemen. Met een actie-URL is het bijvoorbeeld mogelijk eindgebruikers om te leiden naar diverse andere systemen, zoals:

- Een website
- Een postvak met ingesteld e-mailadres
- Een sms-vak
- Een beldienst
- Rechtstreeks naar de app-winkel voor het beoordelen van de toepassing. 

Dit biedt veel mogelijkheden om contact te leggen met eindgebruikers en automatische regels te maken voor betere prestaties.


**Indeling/inhoud**

Er zijn diverse typen en indelingen voor pushmeldingen:

1. **Aankondigingen**: hiermee kunt u op verschillende momenten advertentieberichten verzenden aan gebruikers (buiten de app om, in de app of op willekeurige momenten).
2. **Polls**: hiermee kunt u gegevens verzamelen van eindgebruikers door vragen aan hen te stellen. De antwoorden zijn vervolgens beschikbaar voor het opstellen van criteria om eindgebruikers in uw doelgroep te targeten.
3. **Gegevens-pushes**: hiermee kunt u een binair of base64-gegevensbestand verzenden om de app bij te werken. De informatie in een gegevens-push wordt naar uw toepassing verzonden om de gebruikerservaring in uw app te personaliseren. Uw toepassing moet zo worden ontworpen dat de gegevens in een gegevens-push worden ondersteund.
4. **Tegels (alleen Windows Phone)**: hiermee kunt u de MPNS (Microsoft Push Notification Service) gebruiken voor het verzenden van native pushmeldingen met XML-gegevens. (Dit wordt ondersteund vanaf SDK-versie 0.9.0. De uiteindelijke nettolading voor tegels mag niet groter zijn dan 32 kB.). Het bericht verschijnt rechtstreeks op het mededelingenbord van uw tegel.
5. **Webweergave**: een webweergave is een pop-upvenster met webinhoud. Dit pop-upvenster wordt weergegeven wanneer de eindgebruiker op de pushmelding heeft geklikt. Met een webweergave kunt meer interactie hebben met de eindgebruiker.
 
>[AZURE.NOTE] Zorg ervoor dat de inhoud die u in pushmeldingen verzendt, in overeenstemming is met de richtlijnen van het desbetreffende platform (iOS, Android, Windows) voor het ontwikkelen van apps en het verzenden van pushmeldingen.

 


###### Wanneer: de timing van uw campagne

Wanneer is het beste moment om een campagne met pushmeldingen te starten? Wordt de campagne handmatig uitgevoerd of automatisch? Moet het een terugkerende campagne worden? Het juiste moment en de juiste frequentie zijn essentieel om optimale betrokkenheid van uw gebruikers te bereiken. Voor elke betrokkenheidsreeks en elk scenario moet u opgeven wat het beste moment is om pushmeldingen te verzenden. Hier zijn enkele voorbeelden:

![](./media/mobile-engagement-getting-started-best-practices/campaign-timing-examples.png)

Als u dagelijks veel meldingen verzendt, moet u er rekening mee houden dat uw gebruikers uw communicatie als spam zien. 

Azure Mobile Engagement biedt twee manieren om te voorkomen dat uw communicatie-uitingen als spam worden beschouwd. Gebruik verfijnde segmentering om ervoor te zorgen dat u zich niet op dezelfde gebruikers richt. Azure Mobile Engagement biedt bovendien een quotumfunctie. Met deze functie kan het aantal meldingen in een campagne worden beperkt. Als u bijvoorbeeld een standaardquotum van 5 per week instelt, zorgt u ervoor dat een gebruiker in een gebruikerssegment van een campagne niet meer dan 5 meldingen binnen één week ontvangt.





#### Draaiboekoefening 2: uw betrokkenheidsprogramma maken

Besteed voldoende tijd aan het samenvatten van uw doelstellingen en het definiëren van de campagnes die u met specifieke reeksen wilt uitvoeren. Zorg ervoor dat u de 3W-methode toepast op de meldingen in uw campagnes. 

Gebruik het werkblad **Engagement Program** in de [Media Playbook-sjabloon][link naar Media Playbook] voor voorbeelden en richtlijnen.


## Stap 3: app-integratie


#### Een tagplan maken

Voor de integratie van Azure Mobile Engagement in uw app moet u een tagplan maken. Uw tagplan vormt de basis voor het project. In het tagplan definieert u de relatie tussen de marketingspecificaties, de werkstroom van de toepassing en de feitelijke taggegevens die in de app worden verzameld voor het meten van de KPI's. In het tagplan wordt daarnaast aangegeven welke analyses u in de portal kunt zien. Bovendien helpt het tagplan u gebruikerssegmenten te definiëren en gerichte pushmeldingen te verzenden om uw eindgebruikers tot actie aan te zetten. Na het definiëren van het tagplan kunt u de code eenvoudig in uw app integreren met behulp van de Azure Mobile Engagement SDK.

Het is niet de bedoeling dat u met uw tagplan alles in uw toepassing tagt. Alleen taggegevens die onderdeel uitmaken van uw strategie voor mobiele betrokkenheid, moeten hierin worden opgenomen. Wat u wel opneemt en wat niet, verschilt per toepassing. De [Media Playbook-sjabloon][link naar Media Playbook] die Azure Mobile Engagement biedt, helpt u bij het maken van een tagplan op basis van een bepaalde methode. Gebruik het werkblad **Tag Plan** als voorbeeld voor het ontwikkelen van uw tagplan.

Wees zeer specifiek bij het definiëren van een tagsectie in het werkblad. Dit is erg belangrijk om verwarring te voorkomen. Werk elk verwacht scenario waarin elke tag wordt verzonden, tot in detail uit. Voeg de naam toe van de activiteit waarin elke tag is ingesloten. Deze gegevens moeten allemaal worden opgenomen op het gedeelte **Informative** van het werkblad. Het werkblad van het tagplan vormt het belangrijkste referentiepunt voor de testverificatie. 

In het gedeelte **Data to collect** vindt uw ontwikkelteam de typen, namen, waarden en sleutel-/waardeparen met extra informatie die zijn vereist voor elke tag die in de toepassing wordt ingesloten.

U doet er verstandig aan het tagplan te laten controleren door alle teams die aan het project zijn verbonden. Breng alle noodzakelijke wijzigingen aan en controleer of alles duidelijk is voor de marketing- en ontwikkelteams.

Het werkblad **Statement of work** kan worden gebruikt als richtlijn voor iedereen die bij het project is betrokken.


#### Gegevenstypen

Dit zijn veelvoorkomende gegevenstypen die door Azure Mobile Engagement worden ondersteund.

###### Apparaten en gebruikers

Azure Mobile Engagement identificeert gebruikers door voor elk apparaat een unieke id te genereren. Deze id duiden we aan als apparaat-id. De apparaat-id wordt zo gegenereerd dat alle toepassingen die op hetzelfde apparaat worden uitgevoerd, dezelfde id delen.

###### Sessies en activiteiten

Een sessie is een exemplaar van de app dat wordt uitgevoerd door een gebruiker. Een sessie begint op het moment dat de gebruiker de app start en eindigt wanneer de gebruiker de app stopt.

Een activiteit is een logische groepering van een set taken die de app tijdens een sessie kan uitvoeren. Meestal is dit een bepaald venster in de app, maar het kan ook iets anders zijn dat door de logica van de toepassing is gedefinieerd. U moet in uw app in ieder geval elk venster en elke activiteit taggen. Zo krijgt u inzicht in het gebruikerspad.


###### Gebeurtenissen

Gebeurtenissen worden gebruikt voor het rapporteren van gebruikersinteractie met de app. Ze hebben betrekking op directe bewerkingen, zoals het delen van inhoud of het starten van een video. Het taggen van gebeurtenissen biedt u gegevenssets die laten zien hoe gebruikers de app gebruiken. 

###### Taken

Taken worden gebruikt voor het rapporteren van acties met een bepaalde duur. Enkele voorbeelden:

- Uitvoering van de API-aanroepen
- Weergavetijd van advertenties
- Duur van achtergrondtaken 
- Duur van het aankoopproces
- Een video bekijken


###### Fouten

Fouten worden gebruikt voor het rapporteren van problemen die door de app zijn gedetecteerd. Denk aan onjuiste gebruikersacties of mislukte API-aanroepen.

###### Toepassingsgegevens

Toepassingsgegevens worden gebruikt voor het taggen van gegevens die betrekking hebben op de ervaring van een gebruiker met een toepassing. Ze worden gegenereerd op basis van gebruikersinteractie met de betreffende toepassing. 

Voor toepassingsgegevenssleutels houdt Azure Mobile Engagement alleen de laatste waarde bij (niet de geschiedenis). Toepassingsgegevens geven inzicht in de status van uw app of uw eindgebruikers. Denk aan de aanmeldstatus of de favoriete productgroep van een gebruiker.

###### Crashgegevens

Crashgegevens, die automatisch worden verzameld door de Mobile Engagement SDK, melden toepassingsfouten die niet door de toepassing zijn verwerkt. Voorbeeld: een onverwerkte uitzondering.


###### Extra gegevens

Gebeurtenissen, fouten, activiteiten en taken kunnen worden uitgebreid met parameters. Het gaat hier om extra gegevens die de ontwikkelaar als specifieke toepassingsgerelateerde gegevens kan aanleveren. Deze gegevens zijn belangrijk voor het definiëren van verfijnde segmentering. 

Met de waarde van een 'article'-tag kunt u bijvoorbeeld eindgebruikers in segmenten opdelen op basis van wie een bepaald artikel heeft bekeken. Mogelijk is dit echter niet voldoende. Wellicht is het beter als deze 'article'-tag ook extra informatie, zoals 'news_category', binnen een activiteit bevat. Dit is bijvoorbeeld handig om dynamisch de favoriete categorieën van de gebruiker te bepalen. 

Extra gegevens worden doorgegeven als sleutel-/waardepaar. In het voorbeeld voor deze mediatoepassing bestaan de extra gegevens voor 'news_category' uit de waarde voor die categorie. Bijvoorbeeld 'sport', 'economie' of 'politiek'.





#### Tag- en SDK-integratie 

Voor stapsgewijze instructies voor het integreren van de Azure Mobile Engagement SDK in uw app volgt u de documentatie voor [Engagement SDK-integratie](mobile-engagement-windows-store-integrate-engagement.md) op de Azure-website. Kies uit de koppelingen aan het begin van de pagina uw doelplatform.

Het is raadzaam om projecten te maken voor twee apps gebouwd op Azure Mobile Engagement. Een voor de ontwikkel- en testfase en een andere voor de productiefase. Uw IT-team kan de app vervolgens vanuit de testfase doorzetten naar de productiefase wanneer de gebruikersacceptatietests zijn geslaagd.



#### Gebruikersacceptatietests

Gebruikersacceptatietests zijn bedoeld om ervoor te zorgen dat alles werkt zoals ontworpen. Werkstromen kunnen worden voltooid en alle vereiste gegevens kunnen op basis van uw tagplan worden verzameld:
 
- Gegevenstagging is ingericht volgens gedocumenteerde AZME-concepten
- Alle benodigde gegevens worden verzameld (inclusief extra gegevens en toepassingsgegevens)
- De naamgeving volgt de namen uit uw tagplan
- Er worden geen dubbele tags verzonden

Voer grondige tests uit op alle soorten meldingsgedrag die u in uw app hebt ingesloten

- Aankondigingen, polls, gegevens-pushes buiten en in de app
- Tekst-/webweergaven
- Badge-update, categorieën



#### Instellen

Het instellen van Azure Mobile Engagement is zeer eenvoudig. De documentatie die betrekking heeft op de gebruikersinterface, is beschikbaar op de Azure Mobile Engagement-website: [Navigeren in de gebruikersinterface](mobile-engagement-user-interface.md).

U doet er verstandig aan te beginnen met het instellen van de juiste rollen en rollidmaatschappen voor de gebruikers van uw project. Zo kunt u voor alle gebruikers het juiste toegangsniveau voor het platform beheren. De rollen kunnen zijn:

- Beheerders
- Ontwikkelaars
- Kijkers 

Daarna:
- Registreer uw apparaat-id, zodat u op uw eigen apparaat kunt testen.
- Ga naar uw accountinstellingen en stel de tijdzone in. Grafieken en de aflevertijd van meldingen worden hier dan aan aangepast.
- Ga naar de instellingen van uw toepassing en registreer de toepassingsgegevens die u nodig hebt om eindgebruikers binnen uw bereik te targeten.

Bekijk voor meer informatie over het uitvoeren van uw eerste pushmeldingcampagne [Aan de slag met het gebruik en beheer van pushmeldingen om contact te maken met uw eindgebruikers](mobile-engagement-how-tos.md).



## Conclusie


Betrokkenheidsprogramma's zijn niet eenmalig. Experimenteer daarom met wat het beste voor uw app werkt en verbeter uw programma continu op basis hiervan. 

Probeer terwijl u ervaring opdoet met het ontwikkelen van betrokkenheidsstrategieën, niet meteen een overkoepelende betrokkenheidsstrategie te maken. Kies voor een stapsgewijze benadering. Identificeer uw KPI's en bedenk hoe u deze gaat gebruiken. Voor elke app maakt u een unieke betrokkenheidsstrategie.

Hebt u eenmaal wat ervaring opgedaan, dan kunt u overwegen het volgende aan uw betrokkenheidsprogramma's toe te voegen:

- Prestaties bijhouden: u krijgt gebruikers en u definieert waarschijnlijk bronnen voor gegevensverzameling. Azure Mobile Engagement kan worden gekoppeld aan bronnen voor gegevensverzameling. Hierdoor kunt u de prestaties van elke bron controleren. Deze gegevens zijn van belang voor het maximaliseren van uw investering. 

- A/B-tests: dit is een essentieel onderdeel van het betrokkenheidsprogramma. Elke app heeft zijn eigen specificaties. Met A/B-tests kunt u uw betrokkenheidsprogramma verbeteren.

- Geografische locatie: dit is een grote kans voor merken. Dankzij deze functie kunt u op het juiste moment en de juiste plek contact maken met de juiste gebruikers. U doet er verstandig aan na te gaan of u voldoende gegevens hebt verzameld over het gedrag van eindgebruikers voordat u begint met het gebruik van de functie voor geografische locatie.

- Gegevens-push: een gegevens-push is een onzichtbare push. Met een gegevens-push kunt u uw toepassingen aanpassen op basis van het gedrag van eindgebruikers. Als een gebruikerssegment bijvoorbeeld vaak hightechproducten raadpleegt, kan de eigenaar van de app een gegevens-push sturen waarmee de startpagina wordt gepersonaliseerd met hightechinhoud.



## Volgende stappen

- [Een Azure Mobile Engagement-account maken](mobile-engagement-create.md).
- Ga naar [Uw Mobile Engagement-strategie definiëren](mobile-engagement-define-your-mobile-engagement-strategy.md) voor meer informatie over het definiëren van uw Mobile Engagement-strategie.



  

<!--Image references-->


<!--Link references-->
[link naar Media Playbook]: https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks



<!--HONumber=Jun16_HO2-->


