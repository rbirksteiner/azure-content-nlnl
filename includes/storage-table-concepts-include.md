## Wat is Tabelservice

Met de service Azure Table Storage kunnen grote hoeveelheden gestructureerde gegevens worden opgeslagen. De service is een NoSQL-gegevensarchief die geverifieerde aanroepen in en buiten de Azure-cloud accepteert. Azure-tabellen zijn ideaal voor het opslaan van gestructureerde, niet-relationele gegevens. Veelvoorkomende toepassingen van Tabelservice zijn:

-   Opslaan van terabytes aan gestructureerde gegevens die kunnen worden geleverd aan webschaaltoepassingen
-   Opslaan van gegevenssets die geen complexe joins, refererende sleutels of opgeslagen procedures vereisen en die kunnen worden gedenormaliseerd voor snelle toegang
-   Snel een query voor gegevens uitvoeren met een geclusterde index
-   Toegang tot gegevens krijgen met het OData-protocol en LINQ-query's met WCF Data Service .NET-bibliotheken

U kunt Tabelservice gebruiken om grote sets gestructureerde, niet-relationele gegevens op te slaan en query’s op de gegevens uit te voeren. En wanneer de vraag toeneemt, worden uw tabellen opgeschaald.

## Concepten van Tabelservice

Tabelservice bevat de volgende onderdelen:

![Table1][Table1]

-   **URL-indeling:** voor tabellen in een account wordt code met de volgende adresindeling gebruikt:   
    http://`<storage account>`.table.core.windows.net/`<table>`  
      
    U kunt rechtstreeks naar Azure-tabellen verwijzen met dit adres met het OData-protocol. Zie [OData.org][] voor meer informatie

-   **Storage-account:** alle toegang tot Azure Storage vindt plaats via een opslagaccount. Zie [Azure Storage Scalability and Performance Targets](storage-scalability-targets.md) (Schaalbaarheids- en prestatiedoelen in Azure Storage) voor meer informatie over opslagaccountcapaciteit.

-   **Tabel**: een tabel is een verzameling entiteiten. Met tabellen wordt geen schema voor entiteiten afgedwongen, wat betekent dat één tabel entiteiten kan bevatten die verschillende sets eigenschappen hebben. Het aantal tabellen dat een opslagaccount kan bevatten, wordt alleen beperkt door de capaciteitslimiet van het opslagaccount.

-   **Entiteit**: een entiteit is een set eigenschappen die vergelijkbaar is met een databaserij. Een entiteit kan maximaal 1 MB groot zijn.

-   **Eigenschappen**: een eigenschap is een naamwaardepaar. Elke entiteit kan maximaal 252 eigenschappen voor het opslaan van gegevens bevatten. Elke entiteit heeft ook drie systeemeigenschappen waarmee een partitiesleutel, een rijsleutel en een timestamp worden opgegeven. Entiteiten met dezelfde partitiesleutel kunnen sneller worden opgevraagd en in atomische bewerkingen worden ingevoegd/bijgewerkt. De rijsleutel van een entiteit is de unieke id in een partitie.

Zie [Understanding the Table Service Data Model](https://msdn.microsoft.com/library/azure/dd179338.aspx) (Inzicht krijgen in het Tabelservice-gegevensmodel) voor meer informatie over de naamgeving van tabellen en eigenschappen.
  
  [Table1]: ./media/storage-table-concepts-include/table1.png
  [OData.org]: http://www.odata.org/



<!--HONumber=Jun16_HO2-->


