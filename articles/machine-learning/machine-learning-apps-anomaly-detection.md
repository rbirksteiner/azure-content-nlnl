<properties 
	pageTitle="機器學習應用程式：異常偵測服務 |Microsoft Azure" 
	description="「異常偵測 API」是一個搭配 Microsoft Azure Machine Learning 建置的範例，此 API 使用固定時間間隔的數值，偵測時間序列資料中的異常狀況。" 
	services="machine-learning" 
	documentationCenter="" 
	authors="LuisCabrer" 
	manager="paulettm"
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.devlang="na" 
	ms.topic="reference" 
	ms.tgt_pltfrm="na" 
	ms.workload="multiple" 
	ms.date="05/05/2015" 
	ms.author="luisca"/>


# 機器學習異常偵測服務#

##概觀

「異常偵測 API」是一個搭配 Azure Machine Learning 建置的範例，此 API 使用固定時間間隔的數值，偵測時間序列資料中的異常狀況。

[AZURE.INCLUDE [電腦-學習-免費-試用](../../includes/machine-learning-free-trial.md)]

此異常偵測服務可以偵測下列不同類型的時間序列資料異常：

1. 正向和負向趨勢：監視運算中的記憶體使用量，例如趨勢向上表示有記憶體流失。

2. 動態範圍的值增加：舉例來說，監視服務所擲回的例外狀況時，若動態範圍的值有任何增加，可能表示服務的健康情況不穩定。

3. 尖峰和下降：例如，監視服務的登入失敗數目或電子商務網站中的結帳數目時，若有尖峰或下降可能表示異常的行為。


這些偵測器會隨著時間追蹤數值的變化，並且報告這些數值正在進行的變化。它們不需要臨機操作調整臨界值，而且其分數可用來控制偽陽性率。異常偵測 API 可用在許多案例，例如隨時間追蹤 KPI 的服務監視、使用計量 (例如搜尋數、點擊次數)、效能計數器 (例如隨時間的記憶體、CPU、檔案讀取數等)。

##API 定義

此服務提供透過 HTTPS 的 REST 型 API，有各種不同方式可使用此 API，包括 Web 或行動應用程式、R、Python、Excel 等等。我們的 [Azure Web 應用程式](http://anomalydetection-aml.azurewebsites.net/)可協助您針對資料執行異常偵測 Web 服務，並將結果視覺化。

您也可以透過 REST API 呼叫，將您的時間序列資料傳送到此服務，它會執行上述三個異常類型的組合。服務會在 AzureML Machine Learning 平台上執行，順暢調整您的商業需求並提供 99.95% 的 SLA。

下圖顯示使用上述架構依照時間序列偵測到的異常範例。時間序列有 2 個不同的層級變更和 3 個尖峰。紅點顯示偵測到層級變更時的時間，紅色向上箭號則顯示偵測到的尖峰。


![][1]

##輸入

API 採用 2 個輸入參數

1. "data" 表示輸入時間序列，格式為：t1=v1;t2=v2;... 

 
	範例資料：
		
		"9/21/2014 11:05:00 AM=3;9/21/2014 11:10:00 AM=9.09;9/21/2014 11:15:00 AM=0;"

2. "params" 設定為 "SpikeDetector.TukeyThresh=3; SpikeDetector.ZscoreThresh=3"，控制尖峰偵測器的敏感度。越高的值將會攔截到越高的尖峰，反之亦然。

	具有上述輸入參數的範例 URL：

		https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v1/Score?data=%279%2F21%2F2014%2011%3A05%3A00%20AM%3D3%3B9%2F21%2F2014%2011%3A10%3A00%20AM%3D9.09%3B9%2F21%2F2014%2011%3A15%3A00%20AM%3D0%3B%27&params=%27SpikeDetector.TukeyThresh%3D3%3B%20SpikeDetector.ZscoreThresh%3D3%27



###輸出###

API 會對您的時間序列資料執行這些偵測器，然後傳回每個時間點的異常分數。此輸出可使用簡單的剖析器進行剖析，如 [https://adresultparser.codeplex.com/](https://adresultparser.codeplex.com/) 中所示。其中的範例程式碼示範如何連線到 API 並剖析輸出。產生的警示可以具體呈現在儀表板中並 (或) 傳送給專家人員以採取更正動作。

上面範例輸入的範例輸出：

	"Time,Data,TSpike,ZSpike,Martingale values,Alert indicator,Martingale values(2),Alert indicator(2),;9/21/2014 11:05:00 AM,3,0,0,-0.687952590518378,0,-0.687952590518378,0,;9/21/2014 11:10:00 AM,9.09,0,0,-1.07030497733224,0,-0.884548154298423,0,;9/21/2014 11:15:00 AM,0,0,0,-1.05186237440962,0,-1.173800281031,0,;"

代表的是下列表格：

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">時間</th>
   <th align="left" valign="middle">資料</th>
   <th align="left" valign="middle">Tspike</th>
   <th align="left" valign="middle">Zspike</th>
   <th align="left" valign="middle">Martingale values</th>
   <th align="left" valign="middle">Alert indicator</th>
   <th align="left" valign="middle">Martingale values (2)</th>
   <th align="left" valign="middle">Alert indicator (2)</th>
   </tr>
<tr>
   <td valign="middle">9/21/2014 11:05</td>
   <td valign="middle">3</td>
   <td valign="middle">0</td>
   <td valign="middle">0</td>
   <td valign="middle">-0.687952591</td>
   <td valign="middle">0</td>
   <td valign="middle">-0.687952591</td>
   <td valign="middle">0</td>
   </tr>
<tr>
<td valign="middle">9/21/2014 11:10</td>
   <td valign="middle">9.09</td>
   <td valign="middle">0</td>
   <td valign="middle">0</td>
   <td valign="middle">-1.070304977</td>
   <td valign="middle">0</td>
   <td valign="middle">-0.884548154</td>
   <td valign="middle">0</td>
    </tr>
<tr>
<td valign="middle">9/21/2014 11:15</td>
   <td valign="middle">0</td>
   <td valign="middle">0</td>
   <td valign="middle">0</td>
   <td valign="middle">-1.051862374</td>
   <td valign="middle">0</td>
   <td valign="middle">-1.1738002814</td>
   <td valign="middle">0</td>
   </tr>
   </table>

[1]: ./media/machine-learning-apps-anomaly-detection/anomaly-detection.jpg

 

 

<!---HONumber=July15_HO1-->