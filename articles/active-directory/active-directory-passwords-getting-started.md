<properties
    pageTitle="Aan de slag: Azure AD-wachtwoordbeheer | Microsoft Azure"
    description="Stel gebruikers in staat om hun eigen wachtwoorden opnieuw in te stellen, ontdek wat de vereisten zijn voor het opnieuw instellen van wachtwoorden en schakel Wachtwoord terugschrijven in om on-premises wachtwoorden te beheren in Active Directory."
    services="active-directory"
    keywords="Active directory password management, password management, reset Azure AD password"
    documentationCenter=""
    authors="asteen"
    manager="kbrint"
    editor="billmath"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="02/16/2016" 
    ms.author="asteen"/>

# Aan de slag met wachtwoordbeheer
Er hoeven slechts enkele eenvoudige stappen te worden uitgevoerd om uw gebruikers in staat te stellen hun eigen wachtwoorden voor Azure Active Directory in de cloud of on-premises te beheren. Wanneer u hebt gecontroleerd of u aan een aantal eenvoudige vereisten voldoet, is het wijzigen van wachtwoorden en het opnieuw instellen van wachtwoorden voor uw volledige organisatie voor u het weet ingeschakeld. In dit artikel leest u meer over de volgende concepten:

* [**Gebruikers in staat stellen om hun cloud-Azure Active Directory-wachtwoorden te wijzigen of opnieuw in te stellen**](#enable-users-to-reset-their-azure-ad-passwords)
 - [Vereisten voor de selfservice voor wachtwoordherstel](#prerequisites)
 - [Stap 1: een beleid voor het opnieuw instellen van wachtwoorden configureren](#step-1-configure-password-reset-policy)
 - [Stap 2: contactgegevens toevoegen voor uw testgebruiker](#step-2-add-contact-data-for-your-test-user)
 - [Stap 3: uw wachtwoord opnieuw instellen als gebruiker](#step-3-reset-your-azure-ad-password-as-a-user)
* [**Gebruikers in staat stellen om hun on-premises Active Directory-wachtwoorden te wijzigen of opnieuw in te stellen**](#enable-users-to-reset-or-change-their-ad-passwords)
 - [Vereisten voor het terugschrijven van wachtwoorden](#writeback-prerequisites)
 - [Stap 1: de nieuwste versie van Azure AD Connect downloaden](#step-1-download-the-latest-version-of-azure-ad-connect)
 - [Stap 2: het terugschrijven van wachtwoorden in Azure AD Connect inschakelen via de gebruikersinterface of PowerShell en controles uitvoeren](#step-2-enable-password-writeback-in-azure-ad-connect)
 - [Stap 3: uw firewall configureren](#step-3-configure-your-firewall)
 - [Stap 4: de juiste machtigingen instellen](#step-4-set-up-the-appropriate-active-directory-permissions)
 - [Stap 5: uw AD-wachtwoord als gebruiker opnieuw instellen en controleren](#step-5-reset-your-ad-password-as-a-user)

## Gebruikers in staat stellen om hun Azure AD-wachtwoorden opnieuw in te stellen
In dit gedeelte vindt u meer informatie over het inschakelen van de selfservice voor wachtwoordherstel voor uw AAD-clouddirectory, over het registreren van gebruikers voor de selfservice voor wachtwoordherstel en het als gebruiker uitvoeren van een testwachtwoordherstel via de selfservice.

- [Vereisten voor de selfservice voor wachtwoordherstel](#prerequisites)
- [Stap 1: een beleid voor het opnieuw instellen van wachtwoorden configureren](#step-1-configure-password-reset-policy)
- [Stap 2: contactgegevens toevoegen voor uw testgebruiker](#step-2-add-contact-data-for-your-test-user)
- [Stap 3: uw wachtwoord opnieuw instellen als gebruiker](#step-3-reset-your-azure-ad-password-as-a-user)


###  Vereisten
Voordat u het gebruik van de selfservice voor wachtwoordherstel kunt inschakelen, moet u aan de volgende vereisten voldoen:

- Maak een AAD-tenant. Zie [Aan de slag met Azure AD](https://azure.microsoft.com/trial/get-started-active-directory/) voor meer informatie.
- Zorg voor een Azure-abonnement. Zie [Wat is een Azure AD-tenant?](active-directory-administer.md#what-is-an-azure-ad-tenant) voor meer informatie.
- Koppel uw AAD-tenant aan uw Azure-abonnement. Zie [Hoe Azure-abonnementen worden gekoppeld aan Azure AD](https://msdn.microsoft.com/library/azure/dn629581.aspx) voor meer informatie.
- Voer een upgrade uit naar Azure AD Premium of Basic of gebruik een betaalde O365-licentie. Zie [Azure Active Directory-edities](https://azure.microsoft.com/pricing/details/active-directory/) voor meer informatie.

  >[AZURE.NOTE] Als u de selfservice voor wachtwoordherstel wilt inschakelen voor cloudgebruikers, moet u upgraden naar Azure AD Premium, Azure AD Basic of een betaalde O365-licentie.  Als u de selfservice voor wachtwoordherstel wilt inschakelen voor on-premises gebruikers, moet u upgraden naar Azure AD Premium. Zie [Azure Active Directory-edities](https://azure.microsoft.com/pricing/details/active-directory/) voor meer informatie. Deze informatie bevat gedetailleerde informatie over hoe u zich registreert voor Azure AD Premium of Basic, hoe u uw licentie activeert en hoe u toegang tot Azure AD activeert. U vindt hier ook informatie over het toewijzen van toegang aan beheerders- en gebruikersaccounts.

- Maak ten minste één beheerdersaccount en één gebruikersaccount in uw AAD-directory.
- Wijs een AAD Premium-, Basic- of betaalde O365-licentie toe aan de beheerder en het gebruikersaccount dat u hebt gemaakt.

### Stap 1: een beleid voor het opnieuw instellen van wachtwoorden configureren
Als u beleid voor het opnieuw instellen van wachtwoorden door gebruikers wilt configureren, volgt u de volgende stappen:

1.  Open een browser naar keuze en ga naar de [Azure-beheerportal](https://manage.windowsazure.com).
2.  In de [Azure-beheerportal](https://manage.windowsazure.com) gaat u naar de **Active Directory-extensie** op de navigatiebalk aan de linkerzijde.

    ![Wachtwoordbeheer in Azure AD][001]

3. Op het tabblad **Directory** klikt u op de directory waarin u het beleid voor het configureren van gebruikerswachtwoorden, zoals Wingtip Toys, wilt wijzigen.

    ![][002]

4.  Klik op het tabblad **Configureren**.

    ![][003]

5.  Op het tabblad **Configureren** gaat u naar het gedeelte **Beleid voor het opnieuw instellen van gebruikerswachtwoorden**.  Hier kunt u alle aspecten van het beleid voor het opnieuw instellen van gebruikerswachtwoorden configureren voor een bepaalde directory.  

    >[AZURE.NOTE] Dit **beleid geldt alleen voor eindgebruikers in uw organisatie en niet voor beheerders**. Uit veiligheidsoverwegingen bepaalt Microsoft het beleid voor wachtwoordherstel voor beheerders. Als u dit gedeelte niet ziet, controleert u of u zich hebt geregistreerd voor Azure Active Directory Premium of Basic en of u **een licentie hebt toegewezen** aan het beheerdersaccount waarmee deze functie wordt geconfigureerd.

    ![][004]

6.  Als u het beleid voor het opnieuw instellen voor gebruikerswachtwoorden wilt configureren, stelt u de knop **Gebruikers mogen wachtwoorden opnieuw instellen** in op **Ja**.  U krijgt dan meer besturingselementen te zien waarmee u kunt configureren hoe deze functie werkt in uw directory.  U kunt het opnieuw instellen van wachtwoorden aanpassen zoals u dat wilt.  Als u meer wilt weten over wat elk besturingselement van het beleid voor het opnieuw instellen van wachtwoorden doet, ziet u [Aanpassen: Azure AD-wachtwoordbeheer](active-directory-passwords-customize.md).

    ![][005]

7.  Wanneer u het beleid voor het opnieuw instellen van wachtwoorden naar wens hebt geconfigureerd voor uw tenant, klikt u op **Opslaan**, onder aan het scherm.

  >[AZURE.NOTE] Het beleid met twee uitdagingen wordt aanbevolen; dan kunt u zien hoe de functionaliteit in het meest complexe geval werkt.

  ![][006]

### Stap 2: contactgegevens toevoegen voor uw testgebruiker
U hebt verschillende mogelijkheden voor het opgeven van gegevens van gebruikers in uw organisatie voor het uitvoeren van wachtwoordherstel.

-   Gebruikers bewerken in de [Azure-beheerportal](https://manage.windowsazure.com) of de [Office 365-beheerportal](https://portal.microsoftonline.com)
-   Gebruik AAD Connect om gebruikerseigenschappen met Azure AD te synchroniseren via een on-premises Active Directory-domein
-   Windows PowerShell gebruiken om gebruikerseigenschappen te bewerken
-   Stel gebruikers in staat om hun eigen gegevens te registreren door ze naar de registratieportal te leiden op [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)
-   Vereis dat gebruikers zich registreren voor wachtwoordherstel wanneer ze zich aanmelden bij het deelvenster Toegang [http://myapps.microsoft.com](http://myapps.microsoft.com); dit doet u door de SSPR-configuratieoptie **Vereisen dat gebruikers zich registreren wanneer ze zich aanmelden bij het deelvenster Toegang** in te stellen op **Ja**.

Als u meer wilt weten over welke gegevens worden gebruikt tijdens het opnieuw instellen van een wachtwoord, en welke indelingsvereisten er gelden voor deze gegevens, ziet u [Welke gegevens worden gebruikt voor het opnieuw instellen van wachtwoorden?](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset).

#### Contactgegevens van een gebruiker toevoegen via de gebruikersregistratieportal
1.  Als u de registratieportal voor wachtwoordherstel wilt gebruiken, moet u de gebruikers in uw organisatie een koppeling sturen naar deze pagina ([http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)) of de optie inschakelen zodat wordt vereist dat gebruikers zich automatisch registreren.  Wanneer ze op deze koppeling klikken, wordt hen gevraagd om zich aan te melden met hun bedrijfsaccount.  Wanneer ze dat hebt gedaan, krijgen ze de volgende pagina te zien:

    ![][007]

2.  Hier kunnen gebruikers hun mobiele telefoonnummer opgeven en controleren en hun e-mailadres en/of beveiligingsvragen wijzigen.  Het controleren van een mobiel telefoonnummer gaat als volgt.

    ![][008]

3.  Wanneer een gebruiker deze informatie opgeeft, wordt de pagina bijgewerkt om aan te geven dat de informatie geldig is (deze wordt hieronder verborgen).  De gebruiker gaat door op **Voltooien** of **Annuleren** te klikken naar het deelvenster Toegang.

    ![][009]

4.  Wanneer een gebruiker beide gegevens heeft geverifieerd, wordt zijn of haar profiel bijgewerkt met de opgegeven gegevens.  In dit voorbeeld is het nummer **Telefoon (werk)** handmatig opgegeven zodat de gebruiker dat nummer ook als contactmethode kan gebruiken voor het opnieuw instellen van zijn of haar wachtwoord.

    ![][010]

### Stap 3: uw Azure AD-wachtwoord als gebruiker opnieuw instellen
Nu u een beleid voor het opnieuw instellen van wachtwoorden door gebruikers hebt geconfigureerd en u contactgegevens hebt opgegeven voor uw gebruiker, kan deze gebruiker de selfservice voor wachtwoordherstel gebruiken.

#### De selfservice voor wachtwoordherstel gebruiken
1.  Wanneer u naar een site als [**portal.microsoftonline.com**](http://portal.microsoftonline.com) gaat, ziet u een aanmeldingsscherm zoals dat hieronder.  Klik op de koppeling **Geen toegang tot uw account?** om de gebruikersinterface voor het opnieuw instellen van wachtwoorden te testen.

    ![][011]

2.  Wanneer u op **Geen toegang tot uw account?** hebt geklikt, gaat u naar een nieuwe pagina waarop om de **gebruikersnaam** wordt gevraagd waarvoor u het wachtwoord opnieuw wilt instellen.  Voer uw test**gebruikersnaam** in, voer de CAPTCHA in en klik op **Volgende**.

    ![][012]

3.  Omdat de gebruiker een **zakelijk telefoonnummer**, **mobiele telefoonnummer** en een **alternatief e-mailadres** heeft opgegeven, ziet u dat hij of zij al die opties heeft om de eerste uitdaging te doorstaan.

    ![][013]

4.  Kies er in dat geval voor om eerst te **bellen** naar het **zakelijke telefoonnummer**.  Wanneer de telefoonmethode wordt geselecteerd, wordt gebruikers gevraagd om hun telefoonnummer **te verifiëren** voordat ze hun wachtwoord opnieuw instellen.  Dit is om te voorkomen dat kwaadwillende personen gebruikers in uw organisatie gaan lastigvallen via hun telefoonnummers.

    ![][014]

5.  Wanneer de gebruiker zijn of haar telefoonnummer heeft bevestigd en deze op Bellen heeft geklikt, wordt er een spinner weergegeven en gaat de telefoon over.  Er wordt een bericht afgespeeld wanneer hij of zij de telefoon opneemt. De gebruiker moet **op # klikken** om zijn of haar account te verifiëren.  Wanneer op deze toets wordt gedrukt, wordt automatisch geverifieerd dat de gebruiker de eerste uitdaging heeft doorstaan en gaat deze in de gebruikersinterface door naar de tweede verificatiestap.

    ![][015]

6.  Wanneer u de eerste uitdaging hebt doorstaan, wordt de gebruikersinterface automatisch bijgewerkt zodat deze niet meer in de lijst opties van de gebruiker staat.  Omdat u het **zakelijke telefoonnummer** al hebt gebruikt, zijn alleen het **mobiele telefoonnummer** en het **alternatieve e-mailadres** nog beschikbaar als opties voor de tweede verificatiestap.  Klik op de optie **Stuur e-mail naar mijn alternatieve e-mailadres**.  Wanneer u dat hebt gedaan, wordt er een e-mail naar het geregistreerde alternatieve e-mailadres gestuurd wanneer u op E-mailen klikt.

    ![][016]

7.  Hier volgt een voorbeeld van een e-mailbericht dat gebruikers mogelijk zien (let op de tenantvormgeving):

    ![][017]

8.  Wanneer het e-mailbericht is ontvangen, wordt de pagina bijgewerkt en kunt u de verificatiecode invoeren die in de e-mail staat. Dat doet u in het invoervak, zoals hieronder wordt weergegeven.  Wanneer de juiste code is ingevoerd, wordt de knop Volgende beschikbaar en hebt u de tweede verificatiestap voltooid.

    ![][018]

9.  Als u aan de vereisten van het organisatiebeleid voldoet, kunt u een nieuw wachtwoord kiezen.  Het wachtwoord wordt gevalideerd als het voldoet aan de AAD-vereisten voor een sterk wachtwoord (zie [Wachtwoordbeleid in Azure AD](https://msdn.microsoft.com/library/azure/jj943764.aspx)). In het scherm is te zien of het wachtwoord dat de gebruiker invoert aan het beleid voldoet of niet.

    ![][019]

10. Wanneer u overeenkomende wachtwoorden hebt opgegeven die voldoen aan het organisatiebeleid, wordt uw wachtwoord opnieuw ingesteld en u kunt zich al direct aanmelden met uw nieuwe wachtwoord.

    ![][020]


## Gebruikers in staat stellen om hun AD-wachtwoorden opnieuw in te stellen of te wijzigen

In dit gedeelte vindt u meer informatie over het configureren van het opnieuw instellen van wachtwoorden, zodat ze worden teruggeschreven naar een on-premises Active Directory.

- [Vereisten voor het terugschrijven van wachtwoorden](#writeback-prerequisites)
- [Stap 1: de nieuwste versie van Azure AD Connect downloaden](#step-1-download-the-latest-version-of-azure-ad-connect)
- [Stap 2: het terugschrijven van wachtwoorden in Azure AD Connect inschakelen via de gebruikersinterface of PowerShell en controles uitvoeren](#step-2-enable-password-writeback-in-azure-ad-connect)
- [Stap 3: uw firewall configureren](#step-3-configure-your-firewall)
- [Stap 4: de juiste machtigingen instellen](#step-4-set-up-the-appropriate-active-directory-permissions)
- [Stap 5: uw AD-wachtwoord als gebruiker opnieuw instellen en controleren](#step-5-reset-your-ad-password-as-a-user)


### Vereisten voor terugschrijven
Voordat u het terugschrijven van wachtwoorden kunt inschakelen, moet u aan de volgende vereisten voldoen:

- U hebt een Azure AD-tenant waarvoor Azure AD Premium is ingeschakeld.  Zie [Azure Active Directory-edities](active-directory-editions.md) voor meer informatie.
- Het opnieuw instellen van wachtwoorden is geconfigureerd en ingeschakeld in uw tenant.  Zie [Gebruikers in staat stellen om hun Azure AD-wachtwoorden opnieuw in te stellen](#enable-users-to-reset-their-azure-ad-passwords) voor meer informatie
- U hebt ten minste één beheerdersaccount en één testgebruikersaccount nodig in combinatie met een Azure AD Premium-licentie waarmee u deze functie kunt testen.  Zie [Azure Active Directory-edities](active-directory-editions.md) voor meer informatie.

  > [AZURE.NOTE] Zorg ervoor dat het beheerdersaccount dat u gebruikt om Wachtwoord terugschrijven in te schakelen, een cloudbeheerdersaccount is (gemaakt in Azure AD) en geen gefedereerd account (gemaakt in on-premises AD en gesynchroniseerd met Azure AD).

- U hebt een on-premises AD-implementatie met één of meerdere forests met Windows Server 2008, Windows Server 2008 R2, Windows Server 2012 of Windows Server 2012 R2 waarop de nieuwste servicepacks zijn geïnstalleerd.

  > [AZURE.NOTE] Als u een oudere versie van Windows Server 2008 or 2008 R2 gebruikt, kunt u deze functie ook gebruiken, maar dan moet u [KB 2386717 downloaden en installeren](https://support.microsoft.com/kb/2386717), anders kunt u uw lokale AD-wachtwoordbeleid niet toepassen in de cloud.

- U hebt het Azure AD Connect-hulpprogramma geïnstalleerd en u hebt uw AD-omgeving voorbereid op synchronisatie naar de cloud.  Zie [Uw on-premises infrastructuur voor identiteiten in de cloud gebruiken](active-directory-aadconnect.md) voor meer informatie.

  > [AZURE.NOTE] Voordat u Wachtwoord terugschrijven test, voert u een volledige importeerbewerking en een volledige synchronisatie uit vanuit AD en Azure AD in Azure AD Connect.

- Als u Azure AD Sync of Azure AD Connect gebruikt, moet in sommige gevallen de uitgaande poort **TCP 443** (en in sommige gevallen **TCP 9350-9354**) open zijn.  Zie [Stap 3: uw firewall configureren](#step-3-configure-your-firewall) voor meer informatie. Het gebruik van DirSync wordt in dit scenario niet meer ondersteund.  Als u nog steeds DirSync gebruikt, voert u een upgrade uit naar de nieuwste versie van Azure AD Connect voordat u het terugschrijven van wachtwoorden implementeert.

  > [AZURE.NOTE] We raden iedereen die de hulpprogramma’s Azure AD Sync en DirSync gebruikt om te upgraden naar de nieuwste versie van Azure AD Connect voor de best mogelijke gebruikerservaring en nieuwe functies (zodra ze beschikbaar worden gemaakt).


### Stap 1: de nieuwste versie van Azure AD Connect downloaden
Wachtwoord terugschrijven is beschikbaar in de releases van Azure AD Connect of in het Azure AD Sync-hulpprogramma met versienummer **1.0.0419.0911** of hoger.  Wachtwoord terugschrijven met automatische accountontgrendeling is beschikbaar in de releases van Azure AD Connect of in het Azure AD Sync-hulpprogramma met versienummer **1.0.0485.0222** of hoger. Als u een oudere versie gebruikt, voert u een upgrade uit naar ten minste deze versie voordat u doorgaat. [Klik hier om de nieuwste versie van Azure AD Connect te downloaden](active-directory-aadconnect.md#download-azure-ad-connect).

#### De versie van Azure AD Sync controleren
1.  Ga naar **%ProgramFiles%\Azure Active Directory Sync\**.
2.  Zoek het uitvoerbare bestand **ConfigWizard.exe**.
3.  Klik met de rechtermuisknop op het uitvoerbare bestand en selecteer de optie **Eigenschappen** in het contextmenu.
4.  Klik op het tabblad **Details**.
5.  Zoek het veld **Bestandsversie**.

    ![][021]

Als het versienummer hoger is dan of gelijk is aan **1.0.0419.0911** of als u Azure AD Connect installeert, kunt u doorgaan naar [Stap 2: het terugschrijven van wachtwoorden in Azure AD Connect inschakelen via de gebruikersinterface of PowerShell en controles uitvoeren](#step-2-enable-password-writeback-in-azure-ad-connect).

 > [AZURE.NOTE] Als dit de eerste keer is dat u het Azure AD Connect-hulpprogramma installeert, wordt aangeraden dat u enkele aanbevolen procedures uitvoert om uw omgeving voor te bereiden op de directorysynchronisatie.  Voordat u het hulpprogramma Azure AD Connect installeert, moet u directorysynchronisatie inschakelen via de [Office 365-beheerportal](https://portal.microsoftonline.com) of de [Azure-beheerportal](https://manage.windowsazure.com).  Zie [Azure AD Connect beheren](active-directory-aadconnect-whats-next.md) voor meer informatie.


### Stap 2: Wachtwoord terugschrijven inschakelen in Azure AD Connect
Nu u het Azure AD Connect-hulpprogramma hebt gedownload, kunt u Wachtwoord terugschrijven inschakelen.  U kunt dit op twee manieren doen.  U kunt Wachtwoord terugschrijven inschakelen via het scherm Optionele functies van de Azure AD Connect-installatiewizard, of u kunt het inschakelen via Windows PowerShell.

#### Wachtwoord terugschrijven inschakelen via de configuratiewizard
1.  Op uw **Directory Sync-computer** opent u de **Azure AD Connect**-configuratiewizard.
2.  Klik door de stappen totdat u het configuratiescherm **Optionele functies** bereikt.
3.  Schakel de optie **Wachtwoord terugschrijven** in.

    ![][022]

4.  Voltooi de wizard. Op de laatste pagina wordt een overzicht weergegeven van de wijzigingen. In dit overzicht staat ook dat de configuratie van Wachtwoord terugschrijven is gewijzigd.

> [AZURE.NOTE] U kunt Wachtwoord terugschrijven te allen tijde weer uitschakelen door deze wizard opnieuw uit te voeren en de optie uit te schakelen, of door **Wachtwoorden terugschrijven naar on-premises directory** in te stellen op **Nee** in het gedeelte **Beleid voor het opnieuw instellen van wachtwoorden van gebruikers** van het tabblad **Configureren** van uw directory in de [Azure-beheerportal](https://manage.windowsazure.com).  Zie [Aanpassen: Azure AD-wachtwoordbeheer](active-directory-passwords-customize.md) voor meer informatie over het aanpassen van de ervaring bij het opnieuw instellen van wachtwoorden.

#### Wachtwoord terugschrijven inschakelen met Windows PowerShell
1.  Op uw **Directory Sync-computer** opent u een nieuw **verhoogd Windows PowerShell-venster**.
2.  Als de module nog niet is geladen, typt u in de `Import-Module ADSync`-opdracht om de Azure AD Connect-cmdlets naar uw huidige sessie te laden.
3.  Haal de lijst AAD-connectors in uw systeem op door de cmdlet `Get-ADSyncConnector` uit te voeren en de resultaten op te slaan in `$aadConnectorName`
4.  De huidige status van terugschrijven voor de huidige connector ophalen door de volgende cmdlet uit te voeren: `Get-ADSyncAADPasswordResetConfiguration –Connector $aadConnectorName`
5.  Wachtwoord terugschrijven inschakelen door de cmdlet uit te voeren: `Set-ADSyncAADPasswordResetConfiguration –Connector $aadConnectorName –Enable $true`

> [AZURE.NOTE] Als u wordt gevraagd om een referentie, zorgt u ervoor dat het beheerdersaccount dat u opgeeft voor AzureADCredential een **cloudbeheerdersaccount is (gemaakt in Azure AD)** en geen gefedereerd account (gemaakt in on-premises AD en gesynchroniseerd met Azure AD).
> [AZURE.NOTE] U kunt Wachtwoord terugschrijven uitschakelen via PowerShell door de bovenstaande instructies uit te voeren, maar door `$false` over te slaan of door de instelling **Wachtwoorden terugschrijven naar on-premises directory** in te stellen op **Nee** in het gedeelte **Beleid voor het opnieuw instellen van wachtwoorden van gebruikers** van het tabblad **Configureren** van uw directory in de [Azure-beheerportal](https://manage.windowsazure.com).

#### Controleren of de configuratie is voltooid
Wanneer de configuratie is voltooid, krijgt u het bericht Terugschrijven voor wachtwoord opnieuw instellen is ingeschakeld te zien in het Windows PowerShell-venster, of u krijgt een voltooiingsbericht te zien in de gebruikersinterface van de configuratie.

U kunt ook controleren of de service correct is geïnstalleerd door Logboeken te openen, naar het toepassingsgebeurtenislogboek te gaan en te zoeken naar de gebeurtenis **31005 - OnboardingEventSuccess**, van de bron **PasswordResetService**.

  ![][023]

### Stap 3: uw firewall configureren
Wanneer u Wachtwoord terugschrijven hebt ingeschakeld in het Azure AD Connect-hulpprogramma, moet u controleren of de service verbinding kan maken met de cloud.

1.  Zodra de installatie is voltooid en u onbekende uitgaande verbindingen in uw omgeving blokkeert, moet u ook de volgende regels toevoegen aan de firewall. Start uw AAD Connect-computer opnieuw op nadat u deze wijzigingen hebt doorgevoerd:
   - Uitgaande verbindingen via TCP-poort 443 toestaan
   - Uitgaande verbindingen naar https://ssprsbprodncu-sb.accesscontrol.windows.net/ toestaan
   - Wanneer er problemen zijn met een proxy of als er algemene verbindingsproblemen zijn, staat u uitgaande verbindingen via poorten 9350-9354 en TCP-poort 5671 toe

### Stap 4: de juiste Active Directory-machtigingen instellen
Bij elke forest die gebruikers bevat waarvan de wachtwoorden opnieuw worden ingesteld en waarbij X het account is dat is opgegeven voor die forest in de configuratiewizard (tijdens de eerste configuratie), moet X de machtigingen **Wachtwoord wijzigen**, **Wachtwoord opnieuw instellen**, **Schrijfmachtigingen** voor `lockoutTime` en **Schrijfmachtigingen** voor `pwdLastSet` krijgen. Daarbij moet X ook uitgebreide rechten krijgen voor het hoofdobject van elk domein in het forest. Het recht moet worden gemarkeerd als 'overgenomen door alle gebruikersobjecten'.  

Als u niet zeker welk account hierboven wordt bedoeld, opent u de gebruikersinterface van de Azure Active Directory Connect-configuratie en klikt u op **Uw oplossing controleren**.  Het account waar u machtigingen voor moet toevoegen, is rood onderstreept in de onderstaande schermafbeelding.

**<font color="red">Zorg ervoor dat deze machtiging voor elk domein in elk forest van uw systeem wordt ingesteld, anders werkt Wachtwoord terugschrijven niet goed.</font>**

  ![][032]

  Als u deze machtigingen instelt, kan het MA-serviceaccount voor elk forest de wachtwoorden beheren namens de gebruikersaccounts in dat forest. Als u deze machtigingen niet toewijst, lijkt het alsof terugschrijving correct is geconfigureerd, maar kan het zijn er fouten optreden bij gebruikers wanneer ze hun on-premises wachtwoorden vanuit de cloud proberen te wijzigen. Hier volgen gedetailleerde stappen op basis waarvan u dit kunt doen met de **Active Directory-beheermodule Gebruikers en computers**:

>[AZURE.NOTE] Het kan een uur duren voordat de machtigingen zijn gerepliceerd naar alle objecten in uw directory.

#### De juiste machtigingen instellen voor terugschrijving

1.  Open **Active Directory: gebruikers en computers** met een account met de juiste machtigingen voor domeinbeheer.
2.  Zorg ervoor dat bij **Weergavemenu** de optie **Geavanceerde functies** is ingeschakeld.
3.  In het linkerdeelvenster klikt u met de rechtermuisknop op het object dat de hoofdmap van het domein vertegenwoordigt.
4.  Klik op het tabblad **Beveiliging**.
5.  Klik vervolgens op **Geavanceerd**.

    ![][024]

6.  Klik op het tabblad **Machtigingen** op **Toevoegen**.

    ![][025]

7.  Selecteer het account waar u machtigingen aan wilt toewijzen (hetzelfde account dat is opgegeven tijdens het instellen van de synchronisatie voor dat forest).
8.  Selecteer in de vervolgkeuzelijst bovenaan de optie **Onderliggende gebruikersobjecten**.
9.  In het dialoogvenster **Vermelding van machtiging** dat wordt weergegeven schakelt u de volgende selectievakjes in: **Wachtwoord opnieuw instellen**, **Wachtwoord wijzigen**, **Schrijfmachtigingen** voor `lockoutTime` en **Schrijfmachtigingen** voor `pwdLastSet`.

    ![][026]
    ![][027]
    ![][028]

10. Klik vervolgens op **Toepassen/OK** in alle open dialoogvensters.

### Stap 5: uw AD-wachtwoord opnieuw instellen als gebruiker
Nu Wachtwoord terugschrijven is ingeschakeld, kunt u testen of dit werkt. Hiervoor stelt u het wachtwoord van een gebruiker opnieuw in waarvan het account is gesynchroniseerd met uw cloudtenant.

#### Controleren of Wachtwoord terugschrijven goed werkt
1.  Ga naar [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com) of naar een ander aanmeldingsscherm van een organisatie en klik op de koppeling **Geen toegang tot uw account?**.

    ![][029]

2.  U krijgt nu een nieuwe pagina te zien waarop om de gebruikers-id wordt gevraagd waarvoor u het wachtwoord opnieuw wilt instellen. Voer uw testgebruikers-id in en volg de stappen voor het opnieuw instellen van het wachtwoord.
3.  Wanneer u uw wachtwoord opnieuw hebt ingesteld, krijgt u een scherm te zien dat er ongeveer als volgt uitziet. Dit betekent dat u uw wachtwoord opnieuw hebt ingesteld voor uw on-premises en/of clouddirectory.

    ![][030]

4.  Als u wilt controleren of de bewerking is voltooid of om fouten op te sporten, gaat u naar uw **Directory Sync-computer**, opent u **Logboeken**, gaat u naar het **toepassingsgebeurtenislogboek** en zoek naar de gebeurtenis **31002 - PasswordResetSuccess** van de bron **PasswordResetService** van uw testgebruiker.

    ![][031]


<br/>
<br/>
<br/>

## Koppelingen naar documentatie over wachtwoordherstel
Hieronder vindt u koppelingen naar alle Azure AD-documentatiepagina’s over wachtwoordherstel:

* [**Uw eigen wachtwoord opnieuw instellen**](active-directory-passwords-update-your-own-password.md): meer informatie over hoe u als gebruiker van het systeem uw eigen wachtwoord kunt wijzigen of opnieuw in kunt stellen
* [**Hoe werkt het?**](active-directory-passwords-how-it-works.md): meer informatie over de zes verschillende onderdelen van de service en wat elke service biedt
* [**Aanpassen**](active-directory-passwords-customize.md): informatie over het aanpassen van de weergave en het gedrag van de service om aan de behoeften van uw organisatie te voldoen
* [**Aanbevolen procedures**](active-directory-passwords-best-practices.md): informatie over het snel implementeren en effectief beheren van wachtwoorden in uw organisatie
* [**Inzichten verkrijgen**](active-directory-passwords-get-insights.md): meer informatie over onze geïntegreerde rapportagemogelijkheden
* [**Veelgestelde vragen**](active-directory-passwords-faq.md): verkrijg antwoord op veelgestelde vragen
* [**Probleemoplossing**](active-directory-passwords-troubleshoot.md): informatie over het snel oplossen van problemen met de service
* [**Meer informatie**](active-directory-passwords-learn-more.md): meer informatie over de technische details van de werking van de service



[001]: ./media/active-directory-passwords-getting-started/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-getting-started/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-getting-started/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-getting-started/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-getting-started/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-getting-started/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-getting-started/007.jpg "Image_007.jpg"
[008]: ./media/active-directory-passwords-getting-started/008.jpg "Image_008.jpg"
[009]: ./media/active-directory-passwords-getting-started/009.jpg "Image_009.jpg"
[010]: ./media/active-directory-passwords-getting-started/010.jpg "Image_010.jpg"
[011]: ./media/active-directory-passwords-getting-started/011.jpg "Image_011.jpg"
[012]: ./media/active-directory-passwords-getting-started/012.jpg "Image_012.jpg"
[013]: ./media/active-directory-passwords-getting-started/013.jpg "Image_013.jpg"
[014]: ./media/active-directory-passwords-getting-started/014.jpg "Image_014.jpg"
[015]: ./media/active-directory-passwords-getting-started/015.jpg "Image_015.jpg"
[016]: ./media/active-directory-passwords-getting-started/016.jpg "Image_016.jpg"
[017]: ./media/active-directory-passwords-getting-started/017.jpg "Image_017.jpg"
[018]: ./media/active-directory-passwords-getting-started/018.jpg "Image_018.jpg"
[019]: ./media/active-directory-passwords-getting-started/019.jpg "Image_019.jpg"
[020]: ./media/active-directory-passwords-getting-started/020.jpg "Image_020.jpg"
[021]: ./media/active-directory-passwords-getting-started/021.jpg "Image_021.jpg"
[022]: ./media/active-directory-passwords-getting-started/022.jpg "Image_022.jpg"
[023]: ./media/active-directory-passwords-getting-started/023.jpg "Image_023.jpg"
[024]: ./media/active-directory-passwords-getting-started/024.jpg "Image_024.jpg"
[025]: ./media/active-directory-passwords-getting-started/025.jpg "Image_025.jpg"
[026]: ./media/active-directory-passwords-getting-started/026.jpg "Image_026.jpg"
[027]: ./media/active-directory-passwords-getting-started/027.jpg "Image_027.jpg"
[028]: ./media/active-directory-passwords-getting-started/028.jpg "Image_028.jpg"
[029]: ./media/active-directory-passwords-getting-started/029.jpg "Image_029.jpg"
[030]: ./media/active-directory-passwords-getting-started/030.jpg "Image_030.jpg"
[031]: ./media/active-directory-passwords-getting-started/031.jpg "Image_031.jpg"
[032]: ./media/active-directory-passwords-getting-started/032.jpg "Image_032.jpg"



<!--HONumber=Jun16_HO2-->


