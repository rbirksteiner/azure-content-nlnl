<properties 
	pageTitle="使用 HBase 分析即時的 Twitter 情緒 | Microsoft Azure" 
	description="了解如何使用 HDInsight (Hadoop) 叢集中的 HBase，從 Twitter 進行巨量資料的即時情緒分析。" 
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
	ms.date="01/27/2015" 
	ms.author="jgao"/>

# 使用 HDInsight 中的 HBase 分析即時的 Twitter 情緒

了解如何使用 HDInsight (Hadoop) 叢集中的 HBase，從 Twitter 進行巨量資料的即時[情緒分析](http://en.wikipedia.org/wiki/Sentiment_analysis)。


社交網站是巨量資料採用的主要驅使力之一。像 Twitter 之類的網站所提供的公開 API，是分析和了解流行趨勢的一項實用的資料來源。在本教學課程中，您將開發主控台串流服務應用程式和 ASP.NET Web 應用程式，來執行下列作業：

![][img-app-arch]

- 串流應用程式
	- 使用 Twitter 串流 API 即時取得標記地理位置的推文
	- 評估這些推文的情緒
	- 使用 Microsoft HBase SDK 將情緒資訊儲存於 HBase 中
- Azure 網站應用程式
	- 使用 ASP.NET Web 應用程式將即時統計結果繪製在 Bing 地圖上。以視覺方式呈現推文的結果與以下範例相似：

	![hdinsight.hbase.twitter.sentiment.bing.map][img-bing-map]
	
	您將可以利用某些關鍵字來查詢推文，藉此感知推文中表達的意見是肯定、否定或中性。

如需完整的 Visual Studio 方案範例，請前往 GitHub：[即時社交情緒分析應用程式](https://github.com/maxluk/tweet-sentiment)。































##<a id="prerequisites"></a>必要條件
開始進行本教學課程之前，您必須具備下列條件：

- **HDInsight 中的 HBase 叢集**。如需佈建叢集的指示，請參閱[開始在 HDInsight 中搭配使用 HBase 與 Hadoop][hbase-get-started]。進行教學課程時，您將需要下列資料：


	<table border="1">
<tr><th>叢集屬性</th><th>說明</th></tr>
<tr><td>HBase 叢集名稱</td><td>您的 HDInsight HBase 叢集名稱。例如：https://myhbase.azurehdinsight.net/</td></tr>
<tr><td>叢集使用者名稱</td><td>Hadoop 使用者帳戶名稱。預設 Hadoop 使用者名稱為 <strong>admin</strong>。</td></tr>
<tr><td>叢集使用者密碼</td><td>Hadoop 叢集使用者密碼。</td></tr>
</table>

- 安裝 Visual Studio 2013 的**工作站**。如需指示，請參閱[安裝 Visual Studio](http://msdn.microsoft.com/library/e2h7fzkw.aspx)。





##<a id="twitter"></a>建立 Twitter 應用程式識別碼和密碼

Twitter 串流 API 使用 [OAuth](http://oauth.net/) 以授權要求。使用 OAuth 的第一個步驟，是在 Twitter 開發人員網站上建立新的應用程式。

**建立 Twitter 應用程式 ID 和密碼**

1. 登入 [Twitter 應用程式](https://apps.twitter.com/)。如果您沒有 Twitter 帳戶，請按一下[立即註冊] 連結。
2. 按一下 [建立新的應用程式]。
3. 輸入 [名稱]、[描述] 和 [網站]。我們實際上不會用到 [網站] 欄位。因此您不必輸入有效的 URL。下表列出部分要使用的範例值：

	<table border="1">
<tr><th>欄位</th><th>值</th></tr>
<tr><td>名稱</td><td>MyHDInsightHBaseApp</td></tr>
<tr><td>說明</td><td>MyHDInsightHBaseApp</td></tr>
<tr><td>網站</td><td>http://www.myhdinsighthbaseapp.com</td></tr>
</table>> [AZURE.NOTE]Twitter 應用程式名稱必須是唯一的名稱。

4. 核取 [Yes, I agree]然後按一下 [Create your Twitter application]。
5. 按一下 [權限] 索引標籤。預設權限為 [唯讀]。本教學課程使用預設值即可。 
6. 按一下 **[金鑰和存取權杖**] 索引標籤。
7. 按一下 [Create my access token]。
8. 按一下位於頁面右上角的 [測試 OAuth]。
9. 複製 [消費者金鑰]、[消費者密碼]、[存取權杖] 和 [存取權杖密碼] 等值。稍後會在教學課程中使用這些值。

	![hdi.hbase.twitter.sentiment.twitter.app][img-twitter-app]






























##<a id="streaming"></a> 建立簡易的 Twitter 串流服務

您必須建立主控台應用程式以取得推文、計算推文情緒分數，以及將處理過的推文文字傳送到 HBase。

**下載情緒字典檔案**

1. 瀏覽到[即時社交情緒分析應用程式](https://github.com/maxluk/tweet-sentiment)。
2. 按一下 [下載 ZIP]。
3. 將檔案解壓縮到本機。
4. 複製 **../tweet-sentiment/SimpleStreamingService/data/dictionary/dictionary.tsv** 檔案的路徑。您將需要在應用程式中使用它。

**建立 Visual Studio 方案**

1. 開啟 **Visual Studio**。
2. 在 [檔案] 功能表中指向 [新增]，然後按一下 [專案]。
3. 輸入或選取下列值：

	- 範本：**Visual C# / Windows 桌面 / 主控台應用程式**
	- 名稱：**TweetSentimentStreaming** 
	- 位置：**C:\\Tutorials**
	- 方案名稱：**TweetSentimentStreaming**

4. 按一下 [確定] 以繼續。
 


**安裝 NuGet 套件及加入 SDK 參考**

1. 在 [工具] 功能表中按一下 [Nuget 套件管理員]，然後按一下 [Package Manager Console]。主控台面板會在頁面底部開啟。
2. 使用下列命令來安裝 [HBase .NET SDK](https://www.nuget.org/packages/Microsoft.HBase.Client/) 封裝 (存取 HBase 叢集的用戶端程式庫) 和 [Tweetinvi API](https://www.nuget.org/packages/TweetinviAPI/) 封裝 (用來存取 Twitter API)。

		Install-Package Microsoft.HBase.Client
		Install-Package TweetinviAPI
	
3. 在 [方案總管] 中，以滑鼠右鍵按一下 [參考]，然後按一下 [加入參考]。
4. 在左窗格中，展開 [組件]，然後按一下 [架構]。
5. 在右窗格中，選取 [System.Configuration] 前的核取方塊，然後按一下 [確定]。



**定義 Tweeter 串流服務類別**

1. 在 **方案總管** 中，以滑鼠右鍵按一下 [TweetSentimentStreaming]、指向 [加入]，然後按一下 [類別]。
2. 在 [名稱] 欄位中輸入 **HBaseWriter**，然後按一下 [加入]。
3. 在 **HBaseWriter.cs** 中，於檔案頂端加入以下 **using** 陳述式：

		using System.IO;		
		using System.Threading;
		using System.Globalization;
		using Microsoft.HBase.Client;
		using Tweetinvi.Core.Interfaces;
		using org.apache.hadoop.hbase.rest.protobuf.generated;

4. 在 **HbaseWriter.cs** 中新增類別呼叫 **DictionaryItem**：

	    public class DictionaryItem
	    {
	        public string Type { get; set; }
	        public int Length { get; set; }
	        public string Word { get; set; }
	        public string Pos { get; set; }
	        public string Stemmed { get; set; }
	        public string Polarity { get; set; }
	    }

	此類別結構可用來剖析情緒字典檔案。資料可用來計算每則推文的情緒分數。

5. 在 **HBaseWriter** 類別中，定義以下常數和變數：

        // HDinsight HBase cluster and HBase table information
        const string CLUSTERNAME = "https://<HBaseClusterName>.azurehdinsight.net/";
        const string HADOOPUSERNAME = "<HadoopUserName>"; //the default name is "admin"
        const string HADOOPUSERPASSWORD = "<HaddopUserPassword>";
        const string HBASETABLENAME = "tweets_by_words";

        // Sentiment dictionary file and the punctuation characters
        const string DICTIONARYFILENAME = @"....\data\dictionary\dictionary.tsv";
        private static char[] _punctuationChars = new[] { 
            ' ', '!', '"', '#', '$', '%', '&', ''', '(', ')', '*', '+', ',', '-', '.', '/',   //ascii 23--47
            ':', ';', '<', '=', '>', '?', '@', '[', ']', '^', '_', '`', '{', '|', '}', '~' };   //ascii 58--64 + misc.

        // For writting to HBase
        HBaseClient client;

        // a sentiment dictionary for estimate sentiment. It is loaded from a physical file.
        Dictionary<string, DictionaryItem> dictionary;
        
        // use multithread write
        Thread writerThread;
        Queue<ITweet> queue = new Queue<ITweet>();
        bool threadRunning = true;

6. 設定常數值，包括 **&lt;HBaseClusterName>**、**&lt;HadoopUserName>** 和 **&lt;HaddopUserPassword>**。如果您想要變更 HBase 資料表名稱，則必須連帶變更 Web 應用程式中的資料表名稱。

	在本教學課程稍後的內容中，您將需要下載 dictionary.tsv 檔案並將其移動到特定資料夾。

7. 定義 **HBaseWriter** 類別中的以下函數：

		// This function connects to HBase, loads the sentiment dictionary, and starts the thread for writting.
        public HBaseWriter()
        {
            ClusterCredentials credentials = new ClusterCredentials(new Uri(CLUSTERNAME), HADOOPUSERNAME, HADOOPUSERPASSWORD);
            client = new HBaseClient(credentials);

            // create the HBase table if it doesn't exist
            if (!client.ListTables().name.Contains(HBASETABLENAME))
            {
                TableSchema tableSchema = new TableSchema();
                tableSchema.name = HBASETABLENAME;
                tableSchema.columns.Add(new ColumnSchema { name = "d" });
                client.CreateTable(tableSchema);
                Console.WriteLine("Table "{0}" is created.", HBASETABLENAME);
            }

            // Load sentiment dictionary from a file
            LoadDictionary();

			// Start a thread for writting to HBase
            writerThread = new Thread(new ThreadStart(WriterThreadFunction));
            writerThread.Start();
        }

        ~HBaseWriter()
        {
            threadRunning = false;
        }

        // Enqueue the Tweets received
        public void WriteTweet(ITweet tweet)
        {
            lock (queue)
            {
                queue.Enqueue(tweet);
            }
        }

        // Load sentiment dictionary from a file
        private void LoadDictionary()
        {
            List<string> lines = File.ReadAllLines(DICTIONARYFILENAME).ToList();
            var items = lines.Select(line =>
            {
                var fields = line.Split('\t');
                var pos = 0;
                return new DictionaryItem
                {
                    Type = fields[pos++],
                    Length = Convert.ToInt32(fields[pos++]),
                    Word = fields[pos++],
                    Pos = fields[pos++],
                    Stemmed = fields[pos++],
                    Polarity = fields[pos++]
                };
            });

            dictionary = new Dictionary<string, DictionaryItem>();
            foreach (var item in items)
            {
                if (!dictionary.Keys.Contains(item.Word))
                {
                    dictionary.Add(item.Word, item);
                }
            }
        }

        // Calculate sentiment score
        private int CalcSentimentScore(string[] words)
        {
            Int32 total = 0;
            foreach (string word in words)
            {
                if (dictionary.Keys.Contains(word))
                {
                    switch (dictionary[word].Polarity)
                    {
                        case "negative": total -= 1; break;
                        case "positive": total += 1; break;
                    }
                }
            }
            if (total > 0)
            {
                return 1;
            }
            else if (total < 0)
            {
                return -1;
            }
            else
            {
                return 0;
            }
        }

		// Popular a CellSet object to be written into HBase
        private void CreateTweetByWordsCells(CellSet set, ITweet tweet)
        {
            // Split the Tweet into words
            string[] words = tweet.Text.ToLower().Split(_punctuationChars);

            // Calculate sentiment score base on the words
            int sentimentScore = CalcSentimentScore(words);
            var word_pairs = words.Take(words.Length - 1)
                                  .Select((word, idx) => string.Format("{0} {1}", word, words[idx + 1]));
            var all_words = words.Concat(word_pairs).ToList();

            // For each word in the Tweet add a row to the HBase table
            foreach (string word in all_words)
            {
                string time_index = (ulong.MaxValue - (ulong)tweet.CreatedAt.ToBinary()).ToString().PadLeft(20) + tweet.IdStr;
                string key = word + "_" + time_index;

                // Create a row
                var row = new CellSet.Row { key = Encoding.UTF8.GetBytes(key) };

                // Add columns to the row, including Tweet identifier, language, coordinator(if available), and sentiment 
                var value = new Cell { column = Encoding.UTF8.GetBytes("d:id_str"), data = Encoding.UTF8.GetBytes(tweet.IdStr) };
                row.values.Add(value);
                
                value = new Cell { column = Encoding.UTF8.GetBytes("d:lang"), data = Encoding.UTF8.GetBytes(tweet.Language.ToString()) };
                row.values.Add(value);
                
                if (tweet.Coordinates != null)
                {
                    var str = tweet.Coordinates.Longitude.ToString() + "," + tweet.Coordinates.Latitude.ToString();
                    value = new Cell { column = Encoding.UTF8.GetBytes("d:coor"), data = Encoding.UTF8.GetBytes(str) };
                    row.values.Add(value);
                }

                value = new Cell { column = Encoding.UTF8.GetBytes("d:sentiment"), data = Encoding.UTF8.GetBytes(sentimentScore.ToString()) };
                row.values.Add(value);

                set.rows.Add(row);
            }
        }

        // Write a Tweet (CellSet) to HBase
        public void WriterThreadFunction()
        {
            try
            {
                while (threadRunning)
                {
                    if (queue.Count > 0)
                    {
                        CellSet set = new CellSet();
                        lock (queue)
                        {
                            do
                            {
                                ITweet tweet = queue.Dequeue();
                                CreateTweetByWordsCells(set, tweet);
                            } while (queue.Count > 0);
                        }

                        // Write the Tweet by words cell set to the HBase table
                        client.StoreCells(HBASETABLENAME, set);
                        Console.WriteLine("\tRows written: {0}", set.rows.Count);
                    }
                    Thread.Sleep(100);
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Exception: " + ex.Message);
            }
        }

	此程式碼提供以下功能：

	- **連接到 Hbase [ HBaseWriter() ]**：使用 HBase SDK 來建立含有叢集 URL 和 Hadoop 使用者認證的 *ClusterCredentials* 物件，然後使用 ClusterCredentials 物件來建立 *HBaseClient* 物件。
	- **建立 HBase 資料表 [ HBaseWriter() ]**：方法呼叫是 *HBaseClient.CreateTable()*。
	- **寫入 HBase 資料表 [ WriterThreadFunction() ]**：方法呼叫是 *HBaseClient.StoreCells()*。

**完成 Program.cs**

1. 在 [方案總管] 中，按兩下 **Program.cs** 加以開啟。
2. 在檔案開頭加入下列 **using** 陳述式：

		using System.Configuration;
		using System.Diagnostics;
		using Tweetinvi;

3. 在 **Program** 類別中定義以下常數：

        const string TWITTERAPPACCESSTOKEN = "<TwitterApplicationAccessToken";
        const string TWITTERAPPACCESSTOKENSECRET = "TwitterApplicationAccessTokenSecret";
        const string TWITTERAPPAPIKEY = "TwitterApplicationAPIKey";
        const string TWITTERAPPAPISECRET = "TwitterApplicationAPISecret";

4. 設定與 Twitter 應用程式值相符的常數值。

3. 修改 **Main()** 函數，使其外觀類似：

		static void Main(string[] args)
		{
            TwitterCredentials.SetCredentials(TWITTERAPPACCESSTOKEN, TWITTERAPPACCESSTOKENSECRET, TWITTERAPPAPIKEY, TWITTERAPPAPISECRET);

            Stream_FilteredStreamExample();
		}

4. 在類別中新增下列函數：

        private static void Stream_FilteredStreamExample()
        {
            for (; ; )
            {
                try
                {
                    HBaseWriter hbase = new HBaseWriter();
                    var stream = Stream.CreateFilteredStream();
                    stream.AddLocation(Geo.GenerateLocation(-180, -90, 180, 90));

                    var tweetCount = 0;
                    var timer = Stopwatch.StartNew();

                    stream.MatchingTweetReceived += (sender, args) =>
                    {
                        tweetCount++;
                        var tweet = args.Tweet;

                        // Write Tweets to HBase
                        hbase.WriteTweet(tweet);

                        if (timer.ElapsedMilliseconds > 1000)
                        {
                            if (tweet.Coordinates != null)
                            {
                                Console.ForegroundColor = ConsoleColor.Green;
                                Console.WriteLine("\n{0}: {1} {2}", tweet.Id, tweet.Language.ToString(), tweet.Text);
                                Console.ForegroundColor = ConsoleColor.White;
                                Console.WriteLine("\tLocation: {0}, {1}", tweet.Coordinates.Longitude, tweet.Coordinates.Latitude);
                            }

                            timer.Restart();
                            Console.WriteLine("\tTweets/sec: {0}", tweetCount);
                            tweetCount = 0;
                        }
                    };

                    stream.StartStreamMatchingAllConditions();
                }
                catch (Exception ex)
                {
                    Console.WriteLine("Exception: {0}", ex.Message);
                }
            }
        }



**執行串流服務**

1. 在 Visual Studio 內按 **F5**。以下是主控台應用程式的螢幕擷取畫面：

	![hdinsight.hbase.twitter.sentiment.streaming.service][img-streaming-service]
2. 開發 Web 應用程式時，請保持串流主控台應用程式的執行狀態，如此一來，您將有更多的資料可使用。



























##<a id="web"></a> 使用 Azure 網站建立網站以將 Twitter 情緒視覺化

在本節中，您將建立 ASP.NET MVC Web 應用程式，以便從 HBase 讀取即時情緒資料，並將資料繪製在 Bing 地圖上。

**建立 ASP.NET MVC Web 應用程式**

1. 開啟 Visual Studio。
2. 依序按一下 [檔案]、[新增] 及 [專案]。
3. 輸入以下資訊：

	- 範本類別：**Visual C#/Web**
	- 範本：**ASP.NET Web 應用程式**
	- 名稱：**TweetSentimentWeb**
	- 位置：**C:\\Tutorials** 
4. 按一下 [確定]。
5. 在 [選取範本] 中按一下 **MVC**。 
6. 在 **Microsoft Azure** 中按一下 [管理訂用帳戶]。
7. 在 [管理 Microsoft Azure 訂用帳戶] 中按一下 [登入]。
8. 輸入您的 Azure 認證。系統會將您的 Azure 訂用帳戶資訊顯示在 [帳戶] 索引標籤。
9. 按一下 [關閉] 以關閉 [管理 Microsoft Azure 訂用帳戶] 視窗。
10. 在 [新增 ASP.NET 專案 - TweetSentimentWeb] 中按一下 [確定]。
11. 在 [設定 Microsoft Azure 網站設定] 中，選取與您最接近的 [區域]。您不需要指定資料庫伺服器。 
12. 按一下 [確定]。

**安裝 NuGet 套件**

1. 在 [工具] 功能表中按一下 [Nuget 套件管理員]，然後按一下 [Package Manager Console]。主控台面板會在頁面底部開啟。
2. 使用下列命令來安裝 [HBase .NET SDK](https://www.nuget.org/packages/Microsoft.HBase.Client/) 封裝 (存取 HBase 叢集的用戶端檔案庫)：

		Install-Package Microsoft.HBase.Client 

**加入 HBaseReader 類別**

1. 在 [方案總管] 中展開 **TweetSentiment**。
2. 以滑鼠右鍵按一下 [模型]，然後依序按一下 [新增] 和 [類別]。
3. 在 [名稱] 欄位中輸入 **HBaseReader.cs**，然後按一下 [加入]。
4. 使用下列程式碼來取代此程式碼：

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Web;
		
		using System.Configuration;
		using System.Threading.Tasks;
		using System.Text;
		using Microsoft.HBase.Client;
		using org.apache.hadoop.hbase.rest.protobuf.generated;
		
		namespace TweetSentimentWeb.Models
		{
		    public class HBaseReader
		    {
		        // For reading Tweet sentiment data from HDInsight HBase
		        HBaseClient client;
		
		        // HDinsight HBase cluster and HBase table information
		        const string CLUSTERNAME = "<HBaseClusterName>";
		        const string HADOOPUSERNAME = "<HBaseClusterHadoopUserName>"
		        const string HADOOPUSERPASSWORD = "<HBaseCluserUserPassword>";
		        const string HBASETABLENAME = "tweets_by_words";
		
		        // The constructor
		        public HBaseReader()
		        {
		            ClusterCredentials creds = new ClusterCredentials(
		                            new Uri(CLUSTERNAME),
		                            HADOOPUSERNAME,
		                            HADOOPUSERPASSWORD);
		            client = new HBaseClient(creds);
		        }
		
		        // Query Tweets sentiment data from the HBase table asynchronously 
		        public async Task<IEnumerable<Tweet>> QueryTweetsByKeywordAsync(string keyword)
		        {
		            List<Tweet> list = new List<Tweet>();
		
		            // Demonstrate Filtering the data from the past 6 hours the row key
		            string timeIndex = (ulong.MaxValue -
		                (ulong)DateTime.UtcNow.Subtract(new TimeSpan(6, 0, 0)).ToBinary()).ToString().PadLeft(20);
		            string startRow = keyword + "_" + timeIndex;
		            string endRow = keyword + "|";
		            Scanner scanSettings = new Scanner
		            {
		                batch = 100000,
		                startRow = Encoding.UTF8.GetBytes(startRow),
		                endRow = Encoding.UTF8.GetBytes(endRow)
		            };
		
		            // Make async scan call
		            ScannerInformation scannerInfo =
		                await client.CreateScannerAsync(HBASETABLENAME, scanSettings);
		
		            CellSet next;
		
		            while ((next = await client.ScannerGetNextAsync(scannerInfo)) != null)
		            {
		                foreach (CellSet.Row row in next.rows)
		                {
		                    // find the cell with string pattern "d:coor" 
		                    var coordinates =
		                        row.values.Find(c => Encoding.UTF8.GetString(c.column) == "d:coor");
		
		                    if (coordinates != null)
		                    {
		                        string[] lonlat = Encoding.UTF8.GetString(coordinates.data).Split(',');
		
		                        var sentimentField =
		                            row.values.Find(c => Encoding.UTF8.GetString(c.column) == "d:sentiment");
		                        Int32 sentiment = 0;
		                        if (sentimentField != null)
		                        {
		                            sentiment = Convert.ToInt32(Encoding.UTF8.GetString(sentimentField.data));
		                        }
		
		                        list.Add(new Tweet
		                        {
		                            Longtitude = Convert.ToDouble(lonlat[0]),
		                            Latitude = Convert.ToDouble(lonlat[1]),
		                            Sentiment = sentiment
		                        });
		                    }
		
		                    if (coordinates != null)
		                    {
		                        string[] lonlat = Encoding.UTF8.GetString(coordinates.data).Split(',');
		                    }
		                }
		            }
		
		            return list;
		        }
		    }
		
		    public class Tweet
		    {
		        public string IdStr { get; set; }
		        public string Text { get; set; }
		        public string Lang { get; set; }
		        public double Longtitude { get; set; }
		        public double Latitude { get; set; }
		        public int Sentiment { get; set; }
		    }
		}

4. 在 **HBaseReader** 類別中變更常數值，如下所示：

	- **CLUSTERNAME**：HBase 叢集名稱，例如 *https://<HBaseClusterName>.azurehdinsight.net/*。 
    - **HADOOPUSERNAME**：HBase 叢集 Hadoop 使用者的使用者名稱。預設名稱為 *admin*。
    - **HADOOPUSERPASSWORD**：HBase 叢集 Hadoop 使用者密碼。
    - **HBASETABLENAME** = "tweets_by_words";

	HBase 資料表名稱為 **"tweets_by_words";**。這些值必須符合在串流服務中傳送的值，如此一來，Web 應用程式才能從相同的 HBase 資料表讀取資料。




**加入 TweetsController 控制器**

1. 在 [方案總管] 中展開 **TweetSentimentWeb**。
2. 以滑鼠右鍵按一下 [控制器]，然後依序按一下 [新增] 和 [控制器]。
3. 按一下 **Web API 2 Controller - Empty**，然後按一下 [新增]。
4. 在 [控制器名稱] 中輸入 **TweetsController**，然後按一下 [加入]。
5. 在 [方案總管] 中，按兩下 TweetsController.cs 以開啟檔案。
5. 修改檔案，使其類似下列內容：

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Net;
		using System.Net.Http;
		using System.Web.Http;
		
		using System.Threading.Tasks;
		using TweetSentimentWeb.Models;
		
		namespace TweetSentimentWeb.Controllers
		{
		    public class TweetsController : ApiController
		    {
		        HBaseReader hbase = new HBaseReader();
		
		        public async Task<IEnumerable<Tweet>> GetTweetsByQuery(string query)
		        {
		            return await hbase.QueryTweetsByKeywordAsync(query);
		        }
		    }
		}

**加入 heatmap.js**

1. 在 [方案總管] 中展開 **TweetSentimentWeb**。
2. 以滑鼠右鍵按一下 [指令碼]，然後依序按一下 [新增] 和 [JavaScript 檔案]。
3. 在 [項目名稱] 欄位輸入 **heatmap.js**。
4. 將下列程式碼貼到檔案中。該程式碼由 Alastair Aitchison 所撰寫。如需詳細資訊，請參閱 [BBing 地圖 AJAX v7 HeatMap 程式庫](http://alastaira.wordpress.com/2011/04/15/bing-maps-ajax-v7-heatmap-library/)。

		/*******************************************************************************
		* Author: Alastair Aitchison
		* Website: http://alastaira.wordpress.com
		* Date: 15th April 2011
		* 
		* Description: 
		* This JavaScript file provides an algorithm that can be used to add a heatmap
		* overlay on a Bing Maps v7 control. The intensity and temperature palette
		* of the heatmap are designed to be easily customisable.
		*
		* Requirements:
		* The heatmap layer itself is created dynamically on the client-side using
		* the HTML5 <canvas> element, and therefore requires a browser that supports
		* this element. It has been tested on IE9, Firefox 3.6/4 and 
		* Chrome 10 browsers. If you can confirm whether it works on other browsers or
		* not, I'd love to hear from you!
		
		* Usage:
		* The HeatMapLayer constructor requires:
		* - A reference to a map object
		* - An array or Microsoft.Maps.Location items
		* - Optional parameters to customise the appearance of the layer
		*  (Radius,, Unit, Intensity, and ColourGradient), and a callback function
		*
		*/
		
		var HeatMapLayer = function (map, locations, options) {
		
		    /* Private Properties */
		    var _map = map,
		      _canvas,
		      _temperaturemap,
		      _locations = [],
		      _viewchangestarthandler,
		      _viewchangeendhandler;
		
		    // Set default options
		    var _options = {
		        // Opacity at the centre of each heat point
		        intensity: 0.5,
		
		        // Affected radius of each heat point
		        radius: 1000,
		
		        // Whether the radius is an absolute pixel value or meters
		        unit: 'meters',
		
		        // Colour temperature gradient of the map
		        colourgradient: {
		            "0.00": 'rgba(255,0,255,20)',  // Magenta
		            "0.25": 'rgba(0,0,255,40)',    // Blue
		            "0.50": 'rgba(0,255,0,80)',    // Green
		            "0.75": 'rgba(255,255,0,120)', // Yellow
		            "1.00": 'rgba(255,0,0,150)'    // Red
		        },
		
		        // Callback function to be fired after heatmap layer has been redrawn 
		        callback: null
		    };
		
		    /* Private Methods */
		    function _init() {
		        var _mapDiv = _map.getRootElement();
		
		        if (_mapDiv.childNodes.length >= 3 && _mapDiv.childNodes[2].childNodes.length >= 2) {
		            // Create the canvas element
		            _canvas = document.createElement('canvas');
		            _canvas.style.position = 'relative';
		
		            var container = document.createElement('div');
		            container.style.position = 'absolute';
		            container.style.left = '0px';
		            container.style.top = '0px';
		            container.appendChild(_canvas);
		
		            _mapDiv.childNodes[2].childNodes[1].appendChild(container);
		
		            // Override defaults with any options passed in the constructor
		            _setOptions(options);
		
		            // Load array of location data
		            _setPoints(locations);
		
		            // Create a colour gradient from the suppied colourstops
		            _temperaturemap = _createColourGradient(_options.colourgradient);
		
		            // Wire up the event handler to redraw heatmap canvas
		            _viewchangestarthandler = Microsoft.Maps.Events.addHandler(_map, 'viewchangestart', _clearHeatMap);
		            _viewchangeendhandler = Microsoft.Maps.Events.addHandler(_map, 'viewchangeend', _createHeatMap);
		
		            _createHeatMap();
		
		            delete _init;
		        } else {
		            setTimeout(_init, 100);
		        }
		    }
		
		    // Resets the heat map
		    function _clearHeatMap() {
		        var ctx = _canvas.getContext("2d");
		        ctx.clearRect(0, 0, _canvas.width, _canvas.height);
		    }
		
		    // Creates a colour gradient from supplied colour stops on initialisation
		    function _createColourGradient(colourstops) {
		        var ctx = document.createElement('canvas').getContext('2d');
		        var grd = ctx.createLinearGradient(0, 0, 256, 0);
		        for (var c in colourstops) {
		            grd.addColorStop(c, colourstops[c]);
		        }
		        ctx.fillStyle = grd;
		        ctx.fillRect(0, 0, 256, 1);
		        return ctx.getImageData(0, 0, 256, 1).data;
		    }
		
		    // Applies a colour gradient to the intensity map
		    function _colouriseHeatMap() {
		        var ctx = _canvas.getContext("2d");
		        var dat = ctx.getImageData(0, 0, _canvas.width, _canvas.height);
		        var pix = dat.data; // pix is a CanvasPixelArray containing height x width x 4 bytes of data (RGBA)
		        for (var p = 0, len = pix.length; p < len;) {
		            var a = pix[p + 3] * 4; // get the alpha of this pixel
		            if (a != 0) { // If there is any data to plot
		                pix[p] = _temperaturemap[a]; // set the red value of the gradient that corresponds to this alpha
		                pix[p + 1] = _temperaturemap[a + 1]; //set the green value based on alpha
		                pix[p + 2] = _temperaturemap[a + 2]; //set the blue value based on alpha
		            }
		            p += 4; // Move on to the next pixel
		        }
		        ctx.putImageData(dat, 0, 0);
		    }
		
		    // Sets any options passed in
		    function _setOptions(options) {
		        for (attrname in options) {
		            _options[attrname] = options[attrname];
		        }
		    }
		
		    // Sets the heatmap points from an array of Microsoft.Maps.Locations  
		    function _setPoints(locations) {
		        _locations = locations;
		    }
		
		    // Main method to draw the heatmap
		    function _createHeatMap() {
		        // Ensure the canvas matches the current dimensions of the map
		        // This also has the effect of resetting the canvas
		        _canvas.height = _map.getHeight();
		        _canvas.width = _map.getWidth();
		
		        _canvas.style.top = -_canvas.height / 2 + 'px';
		        _canvas.style.left = -_canvas.width / 2 + 'px';
		
		        // Calculate the pixel radius of each heatpoint at the current map zoom
		        if (_options.unit == "pixels") {
		            radiusInPixel = _options.radius;
		        } else {
		            radiusInPixel = _options.radius / _map.getMetersPerPixel();
		        }
		
		        var ctx = _canvas.getContext("2d");
		
		        // Convert lat/long to pixel location
		        var pixlocs = _map.tryLocationToPixel(_locations, Microsoft.Maps.PixelReference.control);
		        var shadow = 'rgba(0, 0, 0, ' + _options.intensity + ')';
		        var mapWidth = 256 * Math.pow(2, _map.getZoom());
		
		        // Create the Intensity Map by looping through each location
		        for (var i = 0, len = pixlocs.length; i < len; i++) {
		            var x = pixlocs[i].x;
		            var y = pixlocs[i].y;
		
		            if (x < 0) {
		                x += mapWidth * Math.ceil(Math.abs(x / mapWidth));
		            }
		
		            // Create radial gradient centred on this point
		            var grd = ctx.createRadialGradient(x, y, 0, x, y, radiusInPixel);
		            grd.addColorStop(0.0, shadow);
		            grd.addColorStop(1.0, 'transparent');
		
		            // Draw the heatpoint onto the canvas
		            ctx.fillStyle = grd;
		            ctx.fillRect(x - radiusInPixel, y - radiusInPixel, 2 * radiusInPixel, 2 * radiusInPixel);
		        }
		
		        // Apply the specified colour gradient to the intensity map
		        _colouriseHeatMap();
		
		        // Call the callback function, if specified
		        if (_options.callback) {
		            _options.callback();
		        }
		    }
		
		    /* Public Methods */
		
		    this.Show = function () {
		        if (_canvas) {
		            _canvas.style.display = '';
		        }
		    };
		
		    this.Hide = function () {
		        if (_canvas) {
		            _canvas.style.display = 'none';
		        }
		    };
		
		    // Sets options for intensity, radius, colourgradient etc.
		    this.SetOptions = function (options) {
		        _setOptions(options);
		    }
		
		    // Sets an array of Microsoft.Maps.Locations from which the heatmap is created
		    this.SetPoints = function (locations) {
		        // Reset the existing heatmap layer
		        _clearHeatMap();
		        // Pass in the new set of locations
		        _setPoints(locations);
		        // Recreate the layer
		        _createHeatMap();
		    }
		
		    // Removes the heatmap layer from the DOM
		    this.Remove = function () {
		        _canvas.parentNode.parentNode.removeChild(_canvas.parentNode);
		
		        if (_viewchangestarthandler) { Microsoft.Maps.Events.removeHandler(_viewchangestarthandler); }
		        if (_viewchangeendhandler) { Microsoft.Maps.Events.removeHandler(_viewchangeendhandler); }
		
		        _locations = null;
		        _temperaturemap = null;
		        _canvas = null;
		        _options = null;
		        _viewchangestarthandler = null;
		        _viewchangeendhandler = null;
		    }
		
		    // Call the initialisation routine
		    _init();
		};
		
		// Call the Module Loaded method
		Microsoft.Maps.moduleLoaded('HeatMapModule');


**加入 twitterStream.js**

1. 在 [方案總管] 中展開 **TweetSentimentWeb**。
2. 以滑鼠右鍵按一下 [指令碼]，然後依序按一下 [新增] 和 [JavaScript 檔案]。
3. 在 [項目名稱]欄位輸入 **twitterStream.js**。
4. 複製以下程式碼並貼到檔案中：

		var liveTweetsPos = [];
		var liveTweets = [];
		var liveTweetsNeg = [];
		var map;
		var heatmap;
		var heatmapNeg;
		var heatmapPos;
		
		function initialize() {
		    // Initialize the map
		    var options = {
		        credentials: "AvFJTZPZv8l3gF8VC3Y7BPBd0r7LKo8dqKG02EAlqg9WAi0M7la6zSIT-HwkMQbx",
		        center: new Microsoft.Maps.Location(23.0, 8.0),
		        mapTypeId: Microsoft.Maps.MapTypeId.ordnanceSurvey,
		        labelOverlay: Microsoft.Maps.LabelOverlay.hidden,
		        zoom: 2.5
		    };
		    var map = new Microsoft.Maps.Map(document.getElementById('map_canvas'), options);
		
		    // Heatmap options for positive, neutral and negative layers
		
		    var heatmapOptions = {
		        // Opacity at the centre of each heat point
		        intensity: 0.5,
		
		        // Affected radius of each heat point
		        radius: 15,
		
		        // Whether the radius is an absolute pixel value or meters
		        unit: 'pixels'
		    };
		
		    var heatmapPosOptions = {
		        // Opacity at the centre of each heat point
		        intensity: 0.5,
		
		        // Affected radius of each heat point
		        radius: 15,
		
		        // Whether the radius is an absolute pixel value or meters
		        unit: 'pixels',
		
		        colourgradient: {
		            0.0: 'rgba(0, 255, 255, 0)',
		            0.1: 'rgba(0, 255, 255, 1)',
		            0.2: 'rgba(0, 255, 191, 1)',
		            0.3: 'rgba(0, 255, 127, 1)',
		            0.4: 'rgba(0, 255, 63, 1)',
		            0.5: 'rgba(0, 127, 0, 1)',
		            0.7: 'rgba(0, 159, 0, 1)',
		            0.8: 'rgba(0, 191, 0, 1)',
		            0.9: 'rgba(0, 223, 0, 1)',
		            1.0: 'rgba(0, 255, 0, 1)'
		        }
		    };
		
		    var heatmapNegOptions = {
		        // Opacity at the centre of each heat point
		        intensity: 0.5,
		
		        // Affected radius of each heat point
		        radius: 15,
		
		        // Whether the radius is an absolute pixel value or meters
		        unit: 'pixels',
		
		        colourgradient: {
		            0.0: 'rgba(0, 255, 255, 0)',
		            0.1: 'rgba(0, 255, 255, 1)',
		            0.2: 'rgba(0, 191, 255, 1)',
		            0.3: 'rgba(0, 127, 255, 1)',
		            0.4: 'rgba(0, 63, 255, 1)',
		            0.5: 'rgba(0, 0, 127, 1)',
		            0.7: 'rgba(0, 0, 159, 1)',
		            0.8: 'rgba(0, 0, 191, 1)',
		            0.9: 'rgba(0, 0, 223, 1)',
		            1.0: 'rgba(0, 0, 255, 1)'
		        }
		    };
		
		    // Register and load the Client Side HeatMap Module
		    Microsoft.Maps.registerModule("HeatMapModule", "scripts/heatmap.js");
		    Microsoft.Maps.loadModule("HeatMapModule", {
		        callback: function () {
		            // Create heatmap layers for positive, neutral and negative tweets
		            heatmapPos = new HeatMapLayer(map, liveTweetsPos, heatmapPosOptions);
		            heatmap = new HeatMapLayer(map, liveTweets, heatmapOptions);
		            heatmapNeg = new HeatMapLayer(map, liveTweetsNeg, heatmapNegOptions);
		        }
		    });
		
		    $("#searchbox").val("xbox");
		    $("#searchBtn").click(onsearch);
		    $("#positiveBtn").click(onPositiveBtn);
		    $("#negativeBtn").click(onNegativeBtn);
		    $("#neutralBtn").click(onNeutralBtn);
		    $("#neutralBtn").button("toggle");
		}
		
		function onsearch() {
		    var uri = 'api/tweets?query=';
		    var query = $('#searchbox').val();
		    $.getJSON(uri + query)
		        .done(function (data) {
		            liveTweetsPos = [];
		            liveTweets = [];
		            liveTweetsNeg = [];
		
		            // On success, 'data' contains a list of tweets.
		            $.each(data, function (key, item) {
		                addTweet(item);
		            });
		
		            if (!$("#neutralBtn").hasClass('active')) {
		                $("#neutralBtn").button("toggle");
		            }
		            onNeutralBtn();
		        })
		        .fail(function (jqXHR, textStatus, err) {
		            $('#statustext').text('Error: ' + err);
		        });
		}
		
		function addTweet(item) {
		    //Add tweet to the heat map arrays.
		    var tweetLocation = new Microsoft.Maps.Location(item.Latitude, item.Longtitude);
		    if (item.Sentiment > 0) {
		        liveTweetsPos.push(tweetLocation);
		    } else if (item.Sentiment < 0) {
		        liveTweetsNeg.push(tweetLocation);
		    } else {
		        liveTweets.push(tweetLocation);
		    }
		}
		
		function onPositiveBtn() {
		    if ($("#neutralBtn").hasClass('active')) {
		        $("#neutralBtn").button("toggle");
		    }
		    if ($("#negativeBtn").hasClass('active')) {
		        $("#negativeBtn").button("toggle");
		    }
		
		    heatmapPos.SetPoints(liveTweetsPos);
		    heatmapPos.Show();
		    heatmapNeg.Hide();
		    heatmap.Hide();
		
		    $('#statustext').text('Tweets: ' + liveTweetsPos.length + "   " + getPosNegRatio());
		}
		
		function onNeutralBtn() {
		    if ($("#positiveBtn").hasClass('active')) {
		        $("#positiveBtn").button("toggle");
		    }
		    if ($("#negativeBtn").hasClass('active')) {
		        $("#negativeBtn").button("toggle");
		    }
		
		    heatmap.SetPoints(liveTweets);
		    heatmap.Show();
		    heatmapNeg.Hide();
		    heatmapPos.Hide();
		
		    $('#statustext').text('Tweets: ' + liveTweets.length + "   " + getPosNegRatio());
		}
		
		function onNegativeBtn() {
		    if ($("#positiveBtn").hasClass('active')) {
		        $("#positiveBtn").button("toggle");
		    }
		    if ($("#neutralBtn").hasClass('active')) {
		        $("#neutralBtn").button("toggle");
		    }
		
		    heatmapNeg.SetPoints(liveTweetsNeg);
		    heatmapNeg.Show();
		    heatmap.Hide();;
		    heatmapPos.Hide();;
		
		    $('#statustext').text('Tweets: ' + liveTweetsNeg.length + "\t" + getPosNegRatio());
		}
		
		function getPosNegRatio() {
		    if (liveTweetsNeg.length == 0) {
		        return "";
		    }
		    else {
		        var ratio = liveTweetsPos.length / liveTweetsNeg.length;
		        var str = parseFloat(Math.round(ratio * 10) / 10).toFixed(1);
		        return "Positive/Negative Ratio: " + str;
		    }
		}


**修改 layout.cshtml**

1. 在 **方案總管** 中，依序展開 [TweetSentimentWeb]、[Views] 及 [Shared]，然後按兩下 _[Layout.cshtml]。2. 以下列內容取代原始內容：

		<!DOCTYPE html>
		<html>
		<head>
		    <meta charset="utf-8" />
		    <meta name="viewport" content="width=device-width, initial-scale=1.0">
		    <title>@ViewBag.Title</title>
		    @Styles.Render("~/Content/css")
		    @Scripts.Render("~/bundles/modernizr")
		    <!-- Bing Maps -->
		    <script type="text/javascript" src="http://ecn.dev.virtualearth.net/mapcontrol/mapcontrol.ashx?v=7.0&mkt=en-gb"></script>
		    <!-- Spatial Dashboard JavaScript -->
		    <script src="~/Scripts/twitterStream.js" type="text/javascript"></script>
		</head>
		<body onload="initialize()">
		    <div class="navbar navbar-inverse navbar-fixed-top">
		        <div class="container">
		            <div class="navbar-header">
		                <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
		                    <span class="icon-bar"></span>
		                    <span class="icon-bar"></span>
		                    <span class="icon-bar"></span>
		                </button>
		            </div>
		            <div class="navbar-collapse collapse">
		                <div class="row">
		                    <ul class="nav navbar-nav col-lg-5">
		                        <li class="col-lg-12">
		                            <div class="navbar-form">
		                                <input id="searchbox" type="search" class="form-control">
		                                <button type="button" id="searchBtn" class="btn btn-primary">Go</button>
		                            </div>
		                        </li>
		                    </ul>
		                    <ul class="nav navbar-nav col-lg-7">
		                        <li>
		                            <div class="navbar-form">
		                                <div class="btn-group" data-toggle="buttons-radio">
		                                    <button type="button" id="positiveBtn" class="btn btn-primary">Positive</button>
		                                    <button type="button" id="neutralBtn" class="btn btn-primary">Neutral</button>
		                                    <button type="button" id="negativeBtn" class="btn btn-primary">Negative</button>
		                                </div>
		                            </div>
		                        </li>
		                        <li><span id="statustext" class="navbar-text"></span></li>
		                    </ul>
		                </div>
		            </div>
		        </div>
		    </div>
		    <div class="map_container">
		        @RenderBody()
		    </div>
		    @Scripts.Render("~/bundles/jquery")
		    @Scripts.Render("~/bundles/bootstrap")
		    @RenderSection("scripts", required: false)
		</body>
		</html>



**修改 Index.cshtml**

1. 在 [方案總管] 中，依序展開 **TweetSentimentWeb**、**Views** 及 **Home**，然後按兩下 **Index.cshtml**。
2. 以下列內容取代原始內容：

		@{
		    ViewBag.Title = "Tweet Sentiment";
		}
		
		<div class="map_container">
		    <div id="map_canvas"/>
		</div>

**修改 site.css 檔案**

1. 在 [方案總管] 中，依序展開 **TweetSentimentWeb** 和 **Content**，然後按兩下 **Site.css**。
2. 將下列程式碼附加至檔案。
		
		/* make container, and thus map, 100% width */
		.map_container {
			width: 100%;
			height: 100%;
		}
		
		#map_canvas{
		  height:100%;
		}
		
		#tweets{
		  position: absolute;
		  top: 60px;
		  left: 75px;
		  z-index:1000;
		  font-size: 30px;
		}

**修改 global.asax 檔案**

1. 在 [方案總管] 中展開 **TweetSentimentWeb**，然後按兩下 **Global.asax**。
2. 加入下列 **using** 陳述式：

		using System.Web.Http;

2. 將以下文字行新增至 **Application_Start()** 函數內：

		// Register API routes
		GlobalConfiguration.Configure(WebApiConfig.Register);
  
	修改 API 路由的註冊，使 Web API 控制器能在 MVC 應用程式內運作。

**執行 Web 應用程式**

1. 驗證串流服務主控台應用程式仍在執行中，以便您可以看到即時變更。
2. 按 **F5** 以執行 Web 應用程式：

	![hdinsight.hbase.twitter.sentiment.bing.map][img-bing-map]
2. 在文字方塊中輸入關鍵字，然後按一下 [搜尋]。您不一定能找到所有關鍵字，須視 HBase 資料表內收集到的資料而定。請嘗試一些常用的關鍵字，例如 "love"、"xbox" 和 "playstation"。 
3. 切換 **Positive**、**Neutral** 及 **Negative** 以比較投注在主題上的情緒。
4. 讓串流服務再多執行一小時，然後搜尋相同的關鍵字並比較結果。

 
您也可以選擇將應用程式部署到 Azure 網站。如需指示，請參閱[開始使用 Azure 網站和 ASP.NET][website-get-started]。
 
##<a id="nextsteps"></a>後續步驟

在本教學課程中，您了解如何取得推文、分析推文的情緒、將情緒資料儲存到 HBase，以及在 Bing 地圖上呈現即時的 Twitter 情緒資料。若要深入了解，請參閱：

- [開始使用 HDInsight][hdinsight-get-started]
- [在 HDInsight 中設定 HBase 複寫](hdinsight-hbase-geo-replication.md) 
- [在 HDInsight 中使用 Hadoop 分析 Twitter 資料][hdinsight-analyze-twitter-data]
- [使用 HDInsight 分析航班延誤資料][hdinsight-analyze-flight-delay-data]
- [開發 HDInsight 的 C# Hadoop 串流程式][hdinsight-develop-streaming]
- [開發 HDInsight 的 Java MapReduce 程式][hdinsight-develop-mapreduce]


[hbase-get-started]: ../hdinsight-hbase-get-started.md
[website-get-started]: ../web-sites-dotnet-get-started.md



[img-app-arch]: ./media/hdinsight-hbase-analyze-twitter-sentiment/AppArchitecture.png
[img-twitter-app]: ./media/hdinsight-hbase-analyze-twitter-sentiment/TwitterApp.png
[img-streaming-service]: ./media/hdinsight-hbase-analyze-twitter-sentiment/StreamingService.png
[img-bing-map]: ./media/hdinsight-hbase-analyze-twitter-sentiment/TwitterSentimentBingMap.png



[hdinsight-develop-streaming]: hdinsight-hadoop-develop-deploy-streaming-jobs.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce.md
[hdinsight-analyze-twitter-data]: hdinsight-analyze-twitter-data.md
[hdinsight-hbase-get-started]: ../hdinsight-hbase-get-started.md




[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: ../install-configure-powershell.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-storage-powershell]: ../hdinsight-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-ODBC-driver.md

 

<!---HONumber=July15_HO2-->