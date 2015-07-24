<properties 
	pageTitle="更換儲存體存取金鑰之後更新媒體服務" 
	description="本文章會教您如何在更換儲存體存取金鑰之後更新媒體服務。" 
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
	ms.date="05/24/2015" 
	ms.author="juliako"/>

#如何：更換儲存體存取金鑰之後更新媒體服務

當您建立新的 Azure 媒體服務帳戶時，系統也會要求您選取一個 Azure 儲存體帳戶，用來儲存媒體內容。請注意，您可以新增多個儲存體帳戶至媒體服務帳戶中。

建立新的儲存體帳戶時，Azure 會產生兩個 512 位元儲存體存取金鑰，用來驗證儲存體帳戶的存取權。為了讓儲存體連線更加安全，建議您定期重新產生並更換儲存體存取金鑰。您會收到兩個存取金鑰(主要和次要)，這樣當您重新產生其他存取金鑰時，您就可以使用其中一個存取金鑰保持儲存體連線不中斷。此程序也稱為 「 更換存取金鑰 」。

媒體服務一定會用到其中一個儲存體金鑰 (主要或次要)。具體而言，定位器是要傳送或下載您的資產，這要看存取金鑰而定。更換存體存取金鑰時，也請您一定要更新定位器，這樣您的串流服務才不會停擺。

>[AZURE.NOTE]重新產生儲存體金鑰之後，請記得同步更新媒體服務。

本主題會逐步教您如何更換儲存體金鑰以及更新媒體服務，讓正確的儲存體金鑰派上用場。請注意，如果您有多個儲存體帳戶，請針對每一個儲存體帳戶執行此程序。

>[AZURE.NOTE]在實際商用的帳戶上執行本文章描述的步驟之前，請事先在測試用帳戶上進行測試。


## 步驟 1：重新產生次要儲存體存取金鑰

請先重新產生次要儲存體金鑰。根據預設，媒體服務不會使用次要金鑰。如需如何更換儲存體金鑰的詳細資訊，請參閱[如何：檢視、複製及重新產生儲存體存取金鑰](../storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)。
  
##<a id="step2"></a>步驟 2：更新媒體服務，開始使用新的次要儲存體金鑰

更新媒體服務，開始使用次要儲存體存取金鑰。您可以使用下列兩種方法的其中一個，將重新產生的儲存體金鑰與媒體服務同步。

- 使用 Azure 入口網站：選取您的媒體服務帳戶，然後按一下入口網站視窗底部的 [管理金鑰] 圖示。根據您想要為媒體服務同步的儲存體金鑰，選取同步處理主要金鑰或同步處理次要金鑰按鈕。在本案例中，使用次要金鑰。

- 使用媒體服務管理 REST API。

	下列程式碼範例示範如何建構 https://endpoint/<subscriptionId>/services/mediaservices/Accounts/<accountName>/StorageAccounts/<storageAccountName>/Key 要求，以便透過媒體服務同步指定的儲存體金鑰。在本情況中，會使用次要儲存體金鑰。如需詳細資訊，請參閱[如何：使用媒體服務管理 REST API](http://msdn.microsoft.com/library/azure/dn167656.aspx)。
 
		public void UpdateMediaServicesWithStorageAccountKey(string mediaServicesAccount, string storageAccountName, string storageAccountKey)
		{
		    var clientCert = GetCertificate(CertThumbprint);
		
		    HttpWebRequest request = (HttpWebRequest)WebRequest.Create(string.Format("{0}/{1}/services/mediaservices/Accounts/{2}/StorageAccounts/{3}/Key",
		        Endpoint, SubscriptionId, mediaServicesAccount, storageAccountName));
		    request.Method = "PUT";
		    request.ContentType = "application/json; charset=utf-8";
		    request.Headers.Add("x-ms-version", "2011-10-01");
		    request.Headers.Add("Accept-Encoding: gzip, deflate");
		    request.ClientCertificates.Add(clientCert);
		
		
		    using (var streamWriter = new StreamWriter(request.GetRequestStream()))
		    {
		        streamWriter.Write(""");
		        streamWriter.Write(storageAccountKey);
		        streamWriter.Write(""");
		        streamWriter.Flush();
		    }
		
		    using (var response = (HttpWebResponse)request.GetResponse())
		    {
		        string jsonResponse;
		        Stream receiveStream = response.GetResponseStream();
		        Encoding encode = Encoding.GetEncoding("utf-8");
		        if (receiveStream != null)
		        {
		            var readStream = new StreamReader(receiveStream, encode);
		            jsonResponse = readStream.ReadToEnd();
		        }
		    }
		}

之後，請更新現有的定位器 (和舊的儲存體金鑰存在相依性)。

>[AZURE.NOTE]等待 30 分鐘，然後透過媒體服務執行任何操作 (例如，建立新定位器)，以免影響到待處理的工作。

##步驟 3：更新定位器 

30 分鐘後，您可以更新現有的定位器，這樣它們就會對新的次要儲存體金鑰產生相依性。

若要更新定位器的到期日，請使用 [REST](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator) 或 [.NET](http://go.microsoft.com/fwlink/?LinkID=533259) API。請注意，當您更新 SAS 定位器的到期日，URL 也會隨之變更。

##步驟 5：重新產生主要儲存體存取金鑰

重新產生主要儲存體存取金鑰。如需如何更換儲存體金鑰的詳細資訊，請參閱[如何：檢視、複製及重新產生儲存體存取金鑰](../storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)。

##步驟 6：更新媒體服務，開始使用新的主要儲存體金鑰
	
按照[步驟 2](media-services-roll-storage-access-keys.md#step2) 描述的相同程序，這一次只將新主要儲存體存取金鑰同步到媒體服務帳戶。

>[AZURE.NOTE]等待 30 分鐘，然後透過媒體服務執行任何操作 (例如，建立新定位器)，以免影響到待處理的工作。

##步驟 7：更新定位器  

30 分鐘後，您可以更新現有的定位器，這樣它們就會對新的主要儲存體金鑰產生相依性。

若要更新定位器的到期日，請使用 [REST](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator) 或 [.NET](http://go.microsoft.com/fwlink/?LinkID=533259) API。請注意，當您更新 SAS 定位器的到期日，URL 也會隨之變更。

 
 

<!---HONumber=July15_HO2-->