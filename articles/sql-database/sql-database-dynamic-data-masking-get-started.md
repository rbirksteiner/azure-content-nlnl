<properties 
   pageTitle="開始使用 SQL Database 動態資料遮罩 (Azure Preview 入口網站)" 
   description="如何開始在 Azure Preview 入口網站中使用 SQL 資料庫動態資料遮罩" 
   services="sql-database" 
   documentationCenter="" 
   authors="nadavhelfman"
   manager="jeffreyg" 
   editor="v-romcal"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services" 
   ms.date="04/02/2015"
   ms.author="nadavh; ronmat; v-romcal; sstein"/>

# 開始使用 SQL Database 動態資料遮罩 (Azure Preview 入口網站)

> [AZURE.SELECTOR]
- [Dynamic Data Masking - Azure portal](sql-database-dynamic-data-masking-get-started-portal.md)

## 概觀

SQL Database 動態資料遮罩可藉由遮罩處理，使不具權限的使用者無法看見機密資料。在 Azure SQL Database 的 V12 版中，動態資料遮罩是 Basic、Standard 和 Premium 服務層的預覽功能。

動態資料遮罩可讓客戶在應用程式層級受到最小影響的情況下指定要顯示多少機密資料，而協助防止未經授權者存取機密資料。它是以原則為基礎的安全性功能，可針對指定的資料庫欄位隱藏查詢結果集中的機密資料，而不變更資料庫中的資料。

例如，客服中心支援人員可透過數個身分證號碼的信用卡號碼的數字來識別來電者，但這些資料項目不應完全公開給支援人員。開發人員可以定義要套用到每個查詢結果的遮罩規則，針對結果集中任何身分證號碼或信用卡號碼的末四碼以外的所有數字進行遮罩處理。在另一個範例中，開發人員藉由使用適當的資料遮罩來保護個人識別資訊 (PII) 資料，而得以在生產環境中進行疑難排解用途的查詢，且不會違反法務規定。

## SQL Database 動態資料遮罩的基本概念

您可以在 Azure Preview 入口網站中設定動態資料遮罩原則，並使用由應用程式或其他可存取資料庫的用戶端所使用的安全連接字串來完成設定。

### 動態資料遮罩權限

動態資料遮罩可由　Azure 資料庫管理員、伺服器管理員或安全性主管人員等角色來設定。

### 動態資料遮罩原則

* **特殊權限登入** - 在 SQL 查詢結果中可取得非遮罩資料的一組登入。
  
* **遮罩規則** - 一組規則，定義會遮罩處理的指定欄位和所將使用的遮罩函數。指定的欄位可使用資料庫資料表名稱和資料行名稱來定義，或使用別名名稱定義。

* **遮罩依據** - 可在來源或目的地執行。遮罩可藉由識別 [**資料表**] 名稱和 [**資料行**] 名稱而在來源層級上設定，或藉由識別查詢中使用的 [**別名**] 而在結果層級上設定。如果您熟悉資料庫的資料架構，並想要限制所有查詢結果的顯示性，您可能會想使用來源遮罩規則。如果您要限制查詢結果的顯示性而不想分析資料庫資料結構，或想針對來自不同來源的欄位限制顯示性，您可以新增結果遮罩規則。
  
* **擴充限制** - 限制機密資料的顯示性，但可能會影響某些應用程式的功能。

>[AZURE.TIP]使用 [**擴充限制**] 選項，可針對直接存取該資料庫並執行 SQL 查詢進行疑難排解的開發人員，施加存取限制。

* **遮罩函數** - 一組方法，可控制不同案例的資料顯示性。

| 遮罩函數 | 遮罩邏輯 |
|----------|---------------|
| **預設值** |**根據指定欄位的資料類型的完整遮罩**<br/><br/>• 針對字串資料類型 (nchar、ntext、nvarchar)，如果欄位的大小少於 8 個字元，則使用 XXXXXXXX 或較少 X。<br/>• 針對數值資料類型 (bigint、bit、decimal、int、money、numeric、smallint、smallmoney、tinyint、float、real) 使用零的值。<br/>• 針對日期/時間資料類型 (date、datetime2、datetime、datetimeoffset、smalldatetime、time) 使用目前的時間。<br/>• 針對 SQL 變異，會使用目前類型的預設值。<br/>• 針對 XML，會使用文件 <masked/>。<br/>• 針對特殊資料類型 (timestamp table、hierarchyid、GUID、binary、image、varbinary spatial types) 使用空值。
| **信用卡** |**遮罩方法會公開指定欄位的末四碼**，並新增常數字串做為信用卡格式的前置詞。<br/><br/>XXXX-XXXX-XXXX-1234|
| **身分證號碼** |**遮罩方法會公開指定欄位的末兩碼**，並新增常數字串做為美國社會安全碼格式的前置詞。<br/><br/>XXX-XX-XX12 |
| **電子郵件** | **遮罩方法會公開第一個字母和網域**，以常數字串作為電子郵件地址格式的前置詞。<br/><br/>aXX@XXXX.com |
| **隨機數字** | **遮罩方法會產生一個隨機數字**，其根據為選取的界限與實際資料類型。如果指定的邊界相等，則遮罩函數將是常數。<br/><br/>![導覽窗格][Image1] |
| **自訂文字** | **遮罩方法會公開第一個和最後一個字母**，並在中間新增自訂填補字串。<br/>prefix[padding]suffix<br/><br/>![導覽窗格][Image2] |

  
<a name="Anchor1"></a>
### 已啟用安全性的連接字串

當您設定動態資料遮罩時，Azure 會為您提供已啟用安全性的資料庫連接字串。只有使用此連接字串的用戶端會根據動態資料遮罩原則來遮罩處理其機密資料。您也需要更新現有的用戶端 (例如應用程式)，才能使用新的連接字串格式。

* 原始的連接字串格式：<*server name*>.database.windows.net
* 已啟用安全性的連接字串：<*server name*>.database.**secure**.windows.net

您也可以將 [**啟用安全性的存取**] 設定從 [**選擇性**] 變更為 [**必要**]，以確保沒有使用原始連接字串存取資料庫的選項，並忽略動態資料遮罩原則。當您使用特定用戶端來試用動態資料遮罩時 (例如，開發階段應用程式或 SSMS)，請選擇 [**選擇性**]。針對生產階段，請選擇 [**必要**]。<br/><br/>

![導覽窗格][Image3]<br/><br/>

## 使用 Azure Preview 入口網站為您的資料庫設定動態資料遮罩

1. 啟動 Azure Preview 入口網站，位址是 [https://portal.azure.com](https://portal.azure.com)。
	 
2. 導覽至您要遮罩處理的機密資料所在的資料庫組態刀鋒視窗。
	
3. 按一下 [**動態資料遮罩**] 磚，以啟動 [**動態資料遮罩**] 組態刀鋒視窗。

	* 或者，您可以向下捲動至 [**作業**] 區段，然後按一下 [**動態資料遮罩**]。
	 
	![導覽窗格][Image4]<br/><br/>

4. 在 [**動態資料遮罩**] 組態刀鋒視窗中，按一下 [**已啟用**]，以啟用動態資料遮罩功能。

	![導覽窗格][Image5]<br/><br/>

5. 輸入有權存取非遮罩機密資料的特殊權限登入。
 
	![導覽窗格][Image6]

	>[AZURE.TIP]若要讓應用程式層級可以對具有特殊權限的應用程式使用者顯示機密資料，請新增用於查詢的應用程式和資料庫的 SQL 登入。強烈建議此清單應包含最少的登入數目，以儘可能減少公開的機密資料。

6. 按一下 [**加入遮罩**]，以開啟 [**加入遮罩規則**] 組態刀鋒視窗。
	
7. 選擇 [**遮罩依據**]，指出要在來源還是目的地執行遮罩。遮罩可藉由識別 [**資料表**] 名稱和 [**資料行**] 名稱而在來源層級上設定，或藉由識別查詢中使用的 [**別名**] 而在結果層級上設定。請注意，**資料表**名稱指的是資料庫中的所有結構描述，並且不應包含結構描述前置詞。如果您熟悉資料庫的資料架構，並想要限制所有查詢結果的顯示性，您可能會想使用來源遮罩規則。如果您要限制查詢結果的顯示性而不想分析資料庫資料結構，或想針對來自不同來源的欄位限制顯示性，您可以新增結果遮罩規則。

8. 輸入 [**資料表**] 名稱和 [**資料行**] 名稱，或輸入 [**別名**] 名稱，以定義將會遮罩處理的指定欄位。

9. 從機密資料遮罩類別清單中，選擇 [**遮罩欄位格式**]。

	![導覽窗格][Image7]<br/><br/>

10. 按一下資料遮罩規則刀鋒視窗中的 [**儲存**]，以更新動態遮罩原則中的遮罩規則集。

11. 考慮選取 [**使用擴充限制**]，以透過隨選查詢限制機密資料的顯示性。

12. 按一下資料遮罩組態刀鋒視窗中的 [**儲存**]，以儲存新的或更新的遮罩規則。

13. 在 [**啟用安全性的存取**] 下，按一下 [**選擇性**] 或 [**必要**]。如需詳細資訊，請參閱[啟用安全性的連接字串](#Anchor1)。

	![導覽窗格][Image8]
	
## 使用 REST API 為您的資料庫設定動態資料遮罩

請參閱 [Azure SQL Database 的作業](https://msdn.microsoft.com/library/dn505719.aspx)。

[Image1]: ./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png
[Image2]: ./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png
[Image3]: ./media/sql-database-dynamic-data-masking-get-started/3_DDM_Current_Preview.png
[Image4]: ./media/sql-database-dynamic-data-masking-get-started/4_DDM_Activation.png
[Image5]: ./media/sql-database-dynamic-data-masking-get-started/5_DMM_Policy_Tile.png
[Image6]: ./media/sql-database-dynamic-data-masking-get-started/6_DDM_Privileged_Logins.png
[Image7]: ./media/sql-database-dynamic-data-masking-get-started/7_DDM_Add_Masking_Rule.png
[Image8]: ./media/sql-database-dynamic-data-masking-get-started/8_DDM_Security_Enabled_Access.png
 

<!---HONumber=July15_HO2-->