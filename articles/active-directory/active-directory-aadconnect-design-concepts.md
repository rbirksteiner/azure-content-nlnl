<properties
   pageTitle="Azure AD Connect 設計概念 |Microsoft Azure"
	description="本主題詳細說明特定的實作設計領域"
	services="active-directory"
	documentationCenter=""
	authors="AndKjell"
	manager="stevenpo"
	editor=""/>

<tags
   ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="Identity"
	ms.date="08/27/2015"
	ms.author="andkjell"/>

# Azure AD Connect 的設計概念
本主題旨在說明在 Azure AD Connect 實作設計期間必須思考的領域。這是特定領域的深入探討，而在其他主題忠也會簡短描述這些概念。

## sourceAnchor
sourceAnchor 屬性的定義是*在物件存留期間不會變更的屬性*。它可將物件唯一識別為內部部署和 Azure AD 中的相同物件。此屬性也稱為 **immutableId**，兩個名稱可交換使用。

在本主題忠，不可變 (亦即無法變更) 這個字非常重要。由於此屬性的值在設定之後就無法變更，所以請務必挑選可支援您的案例的設計。

此屬性用於下列案例︰

- 建置新的同步處理引擎伺服器，或在災害復原案例後進行重建時，這個屬性會連結 Azure AD 中的現有物件與內部部署的物件。
- 如果您從僅限雲端的身分識別移至已同步處理的身分識別模型，這個屬性可讓物件「完全比對」Azure AD 中的現有物件與內部部署的物件。
- 如果您使用同盟，這個屬性會與 **userPrincipalName** 一起用於宣告來唯一識別使用者。

本主題只會討論與使用者相關的 sourceAnchor。相同的規則適用於所有的物件類型，但僅限於通常有此顧慮的使用者。

### 選取良好的 sourceAnchor 屬性
此屬性值必須遵循下列規則：

- 長度小於 60 個字元
- 不包含特殊字元︰&#92; ! # $ % & * + / = ? ^ &#96; { } | ~ < > ( ) ' ; : , [ ] " @
- 必須是全域唯一的
- 必須是字串、整數或二進位

如果選取的 sourceAnchor 不是字串類型，則 Azure AD Connect 會將此屬性值進行 Base64Encode 處理，以確保不會出現特殊字元。如果您使用 ADFS 以外的其他同盟伺服器，請確定您的伺服器也能夠將此屬性進行 Base64Encode 處理。

sourceAnchor 屬性會區分大小寫。"JohnDoe" 與 "johndoe" 是不同的值。

如果您有單一內部部署樹系，您應該使用的屬性為 **objectGUID**。這也是您在 Azure AD Connect 中使用快速設定時所用的屬性，而且也是 DirSync 所用的屬性。

如果您有多個樹系，而且不會在樹系之間和相同樹系的網域之間移動使用者，則 **objectGUID** 是適合使用的屬性 (即使在此情況下)。

如果您在樹系和網域之間移動使用者，則必須尋找不會變更的屬性。通用的屬性包括 **employeeID**。如果您考慮將含有字母的屬性 (例如 **sAMAccountName**)，請確定屬性值的大小寫 (大寫與小寫) 沒機會變更。不該使用的不合適屬性包括使用者的姓名。結婚或離婚時，此名稱預計會變更，所以不適用於此屬性。這也就是不可能在 Azure AD Connect 安裝精靈中選取 **userPrincipalName**、**mail** 和 **targetAddress** 之類屬性的原因之一。這些屬性也會包含 @ 字元，而 sourceAnchor 中不允許此字元。

萬一完全沒有適當的屬性可用，則必須引進綜合值。例如，可保存 GUID 之類項目的屬性可能適用。在物件建立期間，會建立新的 GUID 建立並於使用者加上戳記。當您移動物件時，請務必同時複製此值的內容。

### 變更 sourceAnchor 屬性
在 Azure AD 中建立物件並同步處理身分識別之後，無法變更 sourceAnchor 屬性值。

基於這個理由，下列限制適用於 Azure AD Connect：

- 只能在初始安裝期間設定 sourceAnchor 屬性。如果您重新執行安裝精靈，這個選項會是唯讀選項。如果您需要變更此值，您就必須解除安裝後重新安裝。
- 如果您安裝其他 Azure AD Connect 伺服器，您必須選取如先前所用的相同 sourceAnchor 屬性。如果您稍早已使用 DirSync 並移至 Azure AD Connect，則必須使用 **objectGUID**，因為這是 DirSync 所用的屬性。
- 如果 sourceAnchor 值在物件匯出至 Azure AD 之後變更，則 Azure AD Connect Sync 將會擲回錯誤，並不允許在修正問題且 sourceAnchor 於來源目錄中變回之前，對此物件進行任何其他變更。

<!---HONumber=August15_HO9-->