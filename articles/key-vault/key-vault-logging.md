<properties
    pageTitle="Logboekregistratie van Azure Sleutelkluis | Microsoft Azure"
    description="Deze zelfstudie helpt u op weg met de logboekregistratie van Azure Sleutelkluis."
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

# Logboekregistratie van Azure Sleutelkluis #
Azure Sleutelkluis is beschikbaar in de meeste regio's. Zie de pagina [Prijzen van Sleutelkluis](https://azure.microsoft.com/pricing/details/key-vault/) voor meer informatie.

## Inleiding  
Nadat u een of meer sleutelkluizen hebt gemaakt, wilt u wellicht controleren hoe en wanneer uw sleutelkluizen toegankelijk zijn en voor wie. U kunt dit doen door logboekregistratie in te schakelen voor Sleutelkluis. Hierbij wordt de informatie opgeslagen in een Azure-opslagaccount dat u opgeeft. Er wordt automatisch een nieuwe container met de naam **insights-logboeken-auditevent** gemaakt voor het opgegeven opslagaccount en u kunt hetzelfde opslagaccount gebruiken voor het verzamelen van logboeken voor meerdere sleutelkluizen.

U kunt uw logboekgegevens maximaal tien minuten nadat de sleutelkluisbewerking is uitgevoerd, bekijken. In de meeste gevallen gaat het echter veel sneller.  Het is aan u om uw logboeken in uw opslagaccount te beheren:

- Gebruik standaardmethoden van Azure voor toegangsbeheer om uw logboeken te beveiligen door het aantal gebruikers te beperken dat toegang heeft tot de logboeken.
- Verwijder de logboeken die u niet meer in uw opslagaccount wilt bewaren. 

Deze zelfstudie helpt u op weg met de logboekregistratie van Azure Sleutelkluis, het maken van uw opslagaccount, het inschakelen van logboekregistratie en het interpreteren van de logboekgegevens die worden verzameld.  


>[AZURE.NOTE]  Deze zelfstudie bevat geen instructies voor het maken van sleutelkluizen, sleutels of geheimen. Zie [Aan de slag met Azure Sleutelkluis](key-vault-get-started.md) voor meer informatie. Zie [deze equivalente zelfstudie](key-vault-manage-with-cli.md) voor instructies voor het maken van een platformonafhankelijke opdrachtregelinterface.
>
>Het is momenteel niet mogelijk om Azure Sleutelkluis in Azure Portal te configureren. Gebruik in plaats daarvan deze instructies voor Azure PowerShell.

Zie [Wat is Azure Sleutelkluis?](key-vault-whatis.md) voor algemene informatie over Azure Sleutelkluis.

## Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

- Een bestaande sleutelkluis die u hebt gebruikt.  
- Azure PowerShell, **versie 1.0.1 of hoger**. Zie [Azure PowerShell installeren en configureren](../powershell-install-configure.md) om Azure PowerShell te installeren en te koppelen aan uw Azure-abonnement. Als u Azure PowerShell al hebt geïnstalleerd, maar niet weet welke versie u hebt, typt u `(Get-Module azure -ListAvailable).Version` in de Azure PowerShell-console.  
- Voldoende opslagruimte op Azure voor uw Sleutelkluis-logboeken.


## <a id="connect"></a>Verbinding maken met uw abonnementen ##

Start een Azure PowerShell-sessie en meld u aan bij uw Azure-account met de volgende opdracht:  

    Login-AzureRmAccount 

Voer in het pop-upvenster in de browser uw gebruikersnaam en wachtwoord voor uw Azure-account in. Azure PowerShell haalt alle abonnementen op die zijn gekoppeld aan dit account en gebruikt standaard het eerste.

Als u meerdere abonnementen hebt, moet u wellicht specifiek opgeven welk abonnement is gebruikt voor het maken van uw Azure Sleutelkluis. Typ het volgende als u de abonnementen voor uw account wilt zien:

    Get-AzureRmSubscription

Geef vervolgens op welk abonnement is gekoppeld aan de sleutelkluis waarvoor u logboekregistratie wilt inschakelen. Typ:

    Set-AzureRmContext -SubscriptionId <subscription ID>

Zie [Azure PowerShell installeren en configureren](../powershell-install-configure.md) voor meer informatie over het configureren van Azure PowerShell.


## <a id="storage"></a>Een nieuw opslagaccount voor uw logboeken maken ##

Hoewel u een bestaand opslagaccount voor uw logboeken kunt gebruiken, maken we hier een nieuw opslagaccount, speciaal voor Sleutelkluis-logboeken. Voor het gemak slaan we de details op in een variabele met de naam **sa**. Deze moeten we later namelijk opgeven. 

En om het ons nog gemakkelijker te maken, gebruiken we de resourcegroep die de sleutelkluis bevat. In de [zelfstudie Aan de slag](key-vault-get-started.md) heeft deze resourcegroep de naam **ContosoResourceGroup** en we gebruiken hier ook de locatie Oost-Azië. Vervang deze waarden voor uzelf, indien van toepassing:

    $sa = New-AzureRmStorageAccount -ResourceGroupName ContosoResourceGroup -Name ContosoKeyVaultLogs -Type Standard_LRS -Location 'East Asia'


>[AZURE.NOTE]  Als u een bestaand opslagaccount gebruikt, moet hiervoor hetzelfde abonnement worden gebruikt als voor uw sleutelkluis. Ook moet het opslagaccount gebruikmaken van het Resource Manager-implementatiemodel, niet van het klassieke implementatiemodel.

## <a id="identify"></a>De sleutelkluis voor uw logboeken identificeren ##

In de zelfstudie Aan de slag is de naam van de sleutelkluis **ContosoKeyVault**. We gaan deze naam ook hier gebruiken en de details in een variabele met de naam **kv** opslaan:

    $kv = Get-AzureRmKeyVault -VaultName 'ContosoKeyVault'


## <a id="enable"></a>Logboekregistratie inschakelen ##

Als u logboekregistratie wilt inschakelen voor Sleutelkluis, gebruikt u de cmdlet Set-AzureRmDiagnosticSetting, samen met de variabelen die we voor ons nieuwe opslagaccount en onze sleutelkluis hebben gemaakt. We moeten de markering **-Ingeschakeld** instellen op **$true** en de categorie instellen op AuditEvent (de enige categorie voor logboekregistratie van Sleutelkluis):

   
    Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent


De uitvoer hiervoor bevat het volgende:

**Logboeken**

**Ingeschakeld: True**

**Categorie: AuditEvent**

Hiermee bevestigt u dat logboekregistratie nu is ingeschakeld voor uw sleutelkluis, waarbij de informatie wordt opgeslagen in uw opslagaccount.

Wat wordt in het logboek vastgelegd?

- Alle geverifieerde REST-API-aanvragen worden vastgelegd, ook mislukte aanvragen als gevolg van toegangsmachtigingen, systeemfouten of ongeldige aanvragen.
- Bewerkingen voor de sleutelkluis zelf, zoals het maken, verwijderen en instellen van het toegangsbeleid voor sleutelkluizen, en het bijwerken van de kenmerken van sleutelkluizen, zoals tags.
- Bewerkingen voor sleutels en geheimen in de sleutelkluis, zoals het maken, wijzigen of verwijderen van deze sleutels of geheimen; bewerkingen zoals het ondertekenen, controleren, versleutelen, ontsleutelen, inpakken en uitpakken van sleutels, het ophalen van geheimen en het weergeven van sleutels, geheimen en hun versies.
- Niet-geverifieerde aanvragen die in een 401-respons resulteren. Dit zijn bijvoorbeeld aanvragen die geen Bearer-token hebben, ongeldige of verlopen aanvragen of aanvragen met een ongeldig token.  


## <a id="access"></a>Toegang tot uw logboeken ##

Sleutelkluis-logboeken worden opgeslagen in de container **insights-logs-auditevent** in het opslagaccount dat u hebt opgegeven. Als u alle blobs in deze container wilt weergeven, typt u:

    Get-AzureStorageBlob -Container 'insights-logs-auditevent' -Context $sa.Context

De uitvoer ziet er ongeveer als volgt uit:

**Container-URI: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent**


**Naam**

**----**

**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json**

**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json**

**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json****
 

Zoals u in deze uitvoer kunt zien, hebben de blobs de volgende naamgevingsregels: **resourceId =<ARM resource ID>/y =<year>/m =<month>/d =<day of month>/h =<hour>/m =<minute>/filename.json**

De datum- en tijdwaarden maken gebruik van UTC.

Aangezien hetzelfde opslagaccount kan worden gebruikt voor het verzamelen van logboeken voor meerdere resources, is de volledige resource-id in de blobnaam zeer handig voor toegang tot of het downloaden van alleen de blobs die u nodig hebt. Maar eerst laten we zien hoe u alle blobs kunt downloaden. 

Maak eerst een map waarin u de blobs wilt downloaden. Bijvoorbeeld:

    New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force

Haal vervolgens een lijst met alle blobs op:  

    $blobs = Get-AzureStorageBlob -Container $container -Context $sa.Context

Sluis deze lijst via 'Get-AzureStorageBlobContent' door om de blobs in de doelmap te downloaden:

    $blobs | Get-AzureStorageBlobContent -Destination 'C:\Users\username\ContosoKeyVaultLogs'

Wanneer u deze tweede opdracht uitvoert maakt het **/** scheidingsteken in de blobnamen een volledige mapstructuur onder de doelmap. Deze structuur wordt gebruikt voor het downloaden en opslaan van de blobs als bestanden.

Als u alleen specifieke blobs wilt downloaden, moet u jokertekens gebruiken. Bijvoorbeeld:

- Als u meerdere sleutelkluizen hebt en het logboek voor slechts één sleutelkluis wilt downloaden met de naam CONTOSOKEYVAULT3:

        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3

- Als u meerdere resourcegroepen hebt en logboeken voor slechts één resourcegroep wilt downloaden, gebruikt u `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'

- Als u alle logboeken voor de maand januari 2016 wilt, gebruikt u `-Blob '*/year=2016/m=01/*'`:

        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'

We gaan zo kijken wat er precies in de logboeken staat. Maar eerst behandelen we nog twee parameters voor Get-AzureRmDiagnosticSetting die handig kunnen zijn:

- De status van diagnostische instellingen voor uw sleutelkluisresource opvragen: `Get-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId`
 
- Logboekregistratie voor uw sleutelkluisresource uitschakelen: `Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories AuditEvent`


## <a id="interpret"></a>De Sleutelkluis-logboekgegevens interpreteren ##

Afzonderlijke blobs worden opgeslagen als tekst, die is opgemaakt als een JSON-blob. Dit is een voorbeeld van een logboekvermelding van het uitvoeren van `Get-AzureRmKeyVault -VaultName 'contosokeyvault'`:

    {
        "records": 
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }


De volgende tabel bevat de namen en beschrijvingen van velden.


| Veldnaam        | Beschrijving |
| ------------- |-------------|
| tijd      | Datum en tijd (UTC).|
| resourceId      | Azure Resource Manager-resource-id. Voor Sleutelkluis-logboeken is dit altijd de Sleutelkluis-resource-id.|
| operationName      | Naam van de bewerking, zoals beschreven in de volgende tabel.|
| operationVersion      | Dit is de REST-API-versie die door de client is aangevraagd.|
| category      | Voor Sleutelkluis-logboeken is AuditEvent de enige beschikbare waarde.|
| resultType      | Resultaat van de REST-API-aanvraag.|
| resultSignature      | HTTP-status.|
| resultDescription     | Extra beschrijving van het resultaat, indien beschikbaar.|
| durationMs      | De tijd die nodig was om de REST-API-aanvraag af te handelen in milliseconden. Hierbij wordt geen rekening gehouden met de netwerklatentie, zodat de tijd die u aan de clientzijde meet mogelijk niet overeenkomt met de tijd die hier wordt weergegeven.|
| callerIpAddress      | IP-adres van de client die de aanvraag heeft ingediend.|
| correlationId      | Een optionele GUID die de client kan doorgeven om de logboeken aan de clientzijde te relateren aan (Sleutelkluis-)logboeken aan de servicezijde.|
| identity      | De identiteit van het token dat is opgegeven bij het maken van de REST-API-aanvraag. Dit is meestal user, service principal of een combinatie user+ appId, bijvoorbeeld bij een aanvraag via een Azure PowerShell-cmdlet.|
| properties      | Dit veld bevat verschillende gegevens op basis van de bewerking (operationName). In de meeste gevallen bevat dit veld clientgegevens (de useragent-tekenreeks die door de client wordt doorgegeven), de exacte URI voor de REST-API-aanvraag en de HTTP-statuscode. Als een object wordt geretourneerd als gevolg van een aanvraag (bijvoorbeeld KeyCreate of VaultGet) bevat dit veld ook de Key URI (als 'id'), Vault URI of Secret URI.|

 


De velwaarden voor **operationName** hebben de ObjectVerb-indeling. Bijvoorbeeld:

- Alle sleutelkluisbewerkingen hebben de indeling 'Sleutelkluis`<action>`', zoals `VaultGet` en `VaultCreate`. 

- Alle sleutelbewerkingen hebben de indeling 'Sleutel`<action>`', zoals `KeySign` en `KeyList`. 

- Alle geheime bewerkingen hebben de indeling 'Geheim`<action>`', zoals `SecretGet` en `SecretListVersions`.

De volgende tabel bevat de operationName en de bijbehorende REST-API-opdracht.

| operationName        | REST-API-opdracht |
| ------------- |-------------|
| Authentication      | Via het Azure Active Directory-eindpunt|
| VaultGet      | [Informatie over een sleutelkluis ophalen](https://msdn.microsoft.com/en-us/library/azure/mt620026.aspx)|
| VaultPut      | [Een sleutelkluis maken of bijwerken](https://msdn.microsoft.com/en-us/library/azure/mt620025.aspx)|
| VaultDelete      | [Een sleutelkluis verwijderen](https://msdn.microsoft.com/en-us/library/azure/mt620022.aspx)|
| VaultPatch      | [Een sleutelkluis bijwerken](https://msdn.microsoft.com/library/azure/mt620025.aspx)|
| VaultList      | [Alle sleutelkluizen in een resourcegroep weergeven](https://msdn.microsoft.com/en-us/library/azure/mt620027.aspx)|
| KeyCreate      | [Een sleutel maken](https://msdn.microsoft.com/en-us/library/azure/dn903634.aspx)|
| KeyGet      | [Informatie over een sleutel ophalen](https://msdn.microsoft.com/en-us/library/azure/dn878080.aspx)|
| KeyImport      | [Een sleutel in een kluis importeren](https://msdn.microsoft.com/en-us/library/azure/dn903626.aspx)|
| KeyBackup      | [Back-up maken van een sleutel](https://msdn.microsoft.com/en-us/library/azure/dn878058.aspx).|
| KeyDelete      | [Een sleutel verwijderen](https://msdn.microsoft.com/en-us/library/azure/dn903611.aspx)|
| KeyRestore      | [Een sleutel herstellen](https://msdn.microsoft.com/en-us/library/azure/dn878106.aspx)|
| KeySign      | [Aanmelden met een sleutel](https://msdn.microsoft.com/en-us/library/azure/dn878096.aspx)|
| KeyVerify      | [Verifiëren met een sleutel](https://msdn.microsoft.com/en-us/library/azure/dn878082.aspx)|
| KeyWrap      | [Een sleutel inpakken](https://msdn.microsoft.com/en-us/library/azure/dn878066.aspx)|
| KeyUnwrap      | [Een sleutel uitpakken](https://msdn.microsoft.com/en-us/library/azure/dn878079.aspx)|
| KeyEncrypt      | [Versleutelen met een sleutel](https://msdn.microsoft.com/en-us/library/azure/dn878060.aspx)|
| KeyDecrypt      | [Ontsleutelen met een sleutel](https://msdn.microsoft.com/en-us/library/azure/dn878097.aspx)|
| KeyUpdate      | [Een sleutel bijwerken](https://msdn.microsoft.com/en-us/library/azure/dn903616.aspx)|
| KeyList      | [De sleutels in een kluis weergeven](https://msdn.microsoft.com/en-us/library/azure/dn903629.aspx)|
| KeyListVersions      | [De versies van een sleutel weergeven](https://msdn.microsoft.com/en-us/library/azure/dn986822.aspx)|
| SecretSet      | [Een geheim maken](https://msdn.microsoft.com/en-us/library/azure/dn903618.aspx)|
| SecretGet      | [Een geheim ophalen](https://msdn.microsoft.com/en-us/library/azure/dn903633.aspx)|
| SecretUpdate      | [Een geheim bijwerken](https://msdn.microsoft.com/en-us/library/azure/dn986818.aspx)|
| SecretDelete      | [Een geheim verwijderen](https://msdn.microsoft.com/en-us/library/azure/dn903613.aspx)|
| SecretList      | [Geheimen in een kluis weergeven](https://msdn.microsoft.com/en-us/library/azure/dn903614.aspx)|
| SecretListVersions      | [Versies van een geheim weergeven](https://msdn.microsoft.com/en-us/library/azure/dn986824.aspx)|




## <a id="next"></a>Volgende stappen ##

Zie [Azure Sleutelkluis in een webtoepassing gebruiken](key-vault-use-from-web-application.md) voor een zelfstudie over het gebruik van Azure Sleutelkluis in een webtoepassing.

Zie [de gids voor ontwikkelaars voor Azure Sleutelkluis](key-vault-developers-guide.md) voor het programmeren van verwijzingen.

Zie [Cmdlets voor Azure Sleutelkluis](https://msdn.microsoft.com/library/azure/dn868052.aspx) voor een lijst met Azure PowerShell 1.0- cmdlets voor Azure Sleutelkluis. 
 





<!--HONumber=Jun16_HO2-->


