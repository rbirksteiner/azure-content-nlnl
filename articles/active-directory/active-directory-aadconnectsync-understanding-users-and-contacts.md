<properties
	pageTitle="Azure AD Connect 同步處理：了解使用者和連絡人"
	description="說明 Azure AD Connect 同步處理中的使用者和連絡人。"
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


# Azure AD Connect 同步處理：了解使用者和連絡人

您可能有幾種不同的原因，而擁有多個 Active Directory 樹系並且具有幾種不同的部署拓撲。常見的模型包括合併與收購之後的帳戶-資源部署與 GAL 同步處理的樹系。雖然有單純的模型，但混合模型也同樣常見。Azure AD Connect 同步處理中的預設組態不會採用任何特定的模型，但是根據在安裝指南中選取使用者比對的方式，可以觀察到不同的行為。

在本主題中，我們將詳細解說預設組態在某些拓撲中的運作方式。我們將詳細解說組態，以及可用來查看組態的同步處理規則編輯器。

組態假設幾個一般規則：

- 無論我們以何種順序讀取來源 Active Directory，最後的結果一律都會相同。
- 使用中的帳戶一律會提供登入資訊，包括 **userPrincipalName** 和 **sourceAnchor**。
- 停用的帳戶將會提供 userPrincipalName 和 sourceAnchor，除非帳戶是連結的信箱。
- 具有連結信箱的帳戶永遠不會用於 userPrincipalName 和 sourceAnchor。它假設稍後就會找到使用中的帳戶。
- 連絡人物件可能會佈建到 Azure AD 成為連絡人或使用者。一直要到處理了所有來源 Active Directory 樹系之後，您才會知道物件成為何者。







## 連絡人

在合併與收購時使用 GALSync 解決方案橋接兩個或多個 Exchange 樹系之後，常會有多個連絡人代表不同樹系中的某個使用者。連絡人物件一律從連接器空間使用 mail 屬性加入 Metaverse。如果已經有具相同郵件地址的連絡人物件或使用者物件，則物件會一起加入。這設定在規則 **In from AD – Contact Join** 中。另外還有一個名為 **In from AD – Contact Common** 的規則，其屬性流程是使用常數 **Contact** 提供給 Metaverse 屬性 **sourceObjectType**。此規則的優先順序非常低，因此如果有任何使用者物件加入同一個 Metaverse 物件，則規則 In from AD – User Common 會提供值 User 給這個屬性。有了這項規則，如果沒有使用者加入，此屬性的值就會是 Contact，如果至少找到了一個使用者，則屬性的值就會是 User。

當佈建物件到 Azure AD 時，如果 Metaverse 屬性 **sourceObjectType** 設為 **Contact**，輸出規則 **Out to AAD – Contact Join** 就會建立連絡人物件。如果此屬性設定為 **User**，則規則 **Out to AAD – User Join** 將會改為建立使用者物件。當有多個來源 Active Directory 匯入並同步處理時，可以將物件從 Contact 升級為 User。

例如，在 GALSync 拓撲中，當我們匯入第一個樹系時，我們會在第二個樹系中找到每個人的連絡人物件。這會在 AAD 連接器中暫存新的連絡人物件。當我們之後匯入和同步處理第二個樹系時，我們會找到真正的使用者，並將他們加入現有的 Metaverse 物件。然後我們就可以刪除 AAD 中的連絡人物件，然後改為建立新的使用者物件。

如果您有一個拓撲，其中使用者以連絡人代表，請確定您在安裝指南中選取根據 mail 屬性比對使用者。如果您選取另一個選項，則您的組態就會與順序有關。連絡人物件永遠會跟隨著 mail 屬性，但如果在安裝指南中選取了此選項，使用者物件就只會跟隨著 mail 屬性。如果在匯入使用者物件之前先匯入連絡人物件，您在 Metaverse 中就會得到具有相同 mail 屬性的兩種不同物件。在匯出到 Azure AD 期間，將會擲回錯誤。此行為是刻意設計，這樣會指出資料有錯誤，或者在安裝期間未正確識別拓撲。





## 停用的帳戶

停用的帳戶也同樣會同步處理到 Azure AD。停用的帳戶在 Exchange 中通常代表資源，例如會議室。例外狀況是具有連結信箱的使用者。如先前所述，這些使用者將永遠不會佈建帳戶到 Azure AD。

這個假設是，如果找到停用的使用者帳戶，我們之後就不會找到另一個使用中的帳戶，而物件會使用找到的 userPrincipalName 和 sourceAnchor 佈建到 Azure AD。如果有另一個使用中的帳戶加入相同的 Metaverse 物件，則會使用其 userPrincipalName 和 sourceAnchor。





## 變更 sourceAnchor

當物件匯出到 Azure AD 之後，則不允許再變更 sourceAnchor。當物件匯出之後，Metaverse 屬性 **cloudSourceAnchor** 就會設為 Azure AD 所接受的 **sourceAnchor** 值。如果 **sourceAnchor** 經過變更且不符合 **cloudSourceAnchor**，則規則 **Out to AAD – User Join** 將會擲回「sourceAnchor 屬性已經變更」錯誤。在此情況下，必須先更正組態或資料，讓 Metaverse 中再度具有相同的 sourceAnchor，才能再次同步處理物件。



## 其他資源

* [Azure AD Connect 同步處理：自訂同步處理選項](active-directory-aadconnectsync-whatis.md)
* [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)

 
<!--Image references-->

<!---HONumber=August15_HO6-->