
<properties 
    pageTitle="設定 Azure RemoteApp 的 Active Directory" 
    description="了解如何設定 Active Directory 以使用 Azure RemoteApp。" 
    services="remoteapp" 
    solutions="" documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/28/2015" 
    ms.author="elizapo" />



# 設定 Azure RemoteApp 的 Active Directory


對於 RemoteApp 的混合式收藏，您必須設定內部部署的 Active Directory 網域基礎結構和具備目錄整合的 Azure Active Directory 租用戶 (及選擇性的單一登入)。此外，您必須在內部部署的目錄中建立一些 Active Directory 物件。請使用下列資訊設定內部部署的 Active Directory 和 Azure AD，然後將兩者整合。

## 設定內部部署的 Active Directory
請先從設定內部部署的 Active Directory 開始。您必須識別要使用的 UPN 網域尾碼，然後為 RemoteApp 建立 Active Directory 物件。

還未將 Active Directory 網域服務新增至您的 Windows Server 2012 R2 環境嗎？ 如需如何進行的資訊，請查看[這些指示](https://technet.microsoft.com/library/cc731053.aspx)。
### 檢查及設定 UPN 網域尾碼
如果您的樹系未設定與您將用於 RemoteApp 之 Azure AD 網域相符的 UPN 尾碼，您必須新增該尾碼。判斷是否已設定必要的尾碼：


1. 啟動 [Active Directory 使用者和電腦]。
2.	展開網域名稱，然後按一下 [使用者]。
3.	以滑鼠右鍵按一下 [系統管理員]，然後按一下 [內容]。
4.	在 [帳戶] 索引標籤上，在 [使用者登入名稱] 欄位中查看為此網域設定的 UPN 名稱。
5.	如果看不到與您要用於 RemoteApp 收藏之網域名稱相符的尾碼，請執行下列動作：
	1.	啟動 [Active Directory 網域及信任]。
	2.	以滑鼠右鍵按一下 [Active Directory 網域及信任]，然後按一下 [內容]。
	3.	檢閱尾碼清單。
	4.	在方塊中輸入網域的 FQDN，然後按一下 [新增]，再按一下 [確定]。範例：contoso.com。 

		請勿在此處的尾碼中包含 "@"。

從現在起，當您建立新的使用者時，即可從使用者登入名稱選取新的尾碼。此外，您可以在使用者內容的 [帳戶] 索引標籤上變更現有使用者的尾碼。

如需詳細資訊，請參閱[新增使用者主要名稱尾碼](http://technet.microsoft.com/library/cc772007.aspx)。

### 在 Active Directory 中建立 RemoteApp 的物件
RemoteApp 在內部部署的 Active Directory 中需要兩個物件：


- 服務帳戶，以透過將 RDSH 端點加入內部部署的網域，提供 RemoteApp 程式的網域資源存取權。
- 組織單位 (OU)，以包含 RemoteApp 電腦物件。建議您使用 OU (但非必要) 來隔離帳戶與將用於 RemoteApp 的原則。

請使用下列資訊來建立這兩個物件。

#### 建立服務帳戶：


1. 啟動 [Active Directory 使用者和電腦]。
2.	以滑鼠右鍵按一下 [使用者]，然後按一下 [新增] > [使用者]。
3.	輸入 RemoteApp 服務帳戶的使用者名稱和密碼。

	**注意：**建立 RemoteApp 收藏時，您需要此帳戶資訊。

#### 建立新組織單位 (OU)


1. 在 [Active Directory 使用者和電腦] 中，以滑鼠右鍵按一下您的網域。按一下 [新增] > [組織單位]。
2. 將您為 RemoteApp 建立的服務帳戶新增至這個新 OU。

	若要這樣做，請找到您所建立的服務帳戶。以滑鼠右鍵按一下它，然後按一下 [移動]。選取新的 OU，然後按一下 [確定]。


1. 授與 RemoteApp 服務帳戶在此 OU 新增和刪除電腦的授權：
	1. 從 [Active Directory 使用者和電腦] 嵌入式管理單元，按一下 [檢視] > [進階功能]。這樣會將 [安全性] 索引標籤新增至 [內容] 資訊。
	2. 在 RemoteApp 服務 OU 上按一下滑鼠右鍵，然後按一下 [內容]。
	3. 在 [安全性] 索引標籤上，按一下 [新增]。選取 RemoteApp 服務帳戶使用者物件，然後按一下 [確定]。
	4. 按一下 [進階]。
	5. 在 [權限] 索引標籤上，選取 RemoteApp 服務帳戶，然後按一下 [編輯]。
	6. 在 [套用到] 欄位中選取 [此物件及所有子系物件]。
	7. 在 [權限] 欄位中，選取建立電腦物件和刪除電腦物件旁的 [允許]，然後按一下 [確定]。 
	8. 在其餘的兩個視窗上按一下 [確定]。


## 設定 Azure Active Directory
在設定好內部部署的 Active Directory 後，接著是 Azure AD。您必須建立一個自訂網域，以符合內部部署之網域的 UPN 網域尾碼，並設定目錄整合。混合式收藏僅支援已透過 Windows Server Active Directory 部署同步 (使用類似 DirSync 的工具) 的 Azure Active Directory 帳戶；更具體而言，就是已同步「密碼同步化」選項，或已同步設定 Active Directory Federation Services (AD FS) 同盟。

請使用下列資訊來設定 Azure Active Directory


- [新增網域](http://technet.microsoft.com/library/hh969247.aspx) – 請使用此資訊來新增一個網域，以符合內部部署之 Active Directory 網域的 UPN 網域尾碼。
- [目錄整合](http://technet.microsoft.com/library/jj573653.aspx) – 請使用此資訊來選擇目錄整合選項 – [含密碼同步化的 DirSync](http://technet.microsoft.com/library/dn441214.aspx) 或[含同盟的 DirSync](http://technet.microsoft.com/library/dn441213.aspx)。

您也可以設定[多重要素驗證 (MFA)](http://technet.microsoft.com/library/dn249466.aspx)。

## 設定目錄同步化時有問題？

如果您設定目錄同步化時遇到問題，請檢查下列項目：

- 您使用的是最新版本的 Azure 目錄同步化工具。 
-	在管理入口網站中的 [Active Directory] -> [預設目錄] -> [網域] 下，您已經新增您自訂的網域 (例如 mydomain.com) 並且將它設定為主要網域。
-	在 [Active Directory] -> [預設目錄] -> [使用者] 下，您在該網域下新增新的使用者 (例如 myAzureSyncUser@mydomain.com))。
-	在 Active Directory 中的網域，您已新增新的網域使用者，並且將它設定為 Enterprise Admin 的成員 (例如 myDomainSyncUser@mydomain.com))。

現在啟動 Azure 目錄同步化工具，然後在第一次提示時使用 **myAzureSyncUser@mydomain.com** 憑證 (Microsoft Azure Active Directory 管理員憑證)，然後在第二次提示時使用 **myDomainSyncUser@mydomain.com**。
 

<!---HONumber=62-->