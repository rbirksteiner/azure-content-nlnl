<properties
	pageTitle="疑難排解應用程式 Proxy"
	description="說明如何疑難排解 Azure AD 應用程式 Proxy 中的錯誤。"
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2015"
	ms.author="rkarlin"/>



# 疑難排解應用程式 Proxy


> [AZURE.IMPORTANT]應用程式 Proxy 是您升級至 Premium 或 Basic 版本的 Azure Active Directory 時才能使用的功能。如需詳細資訊，請參閱 [Azure Active Directory 版本](active-directory-editions.md)。

如果在存取已發佈的應用程式或發佈應用程式時發生錯誤，請檢查下列選項以查看 Microsoft Azure AD 應用程式 Proxy 是否運作正常︰

- 開啟 [Windows 服務] 主控台並確認 [Microsoft AAD 應用程式 Proxy 連接器] 服務已啟用並在執行中。您也可以查看應用程式 Proxy 服務屬性頁面，如下圖所示：

![Microsoft AAD 應用程式 Proxy 連接器屬性螢幕擷取畫面][1]

- 開啟 [事件檢視器] 並尋找與應用程式 Proxy 連接器相關的事件 (位於 [應用程式及服務記錄檔] > [Microsoft] > [AadApplicationProxy] > [Connector] > [Admin] 之下)。
- 如有需要，開啟分析和偵錯記錄檔及開啟應用程式 Proxy 連接器工作階段記錄檔，即可取得更詳細的記錄檔，如下圖所示：

![應用程式 Proxy 連接器工作階段記錄檔螢幕擷取畫面][2]


## 一般錯誤

錯誤 | 說明 | 解決方案
--- | --- | ---
無法存取此公司應用程式。您未獲得授權存取此應用程式。授權失敗。務必將此應用程式的存取權指派給使用者。 | 您可能尚未對此應用程式指派使用者。 | 移至 [應用程式] 索引標籤，在 [使用者及群組] 之下，將此使用者或使用者群組指派給此應用程式。
無法存取此公司應用程式。您未獲得授權存取此應用程式。授權失敗。確定使用者有 Azure Active Directory Premium 或 Basic 的授權。 | 如果訂閱者的系統管理員未明確將Premium/Basic 授權指派給嘗試存取您發佈之應用程式的使用者，則使用者在嘗試存取此應用程式時可能會發生此錯誤。 | 移至訂閱者的 Active Directory [授權] 索引標籤，並確定此使用者或使用者群組已獲得 Premium 或 Basic 授權。


## 連接器疑難排解
如果在連接器精靈安裝期間註冊失敗，您可以在 [Windows 記錄] > [應用程式] 之下尋找事件記錄檔，或執行下列 Windows PowerShell 命令，以檢視失敗的原因。

    Get-EventLog application –source “Microsoft AAD Application Proxy Connector” –EntryType “Error” –Newest 1

| 錯誤 | 說明 | 解決方案 |
| --- | --- | --- |
| 連接器註冊失敗︰確定您已在 Azure 管理入口網站中啟用應用程式 Proxy，並已正確地輸入您的 Active Directory 使用者名稱和密碼。錯誤︰「發生一或多個錯誤。」 | 您可以關閉註冊視窗，而不需執行登入 Azure AD。 | 再次執行連接器精靈並註冊連接器。 |
| 連接器註冊失敗︰確定您已在 Azure 管理入口網站中啟用應用程式 Proxy，並已正確地輸入您的 Active Directory 使用者名稱和密碼。錯誤︰「AADSTS50001: 資源 `https://proxy.cloudwebappproxy.net/registerapp` 已停用。」 | 應用程式 Proxy 已停用。 | 先確定您已在 Azure AD 入口網站中啟用應用程式 Proxy，再嘗試註冊連接器。如需啟用應用程式 Proxy 的詳細資訊，請參閱[啟用應用程式 Proxy 服務](active-directory-application-proxy-enable.md)。 |
| 連接器註冊失敗︰確定您已在 Azure 管理入口網站中啟用應用程式 Proxy，並已正確地輸入您的 Active Directory 使用者名稱和密碼。錯誤︰「發生一或多個錯誤。」 | 如果註冊視窗隨即開啟，然後立即關閉，而不讓您登入，您可能會發生此錯誤。您的系統上發生某種形式的網路錯誤時，就會發生此錯誤。 | 確定有可能從瀏覽器連線至公用網站，並如[應用程式 Proxy 先決條件](active-directory-application-proxy-enable.md)所指定開啟連接埠。 |
| 連接器註冊失敗︰確定您的電腦已連線到網際網路。錯誤︰「沒有任何在 `https://connector.msappproxy.net:9090/register/RegisterConnector` 接聽的端點可以接受此訊息。這通常是由不正確的位址或 SOAP 動作所造成。如需詳細資訊，請參閱 InnerException (如果有的話)。」 | 如果您使用 Azure AD 使用者名稱和密碼進行登入，但之後收到這個錯誤，可能是所有超過 8081 的連接埠遭到封鎖。 | 確定必要的連接埠已開啟。如需詳細資訊，請參閱[應用程式 Proxy 先決條件](active-directory-application-proxy-enable.md)。 |
| 清除錯誤會顯示在註冊視窗中。無法繼續 - 只能關閉視窗。 | 您輸入的使用者名稱或密碼錯誤。 | 請再試一次。 |
| 連接器註冊失敗︰確定您已在 Azure 管理入口網站中啟用應用程式 Proxy，並已正確地輸入您的 Active Directory 使用者名稱和密碼。錯誤︰「AADSTS50059: 在要求中找不到租用戶識別資訊，或任何提供的認證均未隱含租用戶識別資訊，而且依服務原則 URI 的搜尋已失敗。 | 您嘗試使用 Microsoft 帳戶進行登入，而非使用屬於您嘗試存取之目錄的組織識別碼的網域。 | 確定系統管理員屬於與租用戶網域相同的網域名稱，例如，若 Azure AD 網域是 contoso.com，則系統管理員應該是 admin@contoso.com。 |
| 無法擷取目前的執行原則以供執行 PowerShell 指令碼 | 如果連接器安裝失敗，請檢查以確定未停用 PowerShell 執行原則。 | 開啟 [群組原則編輯器]。移至 [電腦設定] > [系統管理範本] > [Windows 元件] > [Windows PowerShell]，連按兩下 [開啟指令碼執行]。這可設為 [未設定] 或 [已啟用]。如果設為 [已啟用]，請確定 [選項] 之下的 [執行原則] 設定為 [允許本機指令碼和遠端簽署指令碼] 或 [允許所有指令碼]。 |
| 連接器無法下載組態 | 連接器用於驗證的用戶端憑證已過期。如果您將連接器安裝在 Proxy 後面，也可能發生這種情形。在此情況下，連接器無法存取網際網路，且無法將應用程式提供給遠端使用者。 | 在 Windows PowerShell 中使用 `Register-AppProxyConnector` Cmdlet，手動更新信任。如果您的連接器位於 Proxy 後面，則必須將網際網路存取權授與「網路服務」和「本機系統」連接器帳戶。授與 Proxy 的存取權或將其設為略過 Proxy，即可完成此作業。 |
| 連接器註冊失敗︰確定您是 Active Directory 的全域管理員以註冊連接器。錯誤︰「註冊要求被拒絕。」 | 您嘗試用以登入的別名不是此網域的系統管理員。您的連接器永遠都會針對擁有使用者網域的目錄進行安裝。 | 確定您嘗試用以登入的系統管理員身分具有 Azure AD 租用戶的全域權限。|


## Kerberos 錯誤

| 錯誤 | 說明 | 解決方案 |
| --- | --- | --- |
| 無法擷取目前的執行原則以供執行 PowerShell 指令碼 | 如果連接器安裝失敗，請檢查以確定未停用 PowerShell 執行原則。 | 開啟 [群組原則編輯器]。移至 [電腦設定] > [系統管理範本] > [Windows 元件] > [Windows PowerShell]，連按兩下 [開啟指令碼執行]。這可設為 [未設定] 或 [已啟用]。如果設為 [已啟用]，請確定 [選項] 之下的 [執行原則] 設定為 [允許本機指令碼和遠端簽署指令碼] 或 [允許所有指令碼]。 |
| 12008 - Azure AD 已超出後端伺服器允許的 Kerberos 驗證嘗試次數上限。 | 此事件可能表示 Azure AD 與後端應用程式伺服器之間的設定不正確，或兩台電腦上的日期和時間設定有問題。 | 後端伺服器拒絕了 Azure AD 所建立的 Kerberos 票證。確認 Azure AD 和後端應用程式伺服器的組態設定正確。確定 Azure AD 與後端應用程式伺服器的日期和時間設定已同步。 |
| 13016 - 因為邊緣權杖或存取 cookie 中沒有 UPN，Azure AD 無法代表使用者擷取 Kerberos 票證。 | STS 組態有問題。 | 在 STS 中修正 UPN 宣告設定。 |
| 13019 - 因為下列一般 API 錯誤，Azure AD 無法代表使用者擷取 Kerberos 票證。 | 此事件可能表示 Azure AD 與網域控制站伺服器之間的設定不正確，或兩台電腦上的日期和時間設定有問題。 | 網域控制站拒絕了 Azure AD 所建立的 Kerberos 票證。確認 Azure AD 和後端應用程式伺服器的組態設定正確，尤其是 SPN 組態。確定 Azure AD 的網域已加入至與網域控制站相同的網域，確保網域控制站建立 Azure AD 的信任。確定 Azure AD 與網域控制站的日期和時間設定已同步。 |
| 13020 - 因為未定義後端伺服器 SPN，Azure AD 無法代表使用者擷取 Kerberos 票證。 | 此事件可能表示 Azure AD 與網域控制站伺服器之間的設定不正確，或兩台電腦上的日期和時間設定有問題。 | 網域控制站拒絕了 Azure AD 所建立的 Kerberos 票證。確認 Azure AD 和後端應用程式伺服器的組態設定正確，尤其是 SPN 組態。確定 Azure AD 的網域已加入至與網域控制站相同的網域，確保網域控制站建立 Azure AD 的信任。確定 Azure AD 與網域控制站的日期和時間設定已同步。 |
| 13022 - 因為後端伺服器以 HTTP 401 錯誤回應 Kerberos 驗證嘗試，Azure AD 便無法驗證使用者。 | 此事件可能表示 Azure AD 與後端應用程式伺服器之間的設定不正確，或兩台電腦上的日期和時間設定有問題。 | 後端伺服器拒絕了 Azure AD 所建立的 Kerberos 票證。確認 Azure AD 和後端應用程式伺服器的組態設定正確。確定 Azure AD 與後端應用程式伺服器的日期和時間設定已同步。 |
| 網站無法顯示頁面。 | 如果應用程式是 IWA 應用程式，則使用者在嘗試存取此應用程式時可能會發生此錯誤，為此應用程式定義的 SPN 可能不正確。 | 對於 IWA 應用程式︰確定為此應用程式設定的 SPN 正確無誤。 |
| 網站無法顯示頁面。 | 如果應用程式是 OWA 應用程式，則使用者在嘗試存取此應用程式時可能會發生此錯誤，這可能是由下列下列其中一項所造成︰ | 相應減輕的步驟︰ |
| | 為此應用程式定義的 SPN 不正確。 | 確定為此應用程式設定的 SPN 正確無誤。 |
| | 嘗試存取應用程式的使用者使用 Microsoft 帳戶，而不是使用適當的公司帳戶進行登入，或使用者是來賓使用者。 | 確定使用者是使用符合已發佈應用程式之網域的公司帳戶進行登入。Microsoft 帳戶使用者和來賓無法存取 IWA 應用程式。 |
| | 未在內部部署端針對此應用程式正確地定義嘗試存取應用程式的使用者。 | 確定此使用者具有適當的權限，如在內部部署電腦上針對此後端應用程式所定義的權限。 |
| 無法存取此公司應用程式。您未獲得授權存取此應用程式。授權失敗。務必將此應用程式的存取權指派給使用者。 | 如果嘗試存取應用程式的使用者使用 Microsoft 帳戶，而不是使用適當的公司帳戶進行登入，或使用者是來賓使用者，則使用者在嘗試存取此應用程式時會發生此錯誤。 | Microsoft 帳戶使用者和來賓無法存取 IWA 應用程式。確定使用者是使用符合已發佈應用程式之網域的公司帳戶進行登入。 |
| 無法立即存取此公司應用程式。請稍後再試...連接器已逾時。 | 如果未在內部部署端針對此應用程式正確地定義嘗試存取應用程式的使用者，則使用者在嘗試存取此應用程式時可能會發生此錯誤。 | 確定此使用者具有適當的權限，如在內部部署電腦上針對此後端應用程式所定義的權限。 |


## 另請參閱

[使用應用程式 Proxy 發佈應用程式來進行安全遠端存取](active-directory-application-proxy-configure.md)

[啟用應用程式 Proxy 服務](active-directory-application-proxy-enable.md)

<!--Image references-->
[1]: ./media/active-directory-application-proxy-troubleshoot/ConnectorProperties.jpg
[2]: ./media/active-directory-application-proxy-troubleshoot/SessionLog.jpg

<!---HONumber=August15_HO9-->