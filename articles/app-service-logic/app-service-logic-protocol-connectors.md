<properties 
	pageTitle="Microsoft Azure API 應用程式通訊協定連接器 | API 應用程式微服務" 
	description="了解如何建立 Microsoft Azure 通訊協定 API 應用程式和新增 API 應用程式至邏輯應用程式；微服務" 
	services="app-service\logic" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="deonhe"/>


# Microsoft Azure 應用程式服務中的通訊協定連接器


## 什麼是通訊協定連接器
通訊協定連接器是您可以使用來為使用各種不同通訊協定 (如 HTTP、SMTP、FTP 等) 進行通訊的應用程式建立 API 應用程式*。

您可以在 Microsoft Azure 入口網站中找到通訊協定連接器，方法是依序按一下 [**新增**] > [**Web + 行動**] > [**Azure Marketplace**] > 輸入搜尋關鍵字**通訊協定** > 按 Enter 鍵

Auzre Marketplace 中提供下列通訊協定連接器：

- SFTP
- POP3
- FTP
- HTTP
- SMTP
- 檔案

以下是每個通訊協定連接器的簡短描述：

<table>
<tr>
<th> 名稱</th>
<th> 說明</th>
<th> 觸發程序</th>
<th> 動作</th>

<tr>
<td>SFTP
<td>SFTP 連接器可讓您從 SFTP 伺服器下載檔案，或將檔案上傳至 SFTP 伺服器。
<td>SFTP 資料夾中可用的新檔案

<td>檔案：上傳、刪除、列出和取得 

</tr>

<tr>
<td>POP3
<td>POP3 連接器可讓您從 POP3 伺服器下載電子郵件。
<td>收到新訊息
<td>N/A
</tr>

<tr>
<td>FTP
<td>FTP 連接器可讓您從 FTP 伺服器下載檔案，或將檔案上傳至 FTP 伺服器。
<td>FTP 資料夾中可用的新檔案
<td>檔案：上傳、取得、刪除和列出
</tr>

<tr>
<td>HTTP
<td>HTTP 連接器可讓您使用 HTTP 或 HTTPS 通訊協定，將資料傳送至 HTTP 伺服器。
<td>無
<td>張貼、取得、刪除、放置
</tr>

<tr>
<td>SMTP
<td>SMTP 連接器可讓您從 SMTP 伺服器傳送電子郵件。
<td>N/A
<td>傳送電子郵件
</tr>

<tr>
<td>檔案
<td>檔案連接器可讓您連接至內部部署伺服器，並執行如上傳、取得、刪除和列出檔案這類的動作。另外還有可擷取檔案的觸發程序。
<td>加入裝置的新檔案
<td>檔案：上傳、取得、刪除和列出
</tr>


</table>

現在您已了解通訊協定連接器可以執行的功能，我們來看看這些連接器的一些簡單使用案例。

### 監視客戶的意見反應 ###
假設貴公司最近發行了新的應用程式，而相關團隊想要知道客戶對於社交媒體的應用程式有何評價。每個團隊成員可以定期查看各個社交媒體網站，並猜測客戶可能使用那些關鍵字來討論您的應用程式。但更有更簡單輕鬆的方式；您可以建立 Twitter 連接器並加以設定，使其監視 Twitter 中的特定雜湊標記、談論內容和關鍵字。您接著可以使用 SMTP 連接器來建立包含符合推文內容的電子郵件，然後將它傳送給您的小組成員。然後您可以使用 HTTP 連接器，將這些推文張貼至公司內部網站。您連一行程式碼都不用寫，就可以做到。

現在就開始吧。

## 建立連接器

可以使用 Azure 入口網站建立連接器。

### 在 Azure 入口網站中建立連接器

讓我們逐步解說如何使用 Azure Marketplace 建立 SMTP 連接器

1. 登入 Microsoft Azure [Preview 入口網站](https://portal.azure.com)。
2. 選取 [**新增**] > [**Web + 行動**] > [**Azure Marketplace**] >
3. 然後在 [**搜尋方塊**] 中輸入 [**通訊協定**]，接著按 **Enter** 鍵。您也可以從清單中選取。選取之後，新分頁或視窗隨即開啟。選取 [**建立**]。 
4. 輸入下列屬性：

<table>
<tr><th>屬性</th> <th>說明</th> </tr>
<tr><td>名稱</td> <td>說明</td> </tr>
<tr><td>Package Settings</td> <td>使用封裝設定屬性來輸入 SMTP 伺服器的所有驗證詳細資料，例如使用者名稱、密碼、連接埠號碼伺服器位址等等。</td> </tr>
<tr><td>應用程式服務方案</td> <td>列出您的付款方案。如果需要增減資源，則可以變更它。</th> </td>
<tr><td>定價層</td> <td>一個唯讀屬性，會列出您 Azure 訂閱內的定價類別。</td> </tr>
<tr><td>資源群組</td> <td>建立新群組或使用現有的群組。使用資源群組說明此屬性</td> </tr>
<tr><td>訂閱</td> <td>列出目前訂閱的唯讀屬性</td> </tr>
<tr><td>位置</td> <td>主控 Azure 服務的地理位置</th> </td>
<tr><td>新增至開始面板</td> <td>選取此選項，可將 API 應用程式新增至開始面板 (首頁)</td></tr>
</table>

### 使用 REST API 存取連接器
[使用 REST API 存取連接器](http://go.microsoft.com/fwlink/p/?LinkId=529766)

## 將連接器新增至您的應用程式 
Microsoft Azure 應用程式服務會公開可使用這些連接器的不同應用程式類型。例如，您可以透過*邏輯方式*將一或多個連接器結合為單一應用程式，以建立*邏輯*應用程式。

若要使用*邏輯*應用程式內的連接器，您可以從清單中選取預先設定的連接器、將其新增至您的設計工作流程中，並確定它已完成所需的設定變更而可供使用。

若要執行這些步驟，您需要 Web 應用程式、行動應用程式或邏輯應用程式。應用程式可供使用後，請新增您的連接器。方式如下：

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

[BizTalk 整合連接器](app-service-logic-integration-connectors.md) | [企業連接器](app-service-logic-enterprise-connectors.md) | [企業對企業連接器](app-service-logic-b2b-connectors.md) | [社交連接器](app-service-logic-social-connectors.md) | [應用程式和資料服務連接器](app-service-logic-data-connectors.md) | [連接器和 API 應用程式清單](app-service-logic-connectors-list.md)<br/><br/> [什麼是連接器和 BizTalk API 應用程式](app-service-logic-what-are-biztalk-api-apps.md)
 

<!---HONumber=62-->