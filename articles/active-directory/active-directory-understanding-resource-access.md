<properties 
                pageTitle="了解 Azure 中的資源存取" 
                description="本主題說明有關使用訂用帳戶管理員來控制整個 Azure 入口網站中資源存取的概念。" 
                services="active-directory" 
                documentationCenter="" 
                authors="markusvi" 
                manager="swadhwa" 
                editor=""/>

<tags 
                ms.service="active-directory" 
                ms.workload="identity" 
                ms.tgt_pltfrm="na" 
                ms.devlang="na" 
                ms.topic="article" 
                ms.date="08/10/2015" 
                ms.author="markusvi"/>


# 了解 Azure 中的資源存取


> [AZURE.NOTE]本主題說明有關使用訂用帳戶管理員來控制整個 Azure 入口網站中資源存取的概念。此外，Azure 預覽入口網站提供[以角色為基礎的存取控制](role-based-access-control-configure.md)，因此可以更精確地管理 Azure 資源。

在 2013 年 10 月，Azure 管理入口網站和服務管理 API 已與 Azure Active Directory 進行整合，為改善使用者經驗及管理 Azure 資源存取奠定基礎。Azure Active Directory 已提供絕佳功能，例如使用者管理、內部部署目錄同步、Multi-Factor Authentication 和應用程式存取控制。當然，這些也應該能夠用於全面管理 Azure 資源。

在 Azure 中從計費觀點著手的存取控制。Azure 帳戶的擁有者 (藉由造訪 [Azure 帳戶中心](https://account.windowsazure.com/subscriptions)來存取) 為帳戶管理員 (AA)。訂用帳戶是計費容器，但它們也可做為安全性界限：每個訂用帳戶都有一個服務管理員 (SA)，他可以使用 [Azure 管理入口網站](https://manage.windowsazure.com/)來新增、移除和修改該訂用帳戶中的 Azure 資源。新訂用帳戶的預設 SA 為 AA，但 AA 可以在 Azure 帳戶中心變更 SA。
 
<br><br>![Azure 帳戶][1]

訂用帳戶也會與目錄產生關聯。目錄會定義一組使用者。他們可以是公司或學校中建立目錄的使用者，也可以是外部使用者 (也就是 Microsoft 帳戶)。訂用帳戶可供已被指派為服務管理員 (SA) 或共同管理員 (CA) 的某些目錄使用者存取；唯一的例外就是，由於舊版原因，Microsoft 帳戶 (先前稱為 Windows Live ID) 不需存在目錄中，即可被指派為 SA 或 CA。

<br><br>![Azure 中的存取控制][2]

 
Azure 管理入口網站內的功能可讓使用 Microsoft 帳戶登入的 SA，使用 [設定] 中 [訂用帳戶] 頁面上的 [編輯目錄] 命令來變更與訂用帳戶相關聯的目錄。請注意，此作業會影響該訂用帳戶的存取控制。



> [AZURE.NOTE]Azure 管理入口網站中的 [編輯目錄] 命令不適用於使用公司或學校帳戶登入的使用者，因為這些帳戶只可以登入其所屬的目錄。

<br><br>![簡單的使用者登入流程][3]

簡而言之，組織 (例如 Contoso) 將會強制執行同一組訂用帳戶的計費和存取控制。也就是說，此目錄會與單一 Azure 帳戶所擁有的訂用帳戶相關聯。成功登入 Azure 管理入口網站後，使用者可看到兩個資源集合 (在上圖中以橘色表示)：


- 其使用者帳戶所在的目錄 (來自或加入為外部主體)。請注意，用於登入的目錄與此計算無關，所以不論您在哪裡登入，一律會顯示您的目錄。

- 屬於訂用帳戶的資源，而這些訂用帳戶會與用來登入且使用者可以存取的目錄 (他們是 SA 或 CA) 相關聯。


<br><br>![具有多個訂閱和目錄的使用者][4]


具有跨多個目錄之訂用帳戶的使用者能夠使用訂用帳戶篩選條件，切換 Azure 管理入口網站的目前內容。實際上，這會導致個別登入到不同的目錄，但卻是使用單一登入 (SSO) 順利完成的。

在訂用帳戶之間移動資源等作業，可能會因為訂用帳戶的這個單一目錄檢視而變得更加困難。若要執行資源移轉，可能必須先使用 [設定] 中 [訂用帳戶] 頁面上的 [編輯目錄] 命令，讓訂用帳戶與相同的目錄產生關聯。



<!--Image references-->
[1]: ./media/active-directory-understanding-resource-access/IC707931.png
[2]: ./media/active-directory-understanding-resource-access/IC707932.png
[3]: ./media/active-directory-understanding-resource-access/IC707933.png
[4]: ./media/active-directory-understanding-resource-access/IC707934.png

<!---HONumber=August15_HO8-->