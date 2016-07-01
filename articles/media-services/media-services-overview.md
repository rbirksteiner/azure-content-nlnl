<properties 
    pageTitle="Overzicht van Azure Media Services en algemene scenario's" 
    description="Dit onderwerp bevat een overzicht van Azure Media Services" 
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
    ms.topic="hero-article" 
    ms.date="05/03/2016" 
    ms.author="juliako;anilmur"/>

#Overzicht van Azure Media Services en algemene scenario's

Microsoft Azure Media Services is een uitbreidbaar cloudplatform waarmee ontwikkelaars schaalbare toepassingen voor mediabeheer en -levering kunnen ontwikkelen. Media Services is gebaseerd op de REST API's waarmee u veilig video- of audio-inhoud kunt uploaden, opslaan, coderen en verpakken, zowel voor levering on demand als levering via livestreaming aan verschillende clients (bijvoorbeeld tv, pc en mobiele apparaten).

U kunt end-to-end-werkstromen volledig met Media Services bouwen. U kunt er ook voor kiezen om onderdelen van derde partijen voor sommige onderdelen van uw werkstroom te gebruiken. U kunt bijvoorbeeld coderen met een coderingsprogramma van een derde partij. Vervolgens kunt u uploaden, beveiligen, verpakken en leveren met Media Services.

U kunt uw inhoud live streamen of on demand leveren. Dit onderwerp bevat algemene scenario's voor het leveren van uw inhoud: [live](media-services-overview.md#live_scenarios) of [on demand](media-services-overview.md#vod_scenarios). Het onderwerp bevat ook koppelingen naar andere relevante onderwerpen.

## SDK's en hulpprogramma's 

Als u Media Services-oplossingen wilt maken, kunt u het volgende gebruiken:

- [Media Services REST API](https://msdn.microsoft.com/library/azure/hh973617.aspx)
- Een van de beschikbare client-SDK's: 
    - [Azure Media Services SDK voor .NET](https://github.com/Azure/azure-sdk-for-media-services) 
    - [Azure SDK voor Java](https://github.com/Azure/azure-sdk-for-java) 
    - [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php) 
    - [Azure Media Services voor Node.js](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js) (Dit is een niet-Microsoft-versie van een Node.js SDK. Deze wordt onderhouden door een community en biedt nog geen 100% dekking voor AMS API's). 
- Bestaande hulpprogramma's: 
    - [Klassieke Azure-portal](http://manage.windowsazure.com/) 
    - [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (Azure Media Services Explorer (AMSE) is een Winforms-/C#-toepassing voor Windows)

##Media Services-leertrajecten

U kunt hier de AMS-leertrajecten bekijken:

- [Werkstroom voor AMS Live Streaming](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [Werkstroom voor AMS On Demand Streaming](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

##Vereisten

Als u Azure Media Services wilt gaan gebruiken, moet u over het volgende beschikken:
 
3. Een Azure-account. Als u geen account hebt, kunt u binnen een paar minuten een account voor de gratis proefversie maken. Zie [Gratis proefversie van Azure](azure.microsoft.com) voor meer informatie.
2. Een Azure Media Services-account. Gebruik de klassieke Azure-portal, .NET of REST API om een Azure Media Services-account te maken. Zie [Een account maken](media-services-create-account.md) voor meer informatie.
3. (Optioneel) Instellen van de ontwikkelomgeving. Kies .NET of REST API voor uw ontwikkelomgeving. Zie [De omgeving instellen](media-services-dotnet-how-to-use.md) voor meer informatie. 

    Leer ook hoe u via een programma [verbinding kunt maken](media-services-dotnet-connect-programmatically.md).
4. (Aanbevolen) Wijs een of meer schaaleenheden toe. U kunt het beste een of meer schaaleenheden toewijzen voor toepassingen in de productieomgeving.   Zie [Streaming-eindpunten beheren](media-services-manage-origins.md) voor meer informatie.

##Concepten en overzicht

Zie [Concepten](media-services-concepts.md) voor Azure Media Services-concepten.

Zie [Stapsgewijze zelfstudies voor Azure Media Services](https://docs.com/fukushima-shigeyuki/3439/english-azure-media-services-step-by-step-series) voor een reeks procedures waarin u kennis kunt maken met de belangrijkste onderdelen van Azure Media Services. Deze reeks biedt een goed overzicht van de concepten en maakt gebruik van het AMSE-hulpprogramma om de AME-taken te demonstreren. Het AMSE-hulpprogramma is een Windows-hulpprogramma. Dit hulpprogramma ondersteunt de meeste taken die u programmatisch kunt uitvoeren met [AMS SDK voor .NET](https://github.com/Azure/azure-sdk-for-media-services), [Azure SDK voor Java](https://github.com/Azure/azure-sdk-for-java) of [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php).

##<a id="vod_scenarios"></a>On-demand media leveren met Azure Media Services: algemene scenario's en taken

In deze sectie worden algemene scenario's beschreven en vindt u koppelingen naar relevante onderwerpen. Het volgende diagram toont u de belangrijkste onderdelen van het Media Services-platform die zijn betrokken bij de on-demand levering van inhoud. 

![VoD-werkstroom](./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png)


###Inhoud in de opslag beveiligen en niet-versleutelde streamingmedia leveren

1. Upload een tussentijds bestand van hoge kwaliteit naar een asset.
    
    Het is raadzaam de optie voor opslagversleuteling toe te passen op de uitvoerasset om de inhoud in de opslag te beveiligen.
 
1. Codeer de assets als een set Adaptive Bitrate MP4-bestanden. 

    Het is raadzaam de optie voor opslagversleuteling toe te passen op de uitvoerasset om de opgeslagen inhoud te beveiligen.
    
1. Configureer het beleid voor de levering van assets (gebruikt voor dynamische pakketten). 
    
    Als de opslag van uw asset is versleuteld, **moet** u een beleid voor assetlevering configureren. 

1. Publiceer de asset door een OnDemand-locator te maken.

    Zorg ervoor dat u ten minste één gereserveerde eenheid streaming hebt op het streaming-eindpunt vanaf waar u de inhoud wilt streamen.

1. Stream de gepubliceerde inhoud.

###De inhoud in de opslag beveiligen, dynamisch versleutelde streamingmedia leveren  

Als u dynamische versleuteling wilt gebruiken, moet u eerst ten minste één gereserveerde eenheid streaming ophalen van het streaming-eindpunt van waaruit u versleutelde inhoud wilt streamen.

1. Upload een tussentijds bestand van hoge kwaliteit naar een asset. Pas de optie voor opslagversleuteling toe op de asset.
1. Codeer de assets als een set Adaptive Bitrate MP4-bestanden. Pas de optie voor opslagversleuteling toe op de uitvoerasset.
1. Maak een inhoudssleutel voor de versleuteling van de asset die tijdens het afspelen dynamisch moet worden versleuteld.
2. Configureer het autorisatiebeleid voor de inhoudssleutel.
1. Configureer het beleid voor de levering van assets (gebruikt door dynamische pakketten en dynamische versleuteling).
1. Publiceer de asset door een OnDemand-locator te maken.
1. Stream de gepubliceerde inhoud. 

###Media Analytics gebruiken om inzichten aan uw video's te ontlenen waarvoor een actie kan worden uitgevoerd 

Media Analytics is een verzameling spraakonderdelen en visuele onderdelen waarmee organisaties en bedrijven gemakkelijker inzichten aan hun video's kunnen ontlenen waarvoor een actie kan worden uitgevoerd. Zie [Overzicht van Azure Media Services Analytics](media-services-analytics-overview.md) voor meer informatie.

1. Upload een tussentijds bestand van hoge kwaliteit naar een asset.
2. Gebruik een van de volgende Media Analytics-services voor de verwerking van uw video:
    
    - **Indexeerfunctie**: [video's verwerken met Azure Media Indexer 2](media-services-process-content-with-indexer2.md)
    - **Hyperlapse**: [Hyperlapse Media-bestanden met Azure Media Hyperlapse](media-services-hyperlapse-content.md)
    - **Bewegingsdetectie**: [bewegingsdetectie voor Azure Media Analytics](media-services-motion-detection.md).
    - **Gezichtsdetectie en gezichtsemoties**: [gezichts- en emotiedetectie voor Azure Media Analytics](media-services-face-and-emotion-detection.md).
    - **Samenvatting van de video**: [gebruik Azure Media Video Thumbnails om een samenvatting van een video te maken](media-services-video-summarization.md)
3. Media Analytics-mediaprocessoren produceren MP4- of JSON-bestanden. Als een Mediaprocessor een MP4-bestand produceert, kunt u het bestand progressief downloaden. Als een mediaprocessor een JSON-bestand produceert, kunt u het bestand downloaden via Azure Blob Storage. 


###Een progressieve download leveren 

1. Upload een tussentijds bestand van hoge kwaliteit naar een asset.
1. Codeer het bestand naar één MP4-bestand.
1. Publiceer de asset door een OnDemand- of SAS-locator te maken.

    Als u een OnDemand-locator gebruikt, moet u ervoor zorgen dat u over minimaal één gereserveerde eenheid streaming beschikt op het streaming-eindpunt van waaruit u van plan bent de inhoud progressief te downloaden.

    Als SAS-locator wordt gebruikt, wordt de inhoud gedownload vanaf Azure Blob Storage. In dit geval hebt u geen gereserveerde streamingeenheden nodig.
  
1. Download de inhoud op progressieve wijze.

##<a id="live_scenarios"></a>Live Streaming-gebeurtenissen met Azure Media Services leveren

Wanneer u met Live Streaming werkt, maakt u doorgaans gebruik van de volgende onderdelen:

- Een camera die wordt gebruikt voor het uitzenden van een gebeurtenis.
- Een coderingsprogramma voor live video dat de signalen van de camera converteert naar stromen die worden verzonden naar een service voor live streamen.

    Eventueel meerdere op tijd gesynchroniseerde coderingsprogramma’s. Voor bepaalde kritieke live gebeurtenissen die een zeer hoge beschikbaarheid en kwaliteit vereisen, kunt u het beste redundante coderingsprogramma’s (actief/actief) met tijdsynchronisatie gebruiken voor een naadloze failover zonder verlies van gegevens.
- Een service voor live streamen waarmee u het volgende kunt doen:
    
    - Live-inhoud met verschillende protocollen voor live streamen opnemen (bijvoorbeeld RTMP of Smooth Streaming).
    - (Optioneel) Uw stream coderen in een stream met een adaptieve bitsnelheid.
    - Een voorbeeld van uw livestream bekijken.
    - De opgenomen inhoud vastleggen en opnemen om deze later te streamen (Video-on-Demand).
    - De inhoud rechtstreeks aan uw klanten leveren via algemene protocollen voor streaming (MPEG DASH, Smooth, HLS, HDS) of aan een netwerk voor contentlevering (CDN) voor verdere distributie.


Met **Microsoft Azure Media Services** (AMS) kunt u de inhoud van uw live-streaming opnemen, coderen, een voorbeeld bekijken, opslaan en leveren.

Als u uw inhoud levert aan klanten, is het uw doel een video van hoge kwaliteit te leveren aan verschillende apparaten met verschillende netwerkomstandigheden. Gebruik voor de kwaliteit en netwerkomstandigheden live coderingen om uw stream te coderen voor een multi-bitrate (adaptieve bitrate) videostream.  Als u op verschillende apparaten wilt streamen, gebruikt u de [dynamische pakketten](media-services-dynamic-packaging-overview.md) van Media Services om uw stream met verschillende protocollen op dynamische wijze opnieuw toe te voegen aan een pakket. Media Services ondersteunt de levering van de volgende Adaptive Bitrate Streaming-technologieën: HLS (HTTP Live Streaming), Smooth Streaming, MPEG DASH en HDS (alleen voor Adobe PrimeTime/Access-licenties).

In Azure Media Services wordt alle functionaliteiten voor live streamen afgehandeld door **kanalen**, **programma's** en **streaming-eindpunten**, waaronder opnemen, opmaken, DVR, beveiliging, schaalbaarheid en redundantie.

Een **kanaal** vertegenwoordigt een pijplijn voor de verwerking van inhoud voor live-streaming. Een kanaal kan op de volgende manieren live invoerstromen ontvangen:

- Een on-premises live codering verzendt multi-bitrate **RTMP** of **Smooth Streaming** (gefragmenteerd MP4) naar het kanaal dat is geconfigureerd voor **passthrough**-levering. **Passthrough**-levering vindt plaats wanneer de opgenomen streams het **kanaal** passeren zonder verdere verwerking. U kunt de volgende live coderingsprogramma’s gebruiken die multi-bitrate Smooth Streaming uitvoeren: Elemental, Envivio, Cisco.  De volgende live coderingsprogramma’s voeren RTMP uit: Adobe Flash Live-, Telestream Wirecast- en Tricaster-transcoders.  Een live coderingsprogramma kan ook een stream met één bitsnelheid verzenden naar een kanaal dat niet is ingeschakeld voor Live Encoding, maar dit wordt niet aanbevolen. Desgevraagd levert Media Services de stream aan klanten.

    >[AZURE.NOTE] Het gebruik van de passthrough-methode is de meest voordelige manier om live te streamen wanneer u meerdere gebeurtenissen gedurende een langere periode streamt en u al hebt geïnvesteerd in on-premises coderingsprogramma’s. Zie de details over de [prijzen](/pricing/details/media-services/).
    
- Een on-premises live codering verzendt een stream met één bitsnelheid naar het kanaal dat is ingeschakeld voor het uitvoeren van de live codering met Media Services in een van de volgende indelingen: RTP (MPEG-TS), RTMP of Smooth Streaming (gefragmenteerde MP4). Het kanaal codeert de inkomende single-bitrate stream vervolgens live naar een (adaptieve) multi-bitrate videostream. Desgevraagd levert Media Services de stream aan klanten.


###Werken met kanalen die een multi-bitrate livestream van on-premises encoders ontvangen (pass-through)

Het volgende diagram toont de belangrijkste onderdelen van het AMS-platform die betrokken zijn bij de **passthrough**-werkstroom.

![Live-werkstroom][live-overview2]

Zie [Werken met kanalen die een multi-bitrate livestream van on-premises coderingsprogramma’s ontvangen](media-services-live-streaming-with-onprem-encoders.md) voor meer informatie.

###Werken met kanalen waarmee Live Encoding kan worden uitgevoerd met Azure Media Services

Het volgende diagram toont de belangrijke onderdelen van het AMS-platform die betrokken zijn bij de Live Streaming-werkstroom waarbij een kanaal live codering kan uitvoeren met Media Services.

![Live-werkstroom][live-overview1]

Zie [Werken met kanalen waarmee Live Encoding kan worden uitgevoerd met Azure Media Services](media-services-manage-live-encoder-enabled-channels.md) voor meer informatie.


##Inhoud gebruiken

Azure Media Services biedt de hulpprogramma's die u nodig hebt om geavanceerde, dynamische clientspelertoepassingen te maken voor de meeste platforms, waaronder: iOS-apparaten, Android-apparaten, Windows, Windows Phone, Xbox en settopboxen. In dit onderwerp vindt u koppelingen naar SDK's en frameworks voor spelers die u kunt gebruiken om uw eigen clienttoepassingen te ontwikkelen die kunnen worden gebruikt voor streamingmedia van Media Services.

[Videospelertoepassingen ontwikkelen](media-services-develop-video-players.md)

##Azure CDN inschakelen

Media Services ondersteunt de integratie met Azure CDN. Zie [Streaming-eindpunten in een Media Services-account beheren](media-services-manage-origins.md#enable_cdn) voor meer informatie over het inschakelen van Azure CDN.

##Een Media Services-account schalen

U kunt **Media Services** schalen door het aantal **gereserveerde streaming-eenheden** en **gereserveerde coderingseenheden ** op te geven waarmee u uw account wilt inrichten.

U kunt uw Media Services-account schalen door opslagaccounts toe te voegen. Elk opslagaccount is beperkt tot 500 TB. Als u uw opslag wilt uitbreiden buiten de standaardbeperkingen, kunt u meerdere opslagaccounts aan een enkel Media Services-account koppelen.

[Dit](media-services-how-to-scale.md) onderwerp bevat koppelingen naar relevante onderwerpen.

##Ondersteuning

[Ondersteuning van Azure](https://azure.microsoft.com/support/options/) biedt ondersteuningsopties voor Azure, met inbegrip van Media Services.

##Feedback geven

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Service Level Agreement (SLA)

- Voor Media Services-codering wordt een beschikbaarheid van 99,9% voor REST API-transacties gegarandeerd.
- Voor streaming worden de aanvragen beantwoord met een beschikbaarheidsgarantie van 99,9% voor bestaande media-inhoud wanneer er minimaal één streaming-eenheid is gekocht.
- Voor livekanalen wordt gegarandeerd dat de actieve kanalen minimaal 99,9% van de tijd over een externe verbinding beschikken.
- Voor Content Protection wordt gegarandeerd dat de sleutelaanvragen minimaal 99,9% van de tijd worden vervuld.
- Voor de indexeerfunctie worden de aanvragen voor indexeertaken 99,9% van de tijd verwerkt met een gereserveerde eenheid codering.

Zie [Microsoft Azure SLA](https://azure.microsoft.com/support/legal/sla/) voor meer informatie.

<!-- Images -->
[overview]: ./media/media-services-overview/media-services-overview.png
[vod-overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png
[live-overview1]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png
[live-overview2]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png
 



<!--HONumber=Jun16_HO2-->


