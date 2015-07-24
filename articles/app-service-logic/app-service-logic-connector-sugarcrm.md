<properties 
   pageTitle="SugarCRM 連接器" 
   description="如何使用 SugarCRM 連接器" 
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
   ms.date="04/01/2015"
   ms.author="vagarw"/>


#在邏輯應用程式中使用 SugarCRM 連接器#

邏輯應用程式可以根據各種資料來源觸發，並提供連接器以取得及處理屬於流程一部分的資料。SugarCRM 連接器可讓您建立和修改不同的實體，例如帳戶、潛在客戶、連絡人等。以下是涉及 SugarCRM 的典型整合案例。

- 在 SugarCRM 和 ERP 系統 (例如 SAP) 間同步帳戶

- 在 Marketo 和 SugarCRM 間同步帳戶、連絡人和潛在客戶

- 從 SugarCRM 到 ERP 系統的「下單至收款」流程


屬於連接器封裝設定的一部分，使用者可以指定連接器可以管理的實體，而系統便會以動態方式填入動作、輸入和輸出參數。

##SugarCRM 連接器動作##
以下是 SugarCRM 連接器中可使用的不同動作。
 
- 建立模組 - 使用此動作建立 SugarCRM 模組的新記錄，例如帳戶、潛在客戶、連絡人。

- 更新模組 - 使用此動作更新 SugarCRM 模組的現有記錄。

- 刪除模組 - 使用此動作刪除 SugarCRM 模組的現有記錄。

- 清單模組 - 使用此動作列出篩選的記錄。若未指定查詢將傳回所有記錄。

- 取得模組 - 使用此動作抓取指定模組中的單一記錄。

- 取得記錄計數 - 使用此動作取得模組中符合查詢的記錄之數目。若未指定查詢將傳回模組中所有記錄的數目。

- 檢查重複模組 - 使用此動作檢查模組中重複的記錄。

*注意*：如需查詢中的引數之詳細資訊，請參閱 SugarCRM REST API 文件。
   
##建立 SugarCRM 連接器 API 應用程式##
1.	使用 Azure 入口網站右下方的 [+新增] 選項開啟 Azure Marketplace。
2.	瀏覽至 [Web 與行動] > [API 應用程式]，並搜尋「SugarCRM 連接器」。
3.	設定 SugarCRM 連接器，提供「主控方案」及資源群組的詳細資料，並選取 API 應用程式的名稱。

4. 設定 SugarCRM 連接器，並按一下 [建立]。下列是建立連接器所需提供的套件設定：

	<table>
  <tr>
    <td><b>Name</b></td>
    <td><b>必要</b></td>
    <td><b>說明</b></td>
  </tr>
  <tr>
    <td>網站 URL</td>
    <td>是</td>
    <td>指定 SugarCRM 執行個體的 URL，例如 https://abcde1234.sugarcrm.com</td>
  </tr>
  <tr>
    <td>用戶端識別碼</td>
    <td>是</td>
    <td>指定 SugarCRM 中 oauth 2.0 金鑰的取用者金鑰。 </td>
  </tr>
  <tr>
    <td>用戶端密碼</td>
    <td>是</td>
    <td>指定 SugarCRM 中 oauth 2.0 金鑰的取用者密碼。 </td>
  </tr>
<tr>
    <td>使用者名稱</td>
    <td>是</td>
    <td>指定 SugarCRM 使用者的使用者名稱</td>
  </tr>
	<tr>
    <td>密碼</td>
    <td>是</td>
    <td>指定 SugarCRM 使用者的密碼</td>
  </tr>
  <tr>
    <td>模組名稱</td>
    <td>是</td>
    <td>指定您要執行作業的 SugarCRM 模組，例如帳戶、連絡人、產品等等。<br><br>帳戶、潛在客戶、連絡人</td>
  </tr>
</table>![][9]



##建立邏輯應用程式##
讓我們建立一個簡單的邏輯應用程式，它會在 SugarCRM 建立帳戶，並更新相同帳戶的帳單寄送地址之詳細資料。

1.	登入 Azure 入口網站，並按一下 [新增] -> [Web + 行動] -> [邏輯應用程式]

	![][1]

2.	在 [建立邏輯應用程式] 頁面中，請提供必要的詳細資料，例如名稱、應用程式服務方案和位置。

	![][2]

3.	按一下 [觸發程序及動作]，邏輯應用程式編輯器畫面便會隨即出現。選取 [手動執行此邏輯]，這表示僅能夠以手動方式叫用此邏輯應用程式。


5.	展開資源庫中的 [此資源群組中的 API 應用程式] 來查看所有可用的 API 應用程式。從資源庫選取 SugarCRM，然後 SugarCRM 連接器 就會加入流程。


	![][3]

6.	選取 [建立帳戶] 動作，而輸入參數便會隨即顯示。

	![][4]

12.	提供名稱為「Microsoft 帳戶」，然後按一下 ✓。

	![][5]

13.	從資源庫的 [最近使用的] 區段中選取[ SugarCRM 連接器]，即會新增新的 SugarCRM 動作。

14.	從動作清單中選取 [更新帳戶]，[更新帳戶] 動作的輸入參數便會隨即顯示。

	![][6]

15.	按一下 [記錄識別碼] 旁邊的 [+]，並從 [建立帳戶] 動作的輸出中挑選識別碼值。

	![][7]

16.	提供帳單寄送地址的值，然後按一下 ✓。

	![][8]

17. 按一下邏輯應用程式編輯器畫面上的 [確定]，然後按一下 [建立]。完成建立大約需要 30 秒的時間。

18. 瀏覽剛建立的邏輯應用程式，然後按一下 [執行] 以啟動該應用程式。

19. 您可以查看名為「Microsoft 帳戶」的新帳戶已建立於您的 SugarCRM 帳戶，且同一個帳戶也跟新了帳單寄送地址資訊。

<!--Image references-->
[1]: ./media/app-service-logic-connector-sugarcrm/1_New_Logic_App.png
[2]: ./media/app-service-logic-connector-sugarcrm/2_Logic_App_Settings.png
[3]: ./media/app-service-logic-connector-sugarcrm/3_Select_SugarCRM_Gallery.png
[4]: ./media/app-service-logic-connector-sugarcrm/4_SugarCRM_Create_Account.png
[5]: ./media/app-service-logic-connector-sugarcrm/5_Create_Account_OK.png
[6]: ./media/app-service-logic-connector-sugarcrm/6_SugarCRM_Update_Account.png
[7]: ./media/app-service-logic-connector-sugarcrm/7_Record_ID_from_Create.png
[8]: ./media/app-service-logic-connector-sugarcrm/8_Update_Account_Address.png
[9]: ./media/app-service-logic-connector-sugarcrm/9_Create_new_SugarCRM_connector.png


 

<!---HONumber=62-->