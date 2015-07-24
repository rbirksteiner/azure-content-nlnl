<properties 
	pageTitle="Azure Multi-Factor Authentication 報告" 
	description="說明如何使用 Azure Multi-Factor Authentication 功能 - 報告。" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/02/2015" 
	ms.author="billmath"/>

# Azure Multi-Factor Authentication 中的報告

Azure Multi-Factor Authentication 提供數個供您和貴組織使用的報告。這些報告可透過 Multi-Factor Authentication 管理入口網站來加以存取。以下是可用的報告清單。

您可以透過 Azure 管理入口網站來存取報告。

名稱| 說明
:------------- | :------------- | 
使用量 | 使用量報告顯示有關整體使用量、使用者摘要和使用者詳細資料等資訊。
伺服器狀態|此報告會顯示與帳戶相關聯之 Multi-Factor Authentication Server 的狀態。
已封鎖的使用者歷程記錄|這些報告會顯示要求封鎖或解除封鎖使用者之申請的歷程記錄。
已略過的使用者歷程記錄|顯示要求略過使用者電話號碼之 Multi-Factor Authentication 的申請歷程記錄。
詐騙警示|顯示在指定日期範圍內提交之詐騙警示的歷程記錄。
已排入佇列|列出已排入佇列並等候處理的報告和其狀態。當報告完成時，系統會提供下載或檢視報告的連結。

## 檢視報告

1. 登入 [http://azure.microsoft.com](http://azure.microsoft.com)
2. 在左側選取 [Active Directory]。
3. 在頂端選取 [Multi-Factor Auth Provider]。這會顯示您的 Multi-Factor Auth Provider 清單。
4. 如果您有一個以上的 Multi-Factor Auth Provider，請選取要檢視詐騙警示報告的提供者，然後按一下頁面底部的 [管理]。如果您只有一個，只要按一下 [管理] 即可。這會開啟 Azure Multi-factor Authentication 管理入口網站。
5. 在 Azure Multi-Factor Authentication 管理入口網站的左側，您可以看見 [檢視報表]。在這裡，您可以選取前文所述的報告。


 
<center>![Cloud](./media/multi-factor-authentication-manage-reports/report.png)</center>


**其他資源**

* [適用於使用者](multi-factor-authentication-end-user.md)
* [MSDN 上的 Azure Multi-Factor Authentication](https://msdn.microsoft.com/library/azure/dn249471.aspx)
 

<!---HONumber=July15_HO2-->