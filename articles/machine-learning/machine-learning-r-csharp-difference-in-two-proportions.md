<properties 
	pageTitle="比例差異測試 | Microsoft Azure" 
	description="比例差異測試" 
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


#比例差異測試


兩個比例有統計上的差異嗎？ 假設使用者想要比較兩部電影，以判斷其中一部電影與另一部電影比較起來是否有明顯較高比例的「讚」。在大型樣本中，在 0.50 和 0.51 比例之間可能會有統計上的顯著差異；但在小型樣本中，可能沒有足夠的資料可以判斷這些比例是否真有差異。


[AZURE.INCLUDE [電腦-學習-免費-試用](../../includes/machine-learning-free-trial.md)]

這項 [Web 服務](https://datamarket.azure.com/dataset/aml_labs/prop_test)會根據使用者針對 2 個比較群組輸入的的成功次數和總試驗次數，來進行兩個比例的差異假設測試。一個可能的案例是從電影比較應用程式內部呼叫這項 Web 服務，根據電影分級讓使用者知道其中一部電影是否比另一部電影受到更多人的喜愛。

>使用者可透過行動裝置應用程式、網站，甚至是本機電腦，來取用這項 Web 服務。不過，該 Web 服務也可用來示範如何使用 Azure Machine Learning，來建立採用 R 程式碼的 Web 服務。只需幾行 R 程式碼並在 Azure Machine Learning Studio 中的按鈕上按幾下，就可以建立採用 R 程式碼的實驗，並將其發佈為 Web 服務。接著可將 Web 服務發佈至 Azure Marketplace，以供世界各地的使用者和裝置取用，而不需要 Web 服務的作者設定基礎結構。


##使用 Web 服務

這項服務可接受 4 個引數，並執行比例的假設測試。

輸入引數包括：

* Successes1 - 樣本 1 中的成功事件數目。
* Successes2 - 樣本 2 中的成功事件數目。
* Total1 - 樣本 1 的大小。
* Total2 - 樣本 2 的大小。

服務的輸出是假設測試的結果，並加上卡方統計資料、df、p 值，以及樣本 1/2 中的比例和信賴區間界限。

>在 Azure Marketplace 上託管的這項服務是一個 OData 服務，可透過 POST 或 GET 方法進行呼叫。

以自動化方式取用服務的方法有很多種 ([這裡](http://microsoftazuremachinelearning.azurewebsites.net/DifferenceInProportionsTest.aspx)提供一個範例應用程式)。

###啟動 Web 服務使用的 C# 程式碼：

	public class Input
	{
	        public string successes1;
	        public string successes2;
	        public string total1;
	        public string total2;
	}
	
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
	{
	        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
	        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}

	void Main()
	{
	        var input = new Input() { successes1 = TextBox1.Text, successes2 = TextBox2.Text, total1 = TextBox3.Text, total2 = TextBox4.Text };
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

Azure Machine Learning 中已建立具有兩個[執行 R 指令碼][execute-r-script]模組的新空白實驗。第一個模組會定義資料結構描述，而第二個模組則會使用 R 內的 prop.test 命令來執行 2 個比例的假設測試。


###實驗流程：

![實驗流程][2]


####模組 1：
	####Schema definition  
	data.set=data.frame(successes1=50,successes2=60,total1=100,total2=100);
	maml.mapOutputPort("data.set"); #send data to output port
	dataset1 = maml.mapInputPort(1) #read data from input port
	

####模組 2：

	test=prop.test(c(dataset1$successes1[1],dataset1$successes2[1]),c(dataset1$total1[1],dataset1$total2[1])) #conduct hypothesis test

	result=data.frame(
	result=ifelse(test$p.value<0.05,"The proportions are different!",
	"The proportions aren't different statistically."),
	ChiSquarestatistic=round(test$statistic,2),df=test$parameter,
	pvalue=round(test$p.value,4),
	proportion1=round(test$estimate[1],4),
	proportion2=round(test$estimate[2],4),
	confintlow=round(test$conf.int[1],4),
	confinthigh=round(test$conf.int[2],4)) 

	maml.mapOutputPort("result"); #output port
	

##限制 

這是一個非常簡單的測試範例，可測試 2 個比例中的差異。從上面的範例程式碼可以看出，未實作錯誤攔截，且服務會假設所有變數都是連續的。

##常見問題集
如需取用 Web 服務或發佈至 Azure Marketplace 的常見問題集，請參閱[這裡](machine-learning-marketplace-faq.md)。

[1]: ./media/machine-learning-r-csharp-difference-in-two-proportions/hyptest-img1.png
[2]: ./media/machine-learning-r-csharp-difference-in-two-proportions/hyptest-img2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 

<!---HONumber=July15_HO2-->