<properties
	pageTitle="Azure AD Connect 同步處理：函式參考"
	description="Azure AD Connect 同步處理中宣告式佈建運算式的參考。"
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


# Azure AD Connect 同步處理：函式參考


在 Azure Active Directory 同步處理中，函式可在同步處理期間用來操作屬性值。<br>函式的語法可使用下列格式來表示：<br>`<output type> FunctionName(<input type> <position name>, ..)`

如果函式是多載的且接受多種語法，即會列出所有的有效語法。<br> 函式是強型別的，且會確認傳入的類型與記載的類型相符。<br> 如果類型不符，即會擲回錯誤。

類型可以使用下列語法來表示：

- **bin** – 二進位
- **bool** – 布林值
- **dt** – UTC 日期/時間
- **enum** – 已知常數的列舉
- **exp** – 運算式，預期會評估為布林值
- **mvbin** – 多重值的二進位
- **mvstr** – 多重值的參考
- **num** – 數值
- **ref** – 單一值的參考
- **str** – 單一值的字串
- **var** – (幾乎) 任何其他類型的變體
- **void** – 不會傳回值



## 函式參考

----------
**轉換：**

[CBool](#cbool) &nbsp;&nbsp;&nbsp;&nbsp; [CDate](#cdate) &nbsp;&nbsp;&nbsp;&nbsp; [CGuid](#cguid) &nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp; [ConvertFromBase64](#convertfrombase64) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToBase64](#converttobase64) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertFromUTF8Hex](#convertfromutf8hex) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToUTF8Hex](#converttoutf8hex) &nbsp;&nbsp;&nbsp;&nbsp; [CNum](#cnum) &nbsp;&nbsp;&nbsp;&nbsp; [CRef](#cref) &nbsp;&nbsp;&nbsp;&nbsp; [CStr](#cstr) &nbsp;&nbsp;&nbsp;&nbsp; [StringFromGuid](#StringFromGuid) &nbsp;&nbsp;&nbsp;&nbsp; [StringFromSid](#stringfromsid)

**日期 / 時間：**

[DateAdd](#dateadd) &nbsp;&nbsp;&nbsp;&nbsp; [DateFromNum](#datefromnum) &nbsp;&nbsp;&nbsp;&nbsp; [FormatDateTime](#formatdatetime) &nbsp;&nbsp;&nbsp;&nbsp; [Now](#now) &nbsp;&nbsp;&nbsp;&nbsp; [NumFromDate](#numfromdate)

**目錄**

[DNComponent](#dncomponent) &nbsp;&nbsp;&nbsp;&nbsp; [DNComponentRev](#dncomponentrev) &nbsp;&nbsp;&nbsp;&nbsp; [EscapeDNComponent](#escapedncomponent)

**檢查：**

[IsBitSet](#isbitset) &nbsp;&nbsp;&nbsp;&nbsp; [IsDate](#isdate) &nbsp;&nbsp;&nbsp;&nbsp; [IsEmpty](#isempty) &nbsp;&nbsp;&nbsp;&nbsp; [IsGuid](#isguid) &nbsp;&nbsp;&nbsp;&nbsp; [IsNull](#isnull) &nbsp;&nbsp;&nbsp;&nbsp; [IsNullOrEmpty](#isnullorempty) &nbsp;&nbsp;&nbsp;&nbsp; [IsNumeric](#isnumeric) &nbsp;&nbsp;&nbsp;&nbsp; [IsPresent](#ispresent) &nbsp;&nbsp;&nbsp;&nbsp; [IsString](#isstring)

**數學運算：**

[BitAnd](#bitand) &nbsp;&nbsp;&nbsp;&nbsp; [BitOr](#bitor) &nbsp;&nbsp;&nbsp;&nbsp; [RandomNum](#randomnum)

**多重值**

[Contains](#contains) &nbsp;&nbsp;&nbsp;&nbsp; [Count](#count) &nbsp;&nbsp;&nbsp;&nbsp; [Item](#item) &nbsp;&nbsp;&nbsp;&nbsp; [ItemOrNull](#itemornull) &nbsp;&nbsp;&nbsp;&nbsp; [Join](#join) &nbsp;&nbsp;&nbsp;&nbsp; [RemoveDuplicates](#removeduplicates) &nbsp;&nbsp;&nbsp;&nbsp; [Split](#split)

**程式流程：**

[Error](#error) &nbsp;&nbsp;&nbsp;&nbsp; [IIF](#iif) &nbsp;&nbsp;&nbsp;&nbsp; [Switch](#switch)


**文字**

[GUID](#guid) &nbsp;&nbsp;&nbsp;&nbsp; [InStr](#instr) &nbsp;&nbsp;&nbsp;&nbsp; [InStrRev](#instrrev) &nbsp;&nbsp;&nbsp;&nbsp; [LCase](#lcase) &nbsp;&nbsp;&nbsp;&nbsp; [Left](#left) &nbsp;&nbsp;&nbsp;&nbsp; [Len](#len) &nbsp;&nbsp;&nbsp;&nbsp; [LTrim](#ltrim) &nbsp;&nbsp;&nbsp;&nbsp; [Mid](#mid) &nbsp;&nbsp;&nbsp;&nbsp; [PadLeft](#padleft) &nbsp;&nbsp;&nbsp;&nbsp; [PadRight](#padright) &nbsp;&nbsp;&nbsp;&nbsp; [PCase](#pcase) &nbsp;&nbsp;&nbsp;&nbsp; [Replace](#replace) &nbsp;&nbsp;&nbsp;&nbsp; [ReplaceChars](#replacechars) &nbsp;&nbsp;&nbsp;&nbsp; [Right](#right) &nbsp;&nbsp;&nbsp;&nbsp; [RTrim](rtrim) &nbsp;&nbsp;&nbsp;&nbsp; [Trim](#trim) &nbsp;&nbsp;&nbsp;&nbsp; [UCase](#ucase) &nbsp;&nbsp;&nbsp;&nbsp; [Word](#word)

----------
### BitAnd

**說明：**<br>BitAnd 函式會在值中設定指定的位元。
 
**語法：**<br> `num BitAnd(num value1, num value2)`

- value1，value2：應該使用 AND 連結在一起的數值
 
**備註：**<br>此函式會將這兩個參數轉換為二進位表示法，並將某一個位元設為：
 
- 0 - 如果 *mask* 和 *flag* 中有一個對應位元或這兩者為 0
- 1 - 如果這兩個對應位元都是 1。 

換句話說，除非這兩個參數的對應位元都是 1，否則在所有情況下都會傳回 0。
 
**範例：**<br>`BitAnd(&HF, &HF7)`<br> 傳回 7，因為十六進位 “F” AND “F7” 會評估為此值。
 
----------
### BitOr

**說明：**<br>BitOr 函式會在值中設定指定的位元。

**語法：** <br> `num BitOr(num value1, num value2)`

- value1，value2：應該使用 OR 連結在一起的數值 

**備註：**<br>此函式會將這兩個參數轉換成二進位表示法，而且，如果 mask 和 flag 中有一個對應位元或這兩者為 1，就會將某一個位元設為 1，如果這兩個對應位元都為 0，則會設為 0。<br> 換句話說，除非這兩個參數的對應位元都是 0，否則在所有情況下都會傳回 1。

----------
### CBool

**說明：**<br>CBool 函式會根據評估的運算式傳回布林值

**語法：** <br> `bool CBool(exp Expression)`

**備註：**<br>如果運算式評估為非零值，CBool 就會傳回 True，否則會傳回 False。


**範例：**<br> `CBool([attrib1] = [attrib2])` <br>

如果這兩個屬性的值相同，即會傳回 True。
 
 


----------
### CDate

**說明：**<br>CDate 函式會傳回字串的 UTC 日期時間。日期時間不是同步處理中的原生屬性類型，但有部分函式會用到。

**語法：**<br> `dt CDate(str value)`

- Value：包含日期、時間和選擇性時區的字串

**備註：**<br>傳回的字串一律以 UTC 來表示。

**範例：**<br>`CDate([employeeStartTime])`<br> 根據員工的開始時間傳回日期時間

`CDate("2013-01-10 4:00 PM -8")`<br> 傳回代表 "2013-01-11 12:00 AM" 的日期時間
 



----------
### CGuid

**說明：**<br>CGuid 函式會將 GUID 的字串表示法轉換為二進位表示法。
 
**語法：**<br> `bin CGuid(str GUID)GUID`

- 使用此模式格式化的字串：xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx 或 {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}
 



----------
### Contains

**說明：**<br>Contains 函式會在多重值屬性內尋找字串
 
**語法：**<br>`num Contains (mvstring attribute, str search)` - 區分大小寫<br>`num Contains (mvstring attribute, str search, enum Casetype)`<br>`num Contains (mvref attribute, str search)` - 區分大小寫

- attribute：要搜尋的多重值屬性。<br>
- search：要在屬性中尋找的字串。<br>
- Casetype：CaseInsensitive 或 CaseSensitive。<br>

傳回在多重值屬性中找到字串的索引。如果找不到該字串，即會傳回 0。
 

**備註：**<br>針對多重值字串屬性，搜尋將會在值中尋找子字串。<br> 針對參考屬性，搜尋的字串必須完全符合要被視為相符的值。
 
**範例：**<br>`IIF(Contains([proxyAddresses],”SMTP:”)>0,[proxyAddresses],Error(“No primary SMTP address found.”))`<br> 如果 proxyAddresses 屬性具有主要電子郵件地址 (以大寫 “SMTP:” 表示)，就會傳回 proxyAddress 屬性，否則會傳回錯誤。
 
 


----------
### ConvertFromBase64

**說明：**<br>ConvertFromBase64 函式會將指定的 base64 編碼值轉換為一般字串。
 
**語法：**<br>`str ConvertFromBase64(str source)` - 假設使用 Unicode 進行編碼<br>`str ConvertFromBase64(str source, enum Encoding)`

- source：Base64 編碼的字串<br>
- Encoding：Unicode、ASCII、UTF8

**範例**<br> `ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`<br> `ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

這兩個範例都會傳回 "*Hello world!*"
 
 


----------
### ConvertFromUTF8Hex

**說明：**<br>ConvertFromUTF8Hex 函式會將指定的 UTF8 十六進位編碼值轉換為字串。
 
**語法：**<br> `str ConvertFromUTF8Hex(str source)`

- source：UTF8 2 個位元組的編碼字串
 
**備註：**<br>此函式與 ConvertFromBase64(,UTF8) 之間的差異在於結果支援 DN 屬性。<br> Azure Active Directory 會使用此格式做為 DN。
 
**範例：**<br>`ConvertFromUTF8Hex("48656C6C6F20776F726C6421")`<br> 傳回 "*Hello world!*"
 
 


----------
### ConvertToBase64

**說明：**<br>ConvertToBase64 函式會將字串轉換為 Unicode Base64 字串。<br> 將整數陣列的值轉換為其對等的字串表示法，此表示法是以 Base-64 數字編碼的。

**語法：**<br> `str ConvertToBase64(str source)`
 
**範例：**<br>`ConvertToBase64("Hello world!")`<br> 傳回 "SABlAGwAbABvACAAdwBvAHIAbABkACEA"
 
 


----------
### ConvertToUTF8Hex

**說明：**<br>ConvertToUTF8Hex 函式會將字串轉換為 UTF8 十六進位編碼值。
 
**語法：**<br> `str ConvertToUTF8Hex(str source)`
 
**備註：**<br>Azure Active Directory 會使用此函式的輸出格式做為 DN 屬性格式。
 
**範例：**<br>`ConvertToUTF8Hex("Hello world!")`<br> 傳回 48656C6C6F20776F726C6421
 
 


----------
### 計數

**說明：**<br>Count 函式會傳回多重值屬性中的元素個數
 
**語法：**<br> `num Count(mvstr attribute)`
 



----------
### CNum

**說明：**<br>CNum 函式會取得字串，並傳回數值資料類型。
 
**語法：**<br> `num CNum(str value)`
 



----------
### CRef

**說明：**<br>將字串轉換為參考屬性
 
**語法：**<br> `ref CRef(str value)`
 
**範例：**<br> `CRef(“CN=LC Services,CN=Microsoft,CN=lcspool01, CN=Pools,CN=RTC Service,” & %Forest.LDAP%)`
 
 


----------
### CStr

**說明：**<br>CStr 函式會轉換為字串資料類型。
 
**語法：**<br> `str CStr(num value)` <br> `str CStr(ref value)` <br> `str CStr(bool value)` <br>

- value：可以是數值、參考屬性或布林值。 
 
**範例：**<br>`CStr([dn]) <br>` 可能傳回 “cn=Joe,dc=contoso,dc=com”
 
 


----------
### DateAdd

**說明：**<br>傳回日期，其中包含已加入指定時間間隔的日期。
 
**語法：**<br> `dt DateAdd(str interval, num value, dt date)`

- interval：字串運算式，此為您想要加入的時間間隔。字串必須具有下列其中一個值：
 - yyyy 年
 - q 季
 - m 月
 - y 一年中第幾天
 - d 日
 - w 工作日
 - ww 周
 - h 小時
 - n 分鐘
 - s 秒
- value：您想要加入的單位數。它可以是正數 (用以取得未來的日期) 或負數 (用以取得過去的日期)。 
- date：日期時間，代表要加入間隔的日期。
 
**範例：**<br>`DateAdd("m", 3, CDate("2001-01-01"))`<br> 加入 3 個月，並傳回代表 "2001-04-01" 的日期時間
 
 


----------
### DateFromNum

**說明：**<br>DateFromNum 函式會將 AD 日期格式的值轉換為日期時間類型。
 
**語法：**<br> `dt DateFromNum(num value)`
 
**範例：**<br> `DateFromNum([lastLogonTimestamp])` <br> `DateFromNum(129699324000000000)` <br> 傳回代表 2012-01-01 23:00:00 的日期時間
 



----------
### DNComponent

**說明：**<br>DNComponent 函式會從左邊傳回指定 DN 元件的值。
 
**語法：**<br> `str DNComponent(ref dn, num ComponentNumber)`

- dn：要解譯的參考屬性
- ComponentNumber：DN 中要傳回的元件
 
**範例：**<br>`DNComponent([dn],1)`<br> 如果 dn 為 “cn=Joe,ou=…，就會傳回 Joe
 



----------
### DNComponentRev

**說明：**<br>DNComponentRev 函式會從右邊 (結尾處) 傳回指定 DN 元件的值。
 
**語法：**<br> `str DNComponentRev(ref dn, num ComponentNumber)` <br> `str DNComponentRev(ref dn, num ComponentNumber, enum Options)`

- dn：要解譯的參考屬性
- ComponentNumber - DN 中要傳回的元件
- Options：DC - 忽略所有含 "dc =" 的元件
 
**範例：**<br>`If dn is “cn=Joe,ou=Atlanta,ou=GA,ou=US, dc=contoso,dc=com” then DNComponentRev([dn],3)`<br>`DNComponentRev([dn],1,”DC”)`<br> 這兩者都會傳回 US。
 
 


----------
### 錯誤

**說明：**<br>Error 函式是用來傳回自訂錯誤。
 
**語法：**<br> `void Error(str ErrorMessage)`
 
**範例：**<br>`IIF(IsPresent([accountName]),[accountName],Error(“AccountName is required”))`<br> 如果 accountName 屬性不存在，即會擲回有關物件的錯誤。
 
 


----------
### EscapeDNComponent

**說明：**<br>EscapeDNComponent 函式會採用 DN 的一個元件並逸出它，以便在 LDAP 中顯示它。
 
**語法：**<br> `str EscapeDNComponent(str value)`
 
**範例：**<br>`EscapeDNComponent(“cn=” & [displayName]) & “,” & %ForestLDAP%`<br> 確保即使 displayName 屬性具有必須在 LDAP 中逸出的字元，還是能夠在 LDAP 目錄中建立物件。
 
 


----------
### FormatDateTime

**說明：**<br>FormatDateTime 函式可用來將日期時間格式化為具有指定格式的字串
 
**語法：**<br> `str FormatDateTime(dt value, str format)`

- value：具備日期時間格式的值<br>
- format：字串，表示要轉換的目標格式。
 
**備註：**<br> 您可以在此處找到格式的可能值：[使用者定義日期/時間格式 (Format 函式)](http://msdn2.microsoft.com/library/73ctwf33(VS.90).aspx)
 
**範例：**<br>
 
`FormatDateTime(CDate(“12/25/2007”),”yyyy-mm-dd”)` <br> 結果是 “2007-12-25”。

`FormatDateTime(DateFromNum([pwdLastSet]),”yyyyMMddHHmmss.0Z”)` <br> 會產生 "20140905081453.0Z"
 
 


----------
### GUID

**說明：**<br> 函式 GUID 會產生新的隨機 GUID
 
**語法：**<br> `str GUID()`
 



----------
### IIF

**說明：**<br> IIF 函式會根據指定的條件傳回其中一組可能值。
 
**語法：**<br> `var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

- condition：可評估為 True 或 False 的任何值或運算式。
- valueIfTrue：條件評估為 True 時所傳回的值。
- valueIfFalse：條件評估為 False 時所傳回的值。

**範例：**<br> `IIF([employeeType]=“Intern”,”t-“&[alias],[alias])` <br> 如果使用者是實習生，就會傳回開頭加上 “t-“ 的使用者別名，否則會依原樣傳回使用者的別名。
 
 


----------
### InStr

**說明：**<br> InStr 函式會在字串中尋找第一個出現的子字串
 
**語法：**<br>
 
`num InStr(str stringcheck, str stringmatch)` <br> `num InStr(str stringcheck, str stringmatch, num start)` <br> `num InStr(str stringcheck, str stringmatch, num start , enum compare)`

- stringcheck：要搜尋的字串 <br>
- stringmatch：要尋找的字串 <br>
- start：開始尋找子字串的位置 <br>
- compare：vbTextCompare 或 vbBinaryCompare
 
**備註：**<br> 會傳回找到子字串的位置，如果找不到，則傳回 0。

**範例：**<br> `InStr("The quick brown fox","quick")` <br> 評估為 5

`InStr("repEated","e",3,vbBinaryCompare)` <br> 評估為 7
 
 


----------
### InStrRev

**說明：**<br> InStrRev 函式會在字串中尋找最後一個出現的子字串
 
**語法：**<br> `num InstrRev(str stringcheck, str stringmatch)` <br> `num InstrRev(str stringcheck, str stringmatch, num start)` <br> `num InstrRev(str stringcheck, str stringmatch, num start, enum compare)`

- stringcheck：要搜尋的字串 <br>
- stringmatch：要尋找的字串 <br>
- start：開始尋找子字串的位置 <br>
- compare：vbTextCompare 或 vbBinaryCompare

**備註：**<br> 會傳回找到子字串的位置，如果找不到，則傳回 0。

**範例：**<br> `InStrRev("abbcdbbbef","bb")` <br> 傳回 7。
 
 


----------
### IsBitSet

**說明：**<br> IsBitSet 函式會測試是否要設定位元
 
**語法：**<br> `bool IsBitSet(num value, num flag)`

- value：評估的數值。flag：具有要評估之位元的數值
 
**範例：**<br> `IsBitSet(&HF,4)` <br> 因為位元 “4” 是使用十六進位值 “F” 所設定，所以會傳回 True
 



----------
### IsDate

**說明：**<br> 如果運算式可評估為日期時間類型，IsDate 函式即會評估為 True。
 
**語法：**<br> `bool IsDate(var Expression)`
 
**備註：**<br> 用來判斷 CDate() 是否會成功。
 



----------
###IsEmpty

**說明：**<br> 如果屬性存在於 CS 或 MV 中，但評估為空字串，IsEmpty 函式即會評估為 True。
 
**語法：**<br> `bool IsEmpty(var Expression)`
 



----------
###IsGuid

**說明：**<br> 如果字串可轉換為 GUID，IsGuid 函式即會評估為 True。
 
**語法：**<br> `bool IsGuid(str GUID)`
 
**備註：**<br> GUID 定義為下列其中一種模式的字串：xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx 或 {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

用來判斷 CGuid() 是否將會成功。
 
**範例：**<br> `IIF(IsGuid([strAttribute]),CGuid([strAttribute]),NULL)` <br> 如果 StrAttribute 具有 GUID 格式，即會傳回二進位表示法，否則會傳回 Null。
 
 


----------
###IsNull

**說明：**<br> 如果運算式評估為 Null，IsNull 函式即會傳回 True。
 
**語法：**<br> `bool IsNull(var Expression)`
 
**備註：**<br> 針對屬性，Null 表示該屬性不存在。
 
**範例：**<br> `IsNull([displayName])` <br> 如果屬性不存在於 CS 或 MV 中，即會傳回 True。
 
 


----------
###IsNullOrEmpty

**說明：**<br> 如果運算式為 Null 或空字串，IsNullOrEmpty 函式即會傳回 True。
 
**語法：**<br> `bool IsNullOrEmpty(var Expression)`
 
**備註：**<br> 針對屬性，如果屬性不存在，或存在但為空字串，即會將此評估為 True。<br> 此函式的相反函式名稱為 IsPresent。
 
**範例：**<br> `IsNull([displayName])` <br> 如果屬性不存在於 CS 或 MV 中或為空字串，即會傳回 True。
 
 


----------
### IsNumeric

**說明：**<br> IsNumeric 函式會傳回布林值，指出運算式是否可評估為數字類型。
 
**語法：**<br> `bool IsNumeric(var Expression)`
 
**備註：**<br> 用來判斷 CNum() 是否可成功剖析運算式。




----------
### IsString

**說明：**<br> 如果運算式可評估為字串類型，IsString 函式即會評估為 True。
 
**語法：**<br> `bool IsString(var expression)`
 
**備註：**<br> 用來判斷 CStr() 是否可成功剖析運算式。
 



----------
### IsPresent

**說明：**<br> 如果運算式評估為非 Null 且不是空字串，IsPresent 函式即會傳回 True。
 
**語法：**<br> `bool IsPresent(var expression)`
 
**備註：**<br> 這個函式的相反函式名稱為 IsNullOrEmpty。
 
**範例：**<br>
 
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`
  



----------
### 項目

**說明：**<br> Item 函式會從多重值字串/屬性傳回一個項目。
 
**語法：**<br> `var Item(mvstr attribute, num index)`

- attribute：多重值的屬性 <br>
- index：多重值字串中項目的索引。
 
**備註：**<br> Item 函式可以與 Contains 函式搭配使用，因為後者會將索引傳回多重值屬性中的項目。

如果索引超出範圍，即會擲回錯誤。
 
**範例：**<br> `Mid(Item([proxyAddress],Contains([proxyAddress], ”SMTP:”)),6)` <br> 會傳回主要電子郵件地址。
 
 


----------
### ItemOrNull

**說明：**<br> ItemOrNull 函式會從多重值字串/屬性傳回一個項目。
 
**語法：**<br> `var ItemOrNull(mvstr attribute, num index)`

- attribute：多重值的屬性 <br>
- index：多重值字串中項目的索引。
 
**備註：**<br> ItemOrNull 函式可以與 Contains 函式一起搭配使用，因為後者會將索引傳回多重值屬性中的項目。

如果索引超出範圍，即會傳回 Null 值。
 



----------
### Join

**說明：**<br> Join 函式會採用多重值的字串，並傳回單一值的字串，其中每個項目之間都插入指定的分隔符號。
 
**語法：**<br> `str Join(mvstr attribute)` <br> `str Join(mvstr attribute, str Delimiter)`

- attribute：包含要聯結之字串的多重值屬性。<br>
- delimiter：任何字串，可用來分隔傳回字串中的子字串。如果省略，即會使用空格字元 (" ")。如果分隔符號是零長度字串 ("") 或 Nothing，就不會使用分隔符號來串連清單中的所有項目。
 
**備註**<br> Join 和 Split 函式之間有同位。Join 函式可接受字串陣列，並使用分隔符號字串來聯結它們，以傳回單一字串。Split 函式會取得字串並以分隔符號來分隔，以傳回字串陣列。不過，主要的差別是 Join 可以使用任何分隔符號字串來串連字串，Split 只能使用單一字元分隔符號來分隔字串。
 
**範例：**<br> `Join([proxyAddresses],”,”)` <br> 可傳回："SMTP:john.doe@contoso.com,smtp:jd@contoso.com”
 
 


----------
### LCase

**說明：**<br> LCase 函式會將字串中的所有字元轉換為小寫。
 
**語法：**<br> `str LCase(str value)`
 
**範例：**<br> `LCase(“TeSt”)` <br> 傳回 “test”。
 
 


----------
### Left

**說明：**<br> Left 函式會從字串左邊傳回指定的字元數。
 
**語法：**<br> `str Left(str string, num NumChars)`

- string：要傳回字元的來源字串 <br>
- NumChars：數字，識別從 string 開頭 (左邊) 傳回的字元數
 
**備註：**<br> 包含 string 中前 numChars 個字元的字串：

- 如果 numChars = 0，會傳回空字串。
- 如果 numChars < 0，會傳回輸入字串。
- 如果 string 為 Null，會傳回空字串。

如果 string 包含的字元數比 numChars 中指定的數目少，即會傳回與 string 完全相同的字串 (例如，包含參數 1 中的所有字元)。
 
**範例：**<br> `Left(“John Doe”, 3)` <br> 傳回 “Joh”。
 



----------
### Len

**說明：**<br> Len 函式會傳回字串中的字元數。
 
**語法：**<br> `num Len(str value)`
 
**範例：**<br> `Len(“John Doe”)` <br> 傳回 8。
 



----------
### LTrim

**說明：**<br> LTrim 函式會從字串中移除開頭空白字元。
 
**語法：**<br> `str LTrim(str value)`
 
**範例：**<br> `LTrim(“ Test ”)` <br> 傳回 “Test ”
 
 


----------
### Mid

**說明：**<br> Mid 函式會從字串中的指定位置傳回指定的字元數。
 
**語法：**<br> `str Mid(str string, num start, num NumChars)`

- string：要傳回字元的來源字串 <br>
- start：數字，可識別 string 中要傳回字元的起始位置
- NumChars：數字，可識別要從 string 中的位置傳回的字元數
 

**備註：**<br> 從 string 中的位置 start 開始傳回 numChars 個字元。<br> 包含從 string 中的位置 start 起算的 numChars 個字元的字串：

- 如果 numChars = 0，會傳回空字串。
- 如果 numChars < 0，會傳回輸入字串。
- 如果 start > 字串的長度，會傳回輸入字串。
- 如果 start < = 0，會傳回輸入字串。
- 如果 string 為 Null，會傳回空字串。

如果 string 中從位置 start 起算所剩餘的字元數不足 numChar 個字元，即會盡可能地傳回所有可傳回的字元。
 
**範例：**<br>
 
`Mid(“John Doe”, 3, 5)` <br> 傳回 “hn Do”。

`Mid(“John Doe”, 6, 999)` <br> 傳回 “Doe”
 
 


----------
### Now

**說明：**<br> Now 函式會根據您電腦的系統日期和時間，傳回指定目前日期和時間的日期時間。
 
**語法：**<br> `dt Now()`
 



----------
### NumFromDate

**說明：**<br> NumFromDate 函式會以 AD 的日期格式傳回日期。
 
**語法：**<br> `num NumFromDate(dt value)`
 

**範例：**<br> `NumFromDate(CDate("2012-01-01 23:00:00"))` <br> 傳回 129699324000000000
 
 


----------
### PadLeft

**說明：**<br> PadLeft 函式會使用提供的填補字元，將字串左側填補到指定的長度。
 
**語法：**<br> `str PadLeft(str string, num length, str padCharacter)`

- string：要填補的字串。<br>
- length：表示 string 所需字串長度的整數。<br>
- padCharacter：字串，由用來做為填補字元的單一字元所組成
 


----------
### 備註 
 
- 如果 string 的長度小於 length，則會將 padCharacter 重複附加至 string 的開頭 (左邊)，直到它的長度等於 length 為止。
- PadCharacter 可以是空白字元，但不能是 Null 值。
- 如果 string 的長度等於或大於 length，就會以原樣傳回 string。
- 如果 string 的長度大於或等於 length，即會傳回與 string 完全相同的字串。
- 如果 string 的長度小於 length，則會傳回所需長度的新字串，包含使用 padCharacter 填補的 string。
- 如果 string 為 Null，函式即會傳回空字串。

**範例：**<br> `PadLeft(“User”, 10, “0”)` <br> 傳回 “000000User”。
 
 


----------
### PadRight

**說明：**<br> PadRight 函式會使用提供的填補字元，將字串右側填補到指定的長度。
 
**語法：**<br> `str PadRight(str string, num length, str padCharacter)`

- string：要填補的字串。 
- length：表示 string 所需長度的整數。
- padCharacter：字串，由用來做為填補字元的單一字元所組成
 
**備註：**
 
- 如果 string 的長度小於 length，則會將 padCharacter 重複附加至 string 的結尾 (右邊)，直到它的長度等於 length 為止。
- padCharacter 可以是空白字元，但不能是 Null 值。
- 如果 string 的長度等於或大於 length，就會以原樣傳回 string。
- 如果 string 的長度大於或等於 length，即會傳回與 string 完全相同的字串。
- 如果 string 的長度小於 length，則會傳回所需長度的新字串，包含使用 padCharacter 填補的 string。
- 如果 string 為 Null，函式即會傳回空字串。
 

**範例：**<br> `PadRight(“User”, 10, “0”)` <br> 傳回 "User000000"。
 
 


----------
### PCase

**說明：**<br> PCase 函式會將字串中每個以空格分隔之單字的第一個字元轉換為大寫，並將其他所有字元轉換為小寫。
 
**語法：**<br> `String PCase(string)`
 
**範例：** <br> `PCase(“TEsT”)` <br> 傳回 “Test”。
 
 


----------
### RandomNum

**說明：**<br> RandomNum 函式會傳回指定間隔之間的隨機數字。
 
**語法：**<br> `num RandomNum(num start, num end)`

- start：可識別要產生之隨機值下限的數字 <br>
- end：可識別要產生之隨機值上限的數字
 
**範例：**<br> `Random(100,999)` <br> 傳回 734。
 
 


----------
### RemoveDuplicates

**說明：**<br> RemoveDuplicates 函式會採用多重值的字串，並確定每個值都是唯一的。
 
**語法：**<br> `mvstr RemoveDuplicates(mvstr attribute)`
 
**範例：**<br> `RemoveDuplicates([proxyAddresses])` <br> 傳回處理過的 proxyAddress 屬性，其中已移除所有重複的值。
 
 


----------
### 將

**說明：**<br> Replace 函式會將所有出現的字串取代為另一個字串。
 
**語法：**<br> `str Replace(str string, str OldValue, str NewValue)`

- string：要取代其值的字串。<br>
- OldValue：要搜尋並取代的字串。<br>
- NewValue：要取代的字串。
 

**備註：**<br> 這個函式會辨識下列特殊的 Moniker：

- \\n – 新行 
- \\r – 歸位字元
- \\t – 定位字元
 

**範例：**<br>
 
`Replace([address],”\r\n”,”, “)` <br> 使用逗號和空格來取代 CRLF，並且可產生 “One Microsoft Way, Redmond, WA, USA”
 
 


----------
### ReplaceChars

**說明：**<br> ReplaceChars 函式會取代 ReplacePattern 字串中找到的所有出現的字元。

**語法：**<br> `str ReplaceChars(str string, str ReplacePattern)`

- string：要取代字元的字串。
- ReplacePattern：字串，包含要取代之字元的字典。 
 
格式為 {source1}:{target1},{source2}:{target2},{sourceN},{targetN}，其中 source 為要尋找的字元，而 target 則為要取代的字串。
 

**備註：**
 
- 此函式會取得已定義之來源的每個出現項目，並使用目標來取代它們。 
- 來源必須是一個 (unicode) 字元。 
- 來源不能是空字串或超過一個字元 (剖析錯誤)。
- 目標可以有多個字元，例如 ö:oe、β:ss。
- 目標可以空白，表示應移除此字元。 
- 來源是區分大小寫且必須完全相符。
- , (逗號) 和 : (冒號) 是保留字元，無法使用這個函式來取代。 
- 系統會忽略空格和 ReplacePattern 字串中的其他空白字元。
 

**範例：**<br> '%ReplaceString% = ’:,Å:A,Ä:A,Ö:O,å:a,ä:a,ö,o'

`ReplaceChars(”Räksmörgås”,%ReplaceString%)` <br> 傳回 Raksmorgas

`ReplaceChars(“O’Neil”,%ReplaceString%)` <br> 傳回 “ONeil”，已將單一撇號定義為要移除。
 



----------
### Right

**說明：**<br> Right 函式會從字串右邊 (結尾處) 傳回指定的字元數。
 
**語法：**<br> `str Right(str string, num NumChars)`

- string：要傳回字元的字串 
- NumChars：數字，可識別要從 string 結尾 (右邊) 傳回的字元數
 
**備註：**<br> 會從 string 的最後一個位置傳回 NumChars 個字元。

包含 string 中最後 numChars 個字元的字串：

- 如果 numChars = 0，會傳回空字串。
- 如果 numChars < 0，會傳回輸入字串。
- 如果 string 為 Null，會傳回空字串。

如果 string 包含的字元數比 numChars 中指定的數目少，即會傳回與 string 完全相同的字串。

**範例：**<br> `Right(“John Doe”, 3)` <br> 傳回 “Doe”。
 



----------
### RTrim

**說明：**<br> RTrim 函式會從字串移除結尾空白字元。
 
**語法：**<br> `str RTrim(str value)`

**範例：** <br> `RTrim(“ Test ”)` <br> 傳回 “Test”。




----------
### 分割

**說明：**<br> Split 函式會採用以分隔符號分隔的字串，並使其成為多重值的字串。
 

**語法：**<br> `mvstr Split(str value, str delimiter)` <br? `mvstr Split(str value, str delimiter, num limit)`

- value：以分隔符號字元分隔的字串。
- delimiter：用來做為分隔符號的單一字元。 
- limit：將傳回的值數目上限。
 
**範例：**<br> `Split(“SMTP:john.doe@contoso.com,smtp:jd@contoso.com”,”,”)` <br> 傳回多重值的字串，其中包含 2 個 proxyAddress 屬性可用的元素
 



----------
### StringFromGuid

**說明：**<br> StringFromGuid 函式會採用二進位 GUID，並將其轉換為字串
 
**語法：**<br> `str StringFromGuid(bin GUID)`
 



----------
### StringFromSid

**說明：**<br> StringFromSid 函式會將位元組陣列或包含安全性識別碼的多重值位元組陣列，轉換為字串或多重值的字串。
 
**語法：**<br> `str StringFromSid(bin ObjectSID)` <br> `mvstr StringFromSid(mvbin ObjectSID)`
 



----------
### Switch

**說明：**<br> Switch 函式可用來根據評估的條件傳回單一值。

**語法：**<br> `var Switch(exp expr1, var value1[, exp expr2, var value … [, exp expr, var valueN]])`

- expr：您想要評估的 Variant 運算式。 
- value：當對應的運算式為 True 時要傳回的值。
 
**備註：**<br> Switch 函式引數清單是由運算式和值的配對所組成。運算式是以從左到右的方式進行評估，並會傳回與要評估為 True 的第一個運算式相關聯的值。如果未正確配對組件，就會發生執行階段錯誤。

例如，如果 expr1 為 True，Switch 就會傳回 value1。如果 expr-1 為 False，但 expr-2 為 True，Switch 就會傳回 value-2，依此類推。

Switch 會在下列情況中傳回 Nothing：- 沒有任何運算式為 True。- 第一個 True 的運算式具有Null 的對應值。

雖然 Switch 只會傳回其中一個運算式，但它會評估所有運算式。基於這個理由，您應該留意非預期的副作用。例如，如果任何運算式的評估會產生除以零的錯誤，就會發生錯誤。

Value 也可以是會傳回自訂字串的 Error 函式。

**範例：**<br> `Switch([city] = "London", "English", [city] = "Rome", "Italian", [city] = "Paris", "French", True, Error(“Unknown city”))` <br> 傳回一些主要城市中所使用的語言，否則會傳回錯誤。




----------
### Trim

**說明：**<br> Trim 函式會從字串移除開頭和結尾的空白字元。
 
**語法：**<br> `str Trim(str value)` <br> `mvstr Trim(mvstr value)`
 
**範例：** <br> `Trim(“ Test ”)` <br> 傳回 “Test”。

`Trim([proxyAddresses])` <br> 會移除 proxyAddress 屬性中每個值的開頭和結尾空格。




----------
### UCase

**說明：**<br> UCase 函式會將字串中的所有字元轉換為大寫。

**語法：**<br> `str UCase(str string)`
 
**範例：**<br> `UCase(“TeSt”)` <br> 傳回 “TEST”。
 
 


----------
### Word

**說明：**<br> Word 函式會根據描述要使用之分隔符號及要傳回之字數的參數，傳回字串內含的單字。
 
**語法：**<br> `str Word(str string, num WordNumber, str delimiters)`

- string：要傳回單字的字串
- WordNumber：數字，可識別應傳回的字數。 
- delimiters：字串，表示應用來識別單字的分隔符號
 
**備註：**<br> string 內以 delimiters 其中一個字元來分隔之字元的每個字串，都會被識別為單字：

- 如果 number < 1，會傳回空字串。
- 如果 string 為 Null，會傳回空字串。

如果 string 所含的字數少於 number 個字，或者 string 不包含任何 delimeters 所識別的單字，就會傳回空字串。
 

**範例：**<br> `Word(“The quick brown fox”,3,” “)` <br> 傳回 “brown”

`Word(“This,string!has&many seperators”,3,”,!&#”)` <br> 會傳回 “has”


## 其他資源

* [了解宣告式佈建運算式](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)
* [Azure AD Connect 同步處理：自訂同步處理選項](active-directory-aadconnectsync-whatis.md)
* [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)
 
 
<!--Image references-->

<!---HONumber=August15_HO7-->