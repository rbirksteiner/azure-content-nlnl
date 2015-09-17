
<properties 
	pageTitle="在 AD FS 使用 Azure AD Connect Health | Microsoft Azure"
	description="這是如何監視內部部署 AD FS 基礎結構的 Azure AD Connect Health 頁面。"
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
	ms.topic="get-started-article"
	ms.date="08/14/2015"
	ms.author="billmath"/>

# 在 AD FS 使用 Azure AD Connect Health 
下列文件適用於使用 Azure AD Connect Health 來監視 AD FS 基礎結構。

## AD FS 的警示
[Azure AD Connect Health 警示] 區段將為您提供作用中警示的清單。每個警示都包含相關資訊、解決步驟，以及相關文件的連結。選取作用中或已解決的警示，您將會看到一個包含額外資訊的新刀鋒視窗，以及解決警示可以採取的步驟，和其他文件的連結。您也可以檢視過去已解決的警示的歷史資料。

選取警示，將會為您提供其他資訊，以及解決警示可以採取的步驟，和其他文件的連結。

![Azure AD Connect Health 入口網站](./media/active-directory-aadconnect-health/alert2.png)



## AD FS 的使用情況分析
Azure AD Connect Health 使用情況分析會分析您的同盟伺服器的驗證流量。選取使用情況分析方塊將會開啟使用情況分析刀鋒視窗，以顯示度量和群組。

>[AZURE.NOTE]若要搭配 AD FS 使用使用情況分析，您必須確定已啟用 AD FS 稽核。如需詳細資訊，請參閱[啟用 AD FS 的稽核](active-directory-aadconnect-health-operations.md#enable-auditing-for-ad-fs)。

![Azure AD Connect Health 入口網站](./media/active-directory-aadconnect-health/report1.png)

若要選取其他度量，請指定時間範圍；若要變更群組，只要在使用情況分析圖表上按一下滑鼠右鍵，並選取 [編輯圖表]。接著，您可以指定時間範圍、變更或選取度量，以及變更群組。您可以根據不同的「度量」檢視驗證流量的分佈，並使用相關的「分組依據」參數，為每個度量分組，如下所述。

| 度量 | 分組依據 | 分組是什麼意思，為什麼分組很有用？ |
| ------ | -------- | -------------------------------------------- |
| 要求總數：同盟服務所處理的要求的總數 | 全部 | 這將會顯示未分組的要求總數的計數。 |
| | 應用程式 | 此選項將會根據目標信賴憑證者，為要求總數分組。這個分組對於了解哪個應用程式收到多少百分比的總流量非常有幫助。 |
| | 伺服器 | 此選項將會根據處理要求的伺服器，為要求總數分組。這個分組對於了解總流量的負載分佈非常有幫助。 |
| | 加入工作場所 | 此選項將會根據要求是否來自已加入工作場所的裝置 (已知)，為要求總數分組。這個分組對於了解是否使用識別基礎結構未知的裝置存取您的資源非常有幫助。 |
| | 驗證方法 | 此選項將會根據用於驗證的驗證方法，為要求總數分組。這個分組對於了解用於驗證的常見驗證方法非常有幫助。以下是可能的驗證方法<ol> <li>Windows 整合式驗證 (Windows)</li> <li>表單型驗證 (表單)</li> <li>SSO (單一登入)</li> <li>X 509 憑證驗證 (憑證)</li> <br>請注意，如果同盟伺服器收到包含 SSO Cookie 的要求，則該要求會列入 SSO (單一登入)。在這種情況下，如果 Cookie 有效，則不會要求使用者提供認證，並不間斷地存取應用程式。如果您有多個同盟伺服器所保護的信賴憑證者，則此情況非常常見。 |
| | 網路位置 | 此選項將會根據使用者的網路位置，為要求總數分組。它可以是內部網路或外部網路。這個分組對於了解流量百分比是來自內部網路還是外部網路非常有幫助。 |
| 失敗要求總數：同盟服務所處理的失敗要求總數。<br> (此度量僅能在適用於 Windows Server 2012 R2 的 AD FS 上使用)| 錯誤類型 | 這將會根據預先定義的錯誤類型，顯示錯誤數目。這個分組對於了解常見的錯誤類型非常有幫助。<ul><li>不正確的使用者名稱或密碼：由於不正確的使用者名稱或密碼而導致的錯誤。</li> <li>「外部網路鎖定」：由於從外部網路鎖定的使用者收到要求而導致的失敗</li><li>「過期的密碼」：由於使用者使用已過期的密碼登入而導致的失敗。</li><li>「停用的帳戶」：由於使用者使用已停用的帳戶登入而導致的失敗。</li><li>「裝置驗證」：由於使用者無法使用裝置驗證進行驗證而導致的失敗。</li><li>「使用者憑證驗證」:由於使用者因為憑證無效而無法進行驗證所導致的失敗。</li><li>「MFA」：由於使用者無法使用 Multi Factor Authentication 來進行驗證而導致的失敗。</li><li>「其他認證」：「發佈授權」：由於授權失敗而導致的失敗。</li><li>「發佈委派」：由於發佈委派錯誤而導致的失敗。</li><li>「權杖接受」：由於 ADFS 拒絕來自第三方身分識別提供者的權杖而導致的失敗。</li><li>「通訊協定」：由於通訊協定錯誤而導致的失敗。</li><li>「未知」：全部攔截。不適合已定義的類別的其他任何失敗。</li> |
| | 伺服器 | 這將會根據伺服器，為錯誤分組。這對於了解跨伺服器的錯誤分佈非常有幫助。分佈不平均可能是伺服器處於錯誤狀態的指標。 |
| | 網路位置 | 這將會根據要求的網路位置 (內部網路與外部網路)，為錯誤分組。這對於了解失敗的要求類型非常有幫助。 |
| | 應用程式 | 這將會根據目標應用程式 (信賴憑證者)，為失敗分組。這對於了解哪個目標應用程式將看到最多錯誤數目非常有幫助。 |
| 使用者計數：系統中有效的唯一使用者的平均數目 | 全部 | 這會提供在所選時間配量內使用同盟服務的使用者平均數目的計數。系統不會為使用者分組。<br>平均值將取決於選取的時間配量。 |
| | 應用程式 | 這將會根據目標應用程式 (信賴憑證者)，為使用者的平均數目分組。這對於了解有多少使用者正在使用哪一個應用程式非常有幫助。 |


## AD FS 的效能監視
Azure AD Connect Health 效能監視會提供關於度量的監視資訊。選取 [監視] 方塊將會開啟一個刀鋒視窗，以提供關於度量的詳細的資訊。


![Azure AD Connect Health 入口網站](./media/active-directory-aadconnect-health/perf1.png)


選取刀鋒視窗頂端的 [篩選] 選項，您可以依伺服器篩選以查看個別伺服器的度量。若要變更度量，只要在監視刀鋒視窗底下的監視圖表上按一下滑鼠右鍵，並選取 [編輯圖表]。接著，在開啟的新刀鋒視窗中，您可以從下拉式清單中選取其他度量，並指定檢視效能資料的時間範圍。




## 相關連結

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [適用於 AD FS 的 Azure AD Connect Health 代理程式安裝](active-directory-aadconnect-health-agent-install-adfs.md)
* [Azure AD Connect Health 操作](active-directory-aadconnect-health-operations.md)
* [Azure AD Connect Health 常見問題集](active-directory-aadconnect-health-faq.md)

<!---HONumber=August15_HO9-->