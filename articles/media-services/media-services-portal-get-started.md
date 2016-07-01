<properties
    pageTitle=" Aan de slag met het leveren van inhoud op aanvraag met de klassieke Azure-portal | Microsoft Azure"
    description="In deze zelfstudie leert u een eenvoudige toepassing voor de levering van VoD-inhoud (Video-on-Demand) te implementeren met Azure Media Services door gebruik te maken van de klassieke Azure-portal."
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
    ms.date="02/25/2016"
    ms.author="juliako"/>


# Aan de slag met het leveren van inhoud op aanvraag via de klassieke Azure-portal


[AZURE.INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]


In deze zelfstudie leert u een eenvoudige toepassing voor de levering van VoD-inhoud (Video-on-Demand) te implementeren met de klassieke Azure-portal.

> [AZURE.NOTE] U hebt een Azure-account nodig om deze zelfstudie te voltooien. Zie [Gratis proefversie van Azure](/pricing/free-trial/?WT.mc_id=A261C142F) voor meer informatie. 


Deze zelfstudie bevat de volgende taken:

1.  Een Azure Media Services-account maken.
2.  Een streaming-eindpunt configureren.
1.  Een videobestand uploaden.
1.  Het bronbestand coderen in een set Adaptive Bitrate MP4-bestanden.
1.  De asset publiceren en URL's voor streamen en progressief downloaden ophalen.  
1.  Uw inhoud afspelen.


## Een Azure Media Services-account maken

1. Klik in de [klassieke Azure-portal](https://manage.windowsazure.com/) achtereenvolgens op **Nieuw**, **Media Service** en **Snelle invoer**.

    ![Media Services voor snelle invoer](./media/media-services-portal-get-started/wams-QuickCreate.png)

2. Voer bij **NAAM** de naam van het nieuwe account in. Voor de naam van een Media Services-account mogen alleen cijfers of kleine letters zonder spaties worden gebruikt. De naam mag 3 tot 24 tekens lang zijn.

3. Selecteer bij **REGIO** de geografische regio die wordt gebruikt om de media en metagegevensrecords voor uw Media Services-account op te slaan. Alleen de beschikbare Media Services-regio's worden in de vervolgkeuzelijst weergegeven.

4. Selecteer bij **OPSLAGACCOUNT** een opslagaccount om Blob Storage van de media-inhoud vanaf uw Media Services-account te leveren. U kunt een bestaand opslagaccount in dezelfde geografische regio als uw Media Services-account selecteren of u kunt een nieuw opslagaccount maken. Een nieuw opslagaccount wordt in dezelfde regio gemaakt.

5. Als u een nieuw opslagaccount hebt gemaakt, voert u bij **NAAM VAN NIEUW OPSLAGACCOUNT** een naam voor het opslagaccount in. De regels voor opslagaccountnamen zijn hetzelfde als voor Media Services-accounts.

6. Klik onder aan het formulier op **Snelle invoer**.

    U kunt de status van het proces in het berichtgedeelte aan de onderkant van het venster bewaken.

    Als het account is gemaakt, wordt de status gewijzigd in Actief.

    Onder aan de pagina wordt de knop **SLEUTELS BEHEREN** weergegeven. Als u op deze knop klikt, wordt een dialoogvenster met de naam van het Media Services-account en de primaire en secundaire sleutel weergegeven. U hebt de accountnaam en de gegevens van de primaire sleutel nodig om programmatisch toegang te krijgen tot het Media Services-account.

    ![Media Services-pagina](./media/media-services-portal-get-started/wams-mediaservices-page.png)

    Wanneer u dubbelklikt op de accountnaam, wordt standaard de pagina Quick Start weergegeven. Met deze pagina kunt u bepaalde beheertaken uitvoeren die ook beschikbaar zijn op andere pagina's van de portal. U kunt bijvoorbeeld via deze pagina een videobestand uploaden of u via de pagina INHOUD.


## Een streaming-eindpunt configureren met de portal

Wanneer er met Azure Media Services wordt gewerkt, wordt er meestal een Adaptive Bitrate Streaming aan uw clients geleverd. De client kan met Adaptive Bitrate Streaming overschakelen op een hogere of lagere bitrate stream, wanneer de video wordt weergegeven op basis van de huidige bandbreedte, het huidige CPU-gebruik en andere factoren. Media Services ondersteunt de volgende Adaptive Bitrate Streaming-technologieën: HLS (HTTP Live Streaming), Smooth Streaming, MPEG DASH en HDS (alleen voor Adobe PrimeTime/Access-licenties).

U kunt dynamische pakketten met Media Services maken, zodat u uw Adaptive Bitrate MP4-inhoud of met Smooth Streaming gecodeerde inhoud in de streaming-indelingen kunt leveren die door Media Services worden ondersteund (MPEG DASH, HLS, Smooth Streaming, HDS) zonder dat u opnieuw verpakte versies van elk van deze streaming-indelingen hoeft op te slaan.

Als u dynamische pakketten wilt gebruiken, moet u het volgende doen:

- Codeer uw tussentijds (bron)bestand in een set Adaptive Bitrate MP4-bestanden of Adaptive Bitrate Smooth Streaming-bestanden (de coderingsstappen worden verderop in deze zelfstudie uitgelegd).  
- Haal ten minste één streaming-eenheid op voor het *streaming-eindpunt* van waaruit u uw inhoud wilt leveren.

Voor dynamische pakketten hoeft u voor slechts één opslagindeling de bestanden op te slaan en hiervoor te betalen. Media Services bouwt en levert de juiste reactie op basis van aanvragen van een client.

Ga als volgt te werk als u het aantal eenheden wilt wijzigen dat voor streaming is gereserveerd:

1. Klik in de [klassieke Azure-portal](https://manage.windowsazure.com/) op **Media Services**. Klik vervolgens op de naam van de mediaservice.

2. Selecteer de pagina STREAMING-EINDPUNTEN. Klik vervolgens op het streaming-eindpunt dat u wilt wijzigen.

3. Geef het aantal streaming-eenheden op door het tabblad **SCHAAL** te selecteren en de schuifregelaar voor **gereserveerde capaciteit** te verplaatsen.

    ![De pagina Schaal](./media/media-services-portal-get-started/media-services-origin-scale.png)

4. Klik op de knop **OPSLAAN** om uw wijzigingen op te slaan.

    Het duurt ongeveer twintig minuten tot de toewijzing van nieuwe eenheden is voltooid.

    >[AZURE.NOTE] Als u op dit moment van een positieve waarde voor de streaming-eenheden naar geen streaming-eenheden gaat, kan het streamen voor maximaal één uur worden uitgeschakeld.
    >
    > Het hoogste aantal eenheden dat is opgegeven voor de periode van 24 uur, wordt gebruikt bij het berekenen van de kosten. Zie [Media Services Pricing Details](http://go.microsoft.com/fwlink/?LinkId=275107) (Informatie over Media Services-prijzen) voor informatie over prijzen.

## Inhoud uploaden


1. Klik in de [klassieke Azure-portal](http://go.microsoft.com/fwlink/?LinkID=256666&clcid=0x409) op **Media Services** en klik vervolgens op de naam van het Media Services-account.
2. Selecteer de pagina INHOUD.
3. Klik op de knop **Uploaden** op de pagina of onder aan de portal.
4. Blader in het dialoogvenster **Inhoud uploaden** naar het gewenste assetbestand. Klik op het bestand en klik vervolgens op **Openen** of druk op Enter.

    ![UploadContentDialog][uploadcontent]

5. Schakel in het dialoogvenster **Inhoud uploaden** het selectievakje in om **Bestand** en **Naam inhoud** te accepteren.
6. Het uploaden wordt gestart en u kunt onder aan de portal de voortgang volgen.  

    ![JobStatus][status]

Nadat het uploaden is voltooid, wordt de nieuwe asset weergegeven in de lijst Inhoud. Overeenkomstig de conventie wordt **-Source** aan het eind toegevoegd om nieuwe inhoud op te sporen als broninhoud voor coderingstaken.

![ContentPage][contentpage]

Als de waarde voor de bestandsgrootte niet wordt bijgewerkt nadat het uploadproces is gestopt, selecteert u de knop **Metagegevens synchroniseren**. Zo wordt de grootte van het assetbestand gesynchroniseerd met de werkelijke bestandsgrootte in de opslag en wordt de waarde op de pagina Inhoud vernieuwd.


## Inhoud coderen

### Overzicht

Als u digitale video via internet wilt leveren, moet u de media comprimeren. Media Services biedt een mediacoderingsprogramma waarmee u kunt opgeven hoe uw inhoud moeten worden gecodeerd (bijvoorbeeld welke codecs moeten worden gebruikt, de bestandsindeling, de resolutie en de bitsnelheid.)

Wanneer er met Azure Media Services wordt gewerkt, wordt er meestal een Adaptive Bitrate Streaming aan uw clients geleverd. De client kan met Adaptive Bitrate Streaming overschakelen op een hogere of lagere bitrate stream, wanneer de video wordt weergegeven op basis van de huidige bandbreedte, het huidige CPU-gebruik en andere factoren. Media Services ondersteunt de volgende Adaptive Bitrate Streaming-technologieën: HLS (HTTP Live Streaming), Smooth Streaming, MPEG DASH en HDS (alleen voor Adobe PrimeTime/Access-licenties).

U kunt dynamische pakketten met Media Services maken, zodat u uw Adaptive Bitrate MP4-inhoud of met Smooth Streaming gecodeerde inhoud in de streaming-indelingen kunt leveren die door Media Services worden ondersteund (MPEG DASH, HLS, Smooth Streaming of HDS) zonder dat u opnieuw verpakte versies van elk van deze streaming-indelingen hoeft op te slaan.

Als u dynamische pakketten wilt gebruiken, moet u het volgende doen:

- Codeer uw tussentijds (bron)bestand in een set Adaptive Bitrate MP4-bestanden of Adaptive Bitrate Smooth Streaming-bestanden (de coderingsstappen worden verderop in deze zelfstudie uitgelegd).
- Haal ten minste één eenheid voor streaming on demand op voor het streaming-eindpunt van waaruit u uw inhoud wilt leveren. Zie [How to scale On-Demand Streaming reserved units](media-services-manage-origins.md#scale_streaming_endpoints/) (Gereserveerde eenheden voor steaming on demand schalen) voor meer informatie.

Voor dynamische pakketten hoeft u voor slechts één opslagindeling de bestanden op te slaan en hiervoor te betalen. Media Services bouwt en levert de juiste reactie op basis van aanvragen van een client.

Naast de mogelijkheden voor dynamische pakketten, bieden de gereserveerde eenheden voor streaming on demand u een speciale uitgangscapaciteit die kan worden aangeschaft per 200 Mbps. Streaming on demand wordt standaard geconfigureerd in een gedeelde-instantiemodel waarvoor de serverresources (bijvoorbeeld de berekenings- of uitgangscapaciteit) worden gedeeld met alle andere gebruikers. Als u de doorvoer voor streaming on demand wilt verbeteren, kunt u het beste gereserveerde eenheden voor streaming on demand aanschaffen.

### Coderen

In deze sectie wordt beschreven hoe u uw inhoud codeert met Media Encoder Standard door gebruik te maken van de klassieke Azure-portal.

1.  Selecteer het bestand dat u wilt coderen.
    Als de codering wordt ondersteund voor dit bestandstype, wordt de knop **VERWERKEN** ingeschakeld onder aan de pagina INHOUD.
4. Selecteer in het dialoogvenster **Proces** de **standaardprocessor voor Media Encoder**.
5. Kies een van de **coderingsconfiguraties**.

    ![Process2][process2]

    In het onderwerp [Task Preset Strings for Media Encoder Standard](https://msdn.microsoft.com/en-US/library/mt269960) (Vooraf ingestelde tekenreeksen voor taken in Media Encoder Standard) wordt uitgelegd wat elke standaardinstelling betekent.  

5. Voer vervolgens de gewenste beschrijvende naam voor de uitvoerinhoud in of accepteer de standaardnaam. Klik vervolgens op het selectievakje om de coderingsbewerking te starten. U kunt de voortgang bijhouden aan de onderkant van de portal.
6. Selecteer **OK**.

    Nadat de codering is voltooid, bevat de pagina INHOUD het gecodeerde bestand.

    Als u de voortgang van de coderingstaak wilt weergeven, schakelt u naar de pagina **JOBS**.  

    Als de waarde voor de bestandsgrootte niet wordt bijgewerkt nadat de codering is voltooid, selecteert u de knop **Metagegevens synchroniseren**. Zo wordt de grootte van het uitgevoerde assetbestand gesynchroniseerd met de werkelijke bestandsgrootte in de opslag en wordt de waarde op de pagina Inhoud vernieuwd.


## Inhoud publiceren

### Overzicht

Als u uw gebruiker een URL wilt leveren die kan worden gebruikt om uw inhoud te streamen of te downloaden, moet u uw asset eerst 'publiceren' door een locator te maken. Locators bieden toegang tot bestanden in de asset. Media Services ondersteunt twee typen locators: OnDemandOrigin-locators, voor het streamen van media (bijvoorbeeld MPEG DASH, HLS, of Smooth Streaming) en SAS-locators (Shared Access Signature), voor het downloaden van media-bestanden.

Wanneer u de klassieke Azure-beheerportal gebruikt om uw assets te publiceren, worden de locators voor u gemaakt en ontvangt u een OnDemand-URL (als uw asset een ISM-bestand bevat) of een SAS-URL.

Een SAS-URL heeft de volgende indeling.

    {blob container name}/{asset name}/{file name}/{SAS signature}

Een streaming-URL heeft de volgende indeling. U kunt de streaming-URL gebruiken om Smooth Streaming-assets af te spelen.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Als u een streaming-URL voor HLS wilt maken, voegt u (format=m3u8-aapl) toe aan de URL.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Als u een streaming-URL voor MPEG DASH wilt maken, voegt u (format=mpd-time-csf) toe aan de URL.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


Locators hebben een vervaldatum. Wanneer u de portal gebruikt om uw assets te publiceren, worden er locators gemaakt met een vervaldatum van 100 jaar.

>[AZURE.NOTE] Als u de portal hebt gebruikt om locators te maken vóór maart 2015, zijn locators met een vervaldatum van twee jaar gemaakt.  

Als u de vervaldatum van een locator wilt bijwerken, gebruikt u [REST](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator )- of [.NET](http://go.microsoft.com/fwlink/?LinkID=533259)-API's. Wanneer u de vervaldatum van een SAS-locator bijwerkt, wordt de URL gewijzigd.

### Publiceren

Als u de portal wilt gebruiken om een asset te publiceren, gaat u als volgt te werk:

1. Selecteer de asset.
2. Klik op de knop voor publiceren.

 ![PublishedContent][publishedcontent]


## Inhoud afspelen vanuit de portal

De klassieke Azure-portal biedt een speler voor het afspelen van inhoud die u kunt gebruiken om uw video te testen.

Klik op de gewenste video en klik vervolgens op de knop **Afspelen** onder aan de portal.

Hierbij geldt het volgende:

- De video moet zijn gepubliceerd.
- **MEDIA SERVICES CONTENT PLAYER** speelt af vanaf het standaard streaming-eindpunt. Als u wilt afspelen vanaf een ander streaming-eindpunt, gebruikt u een andere speler. Bijvoorbeeld [Azure Media Services Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html).


![AMSPlayer][AMSPlayer]



##Volgende stappen: Media Services-leertrajecten

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Feedback geven

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## Zoekt u iets anders?

Als dit onderwerp niet de informatie bevat die u verwacht, er iets ontbreekt of het onderwerp op een andere manier niet aan uw behoeften voldoet, kunt u ons via de onderstaande Disqus-thread feedback geven.

### Aanvullende resources
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-101-Get-your-video-online-now-">Azure Media Services 101: uw video's nu online plaatsen.</a>
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-102-Dynamic-Packaging-and-Mobile-Devices">Azure Media Services 102: dynamische pakketten en mobiele apparaten</a>


<!-- Anchors. -->


<!-- URLs. -->
[Klassieke Azure-portal]: http://manage.windowsazure.com/


<!-- Images -->
[portaloverview]: ./media/media-services-portal-get-started/media-services-content-page.png
[publishedcontent]: ./media/media-services-portal-get-started/media-services-upload-content-published.png
[uploadcontent]: ./media/media-services-portal-get-started/UploadContent.png
[status]: ./media/media-services-portal-get-started/Status.png
[encoder]: ./media/media-services-manage-content/EncoderDialog2.png
[branding]: ./media/branding-reporting.png
[contentpage]: ./media/media-services-portal-get-started/media-services-content-page.png
[process]: ./media/media-services-manage-content/media-services-process-video.png
[process2]: ./media/media-services-portal-get-started/media-services-process-video2.png
[encrypt]: ./media/media-services-manage-content/media-services-encrypt-content.png
[AMSPlayer]: ./media/media-services-portal-get-started/media-services-portal-player.png



<!--HONumber=Jun16_HO2-->


