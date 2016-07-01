<properties 
    pageTitle="Live streamen met Azure Media Services om multi-bitrate streams te maken met .NET" 
    description="In deze zelfstudie wordt u begeleid bij de stappen voor het maken van een kanaal dat een single-bitrate livestream ontvangt, en het coderen van deze stream naar een multi-bitrate stream met .NET SDK." 
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


#Live streamen met Azure Media Services om multi-bitrate streams te maken met .NET

> [AZURE.SELECTOR]
- [Portal](media-services-portal-creating-live-encoder-enabled-channel.md)
- [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
- [REST API](https://msdn.microsoft.com/library/azure/dn783458.aspx)

>[AZURE.NOTE]
> U hebt een Azure-account nodig om deze zelfstudie te voltooien. Zie [Gratis proefversie van Azure](/pricing/free-trial/?WT.mc_id=A261C142F) voor meer informatie. 

##Overzicht

In deze zelfstudie wordt u begeleid bij de stappen voor het maken van een **kanaal** dat een single-bitrate livestream ontvangt, en het coderen van deze stream naar een multi-bitrate stream.

Zie [Live streamen met Azure Media Services om multi-bitrate streams te maken](media-services-manage-live-encoder-enabled-channels.md) voor meer conceptuele informatie over kanalen die zijn ingeschakeld voor live codering.


##Algemeen scenario voor live streamen

In de onderstaande stappen worden de taken beschreven voor het maken van algemene toepassingen voor het maken van livestreams.

>[AZURE.NOTE] De maximum aanbevolen duur van een live gebeurtenis is momenteel acht uur. Neem contact op met amslived op Microsoft.com als u een kanaal voor langere tijd wilt uitvoeren.

1. Sluit een videocamera aan op een computer. Start en configureer een on-premises livecoderingsprogramma dat een single-bitrate stream in een van de volgende protocollen kan uitvoeren: RTMP, Smooth Streaming of RTP (MPEG-TS). Zie [Azure Media Services RTMP-ondersteuning en live coderingsprogramma's](http://go.microsoft.com/fwlink/?LinkId=532824) voor meer informatie.

    Deze stap kan ook worden uitgevoerd nadat u uw kanaal hebt gemaakt.

1. Maak en start een kanaal.

1. Haal de URL voor opnemen voor het kanaal op.

    De URL voor opnemen wordt gebruikt door het live coderingsprogramma om de stream naar het kanaal te verzenden.

1. Haal de voorbeeld-URL voor het kanaal op.

    Gebruik deze URL om te controleren of de livestream goed door het kanaal wordt ontvangen.

2. Maak een asset.
3. Als u wilt dat de asset dynamisch worden versleuteld tijdens het afspelen, gaat u als volgt te werk:
1. Maak een inhoudssleutel.
1. Configureer het autorisatiebeleid voor de inhoudssleutel.
1. Configureer het beleid voor de levering van assets (gebruikt door dynamische pakketten en dynamische versleuteling).
3. Maak een programma en geef op dat de asset die u hebt gemaakt, moet worden gebruikt.
1. Publiceer de asset die aan het programma is gekoppeld door een OnDemand-locator te maken.

    Zorg ervoor dat u ten minste één gereserveerde eenheid streaming hebt op het streaming-eindpunt vanaf waar u de inhoud wilt streamen.

1. Start het programma wanneer u klaar bent om te streamen en te archiveren.
2. Het live coderingsprogramma kan desgewenst een signaal ontvangen dat een advertentie moet worden gestart. De advertentie wordt ingevoegd in de uitvoerstream.
1. Stop het programma als u het streamen wilt stoppen en de gebeurtenis wilt archiveren.
1. Verwijder het programma (en verwijder desgewenst de asset).

## Wat u leert

In dit onderwerp wordt beschreven hoe u verschillende bewerkingen op kanalen en programma's met Media Services .NET SDK uitvoert. Aangezien veel bewerkingen langlopend zijn, worden er .NET API's gebruikt waarmee langlopende bewerkingen worden beheerd.

In het onderwerp wordt beschreven hoe u het volgende doet:

1. Een kanaal maken en starten. Er worden langlopende API's gebruikt.
1. Zorg ervoor dat de kanalen het (invoer)eindpunt opnemen. Dit eindpunt wordt opgegeven voor het coderingsprogramma dat een single-bitrate livestream kan verzenden.
1. Haal het preview-eindpunt op. Dit eindpunt wordt gebruikt om een voorbeeld van uw stream te gebruiken.
1. Maak een asset die wordt gebruikt voor het opslaan van uw inhoud. Het beleid voor de levering van assets moet ook worden geconfigureerd, zoals in dit voorbeeld wordt getoond.
1. Maak een programma en geef op dat de eerder gemaakte asset moet worden gebruikt. Start het programma. Er worden langlopende API's gebruikt.
1. Maak een locator voor de asset zodat de inhoud wordt gepubliceerd en kan worden gestreamd naar uw clients.
1. Geef slates weer en verberg ze. Start en stop advertenties. Er worden langlopende API's gebruikt.
1. Schoon het kanaal en alle bijbehorende resources op.


##Vereisten

Hieronder wordt aangegeven wat de vereisten zijn om de zelfstudie te voltooien.

- U hebt een Azure-account nodig om deze zelfstudie te voltooien. 
    
    Als u geen account hebt, kunt u binnen een paar minuten een account voor de gratis proefversie maken. Zie [Gratis proefversie van Azure](/pricing/free-trial/?WT.mc_id=A261C142F) voor meer informatie. U ontvangt tegoed dat kan worden gebruikt om betaalde Azure-services te proberen. Zelfs nadat het tegoed is gebruikt, kunt u het account houden en de gratis Azure-services en -functies gebruiken, zoals de functie Web-apps in Azure App Service.
- Een Media Services-account. Zie [Account maken](media-services-create-account.md) voor meer informatie over het maken van een Media Services-account.
- Visual Studio 2010 SP1 (Professional, Premium, Ultimate of Express) of hoger.
- U moet Media Services .NET SDK versie 3.2.0.0 of hoger gebruiken.
- Een webcam en een coderingsprogramma dat een single bitrate livestream kan verzenden.

##Overwegingen

- De maximum aanbevolen duur van een live gebeurtenis is momenteel acht uur. Neem contact op met amslived op Microsoft.com als u een kanaal voor langere tijd wilt uitvoeren.
- Zorg ervoor dat u ten minste één gereserveerde eenheid streaming hebt op het streaming-eindpunt vanaf waar u de inhoud wilt streamen.

##Voorbeeld downloaden

U kunt [hier](https://azure.microsoft.com/documentation/samples/media-services-dotnet-encode-live-stream-with-ams-clear/) een voorbeeld ophalen en uitvoeren.


##Setup voor de ontwikkeling met Media Services SDK voor .NET

1. Maak een consoletoepassing met Visual Studio.
1. Gebruik het Media Services NuGet-pakket om de Media Services SDK voor .NET aan uw consoletoepassing toe te voegen.

##Verbinding met Media Services maken
Als best practice moet u een app.config-bestand gebruiken om de Media Services-naam en de accountsleutel op te slaan.

>[AZURE.NOTE]Als u de waarden voor de naam en de sleutel wilt zoeken, gaat u naar de klassieke Azure-portal, selecteert u uw Media Service-account en klikt u onder aan het portalvenster op het pictogram SLEUTELS BEHEREN. Door op het pictogram naast elk tekstvak te klikken, wordt de waarde gekopieerd naar het systeemklembord.

Voeg de sectie appSettings aan het bestand app.config toe en geef de waarden op voor uw Media Services-accountnaam en de accountsleutel.


    <?xml version="1.0"?>
    <configuration>
      <appSettings>
          <add key="MediaServicesAccountName" value="YouMediaServicesAccountName" />
          <add key="MediaServicesAccountKey" value="YouMediaServicesAccountKey" />
      </appSettings>
    </configuration>
     
    
##Voorbeeld van code

    using System;
    using System.Collections.Generic;
    using System.Configuration;
    using System.IO;
    using System.Linq;
    using System.Net;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
    
    namespace EncodeLiveStreamWithAmsClear
    {
        class Program
        {
            private const string ChannelName = "channel001";
            private const string AssetlName = "asset001";
            private const string ProgramlName = "program001";
    
            // Read values from the App.config file.
            private static readonly string _mediaServicesAccountName =
                ConfigurationManager.AppSettings["MediaServicesAccountName"];
            private static readonly string _mediaServicesAccountKey =
                ConfigurationManager.AppSettings["MediaServicesAccountKey"];
    
            // Field for service context.
            private static CloudMediaContext _context = null;
            private static MediaServicesCredentials _cachedCredentials = null;
    
    
            static void Main(string[] args)
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Used the cached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);
    
                IChannel channel = CreateAndStartChannel();
    
                // The channel's input endpoint:
                string ingestUrl = channel.Input.Endpoints.FirstOrDefault().Url.ToString();
    
                Console.WriteLine("Intest URL: {0}", ingestUrl);
    
    
                // Use the previewEndpoint to preview and verify 
                // that the input from the encoder is actually reaching the Channel. 
                string previewEndpoint = channel.Preview.Endpoints.FirstOrDefault().Url.ToString();
    
                Console.WriteLine("Preview URL: {0}", previewEndpoint);
    
                // When Live Encoding is enabled, you can now get a preview of the live feed as it reaches the Channel. 
                // This can be a valuable tool to check whether your live feed is actually reaching the Channel. 
                // The thumbnail is exposed via the same end-point as the Channel Preview URL.
                string thumbnailUri = new UriBuilder
                {
                    Scheme = Uri.UriSchemeHttps,
                    Host = channel.Preview.Endpoints.FirstOrDefault().Url.Host,
                    Path = "thumbnails/input.jpg"
                }.Uri.ToString();
    
                Console.WriteLine("Thumbain URL: {0}", thumbnailUri);
    
                // Once you previewed your stream and verified that it is flowing into your Channel, 
                // you can create an event by creating an Asset, Program, and Streaming Locator. 
                IAsset asset = CreateAndConfigureAsset();
    
                IProgram program = CreateAndStartProgram(channel, asset);
    
                ILocator locator = CreateLocatorForAsset(program.Asset, program.ArchiveWindowLength);
    
                // You can use slates and ads only if the channel type is Standard.  
                StartStopAdsSlates(channel);
    
                // Once you are done streaming, clean up your resources.
                Cleanup(channel);
    
            }
    
            public static IChannel CreateAndStartChannel()
            {
                var channelInput = CreateChannelInput();
                var channePreview = CreateChannelPreview();
                var channelEncoding = CreateChannelEncoding();
    
    
                ChannelCreationOptions options = new ChannelCreationOptions
                {
                    EncodingType = ChannelEncodingType.Standard,
                    Name = ChannelName,
                    Input = channelInput,
                    Preview = channePreview,
                    Encoding = channelEncoding
                };
    
                Log("Creating channel");
                IOperation channelCreateOperation = _context.Channels.SendCreateOperation(options);
                string channelId = TrackOperation(channelCreateOperation, "Channel create");
    
                IChannel channel = _context.Channels.Where(c => c.Id == channelId).FirstOrDefault();
    
                Log("Starting channel");
                var channelStartOperation = channel.SendStartOperation();
                TrackOperation(channelStartOperation, "Channel start");
    
                return channel;
            }
    
            /// <summary>
            /// Create channel input, used in channel creation options. 
            /// </summary>
            /// <returns></returns>
            private static ChannelInput CreateChannelInput()
            {
                return new ChannelInput
                {
                    StreamingProtocol = StreamingProtocol.RTPMPEG2TS,
                    AccessControl = new ChannelAccessControl
                    {
                        IPAllowList = new List<IPRange>
                        {
                            new IPRange
                            {
                                Name = "TestChannelInput001",
                                Address = IPAddress.Parse("0.0.0.0"),
                                SubnetPrefixLength = 0
                            }
                        }
                    }
                };
            }
    
            /// <summary>
            /// Create channel preview, used in channel creation options. 
            /// </summary>
            /// <returns></returns>
            private static ChannelPreview CreateChannelPreview()
            {
                return new ChannelPreview
                {
                    AccessControl = new ChannelAccessControl
                    {
                        IPAllowList = new List<IPRange>
                        {
                            new IPRange
                            {
                                Name = "TestChannelPreview001",
                                Address = IPAddress.Parse("0.0.0.0"),
                                SubnetPrefixLength = 0
                            }
                        }
                    }
                };
            }
    
            /// <summary>
            /// Create channel encoding, used in channel creation options. 
            /// </summary>
            /// <returns></returns>
            private static ChannelEncoding CreateChannelEncoding()
            {
                return new ChannelEncoding
                {
                    SystemPreset = "Default720p",
                    IgnoreCea708ClosedCaptions = false,
                    AdMarkerSource = AdMarkerSource.Api,
                    // You can only set audio if streaming protocol is set to StreamingProtocol.RTPMPEG2TS.
                    AudioStreams = new List<AudioStream> { new AudioStream { Index = 103, Language = "eng" } }.AsReadOnly()
                };
            }
    
            /// <summary>
            /// Create an asset and configure asset delivery policies.
            /// </summary>
            /// <returns></returns>
            public static IAsset CreateAndConfigureAsset()
            {
                IAsset asset = _context.Assets.Create(AssetlName, AssetCreationOptions.None);
    
                IAssetDeliveryPolicy policy =
                    _context.AssetDeliveryPolicies.Create("Clear Policy",
                    AssetDeliveryPolicyType.NoDynamicEncryption,
                    AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);
    
                asset.DeliveryPolicies.Add(policy);
    
                return asset;
            }
    
            /// <summary>
            /// Create a Program on the Channel. You can have multiple Programs that overlap or are sequential;
            /// however each Program must have a unique name within your Media Services account.
            /// </summary>
            /// <param name="channel"></param>
            /// <param name="asset"></param>
            /// <returns></returns>
            public static IProgram CreateAndStartProgram(IChannel channel, IAsset asset)
            {
                IProgram program = channel.Programs.Create(ProgramlName, TimeSpan.FromHours(3), asset.Id);
                Log("Program created", program.Id);
    
                Log("Starting program");
                var programStartOperation = program.SendStartOperation();
                TrackOperation(programStartOperation, "Program start");
    
                return program;
            }
    
            /// <summary>
            /// Create locators in order to be able to publish and stream the video.
            /// </summary>
            /// <param name="asset"></param>
            /// <param name="ArchiveWindowLength"></param>
            /// <returns></returns>
            public static ILocator CreateLocatorForAsset(IAsset asset, TimeSpan ArchiveWindowLength)
            {
                // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.            
                var locator = _context.Locators.CreateLocator
                    (
                        LocatorType.OnDemandOrigin,
                        asset,
                        _context.AccessPolicies.Create
                            (
                                "Live Stream Policy",
                                ArchiveWindowLength,
                                AccessPermissions.Read
                            )
                    );
    
                return locator;
            }
    
            /// <summary>
            /// Perform operations on slates.
            /// </summary>
            /// <param name="channel"></param>
            public static void StartStopAdsSlates(IChannel channel)
            {
                int cueId = new Random().Next(int.MaxValue);
                var path = Path.GetFullPath(Path.Combine(AppDomain.CurrentDomain.BaseDirectory, @"..\\..\\SlateJPG\\DefaultAzurePortalSlate.jpg"));
    
                Log("Creating asset");
                var slateAsset = _context.Assets.Create("Slate test asset " + DateTime.Now.ToString("yyyy-MM-dd HH-mm"), AssetCreationOptions.None);
                Log("Slate asset created", slateAsset.Id);
    
                Log("Uploading file");
                var assetFile = slateAsset.AssetFiles.Create("DefaultAzurePortalSlate.jpg");
                assetFile.Upload(path);
                assetFile.IsPrimary = true;
                assetFile.Update();
    
                Log("Showing slate");
                var showSlateOpeartion = channel.SendShowSlateOperation(TimeSpan.FromMinutes(1), slateAsset.Id);
                TrackOperation(showSlateOpeartion, "Show slate");
    
                Log("Hiding slate");
                var hideSlateOperation = channel.SendHideSlateOperation();
                TrackOperation(hideSlateOperation, "Hide slate");
    
                Log("Starting ad");
                var startAdOperation = channel.SendStartAdvertisementOperation(TimeSpan.FromMinutes(1), cueId, false);
                TrackOperation(startAdOperation, "Start ad");
    
                Log("Ending ad");
                var endAdOperation = channel.SendEndAdvertisementOperation(cueId);
                TrackOperation(endAdOperation, "End ad");
    
                Log("Deleting slate asset");
                slateAsset.Delete();
            }
    
            /// <summary>
            /// Clean up resources associated with the channel.
            /// </summary>
            /// <param name="channel"></param>
            public static void Cleanup(IChannel channel)
            {
                IAsset asset;
                if (channel != null)
                {
                    foreach (var program in channel.Programs)
                    {
                        asset = _context.Assets.Where(se => se.Id == program.AssetId)
                                                .FirstOrDefault();
    
                        Log("Stopping program");
                        var programStopOperation = program.SendStopOperation();
                        TrackOperation(programStopOperation, "Program stop");
    
                        program.Delete();
    
                        if (asset != null)
                        {
                            Log("Deleting locators");
                            foreach (var l in asset.Locators)
                                l.Delete();
    
                            Log("Deleting asset");
                            asset.Delete();
                        }
                    }
    
                    Log("Stopping channel");
                    var channelStopOperation = channel.SendStopOperation();
                    TrackOperation(channelStopOperation, "Channel stop");
    
                    Log("Deleting channel");
                    var channelDeleteOperation = channel.SendDeleteOperation();
                    TrackOperation(channelDeleteOperation, "Channel delete");
                }
            }
    
    
            /// <summary>
            /// Track long running operations.
            /// </summary>
            /// <param name="operation"></param>
            /// <param name="description"></param>
            /// <returns></returns>
            public static string TrackOperation(IOperation operation, string description)
            {
                string entityId = null;
                bool isCompleted = false;
    
                Log("starting to track ", null, operation.Id);
                while (isCompleted == false)
                {
                    operation = _context.Operations.GetOperation(operation.Id);
                    isCompleted = IsCompleted(operation, out entityId);
                    System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
                }
                // If we got here, the operation succeeded.
                Log(description + " in completed", operation.TargetEntityId, operation.Id);
    
                return entityId;
            }
    
            /// <summary> 
            /// Checks if the operation has been completed. 
            /// If the operation succeeded, the created entity Id is returned in the out parameter.
            /// </summary> 
            /// <param name="operationId">The operation Id.</param> 
            /// <param name="channel">
            /// If the operation succeeded, 
            /// the entity Id associated with the sucessful operation is returned in the out parameter.</param>
            /// <returns>Returns false if the operation is still in progress; otherwise, true.</returns> 
            private static bool IsCompleted(IOperation operation, out string entityId)
            {
    
                bool completed = false;
    
                entityId = null;
    
                switch (operation.State)
                {
                    case OperationState.Failed:
                        // Handle the failure. 
                        // For example, throw an exception. 
                        // Use the following information in the exception: operationId, operation.ErrorMessage.
                        Log("operation failed", operation.TargetEntityId, operation.Id);
                        break;
                    case OperationState.Succeeded:
                        completed = true;
                        entityId = operation.TargetEntityId;
                        break;
                    case OperationState.InProgress:
                        completed = false;
                        Log("operation in progress", operation.TargetEntityId, operation.Id);
                        break;
                }
                return completed;
            }
    
    
            private static void Log(string action, string entityId = null, string operationId = null)
            {
                Console.WriteLine(
                    "{0,-21}{1,-51}{2,-51}{3,-51}",
                    DateTime.Now.ToString("yyyy'-'MM'-'dd HH':'mm':'ss"),
                    action,
                    entityId ?? string.Empty,
                    operationId ?? string.Empty);
            }
        }
    }   


##Volgende stappen: Media Services-leertrajecten

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Feedback geven

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

### Zoekt u iets anders?

Als dit onderwerp niet de informatie bevat die u verwacht, er iets ontbreekt of het onderwerp op een andere manier niet aan uw behoeften voldoet, kunt u ons via de onderstaande Disqus-thread feedback geven.



<!--HONumber=Jun16_HO2-->


