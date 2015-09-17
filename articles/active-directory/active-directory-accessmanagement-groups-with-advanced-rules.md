
<properties
	pageTitle="使用屬性來建立進階規則 | Microsoft Azure"
	description="管理安全性群組，以及如何使用這些群組管理資源的存取權的進階說明。"
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/29/2015" 
	ms.author="femila"/>


# 使用屬性來建立進階規則
Azure 管理入口網站會為您提供設定更進階的規則的彈性，以啟用群組的動態成員資格。

若要在 Azure 管理入口網站中**建立進階規則**，在群組的 [設定] 索引標籤底下，選取 [進階規則] 選項按鈕，然後在提供的文字方塊中輸入您的進階規則。您可以使用下列資訊建立進階規則。

## 建構進階規則的主體
您可以為群組的動態成員資格建立的進階規則基本上是一個二進位運算式，其中包含三個部分，且會產生 true 或 false 的結果。這三個部分包括：

- 左側的參數
- 二進位運算子
- 右側的常數 

完整的進階規則外觀如下：(leftParameter binaryOperator "RightConstant")，其中需要左右括號括住整個二進位運算式、需要雙引號括住右側的常數，且左側參數的語法是 user.property。進階規則可以包含多個二進位運算式，並以 -and、 -or 和 -not 邏輯運算子分隔。以下是正確建構的進階規則的範例：

- (user.department -eq "Sales") -or (user.department -eq "Marketing") 
- (user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE") 

如需支援的參數和運算式規則運算子的完整清單，請參閱下列各節。

進階規則主體的總長度不得超過 255 個字元。
> [AZURE.NOTE]字串和 regex 運算都不區分大小寫。您也可以使用 $null 做為常數，執行 Null 檢查，例如，user.department-eq $null。包含引號 " 的字串應該使用 ' 字元逸出，例如 user.department -eq "Sa`"les"。

##支援的運算式規則運算子
下表列出所有支援的運算式規則運算子及其用於進階規則主體中的語法：

| 運算子 | 語法 |
|-----------------|----------------|
| Not Equals | -ne |
| Equals | -eq |
| Not Starts With | -notStartsWith |
| 開頭為 | -startsWith |
| Not Contains | -notContains |
| Contains | -contains |
| Not Match | -notMatch |
| Match | -match |


| 查詢剖析錯誤 | 錯誤的使用方式 | 更正的使用方式 |
|----------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 錯誤：不支援屬性。 | (user.invalidProperty -eq "Value") | (user.department-eq"value") 屬性應該符合上述支援的屬性清單中的一個屬性。 |
| 錯誤：屬性不支援運算子。 | (user.accountEnabled -contains true) | (user.accountEnabled -eq true) 屬性屬於布林型別。使用上述清單中的布林型別支援的運算子 (-eq 或-ne)。 |
| 錯誤：查詢編譯錯誤。 | (user.department -eq "Sales") -and (user.department -eq "Marketing")(user.userPrincipalName -match "*@domain.ext") | (user.department -eq "Sales") -and (user.department -eq "Marketing") 邏輯運算子應該符合上述支援的屬性清單中的一個屬性。(user.userPrincipalName -match ".*@domain.ext")or(user.userPrincipalName -match "@domain.ext$") 規則運算式中發生錯誤。 |
| 錯誤：二進位運算式不是正確的格式。 | (user.department –eq “Sales”) (user.department -eq "Sales")(user.department-eq"Sales") | (user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain") 查詢有多個錯誤。括號不在正確的位置。 |
| 錯誤：設定動態成員資格時發生未知的錯誤。 | (user.accountEnabled -eq "True" AND user.userPrincipalName -contains "alias@domain") | (user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain") 查詢有多個錯誤。括號不在正確的位置。 |

##支援的參數
以下是您可以在進階規則中使用的所有使用者屬性：

**布林型別的屬性**

允許的運算子

* -eq


* -ne


| 屬性 | 允許的值 | 使用量 |
|----------------|-----------------|--------------------------------|
| accountEnabled | true false | user.accountEnabled -eq true) |
| dirSyncEnabled | true false null | (user.dirSyncEnabled -eq true) |

**字串類型的屬性**

允許的運算子

* -eq


* -ne


* -notStartsWith


* -StartsWith


* -contains


* -notContains


* -match


* -notMatch

| 屬性 | 允許的值 | 使用量 |
|----------------------------|-------------------------------------------------------------------------------------------------------|-----------------------------------------------------------|
| city | 任何字串值或 $null。 | (user.city -eq "value") |
| country | 任何字串值或 $null。 | (user.country -eq "value") |
| department | 任何字串值或 $null。 | (user.department -eq "value") |
| displayName | 任何字串值。 | (user.displayName -eq "value") |
| facsimileTelephoneNumber | 任何字串值或 $null。 | (user.facsimileTelephoneNumber -eq "value") |
| givenName | 任何字串值或 $null。 | (user.givenName -eq "value") |
| jobTitle | 任何字串值或 $null。 | (user.jobTitle -eq "value") |
| mail | 任何字串值或 $null。使用者的 SMTP 位址。 | (user.mail -eq "value") |
| mailNickName | 任何字串值。使用者的郵件別名。 | (user.mailNickName -eq "value") |
| mobile | 任何字串值或 $null。 | (user.mobile -eq "value") |
| objectId | 使用者物件的 GUID | (user.objectId -eq "1111111-1111-1111-1111-111111111111") |
| passwordPolicies | None DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword | (user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName | 任何字串值或 $null。 | (user.physicalDeliveryOfficeName -eq "value") |
| postalCode | 任何字串值或 $null。 | (user.postalCode -eq "value") |
| preferredLanguage | ISO 639-1 code | (user.preferredLanguage -eq "zh-tw") |
| sipProxyAddress | 任何字串值或 $null。 | (user.sipProxyAddress -eq "value") |
| state | 任何字串值或 $null。 | (user.state -eq "value") |
| streetAddress | 任何字串值或 $null。 | (user.streetAddress -eq "value") |
| surname | 任何字串值或 $null。 | (user.surname -eq "value") |
| telephoneNumber | 任何字串值或 $null。 | (user.telephoneNumber -eq "value") |
| usageLocation | 兩個字母的國家 (地區) 代碼 | (user.usageLocation -eq "US") |
| userPrincipalName | 任何字串值。 | (user.userPrincipalName -eq "alias@domain") |
| userType | member guest $null | (user.userType -eq "Member") |

**字串集合類型的屬性**

允許的運算子

* -contains


* -notContains

| 屬性 | 允許的值 | 使用量 |
|----------------|---------------------------------------|------------------------------------------------------|
| otherMails | 任何字串值 | (user.otherMails -contains "alias@domain") |
| proxyAddresses | SMTP: alias@domain smtp: alias@domain | (user.proxyAddresses -contains "SMTP: alias@domain") |

## 屬下規則
您現在可以根據使用者的經理屬性在群組中填入成員。
設定群組為「經理」群組
--------------------------------------------------------------------------------
1. 在管理員入口網站上按一下 [設定] 索引標籤，然後選取 [進階規則]。 
2. 使用下列語法輸入規則：*Direct Reports for {UserID\_of\_manager}* 的屬下
3. 儲存這項規則時，符合規則的所有使用者都會加入成為群組的成員。請注意，一開始填入群組可能需要幾分鐘的時間。 


## 其他資訊
以下是一些可提供有關 Azure Active Directory 額外資訊的主題

* [疑難排解群組的動態成員資格](active-directory-accessmanagement-troubleshooting.md)

* [使用 Azure Active Directory 群組來管理資源的存取權](active-directory-manage-groups.md)

* [什麼是 Azure Active Directory？](active-directory-whatis.md)

* [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=August15_HO6-->