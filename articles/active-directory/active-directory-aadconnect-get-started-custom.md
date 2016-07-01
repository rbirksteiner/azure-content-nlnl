<properties
    pageTitle="Azure AD Connect: Aangepaste installatie | Microsoft Azure"
    description="In dit document worden de opties voor een aangepaste installatie van Azure AD Connect beschreven. Volg deze instructies om Active Directory met Azure AD Connect te installeren."
    services="active-directory"
    keywords="what is Azure AD Connect, install Active Directory, required components for Azure AD"
    documentationCenter=""
    authors="andkjell"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory"  
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/31/2016"
    ms.author="billmath;andkjell"/>

# Aangepaste installatie van Azure AD Connect
Voor meer opties voor de installatie gaat u naar **Aangepaste instellingen**. Deze instellingen gebruikt u wanneer u meerdere forests hebt of als u optionele functies wilt configureren die niet in de snelle installatie voorkomen. De aangepaste instellingen worden gebruikt in alle gevallen waarin de optie [**snelle installatie**](active-directory-aadconnect-get-started-express.md) niet aan uw implementatie of topologie voldoet.

Zorg ervoor dat u, voordat u begint met de installatie van Azure AD Connect, [Azure AD Connect downloadt](http://go.microsoft.com/fwlink/?LinkId=615771) en de vereiste stappen in [Azure AD Connect: Hardware and prerequisites](active-directory-aadconnect-prerequisites.md) voltooit. Zorg er ook voor dat de benodigde accounts beschikbaar zijn, zoals beschreven in [Azure AD Connect accounts and permissions](active-directory-aadconnect-accounts-permissions.md).

Als de aangepaste instellingen niet met uw topologie overeenkomen, bijvoorbeeld om DirSync te upgraden, bekijk dan de [verwante documentatie](#related-documentation) voor andere scenario's.

## Installatie van Azure AD Connect met aangepaste instellingen

### Snelle instellingen
Klik op deze pagina op **Aanpassen** om een installatie met aangepaste instellingen te beginnen.

### Vereiste onderdelen installeren
Wanneer u de synchronisatieservices installeert, kunt de sectie optionele configuratie uitschakelen: Azure AD Connect stelt alles dan automatisch in. Het programma stelt een exemplaar van SQL Server 2012 Express LocalDB in, maakt de juiste groepen aan en wijst machtigingen toe. Als u de standaardinstellingen wilt wijzigen, kunt u in de volgende tabel zien welke opties voor optionele configuratie beschikbaar zijn.

![Vereiste onderdelen](./media/active-directory-aadconnect-get-started-custom/requiredcomponents.png)

Optionele configuratie  | Beschrijving
------------- | -------------
Een bestaande SQL Server gebruiken | Hiermee kunt u de naam van de SQL Server en de exemplaarnaam opgeven. Kies deze optie als u al een databaseserver heeft die u wilt gebruiken. Voer de exemplaarnaam, gevolgd door een komma en poortnummer, in bij **Exemplaarnaam** als bladeren niet is ingeschakeld voor uw SQL Server.
Een bestaand serviceaccount gebruiken | Azure AD Connect maakt standaard een lokaal serviceaccount aan voor de synchronisatieservices. Het wachtwoord wordt automatisch gegenereerd en is onbekend bij de persoon die Azure AD Connect installeert. Als u een externe SQL server gebruikt of een proxyserver waarvoor verificatie vereist is, dan heeft u een serviceaccount in het domein nodig en moet u het wachtwoord kennen. Voer in dat geval het te gebruiken serviceaccount in. Zorg dat de gebruiker die de installatie uitvoert een SA in SQL is, zodat een aanmelding voor het serviceaccount kan worden aangemaakt. Zie [Azure AD Connect accounts and permissions](active-directory-aadconnect-accounts-permissions.md#custom-settings-installation)
Aangepaste synchronisatiegroepen opgeven | Azure AD Connect maakt standaard vier groepen lokaal op de server aan wanneer de synchronisatieservices worden geïnstalleerd. Deze groepen zijn: Beheerders, Operators, Bladeren en Wachtwoord opnieuw instellen. U kunt hier uw eigen groepen opgeven. De groepen moeten lokaal op de server zijn en mogen zich niet in het domein bevinden.

### Gebruikersaanmelding
Nadat de vereiste onderdelen zijn geïnstalleerd, wordt u gevraagd een eenmalige aanmeldmethode voor uw gebruikers te selecteren. In de volgende tabel staan de beschikbare opties kort beschreven. Zie voor een volledige beschrijving van de aanmeldmethodes [User sign-in](active-directory-aadconnect-user-signin.md).

![Gebruikersaanmelding](./media/active-directory-aadconnect-get-started-custom/usersignin.png)

Optie voor eenmalige aanmelding | Beschrijving
------------- | -------------
Wachtwoordsynchronisatie | Gebruikers kunnen zich bij Microsoft-cloudservices, zoals Office 365, aanmelden met hetzelfde wachtwoord als ze in hun on-premises netwerk gebruiken. De wachtwoorden van gebruikers worden gesynchroniseerd naar Azure AD als een wachtwoord-hash en verificatie vindt plaats in de cloud. Zie [Password synchronization](active-directory-aadconnectsync-implement-password-synchronization.md) voor meer informatie.
Federatie met AD FS | Gebruikers kunnen zich bij Microsoft-cloudservices, zoals Office 365, aanmelden met hetzelfde wachtwoord als ze in hun on-premises netwerk gebruiken.  De gebruikers worden omgeleid naar hun on-premises AD FS-exemplaar om zich aan te melden en de verificatie vindt plaats on-premises.
Niet configureren | Geen van beide onderdelen wordt geïnstalleerd en geconfigureerd. Kies deze optie als u al een federatieserver van derden of een andere bestaande oplossing heeft.

### Verbinding maken met Azure AD
Voer op het scherm Verbinding maken met Azure AD het account en wachtwoord van een globale beheerder in. Als u op de vorige pagina **Federatie met AD FS** hebt geselecteerd, meld u dan niet aan met een account in een domein waarvoor u federatie wilt inschakelen. Het is aan te raden om een account te gebruiken uit het standaarddomein **onmicrosoft.com**, dat bij uw Azure AD-directory wordt geleverd.

Dit account wordt alleen gebruikt om een serviceaccount in Azure AD aan te maken en wordt niet gebruikt wanneer de wizard is voltooid.  
![Gebruikersaanmelding](./media/active-directory-aadconnect-get-started-custom/connectaad.png)

Als MFA voor het account van de globale beheerder is ingeschakeld, dan moet u het wachtwoord opnieuw in het aanmeldpopupvenster invoeren en de MFA-controle voltooien. De controle kan bestaan uit het invoeren van een verificatiecode of uit een telefonische oproep.  
![Gebruikersaanmelding met MFA](./media/active-directory-aadconnect-get-started-custom/connectaadmfa.png)

Voor het account van de globale beheerder kan ook [Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md) zijn ingeschakeld.

Als u een foutbericht krijgt en problemen met de connectiviteit heeft, raadpleeg dan [Troubleshoot connectivity problems](active-directory-aadconnect-troubleshoot-connectivity.md).

## Pagina's in de sectie Synchronisatie

### Verbinding maken met uw directory’s
Azure AD Connect heeft de referenties van een account met de juiste machtigingen nodig om verbinding met uw Active Directory-domeinservice te maken. U kunt het domeingedeelte in NetBios- of FQDN-indeling invoeren, dat wil zeggen FABRIKAM\syncuser of fabrikam.com\syncuser. Dit account mag een normaal gebruikersaccount zijn, omdat alleen de standaard leesmachtigingen nodig zijn. Afhankelijk van uw scenario heeft u echter mogelijk meer machtigingen nodig. Zie [Azure AD Connect Accounts and permissions](active-directory-aadconnect-accounts-permissions.md#create-the-ad-ds-account) voor meer informatie.

![Verbinding maken met Directory](./media/active-directory-aadconnect-get-started-custom/connectdir.png)

### Aanmeldconfiguratie Azure AD
Op deze pagina kunt u bekijken welke UPN-domeinen zich in de on-premises AD DS bevinden en in Azure AD geverifieerd zijn. Daarnaast kunt u op deze pagina het kenmerk configureren dat voor de userPrincipalName moet worden gebruikt.

![Niet-geverifieerde domeinen](./media/active-directory-aadconnect-get-started-custom/aadsigninconfig.png)  
Bekijk elk domein waarbij **Niet toegevoegd** en **Niet geverifieerd** staat. Zorg ervoor dat de domeinen die u gebruikt in Azure AD zijn geverifieerd. Klik op het symbool Vernieuwen wanneer u uw domeinen hebt geverifieerd. Zie voor meer informatie [add and verify the domain](active-directory-add-domain.md)

**UserPrincipalName** - Met het kenmerk userPrincipalName melden gebruikers zich aan bij Azure AD en Office 365. De gebruikte domeinen, ook wel het UPN-achtervoegsel genoemd, moeten worden geverifieerd in Azure AD voordat de gebruikers worden gesynchroniseerd. Microsoft raad aan om het standaardkenmerk userPrincipalName te houden. Als dit kenmerk niet-routeerbaar is en niet kan worden geverifieerd, dan kunt u een ander kenmerk selecteren. U kunt bijvoorbeeld e-mail selecteren als het kenmerk met het aanmeldings-id. Het gebruik van een ander kenmerk dan userPrincipalName wordt **alternatieve id** genoemd. De waarde van het alternatieve-id-kenmerk moet aan de standaard RFC822 voldoen. Een alternatieve id kan worden gebruikt met wachtwoordsynchronisatie en federatie.

>[AZURE.WARNING]
Het gebruik van een alternatieve id is niet met alle Office 365-werkbelastingen compatibel. Raadpleeg voor meer informatie [Configuring Alternate Login ID](https://technet.microsoft.com/library/dn659436.aspx).

### Domein- en OE-filters
Standaard worden alle domeinen en OE's gesynchroniseerd. Van domeinen of OE’s die u niet wilt synchroniseren naar Azure AD kunt u de selectie opheffen.  
![Domein- en OE-filters](./media/active-directory-aadconnect-get-started-custom/domainoufiltering.png) Op deze pagina in de wizard configureert u filteren op basis van een domein. Zie voor meer informatie [domain-based filtering](active-directory-aadconnectsync-configure-filtering.md#domain-based-filtering).

Sommige domeinen kunnen ook onbereikbaar zijn door firewallbeperkingen. Deze domeinen zijn standaard uitgeschakeld en hebben een waarschuwing.  
![Onbereikbare domeinen](./media/active-directory-aadconnect-get-started-custom/unreachable.png)  
Als u deze waarschuwing ziet, verzeker uzelf er dan van dat deze domeinen inderdaad onbereikbaar zijn en de waarschuwing wordt verwacht.

### Uw gebruikers een unieke id geven
Met de functie Overeenkomend in forests kunt u definiëren hoe gebruikers van uw AD DS-forests worden weergegeven in Azure AD. Een gebruiker kan ofwel slechts één keer in alle forests worden weergegeven of een combinatie van ingeschakelde en uitgeschakelde accounts hebben. De gebruiker kan in sommige forests ook worden weergegeven als een contactpersoon.

![Uniek](./media/active-directory-aadconnect-get-started-custom/unique.png)

Instelling | Beschrijving
------------- | -------------
[Gebruikers worden slechts één keer weergegeven in alle forests](active-directory-aadconnect-topologies.md#multiple-forests-separate-topologies) | Alle gebruikers worden als afzonderlijke objecten in Azure AD aangemaakt. De objecten zijn niet gekoppeld in de metaverse.
[E-mailkenmerk](active-directory-aadconnect-topologies.md#multiple-forests-full-mesh-with-optional-galsync) | Deze optie koppelt gebruikers en contactpersonen als het e-mailkenmerk in verschillende forests dezelfde waarde heeft. Gebruik deze optie wanneer uw contactpersonen met behulp van GALSync zijn aangemaakt.
[ObjectSID en msExchangeMasterAccountSID / msRTCSIP-OriginatorSid](active-directory-aadconnect-topologies.md#multiple-forests-account-resource-forest) | Deze optie koppelt een ingeschakelde gebruiker in een account-forest met een uitgeschakelde gebruiker in een bron-forest. In Exchange wordt deze configuratie een gekoppeld postvak genoemd. Deze optie kan ook worden gebruikt als u alleen Lync gebruikt en Exchange niet in de bron-forest aanwezig is.
sAMAccountName en MailNickName | Deze optie koppelt kenmerken waarbij wordt verwacht dat de aanmeldings-id van de gebruiker gevonden kan worden.
Een specifiek kenmerk | Met deze optie kunt u uw eigen kenmerk selecteren. **Beperking:** Zorg dat u een kenmerk kiest dat al in de metaverse te vinden is. Als u een aangepast kenmerk kiest dat niet in de metaverse staat, kan de wizard niet voltooid worden.

**Bronanker** - Het kenmerk sourceAnchor is onveranderbaar tijdens de levensduur van een gebruikersobject. Het is de primaire sleutel die de on-premises gebruiker aan de gebruiker in Azure AD koppelt. Omdat het kenmerk niet kan worden gewijzigd, moet u een goed kenmerk kiezen. Een goede kandidaat is objectGUID. Dit kenmerk wordt niet gewijzigd, tenzij het gebruikersaccount worden verplaatst tussen forests/domeinen. In een omgeving met meerdere forests waarin u accounts tussen forests verplaatst, moet een ander kenmerk worden gebruikt, zoals een kenmerk met de id van de werknemer. Vermijd kenmerken die wijzigen wanneer iemand trouwt of een andere taak krijgt. U kunt geen kenmerken met een @-teken gebruiken, dus het e-mailadres en userPrincipalName kunnen niet worden gebruikt. Het kenmerk is ook hoofdlettergevoelig, dus als u een object tussen forests verplaatst, zorg dan dat de hoofdletters en kleine letters hetzelfde blijven. Binaire kenmerken krijgen base64-codering, maar andere kenmerktypen blijven ongecodeerd. In scenario's met federatie en in sommige Azure AD-interfaces wordt dit kenmerk ook wel onveranderbare id genoemd. Meer informatie over het bronanker vindt u in de [ontwerpconcepten](active-directory-aadconnect-design-concepts.md#sourceAnchor).

### Synchronisatiefilters op basis van groepen
Met de functie Filteren op groep kunt u bij wijze van proef een kleine subset van objecten synchroniseren. Om deze functie te gebruiken maakt u voor dit doel een groep aan in uw on-premises Active Directory. Voeg vervolgens gebruikers en groepen toe die naar Azure AD moeten worden gesynchroniseerd als directe leden. U kunt later gebruikers aan deze groep toevoegen en eruit verwijderen om de lijst te onderhouden met objecten die in Azure AD aanwezig moeten zijn. Alle objecten die u wilt synchroniseren moet een direct lid van de groep zijn. Gebruikers, groepen, contactpersonen en computers/apparaten moeten allemaal directe leden zijn. Genest groepslidmaatschap is niet opgelost. Wanneer u een groep als lid toevoegt, wordt alleen de groep zelf toegevoegd en niet de leden ervan.

![Synchronisatiefilters](./media/active-directory-aadconnect-get-started-custom/filter2.png)

>[AZURE.WARNING]
Deze functie is alleen bedoeld ter ondersteuning van een proef-implementatie. Gebruik deze functie niet bij een volledige productie-implementatie.

Bij een volledige productie-implementatie wordt het door alle objecten die gesynchroniseerd moeten worden, moeilijk om één groep te behouden. Gebruik in plaats daarvan een van de methodes in [Configure filtering](active-directory-aadconnectsync-configure-filtering.md).

### Optionele functies
In dit scherm kunt u de optionele functies voor uw specifieke scenario's selecteren.

![Optionele functies](./media/active-directory-aadconnect-get-started-custom/optional.png)

>[AZURE.WARNING]
Als DirSync of Azure AD Sync momenteel actief zijn, activeer dan geen terugschrijffuncties in Azure AD Connect.

Optionele functies | Beschrijving
------------------- | -------------
Hybride implementatie voor Exchange | Met de functie Hybride implementatie voor Exchange kunnen on-premises en in Office 365 meerdere Exchange-postbussen naast elkaar bestaan. Azure AD Connect synchroniseert een specifieke set [kenmerken](active-directory-aadconnectsync-attributes-synchronized.md#exchange-hybrid-writeback) vanuit Azure AD naar uw on-premises directory.
Azure AD-app- en -kenmerkfilters | Door de Azure AD-app- en -kenmerkfilters in te schakelen kan de set gesynchroniseerde kenmerken worden aangepast. Door deze optie worden twee extra configuratiepagina’s aan de wizard toegevoegd. Zie voor meer informatie [Azure AD app and attribute filtering](#azure-ad-app-and-attribute-filtering).
Wachtwoordsynchronisatie | Als u federatie als de oplossing voor aanmelden hebt geselecteerd, dan kunt u deze optie inschakelen. Wachtwoordsynchronisatie kan vervolgens als een backupoptie worden gebruikt. Zie [Password synchronization](active-directory-aadconnectsync-implement-password-synchronization.md) voor meer informatie.
Wachtwoord terugschrijven | Door wachtwoord terugschrijven in te schakelen worden wachtwoordwijzigingen in Azure AD teruggeschreven naar uw on-premises directory. Zie voor meer informatie [Getting started with password management](active-directory-passwords-getting-started.md).
Groep terugschrijven | Als u de functie **Office 365-groepen** gebruikt, dan kunnen deze groepen in uw on-premises Active Directory worden weergegeven. Deze optie is alleen beschikbaar als Exchange in uw on-premises Active Directory aanwezig is. Zie voor meer informatie [Group writeback](active-directory-aadconnect-feature-preview.md#group-writeback).
Apparaat terugschrijven | Hiermee kunt u apparaatobjecten in Azure AD terugschrijven naar uw on-premises Active Directory voor scenario's voor voorwaardelijke toegang. Zie voor meer informatie [Enabling device writeback in Azure AD Connect](active-directory-aadconnect-feature-device-writeback.md).
Synchronisatie van directory-extensiekenmerken | Door synchronisatie van directory-extensiekenmerken in te schakelen worden de opgegeven kenmerken gesynchroniseerd naar Azure AD. Zie voor meer informatie [Directory extensions](active-directory-aadconnectsync-feature-directory-extensions.md).

### Azure AD-app- en -kenmerkfilters
Als u wilt beperken welke kenmerken met Azure AD moeten worden gesynchroniseerd, begin dan met te selecteren welke services u gebruikt. Als u de configuratie op deze pagina wijzigt, moet u expliciet een nieuwe service selecteren door de installatiewizard opnieuw uit te voeren.

![Optionele functies apps](./media/active-directory-aadconnect-get-started-custom/azureadapps2.png)

Op deze pagina worden alle kenmerken die worden gesynchroniseerd weergegeven op basis van de services die u in de vorige stap heeft geselecteerd. In deze lijst staan alle objecttypen die worden gesynchroniseerd. Als er een aantal specifieke kenmerken zijn die niet moeten worden gesynchroniseerd, dan kunt u de selectie van deze kenmerken opheffen.

![Optionele functies kenmerken](./media/active-directory-aadconnect-get-started-custom/azureadattributes2.png)

>[AZURE.WARNING]
Het verwijderen van kenmerken kan invloed hebben op de functionaliteit. Zie voor aanbevolen procedures en andere aanbevelingen [attributes synchronized](active-directory-aadconnectsync-attributes-synchronized.md#attributes-to-synchronize).

### Synchronisatie van directory-extensiekenmerken
U kunt het schema in Azure AD uitbreiden met aangepaste kenmerken die door uw organisatie zijn toegevoegd of met andere kenmerken in Active Directory. Om deze functie te gebruiken, selecteert u **Synchronisatie van directory-extensiekenmerken** op de pagina **optionele functies**. U kunt op deze pagina meer kenmerken selecteren om te synchroniseren.

![Uitbreidingen van de directory](./media/active-directory-aadconnect-get-started-custom/extension2.png)

Zie voor meer informatie [Directory extensions](active-directory-aadconnectsync-feature-directory-extensions.md).

## Federatie met AD FS configureren
AD FS is heel eenvoudig met een paar muisklikken met Azure AD Connect te configureren. Voorafgaand aan de configuratie is het volgende vereist.

- Een Windows Server 2012 R2-server als de federatieserver met extern beheer ingeschakeld
- Een Windows Server 2012 R2-server als de webtoepassingsproxyserver met extern beheer ingeschakeld
- Een SSL-certificaat voor de federatie-servicenaam die u wilt gebruiken (bijvoorbeeld sts.contoso.com)

### Vereisten voor de AD FS-configuratie
Zorg dat WinRM is ingeschakeld op de externe servers om uw AD FS-farm met behulp van Azure AD Connect te configureren. Neem daarnaast de vereisten voor poorten door die vermeld staan in [Table 3 - Azure AD Connect and Federation Servers/WAP](active-directory-aadconnect-ports.md#table-3---azure-ad-connect-and-federation-serverswap).

### Maak een nieuwe AD FS-farm aan of gebruik een bestaande AD FS-farm
U kunt een bestaande AD FS-farm gebruiken of u kunt ervoor kiezen een nieuwe AD FS-farm aan te maken. Als u ervoor kiest om een nieuwe aan te maken, dan dient u het SSL-certificaat op te geven. Als het SSL-certificaat met een wachtwoord is beveiligd, dan wordt u gevraagd het wachtwoord op te geven.

![AD FS-Farm](./media/active-directory-aadconnect-get-started-custom/adfs1.png)

Als u een bestaande AD FS-farm gebruikt, wordt u meteen doorgestuurd naar het scherm De vertrouwensrelatie tussen AD FS en Azure AD configureren.

### Geef de AD FS-servers op
Voer de servers in waarop u AD FS wilt installeren. U kunt naar gelang de behoeften van uw capaciteitsplanning een of meer servers toevoegen. Koppel alle servers aan Active Directory voordat u deze configuratie uitvoert. Microsoft raadt aan om voor proef-implementaties één AD FS-server te installeren. Voeg vervolgens meer servers toe en implementeer deze om aan uw schaalbehoeften te voldoen door Azure AD Connect na de eerste configuratie opnieuw uit te voeren.

>[AZURE.NOTE]
Zorg ervoor dat alle servers aan een AD-domein zijn gekoppeld voordat u deze configuratie uitvoert.

![AD FS-Servers](./media/active-directory-aadconnect-get-started-custom/adfs2.png)

### Geef de webtoepassingsproxyservers op
Voer de servers in die u als uw webtoepassingsproxyservers wilt gebruiken. De webtoepassingsproxyserver wordt in uw DMS (extranetgericht) geïmplementeerd en ondersteunt verificatieaanvragen van het extranet. U kunt naar gelang de behoeften van uw capaciteitsplanning een of meer servers toevoegen. Microsoft raadt aan om voor proef-implementaties één webtoepassingsproxyserver te installeren. Voeg vervolgens meer servers toe en implementeer deze om aan uw schaalbehoeften te voldoen door Azure AD Connect na de eerste configuratie opnieuw uit te voeren. Het is aan te raden een gelijk aantal proxyservers te hebben om aan de verificatie van het intranet te voldoen.

>[AZURE.NOTE]
<li> Als het account dat u gebruikt geen lokale beheerder op de AD FS-servers is, wordt u gevraagd om beheerreferenties.</li>
<li> Zorg ervoor dat er een HTTP/HTTPS-verbinding tussen de Azure AD Connect-server en de webtoepassingsproxyserver is voordat u deze stap uitvoert.</li>
<li> Zorg ervoor dat er een HTTP/HTTPS-verbinding tussen de webtoepassingsserver en de AD FS-server is, zodat verificatieaanvragen door kunnen stromen.</li>

![Web-app](./media/active-directory-aadconnect-get-started-custom/adfs3.png)

U wordt gevraagd referenties op te geven zodat de webtoepassingsserver een beveiligde verbinding met de AD FS-server kan maken. Deze referenties moeten van een lokale beheerder op de AD FS-server zijn.

![Proxy](./media/active-directory-aadconnect-get-started-custom/adfs4.png)

### Geef het serviceaccount voor de AD FS-service op
De AD FS-service vereist een domeinserviceaccount om gebruikers te verifiëren en gebruikersinformatie in Active Directory op te zoeken. De service kan twee soorten serviceaccounts ondersteunen:

- **Beheerd serviceaccount voor groepen** - Geïntroduceerd in Active Directory-domainservices met WindowsServer 2012. Dit type account biedt services, zoals AD FS, één account zonder dat het accountwachtwoord regelmatig bijgewerkt hoeft te worden. Gebruik deze optie als u al Windows Server 2012-domeincontrollers hebt in het domein waarbij uw AD FS-servers horen.
- **Domeingebruikersaccount** - Voor dit type account moet u een wachtwoord opgeven en het wachtwoord regelmatig bijwerken wanneer het wachtwoord wordt gewijzigd of verloopt. Gebruik deze optie alleen als u geen Windows Server 2012-domeincontrollers heeft in het domein waarbij uw AD FS-servers horen.

Als u Beheerd serviceaccount voor groepen heeft geselecteerd en deze functie nog nooit in Active Directory is gebruikt, wordt u gevraagd om referenties van de ondernemingsbeheerder. Deze referenties worden gebruikt om de sleutelopslagplaats te beginnen en de functie in Active Directory in te schakelen.

![AD FS-serviceaccount](./media/active-directory-aadconnect-get-started-custom/adfs5.png)

### Selecteer het Azure AD-domein dat u wilt federeren
Deze configuratie wordt gebruikt voor het instellen van de federatieverbinding tussen AD FS en Azure AD. AD FS wordt hiermee geconfigureerd om beveiligingstokens aan Azure AD te verstrekken en Azure AD wordt geconfigureerd om de tokens van deze specifieke AD FS-instantie te vertrouwen. Op deze pagina kunt u tijdens de eerste installatie slechts één domein configureren. U kunt later meer domeinen configureren door Azure AD Connect nogmaals uit te voeren.

![Azure AD-domein](./media/active-directory-aadconnect-get-started-custom/adfs6.png)

### Controleer het Azure AD-domein dat voor federatie is geselecteerd
Wanneer u het te federeren domein selecteert, geeft Azure AD Connect u de informatie die u nodig heeft om een niet-geverifieerd domein te verifiëren. Zie [Add and verify the domain](active-directory-add-domain.md) om uit te vinden hoe deze informatie gebruikt moet worden.

![Azure AD-domein](./media/active-directory-aadconnect-get-started-custom/verifyfeddomain.png)

>[AZURE.NOTE]
AD Connect probeert het domein tijdens de configuratiefase te verifiëren. Als u doorgaat met de configuratie zonder de vereiste DNS-records toe te voegen, dan kan de wizard de configuratie niet voltooien.

## Configureer en verifieer pagina 's
De configuratie wordt op deze pagina uitgevoerd.

>[AZURE.NOTE]
Als u de federatie heeft geconfigureerd, zorg dan dat u, voordat u doorgaat met de installatie, de [Naamomzetting voor federatieservers](active-directory-aadconnect-prerequisites.md#name-resolution-for-federation-servers) heeft geconfigureerd.

![Klaar om te configureren](./media/active-directory-aadconnect-get-started-custom/readytoconfigure2.png)

### Faseringsmodus
Het is mogelijk om een nieuwe synchronisatieserver parallel met de faseringsmodus in te stellen. Het is alleen ondersteund als u één synchronisatieserver naar één map in de cloud laat exporteren. Als u echter vanaf een andere server wilt verplaatsen, bijvoorbeeld een server met DirSync, dan kunt u Azure AD Connect in de faseringsmodus inschakelen. Wanneer dit is ingeschakeld, importeert en synchroniseert de synchronisatie-engine zoals gewoonlijk, maar hij exporteert niets naar Azure AD of AD. De functies Wachtwoordsynchronisatie en Wachtwoord terugschrijven zijn uitgeschakeld in de faseringsmodus.

![Faseringsmodus](./media/active-directory-aadconnect-get-started-custom/stagingmode.png)

In de faseringsmodus kunt u vereiste wijzigingen in de synchronisatie-engine aanbrengen en controleren wat geëxporteerd gaat worden. Voer de installatiewizard opnieuw uit en schakel de faseringsmodus uit wanneer de configuratie er goed uitziet. De gegevens zijn nu van deze server naar Azure AD geëxporteerd. Schakel de andere server op hetzelfde moment uit, zodat slechts één server actief aan het exporteren is.

Zie voor meer informatie [Staging mode](active-directory-aadconnectsync-operations.md#staging-mode).

### Controleer uw federatieconfiguratie
Azure AD Connect verifieert de DNS-instellingen voor u wanneer u op Verifiëren klikt.

![Voltooien](./media/active-directory-aadconnect-get-started-custom/completed.png)

![Verifiëren](./media/active-directory-aadconnect-get-started-custom/adfs7.png)

Voer daarnaast de volgende verificatiestappen uit:

- Controleer of u zich kunt aanmelden met een browser op een computer op het intranet die aan het domein is gekoppeld: Maak verbinding met https://myapps.microsoft.com en controleer de aanmelding met uw aangemelde account. Het ingebouwde beheerdersaccount van AD DS wordt niet gesynchroniseerd en kan niet worden gebruikt voor verificatie.
- Controleer of u zich kunt aanmelden met een apparaat vanaf het extranet. Maak op een computer thuis of op een mobiel apparaat verbinding met https://myapps.microsoft.com en voer uw referenties in.
- Aanmelding uitgebreide client controleren. Maak verbinding met https://testconnectivity.microsoft.com, kies het tabblad **Office 365** en vervolgens de **Office 365 Test Eenmalige aanmelding**.

## Volgende stappen
Nadat de installatie is voltooid, dient u zich af te melden en weer aan te melden bij Windows voordat u de Synchronization Service Manager of Synchronization Rule Editor gaat gebruiken.

Nu u Azure AD Connect geïnstalleerd hebt kunt u [de installatie verifiëren en licenties toewijzen](active-directory-aadconnect-whats-next.md).

Meer informatie over deze functies, die tijdens de installatie zijn ingeschakeld: [Prevent accidental deletes](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) en [Azure AD Connect Health](active-directory-aadconnect-health-sync.md).

Meer informatie over deze algemene onderwerpen: [scheduler and how to trigger sync](active-directory-aadconnectsync-feature-scheduler.md).

Meer informatie over [Integrating your on-premises identities with Azure Active Directory](active-directory-aadconnect.md).

## Verwante documentatie

Onderwerp |  
--------- | ---------
Overzicht Azure AD Connect | [Uw on-premises identiteiten integreren met Azure Active Directory](active-directory-aadconnect.md)
Installeren met de snelle instellingen | [Snelle installatie van Azure AD Connect](active-directory-aadconnect-get-started-express.md)
Upgrade van DirSync | [Upgrade van Azure AD-synchronisatiehulpprogramma (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md)
Accounts die worden gebruikt voor installatie | [Meer over accounts en machtigingen van Azure AD Connect](active-directory-aadconnect-accounts-permissions.md)



<!--HONumber=Jun16_HO2-->


