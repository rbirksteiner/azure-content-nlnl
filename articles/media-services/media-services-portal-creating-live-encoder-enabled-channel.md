<properties 
    pageTitle="Live streamen met Azure Media Services om multi-bitrate streams te maken met de klassieke Azure-portal" 
    description="In deze zelfstudie wordt u begeleid bij de stappen voor het maken van een kanaal dat een single-bitrate live stream ontvangt, en het coderen van deze stream naar een multi-bitrate stream met de klassieke Azure-portal." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako,anilmur" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article"
    ms.date="05/05/2016" 
    ms.author="juliako"/>


#Live streamen met Azure Media Services om multi-bitrate streams te maken met de klassieke Azure-portal

> [AZURE.SELECTOR]
- [Portal](media-services-portal-creating-live-encoder-enabled-channel.md)
- [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
- [REST API](https://msdn.microsoft.com/library/azure/dn783458.aspx)

In deze zelfstudie wordt u begeleid bij de stappen voor het maken van een **kanaal** dat een single-bitrate livestream ontvangt, en het coderen van deze stream naar een multi-bitrate stream.

>[AZURE.NOTE]Zie [Live streamen met Azure Media Services om multi-bitrate streams te maken](media-services-manage-live-encoder-enabled-channels.md) voor meer conceptuele informatie over kanalen die zijn ingeschakeld voor live codering.

##Algemeen scenario voor live streamen

Hieronder volgen de algemene stappen voor het maken van veelvoorkomende toepassingen voor live streamen.

>[AZURE.NOTE] De maximum aanbevolen duur van een live gebeurtenis is momenteel acht uur. Neem contact op met amslived op Microsoft.com als u een kanaal voor langere tijd wilt uitvoeren.

1. Sluit een videocamera aan op een computer. Start en configureer een on-premises livecoderingsprogramma dat een single-bitrate stream in een van de volgende protocollen kan uitvoeren: RTMP, Smooth Streaming of RTP (MPEG-TS). Zie [Azure Media Services RTMP-ondersteuning en live coderingsprogramma's](http://go.microsoft.com/fwlink/?LinkId=532824) voor meer informatie.
    
    Deze stap kan ook worden uitgevoerd nadat u uw kanaal hebt gemaakt.

1. Maak en start een kanaal. 

1. Haal de URL voor opnemen voor het kanaal op. 

    De URL voor opnemen wordt gebruikt door het live coderingsprogramma om de stream naar het kanaal te verzenden.
1. Haal de voorbeeld-URL voor het kanaal op. 

    Gebruik deze URL om te controleren of de livestream goed door het kanaal wordt ontvangen.

3. Maak een programma (hierbij wordt ook een asset gemaakt). 
1. Publiceer het programma (hierbij wordt ook een OnDemand-locator voor de gekoppelde asset gemaakt).  

    Zorg ervoor dat u ten minste één gereserveerde eenheid streaming hebt op het streaming-eindpunt vanaf waar u de inhoud wilt streamen.
1. Start het programma wanneer u klaar bent om te streamen en te archiveren.
2. Het live coderingsprogramma kan desgewenst een signaal ontvangen dat een advertentie moet worden gestart. De advertentie wordt ingevoegd in de uitvoerstream.
1. Stop het programma als u het streamen wilt stoppen en de gebeurtenis wilt archiveren.
1. Verwijder het programma (en verwijder desgewenst de asset).   

##In deze zelfstudie

In deze zelfstudie wordt de klassieke Azure-portal gebruikt om de volgende taken uit te voeren: 

2.  Configureer streaming-eindpunten.
3.  Maak een kanaal dat is ingeschakeld voor het uitvoeren van live codering.
1.  Haal de URL voor opnemen op om deze aan het live coderingsprogramma te leveren. Het live coderingsprogramma gebruikt deze URL om de stream in het kanaal op te nemen. .
1.  Een programma (en een asset) maken
1.  De asset publiceren en streaming-URL's ophalen  
1.  Uw inhoud afspelen 
2.  Opschonen

##Vereisten
Hieronder wordt aangegeven wat de vereisten zijn om de zelfstudie te voltooien.

- U hebt een Azure-account nodig om deze zelfstudie te voltooien. Als u geen account hebt, kunt u binnen een paar minuten een account voor de gratis proefversie maken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie.
- Een Media Services-account. Zie [Account maken](media-services-create-account.md) voor meer informatie over het maken van een Media Services-account.
- Een webcam en een coderingsprogramma dat een single bitrate livestream kan verzenden.

##Een streaming-eindpunt configureren met Portal

Wanneer er met Azure Media Services wordt gewerkt, wordt er meestal een Adaptive Bitrate Streaming aan uw clients geleverd. De client kan met Adaptive Bitrate Streaming overschakelen op een hogere of lagere bitrate stream, wanneer de video wordt weergegeven op basis van de huidige bandbreedte, het huidige CPU-gebruik en andere factoren. Media Services ondersteunt de volgende Adaptive Bitrate Streaming-technologieën: HLS (HTTP Live Streaming), Smooth Streaming, MPEG DASH en HDS (alleen voor Adobe PrimeTime/Access-licenties). 

Als u met live streamen werkt, wordt er met een on-premises live coderingsprogramma (in ons geval Wirecast) een multi-bitrate live stream in het kanaal opgenomen. Wanneer de stream door een gebruiker wordt aangevraagd, gebruikt Media Services dynamische pakketten om de bronstream opnieuw te verpakken in de aangevraagde adaptieve bitrate stream (HLS, DASH of Smooth). 

Als u dynamische pakketten wilt gebruiken, moet u ten minste één streaming-eenheid hebben voor het **streaming-eindpunt** van waaruit u uw inhoud wilt leveren.

Ga als volgt te werk als u het aantal eenheden wilt wijzigen dat voor streaming is gereserveerd:

1. Klik in de [klassieke Azure-portal](https://manage.windowsazure.com/) op **Media Services**. Klik vervolgens op de naam van de mediaservice.

2. Selecteer de pagina STREAMING-EINDPUNTEN. Klik vervolgens op het streaming-eindpunt dat u wilt wijzigen.

3. Geef het aantal streaming-eenheden op door het tabblad SCHAAL te selecteren en de schuifregelaar voor **gereserveerde capaciteit** te verplaatsen.

    ![De pagina Schaal](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-origin-scale.png)

4. Klik op de knop OPSLAAN om uw wijzigingen op te slaan.

    Het duurt ongeveer twintig minuten tot de toewijzing van nieuwe eenheden is voltooid. 

     
    >[AZURE.NOTE] Als u op dit moment van een positieve waarde voor de streaming-eenheden naar geen streaming-eenheden gaat, kan het streamen voor maximaal één uur worden uitgeschakeld.
    >
    > Het hoogste aantal eenheden dat is opgegeven voor de periode van 24 uur, wordt gebruikt bij het berekenen van de kosten. Zie [Media Services Pricing Details](http://go.microsoft.com/fwlink/?LinkId=275107) (Informatie over Media Services-prijzen) voor informatie over prijzen.

 
##Een KANAAL maken

1.  Klik in de [klassieke Azure-portal](http://manage.windowsazure.com/) op Media Services en klik vervolgens op de naam van het Media Services-account.
2.  Selecteer de pagina KANALEN.
3.  Selecteer Toevoegen+ om een nieuw kanaal toe te voegen.

Kies het coderingstype **Standaard**. U geeft met dit type op dat u een kanaal wilt maken dat voor live codering is ingeschakeld. Dit betekent dat de binnenkomende single-bitrate stream naar het kanaal wordt verzonden en naar een multi-bitrate stream wordt gecodeerd met de opgegeven instellingen van het live coderingsprogramma. Zie [Live streamen met Azure Media Services om multi-bitrate streams te maken](media-services-manage-live-encoder-enabled-channels.md) voor meer informatie.

![standard0][standard0]

Geldige opnameprotocolopties voor het coderingstype **Standaard** zijn:

- Single-bitrate Fragmented MP4 (Smooth Streaming);
- Single-bitrate RTMP;
- RTP (MPEG-TS): MPEG-2-transportstroom via RTP.

Zie [Live streamen met Azure Media Services om multi-bitrate streams te maken](media-services-manage-live-encoder-enabled-channels.md) voor een gedetailleerde beschrijving van de protocollen.

![standard1][standard1]

U kunt het invoerprotocol niet wijzigen terwijl het kanaal of de gekoppelde programma's worden uitgevoerd. Als u verschillende protocollen nodig hebt, maakt u afzonderlijke kanalen voor elk invoerprotocol.  

U kunt op de pagina **Advertising Configuration** (Reclameconfiguratie) de bron voor advertentiemarkeringssignalen opgeven. Wanneer u Portal gebruikt, kunt u alleen API selecteren. Hiermee wordt aangegeven dat het live coderingsprogramma binnen het kanaal moet luisteren naar een asynchrone advertentiemarkerings-API. Wanneer u Portal gebruikt, kunt u alleen API selecteren.

Zie [Live streamen met Azure Media Services om multi-bitrate streams te maken](media-services-manage-live-encoder-enabled-channels.md) voor meer informatie.

![standard2][standard2]

U kunt op de pagina **Encoding Preset** (Vooraf gedefinieerde instellingen voor codering) vooraf ingestelde systeemwaarden selecteren. Momenteel kunt u alleen de vooraf ingestelde systeemwaarde **Default 720p** (Standaard 720p) selecteren.

![standard3][standard3]

Op de pagina **Channel Creation** (Kanaal maken) definieert u de IP-adressen die video naar dit kanaal mogen publiceren. Toegestane IP-adressen kunnen worden opgegeven als een enkel IP-adres (bijvoorbeeld 10.0.0.1), een IP-adresbereik met een IP-adres en een CIDR-subnetmasker (bijvoorbeeld 10.0.0.1/22) of een IP-adresbereik met een IP-adres en een decimaal subnetmasker met punten (bijvoorbeeld 10.0.0.1(255.255.252.0)).

Als geen IP-adressen zijn opgegeven en er geen regeldefinitie bestaat, zijn er geen IP-adressen toegestaan. Als u IP-adres(sen) wilt toestaan, maakt u een regel en stelt u 0.0.0.0/0 in.


![standard4][standard4]

>[AZURE.NOTE] Momenteel kan het tot dertig minuten duren voordat het kanaal wordt gestart. Het opnieuw instellen van een kanaal kan tot vijf minuten duren.

Als u het kanaal hebt gemaakt, kunt u het tabblad **CODERINGSPROGRAMMA** selecteren waar u de configuraties van de kanalen kunt bekijken. U kunt ook advertenties en slates beheren. 

![standard5][standard5]

Zie [Live streamen met Azure Media Services om multi-bitrate streams te maken](media-services-manage-live-encoder-enabled-channels.md) voor meer informatie.


##URL’s voor opnemen ophalen

Wanneer het kanaal is gemaakt, kunt u URL’s voor opnemen ophalen die u aan het live coderingsprogramma levert. Het coderingsprogramma gebruikt deze URL's voor het invoeren van een live stream.

![readychannel](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-ready-channel.png)


![ingesturls](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-ingest-urls.png)


##Een programma maken en beheren

###Overzicht

Een kanaal is gekoppeld aan programma's waarmee u het publiceren en opslaan van segmenten in een live stream kunt beheren. Kanalen beheren programma's. De kanaal-/programmarelatie lijkt erg op traditionele media waarbij een kanaal een constante stream met inhoud heeft en een programma is afgestemd op een bepaalde getimede gebeurtenis op dat kanaal.

U kunt het aantal uren opgeven dat u de opgenomen inhoud voor het programma wilt behouden door de lengte voor **Archiefvenster** in te stellen. Deze waarde kan worden ingesteld van minimaal 5 minuten tot maximaal 25 uur. De lengte van een archiefvenster bepaalt ook de maximale hoeveelheid tijd die clients terug in de tijd kunnen zoeken vanaf de huidige live positie. Programma's kunnen in de opgegeven tijdsduur worden uitgevoerd, maar de inhoud die achter de lengte van het venster valt, wordt altijd verwijderd. De waarde van deze eigenschap bepaalt ook hoe lang de clientmanifesten kunnen groeien.

Elk programma is gekoppeld aan een asset. Als u het programma wilt publiceren, moet u een OnDemand-locator voor de gekoppelde asset maken. Met deze locator kunt u een streaming-URL maken die u aan uw clients kunt leveren.

Een kanaal ondersteunt maximaal drie gelijktijdig actieve programma's, zodat u meerdere archieven van dezelfde binnenkomende stream kunt maken. Hierdoor kunt u verschillende onderdelen van een gebeurtenis naar behoefte publiceren en archiveren. Zo kan het voor uw bedrijf nodig zijn zes uur van een programma te archiveren, maar alleen de laatste tien minuten uit te zenden. Hiervoor moet u twee gelijktijdig actieve programma's maken. Het ene programma wordt ingesteld om zes uur van de gebeurtenis te archiveren, maar het programma wordt niet gepubliceerd. Het andere programma wordt ingesteld om tien minuten te archiveren en dit programma wordt wel gepubliceerd.

Gebruik bestaande programma's niet voor nieuwe gebeurtenissen. In plaats daarvan maakt en start u een nieuw programma voor elke gebeurtenis.

Start het programma wanneer u klaar bent om te streamen en te archiveren. Stop het programma als u het streamen wilt stoppen en de gebeurtenis wilt archiveren. 

Als u de gearchiveerde inhoud wilt verwijderen, stopt en verwijdert u het programma en verwijdert u vervolgens de gekoppelde asset. Een asset kan niet worden verwijderd als deze wordt gebruikt door een programma. U moet eerst het programma verwijderen. 

Zelfs na het stoppen en verwijderen van het programma kunnen gebruikers de gearchiveerde inhoud als video op aanvraag streamen, mits u de asset niet hebt verwijderd.

Als u de gearchiveerde inhoud wilt behouden maar deze niet langer voor streaming beschikbaar mag zijn, verwijdert u de streaming-locator.

###Programma's maken/starten/stoppen

Zodra de stream het kanaal binnenkomt, kunt u de streaminggebeurtenis starten door een asset, programma en streaming-locator te maken. Hiermee wordt de stream gearchiveerd en beschikbaar gesteld aan kijkers via het streaming-eindpunt. 

Gebeurtenissen kunnen op twee manieren worden gestart: 

1. Klik vanaf de pagina **KANAAL** op **TOEVOEGEN** om een nieuw programma toe te voegen.

    Geef het volgende op: programmanaam, assetnaam, archiefvenster en versleutelingsoptie.
    
    ![createprogram](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-program.png)
    
    Als u **Dit programma nu publiceren** ingeschakeld hebt gelaten, worden er PUBLICATIE-URL's gemaakt.
    
    U kunt op **STARTEN** klikken, wanneer u klaar bent om het programma te streamen.

    Als u het programma hebt gestart, drukt u op PLAY om het afspelen van de inhoud te starten.


    ![createdprogram](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-created-program.png)

2. U kunt ook een snelkoppeling gebruiken en op de knop **STREAMING STARTEN** op de pagina **KANAAL** klikken. Hiermee wordt een asset, een programma en een streaming-locator gemaakt.

    Het programma heet DefaultProgram en het archiefvenster is ingesteld op één uur.

    U kunt het gepubliceerde programma vanaf de pagina KANAAL afspelen. 

    ![channelpublish](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-channel-play.png)


Als u op **STREAMING STOPPEN** op de pagina **KANAAL** klikt, wordt het standaardprogramma gestopt en verwijderd. De asset is er nog steeds en u kunt deze publiceren of de publicatie ervan ongedaan maken vanaf de pagina **INHOUD**.

Als u overschakelt naar de pagina **INHOUD**, ziet u de assets die zijn gemaakt voor uw programma's.

![contentasset](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-content-assets.png)


##Inhoud afspelen

Als u uw gebruiker een URL wilt leveren die kan worden gebruikt om uw inhoud te streamen, moet u eerst uw asset publiceren (zoals is beschreven in de vorige sectie) door een locator te maken (wanneer u een asset publiceert met Portal, worden locators voor u gemaakt). Locators bieden toegang tot bestanden in de asset. 

Afhankelijk van het streamingprotocol dat u wilt gebruiken om de inhoud af te spelen, moet u mogelijk de URL wijzigen die u via de koppeling **PUBLICATIE-URL** van het kanaal/programma krijgt.

De live stream wordt door de dynamische pakketten in het opgegeven protocol verpakt. 

Een streaming-URL heeft standaard de volgende indeling en u kunt de streaming-URL gebruiken om Smooth Streaming-assets af te spelen:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Als u een streaming-URL voor HLS wilt maken, voegt u (format=m3u8-aapl) toe aan de URL.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Als u een streaming-URL voor MPEG DASH wilt maken, voegt u (format=mpd-time-csf) toe aan de URL.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

Zie [Inhoud leveren](media-services-deliver-content-overview.md) voor meer informatie over het leveren van uw inhoud.

U kunt Smooth Stream afspelen met [AMS Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html) of iOS- en Android-apparaten gebruiken om HLS versie 3 af te spelen.

##Opruimen

Als u klaar bent met het streamen van gebeurtenissen en de resources wilt opruimen die eerder zijn ingericht, volgt u de volgende procedure.

- Stop het pushen van de stream vanuit het coderingsprogramma.
- Stop het kanaal. Nadat het kanaal is gestopt, worden hiervoor geen kosten meer in rekening gebracht. Als u het kanaal opnieuw wilt starten, wordt dezelfde URL voor opnemen gebruikt, zodat u het coderingsprogramma niet opnieuw hoeft te configureren.
- U kunt uw streaming-eindpunt stoppen, tenzij u het archief van uw live gebeurtenis wilt blijven leveren als stream op aanvraag. Nadat het kanaal is gestopt, worden hiervoor geen kosten meer in rekening gebracht.
  

##Overwegingen

- De maximum aanbevolen duur van een live gebeurtenis is momenteel acht uur. Neem contact op met amslived op Microsoft.com als u een kanaal voor langere tijd wilt uitvoeren.
- Zorg ervoor dat u ten minste één gereserveerde eenheid streaming hebt op het streaming-eindpunt vanaf waar u de inhoud wilt streamen.


##Media Services-leertrajecten

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Feedback geven

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



[standard0]: ./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel-standard0.png
[standard1]: ./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel-standard1.png
[standard2]: ./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel-standard2.png
[standard3]: ./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel-standard3.png
[standard4]: ./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel-standard4.png
[standard5]: ./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel-standard_encode.png 


<!--HONumber=Jun16_HO2-->


