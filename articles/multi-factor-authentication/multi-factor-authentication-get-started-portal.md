<properties 
    pageTitle="De gebruikersportal implementeren voor de Azure Multi-Factor Authentication-server" 
    description="Dit is de Azure Multi-Factor Authentication-pagina waarop wordt beschreven hoe u met Azure MFA en de gebruikersportal aan de slag kunt gaan." 
    services="multi-factor-authentication" 
    documentationCenter="" 
    authors="billmath" 
    manager="stevenpo" 
    editor="curtand"/>

<tags 
    ms.service="multi-factor-authentication" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="05/16/2016" 
    ms.author="billmath"/>

# De gebruikersportal implementeren voor de Azure Multi-Factor Authentication-server

De gebruikersportal biedt de beheerder de mogelijkheid om de Azure Multi-Factor Authentication-gebruikersportal te installeren en te configureren. De gebruikersportal is een IIS-website waar gebruikers zich kunnen registreren bij Azure Multi-Factor Authentication en hun account kunnen beheren. Een gebruiker kan bij de volgende aanmelding zijn telefoonnummer en pincode wijzigen of Azure Multi-Factor Authentication overslaan. 

Gebruikers zullen zich bij de gebruikersportal aanmelden met hun gebruikelijke gebruikersnaam en wachtwoord en zullen een Azure Multi-Factor Authentication-telefoonoproep aannemen of beveiligingsvragen beantwoorden om hun verificatie te voltooien. Als gebruikersregistratie is toegestaan, zal een gebruiker zijn telefoonnummer en pincode configureren de eerste keer dat deze zich aanmeldt bij de gebruikersportal. 

Er kunnen beheerders voor de gebruikersportal worden ingesteld. Aan hen kan ook een machtiging worden verleend om nieuwe gebruikers toe te voegen en bestaande gebruikers bij te werken. 

<center>![Instellen](./media/multi-factor-authentication-get-started-portal/install.png)</center>

## De gebruikersportal op dezelfde server implementeren als de Azure Multi-Factor Authentication-server

Als u de gebruikersportal op dezelfde server als de Azure Multi-Factor Authentication-server wilt installeren, moet aan de volgende vereisten worden voldaan:

- IIS moet worden geïnstalleerd met inbegrip van asp.net en compatibiliteit met IIS 6-metabase (voor IIS 7 of hoger) 
- De aangemelde gebruiker moet beschikken over beheerdersrechten voor de computer en het domein, indien van toepassing.  De reden hiervoor is dat het account machtigingen nodig heeft om Active Directory-beveiligingsgroepen te maken.

### De gebruikersportal implementeren voor de Azure Multi-Factor Authentication-server

1. Op de Azure Multi-Factor Authentication-server: klik in het menu links op het Gebruikersportal-pictogram en daarna op de knop Gebruikersportal installeren. 
1. Klik op Volgende.
1. Klik op Volgende.
1. Als de computer lid is van een domein en de Active Directory-configuratie voor de  beveiliging van de communicatie tussen de gebruikersportal en de Multi-Factor Authentication-service niet is voltooid, wordt de Active Directory-stap weergegeven. Klik op de knop Volgende om deze configuratie automatisch te voltooien.
1. Klik op Volgende.
1. Klik op Volgende.
1. Klik op Sluiten.
1. Open op een computer een webbrowser en navigeer naar de URL waar de gebruikersportal is geïnstalleerd (bijvoorbeeld https://www.publicwebsite.com/MultiFactorAuth). Zorg ervoor dat er geen certificaatwaarschuwingen of -fouten worden weergegeven.

<center>![Instellen](./media/multi-factor-authentication-get-started-portal/portal.png)</center>

## De gebruikersportal van de Azure Multi-Factor Authentication-server implementeren op een afzonderlijke server

Voor het gebruik van de Azure Multi-Factor Authentication-app is het volgende vereist om de app correct te laten communiceren met de gebruikersportal: 

Controleer of aan deze hardware- en softwarevereisten is voldaan:

- U moet versie 6.0 of een recentere versie van de Azure Multi-Factor Authentication-server gebruiken.
- De gebruikersportal moet zijn geïnstalleerd op een internetgerichte webserver waarop Microsoft® Internet Information Services (IIS) 6.x IIS 7.x of een recentere versie wordt uitgevoerd.
- Wanneer u IIS 6.x gebruikt, zorgt u ervoor dat ASP.NET v2.0.50727 is geïnstalleerd, is geregistreerd en is ingesteld op Toegestaan.
- Vereiste functieservices bij het gebruik van IIS 7.x of een recentere versie omvatten ASP.NET en compatibiliteit met IIS 6-metabase.
- De gebruikersportal moet zijn beveiligd met een SSL-certificaat.
- De webservice-SDK van Azure Multi-Factor Authentication moet zijn geïnstalleerd in IIS 6.x, IIS 7.x of een recentere versie op de server waarop de Azure Multi-Factor Authentication-server is geïnstalleerd.
- De webservice-SDK van Azure Multi-Factor Authentication moet zijn beveiligd met een SSL-certificaat.
- De gebruikersportal moet via SSL verbinding kunnen maken met de webservice-SDK van Azure Multi-Factor Authentication.
- De gebruikersportal moet bij de webservice-SDK van Azure Multi-Factor Authentication kunnen verifiëren met behulp van de referenties van een serviceaccount dat lid is van een beveiligingsgroep met de naam PhoneFactor Admins. Dit serviceaccount en deze groep bevinden zich in Active Directory als de Azure Multi-Factor Authentication-server wordt uitgevoerd op een server die aan een domein is gekoppeld. Dit serviceaccount en deze groep bevinden zich lokaal op de Azure Multi-Factor Authentication-server als deze niet aan een domein is gekoppeld.

Voor de installatie van de gebruikersportal op een andere server dan de Azure Multi-Factor Authentication-server moeten de volgende drie stappen worden uitgevoerd:

1. De webservice-SDK installeren
2. De gebruikersportal installeren
3. De instellingen voor de gebruikersportal configureren in de Azure Multi-Factor Authentication-server


### De webservice-SDK installeren

Als de webservice-SDK van de Azure Multi-Factor Authentication nog niet op de Azure Multi-Factor Authentication-server is geïnstalleerd, gaat u naar die server en opent u de Azure Multi-Factor Authentication-server. Klik op het pictogram van de webservice-SDK, klik op de knop Webservice-SDK installeren  en volg de aanwijzingen op het scherm. De webservice-SDK moet met een SSL-certificaat worden beveiligd. Een zelfondertekend certificaat volstaat hiervoor, maar het moet dan wel worden geïmporteerd in het certificaatarchief Vertrouwde basiscertificeringsinstanties van het account Lokale computer op de webserver van de gebruikersportal, zodat daar het certificaat wordt vertrouwd wanneer de SSL-verbinding tot stand wordt gebracht. 

<center>![Instellen](./media/multi-factor-authentication-get-started-portal/sdk.png)</center>

### De gebruikersportal installeren

Voordat u de gebruikersportal op een aparte server installeert, houdt u rekening met het volgende:

- Het is handig om een webbrowser te openen op de internetgerichte webserver en te navigeren naar de URL van de webservice-SDK die in het bestand web.config is ingevoerd. Als de browser de webservice kan bereiken, wordt u normaal om referenties gevraagd. Geef de gebruikersnaam en het wachtwoord op precies zoals deze in het bestand web.config zijn ingevoerd. Zorg ervoor dat er geen certificaatwaarschuwingen of -fouten worden weergegeven.
- Als zich voor de webserver van de gebruikersportal een omgekeerde proxy of firewall bevindt en SSL-offloading uitvoert, kunt u het bestand web.config van de gebruikersportal bewerken en de volgende sleutel toevoegen aan de sectie <appSettings>, zodat de gebruikersportal http in plaats van https kan gebruiken. <add key="SSL_REQUIRED" value="false"/>

#### De gebruikersportal installeren

1. Open Windows Verkenner op de Azure Multi-Factor Authentication-server en navigeer naar de map waarin de Azure Multi-Factor Authentication-server is geïnstalleerd (bijvoorbeeld C:\Program Files\Multi-Factor Authentication-server). Kies de 32-bits of 64-bits versie van het MultiFactorAuthenticationUserPortalSetup-installatiebestand, die geschikt is voor de server waarop de gebruikersportal wordt geïnstalleerd. Kopieer het installatiebestand naar de internetgerichte server.
2. Op de internetgerichte webserver moet het installatiebestand worden uitgevoerd met beheerdersrechten. De eenvoudigste manier om dit te doen is een opdrachtprompt openen als een beheerder en navigeren naar de locatie waar het installatiebestand is gekopieerd.
3. Voer het MultiFactorAuthenticationUserPortalSetup64-installatiebestand uit en wijzig de naam van de site en de virtuele map, indien gewenst.
4. Nadat de installatie van de gebruikersportal is voltooid, bladert u naar C:\inetpub\wwwroot\MultiFactorAuth (of de overeenkomstige map op basis van de naam van de virtuele map) en bewerkt u het bestand web.config.
5. Zoek de sleutel USE_WEB_SERVICE_SDK en wijzig de waarde van false (onwaar) in true (waar). Zoek de sleutels WEB_SERVICE_SDK_AUTHENTICATION_USERNAME en WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD en stel de waarden in op de gebruikersnaam en het wachtwoord van het serviceaccount dat lid is van de beveiligingsgroep PhoneFactor Admins (zie de sectie Vereisten hierboven). Zorg ervoor dat u de gebruikersnaam en het wachtwoord invoert tussen de aanhalingstekens aan het einde van de regel (value=””/>). Het is raadzaam om een gekwalificeerde gebruikersnaam (bijvoorbeeld domein\gebruikersnaam of computer\gebruikersnaam) te gebruiken.
6. Zoek de instelling pfup_pfwssdk_PfWsSdk en wijzig de waarde van http://localhost:4898/PfWsSdk.asmx in de URL van de webservice-SDK die wordt uitgevoerd op de Azure Multi-Factor Authentication-server (bijvoorbeeld https://computer1.domein.lokaal/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx). Omdat voor deze verbinding SSL wordt gebruikt, moet u naar de webservice-SDK verwijzen met de servernaam en niet met het IP-adres omdat het SSL-certificaat wordt uitgegeven voor de servernaam. Hierdoor moet de gebruikte URL overeenkomen met de naam op het certificaat. Als de naam van de server niet wordt omgezet in een IP-adres van de internetgerichte server, kunt u een vermelding toevoegen aan het hostbestand op die server, om de naam van de Azure Multi-Factor Authentication-server toe te wijzen aan het IP-adres ervan. Sla het bestand web.config op nadat de wijzigingen zijn aangebracht.
7. Als de website waaronder de gebruikersportal is geïnstalleerd (bijvoorbeeld Standaardwebsite) nog niet is verbonden met een openbaar ondertekend certificaat, installeert u het certificaat op de server als dit nog niet is gebeurd, opent u IIS-beheer en verbindt u het certificaat aan de website.
8. Open op een computer een webbrowser en navigeer naar de URL waar de gebruikersportal is geïnstalleerd (bijvoorbeeld https://www.publicwebsite.com/MultiFactorAuth). Zorg ervoor dat er geen certificaatwaarschuwingen of -fouten worden weergegeven.



## De instellingen voor de gebruikersportal op de Azure Multi-Factor Authentication-server configureren
Nu de portal is geïnstalleerd, moet u de Azure Multi-Factor Authentication-server voor gebruik met de portal configureren.

De Azure Multi-Factor Authentication-server biedt verschillende opties voor de gebruikersportal.  De volgende tabel geeft een lijst van deze opties en een verklaring van de taak waarvoor ze worden gebruikt.

Instellingen van gebruikersportal|Beschrijving|
:------------- | :------------- | 
URL gebruikersportal| Hiermee kunt u de URL invoeren van de locatie waar de portal wordt gehost.
Primaire authenticatie| Hiermee kunt u opgeven welk verificatietype moet worden gebruikt bij de aanmelding bij de portal:  Windows-, Radius- of LDAP-authenticatie.
Aanmelden van gebruikers toestaan|Hiermee kunnen gebruikers een gebruikersnaam en wachtwoord invoeren op de aanmeldingspagina voor de gebruikersportal.  Als deze instelling niet is ingeschakeld, worden de vakken grijs weergegeven.
Registreren van gebruikers toestaan|Hiermee kunnen gebruikers zich registreren voor meervoudige verificatie doordat ze naar een installatiescherm worden geleid waar om aanvullende informatie zoals een telefoonnummer wordt gevraagd.  Bij Vragen om alternatief telefoonnummer kunnen gebruikers een tweede telefoonnummer opgeven.  Bij Vragen om OATH-token van derden kunnen gebruikers een OATH-token van een derde opgeven.
Gebruikers toestaan om eenmalige toegang te initiëren| Hiermee kunnen gebruikers eenmalige toegang initiëren.  Deze optie wordt van kracht wanneer de gebruiker zich de volgende keer aanmeldt.  De optie Vragen om bypass-seconden biedt gebruikers een invoervak, waarin zij de standaardwaarde van 300 seconden kunnen wijzigen.  Anders duurt de eenmalige toegang slechts 300 seconden.
Toestaan dat gebruikers de methode selecteren| Hiermee kan een gebruiker zijn primaire contactmethode opgeven.  Dit kan zijn: telefoonoproep, sms-bericht, mobiele app of OATH-token.
Toestaan dat gebruikers de taal selecteren|  Hiermee kan de gebruiker de taal wijzigen die wordt gebruikt voor de telefoonoproep, het sms-bericht, de mobiele app of het OATH-token.
Toestaan dat gebruikers de mobiele app activeren| Hiermee kunnen de gebruikers een activeringscode genereren om het activeringsproces van de mobiele app te voltooien dat bij de server wordt gebruikt.  U kunt ook instellen op hoeveel apparaten zij dit kunnen activeren.  Kies een waarde van 1 tot 10.
Beveiligingsvragen gebruiken als terugvaloptie|Hiermee kunt u beveiligingsvragen gebruiken indien de meervoudige verificatie mislukt.  U kunt opgeven hoeveel beveiligingsvragen juist moeten worden beantwoord.
Toestaan dat gebruikers OATH-token van derden koppelen| Hiermee kunnen gebruikers een OATH-token van derden opgeven.
OATH-token gebruiken als terugvaloptie|Hiermee kan een OATH-token worden gebruikt indien de meervoudige verificatie mislukt.  U kunt ook de sessietime-out in minuten opgeven.
Logboekregistratie inschakelen|Schakelt de logboekregistratie in op de gebruikersportal.  De logboekbestanden bevinden zich op: C:\Program Files\Multi-Factor Authentication Server\Logs.

De meeste van deze instellingen zijn zichtbaar voor de gebruiker zodra deze zijn ingeschakeld en de gebruiker zich bij de gebruikersportal heeft aangemeld.

![Instellingen gebruikersportal](./media/multi-factor-authentication-get-started-portal/portalsettings.png)



### De instellingen voor de gebruikersportal configureren op de Azure Multi-Factor Authentication-server




1. Klik op de Azure Multi-Factor Authentication-server op het pictogram Gebruikersportal. Voer op het tabblad Instellingen in het tekstvak URL gebruikersportal de URL naar de gebruikersportal in. Als de e-mailfunctionaliteit is ingeschakeld, zal deze URL worden ingevoegd in e-mailberichten die worden verzonden naar gebruikers wanneer ze worden geïmporteerd op de Azure Multi-Factor Authentication-server.
2. Kies de instellingen die u in de gebruikersportal wilt gebruiken. Als u bijvoorbeeld wilt toestaan dat gebruikers hun verificatiemethoden beheren, zorgt u ervoor dat de optie Toestaan dat gebruikers de methode selecteren wordt ingeschakeld, samen met de methoden waaruit ze kunnen kiezen.
3. Klik rechtsboven op de Help-koppeling voor meer informatie over de weergegeven instellingen.

<center>![Instellen](./media/multi-factor-authentication-get-started-portal/config.png)</center>


## Tabblad Beheerders
Op dit tabblad kunt u gewoon gebruikers toevoegen die over beheerdersrechten zullen beschikken.  Wanneer u een beheerder toevoegt, kunt u de machtigingen die zij krijgen exact instellen.  Op die manier weet u zeker dat u aan de beheerder alleen de benodigde machtigingen verleent.  Klik gewoon op de knop Toevoegen en selecteer vervolgens een gebruiker en de bijbehorende machtigingen. Klik vervolgens op Toevoegen.

![Beheerders van gebruikersportal](./media/multi-factor-authentication-get-started-portal/admin.png)


## Beveiligingsvragen
Op dit tabblad kunt u de beveiligingsvragen opgeven die gebruikers juist moeten beantwoorden als de optie Beveiligingsvragen gebruiken als terugvaloptie is geselecteerd.  Azure Multi-Factor Authentication-server wordt geleverd met standaardvragen die u kunt gebruiken.  U kunt ook de volgorde wijzigen of uw eigen vragen toevoegen.  Als u uw eigen vragen toevoegt, kunt u ook de taal opgeven waarin u die vragen wilt stellen.

![Beveiligingsvragen van gebruikersportal](./media/multi-factor-authentication-get-started-portal/secquestion.png)


## Verstreken sessies

## SAML
Hier kunt u instellen dat de gebruikersportal claims accepteert van een identiteitsprovider die SAML gebruikt.  U kunt hier ook de time-out voor een SAML-sessie, het verificatiecertificaat en de omleidings-URL voor afmelden opgeven.

![SAML](./media/multi-factor-authentication-get-started-portal/saml.png)

## Goedgekeurde IP-adressen
Op dit tabblad kunt u een IP-adres of een IP-adresbereik opgeven dat kan worden toegevoegd zodat meervoudige verificatie wordt overgeslagen als een gebruiker zich aanmeldt vanaf een van deze IP-adressen. 

![Goedgekeurde IP-adressen gebruikersportal](./media/multi-factor-authentication-get-started-portal/trusted.png)

## Selfservice voor gebruikersregistratie
Als u wilt dat uw gebruikers zich kunnen aanmelden en registreren, selecteert u de opties Aanmelden van gebruikers toestaan en Registreren van gebruikers toestaan. Houd er rekening mee dat de instellingen die u selecteert, invloed hebben op de gebruikerservaring bij het aanmelden.

Wanneer bijvoorbeeld een gebruiker zich bij de gebruikersportal aanmeldt en op de knop Aanmelden klikt, wordt de gebruiker naar de pagina met gebruikersinstellingen van Azure Multi-Factor Authentication geleid.  Afhankelijk van hoe u Azure Multi-Factor Authentication hebt geconfigureerd, kan een gebruiker al dan niet de verificatiemethode selecteren.  

Als de gebruiker de verificatiemethode Spraakoproep selecteert of als de gebruiker vooraf is geconfigureerd om die methode te gebruiken, wordt de gebruiker gevraagd zijn of haar primaire telefoonnummer en, indien van toepassing, zijn of haar toestelnummer op te geven.  Mogelijk kan de gebruiker ook een alternatief telefoonnummer invoeren.  

![Goedgekeurde IP-adressen gebruikersportal](./media/multi-factor-authentication-get-started-portal/backupphone.png)

Als de gebruiker bij de verificatie een pincode moet gebruiken, wordt de gebruiker ook gevraagd een pincode op te geven.  Nadat de gebruiker zijn of haar telefoonnummer(s) en pincode (indien van toepassing) heeft ingevoerd, klikt de gebruiker op de knop Bel me nu om te verifiëren.  Azure Multi-Factor Authentication zal een verificatie door middel van een spraakoproep uitvoeren met behulp van het primaire telefoonnummer van de gebruiker.  De gebruiker moet de spraakoproep beantwoorden en zijn of haar pincode invoeren (indien van toepassing) en daarna op # drukken om door te gaan naar de volgende stap van het zelfregistratieproces.   

Als de gebruiker de verificatiemethode Sms-tekstbericht selecteert of vooraf is geconfigureerd om die methode te gebruiken, wordt de gebruiker gevraagd zijn of haar mobiel telefoonnummer op te geven.  Als de gebruiker bij de verificatie een pincode moet gebruiken, wordt de gebruiker ook gevraagd een pincode op te geven.  Nadat de gebruiker zijn of haar telefoonnummer en pincode (indien van toepassing) heeft ingevoerd, klikt de gebruiker op de knop Sms me nu om te verifiëren.  Azure Multi-Factor Authentication zal een sms-verificatie uitvoeren met behulp van de mobiele telefoon van de gebruiker.  De gebruiker moet het sms-bericht dat een eenmalige wachtwoordcode bevat ontvangen en het bericht beantwoorden met die eenmalige wachtwoordcode (plus de pincode, indien van toepassing) om door te gaan naar de volgende stap van het zelfregistratieproces. 

![Sms gebruikersportal](./media/multi-factor-authentication-get-started-portal/text.png)   

Als de gebruiker de verificatiemethode Mobiele app selecteert of vooraf is geconfigureerd om die methode te gebruiken, wordt de gebruiker op de pagina gevraagd om de Azure Multi-Factor Authentication-app op zijn of  haar apparaat te installeren en een activeringscode te genereren.  Na de installatie van de Azure Multi-Factor Authentication-app klikt de gebruiker op de knop Activeringscode genereren.    

>[AZURE.NOTE]Als de gebruiker de Azure Multi-Factor Authentication-app wilt gebruiken, moet de gebruiker pushmeldingen voor het apparaat inschakelen. 

Op de pagina worden vervolgens een activeringscode en een URL weergegeven, samen met een afbeelding van een streepjescode.  Als de gebruiker bij de verificatie een pincode moet gebruiken, wordt de gebruiker ook gevraagd een pincode op te geven.  De gebruiker voert de activeringscode en URL in de Azure Multi-Factor Authentication-app in of gebruikt de streepjescodescanner om de afbeelding van de streepjescode te scannen en klikt op de knop Activeren.    

Nadat de activering is voltooid, klikt de gebruiker op de knop Nu verifiëren.  Azure Multi-Factor Authentication zal een authenticatie uitvoeren met behulp van de mobiele app van de gebruiker.  De gebruiker moet in de mobiele app de pincode invoeren (indien van toepassing) en op de knop Verifiëren drukken om door te gaan naar de volgende stap van het zelfregistratieproces.  


Als de beheerders de Azure Multi-Factor Authentication-server hebben geconfigureerd om beveiligingsvragen en antwoorden te verzamelen, wordt de gebruiker hierna naar de pagina Beveiligingsvragen geleid.  De gebruiker moet vier beveiligingsvragen selecteren en deze juist beantwoorden.    

![Beveiligingsvragen van gebruikersportal](./media/multi-factor-authentication-get-started-portal/secq.png)  

De zelfregistratie van de gebruiker is nu voltooid en de gebruiker is aangemeld bij de gebruikersportal.  Gebruikers kunnen zich later op elk gewenst moment opnieuw aanmelden bij de gebruikersportal om hun telefoonnummers, pincodes, verificatiemethoden en beveiligingsvragen te wijzigen als dat door de beheerders is toegestaan.

 


<!--HONumber=Jun16_HO2-->


