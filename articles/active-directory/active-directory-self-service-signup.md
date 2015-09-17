<properties
	pageTitle="什麼是 Azure 的自助式註冊？"
	description="Azure 的自助式註冊、如何管理註冊程序及其作法的概觀。"
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor="LisaToft"/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="08/14/2015" 
	ms.author="stevenpo"/>


# 什麼是 Azure 的自助式註冊？

本主題說明自助式註冊程序 (有時稱為爆紅註冊)，以及如何接管 DNS 網域名稱。

## 為何使用自助式註冊？

- 讓客戶更快取得他們想要的服務。
- 建立服務的電子郵件型 (爆紅) 供應項目。
- 建立以電子郵件為基礎的註冊流程，讓使用者使用其易記的工作電子郵件別名快速地建立身分識別。
- 未受管理的 Azure 租用戶可以成長而成為受管理的租用戶，並重複用於其他服務。

## 詞彙和定義

+ **自助式註冊**：這是使用者用以註冊雲端服務的方法，系統會根據其電子郵件網域在 Azure Active Directory 中自動建立身分識別。
+ **未受管理的 Azure 租用戶**： 這是該身分識別建立所在的目錄。未受管理的租用戶是沒有全域管理員的目錄。
+ **電子郵件驗證的使用者**：這是 Azure AD 中的使用者帳戶類型。在註冊自助式供應項目後自動建立身分識別的使用者，就是所謂的電子郵件驗證的使用者。電子郵件驗證的使用者是加上 creationmethod=EmailVerified 標記之目錄的一般成員。

## 客戶體驗

### 使用者體驗

例如，假設電子郵件為 Dan@BellowsCollege.com 的使用者會透過電子郵件接收機密檔案。檔案已受 Azure 版權管理 (Azure RMS) 保護。但是 Dan 的組織 (Bellows College) 尚未註冊 Azure RMS，也未部署 Active Directory RMS。在此情況下，Dan 可以註冊個人版 RMS 的免費訂閱，以便讀取受保護的檔案。

如果 Dan 是第一個使用 BellowsCollege.com 的電子郵件地址註冊此自助式供應項目的使用者，則會在 Azure AD 中針對 BellowsCollege.com 建立未受管理的租用戶。如果來自 BellowsCollege.com 網域的其他使用者註冊此提供項目或類似的自助式供應項目，他們也會有在 Azure 中相同的未受管理租用戶中建立的電子郵件驗證的使用者帳戶。

### 管理員體驗

擁有未受管理 Azure 租用戶之 DNS 網域名稱的管理員，可以在證明擁有權後接管或合併租用戶。下一節會更詳細地說明管理員體驗，但其摘要如下：

- 當您接管未受管理的 Azure 租用戶時，您只會變成未受管理租用戶的全域管理員。這有時候稱為內部接管。
- 當您合併未受管理的 Azure 租用戶時，您會將未受管理租用戶的 DNS 網域名稱新增至受管理的 Azure 租用戶，而且會建立使用者與資源的對應，以便使用者繼續存取服務而不中斷。這有時候稱為外部接管。

### Microsoft Azure 目錄中會建立什麼？

#### 租用戶

- 針對網域建立 Azure 租用戶 (每個網域一個租用戶)。
- Azure 租用戶目錄沒有全域管理員。

#### 使用者

- 針對註冊的每位使用者，在 Azure 租用戶中建立使用者物件。
- 每個使用者物件都會標示為爆紅。
- 每一位使用者都可以存取他們所註冊的服務。

### 如何針對我擁有的網域宣告自助式 Azure 租用戶？

您可藉由執行網域驗證來宣告自助式 Azure 租用戶。網域驗證會藉由建立 DNS 記錄來證明您擁有該網域。

有兩種方式可進行 Azure 租用戶的 DNS 接管：

- 內部接管 (管理員會探索未受管理的 Azure 租用戶，並且想要轉換成受管理的租用戶)
- 外部接管 (管理員會嘗試將新的網域新增至其受管理的 Azure 租用戶)

您可能會想要驗證您擁有的網域，因為您在使用者執行自助式註冊後接管未受管理的租用戶，或您可能在將新網域加入至現有的受管理租用戶。例如，您擁有名為 contoso.com 的網域，而您想要加入名為 contoso.co.uk 或 contoso.uk 的新網域。

## 什麼是網域接管？  

本節說明如何驗證您擁有的網域。

### 什麼是網域驗證，以及為何使用？

為了在租用戶上執行作業，Azure AD 會要求您驗證 DNS 網域的擁有權。網域驗證可讓您宣告租用戶，將自助式租用戶提升為受管理的租用戶，或將自助式租用戶合併到現有受管理的租用戶中。

## 網域驗證範例

有兩種方式可進行租用戶的 DNS 接管：

+ 內部接管 (例如，管理員會探索未受管理的自助式租用戶，並且想要轉換成受管理的租用戶)
+ 外部接管 (例如，管理員會嘗試將新的網域新增至受管理的租用戶)

### 內部接管 - 將未受管理的自助式租用戶提升為受管理的租用戶

當您執行內部接管時，租用戶會從未受管理的租用戶轉換為受管理的租用戶。您需要完成 DNS 網域名稱驗證，驗證時您會在 DNS 區域中建立 MX 記錄或 TXT 記錄。該動作：

+ 驗證您是否擁有此網域
+ 讓租用戶成為受管理的租用戶
+ 讓您成為租用戶的全域管理員

假設 Bellows College 的 IT 管理員發現學校的使用者已註冊自助式供應項目。身為 DNS 名稱 BellowsCollege.com 的註冊擁有者，IT 管理員可以在 Azure 中驗證 DNS 名稱的擁有權，然後接管未受管理的租用戶。租用戶接著會變成受管理的租用戶，而 IT 管理員會被指派 BellowsCollege.com 目錄的全域管理員角色。

### 外部接款 - 將自助式租用戶合併到現有的受管理租用戶中

在外部接管中，您已有受管理的租用戶，而您希望來自未受管理租用戶的所有使用者和群組都能加入該受管理的租用戶，而非擁有兩個個別的租用戶。

身為受管理租用戶的管理員，您會新增網域，而該網域剛好有與其相關聯的未受管理租用戶。

例如，假設您是 IT 管理員，而且已有 Contoso.com (貴組織註冊的網域名稱) 的受管理租用戶。您會發現貴組織的使用者已使用電子郵件網域名稱 user@contoso.co.uk (這是貴組織擁有的另一個網域名稱) 執行供應項目的自助式服務。這些使用者目前在 contoso.co.uk 的未受管理租用戶中有帳戶。

您不想管理兩個不同的租用戶，所以將 contoso.co.uk 的未受管理租用戶合併到 contoso.com 現有的 IT 受管理租用戶。

外部接管會遵循與內部接管相同的 DNS 驗證程序。差異是：使用者和服務會重新對應至 IT 受管理的租用戶。

#### 執行外部接管的影響為何？

外部接管時會建立使用者與資源的對應，以便使用者繼續存取服務而不中斷。許多應用程式 (包括個人的 RMS) 會妥善處理使用者與資源的對應，而使用者不需變更即可繼續存取這些服務。如果應用程式未有效地處理使用者與資源的對應，外部接管可能會明確遭到封鎖，以免使用者發生不佳的體驗。

#### 服務的租用戶接管支援

目前下列服務支援接管：

- RMS


下列服務很快就會支援接管：

- PowerBI

在外部接管之後，下列項目不需要管理員採取行動來移轉使用者資料。

- SharePoint/OneDrive


## 如何執行 DNS 網域名稱接管

您有幾個選項可用來執行網域驗證 (如果您想要，可執行接管)：

1.  Azure 管理入口網站

	接管是由執行網域新增所觸發。如果網域已存在租用戶，您將會有執行外部接管的選項。

	使用您的認證登入 Azure 入口網站。瀏覽至現有的租用戶，然後瀏覽至 [**新增網域**]。

2.  Office 365

	您可以在 Office 365 中使用[管理網域](https://support.office.com/article/Navigate-to-the-Office-365-Manage-domains-page-026af1f2-0e6d-4f2d-9b33-fd147420fac2/)頁面上的選項來處理您的網域和 DNS 記錄。請參閱[在 Office 365 中驗證您的網域](https://support.office.com/article/Verify-your-domain-in-Office-365-6383f56d-3d09-4dcb-9b41-b5f5a5efd611/)。

3.  Windows PowerShell

	下列步驟是使用 Windows PowerShell 執行驗證的必要步驟。

	步驟 |	要使用的 Cmdlet
	-------	| -------------
	建立認證物件 | Get-Credential
	連接至 Azure AD | Connect-MsolService
	取得網域清單 | Get-MsolDomain
	建立挑戰 | Get-MsolDomainVerificationDns
	建立 DNS 記錄 | 在您的 DNS 伺服器上執行這項操作
	驗證挑戰 | Confirm-MsolEmailVerifiedDomain

例如：

1. 使用用來回應自助式供應項目的認證來連接至 Azure AD：import-module MSOnline $msolcred = get-credential connect-msolservice -credential $msolcred

2. 取得網域清單：

	Get-MsolDomain

3. 然後執行 Get-MsolDomainVerificationDns Cmdlet 來建立挑戰：

	Get-MsolDomainVerificationDns –DomainName *your\_domain\_name* –Mode DnsTxtRecord

	例如：

	Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord

4. 複製從此命令傳回的值 (挑戰)。

	例如：

	MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9

5. 在公用 DNS 命名空間中，建立 DNS txt 記錄，其中包含您在上一個步驟中複製的值。

	此記錄的名稱是父系網域的名稱，所以如果您使用 Windows Server 的 DNS 角色建立此資源記錄，請將記錄名稱空白，只要將此值貼入文字方塊即可。

6. 執行 onfirm-MsolDomain Cmdlet 來驗證挑戰：

	Confirm-MsolEmailVerifiedDomain -DomainName *your\_domain\_name*

	例如：

	Confirm-MsolEmailVerifiedDomain -DomainName contoso.com

成功挑戰會讓您回到提示，但不會產生錯誤。

## 如何控制自助式設定？

系統管理員目前有兩個自助式控制項。可以控制：

- 使用者是否可以透過電子郵件加入租用戶。
- 使用者是否可以自行授權應用程式和服務。


### 如何控制這些功能？

系統管理員可以使用下列 Azure AD Cmdlet Set-MsolCompanySettings 參數設定這些功能：

+ **AllowEmailVerifiedUsers** 控制使用者是否可以建立或加入未受管理的租用戶。如果您將該參數設定為 $false，則沒有任何電子郵件驗證的使用者可以加入租用戶。
+ **AllowAdHocSubscriptions** 控制使用者執行自助式註冊的能力。如果您將該參數為 $false，則沒有任何使用者可以執行自助式註冊。


### 這些控制項如何一起運作？

這兩個參數可合併使用，以定義更精確的自助式註冊控制項。例如，下列命令可讓使用者執行自助式註冊，但僅限於在 Azure AD 中已有帳戶的使用者 (換句話說，需要建立電子郵件驗證帳戶的使用者無法執行自助式註冊)：

	Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true

下列流程圖說明這些參數的各種不同組合以及租用戶和自助式註冊產生的條件。

![][1]

如需如何使用這些參數的詳細資訊和相關範，請參閱 [Set-MsolCompanySettings](https://msdn.microsoft.com/library/azure/dn194127.aspx)。

## 另請參閱

-  [如何安裝和設定 Azure PowerShell](../powershell-install-configure/)

-  [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)

-  [Azure Cmdlet 參考](https://msdn.microsoft.com/library/azure/jj554330.aspx)

-  [Set-MsolCompanySettings](https://msdn.microsoft.com/library/azure/dn194127.aspx)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png

<!---HONumber=August15_HO8-->