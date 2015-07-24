<properties 
	pageTitle="將機器學習訓練實驗轉換成評分實驗 | Microsoft Azure" 
	description="如何將用於預測性分析模型的 Machine Learning 訓練實驗轉換成可以當做 Web 服務發行的評分實驗。" 
	services="machine-learning" 
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/21/2015" 
	ms.author="garye"/>

#將 Machine Learning 訓練實驗轉換成評分實驗

Azure Machine Learning 可讓您建置、測試以及部署預測性分析解決方案。

一旦您建立並反覆測試「訓練實驗」以訓練您的預測性分析模型，並做好使用該模型為新資料評分的準備之後，您必須準備並簡化用於評分的實驗。接著，您可以將此「評分實驗」發行為 Azure Web 服務，讓使用者可將資料傳送至您的模型，並收到您模型的預測。

轉換為評分實驗之後，您就準備好定型模型，可以當做評分 Web 服務發行。Web 服務的使用者會將輸入資料傳送到您的模型，然後您的模型就會傳送回預測結果。因此，當您轉換為評分實驗時，您必須記住您預期其他人使用您模型的方式。

[AZURE.INCLUDE [電腦-學習-免費-試用](../../includes/machine-learning-free-trial.md)]

將訓練實驗轉換為評分實驗包含三個步驟：

1.	儲存您已訓練過的機器學習模型，然後將機器學習演算法和[訓練模型][train-model]模組替換為您所儲存的定型模型。
2.	將訓練調整為僅適用於評分所需的模組。訓練實驗包含訓練所需，但一旦模型受過訓練並準備好用於評分之後，就不再需要的多個模組。
3.	定義實驗中您將接受來自 Web 服務使用者之資料的所在位置，以及將傳回的資料。

##建立評分實驗按鈕

實驗畫布底部的 [建立評分實驗] 按鈕會為您執行將訓練實驗轉換為評分實驗的三個步驟：

1.	它會將定型模型當做模組，儲存在模組調色盤的 [定型模型] 區段 (實驗畫布的左側)，然後將機器學習演算法和[訓練模型][train-model]模組替換為所儲存的定型模型。 
2.	它會移除顯然不需要的模組。在我們的範例中，這包括[分割][split]、第二個[評分模型][score-model]以及[評估模型][evaluate-model]模組。
3.	它會建立 Web 服務輸入和輸出模組，並將它們加入到實驗的預設位置中。

例如，以下實驗會使用範例人口普查資料，訓練二元推進式決策樹模型：

![訓練實驗][figure1]

這項實驗中的模組基本上執行是四個不同的功能：

![模組功能][figure2]

當您將這個訓練實驗轉換為評分實驗時，就不再需要其中部分模組或它們有不同的用途：

- **資料** - 在評分期間，不會使用此範例資料集中的資料，因為 Web 服務的使用者會提供要評分的資料。不過，定型模型會使用此資料集中的中繼資料，例如，資料類型。因此，您必須將資料集保留在評分實驗中，讓它能夠提供這項中繼資料。

- **準備** - 根據要提交用於評分的資料而定，這些模組不一定需要處理傳入的資料。

	例如，在此範例中，範例資料集可能會有遺漏的值，而且它包含培訓模型不需要的資料行。因此，**清除遺漏的資料**模組會用於處理遺漏的值，而[專案資料行][project-columns]模組則用於排除資料流程中的那些額外資料行。如果您知道透過 Web 服務提交用於評分的資料不會有遺漏的值，則您可以移除**清除遺漏的資料**模組。不過，由於[專案資料行][project-columns]模組有助於定義一組要評分的功能，因此必須保留該模組。

- **訓練** - 一旦成功訓練模型之後，您就可以將它儲存為單一定型模型模組。接著，您要將這些個別的模組取代為所儲存的定型模型。

- **評分** - 在此範例中，「分割」模組是用來將資料流分割成一組測試資料和訓練資料。在評分實驗中不需要這個模組，因此可以移除。同樣地，第二個[評分模型][score-model]模組和[評估模型][evaluate-model]模組會用來比較測試資料的結果，因此在評分實驗中也不需要這些模組。但其餘的[評分模型][score-model]模組需要透過 Web 服務傳回評分結果。

以下是按一下 [建立評分實驗] 之後，我們的範例外觀：

![轉換的評分實驗][figure3]

這可能足以準備您的實驗，以當做 Web 服務發行。不過，您可能會想要執行一些您的實驗專屬的額外工作。

###調整輸入和輸出模組

在訓練實驗中，您使用一組訓練資料進行一些處理，以取得 Machine Learning 演算法所需格式的資料。如果您預期透過 Web 服務收到的資料不會需要這項處理，可以將 **Web 服務輸入模組**移到實驗中的另一個節點。

例如，**建立評分實驗**預設會將 **Web 服務輸入**模組放在資料流程的頂端，如下圖所示。不過，如果輸入資料不需要這項處理，則您可以手動將 **Web 服務輸入**放在資料處理模組之後：

![移動 Web 服務輸入][figure4]

透過 Web 服務提供的輸入資料現在會直接傳入 [評分模型] 模組，而不需要任何前置處理。

同樣地，**建立評分實驗**會將「Web 服務輸出」模組放在資料流程的底部。在此範例中，Web 服務會將[評分模型][score-model]模組的輸出傳回給使用者，其中包括完整的輸入資料向量以及評分結果。

不過，如果您想傳回其他內容，例如，僅傳回評分結果但不傳回輸入資料的整個向量，則您可以插入[專案資料行][project-columns]模組，以排除評分結果以外的所有資料行。接著，您要將 **Web 服務輸出**模組移到[專案資料行][project-columns]模組的輸出：

![移動 Web 服務輸出][figure5]

###新增或移除其他資料處理模組

如果在您的實驗中還有您知道在評分期間不需要的模組，則可以移除這些模組。例如，由於我們已經將 **Web 服務輸入**模組移到資料處理模組之後的某個點，因此我們可以從評分實驗中移除[清除遺漏的資料][clean-missing-data]模組。

我們的評分實驗現在看起來像這樣：

![移除額外的模組][figure6]

###新增選用的 Web 服務參數

在某些情況下，您可能需要讓 Web 服務的使用者變更存取服務時的模組行為。*Web 服務參數*可讓您執行這項操作。

常見的範例是設定[讀取者][reader]模組，讓已發行 Web 服務的使用者可以在存取服務時，指定不同的資料來源。或者，設定[寫入者][writer]模組，以便指定不同的目的地。

您可以定義 Web 服務參數，並使其與一個或多個模組參數產生關聯，而且您可以指定它們是必要還是選用參數。接著，Web 服務的使用者可以在服務遭到存取時，提供這些參數的值，並據此修改模組動作。

如需關於 Web 服務參數的詳細資訊，請參閱[使用 Azure Machine Learning Web 服務參數][webserviceparameters]。

[webserviceparameters]: machine-learning-web-service-parameters.md


##將評分實驗當做 Web 服務發行

既然已經為評分實驗做好充分的準備，您可以將它當做 Azure Web 服務發佈。使用者可以使用 Web 服務，將料傳送到您的模型，模型就會傳回其預測。

如需完整發行流程的詳細資訊，請參閱[發行 Azure Machine Learning Web 服務][publish]。

[publish]: machine-learning-publish-a-machine-learning-web-service.md


<!-- Images -->
[figure1]: ./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure1.png
[figure2]: ./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure2.png
[figure3]: ./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure3.png
[figure4]: ./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure4.png
[figure5]: ./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure5.png
[figure6]: ./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure6.png


<!-- Module References -->
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[project-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
 

<!---HONumber=July15_HO2-->