## De ontwikkelomgeving instellen

Vervolgens stelt u uw ontwikkelomgeving in Visual Studio zo in dat u de codevoorbeelden in deze handleiding kunt uitproberen.

### Een Windows-consoletoepassingsproject maken

Maak in Visual Studio een nieuwe Windows-consoletoepassing, zoals hier wordt weergegeven:

![Windows-consoletoepassing maken](./media/storage-development-environment-include/storage-development-environment-include-1.png)

Alle codevoorbeelden in deze zelfstudie kunnen worden toegevoegd aan de methode **Main()** in `program.cs` in uw consoletoepassing.

U kunt de Azure Storage-clientbibliotheek gebruiken vanuit elk type .NET-toepassing, waaronder een Azure-cloudservice, een Azure-web-app, een bureaubladtoepassing of een mobiele toepassing. In deze gids gebruiken we een consoletoepassing voor de eenvoud.

### NuGet gebruiken om de vereiste pakketten te installeren

Er zijn twee pakketten die u moet installeren voor uw project om deze zelfstudie te kunnen voltooien:

- [Microsoft Azure Storage-clientbibliotheek voor .NET](https://www.nuget.org/packages/WindowsAzure.Storage/): dit pakket biedt programmatisch toegang tot gegevensbronnen in uw opslagaccount.
- [Configuration Manager-bibliotheek van Microsoft Azure voor .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/): dit pakket biedt een klasse voor het parseren van een verbindingsreeks vanuit een configuratiebestand, ongeacht waar de toepassing wordt uitgevoerd.

Met NuGet kunt u beide pakketten verkrijgen. Volg deze stappen:

1. Klik met de rechtermuisknop op het project in **Solution Explorer** en kies **NuGet-pakketten beheren**.
2. Zoek online naar 'WindowsAzure.Storage' en klik op **Installeren** om de Storage-clientbibliotheek en de afhankelijkheden ervan te installeren.
3. Zoek online naar 'ConfigurationManager' en klik op **Installeren** om Azure Configuration Manager te installeren.

>[AZURE.NOTE] Het pakket met de Storage-clientbibliotheek is ook opgenomen in de [Azure-SDK voor .NET](https://azure.microsoft.com/downloads/). We raden u echter aan om ook de Storage-clientbibliotheek vanuit NuGet te installeren, zodat u zeker weet dat u altijd de meest recente versie van de clientbibliotheek hebt geïnstalleerd.
>
>De ODataLib-afhankelijkheden in de Storage-clientbibliotheek voor .NET worden opgelost middels ODataLib-pakketten (versie 5.0.2 en hoger), die beschikbaar zijn via NuGet, en niet middels WCF Data Services. De ODataLib-bibliotheken kunnen rechtstreeks worden gedownload of er kan vanuit het codeproject via NuGet naar worden verwezen. De specifieke ODataLib-pakketten die door de Storage-clientbibliotheek worden gebruikt, zijn [OData](http://nuget.org/packages/Microsoft.Data.OData/5.0.2), [Edm](http://nuget.org/packages/Microsoft.Data.Edm/5.0.2) en [Spatial](http://nuget.org/packages/System.Spatial/5.0.2). Hoewel deze bibliotheken door de Azure Table-opslagklassen worden gebruikt, zijn het verplichte afhankelijkheden voor het programmeren met de Storage-clientbibliotheek.


### De doelomgeving bepalen

U kunt de voorbeelden in deze gids in twee omgevingen uitvoeren:

- U kunt de code uitvoeren met een Azure Storage-account in de cloud. 
- U kunt de code uitvoeren met de Azure-opslagemulator. De opslagemulator is een lokale omgeving die een Azure Storage-account in de cloud emuleert. De emulator is een gratis optie waarmee u uw code kunt testen en fouten in de code kunt opsporen terwijl de toepassing nog in ontwikkeling is. De emulator maakt gebruik van een bekend account en een bekende sleutel. Zie [Use the Azure Storage Emulator for Development and Testing](../articles/storage/storage-use-emulator.md) (De Azure-opslagemulator gebruiken voor het ontwikkelen en testen) voor meer informatie.

Als u een opslagaccount in de cloud wilt gebruiken, kopieert u de primaire toegangssleutel voor uw opslagaccount vanuit Azure Portal. Zie [Opslagtoegangssleutels bekijken en kopiëren](../articles/storage/storage-create-storage-account.md#view-and-copy-storage-access-keys) voor meer informatie.

> [AZURE.NOTE] Gebruik de opslagemulator als u mogelijke kosten in verband met Azure-opslag wilt vermijden. Als u er echter voor kiest om een Azure-opslagaccount in de cloud te gebruiken, zijn de kosten voor de uitvoering van deze zelfstudie te verwaarlozen.

### De opslagverbindingsreeks configureren

De clientbibliotheek van Azure Storage voor .NET ondersteunt het gebruik van een opslagverbindingsreeks om eindpunten en referenties voor toegang tot opslagservices te configureren. De beste manier om de opslagverbindingsreeks te onderhouden, is met een configuratiebestand. 

Zie [Azure Storage-verbindingsreeksen configureren](../articles/storage/storage-configure-connection-string.md) voor meer informatie over verbindingsreeksen.

> [AZURE.NOTE] De sleutel van uw opslagaccount is vergelijkbaar met het hoofdwachtwoord voor uw opslagaccount. Zorg dat de sleutel van uw opslagaccount altijd is beveiligd. Geef deze niet aan andere gebruikers en bewaar of noteer de sleutel op een veilige manier en plaats. Genereer een nieuwe sleutel met Azure Portal als deze misschien is aangetast.

U configureert de verbindingsreeks door het bestand `app.config` te openen vanuit Solution Explorer in Visual Studio. Voeg de inhoud van het element `<appSettings>` hieronder toe. Vervang `account-name` door de naam van uw opslagaccount en `account-key` door de toegangssleutel van uw account:

    <configuration>
        <startup> 
            <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
        </startup>
        <appSettings>
            <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
        </appSettings>
    </configuration>

De configuratie-instelling kan er als volgt uitzien:

    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=nYV0gln6fT7mvY+rxu2iWAEyzPKITGkhM88J8HUoyofvK7C6fHcZc2kRZp6cKgYRUM74lHI84L50Iau1+9hPjB==" />

Als u de opslagemulator wilt gebruiken, kunt u de bekende accountnaam en -sleutel op een snelle manier toewijzen. In dat geval ziet de instelling van de verbindingsreeks er als volgt uit:

    <add key="StorageConnectionString" value="UseDevelopmentStorage=true;" />




<!--HONumber=Jun16_HO2-->


