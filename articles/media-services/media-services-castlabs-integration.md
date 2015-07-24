<properties 
	pageTitle="使用 castLabs 將 DRM 授權傳遞到 Azure 媒體服務" 
	description="本文說明如何使用 Azure 媒體服務 (AMS) 來傳遞 AMS 使用 PlayReady 與 Widevine DRM 動態加密的資料流。PlayReady 授權來自媒體服務 PlayReady 授權伺服器，Widevine 授權由 castLabs 授權伺服器傳遞。" 
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
	ms.date="06/03/2015" 
	ms.author="juliako"/>


#使用 castLabs 將 DRM 授權傳遞到 Azure 媒體服務

##概觀

本文說明如何使用 Azure 媒體服務 (AMS) 來傳遞 AMS 使用 PlayReady 與 Widevine DRM 動態加密的資料流。PlayReady 授權來自媒體服務 PlayReady 授權伺服器，Widevine 授權則來自 **castLabs** 授權伺服器。

以下為 Azure 媒體服務與 castLabs 整合架構概況圖。

![Scale page](./media/media-services-castlabs-integration/media-services-castlabs-integration.png)

##一般系統設定

- 媒體內容儲存在 AMS 中。
- 內容金鑰的金鑰識別碼儲存在 castLabs 與 AMS 中。
- castLabs 與 AMS 皆有內建權杖驗證。以下幾節將會討論驗證權杖。 
- 用戶端要求串流視訊時，AMS 會使用 **一般加密** (CENC) 動態加密內容，並動態封裝到 Smooth Streaming 與 DASH。我們也會針對 HLS 串流通訊協定傳遞 PlayReady M2TS 基礎資料流加密。
- PlayReady 授權擷取自 AMS 授權伺服器，Widevine 授權擷取自 castLabs 授權伺服器。 
- 媒體播放器會根據平台功能自動決定提取哪些授權。 

##產生驗證權杖以取得授權

castLabs 與 AMS 皆支援使用 JWT (JSON Web Token) 權杖格式進行授權。

###AMS 中的 JWT 權杖 

下表說明 AMS 中的 JWT 權杖。

<table border="1">
<tr><td>Issuer</td><td>所選安全權杖服務 (STS) 中的簽發者字串</td></tr>
<tr><td>Audience</td><td>所使用 STS 中的對象字串</td></tr>
<tr><td>Claims</td><td>一組宣告</td></tr>
<tr><td>NotBefore</td><td>權杖的生效日期</td></tr>
<tr><td>Expires</td><td>權杖的有效期限</td></tr>
<tr><td>SigningCredentials</td><td>PlayReady 授權伺服器、castLabs 授權伺服器與 STS 之間共用的金鑰，可以是對稱或非對稱金鑰。</td></tr>
</table>

###castLabs 中的 JWT 權杖

下表說明 castLabs 中的 JWT 權杖。

<table border="1">
<tr><td>optData</td><td>JSON 字串，其中包含您的相關資訊。</td></tr>
<tr><td>crt</td><td>JSON 字串，其中包含資產、其授權資訊與播放權限的相關資訊。</td></tr>
<tr><td>iat</td><td>Epoch 中目前的日期時間。</td></tr>
<tr><td>jti</td><td>權杖的唯一識別碼 (每個權杖在 castLabs 系統中只使用一次)。</td></tr>
</table>

##範例解決方案設定 

[範例解決方案](https://github.com/AzureMediaServicesSamples/CastlabsIntegration)包含兩個專案：

-	主控台應用程式，用於為 PlayReady 與 Widevine 在已內嵌的資產上設定 DRM 限制。
-	Web 應用程式，用於遞出權杖，可視為「非常簡易」的 STS 版本。


若要使用主控台應用程式：

1.	變更 app.config 以設定 AMS 認證、castLabs 認證、STS 組態及共用的金鑰。
2.	將資產上傳到 AMS。
3.	從已上傳的資產中取得 UUID，然後變更 Program.cs 檔中的行 32：

		 var objIAsset = _context.Assets.Where(x => x.Id == "nb:cid:UUID:dac53a5d-1500-80bd-b864-f1e4b62594cf").FirstOrDefault();

4.	使用 AssetId 命名 castLabs 系統中的資產 (Program.cs 檔中的行 44)。

	您必須設定 **castLabs** 的 AssetId；必須是唯一的英數字元字串。

5.	執行程式。


若要使用 Web 應用程式 (STS)：

1.	變更 web.config 以設定 castlabs 商店識別碼、STS 組態和共用的金鑰。
2.	部署到 Azure 網站。
3.	瀏覽至該網站。

##播放視訊

若要播放使用一般加密 (PlayReady) 技術加密的視訊，您可以使用 [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html)。執行主控台應用程式時，會回應內容金鑰識別碼和資訊清單 URL。

1.	開啟新索引標籤，並啟動 STS：http://[yourStsName].azurewebsites.net/api/token/assetid/[yourCastLabsAssetId]/contentkeyid/[thecontentkeyid]。
2.	移至 [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html)。
3.	貼上資料流 URL。
4.	按一下 [**進階選項**] 核取方塊。
5.	選取在 [**保護**] 下拉式清單中的 PlayReady。
6.	在 [權杖] 文字方塊中貼上您從 STS 取得的權杖。
7.	更新播放程式。
8.	視訊應隨即播放。

如需在 Chrome 中透過 castLabs 播放程式播放用 HTML5 保護的視訊，請連絡 yanmf@microsoft.com 以取得播放程式的存取權。當您存取時，請注意以下 2 點：

1.	castLabs 播放程式必須存取 MPEG-DASH 資訊清單檔案，讓 (format=mpd-time-csf) 附加到您的資訊清單檔案，以取得 MPEG-DASH 資訊清單檔案，而非預設的 Smooth Streaming 資訊清單檔案。

2.	castLab 授權伺服器不需要權杖前有 “Bearer=” 前置詞。因此請先移除該前置詞，再提交權杖。

 

<!---HONumber=July15_HO2-->