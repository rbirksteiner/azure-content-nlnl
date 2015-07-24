<properties 
	pageTitle="用 Microsoft WebMatrix 開發和部署 Web 應用程式" 
	description="了解如何使用 Microsoft WebMatrix 開發 ASP.NET Web 應用程式並部署至 Azure App Service Web Apps。" 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/15/2015" 
	ms.author="tomfitz"/>


# 用 Microsoft WebMatrix 開發和部署 Web 應用程式

## 概觀

本指南說明如何使用 Microsoft WebMatrix 來建立 .NET 網站並部署至 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps。您將使用 WebMatrix 網站範本提供的範例應用程式。

您將了解：

* 如何從 WebMatrix 內登入 Azure
* 如何使用 WebMatrix 內建的範本來建立網站 
* 如何將自訂的網站直接從 WebMatrix 部署至 Azure。

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期入門 Web 應用程式。不需要信用卡；沒有承諾。

## Sign into Azure

1. 啟動 WebMatrix。
2. 如果這是您第一次使用 WebMatrix 3，則會提示您登入 Azure。否則，您可以按一下 [登入] 按鈕，然後選擇 [新增帳戶]。選擇使用您的 Microsoft 帳戶來 [登入]。

	![Add Account][addaccount]

3. 如果您已註冊 Azure 帳戶，則可使用您的 Microsoft 帳戶登入：

	![Sign into Azure][signin]


## 使用 Azure 內建的範本建立網站

1. 在開始畫面上，按一下 [新增] 按鈕，然後選擇 [範本庫]，從範本庫建立新的網站：

	![New site from Template Gallery][sitefromtemplate]

2. 範本庫會顯示可在本機或 Azure 上執行的可用範本清單。從範本清單中選取 [糕點]，在 [站台名稱] 中輸入 **bakerysample**，然後按 [下一個]。

	![Create Site from Template][sitefromtemplatedetails]

3. 如果您已登入 Azure，現在可選擇為您的本機網站建立 Azure App Service Web Apps 執行個體。請選擇唯一名稱，並選取您想在其中建立 Web Apps 執行個體的資料中心：

	![Create site on Azure][sitefromtemplateazure]

	當 WebMatrix 在 Azure 中完成建立本機網站與 Web Apps 執行個體之後，就會顯示 WebMatrix IDE：

	![WebMatrix IDE][howtowebmatrixide]

## 設定電子郵件

這個糕點範例包括模擬的訂購表單，此訂購表單會傳送一則包含訂購項目的電子郵件訊息。您可以在 Azure 上使用 SendGrid 電子郵件服務，來從您的網站傳送電子郵件。

1. 遵循[如何使用 Azure 上的 SendGrid 傳送電子郵件][sendgridexample]教學課程中的步驟，來設定 SendGrid 帳戶並擷取連線資訊。您不需要執行整個教學課程，僅執行到取得連接資訊的步驟即可。

2. 將 SendGrid NuGet 封裝新增至 WebMatrix 專案。首先，按一下 [NuGet] 按鈕。

    ![新增 SendGrid][addsendgrid]

    搜尋並安裝 SendGrid。

    ![安裝 SendGrid][installsendgrid]

    在封裝完成安裝之後，請注意 SendGrid 組件已新增至 bin。

    ![SendGrid 已新增][binsendgrid]

3. 按兩下檔名來開啟 *Order.cshtml* 頁面。

	![][modify2]

4. 在檔案頂端新增下列程式碼：

        @using SendGrid;
        @using System.Net.Mail;

4. 尋找 //SMTP Configuration for Hotmail 註解，並刪除或取消註解有關使用 WebMail 的所有程式碼。

        /*
        //SMTP Configuration for Hotmail
        WebMail.SmtpServer = "smtp.live.com";
        WebMail.SmtpPort = 25;
        WebMail.EnableSsl = true;

        //Enter your Hotmail credentials for UserName/Password and a "From" address for the e-mail
        WebMail.UserName = "";
        WebMail.Password = "";
        WebMail.From = "";

        if (WebMail.UserName.IsEmpty() || WebMail.Password.IsEmpty() || WebMail.From.IsEmpty()) {
            Response.Redirect("~/OrderSuccess?NoEmail=1");
        } 
        else {
            try {
                WebMail.Send(to: customerEmail, subject: "Fourth Coffee - New Order", body: body);
                Response.Redirect("~/OrderSuccess");
            } catch {
                ModelState.AddFormError("There was an error and your order could not be processed at this time");
            }
        }*/


5. 新增使用 SendGrid (而不是使用 WebMail) 來傳送電子郵件的程式碼。新增下列程式碼以取代您在上一個步驟中所刪除的程式碼。當建立 NetworkCredential 時，請務必新增您的 SendGrid 使用者名稱與密碼。

		 if (email.IsEmpty()) {
            Response.Redirect("~/OrderSuccess?NoEmail=1");
        }
        else {
            // Create the email object first, then add the properties.
            SendGridMessage myMessage = new SendGridMessage();
            myMessage.AddTo(email);
            myMessage.From = new MailAddress("FourthCoffee@example.com", "Fourth Coffee");
            myMessage.Subject = "Fourth Coffee - New Order";
            myMessage.Text = body;

            // Create credentials, specifying your user name and password.
            var credentials = new NetworkCredential("[your user name]", "[your password]");

            // Create an Web transport for sending email.
            var transportWeb = new Web(credentials);

            // Send the email.
            try {
                transportWeb.DeliverAsync(myMessage);
                Response.Redirect("~/OrderSuccess");
            } catch {
                ModelState.AddFormError("There was an error and your order could not be processed at this time");
            }
        }


6. 在 WebMatrix 功能區，按一下 [執行] 來測試網站。

	![][modify4]

7. 在其中一個產品上，按一下 [立即訂購]，將訂單傳送給您自己。

8. 檢查電子郵件，確定您收到訂單確認。如果您無法傳送電子郵件，請參閱＜ASP.NET 網頁 (Razor) 疑難排解指南＞中的＜[傳送電子郵件時的問題][sendmailissues]＞(英文)。
 

## 將自訂的網站從 WebMatrix 部署至 Azure

1. 在 WebMatrix 中，從 [首頁] 功能區按一下 [發行]，以顯示網站的 [發行預覽] 對話方塊。

	![WebMatrix Publish Preview][howtopublishpreview]

2. 按一下以選取 bakery.sdf 的核取方塊，然後按一下 [繼續]。發行完成時，Azure App Service 中已更新的 Web 應用程式的 URL 會顯示在 WebMatrix IDE 底部。

	![Publishing Complete][publishcomplete]

3. 按一下連結在瀏覽器中開啟網站 (Azure 中的 Web Apps 執行個體)：

	![Bakery Sample Site][bakerysample]

	Web Apps 執行個體的 URL 也可以在 [Azure 入口網站](https://portal.azure.com)中找到，請按一下 [瀏覽] > [Web Apps] 以顯示您訂用帳戶的所有 Web Apps 執行個體，然後選取 Web 應用程式。Web 應用程式的 URL 會顯示 Web 應用程式的分頁。

## 修改網站並重新發佈至 Azure 網站

您可以使用 WebMatrix 來修改網站，然後重新發佈至您的 Web Apps 執行個體。在下列程序中，您將加入核取方塊來指出訂單為禮物。

1. 開啟 *Order.cshtml* 頁面。

2. 找出 "shiping" 類別表單定義。在 &lt;li&gt; 區塊後面插入下列程式碼。
		
		<li class="gift">
		    <div class="fieldcontainer" data-role="fieldcontain">
		        <label for="isGift">This is a gift</label>           
		        <div>@Html.CheckBox("isGift")</div>
		    </div>
		</li>

	![][modify5]

3. 在檔案中出 "var shipping = Request["orderShipping"];"，在此行下方直接插入下列這一行程式碼。

		var gift = Request["isGift"];

4. 在驗證送貨地址不是空白的程式碼區塊後面，插入下列程式碼片段。

		if(gift != null) {
			body += "This is a gift." + "<br/>";
		}

	*order.cshtml* 檔案的內容應該如下圖所示。

	![][modify6]

5. 儲存檔案並在本機執行網站，然後將測試訂單傳送給您自己。請記得測試新的核取方塊。

	![][modify7]

6. 在 [首頁] 功能區按一下 [發行]，以重新發行網站。

7. 在 [發行預覽] 對話方塊中，確定已勾選 Order.cshtml，然後按一下 [繼續]。

8. 按一下連結，在瀏覽器中開啟網站，然後在您的 Web Apps 執行個體上測試更新結果。

## 後續步驟

* [WebMatrix 網站](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)

## 變更的項目
* 如需從網站變更為 App Service 的指南，請參閱：[Azure App Service 及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 如需從舊的入口網站變更為新入口網站的指南，請參閱：[巡覽預覽入口網站的參考](http://go.microsoft.com/fwlink/?LinkId=529715)




[howtowebmatrixide]: ./media/web-sites-dotnet-using-webmatrix/howtowebmatrixide2.png
[howtopublishpreview]: ./media/web-sites-dotnet-using-webmatrix/howtopublishpreview.png
[addsendgrid]: ./media/web-sites-dotnet-using-webmatrix/addsendgridpackage.png
[installsendgrid]: ./media/web-sites-dotnet-using-webmatrix/installsendgrid.png
[binsendgrid]: ./media/web-sites-dotnet-using-webmatrix/sendgridbin.png

[publishcomplete]: ./media/web-sites-dotnet-using-webmatrix/howtopublished2.png
[bakerysample]: ./media/web-sites-dotnet-using-webmatrix/howtobakerysamplesite.png
[addaccount]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-add-account.png
[signin]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-sign-in.png
[sitefromtemplate]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template.png
[sitefromtemplatedetails]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template-details.png
[sitefromtemplateazure]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template-azure.png

[modify1]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-1.png
[modify2]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-2.png
[modify3]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-3.png
[modify4]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-4.png
[modify5]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-5.png
[modify6]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-6.png
[modify7]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-7.png



[sendmailissues]: http://go.microsoft.com/fwlink/?LinkId=253001#email
[sendgridexample]: http://azure.microsoft.com/documentation/articles/sendgrid-dotnet-how-to-send-email/
 

<!---HONumber=62-->