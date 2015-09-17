<properties 
	pageTitle="變更 Azure AD Connect 預設組態"
	description="了解如何變更 Azure AD Connect 的預設組態。"
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags 
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2015"
	ms.author="billmath"/>

# 變更 Azure AD Connect 預設組態 


在大部分的情況下，Azure AD Connect 的預設組態就足以將內部部署目錄輕鬆地擴充到雲端。不過，有時，您可能需要修改預設值，並將其調整成符合您組織的商務邏輯。在這些情況下，您可以修改預設組態，不過，在這麼做之前，您需要注意一些事項。

需要變更預設組態時，請執行下列動作：

- 需要修改「非預設」同步處理規則的屬性流程時，請不要進行變更。而是，建立具有較高優先順序 (較低數值) 且包含必要屬性流程的新同步處理規則。
- 使用同步處理規則編輯器，匯出您的自訂同步處理規則。這提供可用來在災害復原情況下輕鬆地重建它們的 PowerShell 指令碼。
- 如果您需要變更「非預設」同步處理規則中的範圍或加入設定，請記錄該項目，並在升級至較新版本的 Azure AD Connect 之後重新套用變更。 

<!---HONumber=August15_HO9-->