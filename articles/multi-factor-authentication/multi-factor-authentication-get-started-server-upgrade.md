<properties 
    pageTitle="De PhoneFactor Agent bijwerken naar Azure Multi-Factor Authentication-server" 
    description="In dit document wordt beschreven hoe u aan de slag gaat met Azure MFA-server en hoe u een upgrade uitvoert vanuit de oudere PhoneFactor-agent." 
    services="multi-factor-authentication" 
    documentationCenter="" 
    authors="billmath" 
    manager="stevenpo" 
    editor="curtland"/>

<tags 
    ms.service="multi-factor-authentication" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="05/12/2016" 
    ms.author="billmath"/>

# De PhoneFactor Agent bijwerken naar Azure Multi-Factor Authentication-server

Om de PhoneFactor Agent v5.x of eerder bij te werken naar de Azure Multi-Factor Authentication-server moeten de PhoneFactor Agent en de bijbehorende onderdelen worden verwijderd voordat de Multi-Factor Authentication-server en de verbonden onderdelen kunnen worden geïnstalleerd. 

## De PhoneFactor Agent bijwerken naar Azure Multi-Factor Authentication-server
<ol>
<li>Maak eerst een back-up van het PhoneFactor-gegevensbestand. De standaardlocatie voor installatie is C:\Program Files\PhoneFactor\Data\Phonefactor.pfdata.


<li>Ga als volgt te werk als de gebruikersportal is geïnstalleerd:</li>
<ol>
<li>Ga naar de installatiemap en maak een back-up van het bestand web.config. De standaardlocatie voor installatie is C:\inetpub\wwwroot\PhoneFactor.</li>


<li>Als u aangepaste thema's aan de portal hebt toegevoegd, maak dan een back-up van uw aangepaste map onder de map C:\inetpub\wwwroot\PhoneFactor\App_Themes.</li>


<li>Verwijder de gebruikersportal via de PhoneFactor Agent (alleen beschikbaar als deze is geïnstalleerd op dezelfde server als de PhoneFactor Agent) of via Windows Programma's en onderdelen.</li></ol>




<li>Ga als volgt te werk als de webservice voor mobiele apps is geïnstalleerd:
<ol>
<li>Ga naar de installatiemap en maak een back-up van het bestand web.config. De standaardlocatie voor installatie is C:\inetpub\wwwroot\PhoneFactorPhoneAppWebService.</li>
<li>Verwijder de webservice voor mobiele apps via Windows Programma's en onderdelen.</li></ol>

<li>Als de webservice-SDK is geïnstalleerd, verwijder deze dan via de PhoneFactor Agent of via Windows Programma's en onderdelen.

<li>Verwijder de PhoneFactor Agent via Windows Programma's en onderdelen.

<li>Installeer de Multi-Factor Authentication-server. Het installatiepad wordt opgehaald uit het register van de vorige installatie van de PhoneFactor Agent. De installatielocatie is dus dezelfde (bijvoorbeeld C:\Program Files\PhoneFactor). Nieuwe installaties hebben een ander standaardinstallatiepad (bijvoorbeeld C:\Program Files\Multi-Factor Authentication Server). Het gegevensbestand van de vorige PhoneFactor Agent moet tijdens de installatie worden bijgewerkt zodat u uw gebruikers en instellingen niet kwijt bent na de installatie van de nieuwe Multi-Factor Authentication-server.

<li>Activeer de Multi-Factor Authentication-server wanneer dit wordt gevraagd en controleer of deze is toegewezen aan de juiste replicatiegroep.

<li>Als de webservice-SDK eerder was geïnstalleerd, installeert u de nieuwe webservice-SDK via de gebruikersinterface van Multi-Factor Authentication-server. De standaardnaam van de virtuele map is nu 'MultiFactorAuthWebServiceSdk', in plaats van 'PhoneFactorWebServiceSdk'. Als u de oude naam wilt gebruiken, moet u de naam van de virtuele map tijdens de installatie wijzigen. Als u de nieuwe standaardnaam wilt gebruiken, moet u de URL wijzigen in alle toepassingen die naar de webservice-SDK verwijzen, zoals de gebruikersportal en de webservice voor mobiele apps, zodat ze naar de juiste locatie wijzigen.

<li>Als de gebruikersportal eerder was geïnstalleerd op de PhoneFactor Agent-server, installeert u de nieuwe Multi-Factor Authentication-gebruikersportal via de gebruikersinterface van de Multi-Factor Authentication-server. De standaardnaam van de virtuele map is nu 'MultiFactorAuth', in plaats van 'PhoneFactor'. Als u de oude naam wilt gebruiken, moet u de naam van de virtuele map tijdens de installatie wijzigen. Als u de nieuwe standaardnaam wilt gebruiken, klikt u op het pictogram Gebruikersportal op de Multi-Factor Authentication-server en werkt u de URL van de gebruikersportal bij op het tabblad Instellingen. 

<li>Ga als volgt te werk als de gebruikersportal en/of webservice voor mobiele apps eerder op een andere server van de PhoneFactor Agent was geïnstalleerd:
<ol>
<li>Ga naar de installatielocatie (bijvoorbeeld C:\Program Files\PhoneFactor) en kopieer de juiste installatieprogramma's naar de andere server. Er zijn 32-bits en 64-bits installatieprogramma's voor zowel de gebruikersportal als de webservice voor mobiele apps. Deze heten respectievelijk MultiFactorAuthenticationUserPortalSetupXX.msi en MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi.</li>
<li>Als u de gebruikersportal op de webserver wilt installeren, opent u een opdrachtprompt als beheerder en voert u de MultiFactorAuthenticationUserPortalSetupXX.msi uit. De standaardnaam van de virtuele map is nu 'MultiFactorAuth', in plaats van 'PhoneFactor'. Als u de oude naam wilt gebruiken, moet u de naam van de virtuele map tijdens de installatie wijzigen. Als u de nieuwe standaardnaam wilt gebruiken, klikt u op het pictogram Gebruikersportal op de Multi-Factor Authentication-server en werkt u de URL van de gebruikersportal bij op het tabblad Instellingen. Bestaande gebruikers moeten op de hoogte worden gebracht van de nieuwe URL.</li>
<li>Ga naar de installatielocatie van de gebruikersportal (bijvoorbeeld C:\inetpub\wwwroot\MultiFactorAuth) en bewerk het bestand web.config. Kopieer de waarden in de secties appSettings en applicationSettings van het oorspronkelijke bestand web.config, waarvan u vóór de upgrade een back-up hebt gemaakt, naar het nieuwe bestand web.config. Als tijdens de installatie van de webservice-SDK de nieuwe standaardnaam van de virtuele map is aangehouden, wijzigt u de URL in de sectie applicationSettings om naar de juiste locatie te verwijzen. Als in het vorige bestand web.config nog andere standaardinstellingen zijn gewijzigd, past u dezelfde wijzigingen ook toe op het nieuwe bestand web.config.</li>
<li>Als u de webservices voor mobiele apps op de webserver wilt installeren, opent u een opdrachtprompt als beheerder en voert u MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi uit. De standaardnaam van de virtuele map is nu 'MultiFactorAuthMobileAppWebService' in plaats van 'PhoneFactorPhoneAppWebService'. Als u de oude naam wilt gebruiken, moet u de naam van de virtuele map tijdens de installatie wijzigen. U kunt een kortere naam kiezen die eindgebruikers gemakkelijker kunnen invoeren op hun mobiele apparaten. Als u de nieuwe standaardnaam wilt gebruiken, klikt u op het pictogram Mobiele app op de Multi-Factor Authentication-server en werkt u de URL van de webservice voor mobiele apps bij.</li>
<li>Ga naar de installatielocatie van de webservice voor mobiele apps (bijvoorbeeld C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService) en bewerk het bestand web.config. Kopieer de waarden in de secties appSettings en applicationSettings van het oorspronkelijke bestand web.config, waarvan u vóór de upgrade een back-up hebt gemaakt, naar het nieuwe bestand web.config. Als tijdens de installatie van de webservice-SDK de nieuwe standaardnaam van de virtuele map is aangehouden, wijzigt u de URL in de sectie applicationSettings om naar de juiste locatie te verwijzen. Als in het vorige bestand web.config nog andere standaardinstellingen zijn gewijzigd, past u dezelfde wijzigingen ook toe op het nieuwe bestand web.config.</li></ol>


 


 


<!--HONumber=Jun16_HO2-->


