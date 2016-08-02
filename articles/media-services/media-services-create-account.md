<properties
    pageTitle="Een Media Services-account maken | Microsoft Azure"
    description="Hierin wordt beschreven hoe u een nieuw Azure Media Services-account in Azure maakt."
    services="media-services"
    documentationCenter=""
    authors="Juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="04/18/2016"
    ms.author="juliako"/>


# Een Azure Media Services-account maken

> [AZURE.SELECTOR]
- [Portal](media-services-create-account.md)
- [PowerShell](media-services-manage-with-powershell.md)
- [REST](http://msdn.microsoft.com/library/azure/dn194267.aspx)


> [AZURE.NOTE] U hebt een Azure-account nodig om deze zelfstudie te voltooien. Zie [Gratis proefversie van Azure](/pricing/free-trial/?WT.mc_id=A261C142F) voor meer informatie.
 
De klassieke Azure-portal biedt een manier om snel een Azure Media Services-account te maken. U kunt uw account gebruiken voor toegang tot Media Services waarmee u media-inhoud in Azure kunt opslaan, versleutelen, coderen, beheren en streamen. Op het moment dat u een Media Services-account maakt, maakt u ook een bijbehorend opslagaccount (of gebruikt u een bestaand account) in hetzelfde geografische gebied als het Media Services-account.

In dit artikel wordt uitgelegd hoe u de methode Snelle invoer gebruikt om een nieuw Media Services-account te maken en dit vervolgens te koppelen aan een opslagaccount.

<a id="concepts"></a>
## Concepten

Voor toegang tot Media Services zijn twee gekoppelde accounts vereist: 

-   **Een Media Services-account**. Met uw account hebt u toegang tot een set Media Services in de cloud die beschikbaar zijn in Azure. Er wordt geen echte media-inhoud opgeslagen in een Media Services-account. In plaats daarvan worden de metagegevens over de media-inhoud en taken voor de verwerking van media opgeslagen in uw account. Op het moment dat u het account maakt, selecteert u een beschikbare Media Services-regio. De regio die u selecteert, is een datacenter waarin de records met metagegevens voor uw account worden opgeslagen.

    Beschikbare Media Services-regio's (AMS) zijn onder meer: Noord-Europa, West-Europa, VS - west, VS - oost, Zuidoost-Azië, Azië, Japan - west, Japan - oost. Media Services gebruikt geen affiniteitsgroepen.
    
    AMS is nu ook beschikbaar in de volgende datacenters: Brazilië - zuid; India, westen; Zuid-India en India, midden. U kunt nu de klassieke Azure-portal gebruiken om [Media Service-accounts te maken](media-services-create-account.md#create-a-media-services-account-using-quick-create) en diverse taken uit te voeren die [hier](https://azure.microsoft.com/documentation/services/media-services/) worden beschreven. Live Encoding is echter niet ingeschakeld in deze datacenters. Bovendien zijn niet alle soorten gereserveerde coderingseenheden beschikbaar in deze datacenters.
    
    - Brazilië - zuid: alleen standaard en eenvoudige gereserveerde coderingseenheden zijn beschikbaar
    - India, westen; Zuid-India en India, midden: alleen eenvoudige gereserveerde coderingseenheden zijn beschikbaar


-   **Een gekoppeld opslagaccount**. Uw opslagaccount is een Azure-opslagaccount dat is gekoppeld aan uw Media Services-account. Het opslagaccount biedt blob-opslag voor mediabestanden en moet zich in hetzelfde geografische regio als het Media Services-account bevinden. Wanneer u een Media Services-account maakt, kunt u een bestaand opslagaccount in dezelfde regio kiezen. U kunt ook een nieuw opslagaccount maken in dezelfde regio. Als u een Media Services-account verwijdert, worden de blobs in uw gerelateerde opslagaccount niet verwijderd.

<a id="quick"></a>
## Een Media Services-account maken met Snelle invoer

1. Klik in de [klassieke Azure-portal][] achtereenvolgens op **Nieuw**, **Media Service** en **Snelle invoer**.

![Media Services voor snelle invoer](./media/media-services-create-account/wams-QuickCreate.png)

2. Voer bij **NAAM** de naam van het nieuwe account in. Voor de naam van een Media Services-account mogen alleen cijfers of kleine letters zonder spaties worden gebruikt. De naam mag 3 tot 24 tekens lang zijn.

3. Selecteer bij **REGIO** de geografische regio die wordt gebruikt om de media en metagegevensrecords voor uw Media Services-account op te slaan. Alleen de beschikbare Media Services-regio's worden in de vervolgkeuzelijst weergegeven.

4. Selecteer bij **OPSLAGACCOUNT** een opslagaccount om Blob Storage van de media-inhoud vanaf uw Media Services-account te leveren. U kunt een bestaand opslagaccount in dezelfde geografische regio als uw Media Services-account selecteren of u kunt een nieuw opslagaccount maken. Een nieuw opslagaccount wordt in dezelfde regio gemaakt.

5. Als u een nieuw opslagaccount hebt gemaakt, voert u bij **NAAM VAN NIEUW OPSLAGACCOUNT** een naam voor het opslagaccount in. De regels voor opslagaccountnamen zijn hetzelfde als voor Media Services-accounts.

6. Klik onder aan het formulier op **Snelle invoer**.

U kunt de status van het proces in het berichtgedeelte aan de onderkant van het venster bewaken.

Als het account is gemaakt, wordt de status gewijzigd in Actief. De pagina **Media Services** wordt geopend, waarop het nieuwe account wordt weergegeven.

Onder aan de pagina wordt de knop **SLEUTELS BEHEREN** weergegeven. Als u op deze knop klikt, wordt een pagina met de naam van het Media Services-account en de primaire en secundaire sleutel weergegeven. U hebt de accountnaam en de gegevens van de primaire sleutel nodig om programmatisch toegang te krijgen tot het Media Services-account.

![Media Services-pagina](./media/media-services-create-account/wams-mediaservices-page.png)

Wanneer u dubbelklikt op de accountnaam, wordt standaard de pagina **Quick Start** weergegeven. Met deze pagina kunt u bepaalde beheertaken uitvoeren die ook beschikbaar zijn op andere pagina's van de portal. U kunt bijvoorbeeld via deze pagina een videobestand uploaden of u via de pagina **INHOUD**.

Daarnaast kunt u code weergeven die gebruikmaakt van de Azure Media Services SDK om de volgende taken uit te voeren: uploaden, coderen en publiceren van video's. U kunt op een van de koppelingen onder de sectie **WRITE SOME CODE** (SCHRIJF HIER DE CODE) klikken, de code kopiëren en deze gebruiken in uw toepassing.



##Media Services-leertrajecten

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Feedback geven

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


## Volgende stappen

- [Aan de slag met het leveren van VoD-inhoud (Video-on-Demand) met .NET SDK](media-services-dotnet-get-started.md)

- [.NET SDK gebruiken om kanalen te maken die een single-bitrate stream live coderen naar een multi-bitrate stream](media-services-dotnet-creating-live-encoder-enabled-channel.md)

<!-- Reusable paths. -->

<!-- Anchors. -->
  [Concepten]: #concepts
  [Voordat u begint]: #begin
  [Procedure: een Media Services-account maken met Snelle invoer]: #quick

<!-- URLs. -->
  [Webplatforminstallatieprogramma]: http://go.microsoft.com/fwlink/?linkid=255386

  [klassieke Azure-portal]: http://manage.windowsazure.com/



<!--HONumber=Jun16_HO2-->


