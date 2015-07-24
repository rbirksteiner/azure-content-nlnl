<properties 
	pageTitle="Microsoft Azure API 應用程式社交連接器 | API 應用程式微服務" 
	description="了解如何建立 Microsoft Azure 社交連接器 API 應用程式，並將 API 應用程式新增至您的邏輯應用程式 (微服務)" 
	services="app-service\logic" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor=""/>

<tags
	ms.service="app-service-logic" 
	ms.workload="connectors" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="mandia"/>

# Microsoft Azure 應用程式服務中的社交連接器


## 什麼是社交 API 應用程式連接器
社交連接器是可以連接到社交應用程式，並代表已驗證的使用者執行數個*動作*的「API 應用程式」。大部分連接器也可以使用*觸發程序*進行設定。觸發程序是可在部分連接器上設定的事件 (與 .NET Framework 中的事件類似)。觸發程序接著可起始已為該連接器 (或任何其他連接器) 設定的動作。例如，Twitter 連接器的執行個體可使用*新訊息*觸發程序來設定，而該觸發程序定義為任何提及 @VisualStudio 的新推文。此觸發程序可接著設定為起始轉推推文的*動作*。

Microsoft 提供了下列社交連接器：

- Facebook
- Twitter
- Chatter
- Yammer
- Twilio

以下是每個社交連接器的簡短說明：

<table>
<tr>
<th> 名稱</th>
<th> 說明</th>
<th> 觸發程序</th>
<th>動作</th>

<tr>
<td>Facebook
<td>Facebook 連接器可讓您從 Facebook 動態消息張貼和閱讀訊息、評論、事件和狀態。您也可以張貼影片和相片。
<td>新文章
<td><li>發佈文章
	<li>發佈相片 
</tr>

<tr>
<td>Twitter
<td>Twitter 連接器可讓您執行數項作業，例如張貼、接收推文以及搜尋推文。您也可以使用 Twitter 連接器來追蹤 Twitter 上的最新動向，取得如推文、時間軸、朋友和追蹤者之類的詳細資料。
<td>新推文
<td><li>搜尋推文
	<li>推文
	<li>取得使用者時間軸
	<li>轉推
</tr>

<tr>
<td>Chatter
<td>Chatter 連接器可用來在您的 Chatter 動態消息上閱讀、張貼及搜尋訊息。
<td>新訊息
<td><li>張貼訊息
<li>搜尋訊息
</tr>

<tr>
<td>Yammer
<td>Yammer 連接器可讓您從 Yammer 群組張貼和閱讀訊息。
<td>新訊息
<td><li>張貼訊息	
	<li>從群組\摘要取得訊息	
</tr>

<tr>
<td>Twilio
<td>Twilio 連接器可讓您從 Twilio 帳戶傳送和接收 SMS。它也可讓您擷取電話號碼和使用量資料。
<td>N/A
<td><li>訊息：傳送、列出、取得和搜尋
	<li>電話號碼：購買、列出及取得當地和免付費電話號碼
</tr>
</table>


現在您已初步認識我們的社交連接器有哪些功能，接著我們就來看看這些連接器的一些簡單的使用案例。

## 為何要使用連接器？

連接器可加速應用程式開發，甚至可讓非開發人員建立功能完整的企業級應用程式，而不需要熟悉程式設計語言或撰寫任何程式碼。

### 監視客戶的意見反應 ###
假設貴公司最近發行了新的應用程式，而相關團隊想要知道客戶對於社交媒體的應用程式有何評價。每個團隊成員可以定期查看各個社交媒體網站，並猜測客戶可能使用那些關鍵字來討論您的應用程式。但更有更簡單輕鬆的方式；您可以建立 Twitter 連接器並加以設定，使其監視 Twitter 中的特定雜湊標記、談論內容和關鍵字。接著，您可以使用 SMTP 連接器，將包含相符推文的內容透過電子郵件傳送給您的團隊成員。您也可以監視 Facebook 和 Yammer，並在 Facebook 或 Yammer 的文章顯示您的客戶可能觸及重大議題時，透過 Twilio 連接器傳送 SMS 給您的開發維運團隊，而執行類似的動作。您連一行程式碼都不用寫，就可以做到。

現在就開始吧。


## 建立連接器
可以使用 Azure 入口網站建立連接器

### 在 Microsoft Azure 入口網站中建立社交連接器

1. 在 Azure 入口網站中，選取 [**新增**] > [**Web + 行動**] > [**Azure Marketplace**]。
2. **搜尋**連接器，或從清單中選取。選取之後，新分頁或視窗隨即開啟。選取 [**建立**]。 
3. 輸入連接器的下列屬性： 
	<table>
    <tr><th>屬性</th> <th>說明</th> </tr>
    <tr><td>名稱</td> <td>輸入 API 應用程式的名稱。例如，您可以將其命名為 RulesDiscountTaxCode 或 APIAppValidateXML</td> </tr>
    <tr><td>應用程式服務計劃</td> <td>列出您的付款方案。如果需要增減資源，則可以變更它。</th> </td>
    <tr><td>定價層</td> <td>一個唯讀屬性，會列出您 Azure 訂閱內的定價類別。</td> </tr>
    <tr><td>資源群組</td> <td>建立新群組或使用現有的群組。使用資源群組說明此屬性</td> </tr>
    <tr><td>訂閱</td> <td>列出目前訂閱的唯讀屬性</td> </tr>
    <tr><td>位置</td> <td>主控 Azure 服務的地理位置 </td></tr>
    <tr><td>新增至開始面板</td> <td>選取此選項，可將 API 應用程式新增至儀表板 (首頁)</td></tr>
</table>
4. 選取 [**建立**]。您的連接器將會建立。此作業可能需要一些時間，在建立連接器期間，將會顯示 [首頁] 畫面。使用左側的 [通知] 功能表項目，可監視連接器的狀態。

現在您已建立第一個連接器，可以考慮建置 Web、行動或邏輯應用程式來搭配使用。


### 使用 REST API 存取連接器

[使用 REST API 存取連接器](http://go.microsoft.com/fwlink/p/?LinkId=529766)


## 將連接器新增至應用程式 
Microsoft Azure 應用程式服務會公開可使用這些連接器的不同應用程式類型。例如，您可以透過*邏輯方式*將一或多個連接器結合為單一應用程式，以建立*邏輯*應用程式。

若要使用*邏輯*應用程式內的連接器，您可以從清單中選取預先設定的連接器、將其新增至您的設計工作流程中，並確定它已完成所需的設定變更而可供使用。

若要執行這些步驟，您需要 Web 應用程式、行動應用程式或邏輯應用程式。請參閱 <> 以了解特定步驟。應用程式可供使用後，請新增您的連接器。方式如下：

使用下列步驟，將連接器新增至邏輯應用程式：

1. 在 Azure 入口網站開始面板 (首頁) 中，移至 [**Marketplace**]，然後搜尋邏輯、行動、或 Web 應用程式。 

	如果您要建立新的應用程式，請搜尋邏輯應用程式、行動應用程式或 Web 應用程式。選取應用程式，然後在新分頁中選取 [**建立**]。[建立邏輯應用程式](app-service-logic-create-a-logic-app.md)會列出步驟。

2. 開啟您的應用程式，然後選取 [**觸發程序和動作**]。
3. 從 [**組件庫**] 中，選取連接器。連接器會新增至您的應用程式。
4. 設定連接器：
5. 每個連接器都有其連接之服務和環境的特定屬性。輸入屬性的詳細資料。請注意，有些屬性是選擇性的。
6. 選取 [**確定**] 以儲存變更。


## Security
連接器會使用 OAuth 或使用者名稱和密碼。

## 閱讀關於邏輯應用程式和 Web 應用程式的資訊
[什麼是邏輯應用程式？](app-service-logic-what-are-logic-apps.md) | [Azure 應用程式服務中的網站和 Web 應用程式](../app-service-web/app-service-web-overview.md) |


## 其他連接器

[BizTalk 整合連接器](app-service-logic-integration-connectors.md) | [企業連接器](app-service-logic-enterprise-connectors.md) | [企業對企業連接器](app-service-logic-b2b-connectors.md) | [通訊協定連接器](app-service-logic-protocol-connectors.md) | [應用程式 + 資料服務連接器](app-service-logic-data-connectors.md) | [連接器和 API 應用程式清單](app-service-logic-connectors-list.md)<br/><br/> [什麼是連接器和 BizTalk API 應用程式](app-service-logic-what-are-biztalk-api-apps.md)
 

<!---HONumber=62-->