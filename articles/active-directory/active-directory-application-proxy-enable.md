<properties
    pageTitle="Azure AD-toepassingsproxy inschakelen | Microsoft Azure"
    description="Schakel de toepassingsproxy in de klassieke Azure-portal in en installeer de connectors voor de omgekeerde proxy."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="StevenPo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="06/01/2016"
    ms.author="kgremban"/>

# Toepassingsproxy inschakelen in de Azure-portal

Dit artikel beschrijft de stappen die nodig zijn om Microsoft Azure AD-toepassingsproxy in te schakelen voor uw clouddirectory in Azure AD. Dit omvat het installeren van de connector voor toepassingsproxy in uw particuliere netwerk, waarmee de verbinding wordt gelegd tussen uw netwerk en de proxyservice. Ook registreert u de connector bij uw Microsoft Azure AD-tenantabonnement. Als u niet bekend bent met de mogelijkheden van toepassingsproxy, gaat u voor meer informatie naar [How to provide secure remote access to on-premises applications](active-directory-application-proxy-get-started.md) (Engelstalig).

Nadat u de stappen voor het inschakelen van Azure AD-toepassingsproxy hebt uitgevoerd, kunt u uw on-premises apps publiceren voor externe toegang.

> [AZURE.NOTE] De functie toepassingsproxy is alleen beschikbaar als u een upgrade hebt uitgevoerd naar de Premium- of Basic-editie van Azure Active Directory. Zie [Azure Active Directory editions](active-directory-editions.md) (Engelstalig) voor meer informatie.

## Vereisten voor toepassingsproxy
Voordat u de services voor toepassingsproxy kunt inschakelen en gebruiken, moet u over het volgende beschikken:

- Een [basis- of premiumabonnement op Microsoft Azure AD](active-directory-editions.md) en een Azure AD-directory waarvan u een globale beheerder bent.
- Een server met Windows Server 2012 R2 of Windows 8.1 of hoger, waarop u de connector voor toepassingsproxy kunt installeren. Via de server worden HTTPS-aanvragen naar de services voor toepassingsproxy in de cloud verzonden en er is een HTTPS-verbinding nodig voor de toepassingen die u wilt publiceren.
- Als er een firewall is geïnstalleerd op het pad, zorgt u ervoor dat deze open staat zodat de connector HTTPS-aanvragen (TCP) kan versturen naar de toepassingsproxy. De connector gebruikt deze poorten samen met subdomeinen die deel uitmaken van de highleveldomeinen: *msappproxy.net* en *servicebus.windows.net*. Zorg ervoor dat **alle** hierna genoemde poorten zijn geopend voor **uitgaand** verkeer:

  	| Poortnummer | Beschrijving |
  	| --- | --- |
  	| 80 | Uitgaand HTTP-verkeer voor beveiligingsvalidatie inschakelen. |
  	| 443 | Gebruikersverificatie met Azure AD inschakelen (alleen vereist voor het registratieproces voor de connector) |
  	| 10100 - 10120 | LOB HTTP-responsen terugsturen naar de proxy inschakelen |
  	| 9352, 5671 | Communicatie tussen de connector en de Azure-service voor binnenkomende aanvragen inschakelen. |
  	| 9350 | Optioneel, voor betere prestaties van binnenkomende aanvragen |
  	| 8080 | De connectoropstartreeks en automatische updates voor de connector inschakelen |
  	| 9090 | Connectorregistratie inschakelen (alleen vereist voor het registratieproces voor de connector) |
  	| 9091 | Automatisch verlengen van het vertrouwenscertificaat van de connector inschakelen |

Als met uw firewall verkeer wordt afgedwongen op basis van de herkomst van gebruiker, opent u deze poorten voor verkeer dat afkomstig is van Windows-services die als netwerkservice worden uitgevoerd. Zorg er ook voor dat poort 8080 is ingeschakeld voor NT Authority\System.


## Stap 1: toepassingsproxy inschakelen in Azure AD
1. Meld u als beheerder aan in de [klassieke Azure-portal](https://manage.windowsazure.com/).
2. Ga naar Active Directory en selecteer de directory waarin u toepassingsproxy wilt inschakelen.

    ![Active Directory - pictogram](./media/active-directory-application-proxy-enable/ad_icon.png)

3. Selecteer **Configureren** op de directorypagina en blader omlaag naar **Toepassingsproxy**.
4. Stel **Services voor toepassingsproxy inschakelen voor deze directory** in op **Ingeschakeld**.

    ![Toepassingsproxy inschakelen](./media/active-directory-application-proxy-enable/app_proxy_enable.png)

5. Selecteer **Nu downloaden**. Hiermee gaat u naar de **download voor de connector voor Azure AD-toepassingsproxy**. Lees en accepteer de licentievoorwaarden en klik op **Downloaden** om het Windows Installer-bestand (.exe) van de connector voor toepassingsproxy op te slaan.

## Stap 2: de connector installeren en registreren
1. Voer *AADApplicationProxyConnectorInstaller.exe* uit op de server die u hebt voorbereid volgens de hierboven vermelde vereisten.
2. Volg de instructies in de wizard om de toepassing te installeren.
3. Tijdens de installatie wordt u gevraagd de connector te registreren bij de toepassingsproxy van uw Azure AD-tenant.

  - Geef de globale-beheerdersreferentie voor Azure AD op. De referenties van uw globale beheerderstenant kunnen afwijken van uw Microsoft Azure-referenties.
  - Zorg ervoor dat de beheerder die de connector registreert zich in dezelfde directory bevindt als die waarin u de service voor toepassingsproxy hebt ingeschakeld. Als het tenantdomein bijvoorbeeld contoso.com is, moet de beheerder admin@contoso.com zijn, of een ander alias in dat domein.
  - Als **Verbeterde beveiliging van Internet Explorer** is ingesteld op **Aan** op de server waarop u de Azure AD-connector installeert, is het registratiescherm mogelijk geblokkeerd. Als dit het geval is, volgt u de instructies in de foutmelding om de toegang toe te staan. Zorg ervoor dat de verbeterde beveiliging van Internet Explorer is uitgeschakeld.
  - Zie [Troubleshoot Application Proxy](active-directory-application-proxy-troubleshoot.md) (Engelstalig) als de registratie van de connector niet lukt.  

4. Wanneer de installatie is voltooid, worden er twee nieuwe services toegevoegd aan uw server, zoals hieronder wordt weergegeven.

    - Met **Microsoft AAD Application Proxy Connector** wordt de connectiviteit mogelijk gemaakt
    - **Microsoft AAD Application Proxy Connector Updater** is een automatische updateservice waarmee periodiek wordt gecontroleerd op nieuwe versies en updates van de connector.

    ![Connectorservices voor toepassingsproxy - schermafbeelding](./media/active-directory-application-proxy-enable/app_proxy_services.png)

5. Klik in het installatievenster op **Voltooien** om de installatie te voltooien.

U bent nu klaar om [toepassingen te publiceren met toepassingsproxy](active-directory-application-proxy-publish.md).

Voor maximale beschikbaarheid dient u minstens nog één extra connector te implementeren. Herhaal de bovenstaande stappen 2 en 3 om meer connectoren te implementeren. Elke connector moet afzonderlijk worden geregistreerd.

Als u de connector wilt verwijderen, verwijdert u de connectorservice en de updaterservice en start u de computer opnieuw op om de service geheel te verwijderen.


## Volgende stappen

- [Toepassingen publiceren met toepassingsproxy (Engelstalig artikel)](active-directory-application-proxy-publish.md)
- [Toepassingen publiceren met uw eigen domeinnaam (Engelstalig artikel)](active-directory-application-proxy-custom-domains.md)
- [Eenmalige aanmelding inschakelen (Engelstalig artikel)](active-directory-application-proxy-sso-using-kcd.md)
- [Problemen met toepassingsproxy oplossen (Engelstalig artikel)](active-directory-application-proxy-troubleshoot.md)

Ga naar het [blog over toepassingsproxy](http://blogs.technet.com/b/applicationproxyblog/) voor nieuws en updates.



<!--HONumber=Jun16_HO2-->


