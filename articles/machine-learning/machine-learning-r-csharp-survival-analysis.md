<properties 
	pageTitle="使用 Azure Machine Learning 進行存活分析 | Microsoft Azure" 
	description="存活分析 - 事件發生機率" 
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


#存活分析 

在許多情況下，主要評估結果會是感興趣事件的時間。換句話說，詢問的問題會是「何時將發生這個事件」。例如，請考慮資料描述感興趣的事件 (疾病復發、取得博士學位、煞車來令片故障) 發生前所經過之時間 (天、年、里程數等) 的案例。資料中的每個執行個體代表特定物件 (病患、學生、汽車等)。


[AZURE.INCLUDE [電腦-學習-免費-試用](../../includes/machine-learning-free-trial.md)]

這項 [Web 服務](https://datamarket.azure.com/dataset/aml_labs/survivalanalysis)回答了「物件 x 在時間 n 前發生感興趣事件的機率為何？」這個問題。 藉由提供存活分析模型，這項 Web 服務可讓使用者提供資料來定型模型並加以測試。此實驗的主旨是要在感興趣的事件發生之前建立經過時間長度的模型。

>使用者可透過行動裝置應用程式、網站，甚至是本機電腦，來取用這項 Web 服務。不過，該 Web 服務也可用來示範如何使用 Azure Machine Learning，來建立採用 R 程式碼的 Web 服務。只需幾行 R 程式碼並在 Azure Machine Learning Studio 中的按鈕上按幾下，就可以建立採用 R 程式碼的實驗，並將其發佈為 Web 服務。接著可將 Web 服務發佈至 Azure Marketplace，以供世界各地的使用者和裝置取用，而不需要 Web 服務的作者設定基礎結構。

##使用 Web 服務

下表中顯示 Web 服務的輸入資料結構描述。必須輸入六項資訊：定型資料、測試資料、感興趣的時間、「時間」維度的索引、「事件」維度的索引和變數類型 (連續或因素)。訓練資料是以字串表示，其中的資料列是以逗號分隔，而資料行是以分號分隔。資料的特徵數目是彈性的。輸入字串中的所有元素都必須為數值。在定型資料中，「時間」維度表示自研究的起點 (病患收到藥物治療方案、學生開始博士研究、汽車開始發動等)，到發生感興趣的事件 (病患再度吸食毒品、學生取得博士學位、汽車的煞車來令片故障等) 所經過的時間單位數 (日、年、里程數等)。「事件」維度指出感興趣的事件是否發生在研究結束時。「事件 = 1」的值表示感興趣的事件發生於「時間」維度所指出的時間；而「事件 = 0」表示到「時間」維度所指定的時間為止都未發生感興趣的事件。

- trainingdata - 字元字串。資料列會以逗號分隔，而資料行會以分號分隔。每個資料列包含「時間」維度、「事件」維度和預測變數。
- testingdata - 包含特定物件之預測變數的一列資料。
- time_of_interest - 興趣 n 的經過時間。
- index_time -「時間」維度的資料行索引 (從 1 開始)。
- index_event -「事件」維度的資料行索引 (從 1 開始)。
- variable_types - 以分號做為分隔符號的字元字串。0 表示連續變數，而 1 表示因子變數。


輸出是在特定時間前發生事件的機率。

>在 Azure Marketplace 上託管的這項服務是一個 OData 服務，可透過 POST 或 GET 方法進行呼叫。

以自動化方式取用服務的方法有很多種 ([這裡](http://microsoftazuremachinelearning.azurewebsites.net/SurvivalAnalysis.aspx)提供一個範例應用程式)。

###啟動 Web 服務使用的 C# 程式碼：
	public class Input
	{
	        public string trainingdata;
	        public string testingdata;
	        public string timeofinterest;
	        public string indextime;
	        public string indexevent;
	        public string variabletypes;
	}

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
	        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
	        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}
	
	void Main()
	{
	        var input = new Input() { trainingdata = TextBox1.Text, testingdata = TextBox2.Text, timeofinterest = TextBox3.Text, indextime = TextBox4.Text, indexevent = TextBox5.Text, variabletypes = TextBox6.Text };
	        var json = JsonConvert.SerializeObject(input);
	        var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
	        var httpClient = new HttpClient();
	
	        httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
	        httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
	
	        var response = httpClient.PostAsync(acitionUri, new StringContent(json));
	        var result = response.Result.Content;
		    var scoreResult = result.ReadAsStringAsync().Result;
	}




這項測試的解譯如下。假設資料的目標是要建立在收到兩種治療方案之一的病患再次吸食毒品的經過時間模型。Web 服務的輸出如下所示：病患目前 35 歲，先前藥物治療 2 次，採用長期居家治療方案，吸食海洛因和古柯鹼，在第 500 天前再度吸食毒品的機率是 95.64%。

##建立 Web 服務

>這項 Web 服務是使用 Azure Machine Learning 所建立。如需免費試用版，以及有關建立實驗和[發佈 Web 服務](machine-learning-publish-a-machine-learning-web-service.md)的簡介影片，請參閱 [azure.com/ml](http://azure.com/ml)。以下是建立 Web 服務之實驗的螢幕擷取畫面，以及實驗內每個模組的範例程式碼。

Azure Machine Learning 中已建立新的空白實驗，並將兩個[執行 R 指令碼][execute-r-script]模組提取到工作區。資料結構描述是以簡單[執行 R 指令碼][execute-r-script]建立的，其定義 Web 服務的輸入資料結構描述。這個模組會接著連結至執行主要工作的第二個[執行 R 指令碼][execute-r-script]模組。此模組會進行資料前處理、模型建立和預測。在資料前處理步驟中，長字串所表示的輸入資料會轉換成資料框架。在模型建立步驟中，首次安裝外部 R 套件 "survival_2.37-7.zip" 以進行存活分析。然後會在序列資料處理工作之後執行 "coxph" 函數。在 R 文件中可以讀取存活分析的 "coxph" 函數詳細資料。在預測步驟中，會利用 "surfit" 函數在定型模型中提供測試執行個體，而且此測試執行個體的存活曲線會產生成為 “curve” 變數。最後會取得感興趣時間的機率。

###實驗流程：

![實驗流程][1]

####模組 1：

    #Data schema with example data (replaced with data from web service)
    trainingdata="53;1;29;0;0;3,79;1;34;0;1;2,45;1;27;0;1;1,37;1;24;0;1;1,122;1;30;0;1;1,655;0;41;0;0;1,166;1;30;0;0;3,227;1;29;0;0;3,805;0;30;0;0;1,104;1;24;0;0;1,90;1;32;0;0;1,373;1;26;0;0;1,70;1;36;0;0;1”
    testingdata="35;2;1;1"
    time_of_interest="500"
    index_time="1"
    index_event="2"

    sampleInput=data.frame(trainingdata,testingdata,time_of_interest,index_time,index_event,variable_types)

    maml.mapOutputPort("sampleInput"); #send data to output port
	
####模組 2：

    #Read data from input port
    data <- maml.mapInputPort(1) 
    colnames(data) <- c("trainingdata","testingdata","time_of_interest","index_time","index_event","variable_types")

    # Preprocessing training data
    traindingdata=data$trainingdata
    y=strsplit(as.character(data$trainingdata),",")
    n_row=length(unlist(y))
    z=sapply(unlist(y), strsplit, ";", simplify = TRUE)
    mydata <- data.frame(matrix(unlist(z), nrow=n_row, byrow=T), stringsAsFactors=FALSE)
    n_col=ncol(mydata)

    # Preprocessing testing data
    testingdata=as.character(data$testingdata)
    testingdata=unlist(strsplit(testingdata,";"))

    # Preprocessing other input parameters
    time_of_interest=data$time_of_interest
    time_of_interest=as.numeric(as.character(time_of_interest))
    index_time = data$index_time
    index_event = data$index_event
    variable_types = data$variable_types

    # Necessary R packages
    install.packages("src/packages_survival/survival_2.37-7.zip",lib=".",repos=NULL,verbose=TRUE)
    library(survival)

    # Prepare to build model
    attach(mydata)

    for (i in 1:n_col){ mydata[,i]=as.numeric(mydata[,i])} 
    d_time=paste("X",index_time,sep = "")
    d_event=paste("X",index_event,sep = "")
    v_time_event <- c(d_time,d_event)
    v_predictors = names(mydata)[!(names(mydata) %in% v_time_event)]

    variable_types = unlist(strsplit(as.character(variable_types),";"))

    len = length(v_predictors)
    c="" # Construct the execution string
    for (i in 1:len){
    if(i==len){
    if(variable_types[i]!=0){ c=paste(c, "factor(",v_predictors[i],")",sep="")}
     else{ c=paste(c, v_predictors[i])}
    }else{
    if(variable_types[i]!=0){c=paste(c, "factor(",v_predictors[i],") + ",sep="")}
    else{c=paste(c, v_predictors[i],"+")}
    }
    }
    f=paste("coxph(Surv(",d_time,",",d_event,") ~")
    f=paste(f,c)
    f=paste(f,", data=mydata )")

    # Fit a Cox proportional hazards model and get the predicted survival curve for a testing instance 
    fit=eval(parse(text=f))

    testingdata = as.data.frame(matrix(testingdata, ncol=len,byrow = TRUE),stringsAsFactors=FALSE)
    names(testingdata)=v_predictors
    for (i in 1:len){ testingdata[,i]=as.numeric(testingdata[,i])}

    curve=survfit(fit,testingdata)

    # Based on user input, find the event occurrence probability
    position_closest=which.min(abs(prob_event$time - time_of_interest))

    if(prob_event[position_closest,"time"]==time_of_interest){# exact match
    output=prob_event[position_closest,"prob"]
    }else{# not exact match
    if(time_of_interest>max(prob_event$time)){
    output=prob_event[position_closest,"prob"]
    }else if(time_of_interest<min(prob_event$time)){
    output=prob_event[position_closest,"prob"]
    }else{output=(prob_event[position_closest,"prob"]+prob_event[position_closest+1,"prob"])/2}
    }

    #Pull out results to send to web service
    output=paste(round(100*output, 2), "%") 
    maml.mapOutputPort("output"); #output port




##限制

這項 Web 服務只能以數值做為特徵變數 (資料行)。[事件] 資料行只能接受 0 或 1 的值。[時間] 資料行必須是正整數。

##常見問題集
如需取用 Web 服務或發佈至 Azure Marketplace 的常見問題集，請參閱[這裡](machine-learning-marketplace-faq.md)。

[1]: ./media/machine-learning-r-csharp-survival-analysis/survive_img2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 

<!---HONumber=July15_HO2-->