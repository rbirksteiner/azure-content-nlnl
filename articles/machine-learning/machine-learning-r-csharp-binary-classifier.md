<properties 
	pageTitle="二元分類器 |Microsoft Azure" 
	description="二元分類器" 
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



#二元分類器

假設您有一個資料集，並想要根據獨立變更來預測二元相依變數。「羅吉斯迴歸」是適用於此類預測的普遍統計技術。這裡的相依變數是二元或二分類變數，而 p 是感興趣特性的存在機率。


[AZURE.INCLUDE [電腦-學習-免費-試用](../../includes/machine-learning-free-trial.md)]

一個簡單的案例是研究人員嘗試根據資訊 (高中的 GPA、家庭收入、居住地、性別)，來預測未來學生是否有可能接受大學的入學許可。預測的結果是未來學生接受入學許可的機率。這項 [Web 服務](https://datamarket.azure.com/dataset/aml_labs/log_regression)會將資料套入羅吉斯迴歸模型，並為資料中的每個觀察輸出機率值 (y)。
  
>使用者可透過行動裝置應用程式、網站，甚至是本機電腦，來取用這項 Web 服務。不過，該 Web 服務也可用來示範如何使用 Azure Machine Learning，來建立採用 R 程式碼的 Web 服務。只需幾行 R 程式碼並在 Azure Machine Learning Studio 中的按鈕上按幾下，就可以建立採用 R 程式碼的實驗，並將其發佈為 Web 服務。接著可將 Web 服務發佈至 Azure Marketplace，以供世界各地的使用者和裝置取用，而不需要 Web 服務的作者設定基礎結構。
  

##使用 Web 服務  
此 web 服務會根據所有觀察的獨立變數來提供相依變數的預測值。Web 服務需要使用者以字串形式輸入資料，其中資料列會以逗號 (,) 分隔，而資料行會以分號 (;) 分隔。Web 服務一次可輸入一個資料列，而且第一個資料行必須是相依變數。範例資料集看起來可能像這樣：

![範例資料][1]

沒有相依變數的觀察應輸入 “NA” 做為 y。上述資料集的資料輸入字串如下：“1;5;2,1;1;6,0;5.3;2.1,0;5;5,0;3;4,1;2;1,NA;3;4”。輸出會是根據獨立變數的每個資料列預估值。

>在 Azure Marketplace 上託管的這項服務是一個 OData 服務，可透過 POST 或 GET 方法進行呼叫。

以自動化方式取用服務的方法有很多種 ([這裡](http://microsoftazuremachinelearning.azurewebsites.net/BinaryClassifier.aspx)提供一個範例應用程式)。

###啟動 Web 服務使用的 C# 程式碼：

	public class Input
	{
   		public string value;
	}

	public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
	{
		byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
		return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}
	
	void Main()
	{
		var input = new Input() { value = TextBox1.Text };
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

Azure Machine Learning 中已建立新的空白實驗，並將兩個[執行 R 指令碼][execute-r-script]模組提取到工作區。這項 Web 服務使用基礎 R 指令碼來執行 Azure Machine Learning 實驗。這項實驗有兩個部分：結構描述定義，以及定型模型 + 計分。第一個模組會定義輸入資料集的預期結構，其中第一個變數是相依變數，而其餘變數是獨立變數。第二個模組會將輸入資料套入一般羅吉斯迴歸模型。

![實驗流程][2]

####模組 1：

	#Schema definition  
	data <- data.frame(value = "1;2;3,1;5;6,0;8;9", stringsAsFactors=FALSE) 
	maml.mapOutputPort("data");  

####模組 2：
	#GLM modeling   
	data <- maml.mapInputPort(1) # class: data.frame  
	
	data.split <- strsplit(data[1,1], ",")[[1]] 
	data.split <- sapply(data.split, strsplit, ";", simplify = TRUE) 
	data.split <- sapply(data.split, strsplit, ";", simplify = TRUE) 
	data.split <- as.data.frame(t(data.split)) data.split <- 
	data.matrix(data.split) 
	data.split <- data.frame(data.split) 
	
	model <- glm(data.split$V1 ~., family='binomial', data=data.split)  
	out <- data.frame(predict(model,data.split, type="response")) 
	pred1 <- as.data.frame(out) 
	group <- array(1:nrow(pred1)) 
	for (i in 1:nrow(pred1))  
		{
		if(as.numeric(pred1[i,])>0.5) {group[i]=1} 
		else {group[i]=0}
		} 
	pred2 <- as.data.frame(group) 
	maml.mapOutputPort("pred2");  


##限制
這是一個非常簡單的二元分類 Web 服務範例。從上面的範例程式碼可以看出，未實作錯誤攔截，且由於建立這項 Web 服務時，服務只輸入數值，因此服務會假設所有項目都是二元/連續變數 (不允許類別功能)。此外，由於 Web 服務呼叫的要求/回應本質，以及每次呼叫 Web 服務時就會調整模型的事實，該項服務目前只能處理有限的資料大小。

##常見問題集
如需取用 Web 服務或發佈至 Azure Marketplace 的常見問題集，請參閱[這裡](machine-learning-marketplace-faq.md)。

[1]: ./media/machine-learning-r-csharp-binary-classifier/binary1.png
[2]: ./media/machine-learning-r-csharp-binary-classifier/binary2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 

<!---HONumber=July15_HO2-->