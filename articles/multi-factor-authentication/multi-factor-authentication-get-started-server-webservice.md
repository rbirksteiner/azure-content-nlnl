<properties 
    pageTitle="Aan de slag met de webservice voor mobiele apps voor de MFA-server" 
    description="De Azure Multi-Factor Authentication-app biedt een extra optie voor verificatie buiten de band.  Hierdoor kan de MFA-server pushmeldingen naar gebruikers sturen." 
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

# Aan de slag met de webservice voor mobiele apps voor de MFA-server

De Azure Multi-Factor Authentication-app biedt een extra optie voor verificatie buiten de band. In plaats van tijdens het aanmelden een automatisch telefoongesprek of SMS-bericht met de gebruiker tot stand te brengen, stuurt Azure Multi-Factor Authentication een melding naar de Azure Multi-Factor Authentication-app op de smartphone of tablet van de gebruiker. De gebruiker hoeft in de app enkel op 'Verifiëren' te tikken (of een pincode in te voeren en op 'Verifiëren te tikken) om zich aan te melden. 

Voor het gebruik van de Azure Multi-Factor Authentication-app is het volgende vereist om de app correct te laten communiceren met de webservice voor mobiele apps: 

- Controleer of aan deze hardware- en softwarevereisten is voldaan
- U moet versie 6.0 of een recentere versie van de Azure Multi-Factor Authentication-server gebruiken.
- De webservice voor mobiele apps moet zijn geïnstalleerd op een internetgerichte webserver waarop Microsoft® Internet Information Services (IIS) 7.x of een recentere versie wordt uitgevoerd.  Zie [IIS.NET](http://www.iis.net/) voor meer informatie over IIS.
- Zorg dat ASP.NET v4.0.30319 is geïnstalleerd, is geregistreerd en is ingesteld op Toegestaan.
- Vereiste functieservices omvatten ASP.NET en compatibiliteit met IIS 6-metabase.
- Webservice voor mobiele apps moet toegankelijk zijn via een openbare URL.
- Webservice voor mobiele apps moet met een SSL-certificaat zijn beveiligd.
- De webservice-SDK van Azure Multi-Factor Authentication moet zijn geïnstalleerd in IIS 7.x of hoger op de server waarop de Azure Multi-Factor Authentication-server is geïnstalleerd.
- De webservice-SDK van Azure Multi-Factor Authentication moet zijn beveiligd met een SSL-certificaat.
- De webservice voor mobiele apps moet via SSL verbinding kunnen maken met de webservice-SDK van Azure Multi-Factor Authentication.
- De webservice voor mobiele apps moet bij de webservice-SDK van Azure Multi-Factor Authentication kunnen verifiëren met behulp van de referenties van een serviceaccount dat lid is van een beveiligingsgroep met de naam PhoneFactor Admins. Dit serviceaccount en deze groep bevinden zich in Active Directory als de Azure Multi-Factor Authentication-server wordt uitgevoerd op een server die aan een domein is gekoppeld. Dit serviceaccount en deze groep bevinden zich lokaal op de Azure Multi-Factor Authentication-server als deze niet aan een domein is gekoppeld.


Voor de installatie van de gebruikersportal op een andere server dan de Azure Multi-Factor Authentication-server moeten de volgende drie stappen worden uitgevoerd:

1. De webservice-SDK installeren
2. De webservice voor mobiele apps installeren
3. De instellingen voor de webservice voor mobiele apps op de Azure Multi-Factor Authentication-server configureren
4. De Azure Multi-Factor Authentication-app voor eindgebruikers activeren

## De webservice-SDK installeren

Als de webservice-SDK van de Azure Multi-Factor Authentication nog niet op de Azure Multi-Factor Authentication-server is geïnstalleerd, gaat u naar die server en opent u de Azure Multi-Factor Authentication-server. Klik op het pictogram van de webservice-SDK, klik op de knop Webservice-SDK installeren en volg de aanwijzingen op het scherm. De webservice-SDK moet met een SSL-certificaat worden beveiligd. Een zelfondertekend certificaat volstaat hiervoor, maar het moet dan wel worden geïmporteerd in het certificaatarchief Vertrouwde basiscertificeringsinstanties van het account Lokale computer op de webserver van de gebruikersportal, zodat daar het certificaat wordt vertrouwd wanneer de SSL-verbinding tot stand wordt gebracht. 

<center>![Instellen](./media/multi-factor-authentication-get-started-server-webservice/sdk.png)</center>

## De webservice voor mobiele apps installeren
Voordat u de webservice voor mobiele apps installeert, moet u op het volgende letten:

- Als de gebruikersportal van Azure Multi-Factor Authentication al is geïnstalleerd op de internetgerichte server, kunnen de gebruikersnaam, het wachtwoord en de URL uit het bestand web.config van de gebruikersportal naar de webservice-SDK worden gekopieerd. 
- Het is handig om een webbrowser te openen op de internetgerichte webserver en te navigeren naar de URL van de webservice-SDK die in het bestand web.config is ingevoerd. Als de browser de webservice kan bereiken, wordt u normaal om referenties gevraagd. Geef de gebruikersnaam en het wachtwoord op precies zoals deze in het bestand web.config zijn ingevoerd. Controleer of er geen certificaatwaarschuwingen of -fouten worden weergegeven.
- Als zich vóór de webserver van de webservice voor mobiele apps een omgekeerde proxy of firewall bevindt en SSL-offloading wordt uitvoert, kunt u het bestand web.config van de webservice voor mobiele apps bewerken en de volgende sleutel toevoegen aan de sectie <appSettings>, zodat de webservice voor mobiele apps http in plaats van https kan gebruiken. SSL is echter nog steeds vereist vanuit de mobiele app naar de firewall/omgekeerde proxy. <add key="SSL_REQUIRED" value="false"/> 

### De webservice voor mobiele apps installeren

<ol>
<li>Open Windows Verkenner op de Azure Multi-Factor Authentication-server en navigeer naar de map waarin de Azure Multi-Factor Authentication-server is geïnstalleerd (bijvoorbeeld C:\Program Files\Azure Multi-Factor Authentication). Kies de 32-bits- of 64-bitsversie van het Azure-installatiebestand MultiFactorAuthenticationPhoneAppWebServiceSetup dat geschikt is voor de server waarop de webservice voor mobiele apps wordt geïnstalleerd. Kopieer het installatiebestand naar de internetgerichte server.</li> 

<li>Op de internetgerichte webserver moet het installatiebestand worden uitgevoerd met beheerdersrechten. De eenvoudigste manier om dit te doen is een opdrachtprompt openen als een beheerder en navigeren naar de locatie waar het installatiebestand is gekopieerd.</li>  

<li>Voer het installatiebestand MultiFactorAuthenticationMobileAppWebServiceSetup uit, waarbij u desgewenst de locatie kunt wijzigen en de naam van de virtuele map kunt wijzigen in een korte naam, bijvoorbeeld 'PA'. De korte naam voor de virtuele map wordt aanbevolen omdat gebruikers tijdens de activering de URL voor de webservice voor mobiele apps op hun mobiele apparaat moeten invoeren.</li> 

<li>Wanneer het Azure-installatiebestand MultiFactorAuthenticationMobileAppWebServiceSetup is geïnstalleerd, bladert u naar C:\inetpub\wwwroot\PA (of de overeenkomstige map op basis van de naam van de virtuele map) en bewerkt u het bestand web.config.</li>  

<li>Zoek de sleutels WEB_SERVICE_SDK_AUTHENTICATION_USERNAME en WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD en stel de waarden in op de gebruikersnaam en het wachtwoord van het serviceaccount dat lid is van de beveiligingsgroep PhoneFactor Admins (zie de sectie Vereisten hierboven). Dit kan hetzelfde account zijn dat ook is gebruikt als de identiteit van de gebruikersportal van Azure Multi-Factor Authentication, als die eerder is geïnstalleerd. Zorg ervoor dat u de gebruikersnaam en het wachtwoord invoert tussen de aanhalingstekens aan het einde van de regel (value=””/>). Het is raadzaam om een gekwalificeerde gebruikersnaam (bijvoorbeeld domein\gebruikersnaam of computer\gebruikersnaam) te gebruiken.</li>  

<li>Zoek de instelling pfMobile App Web Service_pfwssdk_PfWsSdk en wijzig de waarde van http://localhost:4898/PfWsSdk.asmx in de URL van de webservice-SDK die wordt uitgevoerd op de Azure Multi-Factor Authentication-server (bijvoorbeeld https://computer1.domein.lokaal/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx). Omdat voor deze verbinding SSL wordt gebruikt, moet u naar de webservice-SDK verwijzen met de servernaam en niet met het IP-adres, omdat het SSL-certificaat wordt uitgegeven voor de servernaam. Hierdoor moet de gebruikte URL overeenkomen met de naam op het certificaat. Als de naam van de server niet wordt omgezet in een IP-adres van de internetgerichte server, kunt u een vermelding toevoegen aan het hostbestand op die server, om de naam van de Azure Multi-Factor Authentication-server toe te wijzen aan het IP-adres ervan. Sla het bestand web.config op nadat de wijzigingen zijn aangebracht.</li>  

<li>Als de website waaronder de webservice voor mobiele apps is geïnstalleerd (bijvoorbeeld Standaardwebsite) nog niet is verbonden met een openbaar ondertekend certificaat, installeert u het certificaat op de server, als dit nog niet is gebeurd. Vervolgens opent u IIS-beheer en verbindt u het certificaat aan de website.</li>  

<li>Open op een computer een webbrowser en navigeer naar de URL waar de webservice voor mobiele apps is geïnstalleerd (bijvoorbeeld https://www.publicwebsite.com/PA). Controleer of er geen certificaatwaarschuwingen of -fouten worden weergegeven.</li> 

### De instellingen voor de webservice voor mobiele apps op de Azure Multi-Factor Authentication-server configureren
Nu de webservice voor mobiele apps is geïnstalleerd, moet u de Azure Multi-Factor Authentication-server voor gebruik met de gebruikersportal configureren.

#### De instellingen voor mobiele apps op de Azure Multi-Factor Authentication-server configureren

1. Klik op de Azure Multi-Factor Authentication-server op het pictogram Gebruikersportal. Als gebruikers hun verificatiemethoden mogen bepalen, schakelt u op het tabblad Instellingen onder Toestaan dat gebruikers de methode selecteren de optie Mobiele app in. Als deze functie niet is ingeschakeld, moeten eindgebruikers contact opnemen met uw helpdesk om de activering van de mobiele App te voltooien.
2. Schakel het selectievakje Toestaan dat gebruikers de mobiele app activeren in.
3. Schakel het selectievakje Registreren van gebruikers toestaan in.
4. Klik op het pictogram Mobiele app.
5. Voer de URL in die wordt gebruikt met de virtuele map die is gemaakt bij de installatie van de Azure- MultiFactorAuthenticationMobileAppWebServiceSetup. U kunt een accountnaam invoeren in het daarvoor bestemde vak. Deze bedrijfsnaam wordt weergegeven in de mobiele toepassing. Als u dit vak leeg laat, wordt de naam weergegeven van de Multi-Factor Authentication-Provider die in de Azure-beheerportal is gemaakt. 



<center>![Instellen](./media/multi-factor-authentication-get-started-server-webservice/mobile.png)</center>
 


<!--HONumber=Jun16_HO2-->


