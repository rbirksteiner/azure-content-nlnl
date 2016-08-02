<properties
    pageTitle="Azure Notification Hubs"
    description="Informatie over het gebruik van pushmeldingen in Azure. Codevoorbeelden geschreven in C# met .NET API."
    authors="wesmc7777"
    manager="dwrede"
    editor=""
    services="notification-hubs"
    documentationCenter=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="multiple"
    ms.devlang="multiple"
    ms.topic="hero-article"
    ms.date="02/11/2016"
    ms.author="wesmc"/>


#Azure Notification Hubs

##Overzicht

Azure Notification Hubs bieden een gebruiksvriendelijke, uitgebreide pushinfrastructuur voor meerdere platforms waarmee u mobiele pushmeldingen vanaf elke back-end (in de cloud of lokaal) naar elk mobiel platform kunt versturen.

Met Notification Hubs kunt u eenvoudig persoonlijke pushberichten naar meerdere platforms verzenden, waarbij de details van de verschillende platform notification systems (PNS) worden afgeleid. Met één API-aanroep kunt u afzonderlijke gebruikers of een publiek van miljoenen gebruikers op al hun apparaten bereiken.

U kunt Notification Hubs voor zowel bedrijfs- als consumentenscenario's gebruiken. Bijvoorbeeld:

- Meldingen met het laatste nieuws verzenden naar miljoenen gebruikers met lage latentie (Notification Hubs stuurt Bing-toepassingen aan die vooraf zijn geïnstalleerd op alle Windows- en Windows Phone-apparaten).
- Op locatie gebaseerde coupons aan gebruikerssegmenten verzenden.
- Gebeurtenismeldingen aan gebruikers of groepen voor financiële toepassingen en sport- en gamestoepassingen verzenden.
- Gebruikers van bedrijfsgebeurtenissen op de hoogte stellen, zoals nieuwe berichten, e-mailberichten en verkoopkansen.
- Eenmalige wachtwoorden verzenden die vereist zijn voor meervoudige verificatie.



##Wat zijn pushmeldingen?

Smartphones en tablets kunnen gebruikers 'waarschuwen' wanneer een gebeurtenis heeft plaatsgevonden. Deze meldingen kunnen vele vormen aannemen.

In de Windows Store en Windows Phone-toepassingen kan de melding worden weergegeven in de vorm van een _pop-up_: er wordt een venster zonder modus weergegeven met een geluid om de gebruiker ervan op de hoogte stellen dat er een nieuwe melding is. Andere ondersteunde typen meldingen zijn _tegelmeldingen_, _onbewerkte meldingen_ en _badgemeldingen_. Zie [Tegels, badges en meldingen](http://msdn.microsoft.com/library/windows/apps/hh779725.aspx) voor meer informatie over de verschillende typen meldingen die worden ondersteund op Windows-apparaten 

Op iOS-apparaten van Apple waarschuwt de pushbewerking de gebruiker op dezelfde manier via een venster waarin de gebruiker wordt gevraagd om de melding te bekijken of te sluiten. Als de gebruiker op **Bekijken** klikt, wordt de app geopend waarin het bericht is ontvangen. Zie [iOS-meldingen](http://go.microsoft.com/fwlink/?LinkId=615245) voor meer informatie over iOS-meldingen.

Via pushmeldingen kunnen mobiele apparaten op een energie-efficiënte manier nieuwe informatie weergeven. Meldingen kunnen worden verzonden door back-end-systemen op mobiele apparaten, zelfs als de overeenkomstige apps op een apparaat niet actief zijn. Pushmeldingen zijn een essentieel onderdeel voor consumentenapps, waar ze worden gebruikt om het gebruik en de betrokkenheid van apps te verhogen. Meldingen zijn ook handig voor bedrijven, omdat gebruikers actiever reageren op bedrijfsgebeurtenissen als ze altijd op de hoogte zijn van het laatste nieuws.

Specifieke voorbeelden van scenario’s voor mobiele betrokkenheid:

1.  Een tegel in Windows 8 of Windows Phone met de huidige financiële gegevens bijwerken.
2.  Een gebruiker waarschuwen via een pop-up dat sommige werkitems aan deze gebruiker zijn toegewezen in een op een werkstroom gebaseerde bedrijfsapp.
3.  Een badge met het nummer van de huidige verkoopkansen weergeven in een CRM-app (zoals Microsoft Dynamics CRM).

##Hoe werken pushmeldingen?

Pushmeldingen worden geleverd via een platformspecifieke infrastructuur, het zogenaamde _Platform Notification System_ (PNS). Een PNS biedt zogenaamde barebonefuncties (dat wil zeggen, er is geen ondersteuning voor uitzending en persoonlijke instellingen) die geen gemeenschappelijke interface hebben. Als een ontwikkelaar bijvoorbeeld een melding wil verzenden naar een Windows Store-app, moet deze contact opnemen met de WNS (Windows Notification Service). Als de ontwikkelaar een melding wil verzenden naar een iOS-apparaat, moet deze contact opnemen met APNs (Apple Push Notification Service) en het bericht nogmaals versturen. Azure Notification hubs bieden een algemene interface plus andere functies die ondersteuning bieden voor pushmeldingen op elk platform.

Op hoog niveau volgen alle platformmeldingssystemen hetzelfde patroon:

1.  De clientapp neemt contact op met de PNS om de _ingang_ op te halen. Het ingangstype is afhankelijk van het systeem. Voor WNS is het een URI of 'meldingskanaal'. Voor APNs is dit een token.
2.  De clientapp slaat deze ingang op in de _back-end_ van de app voor later gebruik. Voor WNS is de back-end doorgaans een cloudservice. Voor Apple heet het systeem een _provider_.
3.  Voor het verzenden van een pushmelding neemt de back-end van de app via de ingang contact op met de PNS om een bepaald exemplaar van een clientapp te bereiken.
4.  De PNS stuurt de melding door naar het apparaat dat is opgegeven door de ingang.

![][0]

##De uitdagingen voor pushmeldingen

Hoewel deze systemen zeer krachtig zijn, is er nog veel werk voor appontwikkelaars om algemenere scenario’s voor pushmeldingen te implementeren, zoals het uitzenden of verzenden van pushmeldingen naar gesegmenteerde gebruikers.

Pushmeldingen zijn een van de meest aangevraagde functies in cloudservices voor mobiele apps. De reden hiervoor is dat de infrastructuur die nodig is om pushmeldingen te kunnen gebruiken relatief complex is en nagenoeg niet gerelateerd is aan de zakelijke logica van de app. Enkele uitdagingen bij het bouwen van een pushinfrastructuur op aanvraag:

- **Platformafhankelijkheid.** Er moeten meerdere interfaces worden gecodeerd in de back-end om meldingen te kunnen verzenden naar apparaten op verschillende platforms. Niet alleen zijn de details op laag niveau verschillend, de wijze waarop een melding wordt weergegeven (tegel, pop-up of badge) is ook platformafhankelijk. Deze verschillen kunnen leiden tot complexe back-end-code die moeilijk bij te werken is.

- **Schalen.** Bij het schalen van de infrastructuur komen twee aspecten om de hoek kijken:
    + Volgens de PNS-richtlijnen moeten de apparaattokens steeds worden vernieuwd zodra de app wordt gestart. Dit leidt tot een grote hoeveelheid verkeer (en databasetoegang) om de apparaattokens bijgewerkt te houden. Wanneer het aantal apparaten groeit (mogelijk tot miljoenen), zijn de kosten van het maken en bijwerken van deze infrastructuur aanzienlijk.

    + De meeste PNS's bieden geen ondersteuning voor uitzending naar meerdere apparaten. Het gevolg: een uitzending naar miljoenen apparaten leidt tot miljoenen aanroepen naar de PNS. Het schalen van deze verzoeken is zeer lastig, omdat appontwikkelaars de totale latentie laag willen houden (bijvoorbeeld, als het laatste apparaat de melding niet ontvangt binnen 30 minuten nadat de melding is verzonden, wordt het hele doel van pushmeldingen voorbij geschoten).
- **Routing.** Een PNS biedt een manier om een bericht naar een apparaat te verzenden. Meestal zijn appmeldingen echter bedoeld voor bepaalde gebruikers of belangengroepen (bijvoorbeeld alle medewerkers die aan een bepaald klantaccount zijn toegewezen). Dus om de meldingen naar de juiste apparaten te versturen, moet de back-end van de app een register bijhouden waarin belangengroepen zijn gekoppeld aan apparaattokens. Dit komt boven op de onderhoudskosten en de tijd om een app op de markt te brengen.

##Waarom werken met Notification Hubs?

Notification Hubs zijn niet complex en u staat niet voor de uitdagingen die pushmeldingen met zich meebrengen. Notification Hubs maken het een stuk eenvoudiger voor u. Ze maken gebruik van een uitgebreide pushmeldinginfrastructuur voor meerdere platforms en er is veel minder push-specifieke code in de back-end van de app nodig. Notification Hubs implementeren alle functionaliteit van een push-infrastructuur. Apparaten zijn alleen verantwoordelijk voor het registreren van PNS-ingangen en de back-end is verantwoordelijk voor het verzenden van platformonafhankelijke berichten aan gebruikers of belangengroepen, zoals wordt weergegeven in de volgende afbeelding:

![][1]


Notification Hubs bieden een kant-en-klare pushmeldinginfrastructuur, die de volgende voordelen biedt:

- **Meerdere platforms.**
    +  Ondersteuning voor alle veelgebruikte mobiele platforms. Notification Hubs kunnen pushmeldingen verzenden naar Windows Store, iOS, Android en Windows Phone-apps.

    +  Notification Hubs beschikken over een algemene interface voor het verzenden van meldingen naar alle ondersteunde platforms. Platformspecifieke protocollen zijn niet vereist. De back-end van de app kan meldingen verzenden in platformspecifieke of platformonafhankelijk indelingen. De toepassing communiceert alleen met Notification Hubs.

    +  Beheer van apparaatingangen. Notification Hubs werken het ingangenregister en de feedback van PNS's bij.

- **Geschikt voor elke back-end**: cloud of lokaal, .NET, PHP, Java, Node, enzovoort.

- **Schalen.** Notification Hubs kunnen worden uitgebreid tot miljoenen apparaten, waarbij shards en opnieuw opbouwen niet nodig zijn.


- **Groot aantal afleveringswijzen**:

    - *Uitzenden*: met één API-aanroep kan een bericht bijna gelijktijdig naar miljoenen apparaten worden uitgezonden.

    - *Unicast/Multicast*: pushen aan tags van afzonderlijke gebruikers en al hun apparaten, of naar een grotere groep, bijvoorbeeld afzonderlijke vormfactoren (tablet versus telefoon).

    - *Segmentering*: pushen naar complexe segmenten die zijn gedefinieerd door code-expressies (bijvoorbeeld apparaten in New York waarop de Yankees worden gevolgd).

    Elk apparaat kan bij het verzenden van de ingang naar een Notification Hub een of meer _tags_ opgeven. Meer informatie over [tags]. Tags hoeven niet vooraf te worden ingericht of verwijderd. Met tags kunt u eenvoudig meldingen verzenden aan bepaalde gebruikers of belangengroepen. Omdat tags een app-specifieke id (zoals een gebruiker- of groep-id) kunnen bevatten, hoeft de back-end van de app de apparaatingangen niet op te slaan en te beheren.

- **Persoonlijke instellingen**: Elk apparaat kan een of meer sjablonen bevatten, zodat lokalisatie per apparaat kan worden uitgevoerd. Bovendien kunnen persoonlijke instellingen worden gebruikt zonder dat dit van invloed is op de back-end-code.

- **Beveiliging**: Shared Access Secret (SAS) of federatieve verificatie.

- **Uitgebreide telemetrie**: beschikbaar in de portal en programmatisch.


##Integratie met App Service - Mobiele apps

Voor een naadloze werking van alle Azure-services biedt [App Service - Mobiele apps] ingebouwde ondersteuning voor pushmeldingen via Notification Hubs. Met [App Service - Mobiele apps] kunnen ontwikkelaars van ondernemingen en systeemintegrators gebruikmaken van een zeer schaalbaar, algemeen beschikbaar ontwikkelplatform voor mobiele toepassingen, dat uitgebreide mogelijkheden biedt voor ontwikkelaars van mobiele apps.

Mobiele apps van ontwikkelaars kunnen gebruikmaken van Notification Hubs in de volgende werkstroom:

1. PNS-ingang van het apparaat ophalen
2. Het apparaat en [sjablonen] registreren met Notification Hubs via de handige Client SDK API voor mobiele apps
    + Om beveiligingsreden worden alle tags op registraties door mobiele apps verwijderd. Gebruik Notification Hubs om vanuit de back-end rechtstreeks tags aan apparaten koppelen.
3. Meldingen verzenden vanuit de back-end van uw app met Notification Hubs

Hier zijn een aantal voordelen voor ontwikkelaars:
- **Client SDK's voor mobiele apps.** Deze SDK's voor meerdere platforms bieden eenvoudige API's voor de registratie en het contact met de Notification Hub die automatisch aan de mobiele app is gekoppeld. Ontwikkelaars hoeven niet op zoek naar de aanmeldgegevens voor Notification Hubs of met een extra service te werken.
    + De SDK's coderen een apparaat automatisch met de geverifieerde gebruikers-id van de mobiele app om het verzenden van pushberichten mogelijk te maken.
    + De SDK's gebruiken de installatie-id van een mobiele app automatisch als GUID om te registreren bij Notification Hubs. Hierdoor hoeven ze niet meerdere service-GUID's meer bij te houden.
- **Installatiemodel.** Mobiele apps werken met het meest recente pushmodel van Notification Hubs. Hierdoor zijn alle pusheigenschappen beschikbaar die aan een apparaat zijn gekoppeld in een JSON-installatie, die aansluit op de Push Notification Services en eenvoudig te gebruiken is.
- **Flexibiliteit.** Ontwikkelaars kunnen altijd beslissen om rechtstreeks met Notification Hubs te werken, zelfs na integratie.
- **Geïntegreerde ervaring in [Azure Portal].** De pushfunctie in mobiele apps is zeer visueel en ontwikkelaars kunnen eenvoudig via mobiele apps met de bijbehorende Notification Hub werken.



##Volgende stappen

Meer informatie over de Notification Hubs vindt u in de volgende onderwerpen:

+ **[Hoe klanten Notification Hubs gebruiken]**

+ **[Zelfstudies en handleidingen voor Notification Hubs]**

+ **Zelfstudies om aan de slag te gaan met Notification Hubs** ([iOS], [Android], [Windows Universal], [Windows Phone], [Kindle], [Xamarin.iOS], [Xamarin.Android])

De relevante .NET API-verwijzingen voor pushmeldingen vindt u hier:

+ [Microsoft.WindowsAzure.Messaging.NotificationHub]
+ [Microsoft.ServiceBus.Notifications]


  [0]: ./media/notification-hubs-overview/registration-diagram.png
  [1]: ./media/notification-hubs-overview/notification-hub-diagram.png
  [Hoe klanten Notification Hubs gebruiken]: http://azure.microsoft.com/services/notification-hubs
  [Zelfstudies en handleidingen voor Notification Hubs]: http://azure.microsoft.com/documentation/services/notification-hubs
  [iOS]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started
  [Android]: http://azure.microsoft.com/documentation/articles/notification-hubs-android-get-started
  [Windows Universal]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started
  [Windows Phone]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started
  [Kindle]: http://azure.microsoft.com/documentation/articles/notification-hubs-kindle-get-started
  [Xamarin.iOS]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-ios-get-started
  [Xamarin.Android]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-android-get-started
  [Microsoft.WindowsAzure.Messaging.NotificationHub]: http://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx
  [Microsoft.ServiceBus.Notifications]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx
  [App Service - Mobiele apps]: https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-value-prop/
  [sjablonen]: notification-hubs-templates.md
  [Azure Portal]: https://portal.azure.com
  [tags]: (http://msdn.microsoft.com/library/azure/dn530749.aspx)



<!--HONumber=Jun16_HO2-->


