<properties 
	pageTitle="進一步了解 Azure AD Connect 認證和權限"
	description="Azure AD Connect 認證和權限的自訂設定說明。"
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
	ms.topic="article"
	ms.date="08/24/2015"
	ms.author="billmath"/>



# 進一步了解 Azure AD Connect 認證和權限 


Azure AD Connect 精靈提供兩種權限需求相異的不同路徑：

* 在快速設定中，我們需要更多權限，以便輕鬆設定您的組態，而不需要您建立使用者或個別設定權限。 

* 在自訂設定中，我們會提供您更多選擇和選項；但在某些情況下，您必須確定自己擁有正確的權限。


## 所收集的認證，以及該認證在快速設定中的用途

精靈頁面 | 收集的認證 | 所需的權限| 用於 
------------- | ------------- |------------- |------------- |
連線到 Azure AD| Azure AD 目錄認證 | Azure AD 中的全域管理員角色 | <li>啟用 Azure AD 目錄中的同步處理。</li> <li>建立將在 Azure AD 中用於持續同步處理作業的 Azure AD 帳戶。</li>
連線到 AD DS | 內部部署 Active Directory 認證 | Active Directory 中 Enterprise Admins (EA) 群組成員| 做為本機 AD Connector 帳戶；也就是說，該帳戶會讀取和寫入目錄資訊供同步處理。
NA|執行精靈之使用者的登入認證| 本機伺服器的系統管理員|精靈會建立 AD 帳戶，該帳戶將做為本機電腦上的同步處理服務登入帳戶。

<br> <br>


## 所收集的認證，以及該認證在自訂設定中的用途


精靈頁面 | 收集的認證 | 所需的權限| 用於 
------------- | ------------- |------------- |------------- 
NA|執行精靈之使用者的登入認證|本機伺服器的系統管理員| <li>根據預設，精靈會建立 AD 帳戶，該帳戶將做為本機電腦上的同步處理服務登入帳戶</li><li>如果系統管理員沒有指定特定帳戶，我們只會建立同步處理服務登入帳戶</li> <li>除非 DC 上的帳戶是網域使用者，否則該帳戶為本機使用者</li> 
安裝同步處理服務頁面，服務帳戶選項 | AD 或本機使用者帳戶認證 | 本機使用者|如果系統管理員指定帳戶，則此帳戶會做為同步處理服務登入帳戶。
連線到 Azure AD|Azure AD 目錄認證| Azure AD 中的全域管理員角色|精靈會建立 AD 帳戶，該帳戶將做為本機電腦上的同步處理服務登入帳戶。
連接您的目錄|各個將連線至 Azure AD 之樹系的內部部署 Active Directory 認證 |<li>精靈所需權限的最低層級是網域使用者。</li> <li>但是，該指定帳戶必須擁有適用案例所需的權限。</li><li>如果您想要為 Azure AD 設定密碼同步處理，請確定此帳戶擁有下列指派的權限：- 複寫目錄變更 - 複寫目錄全部變更</li> <li>如果您想要設定同步處理，以將資訊從 Azure AD「寫回」本機 AD，請確定該帳戶擁有將目錄物件和屬性回寫的寫入權限。</li> <li>如果您想要設定 AD FS 用於登入，請確定您為 AD FS 伺服器所在樹系提供的 AD 認證，擁有網域系統管理員權限。</li><li>請參閱下表適用於您案例的其他需求清單。</li>|<li>此帳戶將用於本機 AD 管理代理程式 (MA) 帳戶。該帳戶將用於讀取和寫入持續同步處理作業之本機 AD 中的物件和屬性。</li><li>指派正確權限及您選擇的同步處理選項存取控制設定至上述帳戶和 AD。</li>
AD FS 伺服器|在清單的各個伺服器中，如果執行精靈之使用者的登入認證權限不足無法連線，精靈就會收集認證|網域系統管理員|安裝和設定 AD FS 伺服器角色。|
Web 應用程式 Proxy 伺服器 |在清單的各個伺服器中，如果執行精靈之使用者的登入認證權限不足無法連線，精靈就會收集認證|目標電腦上的本機系統管理員。|安裝和設定 WAP 伺服器角色。
Proxy 信任憑證 |Federation Service 信任認證 (Proxy 將用來註冊 FS 信任憑證的認證) |網域帳戶是 AD FS 伺服器的本機系統管理員|FS-WAP 信任憑證的首次註冊
AD FS 服務帳戶頁面，「使用網域使用者帳戶選項」|AD 使用者帳戶認證|網域使用者|AD 使用者帳戶所提供的認證將做為 AD FS 服務的登入帳戶。



<br> <br>
## 特定案例所需的權限

案例 |權限
------------- | ------------- |
密碼同步處理| <li>複寫目錄變更。</li> <li>複寫目錄全部變更。</li>
Exchange 混合部署|請參閱 [Office 365 Exchange 混合 AAD Sync 寫回屬性和權限](https://msdn.microsoft.com/library/azure/dn757602.aspx#exchange)。
密碼回寫 | <li>變更密碼</li><li>重設密碼</li>
使用者、群組和裝置回寫|想要「寫回」之目錄物件和屬性的寫入權限
單一登入和 AD FS| 同盟伺服器所在網域中的網域系統管理員權限。

<br> <br>
## Azure AD Connect 所建立之帳戶的摘要



建立的帳戶 |指派的權限 | 用於
------------- | ------------- |------------- |
Azure AD 帳戶同步處理| 全域管理員|持續同步處理作業 (Azure AD MA 帳戶)
快速設定：用於同步處理的 AD 帳戶|視同步處理和密碼同步處理需要，讀取/寫入目錄上的權限|持續同步處理作業 (Azure AD MA 帳戶)
快速設定：同步處理服務登入帳戶 | 執行精靈之使用者的登入認證|同步處理服務登入帳戶
自訂設定：同步處理服務登入帳戶 |NA|同步處理服務登入帳戶
AD FS:GMSA 帳戶 (aadcsvc$)|網域使用者|FS 服務登入帳戶


**其他資源**



* [密碼同步化的權限](https://msdn.microsoft.com/library/azure/dn757602.aspx#psynch)
* [Exchange 混合的權限](https://msdn.microsoft.com/library/azure/dn757602.aspx#exchange)
* [密碼回寫的權限](https://msdn.microsoft.com/library/azure/dn757602.aspx#pwriteback)
* [自訂 Azure AD Connect 安裝](active-directory-aadconnect-get-started-custom.md)
* [MSDN 上的 Azure AD Connect](active-directory-aadconnect.md)
 

<!---HONumber=August15_HO9-->