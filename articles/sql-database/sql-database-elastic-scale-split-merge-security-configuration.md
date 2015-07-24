<properties 
    title="Elastic Scale Security Configurations" 
    pageTitle="Elastic Scale 安全性設定" 
    description="使用 Elastic Scale for Azure SQL Database 的 Security for Split-Merge 服務" 
    metaKeywords="Elastic Scale Security Configurations, Azure SQL Database sharding, elastic scale " 
    services="sql-database" documentationCenter="" 
    manager="jhubbard" 
    authors="torsteng@microsoft.com"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" ms.topic="article" ms.date="03/20/2015" 
    ms.author="sidneyh" />

# Split-Merge 安全性設定  

若要使用 Split/Merge 服務，您必須正確地設定安全性。此服務是 Microsoft Azure SQL Database 的 Elastic Scale 功能的一部分。如需詳細資訊，請參閱 [Elastic Scale Split 與 Merge 服務教學課程](sql-database-elastic-scale-configure-deploy-split-and-merge.md)

## 設定憑證

憑證有兩種設定方式。 

1. [設定 SSL 憑證][]
2. [設定用戶端憑證][] 

## 取得憑證

您可以從公用憑證授權單位 (CA) 或從 [Windows 憑證服務](http://msdn.microsoft.com/library/windows/desktop/aa376539.aspx) 取得憑證。這些是取得憑證的慣用方法。

如果這些選項都無法使用，您可以產生**自我簽署憑證**。
 
## 產生憑證的工具

* [makecert.exe](http://msdn.microsoft.com/library/bfsktky3.aspx)
* [pvk2pfx.exe](http://msdn.microsoft.com/library/windows/hardware/ff550672.aspx)

### 執行工具

* 從 Visual Studio 的開發人員命令提示字元，請參閱 [Visual Studio 命令提示字元](http://msdn.microsoft.com/library/ms229859.aspx) 

    如果已安裝，請移至：

        %ProgramFiles(x86)%\Windows Kits\x.y\bin\x86 

* 從 [Windows 8.1︰下載套件與工具](http://msdn.microsoft.com/windows/hardware/gg454513#drivers)取得 WDK

##    <a name="to-configure-ssl-cert"></a>設定 SSL 憑證
需要 SSL 憑證，才能將通訊加密和驗證伺服器。從以下三種案例中選擇最適用的案例，然後執行其所有步驟：

### 建立新的自我簽署憑證

1.    [建立自我簽署憑證][]
2.    [建立自我簽署 SSL 憑證的 PFX 檔案][]
3.    [將 SSL 憑證上傳至雲端服務][]
4.    [在服務組態檔中更新 SSL 憑證][]
5.    [匯入 SSL 憑證授權單位][]

### 從憑證存放區使用現有的憑證
1. [從憑證存放區匯出 SSL 憑證][]
2. [將 SSL 憑證上傳至雲端服務][]
3. [在服務組態檔中更新 SSL 憑證][]

### 使用 PFX 檔案中現有的憑證

1. [將 SSL 憑證上傳至雲端服務][]
2. [在服務組態檔中更新 SSL 憑證][]

## <a name="configuring-client-certs"></a>設定用戶端憑證
需要用戶端憑證，才能驗證服務的要求。從以下三種案例中選擇最適用的案例，然後執行其所有步驟：

### 關閉用戶端憑證
1.    [關閉用戶端憑證式驗證][]

### 發行新的自我簽署用戶端憑證
1.    [建立自我簽署憑證授權單位][]
2.    [將 CA 憑證上傳至雲端服務][]
3.    [在服務組態檔中更新 CA 憑證][]
4.    [發行用戶端憑證][]
5.    [建立用戶端憑證的 PFX 檔案][]
6.    [匯入用戶端憑證][]
7.    [複製用戶端憑證指紋][]
8.    [在服務組態檔中設定允許的用戶端][]

### 使用現有的用戶端憑證
1.    [尋找 CA 公開金鑰][]
2.    [將 CA 憑證上傳至雲端服務][]
3.    [在服務組態檔中更新 CA 憑證][]
4.    [複製用戶端憑證指紋][]
5.    [在服務組態檔中設定允許的用戶端][]
6.    [設定用戶端憑證撤銷檢查][]

## <a name="allowed-ip-addresses"></a>允許的 IP 位址

您可將服務端點的存取限制為特定的 IP 位址範圍。

## 設定存放區加密

需要憑證來加密儲存在中繼資料存放區中的認證。從以下三種案例中選擇最適用的案例，然後執行其所有步驟：

### 使用新的自我簽署憑證

1.     [建立自我簽署憑證][]
2.     [建立自我簽署加密憑證的 PFX 檔案][]
3.     [將加密憑證上傳至雲端服務][]
4.     [在服務組態檔中更新加密憑證][]

### 從憑證存放區使用現有的憑證

1.     [從憑證存放區匯出加密憑證][]
2.     [將加密憑證上傳至雲端服務][]
3.     [在服務組態檔中更新加密憑證][]

### 使用 PFX 檔案中現有的憑證

1.     [將加密憑證上傳至雲端服務][]
2.     [在服務組態檔中更新加密憑證][]

## 預設組態

預設組態會拒絕對 HTTP 端點的所有存取。這是建議設定，因為對這些端點的要求可能帶有機密資訊 (如資料庫認證)。
預設組態會允許對 HTTPS 端點的所有存取。這項設定可能會進一步限制。

### 變更組態

套用至與端點的存取控制規則群組會設定於**服務組態檔**的 **<EndpointAcls>** 區段中。

    <EndpointAcls>
      <EndpointAcl role="SplitMergeWeb" endPoint="HttpIn" accessControl="DenyAll" />
      <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="AllowAll" />
    </EndpointAcls>

存取控制群組中的規則會設定於服務組態檔的 <AccessControl name=""> 區段中。 

網路存取控制清單文件會說明其格式。
例如，若只要允許範圍 100.100.0.0 至 100.100.255.255 中的 IP 存取 HTTPS 端點，則規則看起來如下：

    <AccessControl name="Retricted">
      <Rule action="permit" description="Some" order="1" remoteSubnet="100.100.0.0/16"/>
      <Rule action="deny" description="None" order="2" remoteSubnet="0.0.0.0/0" />
    </AccessControl>
    <EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="Restricted" />

## <a name = "denial-of-service-prevention"></a>阻絕服務預防

支援兩個不同的機制來偵測並防止阻斷服務攻擊：

*    限制每個遠端主機的並行要求數目 (預設為關閉)
*    限制每個遠端主機的存取速率 (預設為開關)

這些是根據「IIS 中的動態 IP 安全性」所進一步記載的功能。變更此設定時，請注意下列因素：

* Proxy 和網路位址轉譯裝置對遠端主機資訊的行為
* 考量每個 Web 角色中對任何資源的要求 (例如載入指令碼、影像等)

## 限制並行存取的數量

設定此行為的設定包括：

    <Setting name="DynamicIpRestrictionDenyByConcurrentRequests" value="false" />
    <Setting name="DynamicIpRestrictionMaxConcurrentRequests" value="20" />

將 DynamicIpRestrictionDenyByConcurrentRequests 變更為 true，以啟用這項保護。

## 限制存取的速率

設定此行為的設定包括：

    <Setting name="DynamicIpRestrictionDenyByRequestRate" value="true" />
    <Setting name="DynamicIpRestrictionMaxRequests" value="100" />
    <Setting name="DynamicIpRestrictionRequestIntervalInMilliseconds" value="2000" />

## 設定拒絕要求的回應

下列設定會設定拒絕要求的回應：

    <Setting name="DynamicIpRestrictionDenyAction" value="AbortRequest" />
關於其他支援的值，請參閱「IIS 中的動態 IP 安全性」文件。

## 設定服務憑證的作業
本主題僅供參考。請遵循以下所述的設定步驟：

* 設定 SSL 憑證
* 設定用戶端憑證

## <a name = "create-self-signed-cert"></a>建立自我簽署憑證
執行：

    makecert ^
      -n "CN=myservice.cloudapp.net" ^
      -e MM/DD/YYYY ^
      -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1" ^
      -a sha1 -len 2048 ^
      -sv MySSL.pvk MySSL.cer

自訂：

*    -n 與服務 URL。支援萬用字元 ("CN=*.cloudapp.net") 和替代名稱 ("CN=myservice1.cloudapp.net, CN=myservice2.cloudapp.net")。
*    -e 與憑證到期日
建立強式密碼，並於提示時指定它。

## <a name="create-pfx-for-self-signed-cert"></a>建立自我簽署 SSL 憑證的 PFX 檔案

執行：

        pvk2pfx -pvk MySSL.pvk -spc MySSL.cer

輸入密碼，然後使用這些選項來匯出憑證：
* 是，匯出私密金鑰
* 匯出所有延伸內容

## <a name="export-ssl-from-store"></a>從憑證存放區匯出 SSL 憑證

* 尋找憑證
* 按一下 [動作] -> [所有工作] -> [匯出...]
* 使用這些選項將憑證匯出至 .PFX 檔案：
    * 是，匯出私密金鑰
    * 如果可能的話，包含憑證路徑中的所有憑證
    *匯出所有延伸內容

## <a name="upload-ssl"></a>將 SSL 憑證上傳至雲端服務

連同現有或產生的 .PFX 檔案及 SSL 金鑰組一起上傳憑證：

* 輸入密碼以保護私密金鑰資訊

## <a name="update-ssl-in-csfg"></a>在服務組態檔中更新 SSL 憑證

使用上傳至雲端服務的憑證指紋，在服務組態檔中更新下列設定的指紋值：

    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="import-ssl-ca"></a>匯入 SSL 憑證授權單位

在所有會與服務通訊的帳戶/電腦中，遵循下列步驟：

* 在 Windows 檔案總管中按兩下 .CER 檔案
* 在 [憑證] 對話方塊中，按一下 [安裝憑證...]
* 將憑證匯入信任的根憑證授權單位存放區

## <a name="turn-off-client-cert"></a>關閉用戶端憑證式驗證

僅支援用戶端憑證式驗證，停用它將允許公開存取服務端點，除非有其他機制存在 (例如 Microsoft Azure 虛擬網路)。

在服務組態檔中將這些設定變更為 false 以關閉功能：

    <Setting name="SetupWebAppForClientCertificates" value="false" />
    <Setting name="SetupWebserverForClientCertificates" value="false" />

接著，在 CA 憑證設定中複製與 SSL 憑證相同的憑證指紋：

    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="create-self-signed-ca"></a>建立自我簽署憑證授權單位
執行下列步驟來建立自我簽署憑證，以做為憑證授權單位：

    makecert ^
    -n "CN=MyCA" ^
    -e MM/DD/YYYY ^
     -r -cy authority -h 1 ^
     -a sha1 -len 2048 ^
      -sr localmachine -ss my ^
      MyCA.cer

自訂

*    -e 與憑證到期日


## <a name="find-ca-public-key"></a>尋找 CA 公開金鑰

所有用戶端憑證必須已由服務信任的憑證授權單位發出。尋找發出用戶端憑證的憑證授權單位的公開金鑰，這些用戶端憑證將用於驗證，以便將公開金鑰上載至雲端服務。

如果無法取得含有公用金鑰的檔案，請從憑證存放區匯出：

* 尋找憑證
    * 搜尋相同憑證授權單位所發出的用戶端憑證
* 按兩下憑證
* 在 [憑證] 對話方塊中選取 [憑證路徑] 索引標籤
* 按兩下路徑中的 CA 項目 
* 記下憑證屬性
* 關閉 [憑證] 對話方塊
* 尋找憑證
    * 搜尋剛才記下的 CA
* 按一下 [動作] -> [所有工作] -> [匯出...]
* 使用這些選項將憑證匯出至 .CER：
    * 否，不要匯出私密金鑰
    * 如果可能的話，包含憑證路徑中的所有憑證
    * 匯出所有延伸內容

## <a name="upload-ca-cert"></a>將 CA 憑證上傳至雲端服務

連同現有或產生的 .CER 檔案及 SSL 公開金鑰一起上傳憑證。

## <a name="update-ca-in-csft"></a>在服務組態檔中更新 CA 憑證

使用上傳至雲端服務的憑證指紋，在服務組態檔中更新下列設定的指紋值：

    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

使用相同的憑證指紋更新下列設定的值：

    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />

## <a name="issue-client-certs"></a>發行用戶端憑證

每個獲授權存取服務的人應該有發行給他專用的用戶端憑證，而且應該選擇他自己的強式密碼來保護私密金鑰。 

在產生及儲存自我簽署 CA 憑證的同一部電腦上，必須執行下列步驟：

    makecert ^
      -n "CN=My ID" ^
      -e MM/DD/YYYY ^
      -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.2" ^
      -a sha1 -len 2048 ^
      -in "MyCA" -ir localmachine -is my ^
      -sv MyID.pvk MyID.cer

自訂：

* -n 與將會使用此憑證進行驗證的用戶端的識別碼
* -e 與憑證到期日
* MyID.pvk 和 MyID.cer，使用這個用戶端憑證的唯一檔名

此命令會提示建立密碼，然後使用一次。請使用強式密碼。

## <a name="create-pfx-files"></a>建立用戶端憑證的 PFX 檔案

對於每個產生的用戶端憑證，請執行：

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

自訂：

    MyID.pvk 和 MyID.cer，使用用戶端憑證的檔名

輸入密碼，然後使用這些選項來匯出憑證：

* 是，匯出私密金鑰
* 匯出所有延伸內容
* 此憑證的發行對象應該選擇匯出密碼

## <a name="import-client-cert"></a>匯入用戶端憑證

用戶端憑證已發給的每個人，應該在他要用來與服務通訊的電腦中匯入金鑰組：

* 在 Windows 檔案總管中按兩下 .PFX 檔案
* 至少使用這個選項，將憑證匯入個人存放區：
    * 核取 [包含所有延伸內容]

## <a name="copy-client-cert"> </a> 複製用戶端憑證指紋
用戶端憑證已發給的每個人必須依照下列步驟，以取得將加入至服務組態檔的憑證指紋：
* 執行 certmgr.exe
* 選取 [個人] 索引標籤
* 按兩下要用於驗證的用戶端憑證
* 在開啟的 [憑證] 對話方塊中，選取 [詳細資料] 索引標籤
* 請確定 [顯示] 是顯示 [全部]
* 在清單中選取名為 [憑證指紋] 的欄位
* 複製憑證指紋的值
**刪除第一個數字前面不可見的 Unicode 字元
**刪除所有的空格

## <a name="configure-allowed-client"></a>在服務組態檔中設定允許的用戶端

使用允許存取服務的用戶端憑證指紋的逗號分隔清單，在服務組態檔中更新下列設定的值：

    <Setting name="AllowedClientCertificateThumbprints" value="" />

## <a name="configure-client-revocation"></a>設定用戶端憑證撤銷檢查

預設不會向憑證授權單位查詢用戶端憑證的撤銷狀態。若要開啟檢查 (如果發行用戶端憑證的憑證授權單位支援此檢查)，請使用 X509RevocationMode 列舉所定義的其中一個值來變更下列設定：

    <Setting name="ClientCertificateRevocationCheck" value="NoCheck" />

## <a name="create-pfx-files-encryption"></a>建立自我簽署加密憑證的 PFX 檔案

如需加密憑證，請執行：

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

自訂：

    MyID.pvk 和 MyID.cer，使用加密憑證的檔名

輸入密碼，然後使用這些選項來匯出憑證：
*    是，匯出私密金鑰
*    匯出所有延伸內容
*    將憑證上傳至雲端服務時，您將需要密碼。

## <a name="export-encryption-from-store"></a>從憑證存放區匯出加密憑證

*    尋找憑證
*    按一下 [動作] -> [所有工作] -> [匯出...]
*    使用這些選項將憑證匯出至 .PFX 檔案： 
  *    是，匯出私密金鑰
  *    如果可能的話，包含憑證路徑中的所有憑證 
*    匯出所有延伸內容

## <a name="upload-encryption-cert"></a>將加密憑證上傳至雲端服務

將現有或產生的 .PFX 檔案及加密金鑰組連同憑證一起上傳：

* 輸入密碼以保護私密金鑰資訊

## <a name="update-encryption-in-csft"></a>在服務組態檔中更新加密憑證

使用上傳至雲端服務的憑證指紋，在服務組態檔中更新下列設定的指紋值：

    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />

## 常見的憑證作業

* 設定 SSL 憑證
* 設定用戶端憑證

## 尋找憑證

請遵循下列步驟：

1. 執行 mmc.exe。
2. [檔案] -> [新增/移除嵌入式管理單元]
3. 選取 [憑證]
4. 按一下 [新增]
5. 選擇憑證存放區位置
6. 按一下 [完成]
7. Click OK
8. 展開 [憑證]
9. 展開憑證存放區
10. 展開 [憑證] 子節點
11. 在右邊的清單中選取憑證

## 匯出憑證
在 [憑證匯出精靈] 中：

1. 按 [下一步]
2. 選取 [是，匯出私密金鑰]
3. 按 [下一步]
4. 選取想要的輸出檔案格式
5. 核取所需的選項
6. 核取 [密碼]
7. 輸入增強式密碼並確認它
8. 按 [下一步]
9. 輸入或瀏覽至用來儲存憑證的檔案名稱 (使用 .PFX 副檔名)
10. 按 [下一步]
11. 按一下 [完成]
12. Click OK 

## 匯入憑證

在 [憑證匯入精靈] 中：

1. 選取存放區位置。
    1.     [目前使用者] (如果只有在目前使用者下執行的處理程序會存取服務)
    2.     [本機電腦] (如果這台電腦中的其他處理程序會存取服務)
2. 按 [下一步]
3. 如果從檔案匯入，請確認檔案路徑
4. 如果匯入 .PFX 檔案：
    1.     輸入密碼以保護私密金鑰
    2.     選取匯入選項
5.     選取 [將憑證放在下列存放區]
6.     按一下 [瀏覽]
7.     選取所需的存放區
8.     按一下 [完成]
    1.     若已選擇 [受信任的根憑證授權單位] 存放區，請按一下 [是]
9.     在所有對話方塊視窗上，按一下 [確定]

## <a name="upload-certificate"></a>上傳憑證

在 [Azure 管理入口網站](http://manage.windowsazure.com/)

1. 選取 [雲端服務]
2. 選取雲端服務
3. 按一下頂端功能表的 [憑證]
4. 按一下底列的 [上傳]
5. 選取憑證檔
6. 如果是 .PFX 檔案，請輸入私密金鑰的密碼
7. 完成後，請從清單中的新項目複製憑證指紋

# <a name="other-security"></a>其他安全性考量
 
使用 HTTPS 端點時，這份文件中所述的 SSL 設定會加密服務和用戶端之間的通訊。這很重要，因為通訊中包含用來存取資料庫和其他可能機密資訊的認證。但是請注意，服務會將內部狀態 (包括認證) 保存在 Microsoft Azure SQL 資料庫的內部資料表中 (您在 Microsoft Azure 訂用帳戶中已提供作為中繼資料儲存體)。在服務組態檔中 (.CSCFG 檔案)，下列設定中已定義該資料庫： 

    <Setting name="ElasticScaleMetadata" value="Server=..." />

這個資料庫中儲存的認證會被加密。此外，最佳作法是確定服務部署的 Web 和背景工作角色保持在最新狀態且安全，因為它們都能存取中繼資料資料庫和用來加密和解密已儲存的認證。 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

[設定憑證]:#configuring-certificates
[允許的 IP 位址]:#allowed-ip-addresses
[設定用戶端憑證]:#configuring-client-certs
[建立自我簽署憑證]:#create-self-signed-cert
[建立自我簽署 SSL 憑證的 PFX 檔案]:#create-pfx-for-self-signed-cert
[將 SSL 憑證上傳至雲端服務]: #upload-ssl
[在服務組態檔中更新 SSL 憑證]: #update-ssl-in-csfg
[匯入 SSL 憑證授權單位]: "import-ssl-ca"
[從憑證存放區匯出 SSL 憑證]: #export-ssl-from-store
[關閉用戶端憑證式驗證]: #turn-off-client-cert
[建立自我簽署憑證授權單位]:#create-self-signed-ca
[複製用戶端憑證指紋]:#copy-client-cert
[將 CA 憑證上傳至雲端服務]:#upload-ca-cert
[在服務組態檔中更新 CA 憑證]:#update-ca-in-csft
[發行用戶端憑證]:#issue-client-certs
[建立用戶端憑證的 PFX 檔案]:#create-pfx-files
[匯入用戶端憑證]:#import-client-cert
[在服務組態檔中設定允許的用戶端]:#configure-allowed-client
[尋找 CA 公開金鑰]:#find-ca-public-key
[設定用戶端憑證撤銷檢查]:#configure-client-revocation
[阻絕服務預防]:#denial-of-service-prevention
[取得憑證]:#obtain-certificates
[產生憑證的工具]:#tools
[設定 SSL 憑證]:#to-configure-ssl-cert
[其他安全性考量]:#other-security 
[上傳憑證]:#upload-certificate
[建立自我簽署加密憑證的 PFX 檔案]:#create-pfx-files-encryption
[將加密憑證上傳至雲端服務]:#upload-encryption-cert 
[在服務組態檔中更新加密憑證]:#update-encryption-in-csft
[從憑證存放區匯出加密憑證]:#export-encryption-from-store

<!--HONumber=49--> 