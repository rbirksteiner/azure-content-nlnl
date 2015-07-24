<properties
 pageTitle="在管理入口網站中開始使用排程器"
 description=""
 services="scheduler"
 documentationCenter=".NET"
 authors="krisragh"
 manager="dwrede"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="get-started-article" 
 ms.date="05/12/2015"
 ms.author="krisragh"/>

# 在管理入口網站中開始使用排程器

## 使用 Azure 管理入口網站設定 Azure 排程器，可以快速且輕鬆地建立工作

若要完成本教學課程，您需要一個已啟用 Azure 排程器功能的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](https://msdn.microsoft.com/library/)。

## 開始使用

使用 Azure 管理入口網站，可在 Azure 排程器上輕鬆地建立工作和工作集合。本教學課程將逐步引導您建立要用來儲存工作的工作集合、建立工作集合中的工作，以及大致介紹可透過管理入口網站使用的工作監視和管理工作。您需有先前使用 Azure 的經驗，才能使用本教學課程。

第一次開啟 Azure 管理入口網站時會自動進入 [所有項目] 索引標籤。[所有項目] 索引標籤中的資料行可排序。若要檢視您的排程器工作和工作集合，請按一下 [**排程器**] 索引標籤。

![][1]

## 建立工作集合和工作

1.  登入[管理入口網站](https://manage.windowsazure.com/)。  

2.  按一下 [**應用程式服務**]，然後按一下 [**新建**]、指向 [**排程器**]，然後按一下 [**自訂建立**]。 <br /><br /> ![][2]

3.  在 [**工作集合**] 中，選取現有的工作集合 (如果您已經建立工作集合，而且想要將此工作加入至該工作集合)，方法為在 [**工作集合**] 下拉式清單下選取現有的工作集合的名稱。如果您沒有要將工作加入其中的現有工作集合，請選取 [**新建**]，然後輸入名稱來識別新的工作集合。<br /><br /> ![][3]

4.  在 [**區域**] 中，選取工作集合的地理區域。

5.  按一下方向鍵，建立工作集合，並移至下一個階段 – 建立作業。

6.  讓我們使用 GET 要求建立只要點擊 http://www.microsoft.com/ 的工作。在 [工作動作] 畫面中，為所要求的表單欄位定義下列值：

    1.  **名稱：** ` getmicrosoft`  

    2.  **動作類型：** ` HTTP`

    3.  **方法：** ` GET`

    4.  **URI：** ` http://www.microsoft.com`

   	![][4]

7.  在下一個步驟中，定義排程。工作可以定義為一次工作，但是讓我們挑選週期排程。僅基於說明用途，本教學課程中的一些螢幕擷取畫面會顯示 1 分鐘的週期，但挑選 12 小時的週期。

    1.  **重複頻率：**` 12 Hours`  

    2.  **開始：**` Now`

    3.  **結束於：**` Select date 2 days after current day and any time`

   	![][5]

8.  最後，按一下 [確定]。

9.  可能需要一段時間，才會建立工作集合和工作。若要檢查狀態，可以監控位於入口網站底部的通知。

   	![][6]

   	在建立了工作和工作集合之後，有一則訊息將告訴您已成功建立工作或工作集合。工作將列示在 [排程器] 區段的 [工作] 區段中，而工作集合將列示在 [工作集合] 區段中。若要設定工作中的其他進階設定，請參閱底下＜設定工作＞一節。

   	![][7]

## 管理和監視工作集合和工作

一旦建立工作集合，它就會顯示在主要排程器管理畫面中。

![][8]

按一下工作集合，即會開啟新視窗並顯示下列選項：

1.  儀表板  

2.  調整

3.  歷程記錄

4.  作業

下列主題會更詳細地描述這些索引標籤

### 儀表板

按一下工作集合名稱時，即會顯示 [儀表板] 索引標籤。[儀表板] 會顯示下列資訊：

![][9]

#### 工作使用概觀和執行使用概觀

顯示固定度量清單的資料表和圖表系列。這些度量提供有關工作集合之健全狀況的即時值，包括：

1.  目前的工作  

2.  已完成的工作

3.  發生錯誤的工作

4.  已啟用的工作

5.  已停用的工作

6.  工作執行

#### 快速概覽

顯示固定的狀態和設定度量清單的資料表。這些度量提供有關與工作集合相關聯之狀態和設定的即時值，包括：

1.  狀態  

2.  區域

3.  錯誤數目

4.  錯誤發生次數

5.  URI

### 調整

在 [調整] 索引標籤中，您可以變更排程器所使用的設定和服務層。

![][10]

#### 一般

這會顯示您是在 **免費**，還是在**標準**計畫上。

#### 配額

Azure 排程器會實作以數個條件為基礎的配額。本節列出配額臨界值，並可讓您變更它們。依預設，已設定一組配額。這些配額設定的限制受限於您的計畫，而且變更計畫可能會影響定價。可變更配額來調整您的排程器。選項包括：

1.  最大工作數目  

2.  最大頻率

3.  最大間隔

### 歷程記錄

[記錄] 索引標籤會顯示所選取工作的下列資訊：

![][11]

#### 歷程記錄資枓表

一種資料表，為選取的工作顯示系統中每次執行工作時選取的度量。這些度量提供有關排程器健全狀況的即時值。

#### 可用的度量

以下是可用的效能計數器/度量：

1.  狀態  

2.  詳細資料

3.  重試嘗試

4.  執行次數 (第 1 次、第 2 次、第 3 次等等。)

5.  執行時間戳記

您可以按一下 [**檢視詳細歷程記錄**]， 查看每次執行的完整回應。此對話方塊也可讓您將回應複製到剪貼簿。

![][12]

### 作業

[工作] 索引標籤會顯示下列資訊來監視工作的執行歷程記錄：

![][13]

#### 工作資料表

一種資料表，為系統中的每個工作顯示選取的度量。這些度量提供有關排程器健全狀況的即時值。

#### 停用、啟用或刪除工作

按一下工作名稱，即會提供您啟用、停用或刪除工作的選項。已刪除的工作可能無法復原。

#### 可用的度量

以下是可用的計數器和度量：

1.  名稱  

2.  上次執行

3.  下次執行

4.  狀態

5.  頻率

6.  失敗

7.  錯誤

8.  執行

9.  動作類型

### 設定工作

按一下 [工作] 畫面中的工作，可讓您設定該工作。除了快速建立精靈中可用的設定外，這可讓您設定其他進階設定。若要設定工作，請按一下 [工作] 畫面中工作名稱旁邊的向右箭號。

[工作設定] 頁面可讓您更新工作設定。以下顯示 HTTP 和 HTTPS 工作的工作設定頁面。對於 HTTP 和 HTTPS 工作動作類型，您可能會將方法變更為任何允許的 HTTP 動詞。您也可以新增、刪除或變更標頭和基本驗證資訊。

![][14]

儲存體佇列工作的 [工作設定] 頁面上會出現，如下所示。對於儲存體佇列動作類型，您可能變更儲存體帳戶、佇列名稱、SAS 權杖和主體。[排程] 區段 (未顯示於下) 與 HTTP/HTTPS 工作動作類型的 [排程] 區段相同。

![][15]

最後，對於所有的動作類型，您可能變更排程本身和其週期行為。您可以變更開始日期和時間、週期排程，以及結束日期和時間 (如果工作重複發生。) 進行任何變更之後，您可以按一下 [儲存] 來儲存變更，或按一下 [捨棄] 來捨棄變更。

## 另請參閱

 [排程器是什麼？](scheduler-intro.md)

 [排程器概念、術語及實體階層](scheduler-concepts-terms.md)

 [Azure 排程器的計劃和計費](scheduler-plans-billing.md)

 [如何使用 Azure 排程器建立複雜的排程和進階週期](scheduler-advanced-complexity.md)

 [排程器 REST API 參考](https://msdn.microsoft.com/library/dn528946)

 [排程器 PowerShell Cmdlet 參考](scheduler-powershell-reference.md)

 [排程器高可用性和可靠性](scheduler-high-availability-reliability.md)

 [排程器限制、預設值和錯誤碼](scheduler-limits-defaults-errors.md)

 [排程器輸出驗證](scheduler-outbound-authentication.md)



[1]: ./media/scheduler-get-started-portal/scheduler-get-started-portal001.png
[2]: ./media/scheduler-get-started-portal/scheduler-get-started-portal002.png
[3]: ./media/scheduler-get-started-portal/scheduler-get-started-portal003.png
[4]: ./media/scheduler-get-started-portal/scheduler-get-started-portal004.png
[5]: ./media/scheduler-get-started-portal/scheduler-get-started-portal005.png
[6]: ./media/scheduler-get-started-portal/scheduler-get-started-portal006.png
[7]: ./media/scheduler-get-started-portal/scheduler-get-started-portal007.png
[8]: ./media/scheduler-get-started-portal/scheduler-get-started-portal008.png
[9]: ./media/scheduler-get-started-portal/scheduler-get-started-portal009.png
[10]: ./media/scheduler-get-started-portal/scheduler-get-started-portal010.png
[11]: ./media/scheduler-get-started-portal/scheduler-get-started-portal011.png
[12]: ./media/scheduler-get-started-portal/scheduler-get-started-portal012.png
[13]: ./media/scheduler-get-started-portal/scheduler-get-started-portal013.png
[14]: ./media/scheduler-get-started-portal/scheduler-get-started-portal014.png
[15]: ./media/scheduler-get-started-portal/scheduler-get-started-portal015.png
 

<!---HONumber=July15_HO2-->