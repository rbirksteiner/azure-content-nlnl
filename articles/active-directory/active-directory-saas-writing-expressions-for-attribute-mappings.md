<properties
	pageTitle="在 Azure Active Directory 中撰寫屬性對應的運算式"
	description="了解在 Azure Active Directory 中自動化佈建 SaaS 應用程式物件的期間，如何使用運算式對應將屬性值轉換成可接受的格式。"
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/27/2015"
	ms.author="markusvi"/>


# 在 Azure Active Directory 中撰寫屬性對應的運算式

當您設定佈建到 SaaS 應用程式時，您可以指定的其中一種屬性對應類型是運算式對應。您必須撰寫類似指令碼的運算式，以便讓您將使用者的資料轉換成 SaaS 應用程式更能接受的格式。





## 語法概觀

屬性對應的運算式語法是 Visual Basic for Applications (VBA) 函式。

- 整個運算式必須以函式定義，由函式名稱後面接著以括號括住的引數組成：<br>*FunctionName(<<argument 1>>,<<argument N>>)*


- 您可以在函式內互相巢狀函式。例如：<br>*FunctionOne(FunctionTwo(<<argument1>>))*


- 您可以將三種不同類型的引數傳入函式：

   1. 屬性，必須以方括弧括住。例如：[attributeName]

   2. 字串常數，必須以雙引號括住。例如："United States"

   3. 其他函式。例如：FunctionOne(<<argument1>>, FunctionTwo(<<argument2>>))


- 對於字串常數，如果您在字串中需要反斜線 ( \\ ) 或引號 ( " ) ，則必須使用反斜線 ( \\ ) 符號逸出。例如："公司名稱："Contoso""



## 函式的清單

[Append](#append) &nbsp;&nbsp;&nbsp;&nbsp; [FormatDateTime](#formatdatetime) &nbsp;&nbsp;&nbsp;&nbsp; [Join](#join) &nbsp;&nbsp;&nbsp;&nbsp; [Mid](#mid) &nbsp;&nbsp;&nbsp;&nbsp; [Not](#not) &nbsp;&nbsp;&nbsp;&nbsp; [Replace](#replace) &nbsp;&nbsp;&nbsp;&nbsp; [StripSpaces](#stripspaces) &nbsp;&nbsp;&nbsp;&nbsp; [Switch](#switch)





----------
### Append

**函式：**<br>Append(source, suffix)

**描述：**<br>取出 source 字串值並在結尾附加尾碼。
 
**參數：**<br>

|名稱| 必要 / 重複 | 型別 | 注意事項 |
|--- | ---                 | ---  | ---   |
| **source** | 必要 | String | 通常為 source 物件的屬性名稱 |
| **suffix** | 必要 | String | 您想要附加至 source 值結尾的字串。 |


----------
### FormatDateTime

**函式：**<br> FormatDateTime(source, inputFormat, outputFormat)

**描述：**<br>從一種格式取出日期字串，並將它轉換成不同的格式。
 
**參數：**<br>

|名稱| 必要 / 重複 | 型別 | 注意事項 |
|--- | ---                 | ---  | ---   |
| **source** | 必要 | String | 通常為 source 物件的屬性名稱。 |
| **inputFormat** | 必要 | String | source 值的預期格式。如需支援的格式，請參閱 [http://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](http://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)。 |
| **outputFormat** | 必要 | String | 輸出日期的格式。 |



----------
### Join

**函式：**<br>Join(separator, source1, source2, …)

**描述：**<br> Join() 類似 Append()，不過前者可以將多個 **source** 字串值合併成單一字串，且每個值會以 **separator** 字串分隔。

如果其中一個 source 值是多重值屬性，則該屬性中的每個值會結合在一起，並以分隔符號值分隔。

 
**參數：**<br>

|名稱| 必要 / 重複 | 型別 | 注意事項 |
|--- | ---                 | ---  | ---   |
| **separator** | 必要 | String | 用來分隔串連成一個字串的 source 值的字串。如果不需要分隔符號，可以是 ""。 |
| ****source1 … sourceN ** | 必要、次數可變 | String | 要結合在一起的字串值。 |



----------
### Mid

**函式：**<br>Mid(source, start, length)

**描述：**<br>傳回 source 值的子字串。子字串是只包含 source 字串某些字元的字串。


**參數：**<br>

|名稱| 必要 / 重複 | 型別 | 注意事項 |
|--- | ---                 | ---  | ---   |
| **source** | 必要 | String | 通常為屬性的名稱。 |
| **start** | 必要 | integer | 子字串在 **source** 字串中起始位置的索引。字串第一個字元的索引為 1，第二個字元的索引為 2，依此類推。 |
| **length** | 必要 | integer | 子字串的長度。如果長度超出 **source** 字串結尾，函式會傳回從 **start** 索引一直到 **source** 字串結尾的子字串。 |




----------
### Not

**函式：**<br>Not(source)

**描述：**<br>翻轉 **source** 的布林值。如果 **source** 值為 "*True*"，則傳回 "*False*"。反之則傳回 "*True*"。


**參數：**<br>

|名稱| 必要 / 重複 | 型別 | 注意事項 |
|--- | ---                 | ---  | ---   |
| **source** | 必要 | Boolean String | 預期的 **source** 值為 "True" 或 "False"。 |



----------
### 將

**函式：**<br>ObsoleteReplace(source, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, template)

**描述：**<br>取代字串內的值。根據提供的參數而以不同的方式運作：

- 提供 **oldValue** 和 **replacementValue** 時：

   - 以 replacementValue 取代 source 中所有的 oldValue 項目

- 提供 **oldValue** 和 **template** 時：

   - 以 **source** 值取代 **template** 中所有的 **oldValue** 項目

- 提供 **oldValueRegexPattern**、**oldValueRegexGroupName**、**replacementValue** 時：

   - 以 replacementValue 取代 source 字串中符合 oldValueRegexPattern 的所有值

- 提供 **oldValueRegexPattern**、**oldValueRegexGroupName**、**replacementPropertyName** 時：

   - 如果 **source** 有值，則傳回 **source**

   - 如果 **source** 沒有值，則使用 **oldValueRegexPattern** 和 **oldValueRegexGroupName** 從有 **replacementPropertyName** 的屬性擷取取代值。結果會傳回取代值


**參數：**<br>

|名稱| 必要 / 重複 | 型別 | 注意事項 |
|--- | ---                 | ---  | ---   |
| **source** | 必要 | String | 通常為 source 物件的屬性名稱。 |
| **oldValue** | 選用 | String | 在 **source** 或 **templet** 中要被取代的值。 |
| **regexPattern** | 選用 | String | 在 **source** 中要被取代的值的規則運算式模式。或者，如果使用了 replacementPropertyName，要從取代屬性擷取值的模式。 |
| **regexGroupName** | 選用 | String | **regexPattern** 內的群組名稱。只有當使用了 replacementPropertyName 時，我們才會從取代屬性擷取此群組的值做為 replacementValue。 |
| **replacementValue** | 選用 | String | 要取代舊值的新值。 |
| **replacementAttributeName** | 選用 | String | 當 source 沒有任何值時，要用於取代值的屬性名稱。 |
| **template** | 選用 | String | 提供 **template** 值時，我們會尋找 template 內的 **oldValue** 並以 source 值取代。 |



----------
### StripSpaces

**函式：**<br>StripSpaces(source)

**描述：**<br>移除 source 字串中的所有空格 (" ") 字元。

**參數：**<br>

|名稱| 必要 / 重複 | 型別 | 注意事項 |
|--- | ---                 | ---  | ---   |
| **source** | 必要 | String | 要更新的 **source** 值。 |



----------
### Switch

**函式：**<br>Switch(source, defaultValue, key1, value1, key2, value2, …)

**描述：**<br>當 **source** 值符合某個 **key** 時，傳回該 **key** 的 **value**。如果 **source** 值不符合任何 key，則傳回 **defaultValue**。**Key** 和 **value** 參數必須永遠成對出現。函式必須要有偶數數目的參數。

**參數：**<br>

|名稱| 必要 / 重複 | 型別 | 注意事項 |
|--- | ---                 | ---  | ---   |
| **source** | 必要 | String | 要更新的 **source** 值。 |
| **defaultValue** | 選用 | String | 當 source 不符合任何 key 時要使用的預設值。可以是空字串 ("")。 |
| **key** | 必要 | String | 要與 **source** 值比較的 **key**。 |
| **value** | 必要 | String | 符合 key 的 **source** 的取代值。 |



## 範例

### 刪去已知的網域名稱

您必須從使用者的電子郵件刪去已知的網域名稱，得到使用者名稱。<br>例如，如果網域是 "contoso.com"，您可以使用下列運算式：


**運算式：**<br> `Replace([mail], "@contoso.com", , ,"", ,)`

**範例輸入/輸出：**<br>

- **輸入** (mail)："john.doe@contoso.com"

- **輸出**："john.doe"


### 附加常數尾碼到使用者名稱

如果您使用 Salesforce 沙箱，您可能需要附加額外的尾碼到您所有的使用者名稱，才能進行同步處理。




**運算式：**<br> `Append([userPrincipalName], ".test"))`

**範例輸入/輸出：**<br>

- **輸入**：(userPrincipalName)："John.Doe@contoso.com"


- **輸出**："John.Doe@contoso.com.test"





### 串連部分名字和姓氏產生使用者別名

您必須取出使用者名字的前 3 個字母和使用者姓氏的前 5 個字母來產生使用者別名。


**運算式：**<br> `Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**範例輸入/輸出：**<br>

- **輸入** (givenName)："John"

- **輸入** (surname)："Doe"

- **輸出**："JohDoe"




### 以特定格式將日期輸出為字串

您想要以特定格式傳送日期到 SaaS 應用程式。<br>例如，您要格式化 ServiceNow 的日期。



**運算式：** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**範例輸入/輸出：**

- **輸入** (extensionAttribute1)："20150123105347.1Z"

- **輸出**："2015-01-23"





### 根據預先定義的一組選項取代值

您必須根據儲存在 Azure AD 中的狀態碼定義使用者的時區。<br>如果狀態碼不符合任何預先定義的選項，則使用 "Australia/Sydney" 的預設值。


**運算式：** <br>

`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**範例輸入/輸出：**

- **輸入** (state)："QLD"

- **輸出**："Australia/Brisbane"


[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]

<!---HONumber=August15_HO9-->