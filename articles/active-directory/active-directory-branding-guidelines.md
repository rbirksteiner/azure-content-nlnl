<properties
   pageTitle="應用程式的商標指導方針"
   description="Azure Active Directory 開發人員導向資源的完整指南"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="04/29/2015"
   ms.author="mbaldwin"/>


# 應用程式的商標指導方針


本主題討論使用 Azure Active Directory 開發應用程式時，您應該使用的商標指導方針。當客戶想要使用其工作或學校帳戶 (在 Azure AD 中管理) 來註冊和登入您的應用程式時，這些指導方針可協助引導客戶。

## 個人帳戶與 Microsoft 提供的工作或學校帳戶的比較

Microsoft 管理兩種類型的使用者帳戶：

- **個人帳戶** (之前稱為 Windows Live ID)。這些帳戶代表*個別*使用者和 Microsoft 之間的關聯性，用於存取來自 Microsoft 的消費型裝置和服務。這些帳戶主要供個人使用。

- **工作或學校帳戶。** 這些是使用 Azure Active Directory 的組織交由 Microsoft 代為管理的帳戶。這些帳戶用來從 Microsoft 登入 Office 365 和其他商務服務。

Microsoft 工作或學校帳戶通常由其組織 (公司、 學校、政府機構) 指派給使用者 (員工、學生、聯邦員工)。這些帳戶可能直接在雲端中管理 (在 Azure AD 平台)，或從內部部署目錄 (例如 Windows Server Active Directory) 同步處理到 Azure AD。Microsoft 是工作或學校帳戶的*保管者*，但這些帳戶由組織擁有和控制。

## 在您的應用程式中提及 Azure AD 帳戶

Microsoft 不會在 Azure 或 Active Directory 品牌名稱上公開使用者，所以您也不應該如此。

- 使用者登入之後，您應該盡可能使用組織的名稱和標誌。這比使用「您的組織」之類的通稱更好。

- 當使用者未登入時，您應該將他們的帳戶稱為「工作或學校帳戶」，並使用 Microsoft 標誌來表達這些帳戶由 Microsoft 管理。請勿使用「企業帳戶」、「商務帳戶」或「公司帳戶」之類的詞彙，這會造成使用者混淆。

## 使用者帳戶象形圖
在這些指導方針的較早版本中，我們建議使用「藍色徽章」象形圖。根據使用者和開發人員的意見反應，我們現在建議改用 Microsoft 標誌。這有助於使用者了解，他們用於 Office 365 或其他 Microsoft 商務服務的帳戶，也可以重複用來登入您的應用程式。

## 使用 Azure AD 來註冊和登入

您的應用程式可能將註冊和登入劃分成不同的路徑，下列各節提供這兩個案例的視覺化導引。

**如果您的應用程式支援使用者註冊 (例如免費試用版或免費增值模式)**：您可以顯示 [**登入**] 按鈕，讓使用者利用 Microsoft 提供的工作或學校帳戶來存取您的應用程式。他們第一次存取您的應用程式時，Azure AD 會顯示同意提示。

**如果您的應用程式需要唯有系統管理員才能同意的權限，或您的應用程式需要組織授權**：您應該將系統管理員擷取與使用者登入分開。 **取得此應用程式] 按鈕**會將系統管理員重新導向登入，然後要求他們代表組織中使用者來表示同意。額外的好處是避免您的應用程式顯示使用者同意提示。

## 取得應用程式的視覺化導引

[取得應用程式] 連結必須將使用者重新導向 Azure AD 授與存取權 (授權) 頁面，讓組織的系統管理員可授權您的應用程式來存取由 Microsoft 管理的組織資料。[新增、更新及移除應用程式](https://msdn.microsoft.com/library/azure/dn132599.aspx)主題中討論如何要求存取權的詳細資料。

系統管理員同意您的應用程式之後，他們可以選擇將它加入使用者的 Office 365 應用程式啟動器體驗 (可從非正式管道和 [https://portal.office.com/myapps](https://portal.office.com/myapps) 存取)。如果您想要宣傳這項功能，您可以使用「將此應用程式加入至您的組織」之類的詞彙，並顯示如下的按鈕：

![應用程式類型和案例](./media/active-directory-branding-guidelines/add-to-my-org.png)
  
不過，我們建議您撰寫說明文字，而不要依賴按鈕。例如：
> *如果您已經使用 Office 365 或 Microsoft 的其他商務服務，您可以直接授權 <your_app_name> 存取您的組織資料。這可讓使用者利用其現有的工作帳戶存取 <your_app_name>。*


## 登入的視覺化導引
您的應用程式應該顯示登入按鈕，將使用者重新導向登入端點，此端點符合您用來與 Azure AD 整合的通訊協定。下節提供該按鈕外觀的詳細資料。

### 象形圖和「公司或學校帳戶」 
結合 Microsoft 標誌與通用的「工作或學校」詞彙，才能在應用程式可能支援的其他身分識別提供者之中，彰顯 Azure AD 的獨特性。如果沒有足夠的空間顯示「工作或學校帳戶」，可以縮短為「工作帳戶」。
 
![應用程式類型和案例](./media/active-directory-branding-guidelines/work-or-school-account.png)

![應用程式類型和案例](./media/active-directory-branding-guidelines/work-account.png)

您也可以提供其他說明，幫助使用者了解他們是否可使用此按鈕：

![應用程式類型和案例](./media/active-directory-branding-guidelines/work-account-with-explaination.png)
 
## 商標的建議與禁忌
**建議**使用「工作或學校帳戶」結合 Microsoft 標誌，以代表使用 Azure AD 登入。如果空間有限，您可以只顯示「工作帳戶」，但**禁止**使用「企業帳戶」、「商務帳戶」或「公司帳戶」之類的其他詞彙。

**禁止**使用 "Office 365 ID" 或 "Azure ID"。Office 365 也是 Microsoft 提供的消費型產品名稱，並不使用 Azure AD 進行驗證。

**禁止**改變 Microsoft 標誌。

**禁止**在 Azure 或 Active Directory 品牌上公開使用者。不過，對於開發人員、IT 專業人員和系統管理員，則可以使用這些詞彙。

## 導覽的建議與禁忌

**建議**提供方法讓使用者登出並切換至另一個使用者帳戶。雖然大部分的人只有一個 Microsoft/Facebook/Google/Twitter 個人帳戶，但往往與多個組織相關聯。我們即將支援多重登入使用者。

## 在您的應用程式中同時支援 Azure AD 與 Microsoft 帳戶

如果您的應用程式同時支援 Azure AD 與 Microsoft 帳戶，則您的應用程式中需要包含兩個不同的登入按鈕。我們正在積極進行更新，將可讓您只需要整合一次，就能同時支援 Microsoft 提供的個人和工作帳戶。此更新推出時，您將能夠在應用程式中只顯示一個 [使用 Microsoft 登入] 按鈕。

 

<!---HONumber=62-->