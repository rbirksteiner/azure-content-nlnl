<properties 
    pageTitle="RADIUS-verificatie en Azure Multi-Factor Authentication-server" 
    description="Dit is de pagina Azure Multi-Factor Authentication die u helpt bij het implementeren van RADIUS-verificatie en de Azure Multi-Factor Authentication-server." 
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



# RADIUS-verificatie en Azure Multi-Factor Authentication-server

Via de RADIUS-verificatiesectie kunt u RADIUS-verificatie inschakelen en configureren voor de Azure Multi-Factor Authentication-server. RADIUS is een standaardprotocol om verificatieaanvragen te accepteren en te verwerken. De Multi-Factor Authentication-server fungeert als een RADIUS-server. Deze wordt geplaatst tussen uw RADIUS-client, bijvoorbeeld een VPN-apparaat, en het verificatiedoel, bijvoorbeeld Active Directory (AD), een LDAP-adreslijstdirectory of een andere RADIUS-server, om Azure Multi-Factor Authentication toe te voegen. Azure Multi-Factor Authentication werkt alleen als u de Azure Multi-Factor Authentication-server zo configureert dat deze kan communiceren met zowel de clientservers als het verificatiedoel. De Azure Multi-Factor Authentication-server accepteert aanvragen van een RADIUS-client, controleert referenties met behulp van het verificatiedoel, voegt Azure Multi-Factor Authentication toe en stuurt een antwoord terug naar de RADIUS-client. De hele verificatie slaagt alleen als zowel de primaire verificatie als de Azure Multi-Factor Authentication slaagt.

>[AZURE.NOTE]
>De MFA-server ondersteunt alleen PAP (Password Authentication Protocol) en MSCHAPv2 (Microsoft Challenge-Handshake Authentication Protocol) RADIUS-protocollen wanneer deze als RADIUS-server fungeert.  Andere protocollen zoals EAP (Extensible Authentication Protocol) kunnen worden gebruikt wanneer de MFA-server fungeert als een RADIUS-proxy naar een andere RADIUS-server die ondersteuning biedt voor dat protocol, zoals Microsoft NPS.
></br>
>Wanneer in deze configuratie andere protocollen worden gebruikt, werken eenzijdige SMS- en OATH-tokens niet omdat de MFA-server geen geslaagd RADIUS Challenge-antwoord met dat protocol kan starten.


![RADIUS-verificatie](./media/multi-factor-authentication-get-started-server-rdg/radius.png)

## Configuratie van RADIUS-verificatie

Installeer de Azure Multi-Factor Authentication-server op een Windows-server als u RADIUS-verificatie wilt configureren. Als u een Active Directory-omgeving hebt, moet de server zijn toegevoegd aan het domein in het netwerk. Gebruik de volgende procedure om de Azure Multi-Factor Authentication-server te configureren: 

1. Klik in de Azure Multi-Factor Authentication-server in het menu links op het pictogram RADIUS-authenticatie.
2. Schakel het selectievakje RADIUS-verificatie inschakelen in.
3. Ga naar het tabblad Clients en verander de verificatie- en accountingpoort(en) als de RADIUS-service van Azure Multi-Factor Authentication verbinding moet maken met niet-standaardpoorten om te luisteren naar RADIUS-aanvragen van de te configureren clients.
4. Klik op de knop Toevoegen… .
5. Voer in het dialoogvenster RADIUS-client toevoegen het IP-adres in van het toestel of de server die moet worden geverifieerd bij de Azure Multi-Factor Authentication-server, en voeg (optioneel) een toepassingsnaam toe en een gedeeld geheim toe. Het gedeeld geheim moet op de Azure Multi-Factor Authentication-server en het toestel/de server hetzelfde zijn. De naam van de toepassing wordt vermeld in Azure Multi-Factor Authentication-rapporten en kan worden weergegeven in verificatieberichten via sms of mobiele apps.
6. Schakel het selectievakje Overeenkomende Multi-Factor Authentication-gebruiker vereisen in als alle gebruikers zijn of moeten worden geïmporteerd in de server en aan Multi-Factor Authentication moeten worden onderworpen. Als een groot aantal gebruikers nog niet is geïmporteerd in de server en/of vrijgesteld zal zijn van Multi-Factor Authentication, laat u het vakje uitgeschakeld. Zie het Help-bestand voor meer informatie over deze functie.
7. Schakel het selectievakje Alternatief OATH-token inschakelen in als gebruikers de Azure Multi-Factor Authentication-verificatie voor mobiele apps gaan gebruiken en u OATH-toegangscodes wil gebruiken als een alternatieve verificatie voor telefoongesprekken, SMS of pushmeldingen buiten de band.
8. Klik op de knop OK.
9. U moet stap 4 tot en met 8 mogelijk herhalen om extra RADIUS-clients toe te voegen.
10. Klik op het tabblad Doel.
11. Selecteer Windows-domein als de Azure Multi-Factor Authentication-server is geïnstalleerd op een server die is gekoppeld aan een domein in een Active Directory-omgeving.
12. Selecteer LDAP-binding als gebruikers moeten worden geverifieerd aan de hand van een LDAP-adreslijst. Wanneer u LDAP-binding gebruikt, moet u op het pictogram Adreslijstintegratie klikken en de LDAP-configuratie op het tabblad Instellingen zo bewerken dat de server verbinding kan maken met uw adreslijst. In de handleiding voor LDAP-proxyconfiguratie vindt u instructies voor het configureren van LDAP. 
13. Selecteer RADIUS-server(s) als gebruikers moeten worden geverifieerd aan de hand van een andere RADIUS-server.
14. Configureer de server waarnaar de server RADIUS-verzoeken stuurt door op de knop Toevoegen... te klikken.
15. Voer het IP-adres van de RADIUS-server en een gedeeld geheim in het dialoogvenster RADIUS-server toevoegen in. Het gedeelde geheim moet op de Azure Multi-Factor Authentication-server en de RADIUS-server hetzelfde zijn. Wijzig de verificatiepoort en accountingpoort als door de RADIUS-server andere poorten worden gebruikt.
16. Klik op de knop OK. 
17. U moet de Azure Multi-Factor Authentication-server als een RADIUS-client op de andere RADIUS-server toevoegen opdat toegangsverzoeken afkomstig van de Azure Multi-Factor Authentication-Server worden verwerkt. U moet hetzelfde gedeelde geheim gebruiken dat ook op de Azure Multi-Factor Authentication-server is geconfigureerd.
18. Misschien moet u deze stap herhalen als u meer RADIUS-servers wilt toevoegen. Met de knoppen Omhoog en Omlaag kunt u de volgorde configureren waarin de server ze aanroept. Hiermee hebt u de configuratie van de Azure Multi-Factor Authentication-server voltooid. De server luistert nu naar de geconfigureerde poorten voor RADIUS-toegangsverzoeken van de geconfigureerde clients.   


## RADIUS-clientconfiguratie

Als u de RADIUS-client wilt configureren, gebruikt u de volgende richtlijnen:

- Configureer uw toestel/server voor verificatie via RADIUS bij het IP-adres van de Azure Multi-Factor Authentication-server, die als de RADIUS-server fungeert. 
- Gebruik hetzelfde gedeelde geheim dat hierboven is geconfigureerd. 
- Stel de time-out voor RADIUS in op 30 tot 60 seconden, zodat er voldoende tijd is om de referenties van de gebruiker te valideren, de Multi-Factor Authentication uit te voeren, de reactie daarop te ontvangen en vervolgens te reageren op de RADIUS-toegangsaanvraag.




<!--HONumber=Jun16_HO2-->


