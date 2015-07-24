<properties 
   pageTitle="BizTalk 規則" 
   description="本主題說明 BizTalk 規則的功能，並提供其使用方式的指示" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="anuragdalmia" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="02/27/2015"
   ms.author="andalmia"/>

#BizTalk 規則 

商務規則會封裝可控制商務程序的原則和決策。這些原則可能正式定義於程序手冊、合約或協議中，或內化成為員工本身的知識或專業。這些原則是動態的，而且有可能隨著時間因為商務計劃、法規或其他原因的變動而有所變更。

以傳統的程式設計語言實作這些原則需要大量的時間和協調工作，且無法讓非程式設計人員參與商務原則的建立和維護。BizTalk 商務規則提供快速實作這些原則和解離其餘商務程序的方式。這可讓您對商務原則進行必要的變更，同時不影響到其餘的商務程序。

##為何要使用規則

在商務程序中使用 BizTalk 商務規則有三大原因：

* 將商務邏輯從應用程式程式碼中解離
- 讓商務分析人員更充分掌控商務邏輯管理
+ 商務邏輯變更可更快轉移至生產環境

#規則概念

##詞彙

用來定義規則條件和動作的術語，通常依領域或業界特定專門用語來表示。例如，電子郵件使用者會以訊息「接收自」和訊息「接收自從」的用語來撰寫規則，而保險業者分析人員則會以「風險因子」和「承保金額」的用語來撰寫規則。 此領域特定術語的基礎，是實作規則條件和規則動作的技術成品 (物件、資料庫資料表和 XML 文件)。設計的詞彙用來消弭商務語意與實作之間的差距。

例如，核准狀態的資料繫結可能指向特定資料庫中某個資料列的特定資料行，以 SQL 查詢的形式表示。與其在規則中插入這種複雜的表示法，您可能會選擇以「狀態」的易記名稱建立與該資料繫結相關聯的詞彙定義。 接下來，您可以在任何數量的規則包含「狀態」，且規則引擎可從資料表中擷取對應的資料。_詞彙_是由規則條件和動作中使用之計算物件的易記名稱所組成的定義集合。詞彙定義讓規則更易於給特定商務領域中的人員閱讀、了解與共用。

##規則

商務規則是管理商務程序執行的宣告式陳述式。規則由條件和動作組成。條件會受到評估，如果評估為 true，規則引擎即會指出一或多個動作。商務規則架構中的規則會使用下列格式來定義：

_IF_ _條件_ _THEN_ _動作_

請思考下列範例：

*IF 金額小於或等於可用資金* *THEN 執行交易並列印收據*

此規則會將商務邏輯 (以比較兩個貨幣值的形式) 套用到資料或事實 (以交易數量與可用資金的形式)，以判斷是否要進行交易。您可使用「商務規則」來建立、修改及部署商務規則。或者，您可以透過程式設計方式來執行前述工作。

###條件

條件是一個 True/False (布林值) 運算式，其中包含一或多個述詞。在我們的範例中，述詞小於或等於會套用至金額和可用資金。此條件將一律評估為 true 或 false。述詞可以結合邏輯運算子 AND、OR 及 NOT，來形成邏輯運算式，這個運算式可能相當大，但一律會被評估為 True 或 False。

###動作

動作是條件評估的功能結果。如果符合某個規則條件，會起始對應的一或多個動作。在我們的範例中，「執行交易」和「列印收據」就是在條件 (在此案例中為「若金額小於或等於可用資金」) 為 True 時執行的動作，而且也只有在此時才會執行。動作藉由對 XML 文件執行設定作業，顯示於商務規則架構中。

##原則

原則是規則的邏輯群組。您可以撰寫原則、將它儲存、測試，並且在您滿意結果時，在生產環境中加以使用。

###原則撰寫

您可以在規則入口網站中撰寫原則。原則可以包含任意數量的規則集，但通常您從與將會使用原則的應用程式內容中的特定商務領域相關的規則，來撰寫原則。

###原則測試
您可以先有效執行一輪原則的測試，再將其使用於生產環境中。規則入口網站可讓您提供輸入給原則、執行該原則，並檢視其輸出。輸出包括記錄、規則執行、條件評估與產生的輸出。

##範例案例 - 保險理賠
讓我們以範例案例，逐步撰寫相同商務邏輯。

![替代文字][1]

在非常簡單的保險理賠案例中，Claimant 送出他保險理賠 (透過像是網站、手機應用程式等任何用戶端)。此理賠要求傳送至企業的理賠處理單位，並根據處理的結果，宣告可能通過核准、遭到拒絕或送交進一步的手動處理。我們的案例中，理賠處理單位將是包含系統之商務邏輯的單位。深入檢視此單位時，我們可以發現下列事項：

![替代文字][2]
 
現在，我們將使用商務規則實作此商務邏輯。


##建立規則 API 應用程式


1. 登入 Azure 入口網站並進入首頁。 
1. 按一下 [新增] -> [Azure Marketplace] -> [API 應用程式] -> [BizTalk 規則] -> [建立] ![替代文字][3]
1. 在開啟的新分頁中，輸入下列資訊：  
	1. 名稱 - 提供規則 API 應用程式的名稱
	1. 應用程式主控計劃 – 選取或建立 Web 主控計劃
	1. 定價層 - 選擇您要讓此應用程式屬於哪個定價層
	1. 資源群組 – 選取或建立應用程式應所屬的資源群組
	1. 位置 – 選擇您要部署應用程式的地理位置。
4.	按一下 [建立]。您的 BizTalk 規則 API 應用程式在幾分鐘內就會建立。

##建立詞彙
建立 BizTalk 規則 API 應用程式之後，下一個步驟是建立詞彙。預期為開發人員是常見執行此工作的人員。若要這麼做，請遵循下列步驟：


1. 藉由 [瀏覽] -> [API 應用程式] -> [<Your Rules API App>] 瀏覽至已建立的 API 應用程式。這應會使您進入 [規則 API 應用程式儀表板]，如下所示：
 
   ![替代文字][4]

2. 接著，按一下 [詞彙定義]。這會顯示 [詞彙撰寫] 畫面。按一下 [新增]，開始新增新的詞彙定義。目前支援兩種類型的詞彙定義 – 常值和 XML。

##常值定義
1.	按一下 [新增] 後，新的 [新增定義] 分頁隨即開啟。輸入下列值
  1.	名稱 – 限用不含任何特殊字元的英數字元。此外，這在您現有的詞彙定義清單中必須是唯一的。
  2.	說明 – 選擇性欄位。
  3.	類型 – 有兩種支援的類型。在此範例中選擇常值
  4.	輸入類型 – 這可讓使用者選取定義的資料類型。目前有 4 種資料類型可供選取：i.字串 – 這些值必須在雙引號中輸入 (「範例字串」) ii.布林值 – 這可以是 true 或 false iii.數字 – 可以是任何十進位數字 iv.日期時間 – 這表示定義屬於日期類型。資料必須以下列格式輸入 – mm/dd/yyyy hh:mm:ss AM\PM v.輸入 – 這是您輸入您定義值的位置。在此輸入的值必須符合選擇的資料型。使用者可以輸入單一值、一組以逗號分隔的值，或是使用關鍵字的值範圍。例如，使用者可以輸入唯一值 1；一組值 1、2、3；或是範圍 1 到 5。請注意，範圍僅適用於數字。

![替代文字][5]
##XML 定義
如果選擇 XML 做為 [詞彙類型]，則必須指定下列的輸入 a.結構描述 – 按一下此選項會開啟新分頁，讓使用者從已上傳的結構描述清單中選擇，或讓使用者上傳新的結構描述。b.XPATH – 只有在上一個步驟中選擇結構描述後，才會解除鎖定此輸入。按一下此選項會顯示已選取的結構描述，並讓使用者選取必須建立詞彙定義的節點。c.FACT – 此輸入會指出哪個資料物件會饋送至規則引擎進行處理。這是進階屬性，依預設會設定為所選 XPATH 的父系。在鏈結和集合的案例中，FACT 特別重要。

![替代文字][6]

### 大量新增
上述步驟已擷取建立詞彙定義的經驗。在建立之後，建立的詞彙定義會以清單的形式出現。您可以從相同結構描述產生多個定義，而無須每次都重複上述步驟，但這有特定需求。此時，「大量新增」功能正可彰顯其功效。按一下 [大量新增]，您將會進入新分頁。第一個步驟是選取要建立多個定義的結構描述。按一下此選項會開啟新分頁，讓使用者從已上傳的結構描述清單中選擇，或讓使用者上傳新的結構描述。此時，XPATHS 屬性會解除鎖定。按一下此選項會開啟結構描述檢視器，您可以從中選取多個節點。建立的多個定義會將其名稱預設為所選節點的名稱。這些名稱一律可在建立後修改。

![替代文字][7]

##建立原則
在開發人員建立必要的詞彙後，預期應由商務分析人員透過 Azure 入口網站建立商務原則。1.在建立的規則應用程式上會有原則濾鏡，使用者按一下後即可進入原則建立頁面。2.此頁面會顯示此特定規則應用程式具有的原則清單。使用者只要輸入原則名稱並按索引標籤，即可新增原則。多個原則可以位於單一規則 API 應用程式中。3.按一下已建立的原則，會讓使用者進入原則詳細資料頁面，而得以檢視位於原則中的規則。![替代文字][8] 4.按一下 [新增] 以新增規則。這會使您進入新分頁。

##建立規則
規則是條件和動作陳述式的集合。如果條件評估為 true，則會執行動作。在 [建立規則] 分頁中，提供唯一的規則名稱 (適用於該原則) 和說明 (選擇性)。[條件] 方塊可用來建立複雜的條件陳述式。以下是支援的關鍵字：1.And – 條件運算子 2.Or – 條件運算子 3. does_not_exist 4. exists 5. false 6. is_equal_to 7. is_greater_than 8. is_greater_than_equal_to 9. is_in 10. is_less_than 11. is_less_than_equal_to 12. is_not_in 13. is_not_equal_to 14. mod 15. true

Action(Then) 方塊可以包含多個陳述式 (每行一個)，用來建立要執行的動作。以下是支援的關鍵字：1. equals 2. false 3. true 4. halt 5. mod 6. null 7. update

條件和動作方塊提供 Intellisense 協助使用者快速撰寫規則。按 ctrl + 空格或直接輸入，即可加以觸發。輸入的字元若符合關鍵字，將會自動進行篩選並顯示。Intellisense 視窗會顯示所有的關鍵字和詞彙定義。![替代文字][9]

##明確向前鏈結
BizTalk 規則支援明確向前鏈結。意思就是，如果使用者想要重新評估規則以回應特定動作，可以使用特定的關鍵字加以觸發。下列是支援的關鍵字：1. update <vocabulary definition> - 此關鍵字會重新評估在其條件中使用指定詞彙定義的所有規則。2.Halt – 此關鍵字會停止所有規則執行

##啟用\停用規則
可以啟用或停用原則中的每個規則。依預設會啟用所有規則。停用的規則在原則執行期間不會執行。啟用\停用規則可從規則分頁直接執行 – 命令提供於分頁頂端的命令列中，或可從原則中存取，內容功能表 (以滑鼠右鍵按一下規則) 具有啟用\停用的選項。

##規則優先順序
原則的所有規則都按順序執行。執行的優先順序取決於在原則中的出現順序。只要拖放規則，即可變更此優先順序。

##測試原則
在撰寫原則後，在您將其用於生產環境之前，會有測試原則的佈建。藉由使用「測試原則」命令，使用者將可進入 [測試原則] 分頁。在此分頁中，您可以檢視在原則中使用，且需要使用者輸入的詞彙定義清單。使用者可以針對其測試案例，為這些輸入手動新增值。或者，使用者也可以選擇為輸入匯入測試 XML。在所有輸入皆匯入後，即可執行測試，而每個詞彙定義的輸出都會顯示在輸出資料行中，以便比較。若要檢視商務分析人員容易使用的記錄檔，請按一下 [檢視記錄檔]，以檢視執行記錄檔。若要儲存記錄檔，可以使用 [儲存輸出] 選項來儲存所有測試相關資料，以進行獨立分析。

## 在邏輯應用程式中使用規則
原則已撰寫並經過測試後，此時已可供取用。使用者可以藉由執行 [新增] -> [邏輯應用程式]，來建立新的邏輯應用程式。在設計工具中，BizTalk 規則位於右側的組件庫中。此時您可以將其拖放到設計工具介面上。完成後，將會有一個選項可選擇規則 API 應用程式 (動作) 的目標。動作包括要執行的原則清單。請選擇在其後必須輸入原則的特定原則。使用者可以使用規則 API 應用程式下游的輸出，做出進一步的決策。

## 透過 API 使用規則
規則 API 應用程式也可以使用一組豐富的可用 API 來叫用。如此，使用者就不一定只能使用流程，而可以藉由 REST 呼叫在任何應用程式中使用規則。按一下 [規則] 儀表板中的 [API 定義] 透鏡，即可檢視確切可用的 REST API。

![替代文字][10]

以下是如何在 C# 使用此 API 的範例

   			// Constructing the JSON message to use in API call to Rules API App

			// xmlInstance is the XML message instance to be passed as input to our Policy
            string xmlInstance = "<ns0:Patient xmlns:ns0="http://tempuri.org/XMLSchema.xsd">  <ns0:Name>Name_0</ns0:Name>  <ns0:Email>Email_0</ns0:Email>  <ns0:PatientID>PatientID_0</ns0:PatientID>  <ns0:Age>10.4</ns0:Age>  <ns0:Claim>    <ns0:ClaimDate>2012-05-31T13:20:00.000-05:00</ns0:ClaimDate>    <ns0:ClaimID>10</ns0:ClaimID>    <ns0:TreatmentID>12</ns0:TreatmentID>    <ns0:ClaimAmount>10000.0</ns0:ClaimAmount>    <ns0:ClaimStatus>ClaimStatus_0</ns0:ClaimStatus>    <ns0:ClaimStatusReason>ClaimStatusReason_0</ns0:ClaimStatusReason>  </ns0:Claim></ns0:Patient>";

            JObject input = new JObject();

			// The JSON object is to be of form {"<XMLSchemName>_<RootNodeName>":"<XML Instance String>". 
			// In the below case, we are using XML Schema - "insruanceclaimsschema" and the root node is "Patient". 
			// This is CASE SENSITIVE. 
            input.Add("insuranceclaimschema_Patient", xmlInstance);
            string stringContent = JsonConvert.SerializeObject(input);


            // Making REST call to Rules API App
            HttpClient httpClient = new HttpClient();
	
			// The url is the Host URL of the Rules API App
            httpClient.BaseAddress = new Uri("https://rulesservice77492755b7b54c3f9e1df8ba0b065dc6.azurewebsites.net/");            
            HttpContent httpContent = new StringContent(stringContent);
            httpContent.Headers.ContentType = MediaTypeHeaderValue.Parse("application/json");            

            // Invoking API "Execute" on policy "InsruranceClaimPolicy" and getting response JSON object. The url can be gotten from the API Definition Lens
            var postReponse = httpClient.PostAsync("api/Policies/InsuranceClaimPolicy?comp=Execute", httpContent).Result;

請注意，上述規則 API 應用程式已將其安全性設定設為 "Public Anon"。可從 API 應用程式內設定此項，方法為使用 - 所有設定->應用程式設定 -> 存取層級

![替代文字][11]

## 編輯詞彙和原則
使用商務規則的主要優點之一，是商務邏輯的變更推送至生產環境的速度可快速許多。對詞彙和原則所做的任何變更，都會立即套用在生產環境中。使用者只需要瀏覽至各自的詞彙定義或原則並進行變更，即可生效。

<!--Image references-->
[1]: ./media/app-service-logic-use-biztalk-rules/InsuranceScenario.PNG
[2]: ./media/app-service-logic-use-biztalk-rules/InsuranceBusinessLogic.png
[3]: ./media/app-service-logic-use-biztalk-rules/CreateRuleApiApp.png
[4]: ./media/app-service-logic-use-biztalk-rules/RulesDashboard.png
[5]: ./media/app-service-logic-use-biztalk-rules/LiteralVocab.PNG
[6]: ./media/app-service-logic-use-biztalk-rules/XMLVocab.PNG
[7]: ./media/app-service-logic-use-biztalk-rules/BulkAdd.PNG
[8]: ./media/app-service-logic-use-biztalk-rules/PolicyList.PNG
[9]: ./media/app-service-logic-use-biztalk-rules/RuleCreate.PNG
[10]: ./media/app-service-logic-use-biztalk-rules/APIDef.PNG
[11]: ./media/app-service-logic-use-biztalk-rules/PublicAnon.PNG


 

<!---HONumber=62-->