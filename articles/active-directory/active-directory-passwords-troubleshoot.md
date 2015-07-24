<properties 
	pageTitle="疑難排解：Azure AD 密碼管理 |Microsoft Azure" 
	description="Azure AD 密碼管理的常見疑難排解步驟，包括重設、變更、回寫、註冊，以及尋求協助時應包含的資訊。" 
	services="active-directory" 
	documentationCenter="" 
	authors="asteen" 
	manager="kbrint" 
	editor="billmath"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/08/2015" 
	ms.author="asteen"/>

# 如何疑難排解密碼管理
如果您有密碼管理方面的問題，我們在此提供協助。您可能會碰到的大多數問題都可以透過以下幾個簡單的疑難排解步驟來加以解決，請閱讀其內容來疑難排解您的部署：

* [**您需要協助時應包含的資訊**](#information-to-include-when-you-need-help)
* [**Azure 管理入口網站中的密碼管理設定問題**](#troubleshoot-password-reset-configuration-in-the-azure-management-portal)
* [**Azure 管理入口網站中的密碼管理報告問題**](#troubleshoot-password-management-reports-in-the-azure-management-portal)
* [**密碼重設註冊入口網站的問題**](#troubleshoot-the-password-reset-registration-portal)
* [**密碼重設入口網站的問題**](#troubleshoot-the-password-reset-portal)
* [**密碼回寫的問題**](#troubleshoot-password-writeback)
  - [密碼回寫事件記錄檔錯誤碼](#password-writeback-event-log-error-codes)
  - [密碼回寫連線的問題](#troubleshoot-password-writeback-connectivity)

如果您已試過下列疑難排解步驟，但仍遇到問題，請將問題張貼在 [Azure AD 論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD)或連絡支援人員，我們將會儘快了解您的問題。

## 您需要協助時應包含的資訊

如果在進行過下列指導方針後仍無法解決問題，請連絡我們的支援工程師。當您與他們連絡時，建議您包含下列資訊：

 - **錯誤的一般描述** – 使用者實際看到的錯誤訊息。 如果沒有任何錯誤訊息，請詳細說明您所注意到的未預期行為。
 - **頁面** – 您看到錯誤時的所在頁面 (包含 URL)。
 - **日期/時間/時區** – 您看到錯誤時的精確日期和時間 (包含時區)。
 - **支援碼** – 使用者看到錯誤時所產生的支援碼 (若要找到支援碼，請重現錯誤，然後按一下畫面底部的 [支援碼] 連結，將所產生的 GUID 傳送給支援工程師)。 
   - 如果您所在的頁面底部沒有支援碼，請按 F12，搜尋 SID 和 CID，然後將這兩個結果傳送給支援工程師。

    ![][001]

 - **使用者識別碼** – 看到錯誤之使用者的識別碼 (例如 user@contoso.com)?
 - **使用者的相關資訊** – 使用者已同盟、密碼雜湊同步處理、只在雲端嗎？ 使用者是否已獲得 AAD Premium 或 AAD Basic 授權？
 - **應用程式事件記錄檔** – 如果您使用密碼回寫，而且錯誤位於您的內部部署基礎結構中，請將 Azure AD Connect 伺服器中的應用程式事件記錄檔複本進行壓縮，然後連同要求一起送出。

包含這些資訊將有助於我們儘快為您解決問題。


## 疑難排解 Azure 管理入口網站中的密碼重設設定
如果您在設定密碼重設時遇到錯誤，您可以遵循下列疑難排解步驟來加以解決：

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>錯誤案例</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>使用者看到的錯誤</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>解決方案</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>我在 Azure 管理入口網站的 [<strong>設定</strong>] 索引標籤下沒看到 [<strong>使用者密碼重設原則</strong>] 區段。</p>
            </td>
            <td>
              <p>Azure 管理入口網站的 [<strong>設定</strong>] 索引標籤下沒有出現 [<strong>使用者密碼重設原則</strong>] 區段。</p>
            </td>
            <td>
              <p>如果沒有指派 AAD Premium 或 AAD Basic 授權給執行此作業的系統管理員，就會發生這種情況。</p>
              <p>若要修正此問題，請瀏覽至 [<strong>授權</strong>] 索引標籤將 AAD Premium 或 AAD Basic 授權指派給發生問題的系統管理員帳戶，然後再試一次。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>我在本文所說的 [<strong>使用者密碼重設原則</strong>] 區段底下沒看到任何組態選項。</p>
            </td>
            <td>
              <p>有出現 [<strong>使用者密碼重設原則</strong>] 區段，但其下方所出現的唯一旗標是 [<strong>使用者已啟用密碼重設</strong>] 旗標。</p>
            </td>
            <td>
              <p>當您將 [<strong>使用者已啟用密碼重設</strong>] 旗標切換為 [<strong>是</strong>] 時，就會出現其餘 UI。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>我沒看到特定設定選項。</p>
            </td>
            <td>
              <p>比方說，當我捲動 [<strong>使用者密碼重設原則</strong>] 區段 (或相同問題的其他範例) 時，我看不到 [<strong>使用者必須在幾天內確認其連絡資料</strong>] 選項。</p>
            </td>
            <td>
              <p>許多 UI 項目只會在需要時出現。如果您想要看到這些項目，請嘗試啟用頁面上的所有選項。</p>
              <p>如需所有可用控制項的詳細資訊，請參閱<a href="../active-directory-passwords-customize#password-management-behavior">自訂使用者密碼重設原則</a>。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>我沒看到 [<strong>將密碼回寫至內部部署</strong>] 設定選項</p>
            </td>
            <td>
              <p>Azure 管理入口網站的 [<strong>設定</strong>] 索引標籤下沒有出現 [<strong>將密碼回寫至內部部署</strong>] 選項</p>
            </td>
            <td>
              <p>在您下載 Azure AD Connect 並設定密碼回寫後，才會出現此選項。當您完成上述動作時，此選項便會出現，並可讓您啟用或停用雲端回寫。</p>
              <p>如需如何執行上述動作的詳細資訊，請參閱<a href="../active-directory-passwords-getting-started#enable-users-to-reset-or-change-their-ad-passwords">如何啟用/停用密碼回寫</a>。</p>
            </td>
          </tr>
        </tbody></table>

## 疑難排解 Azure 管理入口網站中的密碼管理報告
如果您在使用密碼管理報告時遇到錯誤，您可以遵循下列疑難排解步驟來加以解決：

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>錯誤案例</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>使用者看到的錯誤</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>解決方案</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>我沒看到任何密碼管理報表</p>
            </td>
            <td>
              <p>[<strong>報告</strong>] 索引標籤的 [<strong>活動記錄檔</strong>] 報告底下沒有出現 [<strong>密碼重設活動</strong>] 和 [<strong>密碼重設註冊活動</strong>] 報告。</p>
            </td>
            <td>
              <p>如果沒有指派 AAD Premium 或 AAD Basic 授權給執行此作業的系統管理員，就會發生這種情況。</p>
              <p>若要修正此問題，請瀏覽至 [<strong>授權</strong>] 索引標籤將 AAD Premium 或 AAD Basic 授權指派給發生問題的系統管理員帳戶，然後再試一次。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>使用者註冊顯示多個時間</p>
            </td>
            <td>
              <p>當使用者註冊備用電子郵件、行動電話和安全性問題時，這些活動會各自顯示一行，而非全部顯示在同一行。</p>
            </td>
            <td>
              <p>目前，當使用者註冊時，我們無法假設他們會在註冊頁面上註冊出現的所有項目。因此，我們目前會將所註冊的每一筆個別資料記錄為個別事件。</p>
              <p>如果您想要彙總此資料，您可以下載報告，並在 Excel 樞紐分析表中開啟資料，以獲得更大的編輯彈性。</p>
            </td>
          </tr>
        </tbody></table>

## 疑難排解密碼重設註冊入口網站
如果您在註冊使用者的密碼重設功能時遇到錯誤，您可以遵循下列疑難排解步驟來加以解決：

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>錯誤案例</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>使用者看到的錯誤</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>解決方案</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>目錄未啟用密碼重設功能</p>
            </td>
            <td>
              <p>您的系統管理員還沒為您啟用這項功能。</p>
            </td>
            <td>
              <p>在 Azure 管理入口網站的目錄設定索引標籤中將 [<strong>使用者已啟用密碼重設</strong>] 旗標切換為 [<strong>是</strong>] 並點擊 [<strong>儲存</strong>]。您必須指派 Azure AD Premium 或 Basic 授權給執行此作業的系統管理員。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>使用者尚未獲得 AAD Premium 或 AAD Basic 授權</p>
            </td>
            <td>
              <p>您的系統管理員還沒為您啟用這項功能。</p>
            </td>
            <td>
              <p>在 Azure 管理入口網站的 [<strong>授權</strong>] 索引標籤底下，將 Azure AD Premium 或 Azure AD Basic 授權指派給使用者。您必須指派 Azure AD Premium 或 Basic 授權給執行此作業的系統管理員。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>處理要求時發生錯誤</p>
            </td>
            <td>
              <p>使用者看到錯誤指出：</p>
              <p>
                
              </p>
              <p>處理要求時發生錯誤 </p>
              <p>在其嘗試重設密碼時。</p>
            </td>
            <td>
              <p>許多問題皆會造成此情形，但這個錯誤通常是因為服務中斷或無法解決的設定問題所導致。</p>
              <p>如果您看到這個錯誤，而且它會影響您的業務，請連絡支援人員，我們會儘快為您提供協助。請參閱<a href="#information-to-include-when-you-need-help">您需要協助時應包含的資訊</a>，了解應提供給支援工程師的資訊以加快解決速度。</p>
            </td>
          </tr>
        </tbody></table>

## 疑難排解密碼重設入口網站
如果您在重設使用者的密碼時遇到錯誤，您可以遵循下列疑難排解步驟來加以解決：

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>錯誤案例</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>使用者看到的錯誤</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>解決方案</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>目錄未啟用密碼重設功能</p>
            </td>
            <td>
              <p>您的帳戶未啟用密碼重設功能</p>
              <p>很抱歉，您的系統管理員還沒將您的帳戶設定用於此服務。</p>
              <p>
                
              </p>
              <p>如果您願意，我們可以連絡貴組織的系統管理員來為您重設密碼。</p>
            </td>
            <td>
              <p>在 Azure 管理入口網站的目錄設定索引標籤中將 [<strong>使用者已啟用密碼重設</strong>] 旗標切換為 [<strong>是</strong>] 並點擊 [<strong>儲存</strong>]。您必須指派 Azure AD Premium 或 Basic 授權給執行此作業的系統管理員。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>使用者尚未獲得 AAD Premium 或 AAD Basic 授權</p>
            </td>
            <td>
              <p>我們雖無法自動重設非系統管理員的帳戶密碼，但可以連絡貴組織的系統管理員來為您重設。</p>
            </td>
            <td>
              <p>在 Azure 管理入口網站的 [<strong>授權</strong>] 索引標籤底下，將 Azure AD Premium 或 Azure AD Basic 授權指派給使用者。您必須指派 Azure AD Premium 或 Basic 授權給執行此作業的系統管理員。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>目錄已啟用密碼重設，但使用者的驗證資訊遺失或格式不正確</p>
            </td>
            <td>
              <p>您的帳戶未啟用密碼重設功能</p>
              <p>很抱歉，您的系統管理員還沒將您的帳戶設定用於此服務。</p>
              <p>
                
              </p>
              <p>如果您願意，我們可以連絡貴組織的系統管理員來為您重設密碼。</p>
            </td>
            <td>
              <p>確定使用者已在目錄中登記格式正確的連絡資料，然後再繼續。如需如何在目錄中設定驗證資訊，以免使用者看到此錯誤的相關資訊，請參閱<a href="../active-directory-passwords-learn-more#what-data-is-used-by-password-reset">密碼重設使用哪些資料</a>。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>目錄已啟用密碼重設，原則設定為需要兩個驗證步驟但使用者只登記一個連絡資料</p>
            </td>
            <td>
              <p>您的帳戶未啟用密碼重設功能</p>
              <p>很抱歉，您的系統管理員還沒將您的帳戶設定用於此服務。</p>
              <p>
                
              </p>
              <p>如果您願意，我們可以連絡貴組織的系統管理員來為您重設密碼。</p>
            </td>
            <td>
              <p>確定使用者有至少兩個已正確設定的連絡方法 (例如行動電話和辦公室電話)，然後再繼續。如需如何在目錄中設定驗證資訊，以免使用者看到此錯誤的相關資訊，請參閱<a href="../active-directory-passwords-learn-more#what-data-is-used-by-password-reset">密碼重設使用哪些資料</a>。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>目錄已啟用密碼重設，並已正確設定使用者，但無法連絡到使用者 </p>
            </td>
            <td>
              <p>抱歉！ 我們在與您連絡時發生非預期的錯誤。</p>
            </td>
            <td>
              <p>原因可能是暫時性的服務錯誤，或連絡資料設定錯誤因此無法正確偵測到。如果使用者等候 10 秒，便會出現再試一次和「連絡您的系統管理員」連結。按一下再試一次會重新發送呼叫，按一下「連絡您的系統管理員」則會傳送表單電子郵件給使用者、密碼或全域系統管理員 (依照此優先順序)，要求對該使用者帳戶執行密碼重設。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>使用者從未收到密碼重設 SMS 或來電</p>
            </td>
            <td>
              <p>使用者按一下「發簡訊給我」或「打電話給我」後，從未收到任何消息。</p>
            </td>
            <td>
              <p>原因可能是目錄中的電話號碼格式不正確。請確定電話號碼的格式是 “+ccc xxxyyyzzzzXeeee”。若要深入了解格式化電話號碼以用於密碼重設，請參閱<a href="../active-directory-passwords-learn-more#what-data-is-used-by-password-reset">密碼重設使用哪些資料</a>。</p>
              <p>如果您需要傳送分機號碼給提出要求的使用者，請注意密碼重設並不支援分機號碼，即使您在目錄中有指定也是一樣 (發送呼叫前會將其刪除)。請嘗試使用沒有分機號碼的電話號碼，或將分機號碼整合至 PBX 中的電話號碼。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>使用者從未收到密碼重設電子郵件</p>
            </td>
            <td>
              <p>使用者按一下「寄電子郵件給我」後，從未收到任何消息。</p>
            </td>
            <td>
              <p>這個問題最常見的原因是垃圾郵件篩選器拒絕了郵件。請檢查您的垃圾郵件或刪除的郵件資料夾中是否有該電子郵件。</p>
              <p>也請確定您是在正確的電子郵件信箱中檢查是否有該郵件，許多人都有非常類似的電子郵件地址，結果在錯誤的收件匣中檢查是否有該郵件。如果上述方式都沒有用，也有可能是目錄中的電子郵件地址格式不正確，請確定電子郵件地址正確無誤，然後再試一次。若要深入了解格式化電子郵件地址以用於密碼重設，請參閱<a href="../active-directory-passwords-learn-more#what-data-is-used-by-password-reset">密碼重設使用哪些資料</a>。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>我已設定密碼重設原則，但是當系統管理員帳戶使用密碼重設時，卻未套用該原則</p>
            </td>
            <td>
              <p>無論在 [<strong>設定</strong>] 索引標籤的 [<strong>使用者密碼重設原則</strong>] 區段底下所設定的原則為何，重設密碼的系統管理員帳戶所看到針對密碼重設、電子郵件與行動電話啟用的選項都相同。</p>
            </td>
            <td>
              <p>在 [<strong>設定</strong>] 索引標籤的 [<strong>使用者密碼重設原則</strong>] 區段底下所設定的選項只會套用到組織中的使用者。</p>
              <p>為了確保最高層級的安全性，系統管理員的密碼重設原則是由 Microsoft 管理及控制</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>使用者被禁止在一天當中嘗試重設密碼太多次</p>
            </td>
            <td>
              <p>使用者看到錯誤指出：</p>
              <p>
                
              </p>
              <p>請使用其他選項。</p>
              <p>在最近 1 小時內，您已嘗試驗證帳戶太多次。為了確保安全，您必須在等候 24 小時後，方可再試一次。</p>
              <p>如果您願意，我們可以連絡貴組織的系統管理員來為您重設密碼。</p>
            </td>
            <td>
              <p>我們實作了自動節流機制來避免使用者在短時間內嘗試重設密碼太多次。其發生條件如下：</p>
              <ol class="ordered">
                <li>
										使用者嘗試在一個小時內驗證電話號碼 5 次。&lt;br>&lt;br></li>
                <li>
										使用者嘗試在一個小時內使用安全性問題關卡 5 次。&lt;br>&lt;br></li>
                <li>
										使用者嘗試在一個小時內重設相同使用者帳戶的密碼 5 次。&lt;br>&lt;br></li>
              </ol>
              <p>若要修正此問題，請指示使用者在最後一次嘗試後等候 24 小時，之後使用者就能重設其密碼。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>使用者在驗證其電話號碼時看到錯誤</p>
            </td>
            <td>
              <p>使用者在嘗試驗證電話來做為驗證方法時，看到錯誤指出：</p>
              <p>
                
              </p>
              <p>指定的電話號碼不正確。</p>
            </td>
            <td>
              <p>輸入的電話號碼與登記的電話號碼不符時，就會發生這個錯誤。</p>
              <p>請確定使用者在嘗試使用電話式方法來重設密碼時有輸入完整的電話號碼，包括區碼和國碼。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>處理要求時發生錯誤</p>
            </td>
            <td>
              <p>使用者看到錯誤指出：</p>
              <p>
                
              </p>
              <p>處理要求時發生錯誤 </p>
              <p>在其嘗試重設密碼時。</p>
            </td>
            <td>
              <p>許多問題皆會造成此情形，但這個錯誤通常是因為服務中斷或無法解決的設定問題所導致。</p>
              <p>如果您看到這個錯誤，而且它會影響您的業務，請連絡支援人員，我們會儘快為您提供協助。請參閱<a href="#information-to-include-when-you-need-help">您需要協助時應包含的資訊</a>，了解應提供給支援工程師的資訊以加快解決速度。</p>
            </td>
          </tr>
        </tbody></table>

## 疑難排解密碼回寫
如果您在啟用、停用或使用密碼回寫時遇到錯誤，您可以遵循下列疑難排解步驟來加以解決：

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>錯誤案例</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>使用者看到的錯誤</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>解決方案</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>一般的上架和啟動失敗</p>
            </td>
            <td>
              <p>內部部署未啟動密碼重設服務，Azure AD Connect 電腦的應用程式事件記錄檔中有錯誤 6800。</p>
              <p>
                
              </p>
              <p>上架之後，已同盟或密碼雜湊同步處理的使用者無法重設其密碼。</p>
            </td>
            <td>
              <p>啟用密碼回寫時，同步處理引擎會與雲端上架服務交談，呼叫回寫資源庫來執行設定 (上架)。在上架期間或啟動密碼回寫的 WCF 端點時遇到任何錯誤，將會導致 Azure AD Connect 電腦的事件記錄檔中出現錯誤。</p>
              <p>在 ADSync 服務重新啟動期間若已設定回寫，將會啟動 WCF 端點。不過，如果端點啟動失敗，我們只會記錄事件 6800，並讓同步處理服務啟動。出現此事件表示密碼回寫端點並未啟動。此事件 (6800) 的事件記錄檔詳細資料以及 PasswordResetService 元件所產生的事件記錄檔項目會指出為何無法啟動端點。如果密碼回寫仍然無法運作，請檢閱這些事件記錄檔錯誤，並嘗試重新啟動 Azure AD Connect。如果此問題持續發生，請嘗試先停用再重新啟用密碼回寫。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>在 Azure AD Connect 安裝期間設定回寫時發生錯誤。</p>
            </td>
            <td>
              <p>在 Azure AD Connect 安裝程序的最後一個步驟時，您看到錯誤指出無法設定密碼回寫。</p>
              <p>
                
              </p>
              <p>Azure AD Connect 應用程式事件記錄檔包含錯誤 32009 以及「取得授權權杖時發生錯誤」文字。</p>
            </td>
            <td>
              <p>下列兩種情況會發生此錯誤：</p>
              <ul>
                <li class="unordered">
										針對在 Azure AD Connect 安裝程序開始時所指定的全域系統管理員帳戶，指定了錯誤的密碼。&lt;br>&lt;br></li>
              </ul>
              <ul>
                <li class="unordered">
										針對在 Azure AD Connect 安裝程序開始時所指定的全域系統管理員帳戶，嘗試使用已同盟的使用者。&lt;br>&lt;br></li>
              </ul>
              <p>若要修正此錯誤，請確定您並未針對在 Azure AD Connect 安裝程序開始時所指定的全域系統管理員使用已同盟的帳戶，而且所指定的密碼正確無誤。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>在 Azure AD Connect 安裝期間設定回寫時發生錯誤。</p>
            </td>
            <td>
              <p>Azure AD Connect 電腦的事件記錄檔包含 PasswordResetService 所擲回的錯誤 32002。</p>
              <p>
                
              </p>
              <p>這個錯誤的內容是：「連線到 ServiceBus 時發生錯誤。權杖提供者無法提供安全性權杖...」</p>
              <p>
                
              </p>
            </td>
            <td>
              <p>此錯誤的根本原因是內部部署環境中執行的密碼重設服務無法連線到雲端的服務匯流排端點。這個錯誤通常是因為防火牆規則封鎖連往特定連接埠或網址的輸出連線所導致。</p>
              <p>
                
              </p>
              <p>請確定您的防火牆允許下列項目的輸出連線：</p>
              <ul>
                <li class="unordered">
										所有透過 TCP 443 (HTTPS) 的流量&lt;br>&lt;br></li>
              </ul>
              <ul>
                <li class="unordered">
										輸出連線&lt;br>&lt;br></li>
              </ul>
              <p>
                
              </p>
              <p>一旦您更新這些規則後，請重新啟動 Azure AD Connect 電腦，密碼回寫應該就會再次開始工作。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>無法連線到內部部署的密碼回寫端點</p>
            </td>
            <td>
              <p>在運作一段時間後，已同盟或密碼雜湊同步處理的使用者無法重設其密碼。</p>
            </td>
            <td>
              <p>在某些罕見情況下，重新啟動 Azure AD Connect 時可能無法重新啟動密碼回寫服務。在這些情況下，請先檢查內部部署是否已啟用密碼回寫。若要執行此作業，請使用 Azure AD Connect 精靈或 PowerShell (請參閱上面的「作法」章節)。如果此功能已啟用，請嘗試透過 UI 或 PowerShell 再次啟用或停用功能。如需如何執行此作業的詳細資訊，請參閱<a href="../active-directory-passwords-getting-started#enable-users-to-reset-or-change-their-ad-passwords">如何啟用/停用密碼回寫</a>中的「步驟 2：在目錄同步處理電腦上啟用密碼回寫和設定防火牆規則」。</p>
              <p>
                
              </p>
              <p>如果這麼做沒有效，請嘗試完整解除安裝再重新安裝 Azure AD Connect。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>權限錯誤</p>
            </td>
            <td>
              <p>已同盟或密碼雜湊同步處理的使用者若嘗試重設其密碼，會在送出密碼後看到錯誤指出服務發生問題。</p>
              <p>
                
              </p>
              <p>此外，在密碼重設作業期間，您可能會在內部部署的事件記錄檔中看到關於管理代理程式存取遭拒的錯誤。</p>
            </td>
            <td>
              <p>如果您在事件記錄檔中看到這些錯誤，請確認 AD MA 帳戶 (在設定時於精靈中所指定) 有密碼回寫的必要權限。</p>
              <p>
                
              </p>
              <p>請注意，一旦給予此權限，最多要 1 小時的時間，此權限才會透過 DC 上的 sdprop 背景工作往下傳遞。</p>
              <p>若要讓密碼重設正常運作，必須在要重設密碼的使用者物件安全性描述元上為權限加上戳記。在使用者物件上出現此權限之前，密碼重設會繼續因存取遭拒而失敗。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>從 Azure AD Connect 設定精靈設定密碼回寫時發生錯誤 </p>
            </td>
            <td>
              <p>在精靈中啟用/停用密碼回寫時發生「找不到 MA」錯誤</p>
            </td>
            <td>
              <p>發行版本的 Azure AD Connect 中已知會在下列情況時發生錯誤：</p>
              <ol class="ordered">
                <li>
										使用認證針對租用戶 abc.com (已驗證的網域) 設定 Azure AD Connect。這會導致建立名稱為 “abc.com – AAD” 的 AAD 連接器。&lt;br>&lt;br></li>
                <li>
										然後 (使用舊版的同步處理 UI) 將連接器的 AAD 認證變更為 (請注意，它是相同的租用戶但網域名稱不同)。&lt;br>&lt;br></li>
                <li>
										現在您嘗試啟用/停用密碼回寫。精靈會使用認證將連接器的名稱建構為 “abc.onmicrosoft.com – AAD”，並傳遞給密碼回寫 Cmdlet。此作業會失敗，因為沒有使用此名稱建立的連接器。&lt;br>&lt;br></li>
              </ol>
              <p>最新的組建已修正此問題。如果您有較舊的組建，有一個解決方法是使用 PowerShell Cmdlet 來啟用/停用此功能。如需如何執行此作業的詳細資訊，請參閱<a href="../active-directory-passwords-getting-started#enable-users-to-reset-or-change-their-ad-passwords">如何啟用/停用密碼回寫</a>中的「步驟 2：在目錄同步處理電腦上啟用密碼回寫和設定防火牆規則」。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>無法對特殊群組中的使用者重設密碼，例如 Domain Admins/Enterprise Admins 等等。</p>
            </td>
            <td>
              <p>屬於受保護群組的已同盟或密碼雜湊同步處理的使用者若嘗試重設其密碼，會在送出密碼後看到錯誤指出服務發生問題。</p>
            </td>
            <td>
              <p>Active Directory 中的特殊權限使用者會透過 AdminSDHolder 受到保護。如需詳細資訊，請參閱 <a href="https://technet.microsoft.com/magazine/2009.09.sdadminholder.aspx">http://technet.microsoft.com/magazine/2009.09.sdadminholder.aspx</a>。</p>
              <p>
                
              </p>
              <p>這表示會定期檢查這些物件上的安全性描述元來比對 AdminSDHolder 中指定的描述元，如有不同就會予以重設。密碼回寫所需的額外權限因此不會傳遞給這類使用者。這會導致這類使用者的密碼回寫沒有作用。因此，我們沒有對這些群組中的使用者支援密碼管理工作，因為它會破壞 AD 安全性模型。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>重設作業因找不到物件而失敗</p>
            </td>
            <td>
              <p>已同盟或密碼雜湊同步處理的使用者若嘗試重設其密碼，會在送出密碼後看到錯誤指出服務發生問題。</p>
              <p>
                
              </p>
              <p>此外，在密碼重設作業期間，您可能會在 Azure AD Connect 服務的事件記錄檔中看到錯誤指出「找不到物件」錯誤。</p>
            </td>
            <td>
              <p>這個錯誤通常表示同步處理引擎找不到 AAD 連接器空間中的使用者物件，或連結的 MV 或 AD 連接器空間物件。</p>
              <p>
                
              </p>
              <p>若要疑難排解這個問題，請確定使用者已確實透過 Azure AD Connect 的目前執行個體從內部部屬同步處理到 AAD，並檢查連接器空間和 MV 中物件的狀態。確認 AD CS 物件透過 “Microsoft.InfromADUserAccountEnabled.xxx” 規則連線到 MV 物件。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>重設作業因找到多個相符項目錯誤而失敗</p>
            </td>
            <td>
              <p>已同盟或密碼雜湊同步處理的使用者若嘗試重設其密碼，會在送出密碼後看到錯誤指出服務發生問題。</p>
              <p>
                
              </p>
              <p>此外，在密碼重設作業期間，您可能會在 Azure AD Connect 服務的事件記錄檔中看到錯誤指出「找到多個相符項目」錯誤。</p>
            </td>
            <td>
              <p>此錯誤指出同步處理引擎偵測到 MV 物件透過 “Microsoft.InfromADUserAccountEnabled.xxx” 連線到多個 AD CS 物件。這表示使用者在多個樹系中啟用帳戶。</p>
              <p>
                
              </p>
              <p>密碼回寫目前不支援此案例。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>密碼作業因設定錯誤而失敗。</p>
            </td>
            <td>
              <p>密碼作業因設定錯誤而失敗。應用程式事件記錄檔中包含 Azure AD Connect 錯誤 6329 和文字：0x8023061f (作業失敗，因為此管理代理程式上未啟用密碼同步處理。)</p>
            </td>
            <td>
              <p>如果在啟用密碼回寫功能<strong>後</strong>變更 Azure AD Connect 設定來加入新的 AD 樹系 (或移除現有樹系再重新加入)，就會發生此錯誤。位於這類新加入樹系中的使用者，其密碼作業將會失敗。若要修正此問題，請在完成樹系設定變更後，先停用再重新啟用密碼回寫功能。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>使用者已重設的密碼回寫正常運作，但使用者變更的或系統管理員為使用者重設的密碼回寫失敗。</p>
            </td>
            <td>
              <p>嘗試在 Azure 管理入口網站代表使用者重設密碼時，您看到訊息指出：「內部部署環境中執行的密碼重設服務不支援系統管理員重設使用者的密碼。請升級至最新版的 Azure AD Connect 來解決此問題。」</p>
            </td>
            <td>
              <p>當同步處理引擎的版本不支援所使用的特定密碼回寫作業時，就會發生此錯誤。1.0.0419.0911 之後的 Azure AD Connect 版本支援所有密碼管理作業，包括密碼重設回寫、密碼變更回寫，以及系統管理員在 Azure 管理入口網站中起始的密碼重設回寫。&#160; 1.0.6862 之後的 DirSync 版本則僅支援密碼重設回寫。若要解決此問題，我們強烈建議您安裝最新版的 Azure AD Connect 或 Azure Active Directory Connect (如需詳細資訊，請參閱<a href="../active-directory-aadconnect#download-azure-ad-connect">目錄整合工具</a>) 來解決此問題，並在組織中徹底發揮密碼回寫的效益。</p>
            </td>
          </tr>
        </tbody></table>


## 密碼回寫事件記錄檔錯誤碼
疑難排解密碼回寫問題時的最佳作法，是檢查 Azure AD Connect 電腦上的應用程式事件記錄檔。這個事件記錄檔會包含密碼回寫的兩個相關來源的事件。PasswordResetService 來源會說明與密碼回寫作業相關的作業和問題。ADSync 來源會說明與 AD 環境中的密碼設定相關的作業和問題。

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>代碼</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>名稱/訊息</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>來源</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>說明</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>6329</p>
            </td>
            <td>
              <p>BAIL: MMS(4924) 0x80230619 – “有一項限制讓密碼無法變更為目前指定的密碼。”</p>
            </td>
            <td>
              <p>ADSync</p>
            </td>
            <td>
              <p>當密碼回寫服務嘗試在本機目錄上設定不符合密碼使用期限、歷程記錄、複雜度或網域篩選需求的密碼時，就會發生此錯誤。</p>
              <ul>
                <li class="unordered">
										如果您有最短的密碼使用期限，且最近在該時段內變更過密碼，則必須在到達網域中指定的使用期限後，才能再次變更密碼。若要進行測試，最短使用期限應該設定為 0。&lt;br>&lt;br></li>
              </ul>
              <ul>
                <li class="unordered">
										如果已啟用密碼歷程記錄需求，則必須選取最近 N 次未用過的密碼，其中 N 是密碼歷程記錄設定。如果您選取最近 N 次用過的密碼，則會在此案例中看到失敗。若要進行測試，歷程記錄應該設定為 0。&lt;br>&lt;br></li>
              </ul>
              <ul>
                <li class="unordered">
										如果您有密碼複雜度需求，則會在使用者嘗試變更或重設密碼時強制執行這些需求。&lt;br>&lt;br></li>
              </ul>
              <ul>
                <li class="unordered">
										如果您啟用密碼篩選器，且使用者選取的密碼不符合篩選準則，則重設或變更作業將會失敗。&lt;br>&lt;br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>31001</p>
            </td>
            <td>
              <p>PasswordResetStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示內部部署服務偵測到源自雲端、針對已同盟或密碼雜湊同步處理的使用者所提出的密碼重設要求。這個事件是每個密碼重設回寫作業的第一個事件。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31002</p>
            </td>
            <td>
              <p>PasswordResetSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示使用者在密碼重設作業期間選取了新的密碼，我們判斷此密碼符合公司的密碼需求，因此該密碼已成功回寫至本機 AD 環境。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31003</p>
            </td>
            <td>
              <p>PasswordResetFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示使用者選取了密碼，該密碼已成功抵達內部部署環境，但是當我們嘗試在本機 AD 環境中設定密碼時，發生了失敗狀況。此狀況有幾個可能原因：</p>
              <ul>
                <li class="unordered">
										使用者的密碼不符合使用期限、歷程記錄、複雜度或網域篩選需求。請嘗試使用全新密碼以解決此問題。&lt;br>&lt;br></li>
              </ul>
              <ul>
                <li class="unordered">
										MA 服務帳戶沒有適當權限，因此無法在指定的使用者帳戶設定新密碼。&lt;br>&lt;br></li>
              </ul>
              <ul>
                <li class="unordered">
										使用者的帳戶位於不允許密碼設定作業的受保護群組中，例如網域或企業系統管理員。&lt;br>&lt;br></li>
              </ul>
              <p>請參閱<a href="#troubleshoot-password-writeback">疑難排解密碼回寫</a>，以深入了解可能造成這個錯誤的其他狀況。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31004</p>
            </td>
            <td>
              <p>OnboardingEventStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>此事件發生在您對 Azure AD Connect 啟用密碼回寫時，並且表示我們已開始將您的組織上架到密碼回寫 Web 服務。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31005</p>
            </td>
            <td>
              <p>OnboardingEventSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示上架程序已順利完成，且密碼回寫功能已準備好可供使用。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31006</p>
            </td>
            <td>
              <p>ChangePasswordStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示內部部署服務偵測到源自雲端、針對已同盟或密碼雜湊同步處理的使用者所提出的密碼變更要求。這個事件是每個密碼變更回寫作業的第一個事件。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31007</p>
            </td>
            <td>
              <p>ChangePasswordSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示使用者在密碼變更作業期間選取了新的密碼，我們判斷此密碼符合公司的密碼需求，因此該密碼已成功回寫至本機 AD 環境。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31008</p>
            </td>
            <td>
              <p>ChangePasswordFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示使用者選取了密碼，該密碼已成功抵達內部部署環境，但是當我們嘗試在本機 AD 環境中設定密碼時，發生了失敗狀況。此狀況有幾個可能原因：</p>
              <ul>
                <li class="unordered">
										使用者的密碼不符合使用期限、歷程記錄、複雜度或網域篩選需求。請嘗試使用全新密碼以解決此問題。&lt;br>&lt;br></li>
              </ul>
              <ul>
                <li class="unordered">
										MA 服務帳戶沒有適當權限，因此無法在指定的使用者帳戶設定新密碼。&lt;br>&lt;br></li>
              </ul>
              <ul>
                <li class="unordered">
										使用者的帳戶位於不允許密碼設定作業的受保護群組中，例如網域或企業系統管理員。&lt;br>&lt;br></li>
              </ul>
              <p>請參閱<a href="#troubleshoot-password-writeback">疑難排解密碼回寫</a>，以深入了解可能造成這個錯誤的其他狀況。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31009</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>內部部署服務偵測到源自系統管理員代表使用者針對已同盟或密碼雜湊同步處理的使用者所提出的密碼重設要求。這個事件是每個系統管理員所起始的密碼重設回寫作業的第一個事件。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31010</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>系統管理員在系統管理員所起始的密碼重設作業期間選取了新的密碼，我們判斷此密碼符合公司的密碼需求，因此該密碼已成功回寫至本機 AD 環境。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31011</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>系統管理員代表使用者選取了密碼，該密碼已成功抵達內部部署環境，但是當我們嘗試在本機 AD 環境中設定密碼時，發生了失敗狀況。此狀況有幾個可能原因：</p>
              <ul>
                <li class="unordered">
										使用者的密碼不符合使用期限、歷程記錄、複雜度或網域篩選需求。請嘗試使用全新密碼以解決此問題。&lt;br>&lt;br></li>
              </ul>
              <ul>
                <li class="unordered">
										MA 服務帳戶沒有適當權限，因此無法在指定的使用者帳戶設定新密碼。&lt;br>&lt;br></li>
              </ul>
              <ul>
                <li class="unordered">
										使用者的帳戶位於不允許密碼設定作業的受保護群組中，例如網域或企業系統管理員。&lt;br>&lt;br></li>
              </ul>
              <p>請參閱<a href="#troubleshoot-password-writeback">疑難排解密碼回寫</a>，以深入了解可能造成這個錯誤的其他狀況。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31012</p>
            </td>
            <td>
              <p>OffboardingEventStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>此事件發生在您對 Azure AD Connect 停用密碼回寫時，並且表示我們已開始將您的組織下架到密碼回寫 Web 服務。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31013</p>
            </td>
            <td>
              <p>OffboardingEventSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示下架程序已順利完成，且密碼回寫功能已順利停用。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31014</p>
            </td>
            <td>
              <p>OffboardingEventFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示下架程序未成功。原因可能是雲端或設定期間指定之內部部署系統管理員帳戶的權限發生錯誤，或您嘗試在停用密碼回寫時使用已同盟的雲端全域系統管理員。若要修正此問題，請檢查您的系統管理權限，並確認您沒有在設定密碼回寫功能時使用任何已同盟的帳戶。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31015</p>
            </td>
            <td>
              <p>WriteBackServiceStarted </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示密碼回寫服務已成功啟動，並已準備好接受來自雲端的密碼管理要求。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31016</p>
            </td>
            <td>
              <p>WriteBackServiceStopped </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示密碼回寫服務已停止，且來自雲端的所有密碼管理要求都不會成功。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31017</p>
            </td>
            <td>
              <p>AuthTokenSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示我們已順利擷取 Azure AD Connect 設定期間所指定之全域系統管理員的授權權杖，以便開始下架或上架程序。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31018</p>
            </td>
            <td>
              <p>KeyPairCreationSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示我們已成功建立密碼加密金鑰，此金鑰將會用來加密從雲端傳送至內部部署環境的密碼。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32000</p>
            </td>
            <td>
              <p>UnknownError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示密碼管理作業期間發生未知的錯誤。請查看事件中的例外狀況文字，以獲得詳細資訊。如果您遇到問題，請嘗試停用再重新啟用密碼回寫。如果這麼做沒有用，請將事件記錄檔複本連同內部指定的追蹤識別碼交給支援工程師。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32001</p>
            </td>
            <td>
              <p>ServiceError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>此事件表示連線到雲端密碼重設服務時發生錯誤，且此事件通常發生在內部部署服務無法連線到密碼重設 Web 服務時。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32002</p>
            </td>
            <td>
              <p>ServiceBusError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示連線到租用戶的服務匯流排執行個體時發生錯誤。可能原因是您在內部部署環境中封鎖了輸出連線。請檢查您的防火牆，確保您允許連線透過 TCP 443 連往 <a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a>，然後再試一次。如果仍遇到問題，請嘗試停用再重新啟用密碼回寫。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32003</p>
            </td>
            <td>
              <p>InPutValidationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示傳遞到我們的 Web 服務 API 的輸入無效。請重試一次此作業。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32004</p>
            </td>
            <td>
              <p>DecryptionError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示在解密從雲端抵達的密碼時發生錯誤。原因可能是雲端服務和內部部署環境的解密金鑰不符。若要解決此問題，請停用再重新啟用內部部署環境的密碼回寫。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32005</p>
            </td>
            <td>
              <p>ConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>上架期間，我們將租用戶特定的資訊儲存在內部部署環境的設定檔中。這個事件表示儲存此檔案時發生錯誤，或啟動服務時發生檔案讀取錯誤。若要修正此問題，請嘗試停用再重新啟用密碼回寫，強制重新寫入此設定檔。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32006</p>
            </td>
            <td>
              <p>EndPointConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>DEPRECATED – Azure AD Connect 不會出現此事件，只有極為早期的 DirSync 組建支援回寫。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32007</p>
            </td>
            <td>
              <p>OnBoardingConfigUpdateError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>上架期間，我們會從雲端傳送資料到內部部署密碼重設服務。該資料接著會寫入至記憶體內檔案，然後再傳送至同步處理服務，以將此資訊安全地儲存在磁碟上。這個事件表示在記憶體中寫入或更新該資料時發生問題。若要修正此問題，請嘗試停用再重新啟用密碼回寫，強制重新寫入此設定。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32008</p>
            </td>
            <td>
              <p>ValidationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示我們從密碼重設 Web 服務所收到的回應無效。若要修正此問題，請嘗試停用再重新啟用密碼回寫。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32009</p>
            </td>
            <td>
              <p>AuthTokenError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示我們無法取得 Azure AD Connect 設定期間所指定之全域系統管理員帳戶的授權權杖。造成這個錯誤的原因可能是全域系統管理員帳戶所指定的使用者名稱或密碼錯誤，或是指定的全域系統管理員帳戶已同盟。若要修正此問題，請以正確的使用者名稱和密碼重新執行設定，並確保系統管理員是受管理的 (僅限雲端或已密碼同步處理的) 帳戶。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32010</p>
            </td>
            <td>
              <p>CryptoError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示在產生密碼加密金鑰或解密從雲端服務抵達的密碼時發生錯誤。此錯誤可能表示您的環境有問題。請查看事件記錄檔的詳細資料，深入了解此問題並加以解決。您也可以嘗試停用再重新啟用密碼回寫服務來解決這個問題。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32011</p>
            </td>
            <td>
              <p>OnBoardingServiceError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示內部部署服務無法與密碼重設 Web 服務正確地通訊，來起始上架程序。這可能是因為有防火牆規則或是取得租用戶的授權權杖時發生問題。若要修正此問題，請確定您沒有封鎖透過 TCP 443 和 TCP 9350-9354 或是連往 <a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a> 的輸出連線，且用來上架的 AAD 系統管理員帳戶並未同盟。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32012</p>
            </td>
            <td>
              <p>OnBoardingServiceDisableError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>DEPRECATED – Azure AD Connect 不會出現此事件，只有極為早期的 DirSync 組建支援回寫。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32013</p>
            </td>
            <td>
              <p>OffBoardingError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示內部部署服務無法與密碼重設 Web 服務正確地通訊，來起始下架程序。這可能是因為有防火牆規則或是取得租用戶的授權權杖時發生問題。若要修正此問題，請確定您沒有封鎖透過 443 或是連往 <a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a> 的輸出連線，且用來下架的 AAD 系統管理員帳戶並未同盟。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32014</p>
            </td>
            <td>
              <p>ServiceBusWarning </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示我們必須重試連線到租用戶的服務匯流排執行個體。在正常情況下，您不必在意此事件，但如果您多次看到此事件，請考慮檢查您的服務匯流排網路連線，尤其是如果它是高延遲或低頻寬連線的話。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32015</p>
            </td>
            <td>
              <p>ReportServiceHealthError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>為了監視密碼回寫服務的健全狀況，我們每隔 5 分鐘就會傳送活動訊號資料給我們的密碼重設 Web 服務。這個事件表示在將此健全狀況資訊傳回到雲端 Web 服務時發生錯誤。此健全狀況資訊不包含 OII 或 PII 資料，純綷只有活動訊號和基本服務統計資料，以便我們可以在雲端中提供服務狀態資訊。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33001</p>
            </td>
            <td>
              <p>ADUnKnownError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示 AD 傳回未知的錯誤，請檢查 Azure AD Connect 伺服器事件記錄檔中來自 ADSync 來源的事件，以獲得關於此錯誤的詳細資訊。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33002</p>
            </td>
            <td>
              <p>ADUserNotFoundError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示在內部部署目錄中找不到嘗試重設或變更密碼的使用者。當使用者已自內部部署中刪除卻不在雲端中，或如果同步處理發生問題，就可能發生這個事件。請檢查您的同步處理記錄檔，以及最後幾次執行的同步處理詳細資料，以獲得詳細資訊。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33003</p>
            </td>
            <td>
              <p>ADMutliMatchError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>當密碼重設或變更要求源自雲端時，我們會使用 Azure AD Connect 設定程序期間所指定的雲端錨點，來判斷如何將該要求往回連結到內部部署環境中的使用者。這個事件表示我們發現內部部署目錄中有兩位使用者具有相同的雲端錨點屬性。請檢查您的同步處理記錄檔，以及最後幾次執行的同步處理詳細資料，以獲得詳細資訊。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33004</p>
            </td>
            <td>
              <p>ADPermissionsError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示管理代理程式服務帳戶沒有適當的指定帳戶權限，因此無法設定新密碼。請確定使用者樹系中的 MA 帳戶有樹系中所有物件的重設和變更密碼權限。如需如何執行此作業的詳細資訊，請參閱<a href="../active-directory-passwords-getting-started#step-4-set-up-the-appropriate-active-directory-permissions">步驟 4：設定適當的 Active Directory 權限</a>。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33005</p>
            </td>
            <td>
              <p>ADUserAccountDisabled </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示我們嘗試對內部部署中已停用的帳戶重設或變更密碼。請啟用帳戶，然後再試一次此作業。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33006</p>
            </td>
            <td>
              <p>ADUserAccountLockedOut </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示我們嘗試對內部部署中已鎖定的帳戶重設或變更密碼。當使用者在短時間內嘗試進行變更或重設密碼作業太多次，就可能發生鎖定。請解除鎖定帳戶，然後再試一次此作業。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33007</p>
            </td>
            <td>
              <p>ADUserIncorrectPassword </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示使用者在執行密碼變更作業時，指定的目前密碼不正確。請指定正確的目前密碼，然後再試一次。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33008</p>
            </td>
            <td>
              <p>ADPasswordPolicyError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>當密碼回寫服務嘗試在本機目錄上設定不符合密碼使用期限、歷程記錄、複雜度或網域篩選需求的密碼時，就會發生此錯誤。</p>
              <ul>
                <li class="unordered">
										如果您有最短的密碼使用期限，且最近在該時段內變更過密碼，則必須在到達網域中指定的使用期限後，才能再次變更密碼。若要進行測試，最短使用期限應該設定為 0。&lt;br>&lt;br></li>
              </ul>
              <ul>
                <li class="unordered">
										如果已啟用密碼歷程記錄需求，則必須選取最近 N 次未用過的密碼，其中 N 是密碼歷程記錄設定。如果您選取最近 N 次用過的密碼，則會在此案例中看到失敗。若要進行測試，歷程記錄應該設定為 0。&lt;br>&lt;br></li>
              </ul>
              <ul>
                <li class="unordered">
										如果您有密碼複雜度需求，則會在使用者嘗試變更或重設密碼時強制執行這些需求。&lt;br>&lt;br></li>
              </ul>
              <ul>
                <li class="unordered">
										如果您啟用密碼篩選器，且使用者選取的密碼不符合篩選準則，則重設或變更作業將會失敗。&lt;br>&lt;br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>33009</p>
            </td>
            <td>
              <p>ADConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示 Active Directory 的設定有問題，因此在將密碼回寫到內部部署目錄時發生問題。請檢查 Azure AD Connect 電腦的應用程式事件記錄檔中來自 ADSync 服務的訊息，以獲得所發生錯誤的詳細資訊。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34001</p>
            </td>
            <td>
              <p>ADPasswordPolicyOrPermissionError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>DEPRECATED – Azure AD Connect 不會出現此事件，只有極為早期的 DirSync 組建支援回寫。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34002</p>
            </td>
            <td>
              <p>ADNotReachableError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>DEPRECATED – Azure AD Connect 不會出現此事件，只有極為早期的 DirSync 組建支援回寫。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34003</p>
            </td>
            <td>
              <p>ADInvalidAnchorError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>DEPRECATED – Azure AD Connect 不會出現此事件，只有極為早期的 DirSync 組建支援回寫。</p>
            </td>
          </tr>
        </tbody></table>
## 疑難排解密碼回寫連線

如果 Azure AD Connect 的密碼回寫元件發生服務中斷，以下是可供用來解決此問題的一些快速步驟：

 - [重新啟動 Azure AD Connect 同步處理服務](#restart-the-azure-AD-sync-service)
 - [停用再重新啟用密碼回寫功能](#disable-and-re-enable-the-password-writeback-feature)
 - [安裝最新版的 Azure AD Connect](#install-the-latest-azure-ad-connect-release)
 - [疑難排解密碼回寫](#troubleshoot-password-writeback)

一般而言，我們會建議您依上述順序執行這些步驟，以便以最快速的方式復原服務。

### 重新啟動 Azure AD Connect 同步處理服務
重新啟動 Azure AD Connect 同步處理服務有助於解決服務的連線問題或其他暫時性問題。

 1.	以系統管理員身分，在執行 **Azure AD Connect** 的伺服器上按一下 [**開始**]。
 2.	在搜尋方塊中輸入 **“services.msc”**，然後按 **Enter**。
 3.	找出 **Microsoft Azure AD Connect** 項目。
 4.	以滑鼠右鍵按一下服務項目，然後按一下 [**重新啟動**]，並等候作業完成。

    ![][002]

這些步驟將會重新建立您與雲端服務的連線，解決您可能會遇到的任何中斷問題。如果重新啟動同步處理服務無法解決您的問題，建議您接下來試著停用再重新啟用密碼回寫功能。

### 停用再重新啟用密碼回寫功能
停用再重新啟用密碼回寫功能有助於解決連線問題。

 1.	以系統管理員身分，開啟 [**Azure AD Connect 設定精靈**]。
 2.	在 [**連線到 Azure AD**] 對話方塊中，輸入您的 **Azure AD 全域系統管理員認證**
 3.	在 [**連線到 AD DS**] 對話方塊中，輸入您的 **AD 網域服務系統管理員認證**。
 4.	在 [**唯一識別您的使用者**] 對話方塊中，按 [**下一步**] 按鈕。
 5.	在 [**選用功能**] 對話方塊中，取消核取 [**密碼回寫**] 核取方塊。

    ![][003]

 6.	在其餘的對話方塊頁面按 [**下一步**]，不要變更任何項目，直到到達 [**準備好設定**] 頁面。
 7.	確認設定頁面顯示 [**密碼回寫選項為停用**]，然後按一下綠色 [**設定**] 按鈕認可變更。
 8.	在 [**已完成**] 對話方塊中，取消選取 [**立即同步處理**] 選項，然後按一下 [**完成**] 關閉精靈。
 9.	重新開啟 [**Azure AD Connect 設定精靈**]。
 10.	**重複步驟 2-8**，但請確定您有在 [**選用功能**] 畫面上勾選 [**密碼回寫選項**] 來重新啟用服務。

    ![][004]

這些步驟將會重新建立您與雲端服務的連線，解決您可能會遇到的任何中斷問題。

如果停用再重新啟用密碼回寫功能無法解決您的問題，建議您接下來試著重新安裝 Azure AD Connect。

### 安裝最新版的 Azure AD Connect
重新安裝 Azure AD Connect 封裝將可解決任何可能會影響您能否連線到雲端服務或能否管理本機 AD 環境中密碼的設定問題。建議您只在嘗試過上述前兩個步驟後，才執行此步驟。

 1.	在[這裡](active-directory-aadconnect.md#download-azure-ad-connect)下載最新版的 Azure AD Connect。
 2.	由於您已安裝 Azure AD Connect，您只需要執行就地升級，即可將 Azure AD Connect 安裝更新為最新版。
 3.	執行下載的封裝，並遵循螢幕上的指示來更新您的 Azure AD Connect 電腦。過程中不需手動執行任何額外步驟，除非您已自訂現成可用的同步處理規則，在此情況下，您就應該**先備份這些規則，然後再繼續進行升級，並於完成後以手動方式重新部署這些規則**。

這些步驟將會重新建立您與雲端服務的連線，解決您可能會遇到的任何中斷問題。

如果安裝最新版的 Azure AD Connect 伺服器無法解決您的問題，建議您最後在安裝最新版的同補處理 QFE 後試著停用再重新啟用密碼回寫。

如果這麼做無法解決您的問題，建議您閱讀[疑難排解密碼回寫](#troubleshoot-password-writeback)和 [Azure AD 密碼管理常見問題集](active-directory-passwords-faq.md)，看看其中是否有討論到您的問題。


<br/> <br/> <br/>

**其他資源**


* [密碼管理是什麼](active-directory-passwords.md)
* [密碼管理如何運作](active-directory-passwords-how-it-works.md)
* [開始使用密碼管理](active-directory-passwords-getting-started.md)
* [自訂密碼管理](active-directory-passwords-customize.md)
* [密碼管理最佳作法](active-directory-passwords-best-practices.md)
* [如何使用密碼管理報告取得 Operational Insights](active-directory-passwords-get-insights.md)
* [密碼管理常見問題集](active-directory-passwords-faq.md)
* [深入了解](active-directory-passwords-learn-more.md)
* [MSDN 上的密碼管理](https://msdn.microsoft.com/library/azure/dn510386.aspx)




[001]: ./media/active-directory-passwords-troubleshoot/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-troubleshoot/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-troubleshoot/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-troubleshoot/004.jpg "Image_004.jpg"

 

<!---HONumber=62-->