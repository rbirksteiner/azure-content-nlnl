<properties 
    pageTitle="Binnen vijf minuten aan de slag met Azure Storage | Microsoft Azure" 
    description="Ga snel aan de slag met Microsoft Azure Blobs, Table en Queues met Azure Storage-Quick Starts, Visual Studio en de Azure-opslagemulator. Voer uw eerste Azure Storage-toepassing uit binnen vijf minuten." 
    services="storage" 
    documentationCenter=".net" 
    authors="tamram" 
    manager="carmonm" 
    editor="tysonn"/>

<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="get-started-article" 
    ms.date="05/23/2016"
    ms.author="tamram"/>

# Binnen vijf minuten aan de slag met Azure Storage 

## Overzicht

Het is gemakkelijk om te beginnen met ontwikkelen met Azure Storage. Deze zelfstudie laat zien hoe u een Azure Storage-toepassing snel gebruiksklaar hebt. U maakt gebruik van de Quick Start-sjablonen die worden meegeleverd met de Azure SDK voor .NET. Deze Quick Starts bevatten gebruiksklare code die enkele eenvoudige programmeringsscenario’s met Azure Storage laten zien. 

Als u meer informatie over Azure Storage wilt voordat u met de code aan de slag gaat, raadpleegt u [Volgende stappen](#next-steps).

## Vereisten

Voordat u begint, moet aan de volgende vereisten zijn voldaan:

1. Voor het samenstellen en maken van de toepassing moet er een versie van [Visual Studio](https://www.visualstudio.com/) op uw computer zijn geïnstalleerd. 

2. Installeer de nieuwste versie van [Azure SDK voor .NET](https://azure.microsoft.com/downloads/). De SDK bevat de Azure QuickStart-voorbeeldprojecten, de Azure-opslagemulator en de [Azure Storage-clientbibliotheek voor .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

3. Zorg ervoor dat [.NET Framework 4.5](http://www.microsoft.com/download/details.aspx?id=30653) op uw computer is geïnstalleerd. Dit is vereist voor de Azure QuickStart-voorbeeldprojecten die in deze zelfstudie worden gebruikt. 

    Als u niet zeker weet welke versie van .NET Framework er op uw computer is geïnstalleerd, raadpleegt u [Procedure: Vaststellen welke .NET Framework-versies er zijn geïnstalleerd](https://msdn.microsoft.com/vstudio/hh925568.aspx). Of druk op de **Start**-knop of de Windows-toets en typ **Configuratiescherm**. Klik vervolgens op **Programma's** > **Programma's en onderdelen** en controleer of .NET Framework 4.5 tussen de geïnstalleerde programma's staat.

4. U hebt een Azure-abonnement en een Azure-opslagaccount nodig.

    - Als u een Azure-abonnement wilt aanschaffen, raadpleegt u [Gratis proefversie](https://azure.microsoft.com/pricing/free-trial/), [Koopopties](https://azure.microsoft.com/pricing/purchase-options/) en [Aanbiedingen voor leden](https://azure.microsoft.com/pricing/member-offers/) (voor leden van MSDN, Microsoft Partner Network BizSpark en andere Microsoft-programma's).
    - Zie [Een opslagaccount maken](storage-create-storage-account.md#create-a-storage-account) voor informatie over het maken van een opslagaccount in Azure.

## Uw eerste Azure Storage-toepassing uitvoeren in Azure Storage in de cloud

Als u een account hebt, kunt u een eenvoudige Azure Storage-toepassing maken met behulp van een van de Azure QuickStart-voorbeeldprojecten. Deze zelfstudie richt zich op de voorbeeldprojecten voor Azure Storage: **Azure Storage: blobs**, **Azure Storage: bestanden**, **Azure Storage: wachtrijen** en **Azure Storage: tabellen**.

1. Start Visual Studio.
2. Klik in het menu **Bestand** op **Nieuw project**.
3. Klik in het dialoogvenster **Nieuw project** op **Geïnstalleerd** > **Sjablonen** > **Visual C#** > **Cloud** > **QuickStarts** > **Data Services**.
    a. Kies een van de volgende sjablonen: **Azure Storage: blobs**, **Azure Storage: bestanden**, **Azure Storage: wachtrijen** of **Azure Storage: tabellen**.
    b. Zorg ervoor dat **.NET Framework 4.5** als doelframework is geselecteerd.
    - 3.c. Geef een naam op voor uw project en maak de nieuwe Visual Studio-oplossing, zoals afgebeeld:
    
    ![Azure Quick Starts][Image1]

Mogelijk wilt u de broncode controleren voordat u de toepassing uitvoert. Als u de code wilt controleren, selecteert u **Solution Explorer** in het menu **Beeld** van Visual Studio. Dubbelklik vervolgens op het bestand Program.cs. 

Voer daarna de voorbeeldtoepassing uit:

1.  Selecteer in Visual Studio **Solution Explorer** in het menu **Beeld**. Open het bestand App.config en uitcommentarieer de verbindingsreeks voor de Azure-opslagemulator:

    `<!--<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>-->`

2.  Verwijder de opmerkingen in de verbindingsreeks voor de Azure Storage-service en geef de naam en toegangssleutel van het opslagaccount op in het bestand App.config: `<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]"`

    Zie [Toegangssleutels beheren voor uw opslagaccount](storage-create-storage-account.md#manage-your-storage-access-keys) voor informatie over het ophalen van de toegangssleutels voor uw opslagaccount.

3.  Nadat u de naam en de toegangssleutel van het opslagaccount hebt opgegeven in het bestand App.config, klikt u in het menu **Bestand** op **Alles opslaan** om alle projectbestanden op te slaan.
4.  Klik in het menu **Bouwen** op **Oplossing bouwen**.
5.  Druk in het menu **Foutopsporing** op **F11** om de oplossing stapsgewijs uit te voeren of druk op **F5** om de oplossing van begin tot eind uit te voeren.


## Uw eerste Azure Storage-toepassing lokaal uitvoeren in de Azure-opslagemulator

De [Azure-opslagemulator](storage-use-emulator.md) biedt een lokale omgeving waarin de Azure-services voor blobs, wachtrijen en tabellen voor ontwikkelingsdoeleinden worden geëmuleerd. U kunt de Azure-opslagemulator gebruiken om uw opslagtoepassing lokaal te testen, zonder een Azure-abonnement aan te schaffen of een opslagaccount te maken, en zonder eventuele kosten.

Laten we eerst een eenvoudige Azure Storage-toepassing maken met behulp van een van de Azure QuickStart-voorbeeldprojecten. Deze zelfstudie richt zich op de **Azure Blob Storage**-, **Azure Table Storage**-, en **Azure Queue Storage**-voorbeeldprojecten:

1. Start Visual Studio.
2. Klik in het menu **Bestand** op **Nieuw project**.
3. Klik in het dialoogvenster **Nieuw project** op **Geïnstalleerd** > **Sjablonen** > **Visual C#** > **Cloud** > **QuickStarts** > **Data Services**.
   a. Kies een van de volgende sjablonen: **Azure Storage: blobs**, **Azure Storage: bestanden**, **Azure Storage: wachtrijen** of **Azure Storage: tabellen**.
   b. Zorg ervoor dat **.NET Framework 4.5** als doelframework is geselecteerd.   
    c. Geef een naam op voor uw project en maak de nieuwe Visual Studio-oplossing, zoals afgebeeld:
    
    ![Azure Quick Starts][Image1]

4.  Selecteer in Visual Studio **Solution Explorer** in het menu **Beeld**. Open het bestand App.config en uitcommentarieer de verbindingsreeks voor uw Azure Storage-account als u er al een hebt toegevoegd. Verwijder vervolgens de opmerkingen in de verbindingsreeks voor de Azure-opslagemulator:

    `<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>`

Mogelijk wilt u de broncode controleren voordat u de toepassing uitvoert. Als u de code wilt controleren, selecteert u **Solution Explorer** in het menu **Beeld** van Visual Studio. Dubbelklik vervolgens op het bestand Program.cs. 

Vervolgens voert u de voorbeeldtoepassing uit in de Azure-opslagemulator:

1.  Druk op de **Start**-knop of de Windows-toets, zoek *Microsoft Azure-opslagemulator* op en start de toepassing. Wanneer de emulator wordt gestart, ziet u een pictogram en een melding in de taakweergave van Windows.
2.  Klik in Visual Studio op **Oplossing bouwen** in het menu **Bouwen**. 
3.  Druk in het menu **Foutopsporing** op **F11** om de oplossing stapsgewijs uit te voeren of druk op **F5** om de oplossing van begin tot eind uit te voeren.

## Volgende stappen

Zie de volgende bronnen voor meer informatie over Azure Storage:

* [Inleiding tot Microsoft Azure Storage](storage-introduction.md)
* [Aan de slag met Azure Blob Storage met .NET](storage-dotnet-how-to-use-blobs.md)
* [Aan de slag met Azure Table Storage met .NET](storage-dotnet-how-to-use-tables.md)
* [Aan de slag met Azure Queue Storage met .NET](storage-dotnet-how-to-use-queues.md)
* [Aan de slag met Azure File Storage in Windows](storage-dotnet-how-to-use-files.md)
* [Gegevensoverdracht met het AzCopy-opdrachtregelprogramma](storage-use-azcopy.md)
* [Documentatie bij Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Microsoft Azure Storage-clientbibliotheek voor .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [REST-API voor Azure Storage-services](https://msdn.microsoft.com/library/azure/dd179355.aspx)

[Image1]: ./media/storage-getting-started-guide/QuickStart.png
 



<!--HONumber=Jun16_HO2-->


