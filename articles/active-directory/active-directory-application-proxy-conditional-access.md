<properties
	pageTitle="使用 Azure AD 應用程式 Proxy 發佈之應用程式的條件式存取"
	description="說明如何針對您使用 Azure AD 應用程式 Proxy 發佈可供遠端存取的應用程式設定條件式存取。"
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="msStevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/09/2015"
	ms.author="rkarlin"/>

# 使用條件式存取
> [AZURE.NOTE]應用程式 Proxy 是您升級至 Premium 或 Basic 版本的 Azure Active Directory 時才能使用的功能。如需詳細資訊，請參閱 [Azure Active Directory 版本](https://msdn.microsoft.com/library/azure/dn532272.aspx)。

您現在可以啟用存取規則，以授與條件式存取給使用者和群組，以便存取使用應用程式 Proxy 發佈的應用程式。這可讓您︰- 需要每個應用程式的 Multi-Factor Authentication - 只有當使用者不在工作時才需要 Multi-Factor Authentication - 封鎖使用者，使其不在工時無法存取應用程式

這些規則可以套用至所有使用者和群組，或只套用至特定使用者和群組。根據預設，規則會套用至所有可存取應用程式的使用者。不過，也可以將規則限制為指定之安全性群組的使用者成員。當使用者存取使用 OAuth 2.0、OpenID Connect、SAML 或 WS-同盟的同盟應用程式時，就會評估存取規則。此外，當 OAuth 2.0 和 OpenID Connect 以及重新整理權杖用來取得存取權杖時，也會評估存取規則。

## 條件式存取的必要條件

- Azure Active Directory Premium 的訂用帳戶 
- 同盟或受管理的 Azure Active Directory 租用戶 
- 同盟的租用戶需要啟用 Multi-Factor Authentication (MFA) 

![](http://i.imgur.com/rv28onQ.png)

## 設定每個應用程式的 Multi-Factor Authentication
1. 在 Azure 管理入口網站中，以系統管理員身分登入。
2. 移至 Active Directory，並選取您要啟用應用程式 Proxy 所在的目錄。
3. 按一下 [應用程式] 並向下捲動至 [存取規則] 區段。只有使用應用程式 Proxy 發佈並使用同盟驗證的應用程式，才會顯示 [存取規則] 區段。
4. 將 [啟用存取規則] 選為 [開啟]，以啟用規則。
5. 指定將套用規則的使用者和群組。使用 [加入群組] 按鈕來選取將套用存取規則的一或多個群組。此對話方塊也可用來移除選取的群組。若將規則選取為套用至群組，則只會對屬於其中一個指定安全性群組的使用者強制執行存取規則。<br> 若要明確地將安全性群組從規則中排除，請勾選 [除外] 並指定一或多個群組。[除外] 清單中群組的使用者成員不需要執行 Multi-Factor Authentication。<br>如果使用者已設為使用每個使用者的 Multi-Factor Authentication 功能，則這項設定會優先於應用程式的 Multi-Factor Authentication 規則。這表示已設定每個使用者的 Multi-Factor Authentication 的使用者都必須執行 Multi-Factor Authentication，即使他們已從應用程式的 Multi-Factor Authentication 規則中免除。[深入了解 Multi-Factor Authentication 和每個使用者設定](../multi-factor-authentication/multi-factor-authentication.md)。 
6. 選取您要設定的存取規則：
- **需要 Multi-Factor Authentication**︰套用存取規則的使用者必須先完成 Multi-Factor Authentication，才能存取套用規則的應用程式。
- **不在工作時需要 Multi-Factor Authentication**︰嘗試從受信任的 IP 位址存取應用程式的使用者不需要執行 Multi-Factor Authentication。可以在 [Multi-Factor Authentication 設定] 頁面上設定受信任的 IP 位址範圍。
- **不在工作時封鎖存取**︰嘗試從公司網路外部存取應用程式的使用者將無法存取應用程式。


## 設定同盟服務的 MFA
對於同盟的租用戶，Multi-Factor Authentication (MFA) 可能由 Azure Active Directory 或內部部署 AD FS 伺服器執行。根據預設，MFA 會發生在 Azure Active Directory 所裝載的任何頁面上。若要設定內部部署 MFA，請執行 Windows PowerShell 並使用 –SupportsMFA 屬性來設定 Azure AD 模組。下列範例示範如何在 contoso.com 租用戶上使用 [Set-MsolDomainFederationSettings cmdlet](https://msdn.microsoft.com/library/azure/dn194088.aspx) 來啟用內部部署 MFA︰`Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true ` 除了設定這個旗標，同盟租用戶 AD FS 執行個體必須設為執行 Multi-Factor Authentication。依照[內部部署 Microsoft Azure Multi-Factor Authentication](http://technet.microsoft.com/library/dn280946.aspx)的指示進行。
## 其他資源

* [以組織身分註冊 Azure](..sign-up-organization.md)
* [Azure 身分識別](..fundamentals-identity.md)

<!---HONumber=August15_HO8-->