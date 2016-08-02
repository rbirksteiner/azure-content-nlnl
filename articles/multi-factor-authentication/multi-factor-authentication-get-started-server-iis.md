<properties 
    pageTitle="IIS-verificatie en Azure Multi-Factor Authentication-server" 
    description="Dit is de pagina Azure Multi-Factor Authentication die u helpt bij het implementeren van IIS-verificatie en de Azure Multi-Factor Authentication-server." 
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
    ms.date="05/12/2016" 
    ms.author="billmath"/>

# IIS-verificatie

Via de IIS-verificatiesectie van de Azure Multi-Factor Authentication-server kunt u IIS-verificatie inschakelen en configureren voor integratie met Microsoft IIS-webtoepassingen. Met de Azure Multi-Factor Authentication-server wordt een invoegtoepassing geïnstalleerd voor het filteren van aanvragen die worden ingediend bij de ISS-webserver om Azure Multi-Factor Authentication te kunnen toevoegen. De IIS-invoegtoepassing biedt ondersteuning voor verificatie op basis van formulier en geïntegreerde Windows-verificatie voor HTTP. Goedgekeurde IP-adressen kunnen ook zo worden geconfigureerd dat interne IP-adressen worden vrijgesteld van verificatie met twee factoren. 


![IIS-verificatie](./media/multi-factor-authentication-get-started-server-iis/iis.png)


## IIS-verificatie samen gebruiken met Azure Multi-Factor Authentication-server

Als u een IIS-webtoepassing wilt beveiligen die gebruikmaakt van verificatie op basis van een formulier, moet u de Azure Multi-Factor Authentication-server op de IIS-webserver installeren en de server volgens de onderstaande procedure configureren.

1. Klik in de Azure Multi-Factor Authentication-server in het menu links op het pictogram IIS-verificatie.
2. Klik op het tabblad Op formulier gebaseerd.
3. Klik op de knop Toevoegen...
4. Als u de gebruikersnaam, het wachtwoord en de domeinvariabelen automatisch wilt detecteren, voert u de aanmeldings-URL (bijvoorbeeld https://localhost/contoso/auth/login.aspx) in het dialoogvenster Formulier-gebaseerde website automatisch configureren in en klikt u op OK.
5. Schakel het selectievakje Overeenkomende Multi-Factor Authentication-gebruiker vereisen in als alle gebruikers zijn of moeten worden geïmporteerd in de server en aan Multi-Factor Authentication onderworpen zijn. Als een groot aantal gebruikers nog niet is geïmporteerd in de server en/of vrijgesteld zal zijn van Multi-Factor Authentication, laat u het vakje uitgeschakeld.
6. Als de paginavariabelen niet automatisch kunnen worden gedetecteerd, klikt u op de knop Handmatig opgeven... in het dialoogvenster Formulier-gebaseerde website automatisch configureren.
7. Voer in het dialoogvenster Formulier-gebaseerde website toevoegen in het veld Indienings-URL de URL naar de aanmeldingspagina in en voer een toepassingsnaam in (optioneel). De naam van de toepassing wordt vermeld in Azure Multi-Factor Authentication-rapporten en kan worden weergegeven in verificatieberichten via sms of mobiele apps. Zie het Help-bestand voor meer informatie over de indienings-URL. 
8. Selecteer de juiste aanvraagindeling. Deze is voor de meeste webtoepassingen ingesteld op 'POST of GET'.
9. Voer de gebruikersnaamvariabele, de wachtwoordvariabele en de domeinvariabele in (als deze op de aanmeldingspagina wordt weergegeven). Mogelijk moet u navigeren naar de aanmeldingspagina in een webbrowser, met de rechtermuisknop op de pagina klikken en 'Bron weergeven' selecteren om de namen van de invoervakken op de pagina te vinden.
10. Schakel het selectievakje Overeenkomende Azure Multi-Factor Authentication-gebruiker vereisen in als alle gebruikers zijn of moeten worden geïmporteerd in de Azure Multi-Factor Authentication-server en aan Multi-Factor Authentication onderworpen zijn. Als een groot aantal gebruikers nog niet is geïmporteerd in de server en/of vrijgesteld zal zijn van Multi-Factor Authentication, laat u het vakje uitgeschakeld. Zie het Help-bestand voor meer informatie over deze functie.
11.  Klik op de knop Geavanceerd... om geavanceerde instellingen te controleren, waaronder de mogelijkheid om een aangepast bestand voor de weigeringspagina te selecteren, om geslaagde verificaties voor de website gedurende een bepaalde periode in een cache op te slaan met behulp van cookies en om te selecteren of de primaire referenties moeten worden geverifieerd aan de hand van het Windows-domein, een LDAP-adreslijst of een RADIUS-server. Als u klaar bent, klikt u op de knop OK om terug te keren naar het dialoogvenster Formulier-gebaseerde website. Zie het Help-bestand voor meer informatie over de geavanceerde instellingen.
12. Klik op de knop OK.
13. Zodra de URL- en paginavariabelen zijn gedetecteerd of ingevoerd, worden de websitegegevens weergegeven in het paneel Op formulier gebaseerd.
14. Zie de sectie IIS-invoegtoepassingen inschakelen voor Azure Multi-Factor Authentication-server meteen hieronder om de configuratie van de IIS-verificatie te voltooien. 

## Geïntegreerde Windows-verificatie met Azure Multi-Factor Authentication-server gebruiken

Als u een IIS-webtoepassing wilt beveiligen die gebruikmaakt van geïntegreerde Windows-verificatie voor HTTP, moet u de Azure Multi-Factor Authentication-server op de IIS-webserver installeren en de server volgens de onderstaande procedure configureren. 

1. Klik in de Azure Multi-Factor Authentication-server in het menu links op het pictogram IIS-verificatie.
2. Klik op het tabblad HTTP. Klik op het tabblad Op formulier gebaseerd.
3. Klik op de knop Toevoegen...
4. Voer in het dialoogvenster Basis-URL toevoegen in het veld Basis-URL de URL in voor de website waar de HTTP-authenticatie wordt uitgevoerd (bijv. http://localhost/owa) en voer een Toepassingsnaam in (optioneel). De naam van de toepassing wordt vermeld in Azure Multi-Factor Authentication-rapporten en kan worden weergegeven in verificatieberichten via sms of mobiele apps.
5. Pas de tijd voor time-out voor inactiviteit en voor Maximale sessie aan als de standaardwaarde niet voldoende is.
6. Schakel het selectievakje Overeenkomende Multi-Factor Authentication-gebruiker vereisen in als alle gebruikers zijn of moeten worden geïmporteerd in de server en aan Multi-Factor Authentication onderworpen zijn. Als een groot aantal gebruikers nog niet is geïmporteerd in de server en/of vrijgesteld zal zijn van Multi-Factor Authentication, laat u het vakje uitgeschakeld. Zie het Help-bestand voor meer informatie over deze functie. 
7. Schakel, indien gewenst, het selectievakje Cookie gebruiken om voltooide authenticaties in de cache op te slaan in.
8. Klik op de knop OK.
9. Zie de sectie [IIS-invoegtoepassingen inschakelen voor Azure Multi-Factor Authentication-server](#enable-iis-plug-ins-for-azure-multi-factor-authentication-server) meteen hieronder om de configuratie van de IIS-verificatie te voltooien. 


## IIS-invoegtoepassingen inschakelen voor Azure Multi-Factor Authentication-server

Zodra u de URL's en instellingen voor op formulier gebaseerde of HTTP-verificaties hebt ingesteld, moet u de locaties selecteren waar de IIS-invoegtoepassingen van Azure Multi-Factor Authentication moeten worden geladen en ingeschakeld in IIS. Gebruik de volgende procedure:

1. Indien uitgevoerd op IIS 6, klikt u op het tabblad ISAPI- en selecteert u de website waaronder die webtoepassing wordt uitgevoerd (bijvoorbeeld de standaardwebsite) om de invoegtoepassing met de ISAPI-filter van Azure Multi-Factor Authentication voor die site in te schakelen.
2. Indien uitgevoerd op IIS 7 of hoger, klikt u op het tabblad Systeemeigen Module en selecteert u de server, de website(s) of de toepassing(en) om de IIS-invoegtoepassing op het gewenste niveau/de gewenste niveaus in te schakelen.
3. Klik op het vak IIS-verificatie inschakelen aan de bovenkant van het scherm. Azure Multi-Factor Authentication is nu de beveiliging van de geselecteerde IIS-toepassing. Zorg ervoor dat gebruikers in de server zijn geïmporteerd. Zie de onderstaande sectie Goedgekeurde IP-adressen als u interne IP-adressen op een lijst met toegestane IP-adressen wilt plaatsen, zodat verificatie met twee factoren niet vereist is bij het aanmelden bij de website vanaf die locaties. 


## Goedgekeurde IP-adressen

De goedgekeurde IP-adressen bieden gebruikers de mogelijkheid om Azure Multi-Factor Authentication over te slaan voor websiteverzoeken die afkomstig zijn van bepaalde IP-adressen of subnetten. Zo kunt u bijvoorbeeld gebruikers vrijstellen van Azure Multi-Factor Authentication wanneer zij zich op kantoor aanmelden. Hiervoor geeft u dan het subnet van het kantoor op als een van de goedgekeurde IP-adressen. Gebruik de volgende procedure als u goedgekeurde IP-adressen wilt configureren:

1. Klik op het tabblad Goedgekeurde IP-adressen in de sectie IIS-verificatie. 
2. Klik op de knop Toevoegen...
3. Wanneer het dialoogvenster Goedgekeurd IP-adres toevoegen wordt weergegeven, selecteert u het keuzerondje Eén IP-adres, IP-bereik of Subnet.
4. Voer het IP-adres, het bereik van IP-adressen of het subnet in dat u op de lijst van goedgekeurde IP-adressen wilt plaatsen. Als u een subnet wilt invoeren, selecteert u het geschikte Netmasker en klikt u op de knop OK. De lijst van goedgekeurde IP-adressen is toegevoegd.



<!--HONumber=Jun16_HO2-->


