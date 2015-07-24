<properties 
   pageTitle="QuickBooks 連接器" 
   description="如何使用 QuickBooks 連接器" 
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


#在邏輯應用程式中使用 QuickBooks 連接器#

邏輯應用程式可以根據各種資料來源觸發，並提供連接器以取得及處理屬於流程一部分的資料。QuickBooks 連接器可讓您建立和修改不同的 QuickBooks 實體。以下清單是 QuickBooks 連接器支援的 QuickBooks 實體。

<Table>
<TR><TD><B>實體</TD><TD><B>描述</TR> <TR> <TD> 帳戶 </TD> <TD> 帳戶是一種會計科目表元件，而且是總帳表的一部分。用來記錄配置給特定用途的總金額 </TD> </TR> <TR> <TD> CreditMemo </TD> <TD> CreditMemo 是代表退貨或信用卡付款或已售出商品或服務付款一部分的財務交易。</TD> </TR> <TR> <TD> 客戶 </TD> <TD> 客戶是您的企業提供之服務或產品的消費者。</TD> </TR> <TR> <TD> 估價 </TD> <TD> 估價代表企業向客戶提出之銷售貨品或服務的財務交易提案，包括建議定價。</TD> </TR> <TR> <TD> 發票 </TD> <TD> 發票代表銷售表單，客戶稍後會據以針對產品或服務付費。Invoice 資料模型的其他資訊可參考此處。</TD> </TR> <TR> <TD> 項目 </TD> <TD> 項目是您的公司購買、銷售或重新銷售的東西，例如產品、寄送及處理費用、折扣和營業稅 (如果適用)。Item 會在發票或其他銷售表單上列為一行。</TD> </TR> <TR> <TD> SalesReceipt </TD> <TD> 此實體表示提供給客戶的銷售收據。</TD> </TR> </Table>


##QuickBooks 動作 ##
以下是 QuickBooks 連接器中可使用的不同動作。<table> <tbody> <tr><td> <strong>動作</strong> </td> <td> <strong>描述</strong> </td> </tr> <tr> <td> 讀取實體 </td> <td> 讀取實體物件。</td> </tr> <tr> <td> 建立或更新實體 </td> <td> 建立或更新的實體物件。如果物件已存在會更新物件，若不存在則會建立新物件。</td> </tr> <tr> <td> 刪除 </td> <td> 這個動作會從選定的實體刪除指定的物件。</td> </tr> <tr> <td> 查詢 </td> <td> 查詢作業是針對實體建立引導式查詢的方法。</td> </tr> </tbody> </table>

##建立 QuickBooks 連接器 API 應用程式##
1.	使用 Azure 入口網站右下方的 [+新增] 選項開啟 Azure Marketplace。
2.	瀏覽至 [Web 與行動] > [API 應用程式]，並搜尋「QuickBooks 連接器」。
3.	設定 QuickBooks 連接器，提供「主控方案」及資源群組的詳細資料，並選取 API 應用程式的名稱。

	![][13]
4. 在 [封裝設定] 中設定您有興趣讀取/寫入的 QuickBooks 實體。

完成上述步驟後，您現在即可建立 QuickBooks 連接器 API 應用程式。


##建立邏輯應用程式##
讓我們建立一個簡單的邏輯應用程式，以便在 QuickBooks 中建立一個帳戶並更新該帳戶的 [類別類型]。

1.	登入 Azure 入口網站，並按一下 [新增] -> [Web + 行動] -> [邏輯應用程式]

	![][1]

2.	在 [建立邏輯應用程式] 頁面中，請提供必要的詳細資料，例如名稱、應用程式服務方案和位置。

	![][2]

3.	按一下 [觸發程序及動作]，邏輯應用程式編輯器畫面便會隨即出現。

	![][3]

4.	選取 [手動執行此邏輯]，這表示僅能夠以手動方式叫用此邏輯應用程式。


5.	展開資源庫中的 [此資源群組中的 API 應用程式] 來查看所有可用的 API 應用程式。從資源庫選取 [QuickBooks 連接器]，[QuickBooks 連接器] 就會加入至流程。


6.	如果 QuickBooks 在線上，您必須驗證和授權邏輯應用程式以代表您執行作業。若要開始授權，請按一下 QuickBooks 連接器上的 [授權]。

	![][4]

7.	按一下 [授權] 會開啟 QuickBooks 的驗證對話方塊。提供您要執行作業之 QuickBooks 帳戶的登入詳細資料。

	![][5]

8. 按一下同意對話方塊中的 [授權]，來授與邏輯應用程式存取您帳戶的權限，以代表您執行作業。

	![][6]

9.	授權完成後，即會顯示所有的動作。

	![][7]

10.	選取 [建立或更新帳戶] 動作，即會顯示輸入參數。

	![][8]

11.	提供 [名稱] 和 [帳戶類型]，然後按 ✓。

	![][9]

12.	從資源庫的 [最近使用過] 區段選取 [QuickBooks 連接器]，就會加入新的 QuickBooks 動作。

13.	從動作清單中選取 [建立或更新帳戶]，即會顯示動作的輸入參數。

	![][10]

14.	按一下 [識別碼] 旁邊的 [+]，從 [建立帳戶] 動作的輸出中挑選識別碼值。

	![][11]

15.	提供帳戶類型新的值，然後按一下 ✓。

	![][12]

16. 按一下邏輯應用程式編輯器畫面上的 [確定]，然後按一下 [建立]。完成建立大約需要 30 秒的時間。

17. 瀏覽剛建立的邏輯應用程式，然後按一下 [執行] 以啟動該應用程式。

18. 您可以在 QuickBooks 帳戶中查看是否已建立名為 'Contoso' 的新帳戶。

<!--Image references-->
[1]: ./media/app-service-logic-connector-quickbooks/1_New_Logic_App.png
[2]: ./media/app-service-logic-connector-quickbooks/2_Logic_App_Settings.png
[3]: ./media/app-service-logic-connector-quickbooks/3_Logic_App_Editor.png
[4]: ./media/app-service-logic-connector-quickbooks/4_QuickBooks_Authorize.png
[5]: ./media/app-service-logic-connector-quickbooks/5_QuickBooks_Login.png
[6]: ./media/app-service-logic-connector-quickbooks/6_QuickBooks_User_Consent.png
[7]: ./media/app-service-logic-connector-quickbooks/7_QuickBooks_Actions.png
[8]: ./media/app-service-logic-connector-quickbooks/8_QuickBooks_Create_Account.png
[9]: ./media/app-service-logic-connector-quickbooks/9_Create_Account_OK.png
[10]: ./media/app-service-logic-connector-quickbooks/10_QuickBooks_Update_Account.png
[11]: ./media/app-service-logic-connector-quickbooks/11_Record_ID_from_Create.png
[12]: ./media/app-service-logic-connector-quickbooks/12_Update_Account_Address.png
[13]: ./media/app-service-logic-connector-quickbooks/13_Create_new_quickbooks_connector.png


 

<!---HONumber=62-->