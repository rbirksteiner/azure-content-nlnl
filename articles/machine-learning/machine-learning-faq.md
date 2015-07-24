<properties 
	pageTitle="Azure Machine Learning 常見問題集 | Microsoft Azure" 
	description="Azure Machine Learning 簡介：常見問題集，涵蓋計費、功能，以及適用於簡化預測性模型化之雲端服務的限制。" 
	keywords="machine learning introduction,predictive modeling,what is machine learning"
	services="machine-learning" 
	documentationCenter="" 
	authors="pablissima" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/07/2015" 
	ms.author="paulettm"/>

#Azure Machine Learning 常見問題集 (FAQ)：計費、功能、限制及支援

此常見問題集針對 Azure Machine Learning 問題提供解答，Azure Machine Learning 是適合透過 Web 服務執行預測性模型化和運作方案的雲端服務。這個常見問題集涵蓋使用服務的相關問題，包括計費模型、功能、限制及支援。
 
##一般問題

**什麼是 Azure Machine Learning 服務？**
 
Azure Machine Learning 是受到完整管理的服務，可用來建立、測試、操作及管理雲端中的預測分析方案。僅使用瀏覽器，您即可以登入、上傳資料，以及立即開始機器學習實驗。拖放式預測性模型化、大型模組和用以啟動範本的程式庫，讓您得以簡便而快速地執行一般機器學習工作。如需詳細資訊，請參閱 [Azure Machine Learning 服務概觀](/services/machine-learning/)。如需涵蓋重要術語和概念的機器學習簡介，請參閱 [Azure Machine Learning 簡介](machine-learning-what-is-machine-learning.md)。


[AZURE.INCLUDE [電腦-學習-免費-試用](../../includes/machine-learning-free-trial.md)]
 
**什麼是 Machine Learning Studio？**

Machine Learning Studio 是您透過網頁瀏覽器存取的工作區環境。Machine Learning Studio 包含一組具有視覺化結構介面的模組，可讓您以實驗的形式建置端對端資料科學工作流程。

如需 Machine Learning Studio 的詳細資訊，請參閱[什麼是 Machine Learning Studio](machine-learning-what-is-ml-studio.md)

**什麼是 Machine Learning API 服務？**

Machine Learning API 服務可讓您將 Machine Learning Studio 中內建的預測模型部署為可延展、容錯的 Web 服務。Machine Learning API 服務所建立的 Web 服務是 REST API，此類 API 提供的介面可用於外部應用程式與您的預測分析模型之間的通訊。

如需詳細資訊，請參閱[連線到 Machine Learning Web 服務](machine-learning-connect-to-azure-machine-learning-web-service.md)。


##計費問題

**機器學習服務如何計費？**

如需計費和定價資訊，請參閱[機器學習服務定價](http://azure.microsoft.com/pricing/details/machine-learning/)。

**機器學習服務是否有免費試用版？**

 註冊 Azure 免費試用版後，您可以試用任何 Azure 服務一個月。若要深入了解 Azure 免費試用版，請造訪 [Azure 免費試用版 FAQ](/pricing/free-trial-faq/)。

## Machine Learning Studio 問題

###建立實驗	
**實驗圖形有版本控制或 Git 整合嗎？**

沒有，不過每次執行實驗便會保留該版本的圖形，無法由其他使用者修改。

###匯入和匯出機器學習服務的資料
**機器學習服務支援何種資料來源？**

資料可透過兩種方式載入 Machine Learning Studio 中：以資料集的形式上傳本機檔案，或使用讀取器模組匯入資料。可以上傳本機檔案，方法是在 Machine Learning Studio 中新增資料集。若要深入了解支援的檔案格式，請參閱[將訓練資料匯入 Azure Machine Learning Studio](machine-learning-import-data.md)。


####<a id="ModuleLimit"></a>我的模組適用多大的資料集？

Machine Learning Studio 中的模組對常見使用案例支援最多 10 GB 的密集數值資料的資料集。如果模組接受一個以上的輸入，10 GB 是所有輸入的大小總計。您也可以取樣較大型資料集，方法是透過 Hive 或 Azure SQL Database 查詢，或在擷取前透過依計數學習前置處理。

下列資料類型可以在功能正規化期間擴充為較大型資料集，並限制在小於 10 GB：

- 疏鬆
- 類別
- 字串
- 二進位資料

下列模組限制在小於 10 GB 的資料集：

- Recommender 模組
- SMOTE 模組
- 指令碼模組：R、Python SQL
- 輸出資料大小可以大於輸入資料大小的模組，例如聯結或功能雜湊。
- 當數字的反覆項目非常大時的交叉驗證、掃掠參數、序數迴歸和一對多的多類別。

針對大於幾 GB 的資料集，您應該將資料上傳至 Azure 儲存體或 Azure SQL Database，或是使用 HDInsight，而不要直接從本機檔案上傳。


####<a id="UploadLimit"></a>資料上傳的限制為何？
針對大於 2 GB 的資料集，請將資料上傳至 Azure 儲存體或 Azure SQL Database，或是使用 HDInsight，而不要直接從本機檔案上傳。

**可以從 Amazon S3 讀取資料嗎？**

如果您有少量的資料，而且想要透過 HTTP URL 公開，則您可以使用[讀取器][reader]模組。若為任何較大量的資料，請先傳送至 Azure 儲存體，然後使用[讀取器][reader]模組將它帶入實驗中。
<!--
<SEE CLOUD DS PROCESS>
-->

**有內建的影像輸入功能嗎？**

您可以在[映像讀取器][image-reader]參考中了解影像輸入功能。

###模組 

**我在尋找的演算法、資料來源、資料格式、資料轉換作業不在 Azure ML Studio 中，我有哪些選擇？**

您可以瀏覽[使用者意見反應論壇](http://go.microsoft.com/fwlink/?LinkId=404231)，以檢視我們所追蹤的功能要求。如果您要找的功能已有人要求，請投票給該要求。如果您要找的功能不存在，請建立新要求。您也可以在此論壇中檢視您的要求狀態。我們會密切追蹤此清單，並經常更新功能可用性的狀態。除了利用對 R 和 Python 的內建支援，需要時也可以建立自訂轉換。


**是否可將我現有的程式碼放入 ML Studio 中？**

是的，您可以在 ML Studio 中放入現有的 R 程式碼，並使用 Azure Machine Learning 提供的學習器在相同的實驗中加以執行，然後透過 Azure Machine Learning 發佈為 Web 服務。請參閱[透過 R 擴展您的實驗](machine-learning-extend-your-experiment-with-r.md)。

**可使用 [PMML](http://en.wikipedia.org/wiki/Predictive_Model_Markup_Language) 之類來定義模型嗎？**

不，不支援，但是自訂 R 和 Python 程式碼可以用來定義模組。


###資料處理 
**有能力在實驗內將資料以互動方式視覺化嗎 (R 視覺效果外)？**

按一下模組的輸出，您即可以將資料視覺化，並取得統計資料。

**在瀏覽器中預覽結果或資料時，資料列和資料行的數目很有限，為什麼？**

因為資料會傳輸至瀏覽器，而且可能很大，限制資料大小是為了防止減緩 ML Studio。最好是下載資料/結果，並使用 Excel 或其他工具，將整個資料視覺化。

###演算法
**Machine Learning Studio 中支援哪些現有的演算法？**

Machine Learning Studio 提供頂級演算法，例如 Scalable Boosted Decision 樹、Bayesian Recommendation 系統、Deep Neural Networks 和 Decision Jungles (由 Microsoft Research 開發)。此外也包含可調整的開放原始碼機器學習封裝，例如 Vowpal Wabbit。Machine Learning Studio 支援多類別與二進位分類、迴歸和叢集的機器學習演算法。請參閱[機器學習服務單元][machine-learning-modules]的完整清單。

**您會自動建議我的資料適用的機器學習演算法嗎？**

不會，但在 Machine Learning Studio 中有數種方法可以比較每一個演算法的結果，以決定適合您的問題的演算法。

**針對從提供的演算法中挑選，是否有任何準則？** 請參閱[如何選擇演算法](machine-learning-algorithm-choice.md)。

**提供的演算法是以 R 或 Python 撰寫？**

否，這些演算法大部分是以編譯的語言的撰寫，以提供更高的效能。

**有提供演算法之任何詳細資料嗎？**

文件提供一些演算法的相關資訊，並說明提供的微調參數，以針對您的使用最佳化演算法。

**線上學習是否有任何支援？**

否，目前只支援以程式設計方式進行重新訓練。

**可以使用內建的模組視覺化類神經網路模型的層級嗎？**

編號

**可以以 C# 或其他語言建立自己的模組嗎？**

目前只能在 R 中建立新的自訂模組。

###R 模組 
**Machine Learning Studio 中可使用什麼 R 套件？**

Machine Learning Studio 目前支援 400 個以上的 R 封裝，且數量仍在持續增加。若要了解如何取得支援的 R 封裝的清單，請參閱 [透過 R 擴展您的實驗](machine-learning-extend-your-experiment-with-r.md)。如果您要的封裝不在此清單中，請在[使用者意見反映論壇](http://go.microsoft.com/fwlink/?LinkId=404231)中提供封裝名稱。

**是否可以建置自訂的 R 模組？**

是，如需詳細資訊，請參閱[在 Azure Machine Learning 中撰寫自訂 R 模組](machine-learning-custom-r-modules.md)。

**是否有 R 適用的 REPL 環境？**

否，Studio 中沒有 R 適用的 REPL 環境。

###Python 模組 

**是否可以建置自訂的 Python 模組？**

目前不行，但是利用標準的 Python 模組或一組模組即可以達成相同的結果。

**是否有 Python 適用的 REPL 環境？**

否，Studio 中沒有 Python 適用的 REPL 環境。

## Web 服務
###以程式設計方式重新訓練模型

**如何以程式設計方式重新訓練 AzureML 模型？** 使用重新訓練 API。[這裡](https://azuremlretrain.codeplex.com/)提供範例程式碼。

###建立

**可以在本機或在沒有網際網路連線的應用程式中部署模型嗎？** 編號


**所有 Web 服務是否有預期的基準延遲？**

請參閱 [Azure 訂用帳戶限制](../azure-subscription-service-limits.md)

###使用
**何時該以「批次執行服務」的形式執行預測模型？何時該以要求回應服務的形式執行？**

「要求回應服務 (RRS)」是低延遲性的高階 Web 服務，可用來為從實驗環境建立並發佈的無狀態模型提供介面。「批次執行服務 (BES)」是可為一批資料記錄進行非同步評分的服務。BES 的輸入類似於 RRS 中使用的資料輸入。主要差異在於，BES 會從多種來源讀取記錄區塊，例如 Blob 服務、Azure 中的資料表服務、Azure SQL Database、HDInsight (Hive 查詢) 和 HTTP 來源。如需詳細資訊，請參閱[如何使用 Machine Learning Web 服務](machine-learning-consume-web-services.md)。

**如何為已部署的 Web 服務更新模型？**

要為已部署的服務更新預測模型，只需修改並重新執行用來撰寫和儲存已訓練模型的實驗即可。在您有新版的已訓練模型後，ML Studio 會詢問您是否要更新預備 Web 服務。在更新套用至預備 Web 服務後，相同的更新也將可供您套用至實際執行 Web 服務。如需有關如何更新已部署的 Web 服務的詳細資訊，請參閱[發佈 Machine Learning Web 服務](machine-learning-publish-a-machine-learning-web-service.md)。


**如何監控部署在實際執行環境中的 Web 服務？**

在預測模型放入實際執行環境中之後，您可以從 Azure 入口網站加以監控。每個已部署的服務都有其本身的儀表板，您可以在此處檢視該服務的監控資訊。

**是否有哪個位置，可讓我查看我的 RRS/BES 輸出？**

是的，您必須提供 Blob 儲存體位置，RRS/BES 輸出將會放在該處。



##延展性 

**什麼是 Web 服務的延展性？**

目前，最大值是每個端點 20 個並行要求，不過它可以調整為 80 個端點。如果我們使用每個資源 (300 個背景工作)，這會轉譯為 4,800 個並行要求。


**R 作業會分散於節點嗎？**

編號


**我可以訓練多少資料？**

Machine Learning Studio 中的模組對常見使用案例支援最多 10 GB 的密集數值資料的資料集。如果模組接受一個以上的輸入，10 GB 是所有輸入的大小總計。您也可以取樣較大型資料集，方法是透過 Hive 或 Azure SQL Database 查詢，或在擷取前透過依計數學習前置處理。

下列資料類型可以在功能正規化期間擴充為較大型資料集，並限制在小於 10 GB：

- 疏鬆
- 類別
- 字串
- 二進位資料

下列模組限制在小於 10 GB 的資料集：

- Recommender 模組
- SMOTE 模組
- 指令碼模組：R、Python SQL
- 輸出資料大小可以大於輸入資料大小的模組，例如聯結或功能雜湊。
- 當數字的反覆項目非常大時的交叉驗證、掃掠參數、序數迴歸和一對多的多類別。

針對大於幾 GB 的資料集，您應該將資料上傳至 Azure 儲存體或 Azure SQL Database，或是使用 HDInsight，而不要直接從本機檔案上傳。


**是否有任何向量大小限制嗎？**

每個資料列和資料行的限制在 .NET 的最大 Int 限制：2,147,483,647。

**可調整正在執行它的 VM 大小嗎？**

編號

##安全性和可用性 

**根據預設，哪些人可以存取部署在實際執行環境之 Web 服務的 HTTP 端點？ 如何限制對此端點的存取？**

在預測模型放入實際執行環境中之後，Azure 入口網站即會列出已部署之 Web 服務的 URL。預備服務 URL 可從 Web 服務區段下的 Machine Learning Studio 環境存取，而實際執行服務 URL 可從 Azure 入口網站的 [機器學習服務] 區段下存取。預備和實際執行 Web 服務的存取金鑰，分別可從 Machine Learning Studio 中的 Web 服務儀表板和 Azure 入口網站環境取得。必須要有存取金鑰，才能呼叫實際執行和預備環境中的 Web 服務。如需詳細資訊，請參閱[連線到 Machine Learning Web 服務](machine-learning-connect-to-azure-machine-learning-web-service.md)。

**如果找不到我的儲存體帳戶，會發生什麼情況？**

Machine Learning Studio 依賴使用者提供的 Azure 儲存體帳戶，才能在執行工作流程時儲存中繼資料。此儲存體帳戶是在建立工作區時提供給 Machine Learning Studio。建立工作區之後，如果刪除儲存體帳戶，而且不再能找到，工作區會停止運作，並且該工作區中的所有實驗將會失敗。
 
如果您不小心刪除儲存體帳戶，從中復原的唯一辦法是使用與刪除的那個完全相同的區域中的相同名稱重新建立儲存體帳戶。在那之後，請重新同步處理存取金鑰。
 

**如果我的儲存體帳戶存取金鑰未同步，會發生什麼情況？** Machine Learning Studio 依賴使用者提供的 Azure 儲存體帳戶，才能在執行工作流程時儲存中繼資料。此儲存體帳戶是在建立工作區時提供給 Machine Learning Studio，並且存取金鑰會與該工作區相關聯。建立工作區之後，如果變更存取金鑰，該工作區不再能存取儲存體帳戶，工作區會停止運作，並且該工作區中的所有實驗將會失敗。

如果您變更了儲存體帳戶存取金鑰，請確保在 Azure 入口網站中的工作區設定中重新同步處理存取金鑰


##Azure Marketplace 

請參閱[在 Machine Learning Marketplace 中發佈和使用 App 的常見問題集](machine-learning-marketplace-faq.md)

##支援和訓練 

**哪裡可以取得 Azure ML 的訓練？**

[Azure Machine Learning 文件中心](/services/machine-learning/)包含視訊教學課程和操作方式指南。這些逐步操作指南提供了服務的簡介，並且說明匯入資料、清除資料、建置預測模型以及使用 Azure ML 將其部署在實際執行環境中的資料科學生命週期。

我們將持續更新機器學習中心的內容。您可以在[使用者意見反應論壇](https://windowsazure.uservoice.com/forums/257792-machine-learning)中，提交關於 Machine Learning Center 的其他學習內容要求。

您也可以在 [Microsoft Virtual Academy](http://www.microsoftvirtualacademy.com/training-courses/getting-started-with-microsoft-azure-machine-learning) 尋找訓練

**如何取得 Azure Machine Learning 的支援？**

若要取得 Azure Machine Learning 的技術支援，請移至 [[Azure 支援](/support/options/)]，並選取 [**機器學習服務**]。

Azure Machine Learning 在 MSDN 上也設有社群論壇，可供您詢問 Azure ML 的相關問題。此論壇由 Azure ML 團隊控管。請瀏覽 [Azure 論壇](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning)。


<!-- Module References -->
[image-reader]: https://msdn.microsoft.com/library/azure/893f8c57-1d36-456d-a47b-d29ae67f5d84/
[join]: https://msdn.microsoft.com/library/azure/124865f7-e901-4656-adac-f4cb08248099/
[machine-learning-modules]: https://msdn.microsoft.com/library/azure/6d9e2516-1343-4859-a3dc-9673ccec9edc/
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
 

<!---HONumber=July15_HO1-->