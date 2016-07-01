<properties
    pageTitle="Wat zijn Mobile Apps?"
    description="Lees welke voordelen App Service heeft voor de mobiele apps in uw onderneming."
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="05/03/2016"
    ms.author="krisagh"/>

# <a name="getting-started"> </a>Wat zijn Mobile Apps?

Azure App Service is een volledig beheerd PaaS (Platform as a Service) voor professionele ontwikkelaars dat uitgebreide mogelijkheden bevat voor web-, mobiele en integratiescenario's. Met *Mobile Apps* in *Azure App Service* kunnen ontwikkelaars van ondernemingen en systeemintegrators gebruikmaken van een zeer schaalbaar, algemeen beschikbaar ontwikkelplatform voor mobiele toepassingen, dat uitgebreide mogelijkheden biedt voor ontwikkelaars van mobiele apps.

![Mobile Apps](./media/app-service-mobile-value-prop/overview.png)

##Waarom Mobile Apps?
Met *Mobile Apps* in *Azure App Service* kunnen ontwikkelaars van ondernemingen en systeemintegrators gebruikmaken van een zeer schaalbaar, algemeen beschikbaar ontwikkelplatform voor mobiele toepassingen, dat uitgebreide mogelijkheden biedt voor ontwikkelaars van mobiele apps. Met Mobile Apps kunt u het volgende doen:

- **Systeemeigen en platformoverschrijdende apps bouwen** - Of u nu systeemeigen iOS-, Android- of Windows-apps of platformoverschrijdende Xamarin- of Cordova-apps (Phonegap) bouwt, u kunt altijd profiteren van App Service met behulp van systeemeigen SDK's.
- **Verbinding maken met uw ondernemingssystemen** - Met Mobile Apps kunt u in slechts enkele minuten zakelijke aanmeldingen toevoegen en on-premises of via cloudresources verbinding maken met uw bedrijf.
- **Apps bouwen die offline beschikbaar zijn met gegevenssynchronisatie** - Maak uw mobiele werknemers productief door apps te bouwen die offline werken en door gebruik te maken van Mobile Apps om gegevens op de achtergrond te synchroniseren wanneer er verbinding is met een van uw gegevensbronnen of SaaS-API's in de onderneming.
- **Pushmeldingen in enkele seconden naar miljoenen klanten verzenden** - Houd contact met uw klanten door gebruik te maken van directe pushmeldingen op elk apparaat, afgestemd op de eigen behoeften van de klant en verzonden op het gewenste moment.

## Functies voor mobiele apps
De volgende functies zijn belangrijk wanneer u mobiele apps ontwikkelt die zijn ingeschakeld voor de cloud:

- **Verificatie en autorisatie** - U kunt kiezen uit de steeds groeiende lijst met identiteitsproviders, waaronder Azure Active Directory voor ondernemingsverificatie, plus sociaalnetwerkproviders zoals Twitter, Facebook, Google en Microsoft-account.  Mobile Apps van Azure biedt een OAuth 2.0-service voor elke provider.  Daarnaast kunt u de SDK voor de identiteitsprovider integreren voor providerspecifieke functionaliteit.

  Lees meer over onze [verificatiefuncties].

- **Gegevenstoegang** - Mobile Apps van Azure biedt een voor mobiele apparaten geschikte OData v3-gegevensbron die is gekoppeld aan SQL Azure of een on-premises SQL Server.  Deze service kan worden gebaseerd op Entity Framework, zodat u eenvoudig kunt integreren met andere NoSQL- en SQL-gegevensproviders, zoals [Azure-tabelopslag], MongoDB, [DocumentDB] en SaaS API-providers, zoals Office 365 en Salesforce.com.
- **Offline synchronisatie** - Met onze client-SDK's kunt u eenvoudig robuuste en responsieve mobiele toepassingen bouwen die werken met een offlinegegevensset die automatisch kan worden gesynchroniseerd met de back-endgegevens, met ondersteuning van conflictoplossing.

  Lees meer over onze [gegevensfuncties].

- **Pushmeldingen** - Onze client-SDK's kunnen probleemloos worden geïntegreerd met de registratiemogelijkheden van Azure Notification Hubs, zodat u pushmeldingen naar miljoenen gebruikers tegelijk kunt verzenden.

  Lees meer over onze [functies voor pushmeldingen].

- **Client-SDK's** - Wij bieden een volledige set client-SDK's voor systeemeigen ontwikkeling ([iOS], [Android] en [Windows]), platformoverschrijdende ontwikkeling ([Xamarin voor iOS en Android], [Xamarin Forms]) en hybride toepassingsontwikkeling ([Apache Cordova]).  Elke client-SDK is beschikbaar met een MIT-licentie en is open source.

## Functies van Azure App Service
De volgende platformfuncties komen eigenlijk altijd van pas voor mobiele productiesites.

- **Automatische schaling** - Met App Service kunt u snel omhoog of omlaag schalen om in te spelen op de inkomende belasting van klanten. U kunt handmatig het aantal virtuele machines en de grootte ervan selecteren of automatische schaling instellen, zodat de back-end voor uw mobiele app wordt geschaald op basis van uw belasting of schema.

  Lees meer over [automatische schaling].

- **Faseringsomgevingen** - Met App Service kunt u meerdere versies van uw site uitvoeren, zodat u A/B-tests, tests in de productieomgeving als onderdeel van een groter DevOps-plan en in-place fasering van een nieuwe back-end kunt uitvoeren.

  Lees meer over [faseringsomgevingen].

- **Doorlopende implementatie** - App Service kan worden geïntegreerd met algemene SCM-systemen, zodat u automatisch een nieuwe versie van uw back-end kunt implementeren door een vertakking van uw SCM-systeem te pushen.

  Lees meer over [implementatieopties].

- **Virtuele netwerken** - App Service kan verbinding maken met on-premises resources met behulp van een virtueel netwerk, ExpressRoute of hybride verbindingen.

  Lees meer over [hybride verbindingen], [virtuele netwerken] en [ExpressRoute].

- **Geïsoleerde/toegewezen omgevingen** - App Service kan worden uitgevoerd in een volledig geïsoleerde en toegewezen omgeving zodat Azure App Service-apps veilig en op grote schaal kunnen worden uitgevoerd.  Dit is ideaal voor toepassingsworkloads die op grote schaal worden uitgevoerd, of waarvoor isolatie of beveiligde netwerktoegang nodig is.

  Leer meer over [App Service-omgevingen].

## Aan de slag ##
Als u aan de slag wilt met Mobile Apps, volgt u de zelfstudie in [Get Started] (Aan de slag).  Hierin vindt u basisprincipes voor het produceren van een mobiele back-end van uw keuze, informatie over het integreren van verificatie, offlinesynchronisatie en pushmeldingen.  U kunt de zelfstudie in [Get started] (Aan de slag) meerdere keren volgen (één keer voor elke clienttoepassing).

Zie ons [leeroverzicht] voor meer informatie over Azure Mobile Apps.
Zie [Azure App Service] voor meer informatie over het Azure App Service-platform.

>[AZURE.NOTE] Als u aan de slag wilt met Azure App Service voordat u zich aanmeldt voor een Azure-account, gaat u naar [App Service uitproberen](https://tryappservice.azure.com/?appServiceName=mobile). Hier kunt u direct een tijdelijke web-app maken in App Service. U hebt geen creditcard nodig en u doet geen toezeggingen.

<!-- URLs. -->
[Mobile Service migreren naar App Service]: app-service-mobile-migrating-from-mobile-services.md
[Azure App Service]: ../app-service/app-service-value-prop-what-is.md
[Aan de slag]: app-service-mobile-ios-get-started.md
[Azure-tabelopslag]: ../storage/storage-getting-started-guide.md
[DocumentDB]: ../documentdb/documentdb-get-started.md
[verificatiefuncties]: ./app-service-mobile-auth.md
[gegevensfuncties]: ./app-service-mobile-offline-data-sync.md
[functies voor pushmeldingen]: ../notification-hubs/notification-hubs-overview.md
[iOS]: ./app-service-mobile-ios-how-to-use-client-library.md
[Android]: ./app-service-mobile-android-how-to-use-client-library.md
[Windows]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin voor iOS en Android]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin Forms]: ./app-service-mobile-xamarin-forms-get-started.md
[Apache Cordova]: ./app-service-mobile-cordova-how-to-use-client-library.md
[automatische schaling]: ../app-service-web/web-sites-scale.md
[faseringsomgevingen]: ../app-service-web/web-sites-staged-publishing.md
[implementatieopties]: ../app-service-web/web-sites-deploy.md
[hybride verbindingen]: ../app-service-web/web-sites-hybrid-connection-get-started.md
[virtuele netwerken]: ../app-service-web/web-sites-integrate-with-vnet.md
[ExpressRoute]: ../app-service/app-service-app-service-environment-network-configuration-expressroute.md
[App Service-omgevingen]: ../app-service-web/app-service-app-service-environment-intro.md
[leeroverzicht]: https://azure.microsoft.com/en-us/documentation/learning-paths/appservice-mobileapps/



<!--HONumber=Jun16_HO2-->


