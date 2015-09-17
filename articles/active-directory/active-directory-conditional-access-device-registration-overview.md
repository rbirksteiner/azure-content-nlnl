<properties
	pageTitle="Azure Active Directory 裝置註冊概觀 | Microsoft Azure"
	description="是裝置型條件式存取案例的基礎。註冊裝置時，Azure Active Directory 裝置註冊會利用當使用者登入時用來驗證裝置的身分識別來佈建裝置。"
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/02/2015"
	ms.author="femila"/>

# Azure Active Directory 裝置註冊概觀

Azure Active Directory 裝置註冊是裝置型條件式存取案例的基礎。註冊裝置時，Azure Active Directory 裝置註冊會利用當使用者登入時用來驗證裝置的身分識別來佈建裝置。然後已驗證的裝置和裝置的屬性即可用來對裝載於雲端和內部部署的應用程式，強制執行條件式存取原則。與 Intune 這類的行動裝置管理解決方案結合時，將會以裝置的其他相關資訊更新 Azure Active Directory 中的裝置屬性。這可讓您建立條件式存取規則，強制使從裝置的存取符合您的安全性和相容性標準。Azure Active Directory 裝置註冊可用於您的 Azure Active Directory。此服務包含 iOS、Android 和 Windows 裝置的支援。利用 Azure Active Directory 裝置註冊的個別案例可能會有更明確的需求和平台支援。深入了解現在可供您使用的特定案例。

## Azure Active Directory 裝置註冊所啟用的案例

Azure AD 裝置註冊可以視為各種案例的基礎。一般而言，此服務包含 iOS、Android 和 Windows 裝置的支援。利用 Azure AD 裝置註冊的個別案例可能會有更明確的需求和平台支援。這些案例如下︰內部部署裝載之應用程式的條件式存取：您可以使用已註冊的裝置搭配適用於已設為使用 AD FS with Windows Server 2012 R2 之應用程式的存取原則。如需有關設定內部部署之條件式存取的詳細資訊，請參閱使用 Azure Active Directory 裝置註冊設定內部部署條件式存取。

適用於包含 Microsoft Intune 之 Office 365 應用程式的條件式存取︰IT 管理員可以佈建條件式存取裝置原則來保護公司資源，同時允許相容裝置上的資訊工作者存取服務。如需詳細資訊，請參閱 Office 365 服務的條件式存取裝置原則

##設定 Azure Active Directory 裝置註冊

下列設定適用於 Azure Active Directory 裝置註冊服務：在 Azure 入口網站啟用 Azure AD 裝置註冊。

Windows 裝置會藉由尋找知名 DNS 記錄來探索服務。您必須設定公司 DNS，讓 Windows 7 和 Windows 8.1 裝置才可以探索和使用服務。

您可以使用 Azure Active Directory 中的系統管理員入口網站，檢視並啟用/停用已註冊的裝置。

## 啟用 Azure Active Directory 裝置註冊
下一節描述如何為您的目錄啟用 Azure Active Directory 裝置註冊服務。
啟用 Azure Active Directory 裝置註冊服務
-------------------------------------------------------------
1. 以系統管理員身分登入 Azure 入口網站。
1. 在左窗格中選取 [Active Directory]。
1. 在 [目錄] 索引標籤中，選取您的目錄。
1. 選取 [設定] 索引標籤。
1. 捲動至名為[裝置] 的區段。
1. 針對 [使用者可以使用「加入工作場所」裝置] 選取 [全部]。
1. 選取您要依每位使用者授權的裝置數目上限。

>[AZURE.NOTE]Microsoft Intune 註冊或 Office 365 的行動裝置管理需要加入工作場所。如果您已設定任一項服務，則會選取 [全部] 並停用 [無] 按鈕。


根據預設，服務未啟用雙因素驗證。不過，建議在註冊裝置時使用雙因素驗證。

* 在要求此服務的雙因素驗證之前，您必須先在 Azure Active Directory 中設定雙因素驗證提供者以及針對 Multi-Factor Authentication 設定您的使用者帳戶，請參閱將 Multi-Factor Authentication 新增至 Azure Active Directory

* 如果您搭配使用 AD FS 與 Windows Server 2012 R2，您必須在 AD FS 中設定雙因素驗證模組，請參閱搭配使用 Multi-Factor Authentication 與 Active Directory Federation Services

## 設定 Azure Active Directory 裝置註冊探索
Windows 7 和 Windows 8.1 裝置會藉由結合使用者帳戶名稱與知名裝置註冊伺服器名稱，探索裝置註冊伺服器。您必須建立 DNS CNAME 記錄，該記錄指向與您的 Azure Active Directory 裝置註冊服務相關聯的 A 記錄。CNAME 記錄必須使用知名的前置詞 enterpriseregistration，其後面接著貴組織的使用者帳戶所使用的 UPN 尾碼。如果您的組織使用多個 UPN 尾碼，則必須在 DNS 中建立多個 CNAME 記錄。

例如，如果您在貴組織使用兩個名為 @contoso.com 和 @region.contoso.com 的 UPN 尾碼，您將建立下列 DNS 記錄。
 
| 項目 | 類型 | 位址 |
|-------------------------------------------|-------|------------------------------------|
| enterpriseregistration.contoso.com | CNAME | enterpriseregistration.windows.net |
| enterpriseregistration.region.contoso.com | CNAME | enterpriseregistration.windows.net |

## 在 Azure Active Directory 中檢視和管理裝置物件
1. 從 Azure 系統管理員入口網站，您可以檢視、封鎖和解除封鎖裝置。已封鎖的裝置將無法再存取已設為僅允許已註冊裝置的應用程式。
1. 以系統管理員身分登入 Microsoft Azure 入口網站。
1. 在左窗格中選取 [Active Directory]。
1. 選取您的目錄。
1. 選取 [使用者] 索引標籤。然後選取使用者以檢視其裝置。
1. 選取 [裝置] 索引標籤。
1. 從下拉式功能表中選取 [已註冊的裝置]。
1. 您可以在此檢視、封鎖或解除封鎖使用者已註冊的裝置。 

## 其他主題

您可以向 Azure AD 裝置註冊註冊 Windows 7 和 Windows 8.1 加入網域的裝置。下列主題提供有關在 Windows 7 和 Windows 8.1 裝置上設定裝置註冊所需之先決條件和步驟的詳細資訊。自動向 Azure Active Directory 註冊加入網域的 Windows 裝置

<!---HONumber=August15_HO9-->