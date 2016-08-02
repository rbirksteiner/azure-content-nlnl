<properties
    pageTitle="Indexeerfuncties in Azure Search | Microsoft Azure | Gehoste service voor zoeken in de cloud"
    description="Azure SQL database, DocumentDB of Azure-opslag verkennen om doorzoekbare gegevens op te halen en een Azure Search-index te vullen."
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="mblythe"
    editor=""
    tags="azure-portal"/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="04/14/2016"
    ms.author="heidist"/>

# Indexeerfuncties in Azure Search
> [AZURE.SELECTOR]
- [Overzicht](search-indexer-overview.md)
- [Portal](search-import-data-portal.md)
- [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [DocumentDB](../documentdb/documentdb-search-indexer.md)
- [Blob Storage (voorbeeld)](search-howto-indexing-azure-blob-storage.md)
- [Table Storage (voorbeeld)](search-howto-indexing-azure-tables.md)

Een **indexeerfunctie** in Azure Search is een verkenner die doorzoekbare gegevens en metagegevens uit een externe gegevensbron ophaalt en een index vult op basis van veld-naar-veld-verwijzingen tussen de index en de gegevensbron. Deze aanpak wordt ook wel het 'pull-model' genoemd, omdat de service de gegevens ophaalt zonder dat u code hoeft te schrijven om de gegevens naar de index te pushen.

U kunt een indexeerfunctie gebruiken voor de opname van gegevens of een combinatie van technieken gebruiken, waaronder een indexeerfunctie voor het laden van slechts enkele velden in de index.

Indexeerfuncties kunnen op aanvraag worden uitgevoerd of volgens een terugkerend schema, waarbij gegevens wel om de vijftien minuten kunnen worden vernieuwd. Als u vaker updates wilt uitvoeren, hebt u een pushmodel nodig dat tegelijkertijd gegevens in Azure Search en uw externe gegevensbron bijwerkt.

Een indexeerfunctie haalt gegevens op uit een **gegevensbron** die informatie, zoals een verbindingsreeks, bevat. Momenteel worden de volgende gegevensbronnen ondersteund:

- [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md) (of SQL Server in een virtuele machine van Azure)
- [DocumentDB](../documentdb/documentdb-search-indexer.md)
- [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) (momenteel in de preview-versie) Haalt tekst op uit pdf-bestanden, Office-documenten en HTML- en XML-bestanden).
- [Azure Table Storage](search-howto-indexing-azure-tables.md) (momenteel in de preview-versie)

Gegevensbronnen worden geconfigureerd en onafhankelijk van de indexeerfuncties beheerd die gebruikmaken van de gegevensbronnen. Dit betekent dat een gegevensbron door meerdere indexeerfuncties kan worden gebruikt om tegelijkertijd meer dan één index te laden. 

Zowel de [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.search.iindexersoperations.aspx) als de [REST-API van de service](https://msdn.microsoft.com/library/azure/dn946891.aspx) bieden ondersteuning voor het beheer van indexeerfuncties en gegevensbronnen. 

U kunt ook een indexeerfunctie configureren in de portal met behulp van de wizard **Gegevens importeren**. Zie [Aan de slag met Azure Search in de portal](search-get-started-portal) voor een snelle zelfstudie waarin gebruik wordt gemaakt van voorbeeldgegevens en de DocumentDB-indexeerfunctie om een index te maken en te laden met behulp van de wizard.






<!--HONumber=Jun16_HO2-->


