<properties
   pageTitle="內容傳遞網路 (CDN) 指引 | Microsoft Azure"
   description="內容傳遞網路 (CDN) 的指引，目的是傳遞 Azure 中裝載的高頻寬內容。"
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/28/2015"
   ms.author="masashin"/>

# 內容傳遞網路 (CDN) 指引

![](media/best-practices-cdn/pnp-logo.png)

## 概觀

Microsoft Azure 內容傳遞網路 (CDN) 為開發人員提供一套傳遞高頻寬內容 (裝載在 Azure 中) 的通用解決方案。CDN 快取會公開從 Azure blob 儲存體載入的可用物件，或位於策略性位置之應用程式資料夾的物件，以提供最大頻寬來傳遞內容給使用者。它通常用來傳遞靜態內容，例如影像、樣式表、文件、檔案、用戶端指令碼和 HTML 頁面。

使用 CDN 的主要優點是較低的延遲及更快將內容傳遞給使用者，而與裝載應用程式的資料中心的地理位置無關，並且可降低應用程式本身的負載，因為它能舒緩存取和傳遞內容所需的處理程序。此降低負載可協助提高應用程式的效能和延展性，以及減少達成特定層級的效能和可用性所需的處理資源，從而將裝載成本降至最低。

如果 Azure CDN 不符合需求，您可以在應用程式中使用非由 Azure 實作的其他內容傳遞網路系統。或者，您可以藉由公開 Azure 儲存體或 Azure 計算執行個體中的靜態內容，為與其他提供者裝載的應用程式使用 Azure CDN。![](media/best-practices-cdn/CDN.png)

## 如何及為何使用 CDN

CDN 的典型用法包括：

- 傳遞用戶端應用程式的靜態資源，通常是來自網站。這些可以是映像、樣式表、文件、檔案、用戶端指令碼、HTML 頁面、HTML 片段，或伺服器不需要為每個要求修改的任何其他內容。應用程式可在執行階段建立項目，並讓 CDN 使用它們 (例如，藉由建立目前頭條新聞清單)，但它不會為每個要求這麼做。

- 將公用靜態和共用內容傳遞給裝置，例如行動電話和平板電腦，其中應用程式本身是一個將 API 提供給用戶端的 Web 服務。除了伺服器不需要為每個要求修改的其他內容外，CDN 可以傳遞靜態資料集供用戶端使用，或許用來產生用戶端 UI。例如，它可用來傳遞 JSON 或 XML 文件。

- 讓用戶端使用僅包含公用靜態內容的整個網站，而不需要任何專用的計算資源。

- 視需要將視訊檔案串流到用戶端。視訊可從提供 CDN 連接的全球資料中心，獲得低延遲和可靠連線的好處。

- 一般可改善使用者體驗，特別是與應用程式的資料中心位置相距遙遠，從而發生較高延遲的使用者。Web 應用程式中內容的總大小大都是靜態的，且使用 CDN 有助於維護效能和整體使用者體驗，同時不需要將應用程式部署到多個資料中心。

- 處理應用程式日益增加的負載，其服務物聯網 (IoT) 的行動和固定裝置。如果需要處理廣播訊息及直接管理韌體更新散發，則這些大量的裝置和設備可能很容易讓應用程式無法因應。

- 處理尖峰和偶發性需求而不需應用程式調整，避免後續增加營運成本。例如，針對硬體裝置 (例如特定型號的路由器) 或消費性裝置 (例如智慧型電視) 將更新發行至作業系統時，如果數以百萬的使用者和裝置在短期間內下載更新，則可能會視需要產生龐大的尖峰量。

- 下表顯示來自不同地理位置的第一個位元組的時間中間值範例。目標 Web 角色會部署到 Azure 美國西部。因為 CDN 很接近 CDN 節點，所以較大的暴增之間有更強的關聯性。如需 Azure CDN 節點位置清單，請參閱 [Azure 內容傳遞網路 (CDN) POP 位置](http://msdn.microsoft.com/library/azure/gg680302.aspx)。

<table xmlns:xlink="http://www.w3.org/1999/xlink"><tr><th><a name="_MailEndCompose" href="#"><span /></a><br /></th><th><p>第一個位元組時間 (原始)</p></th><th><p>第一個位元組時間 (CDN)</p></th><th><p>對 CDN 快上 %</p></th></tr><tr><td><p>* San Jose, CA</p></td><td><p>47.5</p></td><td><p>46.5</p></td><td><p>2 %</p></td></tr><tr><td><p>** Dulles, VA</p></td><td><p>109</p></td><td><p>40.5</p></td><td><p>169 %</p></td></tr><tr><td><p>Buenos Aires, AR</p></td><td><p>210</p></td><td><p>151</p></td><td><p>39 %</p></td></tr><tr><td><p>* London, UK</p></td><td><p>195</p></td><td><p>44</p></td><td><p>343 %</p></td></tr><tr><td><p>Shanghai, CN</p></td><td><p>242</p></td><td><p>206</p></td><td><p>17 %</p></td></tr><tr><td><p>* Singapore</p></td><td><p>214</p></td><td><p>74</p></td><td><p>189%</p></td></tr><tr><td><p>* Tokyo, JP</p></td><td><p>163</p></td><td><p>48</p></td><td><p>240 %</p></td></tr><tr><td><p>Seoul, KR</p></td><td><p>190</p></td><td><p>190</p></td><td><p>0 %</p></td></tr></table>* 在相同的城市中有 Azure CDN 節點。 * 鄰近城市中具有 Azure CDN 節點。


## 挑戰  

計劃使用 CDN 時必須考量有幾項挑戰：

- **部署** 您必須決定從何處載入 CDN 內容 (CDN 將從中提取內容的原點)，及是否需要將內容部署到多個儲存體系統中 (例如在 CDN 和替代位置中)。

- 您的應用程式部署機制必須考慮部署靜態內容和資源，以及部署應用程式檔案，例如 ASPX 頁面。比方說，可能需要個別的步驟，才能將內容載入 Azure blob 儲存體。

- **版本控制和快取控制** 您必須考慮如何更新靜態內容和部署新版本。CDN 目前不提供清除內容機制，如此才有可用的新版本。這類似於管理用戶端快取的挑戰，如在網頁瀏覽器中。

- **測試** 在本機或在預備環境中開發和測試應用程式時，很難為 CDN 設定執行本機測試。

- **SEO** 當您使用如影像和文件等內容時，它們皆由不同的網域服務，此 CDN 內容將會影響 SEO。

- **安全性** 許多 CDN 服務 (例如 Azure CDN) 目前不提供任何類型的內容存取控制。

- **恢復功能** CDN 是應用程式的潛在單一失敗點。它比 blob 儲存體具有更低的可用性 SLA (可用於直接傳遞內容)，因此您可能需要考慮為重要內容實作後援機制。

- 用戶端可從不允許存取 CDN 上資源的環境中連接。這可能是安全性限制的環境，限制只能存取一組已知來源，或防止從原始頁面以外的任何位置載入資源的環境。因此，將必須回溯實作。

- 您應該實作一種機制來監視您透過 CDN 的內容可用性。

CDN 較沒有用的案例括：

- 當內容具有低點擊率，因此在存留時間的有效期間內可能偶爾存取，或只存取一次。第一次下載項目時會產生兩筆交易費用 (從原點到 CDN，然後從 CDN 到客戶)。

- 當資料是私人的，例如適用於大型企業或供應鏈生態系統。


## 一般方針和最佳做法

使用 CDN 是最小化應用程式負載，及最大化可用性與效能的好方法。您應該為應用程式使用的所有適當內容和資源考量此方案。設計使用 CDN 的策略時，請考慮下列幾點：

- **原點 ** 透過 CDN 部署內容時，只需要指定 CDN 服務將用來存取及快取內容的 HTTP (連接埠 80) 端點。+ 端點可指定 Azure blob 儲存體容器，包含您要透過 CDN 傳遞的靜態內容。容器必須標示為公用。只能透過 CDN 取得公用容器中具有公用讀取權限的 blob。
- 端點可在其中一個應用程式之計算層級的根目錄中，指定名為 **cdn** 的資料夾 (例如 Web 角色或虛擬機器)。將在 CDN 上快取資源要求的結果，包括動態資源，例如 ASPX 頁面。最小快取性期間為 300 秒。任何較短的期間會防止內容部署到 CDN (如需詳細資訊，請參閱＜<a href="#cachecontrol" xmlns:dt="uuid:C2F41010-65B3-11d1-A29F-00AA00C14882" xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:MSHelp="http://msdn.microsoft.com/mshelp">快取控制</a>＞一節)。

- 如果您使用 Azure 網站，可在建立 CDN 執行個體時選取網站，將端點設為該網站的根資料夾。可透過 CDN 取得網站的所有內容。

- 在大部分情況下，指向應用程式的其中一個計算層級內的資料夾中 CDN 端點，將提供更多的彈性和控制能力。比方說，它可讓您更輕鬆地管理目前和未來的路由需求，以及動態產生靜態內容，例如影像縮圖。

- 從 ASPX 頁面之類的動態來源傳遞內容時，您可以使用查詢字串來區分快取中的物件。不過，可在指定 CDN 端點時，藉由管理入口網站中的設定來停用這種行為。從 blob 儲存體傳遞內容時，查詢字串會被視為字串常值，因此會將具有相同名稱但有不同查詢字串的兩個項目，儲存為 CDN 上的個別項目。

- 您可以利用 URL 重寫指令碼等資源和其他內容，避免將檔案移至 CDN 原始資料夾。

- 使用 Azure 儲存體 blob 以保存 CDN 內容時，blob 中資源的 URL 對容器和 blob 名稱是區分大小寫的。

- 使用 Azure 網站時，您可以在資源的連結中指定 CDN 執行個體的路徑。例如，下列動作會在將透過 CDN 傳遞之網站的 **Images** 資料夾中，指定影像檔：

  ```
  <img src="http://[your-cdn-instance].vo.msecnd.net/Images/image.jpg" />
  ```

- **部署** 如果您未在應用程式部署封裝或程序中包含靜態內容，該靜態內容可能需要與應用程式分開佈建和部署。請思考這會如何影響版本控制方法，也就是您用於管理應用程式元件和靜態資源內容的方法。

- 請考慮如何針對指令碼與 CSS 檔案處理統合 (將數個檔案結合成一個檔案) 和縮製 (移除不必要的字元，例如空格、新行字元、註解和其他字元)。這些常用的技術可減少用戶端的載入時間，並相容於透過 CDN 傳遞的內容。如需詳細資訊，請參閱[統合和縮製](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification) (英文)。

- 如果您需要將內容部署到其他位置，這會是部署程序中的額外步驟。如果應用程式更新 CDN 的內容 (或許是固定間隔或為了回應事件)，它必須將更新的內容儲存於任何其他位置，以及 CDN 的端點。

- 您無法為 Azure 接移中部署的應用程式，或在本機 Azure 模擬器或 Visual Studio 中，設定 CDN 端點。這會影響單位測試、功能性測試，以及最終預先部署測試。您必須實作替代機制來允許此作法。例如，您可以將內容預先部署到 CDN，方法是使用暫存的自訂應用程式或公用程式，並在其快取期間執行測試。或者，使用編譯指示詞或全域常數，控制應用程式載入資源的位置。例如，在偵錯模式中執行時，可能會從本機資料夾載入資源，例如用戶端指令碼組合和其他內容，並在發行模式中執行時使用 CDN。

- CDN 不支援任何原生壓縮功能。不過，它會傳遞已壓縮的內容，例如 zip 和 gzip 檔案。將應用程式資料夾做為 CDN 端點時，伺服器預設可能會壓縮某些內容，並且使用將內容直接傳遞到網頁瀏覽器或其他類型的用戶端時的相同方法。這必須使用用戶端所傳送的 **Accept-Encoding** 值。在 Azure 中，當 CPU 使用率低於 50% 時預設會自動壓縮內容。變更設定，而且可能需要使用啟動工作，在 IIS 中開啟動態輸出壓縮 (如果您使用雲端服務來裝載應用程式)。如需詳細資訊，請參閱[透過 Web 角色，使用 Azure CDN 以啟用 gzip 壓縮](http://blogs.msdn.com/b/avkashchauhan/archive/2012/03/05/enableing-gzip-compression-with-windows-azure-cdn-through-web-role.aspx) (英文)。

- **路由與版本控制** 您可能需要使用不同的 CDN 執行個體；例如，當您部署新版的應用程式時，可能會想要使用不同的 CDN。如果您將 Azure blob 儲存體做為內容的原點，可以只建立個別的儲存體帳戶或個別的容器，並將 CDN 端點指向它。如果您將應用程式中的 **cdn** 根資料夾做為 CDN 端點，您可以使用 URL 重寫技術，將要求導向不同的資料夾。

- 請勿使用查詢字串來表示 CDN 上資源連結中不同版本的應用程式，因為從 Azure blob 儲存體提取內容時，查詢字串為資源名稱 (blob 名稱) 的一部分。它也會影響用戶端快取資源的方式。

- 若是在 CDN 上快取先前的資源，則在部署新版本的靜態內容時，更新應用程式可能是一項挑戰。如需詳細資訊，請參閱＜<a href="#cachecontrol" xmlns:dt="uuid:C2F41010-65B3-11d1-A29F-00AA00C14882" xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:MSHelp="http://msdn.microsoft.com/mshelp">快取控制</a>＞一節。

<a name="cachecontrol" href="#" xmlns:xlink="http://www.w3.org/1999/xlink"><span /></a>**快取控制**+ 考慮如何管理快取，以及在哪些應用程式層級。比方說，將資料夾做為 CDN 原點時，您可以指定產生內容的頁面快取性，及特定資料夾中所有資源的內容到期。您也可以指定 CDN 的快取屬性，及使用標準 HTTP 標頭的用戶端。雖然您應該已在伺服器和用戶端上管理快取，但使用 CDN 能讓您更了解內容的快取方式及其位置。

- 若要防止在 CDN 上使用物件，您可以從原點刪除它們 (blob 容器或應用程式 **cdn** 根資料夾)、移除或刪除 CDN 端點，或在 blob 儲存體情況下，讓容器或 blob 變成私用。不過，只有當項目的存留時間 (TTL) 到期時，才會從 CDN 移除它們。

- 若未指定快取到期期限 (例如從 blob 儲存體載入內容時)，它會在 CDN 上快取長達 72 小時。

- 在 Web 應用程式中，您可以在 web.config 檔案的 **system.webServer/staticContent** 區段中，使用 **clientCache** 項目來設定所有內容的快取和到期。它可在任何資料夾中放置 web.config 檔案，以便影響該資料夾中的檔案和所有子資料夾中的檔案。

- 如果您使用動態技巧 (例如 ASP.NET) 來建立 CDN 的內容，請確定在每個頁面上指定 **Cache.SetExpires** 屬性。CDN 不會從使用預設快取性設定 (公用) 的頁面中快取輸出。將快取到期期限設為適當值，確保不會在極短時間內從應用程式捨棄和重新載入內容。

- **安全性** CDN 可以使用 CDN 所提供的憑證，透過 HTTPS (SSL) 傳遞內容，但它也可以透過 HTTP 取得。您無法封鎖 HTTP 存取 CDN 中的項目。您可能需要使用 HTTPS，要求透過 HTTPS 載入的頁面所顯示的靜態內容 (例如購物車)，以避免瀏覽器發出混合內容警告。

- 許多 CDN 服務 (例如 Azure CDN) 目前不提供任何存取控制設備來安全地存取內容。您無法搭配使用共用存取簽章 (SAS) 與 CDN。

- 如果您使用 CDN 傳遞用戶端指令碼，則當這些指令碼使用 **XMLHttpRequest** 呼叫，針對資料、影像或字型等其他資源，或不同網域中的字型提出 HTTP 要求，您可能會遇到問題。許多網頁瀏覽器會防止跨原始來源資源分享 (CORS)，除非網頁伺服器已設定適當的回應標頭。如需 CORS 的詳細資訊，請參閱《<span class="highlight" xmlns:dt="uuid:C2F41010-65B3-11d1-A29F-00AA00C14882" xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:MSHelp="http://msdn.microsoft.com/mshelp">API 安全性考量</span>》(英文) 指南的＜威脅消弭＞一節。您可以設定 CDN 以支援 CORS：+ 如果傳遞內容的來源是 Azure blob 儲存體，您可以將 **CorsRule** 新增至服務屬性。此規則可指定 CORS 要求的允許原點、GET 之類的允許方法，以及以秒為單位的規則最長使用期限 (在載入原始內容之後，用戶端必須要求連結資源之期間)。如需詳細資訊，請參閱[跨原始資源共用 (CORS) 支援 Azure 儲存體服務](http://msdn.microsoft.com/library/azure/dn535601.aspx)。

- 如果傳遞內容的原點是應用程式中的資料夾，例如 **cdn** 根資料夾，您可以在應用程式組態檔中設定輸出規則。以便在所有回應上設定 **Access-Control-Allow-Origin** 標頭。如需有關使用重寫規則的詳細資訊，請參閱 [URL 重寫模組](http://www.iis.net/learn/extensions/url-rewrite-module) (英文)。請注意，使用 Azure 網站時無法使用這項技術。

- **自訂網域**+ 大部分 CDN (包括 Azure CDN) 都能讓您指定自訂網域名稱，並使用它透過 CDN 存取資源。您也可以在 DNS 中使用 **CNAME** 記錄，以設定自訂的子網域名稱。使用這種方法可以提供額外的抽象和控制層級。

- 如果您使用 **CNAME**，您無法 (在本指南編寫時) 另外使用 SSL，因為 CDN 會使用它自己的單一 SSL 憑證，而這不符合您的自訂網域/子網域名稱。

- **CDN 後援** 您應該考量應用程式如何處理 CDN 失敗或暫時無法使用。如果無法使用 CDN，用戶端應用程式可使用前一個要求期間的本機快取資源複本 (位於用戶端)，或者也可以使用程式碼來偵測失敗，而非從原點要求資源 (應用程式資料夾或佔用資源的 Azure blob 容器)。

- **SEO** 如果 SEO 是應用程式中重要的考量因素，請確定：+ 在每個網頁或資源中包括 **Rel** 標準標頭。

- 使用 **CNAME** 子網域記錄，並使用此名稱存取資源。

- 請考慮 CDN 的 IP 位址所在的國家或區域，可能與應用程式本身所在的國家或區域不同所造成的影響。

- 使用 Azure blob 儲存體做為原點時，針對 CDN 上的資源，維護與應用程式資料夾中相同的檔案結構。


- **監視和記錄** 加入 CDN 做為應用程式監視策略的一部分，以偵測並測量失敗或發生的過度延遲現象。

- 啟用 CDN 的記錄功能，並加入它做為您日常作業的一部分。

- **成本併入** 當 CDN 從您的應用程式載入資料時，您需要支付 CDN 和儲存體交易的輸出資料傳輸費用。您應該為內容設定可行的快取到期期限以確保最新狀態，但不要短到將內容從應用程式或 blob 儲存體，重複地重新載入到 CDN。不過，過長的到期期限會更難從 CDN 移除項目，因為您必須等候其到期。

- 很少下載的項目會產生兩筆交易費用，而不會大幅降低伺服器負載。



## 程式碼範例
本節包含一些程式碼範例與使用 CDN 的技術。


## URL 重寫
下列雲端服務託管應用程式之根目錄中的檔案 (Web.config 檔案除外) 示範在使用 CDN 時，如何執行 URL 重寫。從 CDN 要求內容時，會根據資源類型 (例如指令碼和影像)，將快取重新導向到應用程式根目錄中的特定資料夾。

```XML
<system.webServer>
  ...
  <rewrite>
    <rules>
      <rule name="VersionedResource" stopProcessing="false">
        <match url="(.*)_v(.*).(.*)" ignoreCase="true" />
        <action type="Rewrite" url="{R:1}.{R:3}" appendQueryString="true" />
      </rule>
      <rule name="CdnImages" stopProcessing="true">
        <match url="cdn/Images/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/Images/{R:1}" appendQueryString="true" />
      </rule>
      <rule name="CdnContent" stopProcessing="true">
        <match url="cdn/Content/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/Content/{R:1}" appendQueryString="true" />
      </rule>
      <rule name="CdnScript" stopProcessing="true">
        <match url="cdn/Scripts/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/Scripts/{R:1}" appendQueryString="true" />
      </rule>
      <rule name="CdnScriptBundles" stopProcessing="true">
        <match url="cdn/bundles/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/bundles/{R:1}" appendQueryString="true" />
      </rule>
    </rules>
  </rewrite>
  ...
</system.webServer>
```

加入重寫規則會執行下列重新導向：

- 第一個規則可讓您在資源的檔案名稱中內嵌版本，接著會略過。例如，**Filename_v123.jpg **重寫為 **Filename.jpg**。
- 接下來四個規則將顯示當您不想將資源儲存在 Web 角色的根目錄中名為 **cdn** 的資料夾時，如何重新導向要求。規則會將 **cdn/Images**、**cdn/Content**、**cdn/Scripts** 和 **cdn/bundles** URL 對應到他們在 web 角色中各自的根資料夾。使用 URL 重寫需要對資源的統合進行一些變更。


## 統合和縮製 ##

可由 ASP.NET 處理統合和縮製。在 MVC 專案中，您可以定義您在 **BundleConfig.cs** 中的套組。藉由呼叫 **Script.Render** 方法 (通常是在程式碼的 view 類別中)，可建立縮製指令碼套組的參考。此參考包括內含雜湊的查詢字串 (根據套組的內容)。如果套組內容變更時，產生的雜湊也會變更。根據預設，Azure CDN 執行個體已停用 [**查詢字串狀態**] 設定。為了讓 CDN 正確處理更新的指令碼套組，您必須為 CDN 執行個體啟用 [**查詢字串狀態**] 設定。請注意，建立 CDN 及設定變更生效前，可能會耗費一個小時以上。


## 詳細資訊
+ [Azure CDN](http://azure.microsoft.com/services/cdn/)
+ [Azure 內容傳遞網路 (CDN) 概觀](http://msdn.microsoft.com/library/azure/ff919703.aspx)
+ [在 Web 應用程式中從 Azure CDN 提供內容](cdn-serve-content-from-cdn-in-your-web-application.md)
+ [整合雲端服務與 Azure CDN](cdn-cloud-service-with-cdn.md)
+ [Azure 內容傳遞網路的最佳作法](http://azure.microsoft.com/blog/2011/03/18/best-practices-for-the-windows-azure-content-delivery-network/) (英文)

<!---HONumber=62-->