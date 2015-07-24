<properties 
	pageTitle="Azure Mobile Engagement - 後端整合" 
	description="連接 Azure Mobile Engagement 與 SharePoint 後端以從 SharePoint 建立行銷活動" 
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/12/2015" 
	ms.author="piyushjo" />

# Azure Mobile Engagement - API 整合

在自動化行銷系統中，建立及啟動行銷活動也會自動發生。針對此目的 - Azure Mobile Engagement 也可以使用 API 建立這類自動化行銷活動。

通常客戶使用 Mobile Engagement 前端介面建立公告/投票等等做為其行銷活動的一部分。不過隨著行銷活動成熟，就需要運用後端系統 (例如任何 CRM 系統或 CMS 系統，例如 SharePoint) 中鎖定的資料，以便建立完全自動化的管線，根據從後端系統傳送的資料，在 Mobile Engagement 中動態建立行銷活動。

![][5]

本教學課程會逐步解說此類案例，其中 SharePoint 商務使用者會以行銷資料填入 SharePoint 清單，自動化程序會從清單挑選項目，並且使用可用的 REST API 與 Mobile Engagement 系統連線，以從 SharePoint 資料建立行銷活動。
 
> [AZURE.IMPORTANT]一般而言，您可以使用這個範例做為起點，了解如何呼叫任何 Mobile Engagement REST API，因為它詳細說明呼叫 API 的兩個重要層面 - 驗證和傳遞參數。

## SharePoint 整合
1. 以下是範例 SharePoint 清單的類似外觀。**標題**、**類別**、**NotificationTitle**、**訊息** 和 **URL** 是用來建立公告。有稱為 **IsProcessed** 的資料行，由主控台程式形式的範例自動化程序使用。您可以執行這個主控台程式做為 Azure WebJob，讓您可以排程至程式或直接使用 SharePoint 工作流程，在項目插入至 SharePoint 清單時建立和啟動公告。在此範例中，我們使用主控台程式，會逐一進行 SharePoint 清單中的項目，並為每個項目在 Azure Mobile Engagement 中建立公告，最後於成功建立公告時將 **IsProcessed** 旗標標示為 true。

	![][1]

2. 我們是從[這裡](https://code.msdn.microsoft.com/Remote-Authentication-in-b7b6f43c)的範例*使用用戶端物件模型在 SharePoint Online 中遠端驗證*的程式碼，以使用 SharePoint 清單進行驗證。
 
3. 驗證之後，我們會重複執行清單項目，以找出任何新建立的項目 (其 **IsProcessed** = false)。

 		static async void CreateCampaignFromSharepoint()
        {
            using (ClientContext clientContext = ClaimClientContext.GetAuthenticatedContext(targetSharepointSite))
            {
                // Using https://code.msdn.microsoft.com/Remote-Authentication-in-b7b6f43c for authentication     
                Web site = clientContext.Web;
                List list = site.Lists.GetByTitle("VideoContent");
                CamlQuery query = new CamlQuery();
                query.ViewXml = "<View/>";
                ListItemCollection items = list.GetItems(query);

                // Load the SharePoint list
                clientContext.Load(list);
                clientContext.Load(items);
                clientContext.ExecuteQuery();

                // Loop through the list to go through all the items which are newly added
                foreach (ListItem item in items)
                    if (item["IsProcessed"].ToString() != "Yes")
                    {
                        string name = item["Title"].ToString();
                        string notificationTitle = item["NotificationTitle"].ToString();
                        string notificationMessage = item["Message"].ToString();
                        string category = item["Category"].ToString();
                        string actionURL = ((FieldUrlValue)item["URL"]).Url;

                        // Send an HTTP request to create AzME campaign
                        int campaignId = CreateAzMECampaign
                            (name, notificationTitle, notificationMessage, category, actionURL).Result;

                        if (campaignId != -1)
                        {
                            // If creating campaign is successful then set this to true
                            item["IsProcessed"] = "Yes";

                            // Now try to activate the campaign also
                            await ActivateAzMECampaign(campaignId);
                        }
                        else
                        {
                            item["IsProcessed"] = "Error";
                        }
                        item.Update();
                    }
                clientContext.ExecuteQuery();
            }
        }

## Mobile Engagement 整合
1.  一旦我們找到需要處理的項目 - 我們會擷取必要的資訊以從清單項目建立公告，並且呼叫 `CreateAzMECampaign` 來建立它，然後呼叫 `ActivateAzMECampaign` 來啟動它。這些基本上是 REST API 呼叫，呼叫至 Mobile Engagement 後端。 

2.  Mobile Engagement REST API 需要**基本驗證配置授權 HTTP 標頭**，由 `ApplicationId` 和 `ApiKey` 組成，您可以從 Azure 入口網站取得。請確定您使用的金鑰是來自 **api 金鑰** 區段，而*不*是來自 **sdk 金鑰** 區段。

	![][2]

        static string CreateAuthZHeader()
        {
            string BasicAuthzHeaderString = "Basic " + EncodeTo64(ApplicationId + ":" + ApiKey);
            return BasicAuthzHeaderString;
        }

        static public string EncodeTo64(string toEncode)
        {
            byte[] toEncodeAsBytes = System.Text.ASCIIEncoding.ASCII.GetBytes(toEncode);
            string returnValue = System.Convert.ToBase64String(toEncodeAsBytes);
            return returnValue;
        }  

3. 度於建立公告類型行銷活動 - 參考[文件](https://msdn.microsoft.com/library/dn913754.aspx)。您必須確定您指定行銷活動 `kind` 做為 *公告* 和 [裝載](https://msdn.microsoft.com/library/dn913749.aspx)，並將它傳遞為 FormUrlEncodedContent。

		static async Task<int> CreateAzMECampaign(string campaignName, string notificationTitle, 
            string notificationMessage, string notificationCategory, string actionURL)
        {
            string createURIFragment = "/reach/1/create";

            using (var client = new HttpClient())
            {
                // Add Authorization Header
                client.DefaultRequestHeaders.TryAddWithoutValidation("Authorization", CreateAuthZHeader());
                
                // Create the payload to send the content
                // Reference -> https://msdn.microsoft.com/zh-tw/library/dn913749.aspx
                string data =
                    @"{""name"":""" + campaignName + @"""," + 
                    @"""type"":""only_notif""," + 
                    @"""deliveryTime"":""any"","" + 
                    @""notificationTitle"":""" + notificationTitle + 
                    @""",""notificationMessage"":""" + notificationMessage + 
                    @""",""actionUrl"":""" + actionURL + @"""}";
                
                var content = new FormUrlEncodedContent(new[] 
                {
                    new KeyValuePair<string, string>("kind", "announcement"),
                    new KeyValuePair<string, string>("data", data),
                });

                // Send the POST request
                var response = await client.PostAsync(url + createURIFragment, content);
                var responseString = response.Content.ReadAsStringAsync().Result;
                int campaignId = -1;
                if (response.StatusCode.ToString() == "OK")
                {
                    // This is the campaign id
                    campaignId = Convert.ToInt32(responseString);
                    Console.WriteLine("Campaign successfully created with id {0}", campaignId);
                }
                else
                {
                    Console.WriteLine("Campaign creation failed with error - '{0}'", responseString);
                }
                return campaignId;
            }
        }

4. 建立公告之後，您會在 Mobile Engagement 入口網站上看到如下的訊息 (請注意，State=Draft 和 Activated = N/A)

	![][3]

5. `CreateAzMECampaign` 會建立公告行銷活動，並將其識別碼傳回給呼叫端。`ActivateAzMECampaign` 需要此識別碼做為參數來啟動行銷活動。

		static async Task<bool> ActivateAzMECampaign(int campaignId)
        {
            string activateUriFragment = "/reach/1/activate";
            using (var client = new HttpClient())
            {
                // Add Authorization Header
                client.DefaultRequestHeaders.TryAddWithoutValidation("Authorization", CreateAuthZHeader());

                var content = new FormUrlEncodedContent(new[] 
                {
                    new KeyValuePair<string, string>("kind", "announcement"),
                    new KeyValuePair<string, string>("id", campaignId.ToString()),
                });

                // Send the POST request
                var response = await client.PostAsync(url + activateUriFragment, content);
                var responseString = response.Content.ReadAsStringAsync().Result;
                if (response.StatusCode.ToString() == "OK")
                {
                    Console.WriteLine("Campaign successfully activated");
                    return true;
                }
                else
                {
                    Console.WriteLine("Campaign activation failed with error - '{0}'", responseString);
                    return false;
                }
            }
        }

6. 啟動公告之後，您會在 Mobile Engagement 入口網站上看到如下的訊息：

	![][4]

7. 啟動行銷活動之後，滿足行銷活動準則的任何裝置上就能開始看到通知。

8. 您也會發現標示為 IsProcessed = false 的清單項目在建立公告行銷活動之後已設定為 True。

此範例會建立簡單的公告行銷活動，指定大部分必要的屬性。您可以使用[這裡](https://msdn.microsoft.com/library/dn913749.aspx)的資訊，從入口網站視需要對此進行自訂。

<!-- Images. -->
[1]: ./media/mobile-engagement-sample-backend-integration-sharepoint/sharepointlist.png
[2]: ./media/mobile-engagement-sample-backend-integration-sharepoint/properties.png
[3]: ./media/mobile-engagement-sample-backend-integration-sharepoint/new-announcement.png
[4]: ./media/mobile-engagement-sample-backend-integration-sharepoint/activate-announcement.png
[5]: ./media/mobile-engagement-sample-backend-integration-sharepoint/diagram.png


 

<!---HONumber=July15_HO2-->