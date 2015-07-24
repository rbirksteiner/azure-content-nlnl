<properties 
	pageTitle="如何使用 Azure Media Encoder 為資產編碼" 
	description="了解如何使用 Azure Media Encoder 為媒體服務上的媒體內容編碼。程式碼範例會使用 REST API。" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/18/2015" 
	ms.author="juliako"/>


#如何使用 Azure Media Encoder 為資產編碼

這篇文章是[媒體服務點播視訊工作流程](media-services-video-on-demand-workflow.md)系列的一部分。

##概觀
若要透過網際網路傳遞數位視訊，您必須壓縮媒體。數位視訊檔案十分龐大，而且可能太大而無法透過網際網路傳遞，或是太大而使您客戶的裝置無法正確顯示。編碼是壓縮視訊和音訊，好讓客戶能檢視您的媒體的程序。

編碼工作是媒體服務中最常見的處理作業。您建立編碼工作以將媒體檔案從一種編碼轉換成另一種編碼。編碼時，您可以使用媒體服務內建的 Media Encoder。您也可以使用媒體服務合作夥伴提供的編碼器；第三方編碼器可透過 Azure Marketplace 取得。您可以使用針對您的編碼器定義的預設字串，或使用預設組態檔，指定編碼工作的詳細資料。若要查看可用的預設類型，請參閱[Azure 媒體服務的工作預設](https://msdn.microsoft.com/library/azure/dn619392.aspx)。如果您使用第三方編碼器，則應該[驗證檔案](https://msdn.microsoft.com/library/azure/dn750842.aspx)。

每個工作可以有一或多個工作，視您想要完成的處理類型而定。您可以透過 REST API 以兩種方式的其中之一建立工作和其相關工作：工作可以透過 Job 實體上的 Tasks 導覽屬性，或透過 OData 批次處理進行內嵌定義。媒體服務 SDK 使用批次處理；不過，為了本主題中的程式碼範例可讀性，工作都是內嵌定義。如需批次處理的資訊，請參閱[開放式資料通訊協定 (OData) 批次處理](http://www.odata.org/documentation/odata-version-3-0/batch-processing/)。您也可以在[工作](https://msdn.microsoft.com/library/azure/hh974289.aspx)主題中找到批次處理範例。

建議一律將夾層檔編碼為調適性位元速率 MP4 集，然後使用[動態封裝](https://msdn.microsoft.com/library/azure/jj889436.aspx)將該集合轉換為所要的格式。若要利用動態封裝，您必須先取得至少一個串流端點的隨選串流單位，您打算從中傳遞您的內容。如需詳細資訊，請參閱[如何調整媒體服務](media-services-manage-origins.md#scale_streaming_endpoints)。

如果您的輸出資產是儲存體加密，必須設定資產傳遞原則。如需詳細資訊，請參閱[設定資產傳遞原則](media-services-rest-configure-asset-delivery-policy.md)。



##建立具有單一編碼工作的工作 

>[AZURE.NOTE]使用媒體服務 REST API 時，適用下列考量事項：
>
>在媒體服務中存取實體時，您必須在 HTTP 要求中設定特定的標頭欄位和值。如需詳細資訊，請參閱[媒體服務 REST API 開發設定](media-services-rest-how-to-use.md)。

>成功連線至 https://media.windows.net 後，您會收到指定另一個媒體服務 URI 的 301 重新導向。您必須依照[使用 REST API 連線至媒體服務](media-services-rest-connect_programmatically.md)所述，對新的 URI 進行後續呼叫。


下列範例會示範如何建立和張貼工作，並將一個工作設為在特定的解析度與品質將視訊編碼。當透過 Azure Media Encoder 編碼時，您可以使用[這裡](https://msdn.microsoft.com/library/azure/dn619389.aspx)指定的工作組態預設。
	
要求：

	POST https://media.windows.net/API/Jobs HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer <token value>
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
	Host: media.windows.net

	
	{"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "H264 Broadband 720p", "MediaProcessorId" : "nb:mpid:UUID:70bdc2c3-ebf4-42a9-8542-5afc1e55d217",  "TaskBody" : "<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}

回應：
	
	HTTP/1.1 201 Created

	. . . 

##注意事項

- TaskBody 屬性必須使用 XML 常值來定義工作所使用的輸入或輸出資產數目。工作主題包含 XML 的 XML 結構描述定義。
- 在 TaskBody 定義中，<inputAsset> 和 <outputAsset> 的每一個內部值必須設定為 JobInputAsset(value) 或 JobOutputAsset(value)。
- 每個工作可以有多個輸出資產。一個 JobOutputAsset(x) 只能使用一次做為工作中的工作輸出。
- 您可以指定 JobInputAsset 或 JobOutputAsset 做為工作的輸入資產。
- 工作不能形成循環。
- 您傳遞至 JobInputAsset 或 JobOutputAsset 的 value 參數代表資產的索引值。實際資產定義在作業實體定義上的 InputMediaAsset 與 OutputMediaAsset 導覽屬性。 
- 由於媒體服務建置在 OData v3 之上，因此 InputMediaAsset 與 OutputMediaAsset 導覽屬性集合中的個別資產會透過 "__metadata : uri" 名稱 / 值組參考。.
- InputMediaAsset 對應至您在媒體服務中建立的一個或多個資產。OutputMediaAsset 由系統建立。它們不會參考現有的資產。
- OutputMediaAsset 可以使用 assetName 屬性命名。如果這個屬性不存在，則 OutputMediaAsset 的名稱將是 <outputAsset> 元素的任何內部文字值，並且尾碼為工作名稱值或工作識別碼值 (在未定義 Name 屬性的情況下)。例如，如果您將 assetName 的值設為 "Sample"，則 OutputMediaAsset Name 屬性會設為 "Sample"。不過，如果您未設定 assetName 的值，但已將工作名稱設為 "NewJob"，則 OutputMediaAsset Name 會是 "JobOutputAsset(value)_NewJob"。 

下列範例示範如何設定 assetName 屬性：

	{ "TaskBody" : "<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName="CustomOutputAssetName">JobOutputAsset(0)</outputAsset></taskBody>"}



##建立具有鏈結工作的工作

在許多應用程式案例中，開發人員想要建立一連串的處理工作。在媒體服務中，您可以建立一連串的鏈結工作。每一項工作會執行不同的處理步驟，而且可以使用不同的媒體處理器。鏈結工作可以將資產從一個工作遞交到另一個工作，對資產執行一連串的工作。不過，在工作中執行的工作不必按照順序。當您建立鏈結工作時，鏈結的 **ITask** 物件會建立在單一 **IJob** 物件中。

>[AZURE.NOTE]目前有每個工作裡 30 個工作的限制。如果您需要鏈結超過 30 個工作，請建立多個工作來包含這些工作。


	POST https://media.windows.net/api/Jobs HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer <token value>
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

	{  
	   "Name":"NewTestJob",
	   "InputMediaAssets":[  
	      {  
	         "__metadata":{  
	            "uri":"https://testrest.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A910ffdc1-2e25-4b17-8a42-61ffd4b8914c')"
	         }
	      }
	   ],
	   "Tasks":[  
	      {  
	         "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
	         "MediaProcessorId":"nb:mpid:UUID:2e7aa8f3-4961-4e0c-b4db-0e0439e524f5",
	         "TaskBody":"<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
	      },
	      {  
	         "Configuration":"H264 Smooth Streaming 720p",
	         "MediaProcessorId":"nb:mpid:UUID:2e7aa8f3-4961-4e0c-b4db-0e0439e524f5",
	         "TaskBody":"<?xml version="1.0" encoding="utf-16"?><taskBody><inputAsset>JobOutputAsset(0)</inputAsset><outputAsset>JobOutputAsset(1)</outputAsset></taskBody>"
	      }
	   ]
	}

###注意事項

啟用工作鏈結：

- 工作必須有至少 2 個工作
- 必須有至少一個工作的輸入是工作中另一項工作的輸出。


## 使用 JobTemplate 建立工作


使用一組常用工作處理多個資產時，JobTemplates 非常實用，因為它可以指定預設的工作預設項目、工作順序等等。

下列範例說明如何使用內嵌定義的 TaskTemplate 建立 JobTemplate。TaskTemplate 是使用 Azure Media Encoder 作為 MediaProcessor 來編碼資產檔案；但是，也可以使用其他 MediaProcessor。


	POST https://media.windows.net/API/JobTemplates HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer <token value>
	Host: media.windows.net

	
	{"Name" : "NewJobTemplate25", "JobTemplateBody" : "<?xml version="1.0" encoding="utf-8"?><jobTemplate><taskBody taskTemplateId="nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789"><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody></jobTemplate>", "TaskTemplates" : [{"Id" : "nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789", "Configuration" : "H264 Smooth Streaming 720p", "MediaProcessorId" : "nb:mpid:UUID:2e7aa8f3-4961-4e0c-b4db-0e0439e524f5", "Name" : "SampleTaskTemplate2", "NumberofInputAssets" : 1, "NumberofOutputAssets" : 1}] }
 

>[AZURE.NOTE]與其他媒體服務實體不同的是，您必須為每個 TaskTemplate 定義新的 GUID 識別碼，並將它置入 taskTemplateId，以及在您的要求本文中置入識別碼屬性。內容識別配置必須遵循「識別 Azure 媒體服務實體」中所述的配置。而且，不能更新 JobTemplate。相反地，您必須使用更新後的變更建立一個新的 JobTemplate。
 

如果成功，則會傳回下列回應：
	
	HTTP/1.1 201 Created
	
	. . .


下面的範例說明如何建立參照 JobTemplate 識別碼的工作：

	POST https://media.windows.net/API/Jobs HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer <token value>
	Host: media.windows.net

	
	{"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A3f1fe4a2-68f5-4190-9557-cd45beccef92')"}}], "TemplateId" : "nb:jtid:UUID:15e6e5e6-ac85-084e-9dc2-db3645fbf0aa"}
	 

如果成功，則會傳回下列回應：
	
	HTTP/1.1 201 Created
	
	. . . 


##控制媒體服務編碼器輸出檔名 

依照預設，Azure 媒體編碼器會透過結合輸入資產與編碼處理程序的多種屬性來建立輸出檔案名稱。每種屬性都是使用下面所述的巨集來識別。

以下是命名輸出檔案時可用之巨集的完整清單：音訊位元速率 - 編碼音訊時使用的位元速率 (kbps)。

- 音訊轉碼器 - 編碼音訊時使用的轉碼器，有效值為：AAC、WMA 及 DDP
- 聲道計數 - 編碼的音訊聲道數，有效值為：1、2 或 6
- 預設副檔名 – 預設的檔案副檔名 
- 語言 - 代表音訊中使用之語言的 BCP-47 語言代碼。此項目目前的預設值為 "und"。 
- 原始檔案名稱 - 上傳至 Azure 儲存體的檔案的名稱
- StreamId – 由預設檔案中 <StreamInfo> 元素的 streamID 屬性定義的資料流識別碼 
- 視訊轉碼器 - 編碼時使用的轉碼器，有效值為：H264 和 VC1
- 視訊位元速率 - 編碼視訊時使用的位元速率 (kbps)

這些巨集可以以任何排列方式組合，以控制媒體服務編碼器所產生之檔案的名稱。例如，預設命名慣例為：

	{Original File Name}_{Video Codec}{Video Bitrate}{Audio Codec}{Language}{Channel Count}{Audio Bitrate}.{Default Extension}

檔案命名慣例是使用 [Preset](https://msdn.microsoft.com/library/azure/dn554334.aspx) 元素的 DefaultMediaOutputFileName 屬性指定。例如：

	<Preset DefaultMediaOutputFileName="{Original file name}{StreamId}_LongOutputFileName{Bit Rate}{Video Codec}{Video Bitrate}{Audio Codec}{Audio Bitrate}{Language}{Channel Count}.{Default extension}"
	  Version="5.0">
	<MediaFile …>
	   <OutputFormat>
	      <MP4OutputFormat StreamCompatibility="Standard">
	         <VideoProfile>
	            <MainH264VideoProfile … >
	               <Streams  AutoSize="False"
	                         FreezeSort="False">
	                  <StreamInfo StreamId="1"
	                              Size="1280, 720">
	                     <Bitrate>
	                       <ConstantBitrate Bitrate="3400"
	                                        IsTwoPass="False"
	                                        BufferWindow="00:00:05" />
	                     </Bitrate>
	                   </StreamInfo>
	                  </Streams>
	               </MainH264VideoProfile>
	            </VideoProfile>
	         </MP4OutputFormat>
	   </OutputFormat>
	</MediaFile>

編碼器將會在每個巨集之間插入底線，例如上述設定會產生像這樣的檔案名稱：MyVideo_H264_4500kpbs_AAC_und_ch2_128kbps.mp4.

##後續步驟
您已了解如何建立為資產編碼的工作，現在請移至[如何使用媒體服務檢查工作進度](media-services-rest-check-job-progress.md) (英文) 主題。

[Azure Marketplace]: https://datamarket.azure.com/
[Encoder Preset]: http://msdn.microsoft.com/library/dn619392.aspx
[How to: Get a Media Processor Instance]: http://go.microsoft.com/fwlink/?LinkId=301732
[How to: Upload an Encrypted Asset]: http://go.microsoft.com/fwlink/?LinkId=301733
[How to: Deliver an Asset by Download]: http://go.microsoft.com/fwlink/?LinkId=301734
[How to Check Job Progress]: http://go.microsoft.com/fwlink/?LinkId=301737
[Task Preset for Azure Media Packager]: http://msdn.microsoft.com/library/windowsazure/hh973635.aspx
 

<!---HONumber=62-->