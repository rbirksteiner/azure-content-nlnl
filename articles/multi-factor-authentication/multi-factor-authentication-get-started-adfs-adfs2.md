<properties 
	pageTitle="搭配 AD FS 2.0 使用 Azure Multi-Factor Authentication Server 保護雲端和內部部署資源" 
	description="這是說明如何開始使用 Azure MFA 和 AD FS 2.0 的 Azure Multi-Factor Authentication 頁面。" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="bryanla"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/02/2015" 
	ms.author="billmath"/>
# 搭配 AD FS 2.0 使用 Azure Multi-Factor Authentication Server 保護雲端和內部部署資源

如果您的組織與 Azure Active Directory 結盟，而且您有想要保護的內部部署或雲端資源，您可以使用 Azure Multi-Factor Authentication Server 並將它設定為搭配 AD FS 運作，以便對高價值端點觸發 Multi-Factor Authentication。

這份文件將說明如何搭配 AD FS 2.0 使用 Azure Multi-Factor Authentication Server。如需有關搭配 Windows Server 2012 R2 AD FS 使用 Azure Multi-Factor Authentication 的資訊，請參閱[搭配 Windows Server 2012 R2 AD FS 使用 Azure Multi-Factor Authentication Server 保護雲端和內部部署資源](multi-factor-authentication-get-started-adfs-w2k12.md)。


## AD FS 2.0 Proxy
若要使用 Proxy 保護 AD FS 2.0，請在 ADFS Proxy 伺服器上安裝 Azure Multi-Factor Authentication Server，並依照下列步驟設定伺服器。

### 使用 Proxy 保護 AD FS 2.0
<ol>
<li>在 Azure Multi-Factor Authentication Server 內，按一下左功能表中的 [IIS 驗證] 圖示。</li>
<li>按一下 [表單架構] 索引標籤。</li>
<li>按一下 [新增...] 按鈕。</li>

<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/setup1.png)</center>

<li>若要自動偵測使用者名稱、密碼和網域變數，請在 [自動設定表單架構網站] 對話方塊中輸入登入 URL (例如 https://sso.contoso.com/adfs/ls)，然後按一下 [確定]。</li>
<li>如果所有使用者都已或將要匯入伺服器並進行 Multi-Factor Authentication，請核取 [需要進行 Azure Multi-Factor Authentication 使用者比對] 方塊。如果有大量使用者尚未匯入伺服器及/或將免除多重要素驗證，請勿核取此方塊。如需此功能的其他資訊，請參閱說明檔。</li>
<li>如果無法自動偵測頁面變數，請按一下 [自動設定表單架構網站] 對話方塊中的 [手動指定...] 按鈕。</li>
<li>在 [新增表單架構網站] 對話方塊中，於 [提交 URL] 欄位中輸入 ADFS 登入頁面的 URL (例如 https://sso.contoso.com/adfs/ls)，然後輸入應用程式名稱 (選擇性)。應用程式名稱會出現在 Azure Multi-Factor Authentication 報表中，而且可能顯示在簡訊或行動應用程式驗證訊息內。如需 [提交 URL] 的詳細資訊，請參閱說明檔。</li>
<li>將要求格式設定為「POST 或 GET」。</li>
<li>輸入使用者名稱變數 (ctl00$ContentPlaceHolder1$UsernameTextBox) 和密碼變數 (ctl00$ContentPlaceHolder1$PasswordTextBox)。如果表單架構登入頁面顯示網域文字方塊，請輸入網域變數。您可能需要瀏覽至網頁瀏覽器中的登入頁面，在頁面上按一下滑鼠右鍵並選取 [檢視來源]，以尋找登入頁面內的輸入方塊名稱。</li>
<li>如果所有使用者都已或將要匯入伺服器並進行 Multi-Factor Authentication，請核取 [需要進行 Azure Multi-Factor Authentication 使用者比對] 方塊。如果有大量使用者尚未匯入伺服器及/或將免除多重要素驗證，請勿核取此方塊。</li>

<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/manual.png)</center>

<li>按一下 [進階...] 按鈕以檢閱進階設定，包括選取自訂拒絕頁面檔案，使用 Cookie 快取一段時間內網站的成功驗證，以及選取如何驗證主要認證的功能。</li>
<li>因為 ADFS Proxy 伺服器不可能加入網域，您可能會使用 LDAP 連接到您的網域控制站，以便使用者匯入和預先驗證。在 [進階表單架構網站] 對話方塊中，按一下 [主要驗證] 索引標籤並選取 [LDAP 繫結] 作為預先驗證的驗證類型。</li>
<li>完成時，按一下 [確定] 按鈕以返回 [新增表單架構網站] 對話方塊。如需進階設定的詳細資訊，請參閱說明檔。</li>
<li>按一下 [確定] 按鈕以關閉對話方塊。</li>
<li>偵測到或已輸入 URL 和頁面變數後，網站資料就會顯示在表單架構面板中。</li>
<li>按一下 [原生模組] 索引標籤，然後選取伺服器、ADFS Proxy 執行所在的網站 (例如 [預設網站]) 或 ADFS Proxy 應用程式 (例如 "adfs" 之下的 "ls")，以在所需的層級啟用 IIS 外掛程式。</li>
<li>按一下畫面頂端的 [啟用 IIS 驗證] 方塊。</li>
<li>現已啟用 IIS 驗證。不過，您必須設定網域控制站的 LDAP 連接，才能透過 LDAP 執行 Active Directory (AD) 的預先驗證。若要這樣做，請按一下 [目錄整合] 圖示。</li>
<li>在 [設定] 索引標籤上，選取 [使用特定 LDAP 設定] 選項按鈕。</li>

<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap1.png)</center>

<li>按一下 [編輯...] 按鈕。</li>
<li>在 [編輯 LDAP 設定] 對話方塊的欄位中，填入連接到 AD 網域控制站所需的資訊。下表包含欄位的說明。注意：此資訊也包含在 Azure Multi-Factor Authentication Server 說明檔中。</li>

欄位| 說明
:------------- | :-------------
伺服器|輸入執行 LDAP 目錄之伺服器的主機名稱或 IP 位址。也可以指定備份伺服器 (以分號隔開)。<br> **附註：**當 [繫結類型] 為 SSL 時，通常需要完整的主機名稱，而且必須符合 LDAP 目錄伺服器上安裝的 SSL 憑證上的名稱。 
基準 DN|輸入作為所有目錄查詢起點之基準目錄物件的辨別名稱。例如，dc=contoso,dc=com。
繫結類型|選取繫結時可用的適當繫結類型，以搜尋 LDAP 目錄。這適用於匯入、同步處理和使用者名稱解析。<ul><li>**匿名** ‐ 將會執行匿名繫結。不會使用繫結 DN 和繫結密碼。這只適用於 LDAP 目錄允許匿名繫結，而且權限允許查詢適當的記錄和屬性時。</li><li>**簡單** ‐ 將會以純文字方式傳遞繫結 DN 和繫結密碼，以繫結至 LDAP 目錄。這只能用於測試目的，以確認可連上伺服器且繫結帳戶具有適當的存取權。建議在安裝適當的憑證後使用 SSL。</li><li>**SSL** ‐ 將會使用 SSL 加密繫結 DN 和繫結密碼，以繫結至 LDAP 目錄。這需要在 Azure Multi-Factor Authentication Server 信任的 LDAP 目錄伺服器上安裝憑證。</li><li>**Windows** ‐ 繫結使用者名稱和繫結密碼將用來安全地連線到 Active Directory 網域控制站或 ADAM 目錄。如果繫結使用者名稱空白，將使用已登入使用者的帳戶進行繫結。</li></ul> 
繫結使用者名稱|輸入帳戶的使用者記錄辨別名稱，以在繫結至 LDAP 目錄時使用。只有在 [繫結類型] 為 [簡單] 或 [SSL] 時，才會使用繫結辨別名稱。    
繫結密碼|輸入用來繫結至 LDAP 目錄之繫結 DN 或使用者名稱的繫結密碼。若要設定 Multi-Factor Auth Server AdSync 服務的密碼，必須啟用同步處理，而且此服務必須在本機電腦上執行。此密碼會儲存在用於執行 Azure Multi-Factor Authentication Server AdSync 服務的帳戶之下的 [Windows 儲存的使用者和密碼] 中。此密碼也會儲存在用於執行 Multi-Factor Auth Server 使用者介面的帳戶以及用於執行 Azure Multi-Factor Authentication Server 的帳戶之下。注意：由於密碼只會儲存在本機伺服器的 [Windows 儲存的使用者和密碼] 中，所以必須在每個需要存取此密碼的 Multi-Factor Auth Server 上執行此步驟。 
查詢大小限制|指定目錄搜尋將傳回的使用者數目上限的大小限制。此限制應該符合 LDAP 目錄上的設定。對於不支援分頁的大型搜尋，匯入和同步處理將會嘗試以批次方式擷取使用者。如果此處指定的大小限制大於 LDAP 目錄上設定的限制，可能會遺漏部分使用者。 



<li>按一下 [測試] 按鈕來測試 LDAP 連接。</li>

<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap2.png)</center>
<li>如果 LDAP 連接測試成功，請按一下 [確定] 按鈕。</li>
<li>接著，按一下 [公司設定] 圖示，然後選取 [使用者名稱解析] 索引標籤。</li>
<li>選取 [使用 LDAP 唯一識別碼屬性來比對使用者名稱] 按鈕。</li>
<li>如果使用者以"domain\username" 格式將其使用者名稱輸入 ADFS Proxy 登入表單中，伺服器則需能夠在建立 LDAP 查詢時，刪除使用者名稱中的網域。這個動作可透過登錄設定完成。</li>
<li>開啟登錄編輯程式並移至 64 位元伺服器上的 HKEY_LOCAL_MACHINE/SOFTWARE/Wow6432Node/Positive Networks/PhoneFactor。如果在 32 位元伺服器上，請拿掉路徑中的 "Wow6432Node"。建立名為 "UsernameCxz_stripPrefixDomain" 的新 DWORD 登錄機碼，並將值設定為 1。Azure Multi-Factor Authentication 現已保護 ADFS Proxy。確定已將使用者從 Active Directory 匯入伺服器中。如果您想要將內部 IP 位址列入白名單，以便從這些位置登入網站時不需進行雙因素驗證，請參閱以下「信任的 IP」一節。</li>

<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/reg.png)</center>

## 沒有 Proxy 的 AD FS 2.0 Direct

若要在未使用 Proxy 的情況下保護 AD FS，請在 AD FS 伺服器上安裝 Azure Multi-Factor Authentication Server，並依照下列步驟設定伺服器。

### 保護沒有 Proxy 的 AD FS 2.0
<ol>
<li>在 Azure Multi-Factor Authentication Server 內，按一下左功能表中的 [IIS 驗證] 圖示。</li>
<li>按一下 [HTTP] 索引標籤。</li>
<li>按一下 [新增...] 按鈕。</li>
<li>在 [新增基底 URL] 對話方塊的 [基底 URL] 欄位中，輸入執行 HTTP 驗證之 ADFS 網站的 URL (例如 https://sso.domain.com/adfs/ls/auth/integrated) ，並輸入應用程式名稱 (選擇性)。應用程式名稱會出現在 Azure Multi-Factor Authentication 報表中，而且可能顯示在簡訊或行動應用程式驗證訊息內。</li>
<li>如有需要，請調整 [閒置逾時] 和 [最大工作階段] 時間。</li>
<li>如果所有使用者都已或將要匯入伺服器並進行 Multi-Factor Authentication，請核取 [需要進行 Azure Multi-Factor Authentication 使用者比對] 方塊。如果有大量使用者尚未匯入伺服器及/或將免除多重要素驗證，請勿核取此方塊。如需此功能的其他資訊，請參閱說明檔。</li>
<li>如有需要，請核取 Cookie 快取方塊。</li>

<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/noproxy.png)</center>

<li>按一下 [確定] 按鈕。</li>
<li>按一下 [原生模組] 索引標籤，然後選取伺服器、ADFS 執行所在的網站 (例如 [預設網站]) 或 ADFS 應用程式 (例如 "adfs" 之下的 "ls")，以在所需的層級啟用 IIS 外掛程式。</li>
<li>按一下畫面頂端的 [啟用 IIS 驗證] 方塊。Azure Multi-Factor Authentication 現已保護 ADFS。確定已將使用者從 Active Directory 匯入伺服器中。如果您想要將內部 IP 位址列入白名單，以便從這些位置登入網站時不需進行雙因素驗證，請參閱以下「信任的 IP」一節。</li>

## 信任的 IP
信任的 IP 可讓使用者對源自特定 IP 位址或子網路的網站要求略過 Azure Multi-Factor Authentication。例如，您可以讓使用者從辦公室登入時免除 Azure Multi-Factor Authentication。為此，您可以將辦公室子網路指定為信任的 IP 項目。

### 設定信任的 IP

<ol>
<li>在 [IIS 驗證] 區段中，按一下 [信任的 IP] 索引標籤。</li>
<li>按一下 [新增...] 按鈕。</li>
<li>當 [新增信任的 IP] 對話方塊出現時，請選取 [單一 IP]、[IP 範圍] 或 [子網路] 選項按鈕。</li>
<li>輸入應列入白名單中的 IP 位址、IP 位址範圍或子網路。如果輸入子網路，請選取適當的網路遮罩，然後按一下 [確定] 按鈕。現已加入白名單。</li>


<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/trusted.png)</center>

<!---HONumber=July15_HO2-->