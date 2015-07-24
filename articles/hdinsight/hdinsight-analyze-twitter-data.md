<properties 
	pageTitle="在 HDInsight 上使用 Hadoop 分析 Twitter 資料 | Microsoft Azure" 
	description="了解如何在 HDInsight 中的 Hadoop 上使用 Hive 來分析 Twitter 資料，以找出特定單字的使用頻率。" 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="jgao"/>

# 在 HDInsight 中使用 Hive 分析 Twitter 資料

社群網站是驅使採用巨量資料的其中一個主要動力。像 Twitter 之類的網站所提供的公開 API，是分析和了解流行趨勢的一項實用的資料來源。在本教學課程中，您將使用 Twitter 串流 API 取得推文，然後使用 Apache Hive on Azure HDInsight 取得傳送了最多內含特定文字之推文的 Twitter 使用者清單。

> [AZURE.NOTE]HDInsight 範例資源庫中有類似範例。觀看 Channel 9 影片：<a href="http://channel9.msdn.com/Series/Getting-started-with-Windows-Azure-HDInsight-Service/Analyze-Twitter-trend-using-Apache-Hive-in-HDInsight" target="_blank">使用 Apache Hive in HDInsight 分析 Twitter 趨勢</a>

###必要條件

開始進行本教學課程之前，您必須具備下列條件：

- 已安裝並設定 Azure PowerShell 的**工作站**。請參閱[安裝和使用 Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)。若要執行 Windows PowerShell 指令碼，您必須以系統管理員的身分執行 Azure PowerShell，並將執行原則設為 *RemoteSigned*。請參閱[執行 Windows PowerShell 指令碼][powershell-script]。

	執行 Windows PowerShell 指令碼之前，請確定您已使用下列 Cmdlet 連接到 Azure 訂用帳戶：

		Add-AzureAccount

	如果您有多個 Azure 訂用帳戶，請使用下列 Cmdlet 設定目前的訂用帳戶：

		Select-AzureSubscription <AzureSubscriptionName>



- **Azure HDInsight 叢集**。如需叢集佈建的指示，請參閱[開始使用 HDInsight][hdinsight-get-started] 或[佈建 HDInsight 叢集][hdinsight-provision]。稍後在教學課程中會用到此叢集名稱。

**了解 HDInsight 儲存體**

HDInsight 會使用 Azure Blob 儲存體來儲存資料。Azure Blob 儲存體 是 Microsoft 實作的 Hadoop 分散式檔案系統 (HDFS)。如需詳細資訊，請參閱[搭配 HDInsight 使用 Azure Blob 儲存體][hdinsight-storage]。

當您佈建 HDInsight 叢集時，會將一個 Blob 儲存體容器指定為預設檔案系統，如同在 HDFS 中一般。除了此容器以外，您也可以在佈建過程中，從相同的 Azure 儲存體帳戶或不同的 Azure 儲存體帳戶新增容器。如需有關新增儲存體帳戶的詳細資訊，請參閱[佈建 HDInsight 叢集][hdinsight-provision]。

> [AZURE.NOTE]為簡化本教學課程中使用的 Windows PowerShell 指令碼，所有檔案都儲存在位於 */tutorials/twitter* 的預設檔案系統容器中。根據預設，此容器的名稱會與 HDInsight 叢集名稱相同。如果您選擇使用不同的容器來儲存這些檔案，請相應地更新指令碼。

Azure Blob 儲存體語法：

	wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [AZURE.NOTE]HDInsight 3.0 版叢集僅支援 *wasb://* 語法。HDInsight 2.1 和 1.6 支援舊的 *asv://* 語法，但在 HDInsight 3.0 叢集中已不受支援，未來的版本也不加以支援。

> Azure Blob 儲存體路徑為虛擬路徑。如需詳細資訊，請參閱[搭配 HDInsight 使用 Azure Blob 儲存體][hdinsight-storage]。

儲存在預設檔案系統容器中的檔案，可使用下列任一統一資源識別元 (URI) 從 HDInsight 存取。這些 URI 使用 tweets.txt 作為範例。

	wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/twitter/tweets.txt
	wasb:///tutorials/twitter/tweets.txt
	/tutorials/twitter/tweets.txt

如果您要直接從儲存體帳戶存取檔案，檔案的Blob 名稱為：

	tutorials/twitter/tweets.txt

下表列出本教學課程中使用的檔案：

<table border="1">
<tr><th>檔案</th><th>說明</th></tr>
<tr><td>/tutorials/twitter/data/tweets.txt</td><td>Hive 工作的來源資料。</td></tr>
<tr><td>/tutorials/twitter/output</td><td>Hive 工作的輸出資料夾。預設 Hive 工作的輸出檔案名稱為 <strong>000000_0</strong>。</td></tr>
<tr><td>tutorials/twitter/twitter.hql</td><td>HiveQL 指令碼檔案。</td></tr>
<tr><td>/tutorials/twitter/jobstatus</td><td>Hadoop 工作狀態。</td></tr>
</table>

##取得 Twitter 摘要

在本教學課程中，您將使用 [Twitter 串流 API][twitter-streaming-api]。您將使用的特定 Twitter 串流 API 為 [statuses/filter][twitter-statuses-filter]。

>[AZURE.NOTE]已在公用 Blob 容器中上傳含有 10,000 則推文的檔案和 Hive 指令碼檔案 (下一節說明)。如果想要使用上傳的檔案，可以略過這一節。

[推文資料](https://dev.twitter.com/docs/platform-objects/tweets)會以包含複雜巢狀結構的 JavaScript 物件標記法 (JSON) 格式儲存。您可以不要使用慣用的程式設計語言撰寫多行程式碼，而將此巢狀結構轉換成 Hive 資料表，以利用 HiveQL 這種類似結構化查詢語言 (SQL) 的語言來查詢資料表。

Twitter 會使用 OAuth 提供對其 API 的授權存取。OAuth 是一項驗證通訊協定，可讓使用者在無須共用其密碼的情況下，允許應用程式代表他們執行動作。如需詳細資訊，請至 [oauth.net](http://oauth.net/)，或參考 Hueniverse 的 [OAuth 的入門指南](http://hueniverse.com/oauth/) (英文)。

使用 OAuth 的第一個步驟，是在 Twitter 開發人員網站上建立新的應用程式。

**建立 Twitter 應用程式**

1. 登入 [https://apps.twitter.com/](https://apps.twitter.com/)。如果您沒有 Twitter 帳戶，請按一下 [**立即註冊**] 連結。
2. 按一下 [建立新的應用程式]。
3. 輸入 [名稱]、[說明]、[網站]。您可以在 [**網站**] 欄位中自行設定 URL。下表列出部分要使用的範例值：

	<table border="1">
<tr><th>欄位</th><th>值</th></tr>
<tr><td>名稱</td><td>MyHDInsightApp</td></tr>
<tr><td>說明</td><td>MyHDInsightApp</td></tr>
<tr><td>網站</td><td>http://www.myhdinsightapp.com</td></tr>
</table>
4. 核取 [Yes, I agree]然後按一下 [Create your Twitter application]。
5. 按一下 [權限] 索引標籤。預設權限為 [唯讀]。本教學課程使用預設值即可。 
6. 按一下 **[金鑰和存取權杖**] 索引標籤。
7. 按一下 [Create my access token]。
8. 按一下位於頁面右上角的 [**測試 OAuth**]。
9. 記下**消費者金鑰**、**消費者密碼**、**存取權杖**和**存取權杖密碼**。稍後在教學課程中會用到這些值。

在本教學課程中，您將使用 Windows PowerShell 發出 Web 服務呼叫。如需 .NET C# 範例，請參閱[使用 HDInsight 中的 HBase 分析即時的 Twitter 情緒][hdinsight-hbase-twitter-sentiment]。另一項常用來發出 Web 服務呼叫的工具，是 [*Curl*][curl]。Curl 可從[這裡][curl-download]下載取得。

>[AZURE.NOTE]在 Windows 上使用 curl 命令時，對選項值請使用雙引號，而不要使用單引號。

**取得推文**

1. 開啟 Windows PowerShell 整合式指令碼環境 (ISE)。(在 Windows 8 的 [開始] 畫面上輸入 **PowerShell_ISE**，然後按一下 [**Windows PowerShell ISE**]。請參閱 [在 Windows 8 和 Windows 上啟動 Windows PowerShell][powershell-start])。

2. 將下列指令碼複製到指令碼窗格中：

		#region - variables and constants
		$clusterName = "<HDInsightClusterName>" # Enter the HDInsight cluster name
		
		# Enter the OAuth information for your Twitter application
		$oauth_consumer_key = "<TwitterAppConsumerKey>";
		$oauth_consumer_secret = "<TwitterAppConsumerSecret>";
		$oauth_token = "<TwitterAppAccessToken>";
		$oauth_token_secret = "<TwitterAppAccessTokenSecret>";
		
		$destBlobName = "tutorials/twitter/data/tweets.txt" # This script saves the tweets into this blob. 
		
		$trackString = "Azure, Cloud, HDInsight" # This script gets the tweets containing these keywords.
		$track = [System.Uri]::EscapeDataString($trackString);
		$lineMax = 10000  # The script will get this number of tweets. It is about 3 minutes every 100 lines.
		#endregion
		
		#region - Connect to Azure subscription
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		Add-AzureAccount
		#endregion
			
		#region - Create a block blob object for writing tweets into Blob storage
		Write-Host "Get the default storage account name and Blob container name using the cluster name ..." -ForegroundColor Green
		$myCluster = Get-AzureHDInsightCluster -Name $clusterName
		$storageAccountName = $myCluster.DefaultStorageAccount.StorageAccountName.Replace(".blob.core.windows.net", "")	
		$containerName = $myCluster.DefaultStorageAccount.StorageContainerName
		Write-Host "`tThe storage account name is $storageAccountName." -ForegroundColor Yellow
		Write-Host "`tThe blob container name is $containerName." -ForegroundColor Yellow
		
		Write-Host "Define the Azure storage connection string ..." -ForegroundColor Green
		$storageAccountKey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		$storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"
		Write-Host "`tThe connection string is $storageConnectionString." -ForegroundColor Yellow
		
		Write-Host "Create block blob object ..." -ForegroundColor Green
		$storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
		$storageClient = $storageAccount.CreateCloudBlobClient();
		$storageContainer = $storageClient.GetContainerReference($containerName)
		$destBlob = $storageContainer.GetBlockBlobReference($destBlobName)
		#end region
				
		# region - Format OAuth strings	
		Write-Host "Format oauth strings ..." -ForegroundColor Green
		$oauth_nonce = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes([System.DateTime]::Now.Ticks.ToString()));
		$ts = [System.DateTime]::UtcNow - [System.DateTime]::ParseExact("01/01/1970", "dd/MM/yyyy", $null)
		$oauth_timestamp = [System.Convert]::ToInt64($ts.TotalSeconds).ToString();
				
		$signature = "POST&";
		$signature += [System.Uri]::EscapeDataString("https://stream.twitter.com/1.1/statuses/filter.json") + "&";
		$signature += [System.Uri]::EscapeDataString("oauth_consumer_key=" + $oauth_consumer_key + "&");
		$signature += [System.Uri]::EscapeDataString("oauth_nonce=" + $oauth_nonce + "&"); 
		$signature += [System.Uri]::EscapeDataString("oauth_signature_method=HMAC-SHA1&");
		$signature += [System.Uri]::EscapeDataString("oauth_timestamp=" + $oauth_timestamp + "&");
		$signature += [System.Uri]::EscapeDataString("oauth_token=" + $oauth_token + "&");
		$signature += [System.Uri]::EscapeDataString("oauth_version=1.0&");
		$signature += [System.Uri]::EscapeDataString("track=" + $track);
				
		$signature_key = [System.Uri]::EscapeDataString($oauth_consumer_secret) + "&" + [System.Uri]::EscapeDataString($oauth_token_secret);
				
		$hmacsha1 = new-object System.Security.Cryptography.HMACSHA1;
		$hmacsha1.Key = [System.Text.Encoding]::ASCII.GetBytes($signature_key);
		$oauth_signature = [System.Convert]::ToBase64String($hmacsha1.ComputeHash([System.Text.Encoding]::ASCII.GetBytes($signature)));
				
		$oauth_authorization = 'OAuth ';
		$oauth_authorization += 'oauth_consumer_key="' + [System.Uri]::EscapeDataString($oauth_consumer_key) + '",';
		$oauth_authorization += 'oauth_nonce="' + [System.Uri]::EscapeDataString($oauth_nonce) + '",';
		$oauth_authorization += 'oauth_signature="' + [System.Uri]::EscapeDataString($oauth_signature) + '",';
		$oauth_authorization += 'oauth_signature_method="HMAC-SHA1",'
		$oauth_authorization += 'oauth_timestamp="' + [System.Uri]::EscapeDataString($oauth_timestamp) + '",'
		$oauth_authorization += 'oauth_token="' + [System.Uri]::EscapeDataString($oauth_token) + '",';
		$oauth_authorization += 'oauth_version="1.0"';
				
		$post_body = [System.Text.Encoding]::ASCII.GetBytes("track=" + $track); 
		#endregion 
					
		#region - Read tweets	
		Write-Host "Create HTTP web request ..." -ForegroundColor Green
		[System.Net.HttpWebRequest] $request = [System.Net.WebRequest]::Create("https://stream.twitter.com/1.1/statuses/filter.json");
		$request.Method = "POST";
		$request.Headers.Add("Authorization", $oauth_authorization);
		$request.ContentType = "application/x-www-form-urlencoded";
		$body = $request.GetRequestStream();
				
		$body.write($post_body, 0, $post_body.length);
		$body.flush();
		$body.close();
		$response = $request.GetResponse() ;
				
		Write-Host "Start stream reading ..." -ForegroundColor Green
		
		Write-Host "Define a MemoryStream and a StreamWriter for writing ..." -ForegroundColor Green
		$memStream = New-Object System.IO.MemoryStream
		$writeStream = New-Object System.IO.StreamWriter $memStream
				
		$sReader = New-Object System.IO.StreamReader($response.GetResponseStream())
				
		$inrec = $sReader.ReadLine()
		$count = 0
		while (($inrec -ne $null) -and ($count -le $lineMax))
		{
			if ($inrec -ne "")
			{
				Write-Host "`n`t $count tweets received." -ForegroundColor Yellow
				
				$writeStream.WriteLine($inrec)
				$count ++
			}
				
			$inrec=$sReader.ReadLine()
		}
		#endregion
				
		#region - Write tweets to Blob storage
		Write-Host "Write to the destination blob ..." -ForegroundColor Green
		$writeStream.Flush()
		$memStream.Seek(0, "Begin")
		$destBlob.UploadFromStream($memStream) 
				
		$sReader.close()
		#endregion
		
		Write-Host "Completed!" -ForegroundColor Green

3. 設定指令碼中的前五到八個變數：

	<table border="1">
<tr><th>變數</th><th>說明</th></tr>
<tr><td>$clusterName</td><td>這是您要執行應用程式的 HDInsight 叢集名稱。</td></tr><tr><td>$oauth_consumer_key</td><td>這是您先前在建立 Twitter 應用程式時所記下的 Twitter 應用程式<strong>消費者金鑰</strong>。</td></tr>
<tr><td>$oauth_consumer_secret</td><td>這是您先前記下的 Twitter 應用程式<strong>消費者密碼</strong>。</td></tr>
<tr><td>$oauth_token</td><td>這是您先前記下的 Twitter 應用程式<strong>存取權杖</strong>。</td></tr>
<tr><td>$oauth_token_secret</td><td>這是您先前記下的 Twitter 應用程式<strong>存取權杖密碼</strong>。</td></tr>	
<tr><td>$destBlobName</td><td>這是輸出 Blob 名稱。預設值為 <strong>tutorials/twitter/data/tweets.txt</strong>。如果您變更預設值，則 Windows PowerShell 指令碼也必須隨之變更。</td></tr>
<tr><td>$trackString</td><td>Web 服務會傳回這些關鍵字的相關推文。預設值為 <strong>Azure, Cloud, HDInsight</strong>。如果您變更預設值，則 Windows PowerShell 指令碼也要隨之變更。</td></tr>
<tr><td>$lineMax</td><td>此值會決定指令碼所將讀取的推文數。讀取 100 則推文大約需要三分鐘。您可以設定更大的數目，但下載時間將會較久。</td></tr>

</table>

5. 按 **F5** 以執行指令碼。如果發生問題，請選取所有程式碼行，然後按 **F8**，以解決問題。
6. 輸出的結尾應會顯示「完成！」。錯誤訊息會顯示為紅色。

在驗證程序中，您可以使用 Azure 儲存體總管或 Azure PowerShell，在您的 Azure Blob 儲存體上查看輸出檔案 **/tutorials/twitter/data/tweets.txt**。如需列出檔案的範例 Windows PowerShell 指令碼，請參閱[搭配 HDInsight 使用 Blob 儲存體][hdinsight-storage-powershell]。



##建立 HiveQL 指令碼

使用 Azure PowerShell 可讓您逐一執行多個 HiveQL 陳述式，或將 HiveQL 陳述式封裝到指令碼檔案中。在本教學課程中，您將會建立 HiveQL 指令碼。指令碼檔案必須上傳至 Azure Blob 儲存體。在下一節中，您將使用 Azure PowerShell 執行指令碼檔案。

>[AZURE.NOTE]已在公用 Blob 容器中上傳 Hive 指令碼檔案和含有 10,000 則推文的檔案。如果想要使用上傳的檔案，可以略過這一節。

HiveQL 指令碼將執行下列作業：

1. **捨棄 tweets_raw 資料表** (若此資料表已存在)。
2. **建立 tweets_raw Hive 資料表**。這個暫時的 Hive 結構化資料表會保存要進一步進行擷取、轉換和載入 (ETL) 處理的資料。如需資料分割的相關資訊，請參閱 [Hive 教學課程][apache-hive-tutorial]。  
3. 從來源資料夾 /tutorials/twitter/data **載入資料**。巢狀 JSON 格式的大型 Tweets 資料集現在已轉換成暫時的 Hive 資料表結構。
3. **捨棄 tweets 資料表** (若此資料表已存在)。
4. **建立 tweets 資料表**。您必須先執行另一個 ETL 程序，才能使用 Hive 來查詢 Tweets 資料集。此 ETL 程序針對您在 "twitter_raw" 資料表中儲存的資料，定義了更詳細的資料表結構描述。  
5. **插入覆寫資料表**。這個複雜的 Hive 指令碼會啟動一組冗長的 MapReduce 工作 (由 Hadoop 叢集執行)。根據資料集和叢集大小而定，這可能需要 10 分鐘。
6. **插入覆寫目錄**。執行查詢，並將資料集輸出至檔案。此查詢會傳回一份 Twitter 使用者清單，這些使用者傳送了最多含有 "Azure" 一字的推文。

**建立 Hive 指令碼並將其上傳至 Azure**

1. 開啟 Windows PowerShell ISE。
2. 將下列指令碼複製到指令碼窗格中：

		#region - variables and constants
		$clusterName = "<HDInsightClusterName>" # Enter your HDInsight cluster name
		
		$sourceDataPath = "/tutorials/twitter/data"
		$outputPath = "/tutorials/twitter/output"
		$hqlScriptFile = "tutorials/twitter/twitter.hql"
				
		$hqlStatements = @"
		set hive.exec.dynamic.partition = true;
		set hive.exec.dynamic.partition.mode = nonstrict;
				
		DROP TABLE tweets_raw;
		CREATE EXTERNAL TABLE tweets_raw (
			json_response STRING
		) 
		STORED AS TEXTFILE LOCATION '$sourceDataPath';
				
		DROP TABLE tweets;
		CREATE TABLE tweets
		(
			id BIGINT,
			created_at STRING,
			created_at_date STRING,
			created_at_year STRING,
			created_at_month STRING,
			created_at_day STRING,
			created_at_time STRING,
			in_reply_to_user_id_str STRING,
			text STRING,
			contributors STRING,
			retweeted STRING,
			truncated STRING,
			coordinates STRING,
			source STRING,
			retweet_count INT,
			url STRING,
			hashtags array<STRING>,
			user_mentions array<STRING>,
			first_hashtag STRING,
			first_user_mention STRING,
			screen_name STRING,
			name STRING,
			followers_count INT,
			listed_count INT,
			friends_count INT,
			lang STRING,
			user_location STRING,
			time_zone STRING,
			profile_image_url STRING,
			json_response STRING
		);
				
		FROM tweets_raw
		INSERT OVERWRITE TABLE tweets
		SELECT
			cast(get_json_object(json_response, '$.id_str') as BIGINT),
			get_json_object(json_response, '$.created_at'),
			concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
			substr (get_json_object(json_response, '$.created_at'),27,4)),
			substr (get_json_object(json_response, '$.created_at'),27,4),
			case substr (get_json_object(json_response,	'$.created_at'),5,3)
				when "Jan" then "01"
				when "Feb" then "02"
				when "Mar" then "03"
				when "Apr" then "04"
				when "May" then "05"
				when "Jun" then "06"
				when "Jul" then "07"
				when "Aug" then "08"
				when "Sep" then "09"
				when "Oct" then "10"
				when "Nov" then "11"
				when "Dec" then "12" end,
			substr (get_json_object(json_response, '$.created_at'),9,2),
			substr (get_json_object(json_response, '$.created_at'),12,8),
			get_json_object(json_response, '$.in_reply_to_user_id_str'),
			get_json_object(json_response, '$.text'),
			get_json_object(json_response, '$.contributors'),
			get_json_object(json_response, '$.retweeted'),
			get_json_object(json_response, '$.truncated'),
			get_json_object(json_response, '$.coordinates'),
			get_json_object(json_response, '$.source'),
			cast (get_json_object(json_response, '$.retweet_count') as INT),
			get_json_object(json_response, '$.entities.display_url'),
			array(	
				trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
				trim(lower(get_json_object(json_response, '$.entities.hashtags[1].text'))),
				trim(lower(get_json_object(json_response, '$.entities.hashtags[2].text'))),
				trim(lower(get_json_object(json_response, '$.entities.hashtags[3].text'))),
				trim(lower(get_json_object(json_response, '$.entities.hashtags[4].text')))),
			array(
				trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
				trim(lower(get_json_object(json_response, '$.entities.user_mentions[1].screen_name'))),
				trim(lower(get_json_object(json_response, '$.entities.user_mentions[2].screen_name'))),
				trim(lower(get_json_object(json_response, '$.entities.user_mentions[3].screen_name'))),
				trim(lower(get_json_object(json_response, '$.entities.user_mentions[4].screen_name')))),
			trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
			trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
			get_json_object(json_response, '$.user.screen_name'),
			get_json_object(json_response, '$.user.name'),
			cast (get_json_object(json_response, '$.user.followers_count') as INT),
			cast (get_json_object(json_response, '$.user.listed_count') as INT),
			cast (get_json_object(json_response, '$.user.friends_count') as INT),
			get_json_object(json_response, '$.user.lang'),
			get_json_object(json_response, '$.user.location'),
			get_json_object(json_response, '$.user.time_zone'),
			get_json_object(json_response, '$.user.profile_image_url'),
			json_response
		WHERE (length(json_response) > 500);
				
		INSERT OVERWRITE DIRECTORY '$outputPath'
		SELECT name, screen_name, count(1) as cc 
			FROM tweets 
			WHERE text like "%Azure%" 
			GROUP BY name,screen_name 
			ORDER BY cc DESC LIMIT 10;
		"@
		#endregion		
		
		#region - Connect to Azure subscription
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		Add-AzureAccount
		#endregion
			
		#region - Create a block blob object for writing the Hive script file
		Write-Host "Get the default storage account name and container name based on the cluster name ..." -ForegroundColor Green
		$myCluster = Get-AzureHDInsightCluster -Name $clusterName
		$storageAccountName = $myCluster.DefaultStorageAccount.StorageAccountName.Replace(".blob.core.windows.net", "")	
		$containerName = $myCluster.DefaultStorageAccount.StorageContainerName
		Write-Host "`tThe storage account name is $storageAccountName." -ForegroundColor Yellow
		Write-Host "`tThe blob container name is $containerName." -ForegroundColor Yellow
				
		Write-Host "Define the connection string ..." -ForegroundColor Green
		$storageAccountKey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		$storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"
				
		Write-Host "Create block blob objects referencing the hql script file" -ForegroundColor Green
		$storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
		$storageClient = $storageAccount.CreateCloudBlobClient();
		$storageContainer = $storageClient.GetContainerReference($containerName)
		$hqlScriptBlob = $storageContainer.GetBlockBlobReference($hqlScriptFile)
				
		Write-Host "Define a MemoryStream and a StreamWriter for writing ... " -ForegroundColor Green
		$memStream = New-Object System.IO.MemoryStream
		$writeStream = New-Object System.IO.StreamWriter $memStream
		$writeStream.Writeline($hqlStatements)
		#endregion
		
		#region - Write the Hive script file to Blob storage		
		Write-Host "Write to the destination blob ... " -ForegroundColor Green
		$writeStream.Flush()
		$memStream.Seek(0, "Begin")
		$hqlScriptBlob.UploadFromStream($memStream)
		#endregion
				
		Write-Host "Completed!" -ForegroundColor Green


4. 設定指令碼中的前兩個變數：

	<table border="1">
<tr><th>變數</th><th>說明</th></tr>
<tr><td>$clusterName</td><td># 提供您要在其中執行 Hive 工作的 HDInsight 叢集名稱</td></tr>
<tr><td>$sourceDataPath</td><td>Hive 查詢將從中讀取資料的 Azure Blob 儲存體位置。您無須變更此變數。</td></tr>
<tr><td>$outputPath</td><td>Hive 查詢將輸出結果的 Azure Blob 儲存體位置。您無須變更此變數。</td></tr>
<tr><td>$hqlScriptFile</td><td>HiveQL 指令碼檔案的位置和檔案名稱。您無須變更此變數。</td></tr>
</table>

5. 按 **F5** 以執行指令碼。如果發生問題，請選取所有程式碼行，然後按 **F8**，以解決問題。
6. 輸出的結尾應會顯示「完成！」。錯誤訊息會顯示為紅色。

在驗證程序中，您可以使用 Azure 儲存體總管或 Azure PowerShell，在您的 Azure Blob 儲存體上查看輸出檔案 **/tutorials/twitter/twitter.hql**。如需列出檔案的範例 Windows PowerShell 指令碼，請參閱[搭配 HDInsight 使用 Blob 儲存體][hdinsight-storage-powershell]。


##使用 Hive 處理 Twitter 資料

您已完成所有準備工作。現在，您可以叫用 Hive 指令碼，並查看結果。

### 提交 Hive 工作

使用下列 Window PowerShell 指令碼可執行 Hive 指令碼。您必須設定第一個變數。

>[AZURE.NOTE]若要使用您在上兩節中上傳的推文和 HiveQL 指令碼，請將 $hqlScriptFile 設定為 "/tutorials/twitter/twitter.hql"。若要使用已為您上傳至公用 Blob 的檔案，請將 $hqlScriptFile 設定為 "wasb://twittertrend@hditutorialdata.blob.core.windows.net/twitter.hql"。

	#region variables and constants
	$clusterName = "<HDInsightClusterName>"
	
	#use one of the following
	$hqlScriptFile = "wasbs://twittertrend@hditutorialdata.blob.core.windows.net/twitter.hql"
	$hqlScriptFile = "/tutorials/twitter/twitter.hql"

	$statusFolder = "/tutorials/twitter/jobstatus"
	#endregion
	
	#region - Invoke Hive
	Write-Host "Invoke Hive ... " -ForegroundColor Green
	Use-AzureHDInsightCluster $clusterName
	$response = Invoke-Hive -file $hqlScriptFile -StatusFolder $statusFolder -OutVariable $outVariable
	
	Write-Host "Display the standard error log ... " -ForegroundColor Green
	$jobID = ($response | Select-String job_ | Select-Object -First 1) -replace ‘\s*$’ -replace ‘.*\s’
	Get-AzureHDInsightJobOutput -cluster $clusterName -JobId $jobID -StandardError 
	#endregion

### 查看結果

使用下列 Windows PowerShell 指令碼可查看 Hive 工作輸出。您必須設定前兩個變數。

	#region variables and constants
	$clusterName = "<HDInsightClusterName>"
	
	$blob = "tutorials/twitter/output/000000_0" # The name of the blob to be downloaded.
	#engregion
	
	#region - Create an Azure storage context object
	Write-Host "Get the default storage account name and container name based on the cluster name ..." -ForegroundColor Green
	$myCluster = Get-AzureHDInsightCluster -Name $clusterName
	$storageAccountName = $myCluster.DefaultStorageAccount.StorageAccountName.Replace(".blob.core.windows.net", "")	
	$containerName = $myCluster.DefaultStorageAccount.StorageContainerName
	Write-Host "`tThe storage account name is $storageAccountName." -ForegroundColor Yellow
	Write-Host "`tThe blob container name is $containerName." -ForegroundColor Yellow
	
	Write-Host "Create a context object ... " -ForegroundColor Green
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	#endregion 
	
	#region - Download blob and display blob
	Write-Host "Download the blob ..." -ForegroundColor Green
	cd $HOME
	Get-AzureStorageBlobContent -Container $ContainerName -Blob $blob -Context $storageContext -Force
	
	Write-Host "Display the output ..." -ForegroundColor Green
	Write-Host "==================================" -ForegroundColor Green
	cat "./$blob"
	Write-Host "==================================" -ForegroundColor Green
	#end region

> [AZURE.NOTE]Hive 資料表會使用 \\001 做為欄位分隔符號。此分隔符號不會顯示在輸出中。

分析結果列示在 Azure Blob 儲存體之後，您可以將資料匯出至 Azure SQL Database/SQL Server，使用 Power Query 將資料匯出至 Excel，或使用 Hive ODBC 驅動程式將應用程式連接到資料。如需詳細資訊，請參閱[搭配 HDInsight 使用 Sqoop][hdinsight-use-sqoop]、[使用 HDInsight 分析航班延誤資料][hdinsight-analyze-flight-delay-data]、[使用 Power Query 將 Excel 連接到 HDInsight][hdinsight-power-query] 和[使用 Microsoft Hive ODBC 驅動程式將 Excel 連接到 HDInsight][hdinsight-hive-odbc]。

##後續步驟

本教學課程中，我們看到如何將非結構化 JSON 資料集轉換成結構化 Hive 資料表，然後在 Azure 上使用 HDInsight 來查詢、探索和分析來自 Twitter 的資料。若要深入了解，請參閱：

- [開始使用 HDInsight][hdinsight-get-started]
- [使用 HDInsight 中的 HBase 分析即時的 Twitter 情緒][hdinsight-hbase-twitter-sentiment]
- [使用 HDInsight 分析航班延誤資料][hdinsight-analyze-flight-delay-data]
- [使用 Power Query 將 Excel 連接到 HDInsight][hdinsight-power-query]
- [使用 Microsoft Hive ODBC Driver 將 Excel 連接到 HDInsight][hdinsight-hive-odbc]
- [搭配 HDInsight 使用 Sqoop][hdinsight-use-sqoop]

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: ../install-configure-powershell.md
[powershell-script]: http://technet.microsoft.com/library/ee176961.aspx


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-storage-powershell]: ../hdinsight-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
 

<!---HONumber=July15_HO2-->