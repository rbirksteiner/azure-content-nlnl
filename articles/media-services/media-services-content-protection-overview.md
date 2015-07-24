<properties 
	pageTitle="保護內容概觀" 
	description="此文章簡介如何利用 Media Services 保護內容。" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/07/2015" 
	ms.author="juliako"/>

#保護內容概觀


Microsoft Azure 媒體服務可讓您保護媒體從離開電腦到進行儲存、處理和傳遞時的安全。媒體服務可讓您傳遞利用進階加密標準 (AES) (使用 128 位元加密金鑰) 和 PlayReady DRM 動態加密的內容。媒體服務也提供服務，傳遞金鑰和 PlayReady 授權給授權用戶端。

![content-protection][content-protection]

要想使用動態加密，您必須從負責傳送加密內容的資料流端點至少取得一個資料流保留單位。

##概念

###資產加密選項

根據您想要上傳、儲存和傳遞的內容類型，媒體服務會提供各種加密選項供您選擇。

**無** - 不使用加密。這是預設值。請注意，使用這個選項時您的內容在傳輸中或在儲存體中不受保護。

如果您計劃使用漸進式下載傳遞 MP4，請使用此選項來上傳內容。

**StorageEncrypted** - 使用此選項，使用 AES 256 位元加密對您的純文字內容進行本機加密，然後將它上傳到已靜止加密儲存的 Azure 儲存體。使用儲存體加密保護的資產會在編碼之前自動解除加密並放在加密的檔案系統中，並且選擇性地在上傳為新的輸出資產之前重新加密。儲存體加密的主要使用案例是當您想要使用強式加密保護靜止在磁碟上的高品質輸入媒體檔案時。

若要傳遞儲存體加密資產，您必須設定資產的傳遞原則，讓媒體服務知道您的內容傳遞方式。串流處理資產之前，串流伺服器會移除儲存體加密，並使用指定的傳遞原則來串流處理您的內容 (例如，AES、PlayReady 或不加密)。

**CommonEncryptionProtected** - 如果您想要使用一般加密或 PlayReady DRM (例如，受到 PlayReady DRM 保護的 Smooth Streaming) 來加密 (或上傳已加密) 內容，請使用此選項。

**EnvelopeEncryptionProtected** - 如果您想要保護利用進階加密標準 (AES) 所加密的 HTTP 即時串流 (HLS) 或上傳利用相同標準所加密的已保護 HTTP 即時串流 (HLS)，請使用此選項。請注意，如果您正在上傳已利用 AES 所加密的 HLS，則必須已由 Transform Manager 進行加密。



###動態加密

Microsoft Azure 媒體服務可讓您傳遞利用進階加密標準 (AES) (使用 128 位元加密金鑰) 和 PlayReady DRM 所動態加密的內容。

目前，您可以加密下列串流格式：HLS、MPEG DASH 和 Smooth Streaming。無法加密 HDS 串流格式，或漸進式下載。

如果您想要媒體服務加密資產，則需要建立加密金鑰 (CommonEncryption 或 EnvelopeEncryption) 與資產的關聯，同時設定金鑰的授權原則。

您也需要設定資產的傳遞原則。如果您想要串流處理儲存體加密的資產，請一定要透過設定資產傳遞原則來指定資產傳遞方式。

播放程式要求串流時，媒體服務便會使用 AES 或 PlayReady 加密，使用指定的金鑰動態加密您的內容。為了將串流解密，播放程式將從金鑰傳遞服務要求金鑰。為了決定使用者是否有權取得金鑰，服務會評估為金鑰指定的授權原則。

>[AZURE.NOTE]若要利用動態加密，您必須先為想要從該處傳遞加密內容的串流端點取得至少一個隨選串流單元。如需詳細資訊，請參閱[如何調整媒體服務](media-services-manage-origins.md#scale_streaming_endpoints)。

###PlayReady DRM 授權和 AES 純文字金鑰傳遞服務

媒體服務提供一種服務，將 PlayReady 授權和 AES 純文字金鑰傳遞給授權用戶端。若要設定您授權和金鑰的授權和驗證原則，才能使用 Azure 管理入口網站、REST API 或 Media Services SDK for .NET。

請注意，如果您是使用入口網站，則可以設定一個 AES 原則 (將會套用到所有 AES 加密內容) 以及一個 PlayReady 原則 (將會套用到所有 PlayReady 加密內容)。如果您想要進一步控制組態，請使用 Media Services SDK for .NET。

###PlayReady 授權範本

媒體服務提供一種服務，來傳遞 PlayReady 授權。使用者播放程式 (例如 Silverlight) 嘗試播放 PlayReady 保護內容時，會將要求傳送到授權傳遞服務來取得授權。如果授權服務核准要求，就會發出傳送給用戶端並可用來解密和播放所指定內容的授權。

授權包含您要 PlayReady DRM 執行階段在使用者嘗試播放受保護內容時強制執行的權限和限制。媒體服務提供可讓您設定 PlayReady 授權的 API。如需詳細資訊，請參閱[媒體服務 PlayReady 授權範本概觀](https://msdn.microsoft.com/library/azure/dn783459.aspx)。

###權杖限制

內容金鑰授權原則可能會有一個或多個授權限制：Open、權杖限制或 IP 限制。token 限制原則必須伴隨著安全權杖服務 (STS) 所發出的權杖。媒體服務支援簡單 Web 權杖 (SWT) 格式和 JSON Web 權杖 (JWT) 格式的權杖。媒體服務不提供安全權杖服務。您可以建立自訂 STS，或利用 Microsoft Azure ACS 來發行權杖。STS 必須設定為建立使用指定的索引鍵和問題宣告您在權杖限制組態中指定簽署的權杖。如果權杖有效，且權杖中的宣告符合針對金鑰 (或授權) 所設定的宣告，媒體服務金鑰傳遞服務會將所要求的金鑰 (或授權) 傳回給用戶端。設定 token 限制原則時，您必須指定主要驗證金鑰、簽發者和對象參數。主要驗證金鑰包含簽署權杖使用的金鑰，簽發者是發行權杖的安全權杖服務。對象 (有時稱為範圍) 描述權杖或權杖獲授權存取之資源的用途。媒體服務金鑰傳遞服務會驗證權杖中的這些值符合在範本中的值。

##常見案例

###保護儲存體中的內容、提供動態加密串流處理媒體、使用 AMS 金鑰\\授權傳遞服務  

1. 將高品質夾層檔內嵌到資產。將儲存體加密選項套用到資產。
2. 設定串流端點。
1. 編碼為調適性位元速率 MP4 集。將儲存體加密選項套用到輸出資產。
1. 針對您想要在播放期間動態加密的資產，建立加密內容金鑰。
2. 設定內容金鑰授權原則。
1. 設定資產傳遞原則 (供動態封裝和動態加密使用)。
1. 透過建立 OnDemand 定位器，來發佈資產。
1. 串流發佈的內容。

###將媒體服務金鑰和授權傳遞服務運用到您自己的加密與串流處理服務

如需詳細資訊，請參閱[如何整合 Azure PlayReady 授權服務與您自己的加密程式/串流伺服器](http://mingfeiy.com/integrate-azure-playready-license-service-encryptorstreaming-server)。

###與夥伴整合

[使用 castLabs 將 DRM 授權傳遞到 Azure 媒體服務](media-services-castlabs-integration.md)


##一般內容加密工作

>[AZURE.NOTE]如果您的內容是儲存體加密，請務必設定資產傳遞原則。

###設定串流端點

如需串流端點的概觀以及如何管理它們的詳細資訊，請參閱 [如何在媒體服務帳戶中管理串流端點](media-services-manage-origins.md)。

###建立內容金鑰

建立內容金鑰，以用來使用 **.NET** 或 **REST API** 加密資產。

[AZURE.INCLUDE [media-services-selector-create-contentkey](../../includes/media-services-selector-create-contentkey.md)]

###設定內容金鑰授權原則 

使用 **.NET** 或 **REST API** 設定內容保護和金鑰授權原則。

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]

###設定資產傳遞原則

使用 **.NET** 或 **REST API** 設定資產傳遞原則。

[AZURE.INCLUDE [媒體-服務-選取器-資產-傳遞-原則](../../includes/media-services-selector-asset-delivery-policy.md)]


##相關連結

[利用 Azure 媒體服務宣告 PlayReady 是一個服務和 AES 動態加密](http://mingfeiy.com/playready)

[Azure 媒體服務 PlayReady 授權傳遞定價說明](http://mingfeiy.com/playready-pricing-explained-in-azure-media-services)

[如何偵錯 Azure 媒體服務的 AES 加密串流](http://mingfeiy.com/debug-aes-encrypted-stream-azure-media-services)

[JWT 權杖驗證](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[整合 Azure 媒體服務 OWIN MVC 型應用程式與 Azure Active Directory 並根據 JWT 宣告限制內容金鑰傳遞](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)。

[使用 Azure ACS 發行權杖](http://mingfeiy.com/acs-with-key-services)。

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
 

<!---HONumber=July15_HO2-->