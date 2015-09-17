<properties 
	pageTitle="自訂：Azure AD 密碼管理 |Microsoft Azure" 
	description="如何自訂 Azure AD 中的密碼管理外觀、行為和通知，以符合您的需求。" 
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

# 自訂密碼管理以符合您的組織的需求
為了給予您的使用者最佳的體驗，我們建議您探索和試驗您可以使用的所有密碼管理組態選項。事實上，您可以前往 [Azure 管理入口網站](https://manage.windowsazure.com)的 [**Active Directory 延伸模組**] 中的組態索引標籤，立即開始探索。本主題會引導您從 [Azure 管理入口網站](https://manage.windowsazure.com)中目錄的 [**設定**] 索引標籤，完成身為系統管理員可以進行的不同密碼管理自訂，包括：

- [**自訂密碼管理外觀**](#password-managment-look-and-feel)
- [**自訂使用者密碼管理行為**](#password-management-behavior)
- [**自訂密碼管理通知**](#password-management-notifications)

## 密碼管理外觀
下表描述每個控制項如何影響使用者註冊密碼重設及重設其密碼的體驗。您可以在 [Azure 管理入口網站](https://manage.windowsazure.com)中目錄的 [**設定**] 索引標籤的 [**目錄內容**] 區段底下，設定這些選項。

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>原則控制</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>說明</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>影響？</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <p>目錄名稱</p>
              </td>
              <td>
                <p>決定使用者或系統管理員會在密碼重設電子郵件通訊上看到什麼組織名稱</p>
              </td>
              <td>
                <p>
                  <strong>「連絡您的系統管理員」電子郵件：</strong>
                </p>
                <ul>
                  <li class="unordered">
												決定寄件地址的易記名稱，例如，「Microsoft 代表 <strong>Wingtip Toys</strong>」<br><br></li>
                  <li class="unordered">
												決定電子郵件的主旨名稱，例如，「<strong>Wingtip Toys</strong> 帳戶電子郵件驗證碼」<br><br></li>
                </ul>
                <p>
                  <strong>密碼重設電子郵件：</strong>
                </p>
                <ul>
                  <li class="unordered">
												決定寄件地址的易記名稱，例如，「Microsoft 代表 <strong>Wingtip Toys</strong>」<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>登入和存取面板頁面外觀</p>
              </td>
              <td>
                <p>決定造訪密碼重設頁面的使用者是否會看到 Microsoft 標誌或您自己的自訂標誌。這個組態項目也會將您的商標加入至存取面板和登入頁面。</p>
                <p>
                  
                </p>
                <p>您可以在<a href="https://technet.microsoft.com/library/dn532270.aspx">將公司商標加入至您的登入和存取面板頁面</a>深入了解租用戶商標和自訂功能。</p>
              </td>
              <td>
                <p>
                  <strong>密碼重設入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
												決定在密碼重設入口網站頂端顯示您的標誌，而不是預設 Microsoft 標誌。<br><br></li>
                  <li class="unordered">
                    <strong>附註：</strong>如果您直接進入密碼重設頁面，可能無法在密碼重設入口網站的第一頁上看見您的標誌。一旦使用者輸入他或她的使用者識別碼，並按 [下一步]，您的標誌會出現。您可以藉由將 whr 參數傳遞至密碼重設頁面，強制您的標誌顯示在頁面載入上，如下所示：<a href="https://passwordreset.microsoftonline.com?whr=wingtiptoysonline.com">https://passwordreset.microsoftonline.com?whr=wingtiptoysonline.com</a><br><br></li>
                </ul>
                <p>
                  <strong>「連絡您的系統管理員」電子郵件：</strong>
                </p>
                <ul>
                  <li class="unordered">
												決定當使用者選擇按一下密碼重設 UI 上的「連絡您的系統管理員」連結連絡您的時候，是否在傳送給系統管理員的電子郵件底端顯示您的標誌。<br><br></li>
                </ul>
                <p>
                  <strong>密碼重設電子郵件：</strong>
                </p>
                <ul>
                  <li class="unordered">
												決定當使用者重設其密碼時，是否在傳送給使用者的電子郵件底端顯示您的標誌。<br><br></li>
                </ul>
              </td>
            </tr>
          </tbody></table>

## 密碼管理行為
下表描述每個控制項如何影響使用者註冊密碼重設及重設其密碼的體驗。您可以在 [Azure 管理入口網站](https://manage.windowsazure.com)中目錄的 [**設定**] 索引標籤的 [**使用者密碼重設原則**] 區段底下，設定這些選項。

> [AZURE.NOTE]您使用的系統管理員帳戶必須具有指派的 AAD Premium 授權，才能看到這些原則控制項。<br><br>這些原則控制項只適用於使用者重設其密碼，不適用於系統管理員。**系統管理員擁有備用電子郵件和/或行動電話的預設原則，由 Microsoft 為其指定且無法變更。**

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>原則控制</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>說明</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>影響？</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <p>使用者已啟用密碼重設</p>
              </td>
              <td>
                <p>判對是否對此目錄中的使用者啟用密碼重設。</p>
              </td>
              <td>
                <p>
                  <strong>註冊入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
												如果設定為否，則沒有使用者可以註冊自己的挑戰資料。<br><br></li>
                  <li class="unordered">
												如果設定為是，則目錄中的任何使用者都可以前往位於 <a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a> 的註冊入口網站，註冊挑戰資料。<br><br></li>
                  <li class="unordered">
                    <strong>附註：</strong>使用者必須擁有受指派的 Azure AD Premium 或 Basic 授權，才可以註冊密碼重設。<br><br></li>
                </ul>
                <p>
                  <strong>密碼重設入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
												如果設定為否，使用者會看到訊息，指出必須連絡系統管理員重設其密碼。<br><br></li>
                  <li class="unordered">
												如果設定為是，使用者就能夠自動重設其密碼，方法是移至 <a href="http://passwordreset.microsoftonline.com">http://passwordreset.microsoftonline.com</a>，或按一下任何組織識別碼登入頁面上的 [<strong>無法存取您的帳戶</strong>] 連結。<br><br></li>
                  <li class="unordered">
                    <strong>附註：</strong>使用者必須擁有受指派的 Azure AD Premium 或 Basic 授權，才可以重設其密碼。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>密碼重設限制存取</p>
              </td>
              <td>
                <p>決定是否只允許特定群組使用者使用密碼重設。(只有在 [<strong>使用者啟用密碼重設</strong>] 設為 [<strong>是</strong>] 時才能看見)。</p>
              </td>
              <td>
                <p>
                  <strong>註冊入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
												如果設定為否，則在您的目錄中的所有使用者都可以在下列位置註冊密碼重設：<a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a><br><br></li>
                  <li class="unordered">
												如果設定為是，則只有在 [<strong>可以執行密碼重設的群組</strong>] 控制項中指定的使用者可以在下列位置註冊密碼重設：<a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a><br><br></li>
                </ul>
                <p>
                  <strong>密碼重設入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
												如果設定為否，則在您的目錄中的所有使用者都可以重設其密碼。<br><br></li>
                  <li class="unordered">
												如果設定為是，則只有在 [<strong>可以執行密碼重設的群組</strong>] 控制項中指定的使用者可以重設其密碼。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>可以執行密碼重設的群組</p>
              </td>
              <td>
                <p>決定允許哪個使用者群組使用密碼重設。</p>
                <p>
                  
                </p>
                <p>(只有在 [<strong>密碼重設限制存取</strong>] 設為 [<strong>是</strong>] 時才能看見)。</p>
              </td>
              <td>
                <p>
                  <strong>注意</strong>：
                </p>
                <ul>
                  <li class="unordered">
												如果未指定任何群組，而且您按一下 [<strong>儲存</strong>]，則會為您建立稱為 <strong>SSPRSecurityGroupUsers</strong> 的空群組。<br><br></li>
                  <li class="unordered">
												如果您想要指定自己的群組，可以提供自己的顯示名稱。<br><br></li>
                </ul>
                <p>
                  <strong>註冊入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
												如果 [<strong>密碼重設限制存取</strong>] 設為 [<strong>是</strong>]，則只有這個群組中的使用者能夠註冊密碼重設。<br><br></li>
                </ul>
                <p>
                  <strong>密碼重設入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
												如果 [<strong>密碼重設限制存取</strong>] 設為 [<strong>是</strong>]，則只有這個群組中的使用者能夠重設其密碼。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>使用者可用的驗證方法</p>
              </td>
              <td>
                <p>決定允許使用者使用哪些挑戰以重設其密碼。</p>
                <p>
                  
                </p>
                <p>(只有在 [<strong>使用者啟用密碼重設</strong>] 設為 [<strong>是</strong>] 時才能看見)。</p>
              </td>
              <td>
                <p>
                  
                </p>
                <p>
                  
                </p>
                <p>
                  <strong>注意</strong>：
                </p>
                <ul>
                  <li class="unordered">
												必須選取至少一個選項。<br><br></li>
                  <li class="unordered">
												我們強烈建議您啟用至少 2 個選項，在您的使用者重設其密碼時給予最大的彈性。<br><br></li>
                  <li class="unordered">
												如果您使用安全性問題，我們強烈建議您使用它們搭配其他驗證方法，因為安全性問題比電話或電子郵件密碼重設方法較不安全。<br><br></li>
                </ul>
                <p>
                  <strong>使用哪些目錄欄位？</strong>
                </p>
                <ul>
                  <li class="unordered">
												辦公室電話對應至目錄中使用者物件上的 [<strong>辦公室電話</strong>] 屬性。<br><br></li>
                  <li class="unordered">
												行動電話對應至目錄中使用者物件上的 [<strong>驗證行動</strong>] 屬性 (不公開可見) 或 [<strong>行動電話</strong>] 屬性 (公開可見)。服務會先檢查 [<strong>驗證電話</strong>] 的資料，如果沒有任何資料存在，就會回到 [<strong>行動電話</strong>] 屬性。<br><br></li>
                  <li class="unordered">
												備用電子郵件地址對應至目錄中使用者物件上的 [<strong>驗證電子郵件</strong>] 屬性 (不公開可見) 或 [<strong>備用電子郵件</strong>] 屬性 (公開可見)。服務會先檢查 [<strong>驗證電子郵件</strong>] 的資料，如果沒有任何資料存在，就會回到 [<strong>備用電子郵件</strong>] 屬性。<br><br></li>
                  <li class="unordered">
												安全性問題會私密且安全地儲存在目錄中的使用者物件上，只能在註冊期間由使用者回答。基於安全性考量，目前系統管理員無法編輯或者查看這些答案。<br><br></li>
                  <li class="unordered">
                    <strong>附註：</strong>依預設，只有雲端屬性辦公室電話和行動電話會從您的內部部署目錄同步處理至您的雲端目錄。若要深入了解哪些內部部署屬性會同步處理至雲端，請參閱 <a href="https://msdn.microsoft.com/library/azure/dn764938.aspx">屬性同步處理至 Azure AD</a>。<br><br></li>
                </ul>
                <p>
                  <strong>註冊入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
												會影響當使用者註冊時，所顯示的驗證方法。如果您未啟用指定的驗證方法，使用者將無法自行註冊該驗證方法。<br><br></li>
                  <li class="unordered">
                    <strong>附註：</strong>使用者目前無法註冊自己的辦公室電話號碼；必須由系統管理員定義該驗證方法。<br><br></li>
                </ul>
                <p>
                  <strong>密碼重設入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
												決定使用者可以針對指定驗證步驟使用哪些驗證方法做為挑戰。例如，如果使用者在 Azure Active Directory 中的 [<strong>辦公室電話</strong>] 和 [<strong>驗證電話</strong>] 欄位都具有資料，則他或她可以使用其中一種驗證方法來復原其密碼。<br><br></li>
                  <li class="unordered">
                    <strong>附註：</strong>使用者只有當您已啟用為系統管理員的驗證方法中有資料存在時，才可以重設其密碼。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>必要驗證方法數目</p>
              </td>
              <td>
                <p>判斷使用者必須通過才能重設其密碼的可用驗證方法數目下限。</p>
                <p>
                  
                </p>
                <p>(只有在 [<strong>使用者啟用密碼重設</strong>] 設為 [<strong>是</strong>] 時才能看見)。</p>
              </td>
              <td>
                <p>
                  <strong>注意</strong>：
                </p>
                <ul>
                  <li class="unordered">
												可以設為 1 或 2 個必要驗證方法。<br><br></li>
                </ul>
                <p>
                  <strong>註冊入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
												決定使用者在完成註冊體驗之前，必須註冊的驗證方法數目下限。<br><br></li>
                </ul>
                <p>
                  <strong>密碼重設入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
												會影響使用者能夠重設密碼之前，必須通過的驗證步驟數目。驗證步驟是定義為使用者使用一段驗證資訊 (例如撥打其辦公室電話，或是傳送至備用電子郵件的電子郵件) 以驗證其帳戶。<br><br></li>
                  <li class="unordered">
                    <strong>附註：</strong>如果使用者在他或她的帳戶上沒有根據您已設定之原則才能成功重設其密碼進行定義的必要數目驗證資訊，則使用者會看到錯誤頁面，引導他們要求系統管理員重設其密碼。 <br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>註冊所需的問題數目</p>
              </td>
              <td>
                <p>決定當註冊安全性問題選項時，使用者必須回答的問題數目下限。</p>
                <p>(只有在已啟用 [<strong>安全性問題</strong>] 核取方塊時才能看見)。</p>
              </td>
              <td>
                <p>
                  <strong>注意</strong>：
                </p>
                <ul>
                  <li class="unordered">
												可以設定為 3 – 5 個註冊所需的問題。<br><br></li>
                  <li class="unordered">
												註冊所需的問題數目必須大於或等於重設所需的問題數目。<br><br></li>
                  <li class="unordered">
												我們建議您將註冊所需的問題數目設定為大於重設所需的問題數目，讓使用者在重設其密碼時享有更大的彈性。因為我們會從已註冊的所有問題的集區中隨機選取問題讓使用者回答，所以這是更安全的組態。<br><br></li>
                </ul>
                <p>
                  <strong>註冊入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
												決定使用者被視為完全註冊密碼重設之前，使用者必須回答的問題數目下限。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>重設所需的問題數目 </p>
              </td>
              <td>
                <p>決定當重設密碼時，使用者必須回答的問題數目下限。</p>
                <p>
                  
                </p>
                <p>(只有在已啟用 [<strong>安全性問題</strong>] 核取方塊時才能看見)。</p>
              </td>
              <td>
                <p>
                  <strong>注意</strong>：
                </p>
                <ul>
                  <li class="unordered">
												可以設定為 3 – 5 個重設所需的問題。<br><br></li>
                  <li class="unordered">
												重設所需的問題數目必須小於或等於註冊所需的問題數目。<br><br></li>
                </ul>
                <p>
                  <strong>密碼重設入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
												決定在使用者的密碼可以重設之前，使用者必須回答的問題數目下限。<br><br></li>
                  <li class="unordered">
												重設密碼時，會從使用者已註冊問題總清單中，隨機選取這個數目的問題。例如，如果使用者有 5 個已註冊的問題，當使用者重設密碼時，會從其中隨機選取 3 個問題。使用者必須正確回答所有問題，才能重設密碼。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>安全性問題</p>
              </td>
              <td>
                <p>定義您的使用者在註冊密碼重設以及重設其密碼時，可能會選取的安全性問題。</p>
                <p>
                  
                </p>
                <p>(只有在已啟用 [<strong>安全性問題</strong>] 核取方塊時才能看見)。</p>
              </td>
              <td>
                <p>
                  <strong>注意</strong>：
                </p>
                <ul>
                  <li class="unordered">
												可以定義最多 20 個問題。<br><br></li>
                  <li class="unordered">
												問題字元最大值限制為 200 個字元。<br><br></li>
                  <li class="unordered">
												答案字元最小值限制為 3 個字元。<br><br></li>
                  <li class="unordered">
												答案字元最大值限制為 40 個字元。<br><br></li>
                  <li class="unordered">
												使用者不能回答相同的問題兩次。<br><br></li>
                  <li class="unordered">
												使用者不能對兩個不同的問題提供相同的答案兩次。<br><br></li>
                  <li class="unordered">
												可以使用任何字元集以定義問題和答案 (包括 Unicode 字元)。<br><br></li>
                  <li class="unordered">
												定義的的問題數目必須大於或等於註冊所需的問題數目。<br><br></li>
                  <li class="unordered">
												尚未支援對不同的地區設定定義不同的問題，但是未來將會支援。<br><br></li>
                </ul>
                <p>
                  <strong>註冊入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
												決定當註冊密碼重設時，使用者可以對哪些問題提供答案。<br><br></li>
                </ul>
                <p>
                  <strong>密碼重設入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
												決定使用者可以使用哪些問題來重設密碼。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>登入存取面板時需要使用者註冊？</p>
                <p>
                  
                </p>
              </td>
              <td>
                <p>決定使用者下一次登入存取面板時是否需要註冊連絡資料以進行密碼重設。</p>
                <p>
                  
                </p>
                <p>(只有在 [<strong>使用者啟用密碼重設</strong>] 設為 [<strong>是</strong>] 時才能看見)。</p>
              </td>
              <td>
                <p>
                  
                </p>
                <p>
                  
                </p>
                <p>
                  <strong>注意</strong>：
                </p>
                <ul>
                  <li class="unordered">
												如果您停用這項功能，您也可以手動將使用者傳送至 <a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a> 以註冊其連絡資料。 <br><br></li>
                  <li class="unordered">
												使用者也可以藉由按一下存取面板中設定檔索引標籤底下的 [<strong>註冊密碼重設</strong>] 連結，連接到註冊入口網站。<br><br></li>
                  <li class="unordered">
												透過這種方式註冊可以藉由按一下 [取消] 按鈕或關閉視窗來關閉，但是如果使用者未註冊，則每次登入時都會被叨擾。<br><br></li>
                </ul>
                <p>
                  <strong>註冊入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
												此設定不會影響註冊入口網站本身的行為，而是會決定當使用者登入存取面板時是否對其顯示註冊入口網站。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>使用者必須確認其連絡資料的天數</p>
              </td>
              <td>
                <p>當 [<strong>要求使用者註冊</strong>] 開啟時，此設定會決定使用者必須重新確認其資料之前所經過的時間長度。</p>
                <p>
                  
                </p>
                <p>(只有在 [<strong>當登入存取面板時要求使用者註冊</strong>] 設為 [<strong>是</strong>] 時才能看見)。</p>
              </td>
              <td>
                <p>
                  
                </p>
                <p>
                  
                </p>
                <p>
                  <strong>注意：</strong>
                </p>
                <ul>
                  <li class="unordered">
												接受介於 0-730 天的值，0 天表示永遠不會要求使用者重新確認其連絡資料。<br><br></li>
                </ul>
                <p>
                  <strong>註冊入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
												此設定不會影響註冊入口網站本身的行為，而是會決定當使用者的連絡資料需要確認時是否對其顯示註冊入口網站。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>自訂連絡您的系統管理員連結？</p>
              </td>
              <td>
                <p>控制當發生錯誤或使用者於作業等候太久時，在密碼重設入口網站上顯示的連絡您的系統管理員連結 (顯示在左側) 是否指向自訂 URL 或電子郵件地址。</p>
                <p>
                  
                </p>
                <p>(只有在 [<strong>使用者啟用密碼重設</strong>] 設為 [<strong>是</strong>] 時才能看見)。</p>
              </td>
              <td>
                <p>
                  <strong>注意：</strong>
                </p>
                <ul>
                  <li class="unordered">
												如果您啟用此設定，您必須選擇自訂 URL 或電子郵件地址，方法是立即篩選此設定下方的 [<strong>自訂電子郵件地址或 URL</strong>] 欄位。<br><br></li>
                </ul>
                <p>
                  <strong>密碼重設入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
												如果設定為否，使用者按一下反白顯示的連結會發送電子郵件給要求重設其密碼之所有租用戶系統管理員的主要電子郵件地址。這封電子郵件是以下列邏輯發送：<br><br></li>
                  <li class="unordered">
                    <ul>
                      <li class="unordered">
														如果有密碼管理員，則傳送電子郵件給所有密碼管理員，總計最多 100 個收件者。<br><br></li>
                      <li class="unordered">
														如果沒有密碼管理員，則傳送電子郵件給所有使用者系統管理員，總計最多 100 個收件者。<br><br></li>
                      <li class="unordered">
														如果沒有使用者系統管理員，則傳送電子郵件給所有全域管理員，總計最多 100 個收件者。<br><br></li>
                    </ul>
                  </li>
                  <li class="unordered">
												如果設定為是，此設定會自訂上述反白顯示連結的行為，以指向自訂 URL 或電子郵件地址，您的使用者可以瀏覽以取得密碼重設的協助。<br><br></li>
                  <li class="unordered">
												如果您指定 URL，它會在新索引標籤中開啟。<br><br></li>
                  <li class="unordered">
												如果您指定電子郵件地址，我們會建立該電子郵件地址的 mailto 連結。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>自訂電子郵件地址或 URL</p>
              </td>
              <td>
                <p>控制 [<strong>連絡您的系統管理員</strong>] 連結所指向的電子郵件地址或 URL。</p>
                <p>
                  
                </p>
                <p>(只有在 [<strong>自訂連絡您的系統管理員連結</strong>] 設定為 [<strong>是</strong>] 時才能看見)。</p>
              </td>
              <td>
                <p>
                  <strong>注意</strong>：
                </p>
                <ul>
                  <li class="unordered">
												必須是有效的內部網路或外部網路 URL 或電子郵件地址。<br><br></li>
                </ul>
                <p>
                  <strong>密碼重設入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
												變更 [<strong>連絡您的系統管理員</strong>] 連結指向的位置。<br><br></li>
                  <li class="unordered">
												如果您提供電子郵件地址，連結會變成該電子郵件地址的 “mailto” 連結。<br><br></li>
                  <li class="unordered">
												如果您提供 URL，連結會變成指向該 URL 的標準 href，在新索引標籤中開啟。 <br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>將密碼回寫至內部部署目錄</p>
              </td>
              <td>
                <p>控制是否對此目錄啟用密碼回寫，如果回寫開啟，則表示內部部署回寫服務的狀態。</p>
                <p>
                  
                </p>
                <p>如果您由於中斷想要暫時停用服務，這非常有用。</p>
              </td>
              <td>
                <p>
                  
                </p>
                <p>
                  <strong>注意</strong>：
                </p>
                <ul>
                  <li class="unordered">
												只有在您已藉由下載最新版本的 Azure AD Connect 並且在 [<strong>選用功能</strong>] 選取畫面底下啟用 [<strong>密碼回寫</strong>] 以安裝密碼回寫時，此控制項才會顯示。<br><br></li>
                  <li class="unordered">
												如果您已啟用密碼回寫，而且覺得服務有組態問題，您可以前往這個索引標籤，並查看 [<strong>密碼寫回服務狀態</strong>] 標籤以查看是否有錯誤。<br><br></li>
                  <li class="unordered">
												可以顯示的狀態如下：<br><br><ul><li class="unordered"><strong>已設定</strong> – 一切如預期般運作<br><br></li><li class="unordered"><strong>未設定</strong> – 您已安裝回寫，但我們無法取用服務，請檢查以確定您並未封鎖與 443 的輸出連線，如果仍有問題，嘗試重新安裝服務。<br><br></li></ul></li>
                </ul>
                <p>
                  <strong>註冊入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
												如果回寫已部署及設定，且此參數設定為 [<strong>否</strong>]，則回寫會停用，而且同盟和密碼雜湊同步處理使用者將無法註冊密碼重設，以重設其密碼。<br><br></li>
                  <li class="unordered">
												如果此參數設定為 [<strong>是</strong>]，則回寫會啟用，而且同盟和密碼雜湊同步處理使用者可以重設其密碼。<br><br></li>
                </ul>
                <p>
                  <strong>密碼重設入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
												如果回寫已部署及設定，且此參數設定為 [<strong>否</strong>]，則回寫會停用，而且同盟和密碼雜湊同步處理使用者將無法重設其密碼。<br><br></li>
                  <li class="unordered">
												如果此參數設定為 [<strong>是</strong>]，則回寫會啟用，而且同盟和密碼雜湊同步處理使用者可以重設其密碼。<br><br></li>
                </ul>
              </td>
            </tr>
          </tbody></table>

## 密碼管理通知
下表描述每個控制項如何影響接收密碼重設通知之使用者和系統管理員的體驗。您可以在 [Azure 管理入口網站](https://manage.windowsazure.com)中目錄的 [**設定**] 索引標籤的 [**通知**] 區段底下，設定這些選項。

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>原則控制</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>說明</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>影響？</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <p>當其他系統管理員重設自己的密碼時通知系統管理員</p>
              </td>
              <td>
                <p>決定當任何類型的其他系統管理員重設自己的密碼時，是否透過傳送至其主要電子郵件地址的電子郵件，通知所有全域管理員。</p>
              </td>
              <td>
                <p>
                  <strong>密碼重設入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
												如果設定為否，則不會傳送通知。<br><br></li>
                  <li class="unordered">
												如果設定為是，則當任何單一系統管理員重設自己的密碼時，會通知所有全域管理員。<br><br></li>
                  <li class="unordered">
												此通知是透過傳送至組織中所有其他全域管理員主要電子郵件地址的電子郵件來傳送的。<br><br></li>
                </ul>
                <p>
                  <strong>範例：</strong>
                </p>
                <ul>
                  <li class="unordered">
												如果已啟用此選項，當系統管理員 A 重設其密碼，且租用戶中有其他 3 個系統管理員 B、C 和 D，則系統管理員 B、C 和 D 會收到一封電子郵件，表示系統管理員 A 重設其密碼。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>當使用者和系統管理員的密碼被重設時通知他們</p>
              </td>
              <td>
                <p>決定重設自己密碼的使用者或系統管理員是否會收到電子郵件通知，表示他們的密碼已重設。</p>
              </td>
              <td>
                <p>
                  <strong>密碼重設入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
												如果設定為否，則不會傳送通知。<br><br></li>
                  <li class="unordered">
												如果設定為是，則每當使用者或系統管理員重設自己的密碼時，他或她會收到通知，指出他或她的密碼已重設。<br><br></li>
                  <li class="unordered">
												此通知是透過傳送至重設他或她的密碼之使用者的主要和備用 (或驗證) 電子郵件地址的電子郵件來傳送的。<br><br></li>
                </ul>
              </td>
            </tr>
          </tbody></table>


<br/> <br/> <br/>

**其他資源**


* [密碼管理是什麼](active-directory-passwords.md)
* [密碼管理如何運作](active-directory-passwords-how-it-works.md)
* [開始使用密碼管理](active-directory-passwords-getting-started.md)
* [密碼管理最佳作法](active-directory-passwords-best-practices.md)
* [如何使用密碼管理報告取得 Operational Insights](active-directory-passwords-get-insights.md)
* [密碼管理常見問題集](active-directory-passwords-faq.md)
* [疑難排解密碼管理](active-directory-passwords-troubleshoot.md)
* [深入了解](active-directory-passwords-learn-more.md)
* [MSDN 上的密碼管理](https://msdn.microsoft.com/library/azure/dn510386.aspx) 

<!---HONumber=August15_HO6-->