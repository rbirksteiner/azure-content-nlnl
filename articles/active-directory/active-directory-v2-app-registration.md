<properties
	pageTitle="應用程式模型 v2.0 | Microsoft Azure"
	description="如何向 Microsoft 註冊應用程式，以便啟用登入及整合應用程式與應用程式模型 v2.0。"
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/12/2015"
	ms.author="dastrock"/>

# 應用程式模型 v2.0 預覽版本：如何向 Microsoft 註冊應用程式

若要建置同時接受 MSA 與 Azure AD 登入的應用程式，您必須先向 Microsoft 註冊應用程式。您將無法使用任何現有的應用程式搭配 Azure AD 或 MSA - 立刻建立一個全新的應用程式。

> [AZURE.NOTE]此資訊適用於 v2.0 應用程式模型公開預覽版本。如需如何整合公開上市 Azure AD 服務的相關指示，請參閱 [Azure Active Directory 開發人員指南](active-directory-developers-guide.md)。

## 造訪 Microsoft 應用程式註冊入口網站
第一件事就是先瀏覽至 [https://apps.dev.microsoft.com](https://apps.dev.microsoft.com)。這是新的應用程式註冊入口網站，可供您管理有關 Microsoft 應用程式的所有一切。

使用個人或公司或學校的 Microsoft 帳戶進行登入。如果您沒有任何帳戶，請註冊新的個人帳戶。請繼續進行，這不需要很長的時間 - 我們會在此等候。

完成了嗎？ 您現在應該看一下您的 Microsoft 應用程式清單，該清單有可能空白。讓我們改變這點。

<!-- TODO: Verify strings here -->
按一下 [新增應用程式]，並為它命名。入口網站將會指派全域唯一的應用程式識別碼給您的應用程式，以便稍後在您的程式碼中使用。如果您的應用程式包含的伺服器端元件需要用來呼叫 API (例如：Office、Azure 或協力廠商) 的存取權杖，您也會想在此建立 **應用程式密碼**。<!-- TODO: Link for app secrets -->

接著，加入您的應用程式將使用的平台。 - 若為 Web 應用程式，請提供可傳送登入訊息的 **重新導向 URI**。 - 若為行動應用程式，請複製自動為您建立的預設重新導向 URI。

(選擇性) 您可以在 [設定檔] 區段中自訂登入頁面的外觀及操作方式。繼續之前，請務必按一下 [儲存]。

## 建置快速入門應用程式
您現在已有 Microsoft 應用程式，完成其中一個快速入門教學課程，即可啟動並執行應用程式模型 v2.0。以下是一些建議：

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

<!---HONumber=August15_HO7-->