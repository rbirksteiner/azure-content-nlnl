## De ARM-sjabloon downloaden en begrijpen

U kunt de bestaande ARM-sjabloon voor het aanmaken van een VNet en twee subnets vanuit github downloaden. Vervolgens kunt u de wijzigingen aanbrengen die u wilt aanbrengen en hem opnieuw gebruiken. Volg de onderstaande stappen om dit te doen:

1. Navigeer naar [de sjabloon-voorbeeldpagina](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
2. Klik op **azuredeploy.json** en vervolgens op **RAW**.
3. Sla het bestand op in een lokale map op uw computer.
4. Als u bekend bent met ARM-sjablonen, kunt u doorgaan naar stap 7.
5. Open het bestand dat u zojuist hebt opgeslagen en bekijk de inhoud onder **parameters** in regel 5. ARM-sjabloonparameters bieden een tijdelijke aanduiding voor waarden die kunnen worden ingevuld tijdens de implementatie.

    | Parameter | Beschrijving |
    |---|---|
    | **locatie** | Azure-regio waar het VNet wordt aangemaakt |
    | **vnetName** | Naam voor het nieuwe VNet |
    | **addressPrefix** | Adresruimte voor het VNet, in CIDR-indeling |
    | **subnet1Name** | Naam voor het eerste VNet |
    | **subnet1Prefix** | CIDR-blokkering voor het eerste subnet |
    | **subnet2Name** | Naam voor het tweede VNet |
    | **subnet2Prefix** | CIDR-blokkering voor het tweede subnet |

    >[AZURE.IMPORTANT] ARM-sjablonen die in github worden bewaard, kunnen op den duur veranderen. Zorg ervoor dat u de sjabloon controleert, voordat u deze gebruikt.
    
6. Controleer de inhoud onder **Resources** en let op het volgende:

    - **Type**. Het type resource dat door de sjabloon wordt aangemaakt. In dit geval **Microsoft.Network/virtualNetworks**, die een VNet vertegenwoordigen.
    - **Naam**. Naam voor de resource. Let op het gebruik van **[parameters('vnetName')]**. Deze geeft aan of de naam wordt geleverd als invoer door de gebruiker of een parameterbestand tijdens implementatie.
    - **Eigenschappen**. Lijst met eigenschappen voor de resource. Deze sjabloon maakt gebruik van de adresruimte en de subneteigenschappen tijdens het aanmaken van het VNet.

7. Ga naar de [pagina Voorbeeldsjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
8. Klik op **azuredeploy-paremeters.json** en vervolgens op **RAW**.
9. Sla het bestand op in een lokale map op uw computer.
10. Open het bestand dat u zojuist hebt opgeslagen en bewerk de waarden voor de parameters. Gebruik de onderstaande waarden voor het implementeren van het VNet dat in ons scenario wordt beschreven.

        {
          "location": {
            "value": "Central US"
          },
          "vnetName": {
              "value": "TestVNet"
          },
          "addressPrefix": {
              "value": "192.168.0.0/16"
          },
          "subnet1Name": {
              "value": "FrontEnd"
          },
          "subnet1Prefix": {
            "value": "192.168.1.0/24"
          },
          "subnet2Name": {
              "value": "BackEnd"
          },
          "subnet2Prefix": {
              "value": "192.168.2.0/24"
          }
        }

11. Sla het bestand op.
  


<!--HONumber=Jun16_HO2-->


