De [Configuration Manager-bibliotheek van Microsoft Azure voor .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) biedt een klasse voor het parseren van een verbindingsreeks uit een configuratiebestand. De [klasse CloudConfigurationManager](https://msdn.microsoft.com/library/azure/mt634650.aspx) parseert configuratie-instellingen, ongeacht of de clienttoepassing wordt uitgevoerd op het bureaublad, op een mobiel apparaat, op een virtuele machine in Azure, of in een Azure-cloudservice.

Voeg de volgende `using`-instructie aan uw klasse toe om naar het pakket CloudConfigurationManager te verwijzen:

    using Microsoft.Azure;  //Namespace for CloudConfigurationManager

In het volgende voorbeeld ziet u hoe u een verbindingsreeks ophaalt uit een configuratiebestand:

    // Parse the connection string and return a reference to the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Het gebruik van Azure Configuration Manager is optioneel. U kunt ook een API gebruiken, zoals de [ConfigurationManager-klasse](https://msdn.microsoft.com/library/system.configuration.configurationmanager.aspx) van .NET Framework.



<!--HONumber=Jun16_HO2-->


