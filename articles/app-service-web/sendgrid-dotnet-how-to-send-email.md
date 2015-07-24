<properties 
	pageTitle="如何使用 SendGrid 電子郵件服務 (.NET) - Azure" 
	description="了解如何在 Azure 使用 SendGrid 電子郵件服務傳送電子郵件。程式碼範例是以 C# 撰寫並使用 .NET API。" 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="thinkingserious" 
	manager="sendgrid" 
	editor="erikre"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/24/2015" 
	ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork"/>





# 如何在 Azure 上使用 SendGrid 傳送電子郵件

上次更新時間：2015 年 2 月 24 日

<h2><a name="overview"></a><span  class="short-header">概觀</span></h2>

本指南示範如何在 Azure 上透過 SendGrid 電子郵件服務執行常見程式設計工作。相關範例是以 C# 撰寫並使用 .NET API。涵蓋的案例包括**建構電子郵件**、**傳送電子郵件**、**新增附件**及**使用篩選器**。如需 SendGrid 及傳送電子郵件的詳細資訊，請參閱[後續步驟][]一節。

<h2><a name="whatis"></a><span  class="short-header">什麼是 SendGrid 電子郵件服務？</span></h2>

SendGrid 是[雲端架構電子郵件服務] (英文)，能提供可靠的[交易式電子郵件傳遞] (英文)，擴充性和即時分析，以及有彈性的 API 來輕鬆進行自訂整合。常見的 SendGrid 使用案例包括：

-   自動傳送回條給客戶。
-   管理通訊群組清單，以便將每月電子傳單和特別優惠傳送給客戶。
-   收集即時事物 (如封鎖的電子郵件) 的度量以及客戶回應。
-   產生報表，協助找出趨勢。
-   轉寄客戶查詢。
-   處理內送電子郵件。

如需詳細資訊，請參閱 [https://sendgrid.com](https://sendgrid.com)。

<h2><a name="createaccount"></a>建立 SendGrid 帳戶</h2>

[AZURE.INCLUDE [sendgrid-sign-up](../../includes/sendgrid-sign-up.md)]

<h2><a name="reference"></a>參考 SendGrid .NET 類別庫</h2>

[SendGrid NuGet 封裝](https://www.nuget.org/packages/Sendgrid)是取得 SendGrid API 及透過所有相依性設定應用程式的最簡單方式。NuGet 是 Microsoft Visual Studio 2012 隨附的 Visual Studio 延伸模組，能輕鬆地安裝及更新程式庫和工具。

> [AZURE.NOTE]如果您是執行 Visual Studio 2012 之前的 Visual Studio 版本，若要安裝 NuGet，請造訪 [http://www.nuget.org](http://www.nuget.org) (英文)，然後按一下 [安裝 NuGet] 按鈕。

若要在應用程式中安裝 SendGrid NuGet 封裝，請執行下列動作：

1.  在 [方案總管] 中，以滑鼠右鍵按一下 [參考]，然後按一下 [Manage NuGet Packages]。

2.  在 [Manage NuGet Packages] 對話方塊的左窗格中，按一下 [線上]。

3.  在結果清單中搜尋 **SendGrid** 並選取 **SendGrid** 項目 (目前的版本是 5.0.0)。

    ![SendGrid NuGet 封裝][SendGrid-NuGet-package]

4.  按一下 [安裝] 完成安裝，然後關閉此對話方塊。

SendGrid 的 .NET 類別庫稱為 **SendGridMail**。其中包含下列命名空間：

-   **SendGridMail** 可供建立和處理電子郵件項目。
-   **SendGridMail.Transport** 可供使用 **SMTP** 通訊協定或 HTTP 1.1 通訊協定 (透過 **Web/REST**) 傳送電子郵件。

將下列程式碼命名空間宣告，新增至您想要在其中以程式設計方式存取 SendGrid 電子郵件服務之任何 C# 檔案內的頂端。**System.Net** 和 **System.Net.Mail** 均為 .NET Framework 命名空間，其納入原因是因為包含您通常搭配 SendGrid API 使用的類型。

    using System;
    using System.Net;
    using System.Net.Mail;
    using SendGrid;

<h2><a name="createemail"></a>作法：建立電子郵件</h2>

使用 **SendGridMessage** 物件來建立電子郵件訊息。建立訊息物件後，即可設定屬性和方法，包括電子郵件寄件者、電子郵件收件者以及電子郵件的主旨和本文。

下列範例示範如何建立完全填入的電子郵件物件：

    // Create the email object first, then add the properties.
    var myMessage = new SendGridMessage();

    // Add the message properties.
    myMessage.From = new MailAddress("john@example.com");

    // Add multiple addresses to the To field.
    List<String> recipients = new List<String>
    {
        @"Jeff Smith <jeff@example.com>",
        @"Anna Lidman <anna@example.com>",
        @"Peter Saddow <peter@example.com>"
    };

    myMessage.AddTo(recipients);

    myMessage.Subject = "Testing the SendGrid Library";

    //Add the HTML and Text bodies
    myMessage.Html = "<p>Hello World!</p>";
    myMessage.Text = "Hello World plain text!";

如需 **SendGrid** 類型支援的所有屬性和方法的詳細資訊，請參閱 GitHub 上的 [sendgrid-csharp][] (英文)。

<h2><a name="sendemail"></a>作法：傳送電子郵件</h2>

建立電子郵件之後，您可以使用 SendGrid 提供的 Web API 進行傳送。或者，您也可以[使用 .NET 的內建程式庫](https://sendgrid.com/docs/Code_Examples/csharp.html)。

您必須提供 SendGrid 帳戶認證 (使用者名稱和密碼)，才能傳送電子郵件。下列程式碼示範如何在 **NetworkCredential** 物件中包裝認證：
    
    // Create network credentials to access your SendGrid account
    var username = "your_sendgrid_username";
    var pswd = "your_sendgrid_password";

    /* Alternatively, you may store these credentials via your Azure portal
       by clicking CONFIGURE and adding the key/value pairs under "app settings".
       Then, you may access them as follows: 
       var username = System.Environment.GetEnvironmentVariable("SENDGRID_USER"); 
       var pswd = System.Environment.GetEnvironmentVariable("SENDGRID_PASS");
       assuming you named your keys SENDGRID_USER and SENDGRID_PASS */

    var credentials = new NetworkCredential(username, pswd);

下列範例顯示如何使用 Web API 傳送郵件。

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Create credentials, specifying your user name and password.
    var credentials = new NetworkCredential("username", "password");

    // Create an Web transport for sending email.
    var transportWeb = new Web(credentials);

    // Send the email.
    // You can also use the **DeliverAsync** method, which returns an awaitable task.
    transportWeb.Deliver(myMessage);

<h2><a name="addattachment"></a>作法：新增附件</h2>

呼叫 **AddAttachment** 方法及指定您要附加的檔案名稱和路徑，即可將附件新增至郵件。您可以對想要附加的每個檔案呼叫一次此方法，即可包含多個附件。下列範例示範如何將附件新增至郵件：

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.AddAttachment(@"C:\file1.txt");
    
您也可以從資料的**串流**來新增附件。您可呼叫與上述相同的 **AddAttachment** 方法來進行此作業，只是要傳入該資料串流以及您要檔案在郵件中顯示的名稱。在此情況下，您必須新增 System.IO 程式庫。

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    using (var attachmentFileStream = new FileStream(@"C:\file.txt", FileMode.Open))
    {
        myMessage.AddAttachment(attachmentFileStream, "My Cool File.txt");
    }


<h2><a name="usefilters"></a><span  class="short-header">作法：使用應用程式來啟用頁尾、追蹤和分析</span></h2>

SendGrid 提供了運用應用程式的其他電子郵件功能。這些設定可新增到電子郵件以啟用特定功能，例如點選追蹤、Google 分析、訂閱追蹤等。如需完整的應用程式清單，請參閱[應用程式設定][]。

使用與 **SendGrid** 類別一起實作的方法，即可將應用程式套用到 **SendGrid** 電子郵件。

下列範例示範頁尾和點選追蹤篩選器：

### 頁尾

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Add a footer to the message.
    myMessage.EnableFooter("PLAIN TEXT FOOTER", "<p><em>HTML FOOTER</em></p>");

### 點選追蹤

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Html = "<p><a href="http://www.example.com">Hello World Link!</a></p>";
    myMessage.Text = "Hello World!";
    
    // true indicates that links in plain text portions of the email 
    // should also be overwritten for link tracking purposes. 
    myMessage.EnableClickTracking(true);

<h2><a name="useservices"></a>作法：使用其他 SendGrid 服務</h2>

SendGrid 提供的網頁式 API 與 Webhook 可供從 Azure 應用程式運用其他 SendGrid 功能。如需完整詳細資料，請參閱 [SendGrid API 文件][] (英文)。

<h2><a name="nextsteps"></a>後續步驟</h2>

了解 SendGrid 電子郵件服務的基本概念後，請參考下列連結以取得更多資訊。

* SendGrid C# 程式庫儲存機制：[sendgrid-csharp][]
*   SendGrid API 文件：<https://sendgrid.com/docs>
*   Azure 客戶的 SendGrid 特別優惠：[https://sendgrid.com](https://sendgrid.com)

  [後續步驟]: #nextsteps
  [What is the SendGrid Email Service?]: #whatis
  [Create a SendGrid Account]: #createaccount
  [Reference the SendGrid .NET Class Library]: #reference
  [How to: Create an Email]: #createemail
  [How to: Send an Email]: #sendemail
  [How to: Add an Attachment]: #addattachment
  [How to: Use Filters to Enable Footers, Tracking, and Analytics]: #usefilters
  [How to: Use Additional SendGrid Services]: #useservices
  
  
  [special offer]: https://www.sendgrid.com/windowsazure.html
  
  
  
  [SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid01.png
  [sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
  [SMTP vs. Web API]: https://sendgrid.com/docs/Integrate/index.html
  [應用程式設定]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
  [SendGrid API 文件]: https://sendgrid.com/docs
  
  [雲端架構電子郵件服務]: https://sendgrid.com/email-solutions
  [交易式電子郵件傳遞]: https://sendgrid.com/transactional-email
 

<!---HONumber=62-->