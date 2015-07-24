<properties 
	pageTitle="以程式設計方式重新定型機器學習服務模型 | Microsoft Azure" 
	description="了解如何在 Azure Machine Learning 中以程式設計方式重新定型模型，以及使用新定型的模型來更新 Web 服務。" 
	services="machine-learning" 
	documentationCenter="" 
	authors="raymondlaghaeian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services" 
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/29/2015"
	ms.author="raymondl;garye"/>


#以程式設計方式重新定型機器學習服務模型  
 
在 Azure Machine Learning 中進行機器學習服務模型的實作程序時，模型需要定型並儲存後，才能用來建立評分 Web 服務。接著才能在網站、儀表板及行動應用程式取用 Web 服務。

您通常必須將使用新資料在第一個步驟建立的模型重新定型。這在以前只能透過 Azure ML UI 進行，但隨著 Programmatic Retraining API 功能引進後，您現在能夠以程式設計方式使用重新定型 API 來重新定型模型並更新 Web 服務，以使用新定型的模型。

本文說明上述程序並示範如何使用重新定型 API。

[AZURE.INCLUDE [電腦-學習-免費-試用](../../includes/machine-learning-free-trial.md)]
 

##為何要重新定型：定義問題  
進行 ML 定型程序時，會使用一組資料來將模型定型。在有新資料可用的情況下，或當 API 的取用者有自己要定型模型的資料時，或是需要篩選資料並以資料子集定型模型時，就必須重新定型模型。

在這些情況下，程式設計 API 可方便您或 API 取用者建立能夠單次或定期使用自己的資料重新定型模型的用戶端。接著可以評估重新定型的結果，然後更新 Web 服務 API 以使用新定型的模型。

##如何重新定型：端對端程序  
首先，此程序包含下列元件：發佈為 Web 服務的定型實驗與評分實驗。若要啟用已定型模型的重新定型功能，也必須利用定型模型的輸出將定型實驗發佈為 Web 服務。這樣做可讓 API 存取模型進行重新定型。設定重新定型的程序包含下列步驟：

![][1]
 
圖 1：重新定型程序概觀

1. *建立定型實驗* 針對這個範例，我們將使用 Azure ML 範例實驗當中的實驗「範例 5 (定型、測試、評估二進位分類：成人資料集)」。如您在下面所見，我移除一些模組來簡化範例。我也將實驗命名為「普查模型」。

 	![][2]

	上述各項準備就緒後，我們可以按一下畫面底部的 [執行] 來執行這項實驗。  
2. *建立評分實驗並發佈為 Web 服務*  
	
	![][3]

	實驗執行完成後，可以按一下 [建立評分實驗]。這樣會建立評分實驗，將模型儲存為定型模型，並新增 Web 服務輸入與輸出模型。接下來，按一下 [執行]。

	在實驗執行完成後，按一下 [發佈 Web 服務]，就會將評分實驗發佈為 Web 服務，並建立預設端點。這個 Web 服務中的定型模式可以更新，如下所示。此端點的預設值將隨即顯示在畫面上。  
3. *將定型實驗發佈為 Web 服務* 若要將定型模型重新定型，我們必須將上述步驟 1 中建立的定型實驗發佈為 Web 服務。這個 Web 服務需要將 Web 服務輸出模組連接到[定型模型][train-model]模組，才能產生新的定型模組。按一下左窗格中的 [實驗] 圖示，然後按一下稱為 [普查模式] 的實驗，以返回定型實驗。  

	接下來，將一個 Web 服務輸入與兩個 Web 服務輸出模組新增至工作流程。定型模型的 Web 服務輸出將會提供新的定型模型。連接到評估模型的輸出將傳回該模組的評估模型輸出。

	現在可以按一下 [執行]。在實驗完成執行後，產生的工作流程應該如下所示：
 
	![][4]

	接下來，按一下 [發佈 Web 服務] 按鈕，然後按一下 [是]。這樣就會將定型實驗發佈為可產生定型模型與模型評估結果的 Web 服務。隨即顯示的 Web 服務儀表板包含可用於批次執行的 API 索引鍵與 API 說明頁面。請注意，只有批次執行方法能夠用來建立定型模型。  
4. *新增端點*我們在步驟 2 發佈的評分 Web 服務是以預設端點建立。預設端點會與原始定型和計分實驗保持同步，因此無法取代預設端點的定型模型。若要建立可更新的端點，請造訪 Azure 入口網站，然後按一下 [新增端點] (如需詳細資訊，請參閱[這裡](machine-learning-create-endpoint.md))。	

5. *以新資料與 BES 重新定型模型* 為了呼叫重新定型 API，我們建立在 Visual Studio 中建立新的 C# 主控台應用程式 ([新增]->[專案]->[Windows 桌面]->[主控台應用程式])。

	接下來，從定型 Web 服務的 API 說明頁面中複製可用於批次執行的範例 C# 程式碼 (在上述的步驟 3 建立)，然後貼入 Program.cs 檔案，確保命名空間保持不變。

	請注意，範例程式碼會有註解，指出需要更新的程式碼部分。此外，當指定要求承載中的 "output1" 位置時，"RelativeLocation" 的副檔名必須變更為 ".ileaner"，如下列輸出所示：{Global Parameters ... { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=mystorageacct;AccountKey=Dx9WbMIThAvXRQWap/aLnxT9LV5txxw==", "RelativeLocation": "mycontainer/output1results.ilearner"}}。

	1. 提供 Azure 儲存體資訊：BES 的範例程式碼將從本機磁碟機上傳一個檔案 (例如 “C:\\temp\\CensusIpnput.csv”) 至 Azure 儲存體、加以處理後，再將結果寫回 Azure 儲存體。  

		若要完成上述動作，您必須從 Azure 管理入口網站擷取您儲存體帳戶的儲存體帳戶名稱、金鑰以及容器資訊，然後更新這裡的程式碼。您還必須確保程式碼中指定的位置有輸入檔案可用。

		我們已利用兩個輸出來設定這個定型實驗，因此結果將包含這兩者的儲存位置資訊，如下所示。“output1” 是定型模型的輸出，而 “output2” 是評估模型的輸出。另請注意，定型模型 (Output1) 的輸出副檔名為 ".ileaner"，而不是 ".csv"。

		![][6]
 
6. *評估重新定型結果* 使用 BaseLocation、RelativeLocaiton 及 SasBlobToken 的組合，在瀏覽器網址列貼入完整的 URL，我們能從上述 “output2” 的輸出結果當中，看到重新定型模式的效能結果。

	這將告訴我們新的定型模型的執行效能是否良好並足以取代現有模型。

7. *更新所新增端點的定型模型* 我們必須針對在上述步驟 4 建立的評分端點更新其定型模型後，才算是完成程序。

	上述的 BES 輸出顯示的資訊是 “output1” 的重新定型結果，其中包含重新定型模式的位置資訊。現在我們必須使用這個定型模式，並更新評分端點。(在上述的步驟 4 建立)

	![][7]
  
	您可以在端點儀表板上看到這個呼叫的 “apiKey” 與 “endpointUrl”。

	這個呼叫成功時，大約會在 15 秒內使用重新定型的模式來啟動新端點。

##摘要  
使用重新定型 API，我們就可以更新預測 Web 服務的定型模型，而能定期使用新資料重新定型模型，或向客戶發佈模型，讓他們使用自己的資料重新定型模型。

[1]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE01.png
[2]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE02.png
[3]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE03.png
[4]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE04.png
[5]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE05.png
[6]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE06.png
[7]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE07.png


<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
 

<!---HONumber=July15_HO2-->