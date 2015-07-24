<properties 
	pageTitle="常態分佈 Web 服務套件 | Microsoft Azure" 
	description="常態分佈 Web 服務套件" 
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

#常態分配套件



常態分佈套件是一組範例 Web 服務 ([產生器](https://datamarket.azure.com/dataset/aml_labs/ndg7)、[分位數計算機](https://datamarket.azure.com/dataset/aml_labs/ndq5)、[機率計算機](https://datamarket.azure.com/dataset/aml_labs/ndp5))，可協助產生和處理常態分佈。這些服務允許產生任何長度的常態分佈序列、計算指定機率的分位數，以及計算指定分位數的機率。每個服務會根據所選取的服務發出不同的輸出 (請參閱下列說明)。常態分佈套件會以 R 統計封裝中所包含的 R 函數 qnorm、rnorm 和 pnorm 為基礎。


[AZURE.INCLUDE [電腦-學習-免費-試用](../../includes/machine-learning-free-trial.md)]

>使用者可透過行動裝置應用程式、網站，甚至是本機電腦，來取用這項 Web 服務。不過，該 Web 服務也可用來示範如何使用 Azure Machine Learning，來建立採用 R 程式碼的 Web 服務。只需幾行 R 程式碼並在 Azure Machine Learning Studio 中的按鈕上按幾下，就可以建立採用 R 程式碼的實驗，並將其發佈為 Web 服務。接著可將 Web 服務發佈至 Azure Marketplace，以供世界各地的使用者和裝置取用，而不需要 Web 服務的作者設定基礎結構。
 

##使用 Web 服務
常態分佈套件包括下列 3 個服務。

###常態分佈分位數計算機
這項服務可接受 4 個常態分配的引數，並計算相關聯的分位數。

輸入引數包括：

* p - 常態分佈事件的單一機率。 
* Mean - 常態分佈平均值。
* SD - 常態分佈標準差。 
* Side - L 代表分佈的下限，而 U 代表分佈的上限。

服務的輸出是計算與指定機率相關聯的分位數。

###常態分佈機率計算機
這項服務可接受 4 個常態分佈的引數，並計算相關聯的機率。

輸入引數包括：

* q - 常態分佈事件的單一分位數。 
* Mean - 常態分佈平均值。
* SD - 常態分佈標準差。 
* Side - L 代表分佈的下限，而 U 代表分佈的上限。

服務的輸出是計算與指定分位數相關聯的機率。

###常態分佈產生器
這項服務可接受 3 個常態分佈的引數，並產生已常態分佈的隨機序號。要求中應提供下列引數：

* n - 觀察的次數。 
* mean - 常態分佈平均值。
* sd - 常態分佈標準差。 

服務的輸出是一系列長度 n 和根據 mean 和 sd 引數的常態分佈。

>在 Azure Marketplace 上託管的這項服務是一個 OData 服務，可透過 POST 或 GET 方法進行呼叫。

以自動化方式取用服務的方法有很多種 (範例應用程式如下：[產生器](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionGenerator.aspx)、[機率計算機](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionProbabilityCalculator.aspx)、[分位數計算機](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionQuantileCalculator.aspx))。

###啟動 Web 服務使用的 C# 程式碼：

###常態分佈分位數計算機
	public class Input
	{
	        public string p;
	        public string mean;
	        public string sd;
	        public string side;
	}
	
	public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
	{
	        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
	        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}
	
	void Main()
	{
	        var input = new Input() { p = TextBox1.Text, mean = TextBox2.Text, sd = TextBox3.Text, side = TextBox4.Text };
	        var json = JsonConvert.SerializeObject(input);
	        var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
	        var httpClient = new HttpClient();
	
	        httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
	        httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
	
	        var response = httpClient.PostAsync(acitionUri, new StringContent(json));
	        var result = response.Result.Content;
	    	var scoreResult = result.ReadAsStringAsync().Result;
	}


###常態分佈機率計算機
	public class Input
	{
	        public string q;
	        public string mean;
	        public string sd;
	        public string side;
	}
	
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
	{
	        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
	        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}
	
	void Main()
	{
	        var input = new Input() { q = TextBox1.Text, mean = TextBox2.Text, sd = TextBox3.Text, side = TextBox4.Text };
	        var json = JsonConvert.SerializeObject(input);
	        var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
	        var httpClient = new HttpClient();
	
	        httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
	        httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
	
	        var response = httpClient.PostAsync(acitionUri, new StringContent(json));
	        var result = response.Result.Content;
		    var scoreResult = result.ReadAsStringAsync().Result;
	}

###常態分佈產生器
	public class Input
	{
	        public string n;
	        public string mean;
	        public string sd;
	}
	
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
	{
	        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
	        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}
	
	void Main()
	{
	        var input = new Input() { n = TextBox1.Text, mean = TextBox2.Text, sd = TextBox3.Text };
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
>這項 Web 服務是使用 Azure Machine Learning 所建立。如需免費試用版，以及有關建立實驗和[發佈 Web 服務](machine-learning-publish-a-machine-learning-web-service.md)的簡介影片，請參閱 [azure.com/ml](http://azure.com/ml)。

以下是建立 Web 服務之實驗的螢幕擷取畫面，以及實驗內每個模組的範例程式碼。

###常態分佈分位數計算機

實驗流程：

![實驗流程][2]
 
	#Data schema with example data (replaced with data from web service)
	data.set=data.frame(p=0.1,mean=0,sd=1,side='L');
	maml.mapOutputPort("data.set"); #send data to output port
	
	# Map 1-based optional input ports to variables
	dataset1 <- maml.mapInputPort(1) # class: data.frame

	param = dataset1
	if (param$p < 0 ) {
	print('Bad input: p must be between 0 and 1')
	param$p = 0
	} else if (param$p > 1) {
	print('Bad input: p must be between 0 and 1')
	param$p = 1
	}
	q = qnorm(param$p,mean=param$mean,sd=param$sd)

	if (param$side == 'U'){
	q = 2* param$mean - q
	} else if (param$side =='L') {
	q = q
	} else {
	print("Invalid side choice")
	}

	output = as.data.frame(q)
	
	# Select data.frame to be sent to the output Dataset port
	maml.mapOutputPort("output");
	
###常態分佈機率計算機
實驗流程：

![實驗流程][3]
 
 	#Data schema with example data (replaced with data from web service)
	data.set=data.frame(q=-1,mean=0,sd=1,side='L');
	maml.mapOutputPort("data.set"); #send data to output port
	
	# Map 1-based optional input ports to variables
	dataset1 <- maml.mapInputPort(1) # class: data.frame

	param = dataset1
	prob = pnorm(param$q,mean=param$mean,sd=param$sd)

	if (param$side == 'U'){
	prob = 1 - prob
	} else if (param$side =='B') {
	prob = ifelse(prob<=0.5,prob * 2, 1)
	} else if (param$side =='L') {
	prob = prob
	} else {
	print("Invalid side choice")
	}

	output = as.data.frame(prob)
	
	# Select data.frame to be sent to the output Dataset port
	maml.mapOutputPort("output");
	
###常態分佈產生器
實驗流程：

![實驗流程][4]

	#Data schema with example data (replaced with data from web service)
	data.set=data.frame(n=50,mean=0,sd=1);
	maml.mapOutputPort("data.set"); #send data to output port
	
	# Map 1-based optional input ports to variables
	dataset1 <- maml.mapInputPort(1) # class: data.frame

	param = dataset1
	dist = rnorm(param$n,mean=param$mean,sd=param$sd)

	output = as.data.frame(t(dist))

	# Select data.frame to be sent to the output Dataset port
	maml.mapOutputPort("output");

##限制 

這些是常態分佈的相關簡單範例。從上面的範例程式碼可以看出，已實作小小的錯誤攔截。

##常見問題集
如需取用 Web 服務或發佈至 Azure Marketplace 的常見問題集，請參閱[這裡](machine-learning-marketplace-faq.md)。

[1]: ./media/machine-learning-r-csharp-normal-distribution/normal-img1.png
[2]: ./media/machine-learning-r-csharp-normal-distribution/normal-img2.png
[3]: ./media/machine-learning-r-csharp-normal-distribution/normal-img3.png
[4]: ./media/machine-learning-r-csharp-normal-distribution/normal-img4.png
 

<!---HONumber=July15_HO2-->