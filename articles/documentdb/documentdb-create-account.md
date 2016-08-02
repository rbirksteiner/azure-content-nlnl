<properties
    pageTitle="Een DocumentDB-account maken met Azure Portal | Microsoft Azure"
    description="Bouw een NoSQL-database met Azure DocumentDB. Volg deze instructies om een DocumentDB-account te maken en bouw uw razendsnelle, wereldwijde NoSQL-database." 
    keywords="build a database"
    services="documentdb"
    documentationCenter=""
    authors="mimig1"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/16/2016"
    ms.author="mimig"/>

# Een DocumentDB-account maken met Azure Portal

> [AZURE.SELECTOR]
- [Azure Portal](documentdb-create-account.md)
- [Azure CLI en ARM](documentdb-automation-resource-manager-cli.md)

Als u een database met Microsoft Azure DocumentDB wilt maken, moet u:

- Een Azure-account hebben. Als u nog geen account hebt, kunt u een [gratis Azure-account](https://azure.microsoft.com/free) krijgen. 
- Een DocumentDB-account maken.  

U kunt een DocumentDB-account maken met Azure Portal, Azure Resource Manager-sjablonen of Azure CLI (Command-Line Interface; opdrachtregelinterface). In dit artikel wordt uitgelegd hoe u een databaseaccount maakt met Azure Portal. Zie [Het maken van een DocumentDB-databaseaccount automatiseren](documentdb-automation-resource-manager-cli.md) voor informatie over het maken van een account met Azure Resource Manager of Azure CLI.

Bent u niet bekend met DocumentDB? Bekijk [deze](https://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/) vier minuten durende video van Scott Hanselman om te zien hoe u de meest algemene taken in de online portal uitvoert.

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## Volgende stappen

Nu u een DocumentDB-acct hebt, moet u vervolgens de DocumentDB-database bouwen. U kunt een van de volgende manieren gebruiken om een database te bouwen:

- Azure Portal, zoals beschreven in [Een DocumentDB-database maken met Azure Portal](documentdb-create-database.md).
- De C# .NET-voorbeelden in het [DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement)-project van de [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples)-opslagplaats op GitHub.
- De allesomvattende zelfstudies: [.NET](documentdb-get-started.md), [.NET MVC](documentdb-dotnet-application.md), [Java](documentdb-java-application.md), [Node.js](documentdb-nodejs-application.md) of [Python](documentdb-python-application.md).
- De [DocumentDB SDK's](documentdb-sdk-dotnet.md). DocumentDB heeft .NET, Java, Python, Node.js en JavaScript API SDK's.


Nadat de database is gemaakt, moet u [een of meer verzamelingen toevoegen ](documentdb-create-collection.md) aan de database en vervolgens [documenten toevoegen](documentdb-view-json-document-explorer.md) aan de verzamelingen.

Nadat u documenten aan een verzameling hebt toegevoegd, kunt u [DocumentDB SQL](documentdb-sql-query.md) gebruiken om [query's uit te voeren](documentdb-sql-query.md#executing-queries) op uw documenten door de [Queryverkenner](documentdb-query-collections-query-explorer.md) in de portal, de [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx), of een van de [SDK's](documentdb-sdk-dotnet.md) te gebruiken.

Raadpleeg de volgende resources voor meer informatie over DocumentDB:

-   [Leertraject voor DocumentDB](https://azure.microsoft.com/documentation/learning-paths/documentdb/)
-   [DocumentDB-resourcemodel en -concepten](documentdb-resources.md)



<!--HONumber=Jun16_HO2-->


