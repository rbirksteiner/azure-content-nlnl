<properties 
    pageTitle="Extern bureaublad-gateway en Azure Multi-Factor Authentication-server met behulp van RADIUS" 
    description="Dit is de pagina Azure Multi-Factor Authentication die u helpt bij het implementeren van RD-gateway (Extern bureaublad) en Azure Multi-Factor Authentication-server met RADIUS." 
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

# Extern bureaublad-gateway en Azure Multi-Factor Authentication-server met behulp van RADIUS

In veel gevallen gebruikt Extern bureaublad-gateway het lokale NPS om gebruikers te verifiëren. In dit document wordt beschreven hoe u RADIUS-aanvragen van de Extern bureaublad-gateway (via de lokale NPS) omleidt naar de Multi-Factor Authentication-server.

De Multi-Factor Authentication-server moet op een afzonderlijke server worden geïnstalleerd. Deze stuurt de RADIUS-aanvraag vervolgens terug naar de NPS op de Extern bureaublad-gatewayserver. Wanneer NPS de gebruikersnaam en het wachtwoord heeft gevalideerd, wordt een antwoord teruggestuurd naar de Multi-Factor Authentication-server, die de tweede verificatiestap uitvoert alvorens een resultaat te retourneren aan de gateway.





## De RD-gateway configureren

De RD-gateway moet worden geconfigureerd om RADIUS-verificatie naar een Azure Multi-Factor Authentication-server te verzenden. Zodra RD-gateway is geïnstalleerd, is geconfigureerd en werkt, gaat u naar de eigenschappen van de RD-gateway. Ga naar het tabblad Archief van Extern bureaublad en verander dit in het gebruik van een centrale server waarop NPS wordt uitgevoerd, in plaats van een lokale server waarop NPS wordt uitgevoerd. Voeg een of meer Azure Multi-Factor Authentication-servers toe als RADIUS-servers en geef voor elke server een gedeeld geheim op.





## NPS configureren

De RD-gateway gebruikt NPS om de RADIUS-aanvraag te verzenden naar Azure Multi-Factor Authentication. Er moet een time-out worden gewijzigd om te voorkomen dat er een time-out op de RD-gateway optreedt voordat Multi-Factor Authentication is voltooid. Gebruik de volgende procedure om NPS te configureren.

1. Vouw in NPS het menu RADIUS-clients en -server in de linkerkolom uit en klik op Externe RADIUS-servergroepen. Ga naar de eigenschappen van de groep TS-gatewayserver. Bewerk de weergegeven RADIUS-server(s) en ga naar het tabblad Taakverdeling. Wijzig het Aantal seconden zonder reactie voordat een aanvraag als een genegeerde aanvraag wordt beschouwd en het Aantal seconden tussen aanvragen wanneer de server is geïdentificeerd als Niet beschikbaar in 30 tot 60 seconden. Klik op het tabblad Verificatie/Account en controleer of de opgegeven RADIUS-poorten overeenkomen met de poorten waarnaar de Multi-Factor Authentication-server luistert.
2. NPS moet ook worden geconfigureerd om teruggestuurde RADIUS-verificaties van de Azure Multi-Factor Authentication-server te ontvangen. Klik in het linkermenu op RADIUS-clients. Voeg de Azure Multi-Factor Authentication-server toe als een RADIUS-client. Kies een beschrijvende naam en geef een gedeeld geheim op.
3. Vouw de sectie Beleid in het linkernavigatievenster uit en klik op Beleid voor verbindingsaanvragen. Hier moet een beleid voor verbindingsaanvragen met de naam TS-GATEWAY-AUTORISATIEBELEID worden vermeld dat tijdens de configuratie van RD-gateway is gemaakt. Dit beleid stuurt RADIUS-verzoeken door naar de Multi-Factor Authentication-server.
4. Kopieer dit beleid naar een nieuw beleid. Voeg in het nieuwe beleid een voorwaarde toe die de Beschrijvende naam van client koppelt aan de Beschrijvende naam die in stap 2 hierboven is ingesteld voor de RADIUS-client voor de Azure Multi-Factor Authentication-server. Wijzig de verificatieprovider in Lokale computer. Wanneer een RADIUS-aanvraag wordt ontvangen van de Azure Multi-Factor Authentication-server, zorgt dit beleid ervoor dat de verificatie lokaal wordt uitgevoerd in plaats van dat er een RADIUS-aanvraag wordt teruggestuurd naar de Azure Multi-Factor Authentication-server, waardoor een oneindige lus zou ontstaan. Om deze lussituatie te voorkomen, moet u dit nieuwe beleid BOVEN het oorspronkelijke beleid plaatsen dat de aanvraagt doorstuurt naar de Multi-Factor Authentication-server.

## Azure Multi-Factor Authentication configureren


--------------------------------------------------------------------------------



De Azure Multi-Factor Authentication-server is geconfigureerd als een RADIUS-proxy tussen RD-gateway en NPS.  Het moet worden geïnstalleerd op een in het domein opgenomen server die is gescheiden van de RD-gatewayserver. Gebruik de volgende procedure om de Azure Multi-Factor Authentication-server te configureren.

1. Open de Azure Multi-Factor Authentication-server en klik op het pictogram RADIUS-verificatie. Schakel het selectievakje RADIUS-verificatie inschakelen in.
2. Controleer op het tabblad Clients of de poorten overeenkomen met wat in NPS is geconfigureerd en klik op de knop Toevoegen . Voeg het IP-adres van de RD-gatewayserver, de naam van de toepassing (optioneel) en een gedeeld geheim toe. Het gedeeld geheim moet op de Azure Multi-Factor Authentication-server en de RD-gateway hetzelfde zijn.
3. Klik op het tabblad Doel en kies het keuzerondje RADIUS-server(s).
4. Klik op de knop  Toevoegen. Voer het IP-adres, het gedeeld geheim en de poorten van de NPS-server in. De RADIUS-client en het RADIUS-doel zijn hetzelfde, tenzij u een centrale NPS gebruikt. Het gedeeld geheim moet overeenkomen met het gedeeld geheim dat is ingesteld in de sectie RADIUS-client van de NPS-server. 

![RADIUS-verificatie](./media/multi-factor-authentication-get-started-server-rdg/radius.png)



<!--HONumber=Jun16_HO2-->


