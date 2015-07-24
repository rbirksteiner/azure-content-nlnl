<properties 
	pageTitle="叢集模型 | Microsoft Azure" 
	description="叢集模型" 
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


#叢集模型    

我們可以如何預測多組的信用卡持卡人行為，以降低信用卡發卡機構的壞帳風險？ 我們可以如何定義多組員工的人格特質，以提高員工工作效能？ 醫生可以如何根據疾病特性來將病人分類？ 基本上，叢集分析可以回答所有這些問題。


[AZURE.INCLUDE [電腦-學習-免費-試用](../../includes/machine-learning-free-trial.md)]
   
叢集分析會根據變數的組合，將一組觀察分成兩個或多個互斥的未知群組。叢集分析的目的是要找出將觀察 (通常是人員或其特性) 分組的系統，其中群組成員會共用共同的屬性。這項[服務](https://datamarket.azure.com/dataset/aml_labs/k_cluster_model)使用 K-Means 方法 (常用的叢集技術) 來群集任意資料。這項 Web 服務會接受資料和 k 個叢集做為輸入，並產生有關每個觀察屬於哪個 k 群組的預測。

>使用者可透過行動裝置應用程式、網站，甚至是本機電腦，來取用這項 Web 服務。不過，該 Web 服務也可用來示範如何使用 Azure Machine Learning，來建立採用 R 程式碼的 Web 服務。只需幾行 R 程式碼並在 Azure Machine Learning Studio 中的按鈕上按幾下，就可以建立採用 R 程式碼的實驗，並將其發佈為 Web 服務。接著可將 Web 服務發佈至 Azure Marketplace，以供世界各地的使用者和裝置取用，而不需要 Web 服務的作者設定基礎結構。

##使用 Web 服務   
此 Web 服務會將資料分成一組 k 群組，並輸出每個資料列的群組指派。Web 服務需要使用者以字串形式輸入資料，其中資料列會以逗號 (,) 分隔，而資料行會以分號 (;) 分隔。Web 服務一次需要 1 個資料列。範例資料集看起來可能像這樣：

![範例資料][1]

假設使用者想要將資料分成三個互斥的群組。上述資料集的輸入資料應如下所示：值 = “10;5;2,18;1;6,7;5;5,22;3;4,12;2;1,10;3;4”；k=”3”。輸出會是每個資料列的預測群組成員資格。

>在 Azure Marketplace 上託管的這項服務是一個 OData 服務，可透過 POST 或 GET 方法進行呼叫。

以自動化方式取用服務的方法有很多種 ([這裡](http://microsoftazuremachinelearning.azurewebsites.net/ClusterModel.aspx)提供一個範例應用程式)。

###啟動 Web 服務使用的 C# 程式碼：

	public class Input
	{
	        public string value;
	        public string k;
	}
	
	public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
	{
	        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
	        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}
	
	void Main()
	{
	        var input = new Input() { value = TextBox1.Text, k = TextBox2.Text };
	        var json = JsonConvert.SerializeObject(input);
	        var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
	        var httpClient = new HttpClient();
	
	        httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
	        httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
	
	        var response = httpClient.PostAsync(acitionUri, new StringContent(json));
	        var result = response.Result.Content;
	    	var scoreResult = result.ReadAsStringAsync().Result;
	}




##建立 Web 服務  
>這項 Web 服務是使用 Azure Machine Learning 所建立。如需免費試用版，以及有關建立實驗和[發佈 Web 服務](machine-learning-publish-a-machine-learning-web-service.md)的簡介影片，請參閱 [azure.com/ml](http://azure.com/ml)。以下是建立 Web 服務之實驗的螢幕擷取畫面，以及實驗內每個模組的範例程式碼。

Azure Machine Learning 中已建立新的空白實驗，並將兩個[執行 R 指令碼][execute-r-script]模組提取到工作區。資料結構描述是採用簡單的[執行 R 指令碼][execute-r-script]所建立。接著，資料結構描述會連結到同樣也是採用[執行 R 指令碼][execute-r-script]建立的叢集模型區段。在用於叢集模型的[執行 R 指令碼][execute-r-script]中，Web 服務會接著使用 "k-means" 函數 (已預先建置到 Azure Machine Learning 的[執行 R 指令碼][execute-r-script]中)。
   

     
![實驗流程][3]

####模組 1： 
	#Enter the input data as a string 
	mydata <- data.frame(value = "1; 3; 5; 6; 7; 7, 5; 5; 6; 7; 2; 1, 3; 7; 2; 9; 56; 6, 1; 4; 5; 26; 4; 23, 15; 35; 6; 7; 12; 1, 32; 51; 62; 7; 21; 1", k=5, stringsAsFactors=FALSE)
	
	maml.mapOutputPort("mydata");     
	

####模組 2：
	# Map 1-based optional input ports to variables
	mydata <- maml.mapInputPort(1) # class: data.frame

	data.split <- strsplit(mydata[1,1], ",")[[1]]
	data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)
	data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)
	data.split <- as.data.frame(t(data.split))

	data.split <- data.matrix(data.split)
	data.split <- data.frame(data.split)

	# K-Means cluster analysis
	fit <- kmeans(data.split, mydata$k) # k-cluster solution

	# Get cluster means 
	aggregate(data.split,by=list(fit$cluster),FUN=mean)
	# Append cluster assignment
	mydatafinal <- data.frame(t(fit$cluster))
	n_col=ncol(mydatafinal)
	colnames(mydatafinal) <- paste("V",1:n_col,sep="")

	# Select data.frame to be sent to the output Dataset port
	maml.mapOutputPort("mydatafinal");
   
 
##限制
這是一個非常簡單的群集 Web 服務範例。從上面的範例程式碼可以看出，未實作錯誤攔截，且由於建立這項 Web 服務時，服務只輸入數值，因此服務會假設所有項目都是連續變數 (不允許類別功能)。此外，由於 Web 服務呼叫的要求/回應本質，以及每次呼叫 Web 服務時就會調整模型的事實，該項服務目前只能處理有限的資料大小。

##常見問題集
如需取用 Web 服務或發佈至 Azure Marketplace 的常見問題集，請參閱[這裡](machine-learning-marketplace-faq.md)。

[1]: ./media/machine-learning-r-csharp-cluster-model/cluster-img1.png
[2]: ./media/machine-learning-r-csharp-cluster-model/cluster-img2.png
[3]: ./media/machine-learning-r-csharp-cluster-model/cluster-img3.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 

<!---HONumber=July15_HO2-->