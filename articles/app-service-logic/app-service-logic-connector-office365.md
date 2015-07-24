<properties 
   pageTitle="Office 365 連接器" 
   description="如何使用 Office 365 連接器" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="sutalasi" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/20/2015"
   ms.author="sutalasi"/>


#在邏輯應用程式中使用 Office 365 連接器#

邏輯應用程式可以根據各種資料來源和提供項目連接器觸發，以在流程中取得和處理資料。Office 365 連接器可讓您傳送和接收電子郵件，及管理 Office 365 帳戶的行事曆和連絡人。您可以執行各種動作，例如傳送、接收和取得電子郵件、建立和刪除行事曆中的事件，以及建立、更新、取得和刪除您的連絡人。

**基本動作**

- 新增郵件 (觸發程序)
- 傳送郵件
- 回覆郵件
- 傳送事件
- 新增連絡人

##建立 O365 連接器 API 應用程式##
若要建立 O365 連接器 API 應用程式，請實施下列步驟：

1.	使用 Azure 入口網站右下方的 [+新增] 選項開啟 Azure Marketplace。
2.	瀏覽至 [Web 與行動] > [API 應用程式]，並搜尋 Office365。
3.	設定 Office365 連接器，方法為提供裝載方案及資源群組的詳細資料，並選取 API 應用程式的名稱。

	![][21]


##建立邏輯應用程式##
讓我們建立一個會在收到電子郵件 (地址是您的銷售查詢電子郵件識別碼 - 例如 sales@contoso.com)) 時觸發的簡單邏輯應用程式。接著，它會建立事件、使用寄件者的詳細資料新增連絡人、傳送電子郵件到您個人帳戶，最後會傳送包含通知的回覆。

1.	登入 Azure 入口網站，並按一下 [新增] -> [Web + 行動] -> [邏輯應用程式]

	![][1]

2.	在 [建立邏輯應用程式] 頁面中，請提供必要的詳細資料，例如名稱、應用程式服務方案和位置。

	![][2]

3.	按一下 [觸發程序及動作]，邏輯應用程式編輯器畫面便會隨即出現。

	![][3]

4.	從組件庫中的 [此資源群組中的 API 應用程式] 區段選取 Office 365 觸發程序，以將它加入至流程。

	![][4]

6.	連接至 Office 365 需要您授權邏輯應用程式能夠存取您的帳戶。按一下 [授權] 以提供 Office 365 登入認證。

	![][5]

7.	您將會被重新導向至 Office 365 登入頁面，並且可以使用 Office 365 帳戶認證進行驗證。

	![][6]

	![][7]

8.	授權完成後，即會顯示 Office 365 觸發程序。

	![][8]

9.	選取 [新增電子郵件] 觸發程序，而輸入參數便會隨即顯示。


10.	將觸發程序頻率變更為 [分鐘]，然後按一下 [✓]。

	![][9]

11. 隨即會設定 Office 365 的 [新增電子郵件] 觸發程序，而且您可以看到一同顯示的輸出參數。
	
	![][10]

12.	從組件庫的 [最近使用的] 區段中選取 [Office 365 連接器]，即會新增 Office 365 動作。

13.	從動作清單中選取 [傳送事件]，[傳送事件] 動作的輸入參數便會隨即顯示。

	![][11]

14.	指定事件詳細資料，然後按一下 [✓]。

	![][12]

15.	從組件庫的 [最近使用的] 區段中選取 [Office 365 連接器]，即會新增 Office 365 動作。

16.	從動作清單中選取 [新增連絡人]，[新增連絡人] 動作的輸入參數便會隨即顯示。

	![][13]

17.	按一下 欄位旁邊的 [+]，並從觸發程序選取 輸出欄位值。

	![][14]

18. 按一下 [✓]，動作組態便即完成。

	![][15]

19.	從組件庫的 [最近使用的] 區段中選取 [Office 365 連接器]，即會新增 Office 365 動作。


20.	從動作清單中選取 [傳送電子郵件]，[傳送電子郵件] 動作的輸入參數便會隨即顯示。

	![][19]

21.	提供傳送電子郵件所需的詳細資料。您可以透過輸入如下所示的內容來建構訊息。設定 [傳送電子郵件] 動作之後，請按一下 [✓]。

		Body - @concat('You got a new sales enquiry from',triggers().output.body.From)

	![][20]
22.	從組件庫的 [最近使用的] 區段中選取 [Office 365 連接器]，即會新增 Office 365 動作。


23.	從動作清單中選取 [回覆]，[回覆] 動作的輸入參數便會隨即顯示。

	![][16]

24.	按一下 [寄件者] 欄位旁邊的 [+]，並從觸發程序的輸出訊息識別碼中選取值，然後按一下 [✓]。

	![][17]

25. 按一下邏輯應用程式編輯器畫面上的 [確定]，然後按一下 [建立]。完成建立大約需要 30 秒的時間。

26. 傳送電子郵件到設定觸發程序的帳戶，接著您應該會在您的個人郵件帳戶中看到一封電子郵件，並在公司郵件帳戶中看到行事曆事件和連絡人。此外，您應該會取得一則回覆，表示您不久之後將會收到銷售查詢的回應。

<!--Image references-->
[1]: ./media/app-service-logic-connector-office365/1_New_Logic_App.png
[2]: ./media/app-service-logic-connector-office365/2_Logic_App_Settings.png
[3]: ./media/app-service-logic-connector-office365/3_Logic_App_Editor.png
[4]: ./media/app-service-logic-connector-office365/4_Select_Office365_Gallery.png
[5]: ./media/app-service-logic-connector-office365/5_Office365_Authorize.png
[6]: ./media/app-service-logic-connector-office365/6_Office365_Login.png
[7]: ./media/app-service-logic-connector-office365/7_Office365_User_Consent.png
[8]: ./media/app-service-logic-connector-office365/8_Office365_Trigger.png
[9]: ./media/app-service-logic-connector-office365/9_Office365_Trigger_Settings.png
[10]: ./media/app-service-logic-connector-office365/10_Office365_Trigger_Configured.png
[11]: ./media/app-service-logic-connector-office365/11_Office365_Actions_List.png
[12]: ./media/app-service-logic-connector-office365/12_Office365_Create_Event_Inputs.png
[13]: ./media/app-service-logic-connector-office365/13_Office365_Add_Contact_Inputs.png
[14]: ./media/app-service-logic-connector-office365/14_Office365_Add_Contact_Email_FromTrigger.png
[15]: ./media/app-service-logic-connector-office365/15_Office365_Add_Contacts_Configured.png
[16]: ./media/app-service-logic-connector-office365/16_Office365_Reply_To_Inputs.png
[17]: ./media/app-service-logic-connector-office365/17_Office365_Reply_To_MessageId.png
[18]: ./media/app-service-logic-connector-office365/18_Office365_Reply_To_Configured.png
[19]: ./media/app-service-logic-connector-office365/19_Office365_Send_Inputs.png
[20]: ./media/app-service-logic-connector-office365/20_Office365_Send_Configured.png
[21]: ./media/app-service-logic-connector-office365/21-create-new-o365-api-app.png


 

<!---HONumber=62-->