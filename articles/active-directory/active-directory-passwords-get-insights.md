<properties 
	pageTitle="取得深入了解：Azure AD 密碼管理報告 |Microsoft Azure" 
	description="本文說明如何使用報告，在您的組織中取得密碼管理作業的深入了解。" 
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

# 如何使用密碼管理報告取得 Operational Insights
本章節描述如何使用 Azure Active Directory 的密碼管理報告，來檢視使用者如何使用密碼重設及您的組織中的變更。

- [**密碼管理報告概觀**](#overview-of-password-management-reports)
- [**如何檢視密碼管理報告**](#how-to-view-password-management-reports)
- [**在您的組織中檢視密碼重設註冊活動**](#view-password-reset-registration-activity)
- [**在您的組織中檢視密碼重設活動**](#view-password-reset-activity)

## 密碼管理報告概觀
一旦部署密碼重設之後，其中一個最常見的下一步是查看它在您的組織中的使用情形。例如，您可能想要取得使用者如何註冊密碼重設，或過去幾天內已完成多少密碼重設等深入了解。以下是一些常見問題，您可以使用目前存在於 [Azure 管理入口網站](https://manage.windowsazure.com)的密碼管理報告來回答：

- 有多少人已註冊密碼重設？
- 有誰已註冊密碼重設？
- 什麼資料是人員註冊？
- 有多少人在過去 7 天內重設其密碼？
- 使用者或系統管理員用來重設其密碼最常見的方法是什麼？
- 當使用者或系統管理員嘗試使用密碼重設時所面臨的常見問題是什麼？
- 哪些系統管理員經常重設自己的密碼？
- 密碼重設時是否有任何可疑的活動？


## 如何檢視密碼管理報告
若要尋找密碼管理報告，請遵循下列步驟：

1.	按一下 [Azure 管理入口網站](https://manage.windowsazure.com)中的 [**Active Directory**] 延伸模組。
2.	從入口網站顯示的清單中選取您的目錄。
3.	按一下 [**報告**] 索引標籤。
4.	查看 [**活動記錄檔**] 區段。
5.	選取 [**密碼重設活動**] 報告或 [**密碼重設註冊活動**] 報告。

    ![][001]

## 檢視密碼重設註冊活動

密碼重設註冊活動報告會顯示您的組織中發生的所有密碼重設註冊。當任何使用者已成功在密碼重設註冊入口網站 ([http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)) 註冊驗證資訊，則密碼重設註冊會顯示在此報告中。

- **時間範圍最大值**：1 個月
- **資料列數最大值**：無限
- **可下載**：是，透過 CSV 檔案

    ![][002]

### 報告資料行說明
下列清單詳細說明每個報告資料行：

- **使用者** – 嘗試進行密碼重設註冊作業的使用者。
- **角色** – 使用者在目錄中的角色。
- **日期和時間** – 嘗試的日期和時間。
- **已註冊資料** – 使用者在密碼重設註冊期間提供哪些驗證資料。

### 報告值說明
下表描述每個資料行允許的不同值：

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>資料行</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>允許的值及其意義</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <p>已註冊資料</p>
              </td>
              <td>
                <ul>
                  <li class="unordered">
                    <strong>備用電子郵件</strong> – 使用者用來驗證的備用電子郵件或驗證電子郵件<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>辦公室電話</strong> – 使用者用來驗證的辦公室電話<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>行動電話</strong> – 使用者用來驗證的行動電話或驗證電話<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>安全性問題</strong> – 使用者用來驗證的安全性問題<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>上述項目的任意組合 (例如，備用電子郵件 + 行動電話)</strong> – 發生時機為當指定 2 個閘道原則，且顯示使用者使用哪兩個方法來驗證其密碼重設要求時。<br><br></li>
                </ul>
              </td>
            </tr>
          </tbody></table>

## 檢視密碼重設活動

此報告會顯示您的組織中發生的所有密碼重設嘗試。

- **時間範圍最大值**：1 個月
- **資料列數最大值**：無限
- **可下載**：是，透過 CSV 檔案

    ![][003]

### 報告資料行說明
下列清單詳細說明每個報告資料行：

1. **使用者** – 嘗試密碼重設作業的使用者 (根據使用者重設密碼時提供的 [使用者識別碼] 欄位)。
2. **角色** – 使用者在目錄中的角色。
3. **日期和時間** – 嘗試的日期和時間。
4. **使用方法** – 使用者用於此重設作業的驗證方法。
5. **結果** – 密碼重設作業的最終結果。
6. **詳細資料** – 為什麼密碼重設會導致所產生的值的詳細資料。也包含任何防範措施，您可能會採取以解決未預期的錯誤。

### 報告值說明
下表描述每個資料行允許的不同值：

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>資料行</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>允許的值及其意義</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <p>使用方法</p>
              </td>
              <td>
                <ul>
                  <li class="unordered">
                    <strong>備用電子郵件</strong> – 使用者用來驗證的備用電子郵件或驗證電子郵件<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>辦公室電話</strong> – 使用者用來驗證的辦公室電話<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>行動電話</strong> – 使用者用來驗證的行動電話或驗證電話<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>安全性問題</strong> – 使用者用來驗證的安全性問題<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>上述項目的任意組合 (例如，備用電子郵件 + 行動電話)</strong> – 發生時機為當指定 2 個閘道原則，且顯示使用者使用哪兩個方法來驗證其密碼重設要求時。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>結果</p>
              </td>
              <td>
                <ul>
                  <li class="unordered">
                    <strong>Abandoned</strong> – 使用者開始重設密碼，但是又中途停止而未完成<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Blocked</strong> – 由於在 24 小時期間內嘗試使用密碼重設頁面或單一密碼重設閘道太多次，所以已防止使用者帳戶使用密碼重設<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Cancelled</strong> – 使用者開始重設密碼，但是隨後按一下 [取消] 按鈕，在中途取消工作階段 <br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Contacted Admin</strong> – 使用者在其工作階段期間有無法解決的問題，所以使用者按一下「連絡您的系統管理員」連結，而不是完成密碼重設流程<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Failed</strong> – 使用者無法重設密碼，可能是因為使用者未設定使用功能 (例如，沒有授權、遺失驗證資訊、在內部部署管理密碼但是回寫已關閉)。<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Succeeded</strong> – 密碼重設成功。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>詳細資料</p>
              </td>
              <td>
                <p>請參閱下表</p>
              </td>
            </tr>
          </tbody></table>

### 允許的詳細資料資料行的值
以下是使用密碼重設活動報告時，您預期的結果類型清單：

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>詳細資料</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>結果類型</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <p>在完成電子郵件驗證選項之後使用者已放棄</p>
              </td>
              <td>
                <p>Abandoned</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>在完成行動 SMS 驗證選項之後使用者已放棄</p>
              </td>
              <td>
                <p>Abandoned</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>在完成行動語音通話驗證選項之後使用者已放棄</p>
              </td>
              <td>
                <p>Abandoned</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>在完成辦公室語音通話驗證選項之後使用者已放棄</p>
              </td>
              <td>
                <p>Abandoned</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>在完成安全性問題選項之後使用者已放棄</p>
              </td>
              <td>
                <p>Abandoned</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>在輸入其使用者識別碼之後使用者已放棄</p>
              </td>
              <td>
                <p>Abandoned</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>在開始電子郵件驗證選項之後使用者已放棄</p>
              </td>
              <td>
                <p>Abandoned</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>在開始行動 SMS 驗證選項之後使用者已放棄</p>
              </td>
              <td>
                <p>Abandoned</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>在開始行動語音通話驗證選項之後使用者已放棄</p>
              </td>
              <td>
                <p>Abandoned</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>在開始辦公室語音通話驗證選項之後使用者已放棄</p>
              </td>
              <td>
                <p>Abandoned</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>在開始安全性問題選項之後使用者已放棄</p>
              </td>
              <td>
                <p>Abandoned</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>選取新密碼之前使用者已放棄</p>
              </td>
              <td>
                <p>Abandoned</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>選取新密碼之際使用者已放棄</p>
              </td>
              <td>
                <p>Abandoned</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>使用者輸入太多無效的電子郵件驗證碼，因此封鎖 24 小時</p>
              </td>
              <td>
                <p>Blocked</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>使用者輸入太多無效的 SMS 驗證碼，因此封鎖 24 小時</p>
              </td>
              <td>
                <p>Blocked</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>使用者嘗試行動電話語音驗證太多次，因此封鎖 24 小時</p>
              </td>
              <td>
                <p>Blocked</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>使用者嘗試辦公室電話語音驗證太多次，因此封鎖 24 小時</p>
              </td>
              <td>
                <p>Blocked</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>使用者嘗試回答安全性問題太多次，因此封鎖 24 小時</p>
              </td>
              <td>
                <p>Blocked</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>使用者嘗試驗證電話號碼太多次，因此封鎖 24 小時</p>
              </td>
              <td>
                <p>Blocked</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>在傳遞必要的驗證方法之前使用者已取消</p>
              </td>
              <td>
                <p>Cancelled</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>在提交新密碼之前使用者已取消</p>
              </td>
              <td>
                <p>Cancelled</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>在嘗試電子郵件驗證選項之後使用者連絡系統管理員</p>
              </td>
              <td>
                <p>Contacted admin</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>在嘗試行動 SMS 驗證選項之後使用者連絡系統管理員</p>
              </td>
              <td>
                <p>Contacted admin</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>在嘗試行動語音通話驗證選項之後使用者連絡系統管理員</p>
              </td>
              <td>
                <p>Contacted admin</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>在嘗試辦公室語音通話驗證選項之後使用者連絡系統管理員</p>
              </td>
              <td>
                <p>Contacted admin</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>在嘗試安全性問題驗證選項之後使用者連絡系統管理員</p>
              </td>
              <td>
                <p>Contacted admin</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>這位使用者未啟用密碼重設。在 [設定] 索引標籤底下啟用密碼重設以解決此問題</p>
              </td>
              <td>
                <p>Failed</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>使用者沒有授權。您可以新增授權給使用者以解決此問題</p>
              </td>
              <td>
                <p>Failed</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>使用者嘗試從未啟用 cookie 的裝置重設</p>
              </td>
              <td>
                <p>Failed</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>使用者的帳戶已定義驗證方法不足。新增驗證資訊以解決此問題</p>
              </td>
              <td>
                <p>Failed</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>使用者的密碼是在內部部署進行管理。您可以啟用密碼回寫以解決此問題</p>
              </td>
              <td>
                <p>Failed</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>我們無法取得您的內部部署密碼重設服務。請檢查您的同步處理電腦的事件記錄檔</p>
              </td>
              <td>
                <p>Failed</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>我們在重設使用者的內部部署密碼時發現問題。請檢查您的同步處理電腦的事件記錄檔</p>
              </td>
              <td>
                <p>Failed</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>此使用者不是密碼重設使用者群組的成員。將此使用者加入至該群組中以解決此問題。</p>
              </td>
              <td>
                <p>Failed</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>已針對此租用戶完全停用密碼重設。請參閱 <a href="http://aka.ms/ssprtroubleshoot">http://aka.ms/ssprtroubleshoot</a> 來解決這個問題。</p>
              </td>
              <td>
                <p>Failed</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>使用者成功重設密碼</p>
              </td>
              <td>
                <p>Succeeded</p>
              </td>
            </tr>
          </tbody></table>

<br/> <br/> <br/>

**其他資源**


* [密碼管理是什麼](active-directory-passwords.md)
* [密碼管理如何運作](active-directory-passwords-how-it-works.md)
* [開始使用密碼管理](active-directory-passwords-getting-started.md)
* [自訂密碼管理](active-directory-passwords-customize.md)
* [密碼管理最佳作法](active-directory-passwords-best-practices.md)
* [密碼管理常見問題集](active-directory-passwords-faq.md)
* [疑難排解密碼管理](active-directory-passwords-troubleshoot.md)
* [深入了解](active-directory-passwords-learn-more.md)
* [MSDN 上的密碼管理](https://msdn.microsoft.com/library/azure/dn510386.aspx)



[001]: ./media/active-directory-passwords-get-insights/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-get-insights/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-get-insights/003.jpg "Image_003.jpg"
 

<!---HONumber=62-->