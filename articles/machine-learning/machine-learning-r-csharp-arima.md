<properties 
	pageTitle="預測 - 自動迴歸整合式移動平均 (ARIMA) | Microsoft Azure" 
	description="預測 - 自動迴歸整合移動平均 (ARIMA)" 
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

 
#預測 - 自動迴歸整合移動平均 (ARIMA)

這項[服務](https://datamarket.azure.com/dataset/aml_labs/arima)會實作自動迴歸整合式移動平均 (ARIMA)，以根據使用者所提供的歷程記錄資料產生預測。今年的特定產品需求會增加嗎？ 為方便有效地規劃庫存，我可以預測聖誕節的產品銷售嗎？ 預測模型專門處理此類問題。有了過去的資料，這些模型可以檢查隱藏的趨勢和季節性來預測未來的趨勢。


[AZURE.INCLUDE [電腦-學習-免費-試用](../../includes/machine-learning-free-trial.md)]

>使用者可透過行動裝置應用程式、網站，甚至是本機電腦，來取用這項 Web 服務。不過，該 Web 服務也可用來示範如何使用 Azure Machine Learning，來建立採用 R 程式碼的 Web 服務。只需幾行 R 程式碼並在 Azure Machine Learning Studio 中的按鈕上按幾下，就可以建立採用 R 程式碼的實驗，並將其發佈為 Web 服務。接著可將 Web 服務發佈至 Azure Marketplace，以供世界各地的使用者和裝置取用，而不需要 Web 服務的作者設定基礎結構。

##使用 Web 服務 

這項服務會接受 4 個引數，並計算 ARIMA 預測。輸入引數包括：

* 頻率 - 表示原始資料的頻率 (每日/每週/每月/每季/每年一次)。
* 水平 - 未來預測時間範圍。
* 日期 - 加入時間的新時間序列資料。
* 值 - 加入新的時間序列資料值。

服務的輸出會是已經計算的預測值。

可能的範例輸入如下：

* 頻率 - 12
* 水平 - 12
* 日期 - 1/15/2012;2/15/2012;3/15/2012;4/15/2012;5/15/2012;6/15/2012;7/15/2012;8/15/2012;9/15/2012;10/15/2012;11/15/2012;12/15/2012; 1/15/2013;2/15/2013;3/15/2013;4/15/2013;5/15/2013;6/15/2013;7/15/2013;8/15/2013;9/15/2013;10/15/2013;11/15/2013;12/15/2013; 1/15/2014;2/15/2014;3/15/2014;4/15/2014;5/15/2014;6/15/2014;7/15/2014;8/15/2014;9/15/2014
* 值 - 3.479;3.68;3.832;3.941;3.797;3.586;3.508;3.731;3.915;3.844;3.634;3.549;3.557;3.785;3.782;3.601;3.544;3.556;3.65;3.709;3.682;3.511; 3.429;3.51;3.523;3.525;3.626;3.695;3.711;3.711;3.693;3.571;3.509
 
>在 Azure Marketplace 上託管的這項服務是一個 OData 服務，可透過 POST 或 GET 方法進行呼叫。

以自動化方式取用服務的方法有很多種 ([這裡](http://microsoftazuremachinelearning.azurewebsites.net/ArimaForecasting.aspx)提供一個範例應用程式)。

###啟動 Web 服務使用的 C# 程式碼：

	public class Input
    {
        public string frequency;
        public string horizon;
        public string date;
        public string value;
    }

	public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
         byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
         return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }

       
	void Main()
	{
  		var input = new Input() { frequency = TextBox1.Text, horizon = TextBox2.Text, date = TextBox3.Text, value = TextBox4.Text };
		var json = JsonConvert.SerializeObject(input);
		var acitionUri =  "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
	       
	  	var httpClient = new HttpClient();
	   	httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere","ChangeToAPIKey");
	   	httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
	  	var query = httpClient.PostAsync(acitionUri,new StringContent(json));
	  	var result = query.Result.Content;
	  	var scoreResult = result.ReadAsStringAsync().Result;
  	}

##建立 Web 服務 

>這項 Web 服務是使用 Azure Machine Learning 所建立。如需免費試用版，以及有關建立實驗和[發佈 Web 服務](machine-learning-publish-a-machine-learning-web-service.md)的簡介影片，請參閱 [azure.com/ml](http://azure.com/ml)。以下是建立 Web 服務之實驗的螢幕擷取畫面，以及實驗內每個模組的範例程式碼。

Azure Machine Learning 中已建立新的空白實驗，並已使用預先定義的資料結構描述上傳範例輸入資料。資料結構描述會連結至[執行 R 指令碼][execute-r-script]模組，這個模組使用 R 的 ‘auto.arima’ 和 ‘forecast’ 函數來產生 ARIMA 預測模型。

###實驗流程：

![建立工作區][2]

####模組 1：
 
	# Add in the CSV file with the data in the format shown below 
![建立工作區][3]

####模組 2：
	# data input
	data <- maml.mapInputPort(1) # class: data.frame
	library(forecast)
	
	# preprocessing
	colnames(data) <- c("frequency", "horizon", "dates", "values")
	dates <- strsplit(data$dates, ";")[[1]]
	values <- strsplit(data$values, ";")[[1]]
	
	dates <- as.Date(dates, format = '%m/%d/%Y')
	values <- as.numeric(values)
	
	# fit a time-series model
	train_ts<- ts(values, frequency=data$frequency)
	fit1 <- auto.arima(train_ts)
	train_model <- forecast(fit1, h = data$horizon)
	plot(train_model)
	
	# produce forecasting
	train_pred <- round(train_model$mean,2)
	data.forecast <- as.data.frame(t(train_pred))
	colnames(data.forecast) <- paste("Forecast", 1:data$horizon, sep="")
	
	# data output
	maml.mapOutputPort("data.forecast");


##限制 

這是一個非常簡單的 ARIMA 預測範例。從上面的範例程式碼可以看出，未實作錯誤攔截，且這項服務假設所有變數都是連續/正值，而頻率應該是大於 1 的整數。日期和值向量的長度則應該相同。日期變數應遵守 ‘mm/dd/yyyy’ 格式。

##常見問題集
如需取用 Web 服務或發佈至 Marketplace 的常見問題集，請參閱[這裡](machine-learning-marketplace-faq.md)。

[1]: ./media/machine-learning-r-csharp-arima/arima-img1.png
[2]: ./media/machine-learning-r-csharp-arima/arima-img2.png
[3]: ./media/machine-learning-r-csharp-arima/arima-img3.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 

<!---HONumber=July15_HO2-->