<properties
	pageTitle="在應用程式 Proxy 中使用宣告感知應用程式"
	description="涵蓋如何使用 Azure AD 應用程式 Proxy 啟動並執行。"
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



# 在應用程式 Proxy 中使用宣告感知應用程式


宣告感知應用程式會執行前往 STS 的重新導向，而 STS 會接著向使用者要求認證以交換權杖，之後才會將使用者重新導向至應用程式。為了要讓應用程式 Proxy 能夠使用這些重新導向，您必須採取下列步驟才能讓應用程式 Proxy 使用宣告感知應用程式。

> [AZURE.IMPORTANT]應用程式 Proxy 是您升級至 Premium 或 Basic 版本的 Azure Active Directory 時才能使用的功能。如需詳細資訊，請參閱 [Azure Active Directory 版本](active-directory-editions.md)。


## 必要條件

執行此程序之前，請確定您可以從內部部署網路外部存取宣告感知應用程式要重新導向的目標 STS。

### Azure 入口網站組態

1. 根據[使用應用程式 Proxy 發佈應用程式](active-directory-application-proxy-publish.md)中的所述指示來發佈您的應用程式。
2. 在應用程式清單中選取宣告感知應用程式，並按一下 [設定]。
3. 如果您選擇 [通過] 做為您的 [預先驗證方法]，則請務必選取 [HTTPS] 做為您的 [外部 URL] 配置。
4. 如果您選擇 [Azure Active Directory] 做為您的 [預先驗證方法]，則請務必選取 [無] 做為您的 [內部驗證方法]。

### ADFS 組態

1. 開啟 [ADFS 管理]。
2. 移至 [信賴憑證者信任]，並以滑鼠右鍵按一下您要使用應用程式 Proxy 發佈的應用程式，然後選擇 [屬性]。

![信賴憑證者信任 - 以滑鼠右鍵按一下應用程式名稱 - 螢幕擷取畫面][1]

3. 在 [端點] 索引標籤的 [端點類型] 下，選取 [WS-同盟]。
4. 在 [受信任的 URL] 下，輸入您在應用程式 Proxy 的 [外部 URL] 下所輸入的 URL，然後按一下 [確定]。

![新增端點 - 設定 [信任的 URL] 值 - 螢幕擷取畫面][2]

<!--image references-->
[1]: ./media/active-directory-application-proxy-claims-aware-apps/AppDropdown.jpg
[2]: ./media/active-directory-application-proxy-claims-aware-apps/AddEndpoint.jpg

<!---HONumber=August15_HO9-->