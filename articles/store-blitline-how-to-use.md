<properties 
	pageTitle="如何使用 Blitline 進行影像處理 - Azure 功能指南" 
	description="了解如何使用 Blitline 服務處理 Azure 應用程式內的影像。" 
	services="" 
	documentationCenter=".net" 
	authors="blitline-dev" 
	manager="jason@blitline.com" 
	editor="jason@blitline.com"/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/09/2014" 
	ms.author="support@blitline.com"/>






# 如何使用搭配 Azure 和 Azure 儲存體的 Blitline

本指南將說明如何存取 Blitline 服務，以及如何將工作提交至 Blitline。

## 目錄

[什麼是 Blitline？][] [Blitline 不是什麼][] [建立 Blitline 帳戶][] [如何建立 Blitline 工作][] [如何將影像儲存至您的 Azure 儲存體][] [後續步驟][]

## <a id="whatis"></a>什麼是 Blitline？

Blitline 是雲端影像處理服務，可提供企業級的影像處理，而價格卻只有自我建置的數分之一。

事實上，影像處理一再重複出現，通常需要從頭開始重新建置每個網站。我們了解這個情況，那是因為我們已經建立了上百萬次。有一天，我們決定或許是替所有人執行此動作的時候了。我們知道如何做、如何做得快以及做得有效率，並同時減輕每個人的工作。

如需詳細資訊，請參閱 [http://www.blitline.com](http://www.blitline.com) (英文)。

## <a id="whatisnot"></a>Blitline 不是什麼...

若要釐清 Blitline 有什麼功能，在繼續之前，找出 Blitline 沒有什麼功能通常比較容易。

- Blitline 沒有上傳影像的 HTML Widget。您必須要有公開可用的影像，或 Blitline 可用來存取的限制權限。

- Blitline 不會執行即時影像處理，如 Aviary.com

- Blitline 不接受影像上傳，您無法將影像直接推送至 Blitline。您必須將影像推送至 Azure 儲存體或其他 Blitline 支援的位置，然後通知 Blitline 取得這些影像的位置。

- Blitline 可大量平行處理，但無法執行任何同步處理。這表示您必須提供 postback_url 給我們，我們會在處理完成時通知您。

## <a id="createaccount"></a>建立 Blitline 帳戶

[AZURE.INCLUDE [blitline-signup](../includes/blitline-signup.md)]

## <a id="createjob"></a>如何建立 Blitline 工作

Blitline 使用 JSON 定義您要對影像採取的動作。此 JSON 是由幾個簡單欄位組合而成。

下列是個最簡單範例：

	    json : '{
       "application_id": "MY_APP_ID",
       "src" : "http://cdn.blitline.com/filters/boys.jpeg",
       "functions" : [ {
           "name": "resize_to_fit",
           "params" : { "width": 240, "height": 140 },
           "save" : { "image_identifier" : "external_sample_1" }
       } ]
    }'

在此，我們要求 JSON 準備一個 "src" 影像 "...boys.jpeg"，然後將該影像大小調整為 240x140。

您可以在 Azure 上的 [連接資訊] 或 [管理] 索引標籤中找到應用程式識別碼的資訊。應用程式識別碼是可讓您在 Blitline 上執行工作的密碼識別碼。

"save" 參數可識別有關處理完影像時，您想要放置該影像的位置資訊。在這個簡單的案例中，我們尚未定義一個位置。如果沒有定義位置，Blitline 會將它儲存在本機 (並暫時地) 的唯一雲端位置。建立 Blitline 時，您將能夠從 Blitline 所傳回的 JSON 取得該位置。"image" 識別碼為必要欄位，並會在要識別此特定儲存影像時傳回。

您可以在下列連結中找到有關我們可支援的更多「函數」相關資訊：<http://www.blitline.com/docs/functions> 

您也可以在下列連結中找到有關工作選項的相關文件：<http://www.blitline.com/docs/api>

在有了 JSON 之後，您唯一需要做的動作是將它 **POST** 至 `http://api.blitline.com/jobs`

您將取回如下所示的 JSON：

    {
     "results":
         {"images":
            [{
              "image_identifier":"external_sample_1",
              "s3_url":"https://s3.amazonaws.com/dev.blitline/2011110722/YOUR_APP_ID/CK3f0xBF_2bV6wf7gEZE8w.jpg"
            }],
          "job_id":"4eb8c9f72a50ee2a9900002f"
         }
    }


這代表 Blitline 已收到您的要求，它已將您的要求置入處理佇列，以及當它完成影像時的影像位置：**https://s3.amazonaws.com/dev.blitline/2011110722/YOUR_APP_ID/CK3f0xBF_2bV6wf7gEZE8w.jpg**

## <a id="saveazure"></a>如何將影像儲存至您的 Azure 儲存體帳戶

如果您有 Azure 儲存體帳戶，您可以輕易地要求 Blitline 將已處理的影像推送到 Azure 容器。透過新增 "azure_destination"，您可以定義 Blitline 要推送的位置和權限。

下列是一個範例：

    job : '{
      "application_id": "YOUR_APP_ID",
      "src" : "http://www.google.com/logos/2011/houdini11-hp.jpg",
         "functions" : [{
         "name": "blur",
         "save" : {
             "image_identifier" : "YOUR_IMAGE_IDENTIFIER",
             "azure_destination" : {
                 "account_name" : "YOUR_AZURE_CONTAINER_NAME",
                 "shared_access_signature" : "SAS_THAT_GIVES_BLITLINE_PERMISSION_TO_WRITE_THIS_OBJECT_TO_CONTAINER",
               }
           }
         }]
       }'


透過靠您自己填入大寫值，您可以將此 JSON 提交至 http://api.blitline.com/job，並對 "src" 影像採用模糊濾鏡的處理，然後推送至 Azure 目的地。

<h3>請注意：</h3>

SAS 必須包含整個 SAS URL，包括目的地檔案的檔案名稱。

範例：

    http://blitline.blob.core.windows.net/sample/image.jpg?sr=b&sv=2012-02-12&st=2013-04-12T03%3A18%3A30Z&se=2013-04-12T04%3A18%3A30Z&sp=w&sig=Bte2hkkbwTT2sqlkkKLop2asByrE0sIfeesOwj7jNA5o%3D


您也可以參閱[此處](http://www.blitline.com/docs/azure_storage) (英文) 的最新版本 Blitline Azure 儲存體文件。


## <a id="nextsteps"></a>後續步驟

請造訪 blitline.com 以閱讀所有其他功能：

* Blitline API 端點文件 <http://www.blitline.com/docs/api>
* Blitline API 函數 <http://www.blitline.com/docs/functions>
* Blitline API 範例 <http://www.blitline.com/docs/examples>
* 協力廠商 Nuget 程式庫 <http://nuget.org/packages/Blitline.Net>


  [後續步驟]: #nextsteps
  [什麼是 Blitline？]: #whatis
  [Blitline 不是什麼]: #whatisnot
  [建立 Blitline 帳戶]: #createaccount
  [如何建立 Blitline 工作]: #createjob
  [如何將影像儲存至您的 Azure 儲存體]: #saveazure

<!---HONumber=July15_HO1-->