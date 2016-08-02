<properties
    pageTitle="Aan de slag met Azure Sleutelkluis | Microsoft Azure"
    description="Gebruik deze handleiding om aan de slag te gaan met Azure Sleutelkluis en een geharde container in Azure te maken om cryptografiesleutels en geheimen op te slaan in Azure."
    services="key-vault"
    documentationCenter=""
    authors="cabailey"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="05/06/2016"
    ms.author="cabailey"/>

# Aan de slag met Azure Sleutelkluis #
Azure Sleutelkluis is beschikbaar in de meeste regio's. Zie de pagina [Prijzen van Sleutelkluis](https://azure.microsoft.com/pricing/details/key-vault/) voor meer informatie.

## Inleiding  
Gebruik deze handleiding om aan de slag te gaan met Azure Sleutelkluis en een geharde container (een kluis) in Azure te maken om cryptografiesleutels en geheimen op te slaan in Azure. In het artikel wordt uitgelegd hoe u Azure PowerShell gebruikt om een kluis te maken die een wachtwoord of sleutel bevat die u vervolgens kunt gebruiken voor een Azure-toepassing. Vervolgens wordt uitgelegd hoe een toepassing de sleutel of het wachtwoord kan gebruiken.

**Geschatte duur:** 20 minuten

>[AZURE.NOTE]  Deze zelfstudie bevat geen instructies voor het schrijven van de Azure-toepassing die in een van de stappen wordt gebruikt, namelijk hoe u een toepassing toestemming geeft voor het gebruik van een sleutel of geheim in de sleutelkluis.
>
>Het is momenteel niet mogelijk om Azure Sleutelkluis in Azure Portal te configureren. Gebruik in plaats daarvan deze instructies voor Azure Powershell. Zie [deze equivalente zelfstudie](key-vault-manage-with-cli.md) voor instructies om een platformonafhankelijke opdrachtregelinterface te maken.

Zie [Wat is Azure Sleutelkluis?](key-vault-whatis.md) voor algemene informatie over Azure Sleutelkluis.

## Vereisten

U hebt het volgende nodig om deze zelfstudie te voltooien:

- Een abonnement op Microsoft Azure Als u nog geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/pricing/free-trial/).
- Azure PowerShell, **minimaal versie 1.1.0 of hoger**. Zie [Azure PowerShell installeren en configureren](../powershell-install-configure.md) om Azure PowerShell te installeren en te koppelen aan uw Azure-abonnement. Als u Azure PowerShell al hebt geïnstalleerd, maar niet weet welke versie u hebt, typt u `(Get-Module azure -ListAvailable).Version` in de Azure PowerShell-console. Ook wanneer u een van de versies van Azure PowerShell versie 0.9.1 tot en met 0.9.8 hebt geïnstalleerd, kunt u deze zelfstudie gebruiken en zij er slechts enkele kleine wijzigingen van toepassing. U moet bijvoorbeeld de opdracht `Switch-AzureMode AzureResourceManager` gebruiken en bepaalde Azure Sleutelkluis-opdrachten zijn gewijzigd. Zie [Azure Sleutelkluis-cmdlets](https://msdn.microsoft.com/library/azure/dn868052\(v=azure.98\).aspx) voor een overzicht van de Sleutelkluis-cmdlets voor versie 0.9.1 tot en met versie 0.9.8. 
- Een toepassing die wordt geconfigureerd voor het gebruik van de sleutel of het wachtwoord dat u in deze zelfstudie maakt. Er is een voorbeeldtoepassing beschikbaar in het [Microsoft Downloadcentrum](http://www.microsoft.com/en-us/download/details.aspx?id=45343). Zie het bijbehorende Leesmij-bestand voor instructies.


Deze zelfstudie is ontworpen voor beginnende gebruikers van Azure PowerShell, maar er wordt vanuit gegaan dat u bekend bent met de basisconcepten, zoals modules, cmdlets en sessies. Zie [Aan de slag met Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx) voor meer informatie.

Voor gedetailleerde hulp bij een van de cmdlets in deze zelfstudie kunt u de cmdlet **Get-Help** gebruiken.

    Get-Help <cmdlet-name> -Detailed

Als u bijvoorbeeld ondersteuning voor de cmdlet **Login-AzureRmAccount** wilt aanvragen, typt u:

    Get-Help Login-AzureRmAccount -Detailed

U kun ook de volgende zelfstudies lezen om vertrouwd te raken met Azure Resource Manager in Azure PowerShell:

- [Azure PowerShell installeren en configureren](../powershell-install-configure.md)
- [Azure PowerShell gebruiken met Resource Manager](../powershell-azure-resource-manager.md)


## <a id="connect"></a>Verbinding maken met uw abonnementen ##

Start een Azure PowerShell-sessie en gebruik de volgende opdracht om u aan te melden bij uw Azure-account:  

    Login-AzureRmAccount 

Houd er rekening mee dat wanneer u een specifieke instantie van Azure gebruikt, bijvoorbeeld Azure Government, u de parameter -Environment met deze opdracht moet gebruiken. Bijvoorbeeld: `Login-AzureRmAccount –Environment (Get-AzureRmEnvironment –Name AzureUSGovernment)`

Voer in het pop-upvenster in de browser uw gebruikersnaam en wachtwoord voor uw Azure-account in. Azure PowerShell haalt alle abonnementen op die zijn gekoppeld aan dit account en gebruikt standaard het eerste abonnement.

Als u meerdere abonnementen hebt en u een specifiek abonnement voor Azure Sleutelkluis wilt gebruiken, typt u het volgende om de abonnementen voor uw account weer te geven:

    Get-AzureRmSubscription

Typ vervolgens het volgende om het abonnement op te geven dat u wilt gebruiken:

    Set-AzureRmContext -SubscriptionId <subscription ID>

Zie [Azure PowerShell installeren en configureren](../powershell-install-configure.md) voor meer informatie over het configureren van Azure PowerShell.


## <a id="resource"></a>Een nieuwe resourcegroep maken ##

Als u Azure Resource Manager gebruikt, worden alle gerelateerde resources gemaakt binnen een resourcegroep. In deze zelfstudie maken we de resourcegroep **ContosoResourceGroup**:

    New-AzureRmResourceGroup –Name 'ContosoResourceGroup' –Location 'East Asia'


## <a id="vault"></a>Een sleutelkluis maken ##

Gebruik de cmdlet [New-AzureRmKeyVault](https://msdn.microsoft.com/library/azure/mt603736.aspx) om een sleutelkluis te maken. Deze cmdlet heeft drie verplichte parameters: een **resourcegroepnaam**, een **sleutelkluisnaam** en de **geografische locatie**.

Als u bijvoorbeeld de kluisnaam **ContosoKeyVault**, de resourcegroepnaam **ContosoResourceGroup** en de locatie **Oost-Azië** wilt gebruiken, typt u het volgende:

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

De uitvoer van deze cmdlet toont u de eigenschappen van de sleutelkluis die u zojuist hebt gemaakt. De twee belangrijkste eigenschappen zijn:

- **Vault Name**: in het voorbeeld is dit **ContosoKeyVault**. U gebruikt deze naam voor andere Sleutelkluis-cmdlets.
- **Vault URI**: in het voorbeeld is dit https://contosokeyvault.vault.azure.net/. Toepassingen die via de REST API gebruikmaken van uw kluis, moeten deze URI gebruiken.

Uw Azure-account is nu gemachtigd om alle bewerkingen op deze sleutelkluis uit te voeren. Tot dusver is er nog niemand anders gemachtigd.

>[AZURE.NOTE]  Als de foutmelding **Het abonnement is niet geregistreerd voor het gebruik van de naamruimte 'Microsoft.KeyVault'** wordt weergegeven wanneer u de nieuwe sleutelkluis maakt, voert u `Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"` uit en voert u vervolgens opdracht New-AzureRmKeyVault opnieuw uit. Zie [Register-AzureRmProvider](https://msdn.microsoft.com/library/mt679020.aspx) voor meer informatie.
>

## <a id="add"></a>Een sleutel of geheim toevoegen aan de sleutelkluis ##

Als u wilt dat Azure Sleutelkluis een softwarematig beveiligde sleutel voor u maakt, gebruikt u de cmdlet [Add-AzureKeyVaultKey](https://msdn.microsoft.com/library/azure/dn868048.aspx) en typt u het volgende:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -Destination 'Software'

Als u al een bestaande softwarematig beveiligde sleutel in een PFX-bestand met de naam softkey.pfx hebt opgeslagen naar station C:\ en u dat bestand wilt uploaden naar Azure Sleutelkluis, typt u het volgende om de variabele **securepfxpwd** voor het wachtwoord **123** voor PFX-bestand in te stellen:

    $securepfxpwd = ConvertTo-SecureString –String '123' –AsPlainText –Force

Typ vervolgens het volgende om de sleutel te importeren uit het PFX-bestand, dat de sleutel beveiligt met software in de Sleutelkluis-service:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd


U kunt nu de URI van de sleutel gebruiken om te verwijzen naar de sleutel die u hebt gemaakt of geüpload naar Azure Sleutelkluis. Gebruik **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** om altijd de huidige versie op te halen en gebruik **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** om deze specifieke versie op te halen.  

Als u de URI voor deze sleutel wilt weergeven, typt u het volgende:

    $Key.key.kid

Als u een geheim aan de kluis wilt toevoegen, namelijk het wachtwoord met de naam SQLPassword en de waarde van Pa$$w0rd voor Azure Sleutelkluis, converteert u de waarde Pa$$w0rd eerst naar een beveiligde tekenreeks door het volgende te typen:

    $secretvalue = ConvertTo-SecureString 'Pa$$w0rd' -AsPlainText -Force

Typ vervolgens het volgende:

    $secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword' -SecretValue $secretvalue

Als u nu naar het wachtwoord wilt verwijzen dat u hebt toegevoegd aan Azure Sleutelkluis, kunt u de URI van het wachtwoord gebruiken. Gebruik **https://ContosoVault.vault.azure.net/secrets/SQLPassword** om altijd de huidige versie op te halen en gebruik **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** om deze specifieke versie op te halen.

Als u de URI voor dit geheim wilt weergeven, typt u het volgende:

    $secret.Id

Laten we de sleutel of het geheim eens bekijken dat u net hebt gemaakt:

- Als u de sleutel wilt weergeven, typt: `Get-AzureKeyVaultKey –VaultName 'ContosoKeyVault'`
- Als u het geheim wilt weergeven, typt u: `Get-AzureKeyVaultSecret –VaultName 'ContosoKeyVault'`

U sleutelkluis en sleutel of geheim kunnen nu door toepassingen worden gebruikt. U moet toepassingen autoriseren om ze te gebruiken.  

## <a id="register"></a>Een toepassing registreren met Azure Active Directory ##

Deze stap wordt doorgaans uitgevoerd door een ontwikkelaar op een afzonderlijke computer. De stap is niet specifiek voor Azure Sleutelkluis maar opgenomen voor de volledigheid.


>[AZURE.IMPORTANT] Uw account, de kluis en de toepassing die u in deze stap registreert moet allemaal in dezelfde Azure-directory bevinden om de zelfstudie te kunnen voltooien.

Toepassingen die gebruikmaken van een sleutelkluis moeten een token van Azure Active Directory gebruiken om zich te verifiëren. Hiervoor moet de eigenaar van de toepassing deze eerst registreren bij Azure Active Directory. Aan het eind van het registratieproces ontvangt de eigenaar de volgende waarden:


- Een **toepassings-ID** (ook wel een client-id genoemd) en een **verificatiesleutel** (ook wel het gedeelde geheim genoemd). De toepassing moet beide waarden aan Azure Active Directory verstrekken om een token te verkrijgen. Hoe de toepassing is geconfigureerd om dit te doen, is afhankelijk van de toepassing. In de voorbeeldtoepassing voor Sleutelkluis configureert de eigenaar van de toepassing deze waarden in het bestand app.config.

De toepassing registreren in Azure Active Directory:

1. Meld u aan bij de klassieke Azure-portal.
2. Klik aan de linkerkant op **Active Directory** en selecteer vervolgens de directory waarin u de toepassing wilt registreren. <br> <br> **Opmerking:** u moet de directory selecteren die ook het Azure-abonnement bevat waarmee u de sleutelkluis hebt gemaakt. Als u niet weet welke directory dit is, klikt u op **Instellingen**, identificeert u het abonnement waarmee u de sleutelkluis hebt gemaakt en noteert u de directorynaam die in de laatste kolom wordt weergegeven.

3. Klik op **TOEPASSINGEN**. Als er geen apps aan uw directory zijn toegevoegd, wordt op deze pagina alleen de koppeling **Een app toevoegen** weergegeven. Klik op de koppeling. U kunt eventueel ook op **TOEVOEGEN** op de opdrachtbalk klikken.
4.  In de wizard **TOEPASSING TOEVOEGEN** klikt u op de pagina **What do you want to do?** (Wat wilt u doen?) op **Add an application my organization is developing** (Een toepassing toevoegen die door mijn organisatie wordt ontwikkeld).
5.  Geef op de pagina **Tell us about your application** (Vertel ons iets over uw toepassing) een naam voor uw toepassing op en selecteer **WEBTOEPASSING EN/OF WEB-API** (de standaardinstelling). Klik op het pictogram **Volgende**.
6.  Geef op de pagina **App-eigenschappen** de **AANMELDINGS-URL** en **APP ID URI** (URI VAN DE APP-ID) voor uw webtoepassing op. Als uw toepassing deze waarden niet heeft, kunt u voor deze stap zelf iets verzinnen (u kunt voor beide vakken bijvoorbeeld http://test1.contoso.com opgeven). Het maakt niet uit of deze sites bestaan. Belangrijk is dat voor elke toepassing in uw directory een andere URI van de app-id wordt gebruikt. De directory gebruikt deze tekenreeks om uw app te identificeren.
7.  Klik op de pictogram **Voltooien** om de wijzigingen in de wizard op te slaan.
8.  Klik op de pagina **Snel starten** op **CONFIGUREREN**.
9.  Schuif naar de sectie **sleutels**, selecteer de duur en klik vervolgens op **OPSLAAN**. De pagina wordt vernieuwd en bevat nu een sleutelwaarde. U moet nu uw toepassing configureren met deze sleutelwaarde en de waarde **CLIENT-ID**. (Instructies voor deze configuratie zijn toepassingsspecifiek.)
10. Kopieer de client-id-waarde op deze pagina. Deze waarde gebruikt in de volgende stap om machtigingen voor uw kluis in te stellen.

## <a id="authorize"></a>De toepassing toestemming geven om de sleutel of het geheim te gebruiken ##

Als u de toepassing toegang wilt verlenen toegang tot de sleutel of het geheim in de kluis, gebruikt u de cmdlet  [Set-AzureRmKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/mt603625.aspx).

Als bijvoorbeeld uw kluisnaam **ContosoKeyVault** is en de toepassing die u wilt hosten de client-id 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed heeft, kunt u het volgende uitvoeren om de toepassing toestemming te geven om ondertekenings- en versleutelingsbewerking met de sleutels in uw kluis uit te voeren:


    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign

Als u dezelfde toepassing wilt autoriseren voor het lezen van geheimen in uw kluis, voert u de volgende opdracht uit:


    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToSecrets Get

## <a id="HSM"></a>Als u een Hardware Security Module (HSM) wilt gebruiken ##

Voor de zekerheid kunt u sleutels in HSM's (Hardware Security Module) importeren of genereren die de HSM-grens nooit verlaten. De HSM's zijn FIPS 140-2 Level 2-gevalideerde modules. Als deze vereiste niet van toepassing is op u, kunt u deze sectie overslaan om naar [De sleutelkluis en de bijbehorende sleutels en geheimen verwijderen](#delete) te gaan.

Als u deze met HSM beveiligde sleutels wilt maken, moet u beschikken over een [kluisabonnement dat ondersteuning biedt voor sleutels die zijn beveiligd met HSM](https://azure.microsoft.com/pricing/free-trial/).  Deze functionaliteit is niet beschikbaar voor Azure China.


Wanneer u de kluis maakt, voegt u de parameter **- SKU** toe:


    New-AzureRmKeyVault -VaultName 'ContosoKeyVaultHSM' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -SKU 'Premium'



U kunt softwarematige beveiligde sleutels (zoals hiervoor) en met HSM beveiligde sleutels toevoegen aan deze kluis. Als u een sleutel wilt maken die is beveiligd met HSM, stelt u de parameter **-Destination** in op HSM:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -Destination 'HSM'

U kunt de volgende opdracht gebruiken om een sleutel uit een PFX-bestand op uw computer te importeren. Met deze opdracht wordt de sleutel geïmporteerd in HSM's in de Sleutelkluis-service:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd -Destination 'HSM'


Met de volgende opdracht wordt een BYOK-pakket (Bring Your Own Key) geïmporteerd. Hiermee kunt u de sleutel in uw lokale HSM genereren en overdragen naar HSM's in de Sleutelkluis-service, zonder dat de sleutel de HSM-grens verlaat:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\ITByok.byok' -Destination 'HSM'

Zie [Met HSM beveiligde sleutels voor Azure Sleutelkluis genereren en overdragen](key-vault-hsm-protected-keys.md) voor gedetailleerde instructies om dit BYOK-pakket te genereren.

## <a id="delete"></a>De sleutelkluis en de bijbehorende sleutels en geheimen verwijderen ##

Als u de sleutelkluis en de sleutel of het geheim in de kluis niet meer nodig hebt, kunt u de sleutelkluis verwijderen met de cmdlet [verwijderen AzureRmKeyVault](https://msdn.microsoft.com/library/azure/mt619485.aspx):

    Remove-AzureRmKeyVault -VaultName 'ContosoKeyVault'

U kunt ook een volledige Azure-resourcegroep verwijderen. Deze bevat de sleutelkluis en alle andere resources die u hebt opgenomen in de groep:

    Remove-AzureRmResourceGroup -ResourceGroupName 'ContosoResourceGroup'


## <a id="other"></a>Overige Azure PowerShell-cmdlets ##

Overige opdrachten die handig kunnen zijn voor het beheren van Azure Sleutelkluis:

- `$Keys = Get-AzureKeyVaultKey -VaultName 'ContosoKeyVault'`: met deze opdracht worden alle sleutels en geselecteerde eigenschappen weergegeven in een tabel.
- `$Keys[0]`: met deze opdracht geeft u een volledige lijst met eigenschappen voor de opgegeven sleutel weer.
- `Get-AzureKeyVaultSecret`: met deze opdracht worden alle geheime namen en geselecteerde eigenschappen weergegeven in een tabel.
- `Remove-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey'`: voorbeeld hoe u een specifieke sleutel verwijdert.
- `Remove-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword'`: voorbeeld hoe u een specifiek geheim verwijdert.


## <a id="next"></a>Volgende stappen ##

Zie [Azure Sleutelkluis in een webtoepassing gebruiken](key-vault-use-from-web-application.md) voor een vervolgzelfstudie over het gebruik van Azure Sleutelkluis in een webtoepassing.

Zie [Logboekregistratie van Azure Sleutelkluis](key-vault-logging.md) om te zien hoe uw sleutelkluis wordt gebruikt.

Zie [Cmdlets voor Azure Sleutelkluis](https://msdn.microsoft.com/library/azure/dn868052.aspx) voor een lijst met de nieuwste Azure PowerShell 1.0-cmdlets voor Azure Sleutelkluis. 
 

Zie de [Ontwikkelaarshandleiding voor Azure Sleutelkluis](key-vault-developers-guide.md) voor het programmeren van verwijzingen.



<!--HONumber=Jun16_HO2-->


