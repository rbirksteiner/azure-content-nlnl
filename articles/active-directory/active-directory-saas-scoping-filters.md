<properties
	pageTitle="含範圍篩選器的屬性型 App 佈建"
	description="了解如果某個物件無法滿足您的業務需求，如何使用範圍篩選器來防止實際佈建 App 中支援使用者佈建自動化的物件。"
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


# 含範圍篩選器的屬性型 App 佈建

本節的目標在於說明如何使用範圍篩選器定義將會決定哪些使用者將佈建到應用程式的屬性型規則。





## 子句和範圍群組


![範圍篩選器][1]




範圍篩選器由一或多個**範圍群組**所定義，其中每個範圍群組都包含一或多個**子句**。若要查看特定範圍群組的子句，請按一下群組名稱左側的箭號，將其展開。

**子句**透過評估每個使用者的屬性，決定哪些使用者可以通過範圍篩選器。例如，您可能有一個子句要求使用者的 'state' 屬性等於 New York，也就是說，只有您的紐約使用者將會佈建到應用程式。

![範圍群組名稱][2]



每個**範圍群組**開頭都是一個強制**子句**，如以上的螢幕擷取畫面所示。這個子句只會指出必須先指派使用者給應用程式，您的範圍篩選器才能評估該使用者。您無法刪除或修改這個子句。

您可以按下適當的按鈕，加入新的子句或新的範圍群組。您可以編輯範圍群組的 [範圍群組名稱] 屬性，以便為每個範圍群組提供一個名稱。





## 範圍篩選器的評估方式

在佈建期間，我們會針對您的範圍篩選器來測試每個指派的使用者，以判斷該使用者是否可以存取應用程式。您可以將每個子句視為必須通過的測試，使用者才能避免被篩選掉。

如果您已經定義多個範圍群組，每個使用者都必須至少通過其中一個，才能存取應用程式。不過，在每個範圍群組中，使用者必須通過每個單一子句，才能通過該特定的範圍群組。

換句話說，您可以將範圍群組視為 OR，並將子句視為 AND。例如，請考慮以下的範圍篩選器：


![範圍群組名稱][2]


根據此範圍篩選器，使用者必須滿足下列條件，才能加以佈建：

1. 他們必須被指派給應用程式。

2. 他們必須在工程部門工作

3. 他們必須在舊金山或加拿大工作。


[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]

<!--Image references-->
[1]: ./media/active-directory-saas-scoping-filters/ic782811.png
[2]: ./media/active-directory-saas-scoping-filters/ic782812.png
[3]: ./active-directory-saas-scoping-filters/ic782813.png

<!---HONumber=August15_HO6-->