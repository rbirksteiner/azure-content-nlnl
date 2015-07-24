<properties 
	pageTitle="使用 Azure AD Connect 連接目錄" 
	description="Azure AD Connect 連接目錄的自訂設定說明。" 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory"  
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="billmath"/>



# 使用 Azure AD Connect 連接目錄

若為自訂設定，不需要將企業系統管理員帳戶連接到您的 Active Directory 樹系。精靈會接受網域或本機使用者帳戶。不過，該帳戶會做為本機 AD Connector 帳戶；也就是說，該帳戶會讀取和寫入目錄資訊供同步處理。

這表示您必須指派其他權限以啟用下列案例：

案例 |權限
------------- | ------------- |
密碼同步處理| <li>複寫目錄變更。</li> <li>複寫目錄全部變更。</li>
Exchange 混合部署|請參閱 [Office 365 Exchange 混合 AAD Sync 寫回屬性和權限](https://msdn.microsoft.com/library/azure/dn757602.aspx#exchange)。
密碼回寫 | <li>變更密碼</li><li>重設密碼</li>
使用者、群組和裝置回寫|想要「寫回」之目錄物件和屬性的寫入權限
單一登入和 AD FS| 同盟伺服器所在網域中的網域系統管理員權限。





**其他資源**

* [Azure AD Connect 帳戶與權限的詳細資訊](active-directory-aadconnect-account-summary.md)
* [密碼同步化的權限](https://msdn.microsoft.com/library/azure/dn757602.aspx#psynch)
* [Exchange 混合的權限](https://msdn.microsoft.com/library/azure/dn757602.aspx#exchange)
* [密碼回寫的權限](https://msdn.microsoft.com/library/azure/dn757602.aspx#pwriteback)
* [自訂 Azure AD Connect 安裝](active-directory-aadconnect-get-started-custom.md)
* [MSDN 上的 Azure AD Connect](https://msdn.microsoft.com/library/azure/dn832695.aspx)
 

<!---HONumber=62-->