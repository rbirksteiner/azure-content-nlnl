<properties
   pageTitle="Azure AD Connect: upgraden van DirSync | Microsoft Azure"
   description="Kom meer te weten over het upgraden van DirSync naar Azure AD Connect. In deze artikelen worden de stappen beschreven voor het upgraden van DirSync naar Azure AD Connect."
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.date="05/31/2016"
   ms.author="andkjell;shoatman;billmath"/>

# Azure AD Connect: upgraden van DirSync
Azure AD Connect is de opvolger van DirSync. In dit onderwerp vindt u de manieren voor het upgraden van DirSync. Deze stappen werken niet voor een upgrade van een andere versie van Azure AD Connect of Azure AD Sync.

Zorg ervoor dat, voordat u begint met de installatie van Azure AD Connect, u [Azure AD Connect downloadt](http://go.microsoft.com/fwlink/?LinkId=615771) en de vereiste stappen in [Azure AD Connect: Hardware en vereisten](active-directory-aadconnect-prerequisites.md) voltooit.

Als u de upgrade niet vanaf DirSync wilt uitvoeren, zie dan [verwante documentatie](#related-documentation) voor andere scenario's.

## Upgraden van DirSync
Er zijn verschillende opties voor de upgrade, afhankelijk van uw huidige DirSync-implementatie. Als de verwachte tijd voor de upgrade korter is dan drie uur, wordt het aanbevolen om een in-place upgrade uit te voeren. Als de verwachte tijd voor de upgrade langer is dan drie uur, wordt het aanbevolen om een parallelle implementatie uit te voeren op een andere server. Als u meer dan 50.000 objecten hebt, schatten we dat de upgrade langer dan drie uur zal duren.

Scenario |  
---- | ----
[In-place upgrade](#in-place-upgrade)  | Voorkeursoptie als wordt verwacht dat de upgrade niet langer dan drie uur duurt.
[Parallelle implementatie](#parallel-deployment) | Voorkeursoptie als wordt verwacht dat de upgrade wel langer dan drie uur duurt.

>[AZURE.NOTE] Wanneer u van plan bent een upgrade uit te voeren van DirSync naar Azure AD Connect, moet u niet zelf DirSync verwijderen voorafgaand aan de upgrade. Azure AD Connect zal de configuratie van DirSync lezen en migreren, en vervolgens verwijderen na het inspecteren van de server.

**In-place upgrade**  
De verwachte tijd om de upgrade te voltooien wordt door de wizard weergegeven. Deze schatting is gebaseerd op de veronderstelling dat het uitvoeren van een upgrade voor een database met 50.000 objecten (gebruikers, contactpersonen en groepen) drie uur duurt. Azure AD Connect analyseert uw huidige DirSync-instellingen en het beveelt een in-place upgrade aan als het aantal objecten in uw database minder dan 50.000 is. Als u besluit om door te gaan, worden uw huidige instellingen automatisch toegepast tijdens de upgrade en hervat uw server automatisch de actieve synchronisatie.

Als u een configuratiemigratie en een parallelle implementatie wilt uitvoeren, kunt u de aanbeveling voor een in-place upgrade negeren. U kunt bijvoorbeeld van het moment gebruikmaken voor het vernieuwen van de hardware en het besturingssysteem. Zie de sectie [parallelle implementatie](#parallel-deployment) voor meer informatie.

**Parallelle implementatie**  
Het uitvoeren van een parallelle implementatie wordt aanbevolen als u meer dan 50.000 objecten hebt. Dit voorkomt dat uw gebruikers last krijgen van een vertraagde service. Het installatieprogramma van Azure AD Connect probeert de downtime voor de upgrade in te schatten, maar als u DirSync in het verleden hebt geüpgraded, is uw eigen ervaring waarschijnlijk de beste richtlijn.

### Ondersteunde configuraties voor DirSync die worden bijgewerkt
De volgende configuratiewijzigingen worden ondersteund bij DirSync en zullen worden bijgewerkt:

- Domein en OE filteren
- Alternatief id (UPN)
- Wachtwoordsynchronisatie en Exchange hybrid uitwisselen
- Uw forest/domein- en Azure AD-instellingen
- Filteren op basis van gebruikerskenmerken

De volgende wijziging kan niet worden bijgewerkt. Als u deze wijziging hebt aangebracht, wordt de upgrade geblokkeerd:

- Niet-ondersteunde DirSync-wijzigingen: bijvoorbeeld verwijderde kenmerken en het gebruik van een aangepaste extensie-DLL

![Upgrade geblokkeerd](./media/active-directory-aadconnect-dirsync-upgrade-get-started/analysisblocked.png)

In dit soort gevallen wordt het installeren van een nieuwe Azure AD Connect-server in de [faseringsmodus](active-directory-aadconnectsync-operations.md#staging-mode) en het controleren van de oude DirSync en nieuwe Azure AD Connect-configuratie aanbevolen. Pas eventuele wijzigingen aan met behulp van aangepaste configuratie, zoals beschreven in [Aangepaste configuratie Azure AD Connect-synchronisatie](active-directory-aadconnectsync-whatis.md).

De wachtwoorden die door DirSync worden gebruikt voor de serviceaccounts kunnen niet worden opgehaald en worden niet gemigreerd. Deze wachtwoorden worden opnieuw ingesteld tijdens de upgrade.

### Belangrijke stappen voor het upgraden van DirSync naar Azure AD Connect

1. Welkom bij Azure AD Connect
2. Analyse van de huidige DirSync-configuratie
3. Azure AD globaal beheerderswachtwoord ophalen
4. Referenties voor een enterprise-beheerdersaccount ophalen (alleen gebruikt tijdens de installatie van Azure AD Connect)
5. Installatie van Azure AD Connect
    * DirSync verwijderen
    * Azure AD Connect installeren
    * Optioneel beginnen met synchroniseren

Extra stappen zijn vereist wanneer:

* U momenteel de volledige SQL Server gebruikt (lokaal of extern)
* U meer dan 50.000 objecten gereed hebt voor synchronisatie

## In-place upgrade

1. Start het Azure AD Connect-installatieprogramma (MSI) op.
2. Lees en ga akkoord met de licentievoorwaarden en de privacyverklaring.
![Welkom bij Azure AD](./media/active-directory-aadconnect-dirsync-upgrade-get-started/Welcome.png)
3. Klik op Volgende om te beginnen met de analyse van uw huidige DirSync-installatie.
![Analyse van de huidige Directory Sync-installatie](./media/active-directory-aadconnect-dirsync-upgrade-get-started/Analyze.png)
4. Als de analyse is voltooid, verschijnen de aanbevelingen voor de vervolgacties.  
    - Als u SQL Server Express gebruikt en minder dan 50.000 objecten hebt, wordt het volgende venster weergegeven: ![Analyse voltooid, gereed voor upgrade van DirSync](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisReady.png)
    - Als u een volledige SQL Server voor DirSync gebruikt, ziet u in plaats daarvan deze pagina: ![Analyse voltooid, gereed voor upgrade van DirSync](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisReadyFullSQL.png)  
De informatie over de huidige SQL Server-databaseserver die wordt gebruikt door DirSync wordt weergegeven. Maak, indien nodig, de juiste aanpassingen. Klik op **Volgende** om door te gaan met de installatie.
    - Als u meer dan 50.000 objecten hebt, ziet u dit venster: ![Analyse voltooid, gereed voor upgrade van DirSync](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)  
Klik om door te gaan met een in-place upgrade op het selectievakje naast dit bericht : **Doorgaan met het uitvoeren van de upgrade van DirSync op deze computer.**
Voor het uitvoeren van een [parallelle implementatie](#parallel-deployment) dient u de DirSync-configuratie-instellingen te exporteren en deze naar de nieuwe server te verplaatsen.
5. Geef het wachtwoord op voor het account dat u momenteel gebruikt om verbinding te maken met Azure AD. Dit moet het account zijn dat momenteel wordt gebruikt door DirSync.
![Voer uw Azure AD-referenties in](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ConnectToAzureAD.png)  
Als u een foutbericht ontvangt en problemen met de connectiviteit hebt, raadpleeg dan [Connectiviteitsproblemen oplossen](active-directory-aadconnect-troubleshoot-connectivity.md).
6. Geef een enterprise-beheerdersaccount op voor Active Directory.
![Voer uw ADDS-referenties in](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ConnectToADDS.png)
7. U kunt nu starten met de configuratie. Wanneer u klikt op **Upgrade**, wordt DirSync verwijderd en Azure AD Connect geconfigureerd. Azure AD Connect zal ook de synchronisatie starten.
![Gereed voor configuratie](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ReadyToConfigure.png)
8. Nadat de installatie is voltooid, dient u zich af te melden en weer aan te melden bij Windows vóór u Synchronization Service Manager of Synchronization Rule Editor gaat gebruiken of andere configuratiewijzigingen gaat maken.

## Parallelle implementatie

### De DirSync-configuratie exporteren
**Parallelle implementatie met meer dan 50.000 objecten**

Als u meer dan 50.000 objecten hebt, raadt de Azure AD Connect-installatie een parallelle implementatie aan.

Er wordt een venster met de volgende strekking weergegeven:

![Analyse voltooid](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)

Als u wilt doorgaan met de parallelle implementatie, moet u de volgende stappen uitvoeren:

- Klik op de knop **Instellingen exporteren**. Als u Azure AD Connect op een afzonderlijke server installeert, worden deze instellingen geïmporteerd voor het migreren van de instellingen van uw huidige DirSync naar de nieuwe Azure AD Connect-installatie.

Wanneer uw instellingen zijn geëxporteerd, kunt u de Azure AD Connect-wizard afsluiten op de DirSync-server. Ga door met de volgende stap om [Azure AD Connect op een afzonderlijke server te installeren](#installation-of-azure-ad-connect-on-separate-server)

**Parallelle implementatie met minder dan 50.000 objecten**

Als u minder dan 50.000 objecten hebt, maar nog steeds een parallelle implementatie wilt uitvoeren, doet u het volgende:

1. Start het Azure AD Connect-installatieprogramma (MSI) op.
2. Als u het venster **Welkom bij Azure AD Connect** ziet, sluit u de installatiewizard door te klikken op 'X' in de rechterbovenhoek van het venster.
3. Open een opdrachtprompt.
4. Voer de volgende opdracht uit vanaf de locatie waar Azure AD Connect is geïnstalleerd (Standaard: C:\Program Files\Microsoft Azure Active Directory Connect) `AzureADConnect.exe /ForceExport`
5. Klik op de knop **Instellingen exporteren**.  Als u Azure AD Connect op een afzonderlijke server installeert, worden deze instellingen geïmporteerd voor het migreren van de instellingen van uw huidige DirSync naar de nieuwe Azure AD Connect-installatie.

![Analyse voltooid](./media/active-directory-aadconnect-dirsync-upgrade-get-started/forceexport.png)

Wanneer uw instellingen zijn geëxporteerd, kunt u de Azure AD Connect-wizard afsluiten op de DirSync-server. Ga door met de volgende stap om [Azure AD Connect op een afzonderlijke server te installeren](#installation-of-azure-ad-connect-on-separate-server)

### Azure AD Connect op een afzonderlijke server installeren

Wanneer u Azure AD Connect installeert op een nieuwe server gaat deze ervan uit dat u een schone installatie van Azure AD Connect wilt uitvoeren. Omdat u de DirSync-configuratie wilt gebruiken, zult u nog een paar extra stappen moeten uitvoeren:

1. Start het Azure AD Connect-installatieprogramma (MSI) op.
2. Als u het venster **Welkom bij Azure AD Connect** ziet, sluit u de installatiewizard door te klikken op 'X' in de rechterbovenhoek van het venster.
3. Open een opdrachtprompt.
4. Voer de volgende opdracht uit vanaf de locatie waar Azure AD Connect is geïnstalleerd (Standaard: C:\Program Files\Microsoft Azure Active Directory Connect):  `AzureADConnect.exe /migrate`.
    De Azure AD Connect-installatiewizard wordt gestart en geeft het volgende weer: ![Voer uw Azure AD-referenties in](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ImportSettings.png)
5. Selecteer het instellingenbestand dat is geëxporteerd vanuit uw DirSync-installatie.
6. Configureer de geavanceerde opties, inclusief:
    - Een aangepaste installatielocatie voor Azure AD Connect.
    - Een bestaand exemplaar van SQL Server (standaard: Azure AD Connect installeert SQL Server 2012 Express). Gebruik niet hetzelfde database-exemplaar als die van uw DirSync-server.
    - Een serviceaccount gebruikt voor de verbinding met SQL Server (als de SQL Server-database extern is, moet dit account een domeinserviceaccount zijn).
Deze opties kunnen worden weergegeven op dit venster: ![Voer uw Azure AD-referenties in](./media/active-directory-aadconnect-dirsync-upgrade-get-started/advancedsettings.png)
7. Klik op **Volgende**.
8. Laat op de pagina **Gereed voor configuratie** het vakje **Start het synchronisatieproces zodra de configuratie is voltooid** aangevinkt. De server bevindt zich in de [faseringsmodus](active-directory-aadconnectsync-operations.md#staging-mode) zodat wijzigingen deze keer niet geëxporteerd worden naar Azure AD.
9. Klik op **Installeren**.
10. Nadat de installatie is voltooid, dient u zich af te melden en weer aan te melden bij Windows vóór u Synchronization Service Manager of Synchronization Rule Editor gaat gebruiken of andere configuratiewijzigingen gaat maken.

>[AZURE.NOTE] De synchronisatie van Windows Server Active Directory en Azure Active Directory wordt gestart, maar er zijn zullen geen wijzigingen worden geëxporteerd naar Azure AD.  Slechts één synchronisatieprogramma tegelijk kan actief wijzigingen exporteren. Dit wordt de [faseringsmodus](active-directory-aadconnectsync-operations.md#staging-mode) genoemd.

### Controleren of Azure AD Connect gereed is voor de synchronisatie

Om te controleren of Azure AD Connect gereed is om het over te nemen van DirSync dient u **Synchronization Service Manager** in de groep **Azure AD Connect** te openen vanuit het startmenu.

In de toepassing dient u naar het tabblad **Bewerkingen** te kijken. Op dit tabblad moet u controleren of de volgende bewerkingen zijn uitgevoerd:

- Importeren op de AD-connector
- Importeren op de Azure AD-connector
- Volledige synchronisatie van de AD-Connector
- Volledige synchronisatie van de Azure AD-Connector

![Importeren en synchroniseren zijn voltooid](./media/active-directory-aadconnect-dirsync-upgrade-get-started/importsynccompleted.png)

Controleer het resultaat van deze bewerkingen en zorg ervoor dat er geen fouten zijn.

Als u de wijzigingen die op het punt staan geëxporteerd te worden naar Azure AD wilt bekijken en controleren, lees dan hier hoe u de configuratie onder [faseringsmodus](active-directory-aadconnectsync-operations.md#staging-mode) kunt controleren. Voer de vereiste wijzigingen door in de configuratie zodat alles klopt.

Wanneer deze 4 bewerkingen zijn voltooid, er geen fouten zijn en u tevreden bent met de wijzigingen die geëxporteerd zullen worden, bent u klaar om DirSync te verwijderen en de Azure AD Connect-synchronisatie in te schakelen. Voer de volgende twee stappen uit om de migratie te voltooien.

### DirSync verwijderen (oude server)

- Zoek in **Programma's en onderdelen** naar **Windows Azure Active Directory-synchronisatie**
- **Windows Azure Active Directory-synchronisatie** verwijderen
- Houd er rekening mee dat de verwijdering tot wel 15 minuten kan duren.

Wanneer DirSync verwijderd is, is er geen actieve server meer die naar Azure AD exporteert. De volgende stap moet worden voltooid voordat de wijzigingen in uw lokale Active Directory gesynchroniseerd blijven worden naar Azure AD.

### Azure AD Connect inschakelen (nieuwe server)
Na de installatie, biedt Azure AD Connect u de mogelijkheid om extra configuratiewijzigingen door te voeren. Start **Azure AD Connect** op vanuit het startmenu of vanuit de snelkoppeling op het bureaublad. Zorg ervoor dat u niet nogmaals het installatieprogramma MSI opstart.

U ziet nu het volgende:

![Extra taken](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AdditionalTasks.png)

- Selecteer **Faseringsmodus configureren**.
- Schakel fasering uit door het vinkje weg te halen bij het selectievakje **Faseringsmodus ingeschakeld**.

![Voer uw Azure AD-referenties in](./media/active-directory-aadconnect-dirsync-upgrade-get-started/configurestaging.png)

- Klik op de knop **Volgende**
- Klik op de bevestigingspagina op de knop **Installeren**.

Azure AD Connect is nu uw actieve server.

## Volgende stappen
Nu u Azure AD Connect geïnstalleerd hebt, kunt u [de installatie verifiëren en licenties toewijzen](active-directory-aadconnect-whats-next.md).

Kom meer te weten over deze functies, die tijdens de installatie zijn ingeschakeld: [Automatische upgrade](active-directory-aadconnect-feature-automatic-upgrade.md), [Onopzettelijk verwijderen voorkomen](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) en [Azure AD Connect Health](active-directory-aadconnect-health-sync.md).

Kom meer te weten over deze veelvoorkomende onderwerpen: [Scheduler en het activeren van de synchronisatie](active-directory-aadconnectsync-feature-scheduler.md).

Kom meer te weten over het [integreren van uw on-premises identiteiten met Azure Active Directory ](active-directory-aadconnect.md).

## Verwante documentatie

Onderwerp |  
--------- | ---------
Overzicht Azure AD Connect | [Uw on-premises identiteiten integreren met Azure Active Directory](active-directory-aadconnect.md)
Upgraden van een vorige versie van Connect | [Upgraden van een vorige versie van Connect](active-directory-aadconnect-upgrade-previous-version.md)
Installeren met behulp van snelle instellingen | [Snelle installatie van Azure AD Connect](active-directory-aadconnect-get-started-express.md)
Installeren met behulp van aangepaste instellingen | [Aangepaste installatie van Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
Accounts die worden gebruikt voor installatie | [Meer informatie over accounts en machtigingen van Azure AD Connect](active-directory-aadconnect-accounts-permissions.md)



<!--HONumber=Jun16_HO2-->


