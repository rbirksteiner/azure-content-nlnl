<properties 
    pageTitle="LDAP-verificatie en Azure Multi-Factor Authentication-server" 
    description="Dit is de pagina Azure Multi-Factor Authentication die u helpt bij het implementeren van LDAP-verificatie en de Azure Multi-Factor Authentication-server." 
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

# LDAP-verificatie en Azure Multi-Factor Authentication-server 


Standaard is de Azure Multi-Factor Authentication-server geconfigureerd om de gebruikers van Active Directory te importeren of te synchroniseren. Deze kan echter worden geconfigureerd om verbinding te maken met verschillende LDAP-adreslijsten, zoals een ADAM-adreslijst of een specifieke Active Directory-domeincontroller. Wanneer de configuratie zodanig is dat er via LDAP verbinding wordt gemaakt met een adreslijst, kan de Azure Multi-Factor Authentication-server worden geconfigureerd om te fungeren als een LDAP-proxy om verificaties uit te voeren. Daarnaast kunt u de LDAP-binding gebruiken als een RADIUS-doel voor het uitvoeren van pre-verificaties van gebruikers wanneer IIS-verificatie wordt gebruikt, of voor het uitvoeren van primaire verificaties in de Azure Multi-Factor Authentication-gebruikersportal.

Wanneer u Azure Multi-Factor Authentication als een LDAP-proxy gebruikt, wordt de Azure Multi-Factor Authentication-server ingevoegd tussen de LDAP-client (bijvoorbeeld VPN-apparaat, toepassing) en de LDAP-adreslijstserver om Multi-Factor Authentication toe te kunnen voegen. Azure Multi-Factor Authentication werkt alleen als de Azure Multi-Factor Authentication-server is geconfigureerd om te kunnen communiceren met zowel de clientservers als de LDAP-adreslijst. In deze configuratie accepteert de Azure Multi-Factor Authentication-server LDAP-aanvragen van clientservers en toepassingen, en stuurt deze door naar de doel-LDAP-adreslijstserver om de primaire referenties te valideren. Als uit het antwoord van de LDAP-adreslijst blijkt dat de primaire referenties geldig zijn, voert Azure Multi-Factor Authentication een verificatie met twee factoren uit, en stuurt deze een antwoord terug naar de LDAP-client. De volledige verificatie slaagt alleen als zowel de verificatie met de LDAP-server als de Multi-Factor Authentication kunnen worden uitgevoerd. 





## Configuratie LDAP-verificatie


Als u LDAP-verificatie wilt configureren, moet u de Azure Multi-Factor Authentication-server op een Windows-server installeren. Gebruik de volgende procedure: 

1. Klik in de Azure Multi-Factor Authentication-server in het menu links op het pictogram LDAP-verificatie.
2. Schakel het selectievakje LDAP-verificatie in.![LDAP-verificatie](./media/multi-factor-authentication-get-started-server-ldap/ldap2.png) 
3. Wijzig op het tabblad Clients de TCP-poort en de SSL-poort als de LDAP-service van Azure Multi-Factor Authentication verbinding moet maken met niet-standaardpoorten om te luisteren naar LDAP-aanvragen van clients die geconfigureerd gaan worden.
4. Als u van plan bent LDAPS te gebruiken van de client naar de Azure Multi-Factor Authentication-server, moet er een SSL-certificaat worden geïnstalleerd op de server waarop de Azure Multi-Factor Authentication-server wordt uitgevoerd. Klik op de knop Bladeren... naast het vak SSL-certificaat en selecteer het geïnstalleerde certificaat dat voor de beveiligde verbinding wordt gebruikt. 
5. Klik op de knop Toevoegen...
6. Voer in het dialoogvenster LDAP-client het IP-adres in van het toestel, de server of de toepassing die moet worden geverifieerd bij de Azure Multi-Factor Authentication-server, en voeg (optioneel) een toepassingsnaam toe. De naam van de toepassing wordt vermeld in Azure Multi-Factor Authentication-rapporten en kan worden weergegeven in verificatieberichten via sms of mobiele apps.
7. Schakel het selectievakje Overeenkomende Azure Multi-Factor Authentication-gebruiker vereisen in als alle gebruikers zijn of moeten worden geïmporteerd in de server en aan Multi-Factor Authentication onderworpen zijn. Als een groot aantal gebruikers nog niet is geïmporteerd in de Azure Multi-Factor Authentication-server en/of vrijgesteld zal zijn van Multi-Factor Authentication, laat u het vakje uitgeschakeld. Zie het Help-bestand voor meer informatie over deze functie. 
8. U moet stap 5 tot en met 7 mogelijk herhalen om extra LDAP-clients toe te voegen.
9. Wanneer Azure Multi-Factor Authentication is geconfigureerd om LDAP-verificaties te kunnen ontvangen, moet deze als proxy optreden voor verificaties bij de LDAP-adreslijst. Dat is de reden waarom op het tabblad Doel een enkele niet beschikbare optie voor het gebruik van een LDAP-doel wordt weergegeven. Als u de verbinding voor de LDAP-adreslijst wilt configureren, klikt u op het pictogram Adreslijstintegratie. 
10. Schakel op het tabblad Instellingen het keuzerondje Specifieke LDAP-configuratie gebruiken in.
11. Klik op de knop Bewerken....
12. Vul in de velden van het dialoogvenster LDAP-configuratie bewerken de benodigde informatie in om verbinding te maken met de LDAP-adreslijst. Beschrijvingen van de velden zijn opgenomen in de onderstaande tabel. Opmerking: deze informatie is ook beschikbaar in het Help-bestand voor de Azure Multi-Factor Authentication-server.![Adreslijstintegratie](./media/multi-factor-authentication-get-started-server-ldap/ldap.png) 
13. Test de LDAP-verbinding door te klikken op de knop Testen.
14. Als de LDAP-verbindingstest is geslaagd, klikt u op de knop OK. 
15. Klik op het tabblad Filters. De Azure Multi-Factor Authentication-server is vooraf geconfigureerd om containers, beveiligingsgroepen en gebruikers van Active Directory te laden. Als u verbinding maakt met een andere LDAP-adreslijst, moet u waarschijnlijk de weergegeven filters bewerken. Klik op de koppeling Help voor meer informatie over filters.
16. Klik op tabblad Kenmerken. De Azure Multi-Factor Authentication-server is vooraf geconfigureerd om kenmerken van Active Directory toe te wijzen.
17. Als u verbinding maakt met een andere LDAP-adreslijst of als u de vooraf geconfigureerde kenmerktoewijzingen wilt wijzigen, klikt u op de knop Bewerken....
18. Wijzig in het dialoogvenster Kenmerken bewerken de toewijzingen van de LDAP-kenmerktoewijzingen voor uw adreslijst. Kenmerknamen kunnen worden getypt of geselecteerd door te klikken op de ... -knop naast elk veld.
19. Klik op de koppeling Help voor meer informatie over kenmerken.
20. Klik op de knop OK.
21. Klik op het pictogram Bedrijfsinstellingen en selecteer het tabblad Gebruikersnaamomzetting.
22.Als u een verbinding maakt met Active Directory vanaf een server die is gekoppeld aan een domein, kunt u mogelijk het keuzerondje Windows-beveiligings-id's (SID's) gebruiken voor overeenkomende gebruikersnamen ingeschakeld laten. Schakel anders het keuzerondje Het kenmerk Unieke LDAP-id gebruiken voor overeenkomende gebruikersnamen in. Als dit is ingeschakeld, zet de Azure Multi-Factor Authentication-server elke gebruikersnaam om in een unieke id in de LDAP-adreslijst. Een LDAP-zoekopdracht wordt uitgevoerd op de gebruikersnaamkenmerken die zijn gedefinieerd op het tabblad Adreslijstintegratie > Kenmerken. Wanneer een gebruiker wordt geverifieerd, wordt de gebruikersnaam omgezet in de unieke id in de LDAP-adreslijst en wordt de unieke id gebruikt om de overeenkomende gebruiker erbij te zoeken in het gegevensbestand van Azure Multi-Factor Authentication. Hiermee kunt u vergelijkingen maken in niet-hoofdlettergevoelige indelingen, maar ook in indelingen met lange en korte gebruikersnamen. Hiermee is de configuratie van de Azure Multi-Factor Authentication-server voltooid. De Azure Multi-Factor Authentication-server luistert nu op de geconfigureerde poorten naar toegangsaanvragen van LDAP van de geconfigureerde clients, en stelt deze aanvragen voor de LDAP-adreslijst voor verificatie in op proxy.


## LDAP-clientconfiguratie

Als u de LDAP-client wilt configureren, gebruikt u de volgende richtlijnen:

- Configureer uw apparaat, server of toepassing als u wilt dat deze via LDAP bij de Azure Multi-Factor Authentication-server wordt geverifieerd alsof het uw LDAP was. U moet dezelfde instellingen gebruiken die u normaal gesproken gebruikt om rechtstreeks verbinding te maken met de LDAP-adreslijst, met uitzondering van de servernaam of het IP-adres omdat deze toebehoren aan de Azure Multi-Factor Authentication-server. 
- Stel de time-out voor LDAP in op 30 tot 60 seconden zodat er voldoende tijd is om de referenties van de gebruiker met de LDAP-adreslijst te valideren, de verificatie met twee factoren uit te voeren , de reacties daarop te ontvangen en vervolgens te reageren op de LDAP-toegangsaanvraag. 
- Als LDAPS wordt gebruikt, moet het apparaat of de server die de LDAP-query's maakt het SSL-certificaat kunnen vertrouwen dat op de Azure Multi-Factor Authentication-server is geïnstalleerd.




<!--HONumber=Jun16_HO2-->


