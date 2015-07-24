<properties 
	pageTitle="語彙型情感分析 | Microsoft Azure" 
	description="語彙型情感分析" 
	services="machine-learning" 
	documentationCenter="" 
	authors="jaymathe" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/24/2015" 
	ms.author="jaymathe"/>



#語彙型情感分析 

如何測量使用者在線上社交網路中 (例如 Facebook 文章、推文、評論等)，針對品牌或主題的意見和態度？ 情感分析提供分析這類問題的方法。


[AZURE.INCLUDE [電腦-學習-免費-試用](../../includes/machine-learning-free-trial.md)]

情感分析通常有兩個方法。其中一個方法使用監督式學習演算法，而另一個方法可視為非監督式學習。監督式學習演算法通常會在大型註解主體上建置分類模型。其精確度主要會視註解的品質而定，且定型程序通常需要很長的時間。除此之外，當我們將演算法套用到另一個網域時，效果通常不是很好。相較於監督式學習，語彙型非監督式學習使用情感字典，由於不需要儲存大型資料主體和定型，因此可加快整個程序的速度。

我們的[服務](https://datamarket.azure.com/dataset/aml_labs/lexicon_based_sentiment_analysis)以 MPQA 詞典 (http://mpqa.cs.pitt.edu/lexicons/subj_lexicon/)) 為建置基礎，這是其中一個最常用的主觀性詞典。MPQA 中有 5,097 個否定字和 2,533 個肯定字。而且所有這些字都會加上強極性或弱極性的註解。整個主體會位於 [GNU 通用公共授權] 下方。Web 服務可以套用至任何簡短的句子，例如推文和 Facebook 文章。

>使用者可透過行動裝置應用程式、網站，甚至是本機電腦，來取用這項 Web 服務。不過，該 Web 服務也可用來示範如何使用 Azure Machine Learning，來建立採用 R 程式碼的 Web 服務。只需幾行 R 程式碼並在 Azure Machine Learning Studio 中的按鈕上按幾下，就可以建立採用 R 程式碼的實驗，並將其發佈為 Web 服務。接著可將 Web 服務發佈至 Azure Marketplace，以供世界各地的使用者和裝置取用，而不需要 Web 服務的作者設定基礎結構。

##使用 Web 服務

輸入資料可以是任何文字，但 Web 服務最適合用在簡短的句子。輸出可以是 -1 和 1 之間的數值。任何小於 0 的值表示文字的情感是負面的；如果大於 0，則表示情感是正面的。結果的絕對值表示相關聯情感的強度。

>在 Azure Marketplace 上託管的這項服務是一個 OData 服務，可透過 POST 或 GET 方法進行呼叫。

以自動化方式取用服務的方法有很多種 ([這裡](http://microsoftazuremachinelearning.azurewebsites.net/)提供一個範例應用程式)。

###啟動 Web 服務使用的 C# 程式碼：

	public class ScoreResult
	{
	        [DataMember]
	        public double result
	        {
	            get;
	            set;
	        }
	}

	void main()
	{
	        using (var wb = new WebClient())
	        {
	            var acitionUri = new Uri("PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score");
	            DataServiceContext ctx = new DataServiceContext(acitionUri);
	            var cred = new NetworkCredential("PutEmailAddressHere", "ChangeToAPIKey");
	            var cache = new CredentialCache();
	
	            cache.Add(acitionUri, "Basic", cred);
	            ctx.Credentials = cache;
	            var query = ctx.Execute<ScoreResult>(acitionUri, "POST", true, new BodyOperationParameter("Text", TextBox1.Text));
	            ScoreResult scoreResult = query.ElementAt(0);
	            double result = scoreResult.result;
	    	}
	}



輸入是「今天是美好的一天」。 輸出是 "1"，表示與輸入句子相關聯的正面情感。

##建立 Web 服務
>這項 Web 服務是使用 Azure Machine Learning 所建立。如需免費試用版，以及有關建立實驗和[發佈 Web 服務](machine-learning-publish-a-machine-learning-web-service.md)的簡介影片，請參閱 [azure.com/ml](http://azure.com/ml)。以下是建立 Web 服務之實驗的螢幕擷取畫面，以及實驗內每個模組的範例程式碼。


Azure Machine Learning 中已建立新的空白實驗。下圖說明語彙型情感分析的實驗流程。“sent_dict.csv” 檔案是 MPQA 主觀性詞典，並已設定為[執行 R 指令碼][execute-r-script]的其中一個輸入。另一個輸入是來自 Amazon 評論測試資料集的取樣評論，我們在這個資料集中執行範圍選取、資料行名稱修改和分割作業。我們使用雜湊封裝將主觀性詞典儲存在記憶體中，以加快計分程序的速度。“tm” 封裝會將整個文字語彙基元化，並與情感字典中的單字進行比較。最後，分數的計算方式是在文字中加上每個主觀字的加權。

###實驗流程：

![實驗流程][2]


####模組 1：
	
	# Map 1-based optional input ports to variables
    sent_dict_data<- maml.mapInputPort(1) # class: data.frame
    dataset2 <- maml.mapInputPort(2) # class: data.frame
 
   # 安裝雜湊封裝 install.packages("src/hash_2.2.6.zip", lib = ".", repos = NULL, verbose = TRUE) success <- library("hash", lib.loc = ".", logical.return = TRUE, verbose = TRUE) library(tm) library(stringr)

    #create sentiment dictionary
    negation_word <- c("not","nor", "no")
    result <- c()
    sent_dict <- hash()
    sent_dict <- hash(sent_dict_data$word, sent_dict_data$polarity)

    #  Compute sentiment score for each document
    for (m in 1:nrow(dataset2)){
	polarity_ratio <- 0
	polarity_total <- 0
	not <- 0
	sentence <- tolower(dataset2[m,1])
	if (nchar(sentence) > 0){
		token_array <- scan_tokenizer(sentence)
		for (j in 1:length(token_array)){
			word = str_replace_all(token_array[j], "[^[:alnum:]]", "")
		    for (k in 1:length(negation_word)){
		      if (word == negation_word[k]){
		        not <- (not+1) %% 2

			  }
		    }
			if (word != ""){
			    if (!is.null(sent_dict[[word]])){
			      polarity_ratio <- polarity_ratio + (-2*not+1)*sent_dict[[word]]
			      polarity_total <- polarity_total + abs(sent_dict[[word]])
			    }
			}
		  
		}
	}
	if (polarity_total > 0){
		result <- c(result, polarity_ratio/polarity_total)
	}else{
		result<- c(result,0)
	}
    }

    # Sample operation
    data.set <- data.frame(result)

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("data.set")
	


##限制

從演算法的觀點來看，語彙型情感分析是一般情感分析工具，在某些領域中，這項工具的執行效果可能沒有分類方法來得出色。否定問題不是很好處理。我們在程式中硬式編碼了幾個否定字，但使用否定字典並建置一些規則會是更好的方法。相較於又長又複雜的句子 (例如 Amazon 評論)，在簡短的句子 (例如推文和 Facebook 文章) 上執行 Web 服務會有較佳的效果。

##常見問題集
如需取用 Web 服務或發佈至 Azure Marketplace 的常見問題集，請參閱[這裡](machine-learning-marketplace-faq.md)。

[1]: ./media/machine-learning-r-csharp-lexicon-based-sentiment-analysis/sentiment_analysis_1.png
[2]: ./media/machine-learning-r-csharp-lexicon-based-sentiment-analysis/sentiment_analysis_2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/

 

<!---HONumber=July15_HO2-->