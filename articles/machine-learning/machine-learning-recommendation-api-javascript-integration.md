<properties 
	pageTitle="Machine Learning 建議：JavaScript 整合 | Microsoft Azure" 
	description="Azure Machine Learning Recommendations - JavaScript 整合 – 文件" 
	services="machine-learning" 
	documentationCenter="" 
	authors="AharonGumnik" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="03/29/2015" 
	ms.author="v-ahgumn"/>

# Azure Machine Learning Recommendations - JavaScript 整合

本文件說明如何使用 JavaScript 整合您的網站。JavaScript 可讓您傳送資料擷取事件，並在建立建議模型之後取用建議。透過 JS 完成的所有操作也能從伺服器端完成。

[AZURE.INCLUDE [電腦-學習-免費-試用](../../includes/machine-learning-free-trial.md)]

##1.一般概觀
利用 2 階段所包含的 Azure ML Recommendations 來整合您的網站：

1.	將事件傳送到 Azure ML Recommendations。這樣做將能建立建議模型。
2.	取用建議。建立模型之後，您就可以取用建議。(本文件並未說明如何建立模型，請閱讀快速入門指南以取得做法的詳細資訊)。


<ins>第一階段</ins>

在第一階段中，您會將一個小型 JavaScript 程式庫插入 HTML 網頁，讓該頁面可在事件於 HTML 網頁上發生時將其傳送到 Azure ML Recommendations 伺服器 (透過資料市場)：

![繪圖 1][1]

<ins>第二階段</ins>

在第二階段中，當您想要在頁面上顯示建議時，請選取下列其中一個選項：

1. 您的伺服器 (在頁面轉譯階段) 呼叫 Azure ML Recommendations 伺服器 (透過資料市場) 以取得建議。結果會包含項目識別碼的清單。您的伺服器需要利用「中繼資料」 (例如影像、描述) 項目擴充結果，以及將建立的頁面傳送給瀏覽器。

![繪圖 2][2]

2. 另一個選擇是使用第一階段中的小型 JavaScript 檔案，以取得簡單的建議項目清單。這裡收到的資料會比第一個選擇還要精簡。

![繪圖 3][3]

##2.必要條件

1. 使用 API 建立新的模型。如需如何執行此操作，請參閱快速入門指南。
2. 使用 base64 將 &lt;dataMarketUser&gt;:&lt;dataMarketKey&gt; 編碼。(這將會用於基本驗證，讓 JS 程式碼能夠呼叫 API)。


##3.使用 JavaScript 傳送資料擷取事件
下列步驟可加入傳送事件的速度：

1.	在程式碼中納入 JQuery 程式庫。您可以利用下列 URL 來從 nuget 下載。

		http://www.nuget.org/packages/jQuery/1.8.2
2.	從下列 URL 納入 Recommendations Java Script 程式庫：http://1drv.ms/1Aoa1Zp

3.	使用適當參數初始化 Azure ML Recommendations 程式庫。

		<script>
			AzureMLRecommendationsStart("<base64encoding of username:key>",
			"<model_id>");
		</script>

4.	傳送適當的事件。有關所有類型之事件 (以點選事件為例) 的詳細說明，請參閱下節

		<script>
			if (typeof AzureMLRecommendationsEvent=="undefined") { 		
        	        	AzureMLRecommendationsEvent = [];
	                }
			AzureMLRecommendationsEvent.push({ event: "click", item: "18321116" });
		</script>


###3.1.限制和瀏覽器支援
這是參考實作並依現況提供。其應該支援所有主要瀏覽器。

###3.2.事件類型
程式庫一共支援 5 種類型的事件：點選 (Click)、建議點選 (Recommendation Click)、加入購物車 (Add to Shop Cart)、從購物車移除 (Remove from Shop Cart) 以及購買 (Purchase)。另外還有一種用來設定使用者內容的事件，稱為登入 (Login)。

####3.2.1.點選事件
每當使用者點選項目時，都應該會使用這個事件。當使用者點選項目時，通常會開啟含有該項目詳細資料的新頁面。在這個頁面中，應該會觸發此事件。

參數：- event (字串，強制) – “click” - item (字串，強制) – 項目的唯一識別碼 - itemName (字串，選擇性) – 項目的名稱 - itemDescription (字串，選擇性) – 項目的描述 - itemCategory (字串，選擇性) – 項目的類別
		
		<script>
			if (typeof AzureMLRecommendationsEvent == "undefined") { AzureMLRecommendationsEvent = []; }
			AzureMLRecommendationsEvent.push({ event: "click", item: "3111718" });
		</script>

或使用選擇性資料：

		<script>
			if (typeof AzureMLRecommendationsEvent === "undefined") { AzureMLRecommendationsEvent = []; }
			AzureMLRecommendationsEvent.push({event: "click", item: "3111718", itemName: "Plane", itemDescription: "It is a big plane", itemCategory: "Aviation"});
		</script>


####3.2.2.建議點選事件
每當使用者點選項目 (從 Azure ML Recommendations 接收當成建議的項目) 時，都應該會使用這個事件。當使用者點選項目時，通常會開啟含有該項目詳細資料的新頁面。在這個頁面中，應該會觸發此事件。

參數：- event (字串，強制) – “recommendationclick” - item (字串，強制) – 項目的唯一識別碼 - itemName (字串，選擇性) – 項目的名稱 - itemDescription (字串，選擇性) – 項目的描述 - itemCategory (字串，選擇性) – 項目的類別 - seeds (字串陣列，選擇性) – 產生建議查詢的種子。- recoList (字串陣列，選擇性) – 產生所點選項目之建議要求的結果。
		
		<script>
			if (typeof AzureMLRecommendationsEvent=="undefined") { AzureMLRecommendationsEvent = []; }
			AzureMLRecommendationsEvent.push({event: "recommendationclick", item: "18899918" });
		</script>

或使用選擇性資料：

		<script>
			if (typeof AzureMLRecommendationsEvent == "undefined") { AzureMLRecommendationsEvent = []; }
			AzureMLRecommendationsEvent.push({ event: eventName, item: "198", itemName: "Plane2", itemDescription: "It is a big plane2", itemCategory: "Default2", seeds: ["Seed1", "Seed2"], recoList: ["199", "198", "197"] 				});
		</script>


####3.2.3.加入購物車事件
當使用者將項目加入購物車時，應該會使用這個事件。參數：* event (字串，強制) – “addshopcart” * item (字串，強制) – 項目的唯一識別碼 * itemName (字串，選擇性) – 項目的名稱 * itemDescription (字串，選擇性) – 項目的描述 * itemCategory (字串，選擇性) – 項目的類別
		
		<script>
			if (typeof AzureMLRecommendationsEvent == "undefined") { AzureMLRecommendationsEvent = []; }
			AzureMLRecommendationsEvent.push({event: "addshopcart", item: "13221118" });
		</script>

####3.2.4.移除購物車事件
當使用者移除購物車中的項目時，應該會使用這個事件。

參數：* event (字串，強制) – “removeshopcart” * item (字串，強制) – 項目的唯一識別碼 * itemName (字串，選擇性) – 項目的名稱 * itemDescription (字串，選擇性) – 項目的描述 * itemCategory (字串，選擇性) – 項目的類別
		
		<script>
			if (typeof AzureMLRecommendationsEvent=="undefined") { AzureMLRecommendationsEvent = []; }
			AzureMLRecommendationsEvent.push({ event: "removeshopcart", item: "111118" });
		</script>

####3.2.5.購買事件
當使用者購買購物車中的項目時，應該會使用這個事件。

參數：* event (字串) - “purchase” * items (已購買項目) - 陣列會為購買的每個項目保留一個項目。<br><br> 已購買項目的格式：* item (字串) – 項目的唯一識別碼。* count (整數或字串) – 已購買的項目數量。* price (浮點數或字串) – 選擇性欄位 – 項目的價格。

以下範例顯示總共購買 3 個項目 (33, 34, 35)，已填入其中兩個的所有欄位 (item, count, price)，還有一個 (item 34) 沒有價格。

		<script>
			if ( typeof AzureMLRecommendationsEvent == "undefined"){ AzureMLRecommendationsEvent = []; }
			AzureMLRecommendationsEvent.push({ event: "purchase", items: [{ item: "33", count: "1", price: "10" }, { item: "34", count: "2" }, { item: "35", count: "1", price: "210" }] });
		</script>

####3.2.6.使用者登入事件
Azure ML Recommendations 事件程式庫會建立並使用 Cookie，以識別來自相同瀏覽器的事件。為了改善模型結果，Azure ML Recommendations 能夠為使用者設定將覆寫 Cookie 使用的唯一識別。

在使用者登入您的網站後，應該會使用這個事件。

參數：* event (字串) - “userlogin” * user (字串) - 使用者的唯一識別。<script> if (typeof AzureMLRecommendationsEvent=="undefined") { AzureMLRecommendationsEvent = ; } AzureMLRecommendationsEvent.push({event: "userlogin", user: “ABCD10AA” }); </script>

##4.透過 JavaScript 取用建議
取用建議的程式碼是由用戶端網頁的某些 JavaScript 事件所觸發。建議回應包含建議項目識別碼、其名稱及評等。最好只有在以清單顯示建議的項目時，才使用這個選項 – 較複雜的處理 (例如，新增項目的中繼資料) 應該在伺服器端整合完成。

###4.1 取用建議
若要取用建議，您必須將必要的 JavaScript 程式碼納入頁面中，然後呼叫 AzureMLRecommendationsStart。請參閱第 2 節。

若要取用一或多個項目的建議，您必須呼叫以下方法： AzureMLRecommendationsGetI2IRecommendation。

參數：* items (字串的陣列) – 要取得建議的一或多個項目。如果您取用 Fbt 組件，那麼您在這裡只能設定項目。* numberOfResults (int) – 必要結果的數目。* includeMetadata (布林值，選擇性) – 如果設為 ‘true’，代表結果中必須填入中繼資料欄位。* 處理函式 – 將會處理所傳回建議的函式。資料是以陣列形式傳回：* Item – 項目的唯一識別碼 * name – 項目名稱 (如果存在於目錄中) * rating – 建議評等 * metadata – 代表項目中繼資料的字串

範例：下列程式碼要求項目 "64f6eb0d-947a-4c18-a16c-888da9e228ba" (而不指定 includeMetadata – 即暗示不需要任何中繼資料) 提供 8 個建議，然後將結果串連到緩衝區。

		<script>
 			var reco = AzureMLRecommendationsGetI2IRecommendation(["64f6eb0d-947a-4c18-a16c-888da9e228ba"], 8, false, function (reco) {
 				var buff = "";
 				for (var ii = 0; ii < reco.length; ii++) {
   					buff += reco[ii].item + "," + reco[ii].name + "," + reco[ii].rating + "\n";
 				}
 				alert(buff);
			});
		</script>


[1]: ./media/machine-learning-recommendation-api-javascript-integration/Drawing1.png
[2]: ./media/machine-learning-recommendation-api-javascript-integration/Drawing2.png
[3]: ./media/machine-learning-recommendation-api-javascript-integration/Drawing3.png
 

<!---HONumber=July15_HO2-->