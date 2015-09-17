<properties
	pageTitle="使用 Azure AD 應用程式 Proxy 發佈應用程式"
	description="涵蓋如何使用 Azure AD 應用程式 Proxy 發佈內部部署應用程式。"
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="terrylan"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/02/2015"
	ms.author="rkarlin"/>


# 使用 Azure AD 應用程式 Proxy 發佈應用程式

> [AZURE.NOTE]應用程式 Proxy 是您升級至 Premium 或 Basic 版本的 Azure Active Directory 時才能使用的功能。如需詳細資訊，請參閱 [Azure Active Directory 版本](https://msdn.microsoft.com/library/azure/dn532272.aspx)。

啟用 Microsoft Azure AD 應用程式 Proxy 之後，您就可以發佈應用程式，讓您私人網路外部的使用者可以存取。

本節會引導您在您要從網路外部啟用安全遠端存取的區域網路上，發佈所執行的應用程式所需的步驟。

> [AZURE.NOTE]若要確認連接器正常執行，您發佈的第一個應用程式應該是可從您的私人網路內部存取的任何網站，以確保使用者可以從網際網路存取該應用程式，然後再發佈實際的應用程式。

## 使用精靈發佈應用程式

1. 開啟您選擇的瀏覽器並瀏覽至 Azure 管理入口網站。
2. 在 Azure 管理入口網站的左窗格中，按一下 [Active Directory] 索引標籤。
3. 按一下您啟用應用程式 Proxy 所在的目錄，以及您要發佈應用程式的目錄 (例如，Wingtip Toys)。
4. 按一下 [應用程式] 索引標籤，然後按一下畫面底部的 [新增] 按鈕 ![新增應用程式](http://i.imgur.com/wEeZ7IR.png)
5. 在 [您想要執行什麼動作?] 對話方塊中，按一下 [發佈將可以在您的網路外部存取的應用程式]。![將可從您的網路外部存取的新應用程式](http://i.imgur.com/Wj3vkyD.png)

6. 依照畫面上指示，提供下列有關您的應用程式的資訊：![應用程式屬性](http://i.imgur.com/MkPNc93.png)

**設定** | **詳細資料**
---|---
外部 URL | 這是用來從私人網路外部存取應用程式的雲端服務的 URL。URL 會根據您所提供的名稱與後置詞 msappproxy.net 自動產生。
預先驗證方法 | <p>設定您希望應用程式使用的預先驗證方法的類型：</p><p>a.Microsoft Azure Active Directory (Microsoft Azure AD) – 將預先驗證方法設定為 Microsoft Azure Active Directory (Microsoft Azure AD)。當使用者嘗試存取應用程式時，應用程式 Proxy 會將使用者重新導向，以便使用將會驗證使用者的 Azure AD 登入，以確保使用者具有目錄和應用程式所需的權限。</p><p>b.通過 – 不執行預先驗證。</p>
外部 URL 通訊協定 | <p>根據預設，應用程式會使用 HTTPS 通訊協定發佈。</p> <p>若要為內部應用程式啟用 HTTP，您必須將 [預先驗證] 設定為 [通過]，然後您就可以將外部 URL 通訊協定從 HTTPS 變更為 HTTP。</p> <p>請注意，使用 HTTP 發佈應用程式可能會產生您的應用程式和使用者的安全性問題。</p> <p>您可以插入自訂網域，而不是使用 msappproxy.net 的預設值。如需詳細資訊，請參閱下方。</p>
內部 URL | 這是應用程式 Proxy 連接器在內部存取應用程式所使用的內部 URL。這應該是從您私人網路內部存取應用程式所使用的已發佈應用程式的 URL。這是有效的 URL，不含路徑，也不含空格或符號。

若要完成精靈，請按一下畫面底部的核取記號。應用程式現在已在 Azure AD 中定義。



## 將使用者和群組指派給應用程式

1. 若是已預先驗證的應用程式，您必須指派將可存取應用程式的使用者和群組。<p>若是通過的應用程式，則所有使用者都可以存取。不過，若要讓使用者查看其應用程式清單中的應用程式，您必需將應用程式指派給該使用者。
2. 完成 [新增應用程式] 精靈之後，[應用程式 Proxy 快速啟動] 頁面隨即顯示。若要指派使用者，按一下 [指派使用者]。![應用程式 Proxy 快速啟動畫面](http://i.imgur.com/OmuWElV.png)
3. 選取您要指派給這個應用程式的每個使用者或群組，然後按一下 [指派]。 

> [AZURE.NOTE]若是整合式 Windows 驗證應用程式，您可以僅指派從內部部署 Active Directory 同步的使用者及群組。您無法為使用 Azure Active Directory 應用程式 Proxy 發佈的應用程式，指派使用 Microsoft 帳戶和來賓登入的使用者。請確定您指派的使用者使用與您要發佈的應用程式屬於相同網域的認證登入。

## 進階組態

1. 您可以修改已發佈的應用程式，或從 [設定] 頁面設定進階選項，例如，單一登入內部部屬應用程式。![進階組態](http://i.imgur.com/FhbRvoq.png)

2. 選取應用程式，然後按一下 [設定]。可以使用下列選項：

**設定** | **詳細資料**
---|---
名稱 | 提供您的應用程式的描述性名稱
外部 URL | 這是用來從私人網路外部存取應用程式的雲端服務的 URL。URL 會根據您所提供的名稱與後置詞 **msappproxy.net** 自動產生。
預先驗證方法 | 設定您希望應用程式使用的預先驗證方法的類型：<p>Active Directory – 將預先驗證方法設定為 Active Directory。當使用者嘗試存取應用程式時，應用程式 Proxy 會將使用者重新導向，以便使用將會驗證使用者的應用程式 Proxy 登入，以確保使用者具有目錄和應用程式所需的權限。</p><p> 通過 – 不執行預先驗證。</p>
外部 URL | 根據預設，應用程式會使用 HTTPS 通訊協定發佈。若要為內部應用程式啟用 HTTP，您必須將 [預先驗證] 設定為 [通過]，然後您就可以將外部 URL 通訊協定從 HTTPS 變更為 HTTP。請注意，使用 HTTP 發佈應用程式可能會產生您的應用程式和使用者的安全性問題。您可以設定自訂網域，而不是使用提供的預設網域 (msappproxy.net)。如需詳細資訊，請參閱「使用自訂網域」。
內部 URL | 這是應用程式 Proxy 連接器在內部存取應用程式所使用的內部 URL。這應該是從您私人網路內部存取應用程式所使用的已發佈應用程式的 URL。這是有效的 URL，不含路徑，也不含空格或符號。
轉譯標頭中的 URL | 針對不需要轉譯 HTTP 主機標頭的應用程式 (例如某些 SharePoint 組態)，將此設定為 [否]。這樣會同時為要求和回應標頭停用標頭轉譯。
內部驗證方法 | 如果您使用應用程式 Proxy 進行預先驗證，您可以設定內部驗證方法，讓使用者可以從單一登入 (SSO) 到此應用程式受益。<p>如果您的應用程式使用 IWA，選取 [整合式 Windows 驗證 (IWA)]，而且您可以設定 Kerberos 限制委派 (KCD) 以啟用此應用程式的 SSO。<p>如果您的應用程式不使用 IWA，選取 [無]。<p>使用 IWA 的應用程式必須使用 KCD 設定，否則應用程式 Proxy 將無法發佈這些應用程式。<p>如需詳細資訊，請參閱「使用 KCD 搭配應用程式 Proxy 的內部部署 IWA 應用程式的單一登入」
內部應用程式 SPN | 這是內部應用程式的服務主體名稱 (SPN)，如內部部屬應用程式 Proxy 中所設定。應用程式 Proxy 連接器會使用 SPN 擷取使用 Kerberos 限制委派 (KCD) 的應用程式的 Kerberos 權杖。<p>如需詳細資訊，請參閱「使用 KCD 搭配應用程式 Proxy 的內部部署 IWA 應用程式的單一登入」

使用 Azure Active Directory 應用程式 Proxy 發佈應用程式之後，這些應用程式會出現在 Azure AD 中的應用程式清單內，您可以在那裡進行管理。如果您在發佈應用程式之後停用應用程式 Proxy 服務，則不會刪除應用程式，但將不再能夠從您的私人網路外部存取。若要檢視應用程式，並確定可以存取該應用程式，按兩下應用程式的名稱。如果已停用應用程式 Proxy 服務，而且無法使用應用程式，則會在畫面頂端出現一個警告訊息。若要刪除某個應用程式，在清單中選取該應用程式，然後按一下 [刪除]。

## 其他資源

* [關於 Kerberos 限制委派](http://technet.microsoft.com/library/cc995228.aspx)

<!---HONumber=August15_HO6-->