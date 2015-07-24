<properties 
	pageTitle="二項分配套件 | Microsoft Azure" 
	description="二項分配套件" 
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


#二項分配套件




二項分配套件是一組範例 Web 服務 ([二項產生器](https://datamarket.azure.com/dataset/aml_labs/bdg5)、[機率計算機](https://datamarket.azure.com/dataset/aml_labs/bdp4)、[分位數計算機](https://datamarket.azure.com/dataset/aml_labs/bdq5))，可協助產生和處理二項分配。這些服務允許產生任何長度的二項分配序列、算出指定機率的分位數，以及計算指定分位數的機率。每個服務會根據所選取的服務發出不同的輸出 (請參閱下列說明)。二項分配套件會以 R 統計封裝中所包含的 R 函數 qbinom、rbinom 和 pbinom 為基礎。


[AZURE.INCLUDE [電腦-學習-免費-試用](../../includes/machine-learning-free-trial.md)]

>使用者可透過行動裝置應用程式、網站，甚至是本機電腦，直接在 Marketplace 上取用這些 Web 服務。不過，該 Web 服務也可用來示範如何使用 Azure Machine Learning，來建立採用 R 程式碼的 Web 服務。只需幾行 R 程式碼並在 Azure Machine Learning Studio 中的按鈕上按幾下，就可以建立採用 R 程式碼的實驗，並將其發佈為 Web 服務。接著可將 Web 服務發佈至 Azure Marketplace，以供世界各地的使用者和裝置取用，而不需要 Web 服務的作者設定基礎結構。

##使用 Web 服務
二項分配套件包括下列 3 個服務。

###二項分配分位數計算機
這項服務可接受 4 個常態分配的引數，並計算相關聯的分位數。輸入引數包括：

- p - 多個試験的單一彙總機率。  
- size - 試驗的次數。
- prob - 試驗中的成功機率。
- Side - L 代表分配的下限，U 代表分配的上限。 

服務的輸出是計算與指定機率相關聯的分位數。

###二項分配機率計算機
這項服務可接受 4 個二項分配的引數，並計算相關聯的機率。輸入引數包括：

- q - 二項分配事件的單一分位數。 
- size - 試驗的次數。
- prob - 試驗中的成功機率。
- side - L 代表分配的下限，U 代表分配的上限，或 E 等於成功的單一數目。

服務的輸出是計算與指定分位數相關聯的機率。

###二項分配產生器
這項服務可接受 3 個二項分配的引數，並產生已二項分配的隨機序號。要求中應提供下列引數：

- n - 觀察的次數。 
- size - 試驗的次數。
- prob - 成功的機率。

服務的輸出是一系列長度 n 和根據 size 和 prob 引數的二項分配。

>在 Azure Marketplace 上託管的這項服務是一個 OData 服務，可透過 POST 或 GET 方法進行呼叫。

以自動化方式取用服務的方法有很多種 (範例應用程式如下：[產生器](http://microsoftazuremachinelearning.azurewebsites.net/BinomialDistributionGenerator.aspx)、[機率計算機](http://microsoftazuremachinelearning.azurewebsites.net/BinomialDistributionProbabilityCalculator.aspx)、[分位數計算機](http://microsoftazuremachinelearning.azurewebsites.net/BinomialDistributionQuantileCalculator))。

###啟動 Web 服務使用的 C# 程式碼：

###二項分配分位數計算機
	public class Input
	{
	        public string p;
	        public string size;
	        public string prob;
	        public string side;
	}
	
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
	{
	        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
	        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}
	
    void main()
	{
	        var input = new Input() { p = TextBox1.Text, size = TextBox2.Text, prob = TextBox3.Text, side = TextBox4.Text };
	        var json = JsonConvert.SerializeObject(input);
	        var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
	        var httpClient = new HttpClient();
	
	        httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
	        httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
	
	        var response = httpClient.PostAsync(acitionUri, new StringContent(json));
	        var result = response.Result.Content;
		    var scoreResult = result.ReadAsStringAsync().Result;
	}

###二項分配機率計算機
	public class Input
	{
	        public string q;
	        public string size;
	        public string prob;
	        public string side;
	}
	
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
	{
	        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
	        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}
	
	void Main()
	{
	        var input = new Input() { q = TextBox1.Text, size = TextBox2.Text, prob = TextBox3.Text, side = TextBox4.Text };
	        var json = JsonConvert.SerializeObject(input);
	        var acitionUri = " PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
	        var httpClient = new HttpClient();
	
	        httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
	        httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
	
	        var response = httpClient.PostAsync(acitionUri, new StringContent(json));
	        var result = response.Result.Content;
		    var scoreResult = result.ReadAsStringAsync().Result;
	}


###二項分配產生器
	public class Input
	{
	        public string n;
	        public string size;
	        public string p;
	}
	
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
	{
	        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
	        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}
	
	void Main()
	{
	        var input = new Input() { n = TextBox1.Text, size = TextBox2.Text, p = TextBox3.Text };
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

###二項分配分位數計算機

![建立工作區][4]

####模組 1：
    #data schema with example data (replaced with data from web service)
    data.set=data.frame(p=0.1,size=10,prob=.5,side='L');
    maml.mapOutputPort("data.set"); #send data to output port
####模組 2：

    dataset1 <- maml.mapInputPort(1) # class: data.frame
    param = dataset1
    if (param$p < 0 ) {
	print('Bad input: p must be between 0 and 1')
	param$p = 0
    } else if (param$p > 1) {
	print('Bad input: p must be between 0 and 1')
	param$p = 1
    }

    if (param$prob < 0 ) {
	print('Bad input: prob must be between 0 and 1')
	param$prob = 0
    } else if (param$prob > 1) {
	print('Bad input: prob must be between 0 and 1')
	param$prob = 1
    }

    quantile = qbinom(param$p,size=param$size,prob=param$prob)
    df = data.frame(x=1:param$size, prob=dbinom(1:param$size, param$size, prob=param$prob))
    quantile

    if (param$side == 'U'){
	quantile = qbinom(param$p,size=param$size,prob=param$prob,lower.tail = F)
	band=subset(df,x>quantile)
    } else if (param$side =='L') {
	quantile = qbinom(param$p,size=param$size,prob=param$prob,lower.tail = T)
	band=subset(df,x<=quantile)
    } else {
	print("Invalid side choice")
    }

    output = as.data.frame(quantile)
    
	# Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");


###二項分配機率計算機

![建立工作區][5]

####模組 1：

    #data schema with example data (replaced with data from web service)
    data.set=data.frame(q=5,size=10,prob=.5,side='L');
    maml.mapOutputPort("data.set"); #send data to output port


####模組 2：
    dataset1 <- maml.mapInputPort(1) # class: data.frame
    param = dataset1
    prob = pbinom(param$q,size=param$size,prob=param$prob)
    prob.eq = dbinom(param$q,size=param$size,prob=param$prob)
    df = data.frame(x=1:param$size, prob=dbinom(1:param$size, param$size, prob=param$prob))
    prob

    if (param$side == 'U'){
	prob = 1 - prob
	band=subset(df,x>param$q)
    } else if (param$side =='E') {
	prob = prob.eq
	band=subset(df,x==param$q)
    } else if (param$side =='L') {
	prob = prob
	band=subset(df,x<=param$q)
    } else {
	print("Invalid side choice")
    }

    output = as.data.frame(prob)
    
	# Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");

###二項分配產生器

![建立工作區][6]

####模組 1：

    #data schema with example data (replaced with data from web service)
    data.set=data.frame(n=50,size=10,p=.5);
    maml.mapOutputPort("data.set"); #send data to output port

####模組 2：
    dataset1 <- maml.mapInputPort(1) # class: data.frame
    param = dataset1
    dist = rbinom(param$n,param$size,param$p)

    output = as.data.frame(t(dist))
    
	# Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");

##限制 
這些是二項分配的相關簡單範例。從上面的範例程式碼可以看出，已實作小小的錯誤攔截。

##常見問題集
如需取用 Web 服務或發佈至 Azure Marketplace 的常見問題集，請參閱[這裡](machine-learning-marketplace-faq.md)。


[1]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_1.png

[2]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_2.png

[3]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_3.png

[4]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_4.png

[5]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_5.png

[6]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_6.png
 

<!---HONumber=July15_HO2-->