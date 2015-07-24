<properties 
   pageTitle="使用 HDinsight 中的 Apache Phoenix 和 SQuirreL | Microsoft Azure" 
   description="了解如何使用 HDinsight 中的 Apache Phoenix，以及如何在您的工作站上安裝與設定 SQuirreL 以連線到 HDInsight 中的 HBase 叢集。" 
   services="hdinsight" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/05/2015"
   ms.author="jgao"/>

# 在 HDinsight 中搭配 HBase 叢集使用 Phoenix 和 SQuirreL  

了解如何使用 HDinsight 中的 [Apache Phoenix](http://phoenix.apache.org/)，以及如何在您的工作站上安裝與設定 SQuirreL 以連線到 HDInsight 中的 HBase 叢集。如需有關 Phoenix 的詳細資訊，請參閱 [15 分鐘內了解 Phoenix](http://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html)。如需 Phoenix 文法，請參閱 [Phoenix 文法](http://phoenix.apache.org/language/index.html)。

>[AZURE.NOTE]如需 HDInsight 中的 Phoenix 版本資訊，請參閱 [HDInsight 在 Hadoop 叢集版本中提供什麼新功能？][hdinsight-versions]

##使用 SQLLine
[SQLLine](http://sqlline.sourceforge.net/) 是執行 SQL 的命令列公用程式。

###必要條件
開始使用 SQLLine 之前，您必須具備下列項目：

- **HDInsight 中的 HBase 叢集**。如需有關佈建 HBase 叢集的資訊，請參閱[開始使用 HDInsight 中的 Apache HBase][hdinsight-hbase-get-started]。
- **透過遠端桌面通訊協定連接到 HBase 叢集**。如需相關指示，請參閱[使用 Azure 入口網站管理 HDInsight 中的 Hadoop 叢集][hdinsight-manage-portal]。

**找出主機名稱**

1. 從桌面開啟 **Hadoop 命令列**。
2. 執行下列命令以取得 DNS 尾碼：

		ipconfig

	並記下**連線專用 DNS 尾碼**。例如， *myhbasecluster.f5.internal.cloudapp.net*。當您連線至 HBase 叢集時，您必須使用 FQDN 連線到其中一個 Zookeeper。每個 HDInsight 叢集有 3 個 Zookeeper。它們是 *zookeeper0*、*zookeeper1* 和 *zookeeper2*。FQDN 看起來像是 *zookeeper2.myhbasecluster.f5.internal.cloudapp.net*。

**使用 SQLLine**

1. 從桌面開啟 **Hadoop 命令列**。
2. 執行下列命令來開啟 SQLLine：

		cd %phoenix_home%\bin
		sqlline.py [The FQDN of one of the Zookeepers]

	![hdinsight hbase phoenix sqlline][hdinsight-hbase-phoenix-sqlline]

	此範例中使用的命令：

		CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));
		
		!tables;
		
		UPSERT INTO Company VALUES(1, 'Microsoft');
		
		SELECT * FROM Company;

如需詳細資訊，請參閱 [SQLLine 手冊](http://sqlline.sourceforge.net/#manual)和 [Phoenix 文法](http://phoenix.apache.org/language/index.html)。


















##使用 SQuirreL

[SQuirrel SQL 用戶端](http://squirrel-sql.sourceforge.net/) 是圖形化 Java 程式，可讓您檢視 JDBC 相容資料庫的結構、瀏覽資料表中的資料，並發出 SQL 命令等等。它可以用來連接到 HDInsight 上的 Apache Phoenix。

本結說明如在您的工作站上安裝與設定 SQuirreL 以透過 VPN 連線到 HDInsight 中的 HBase 叢集。

###必要條件

遵循程序之前，您必須具備下列項目：

- 將 HBase 叢集部署至具備 DNS 虛擬機器的 Azure 虛擬網路。如需相關指示，請參閱 [在 Azure 虛擬網路上佈建 HBase 叢集][hdinsight-hbase-provision-vnet]。 

	>[AZURE.IMPORTANT]您必須將 DNS 伺服器安裝到虛擬網路。如需相關指示，請參閱[設定兩個 Azure 虛擬網路之間的 DNS](hdinsight-hbase-geo-replication-configure-DNS.md)

- 取得 HBase 叢集的連線專用 DNS 尾碼。若要取得該尾碼，請 RDP 到叢集，然後執行 IPConfig。DNS 尾碼會類似於：

		myhbase.b7.internal.cloudapp.net
- 在您的工作站上下載並安裝 [Microsoft Visual Studio Express 2013 for Windows Desktop](https://www.visualstudio.com/products/visual-studio-express-vs.aspx)。您將需要封裝的 makecert 以建立您的憑證。  
- 在您的工作站上下載並安裝 [Java Runtime Environment](http://www.oracle.com/technetwork/java/javase/downloads/jre7-downloads-1880261.html)。SQuirreL SQL 用戶端 3.0 版和更新版本需要 JRE 1.6 版或更新版本。  


###設定點對站 VPN 連線到 Azure 虛擬網路

設定點對站 VPN 連線包含 3 個步驟：

1. [設定虛擬網路和動態路由閘道](#Configure-a-virtual-network-and-a-dynamic-routing-gateway)
2. [建立您的憑證](#Create-your-certificates)
3. [設定 VPN 用戶端](#Configure-your-VPN-client)

如需詳細資訊，請參閱[設定點對站 VPN 連線到 Azure 虛擬網路](https://msdn.microsoft.com/library/azure/dn133792.aspx)。

#### 設定虛擬網路和動態路由閘道

確保您已在 Azure 虛擬網路中佈建 HBase 叢集 (請參閱本節的必要條件)。下一步是設定點對站連線。

**設定點對站連線**

1. 登入 [Azure 入口網站][azure-portal]。
2. 在左側按一下 [**網路**]。
3. 按一下您已建立的虛擬網路 (請參閱[在 Azure 虛擬網路上佈建 HBase 叢集][hdinsight-hbase-provision-vnet])。
4. 按一下頂端的 [**設定**]。
5. 在 [**點對站連線**] 區段中，選取 [**設定點對站連線**]。 
6. 設定 **起始 IP** 和 **CIDR** 來指定您的 VPN 用戶端在連線時接收 IP 位址的 IP 位址範圍。此範圍不能與任何位於內部部署網路及您將連線之 Azure 虛擬網路的範圍重疊。例如，如果您選取 10.0.0.0/20 做為虛擬網路，您可以選取 10.1.0.0/24 做為用戶端的位址空間。如需詳細資訊，請參閱 [[點對站連線][vnet-point-to-site-connectivity]] 頁面。
7. 在 [虛擬網路位址空間] 區段中，按一下 [**新增閘道子網路**]。
7. 按一下頁面底部的 [**儲存**]。
8. 按一下 [**是**] 以確認變更。請等候系統完成變更，才能繼續進行下一個程序。


**建立動態路由閘道**

1. 從 Azure 入口網站中，按一下頁面頂端的 [**儀表板**]。
2. 按一下頁面底部的 [**建立閘道**]。
3. 按一下 [**是**] 以確認。請等候建立閘道。
4. 按一下頂端的 [**儀表板**]。您會看到虛擬網路的視覺化圖表：

	![Azure 虛擬網路點對站虛擬圖表][img-vnet-diagram]

	此圖表顯示 0 個用戶端連線。建立虛擬網路的連線之後，此數字將會更新為一。

#### 建立您的憑證

建立 X.509 憑證的其中一個方式是使用 [Microsoft Visual Studio Express 2013 for Windows Desktop](https://www.visualstudio.com/products/visual-studio-express-vs.aspx) 隨附的憑證建立工具 (makecert.exe)。


**建立自我簽署根憑證**

1. 從您的工作站，開啟命令提示字元視窗。
2. 瀏覽至 [Visual Studio 工具] 資料夾。 
3. 下面範例中的下列命令將在您工作站上的個人憑證存放區中建立與安裝根憑證，並建立您稍後將上傳至 Azure 入口網站的相對應 .cer 檔案。 

		makecert -sky exchange -r -n "CN=HBaseVnetVPNRootCertificate" -pe -a sha1 -len 2048 -ss My "C:\Users\JohnDole\Desktop\HBaseVNetVPNRootCertificate.cer"

	將變更為您想要在其中放置 .cer 檔案的目錄，在該目錄中 HBaseVnetVPNRootCertificate 是您想要使用的憑證名稱。

	不要關閉命令提示字元。您將在下一個程序中需要此提示字元。

	>[AZURE.NOTE]因為您已經建立會產生用戶端憑證的根憑證，您可能會想要將此憑證及其私密金鑰匯出，並將它儲存到可復原的安全位置。

**建立用戶端憑證**

- 從相同的命令提示字元 (必須在您建立根憑證所在的相同電腦上。用戶端憑證必須從根憑證產生)，執行下列命令：

  		makecert.exe -n "CN=HBaseVnetVPNClientCertificate" -pe -sky exchange -m 96 -ss My -in "HBaseVnetVPNRootCertificate" -is my -a sha1

	HBaseVnetVPNRootCertificate 是根憑證名稱。它必須符合根憑證名稱。

	根憑證和用戶端憑證都會儲存在電腦上的個人憑證存放區中。使用 certmgr.msc 來驗證。

	![Azure 虛擬網路點對站 vpn 憑證][img-certificate]

	您想要連接到虛擬網路的每一部電腦都必須安裝用戶端憑證。建議您最好針對要連接到虛擬網路的每部電腦建立唯一的用戶端憑證。若要匯出用戶端憑證，請使用 certmgr.msc。

**將根憑證上傳至 Azure 入口網站**

1. 從 Azure 入口網站，按一下左邊的 [**網路**]。
2. 按一下 HBase 叢集部署所在的虛擬網路。
3. 按一下頂端的 [**憑證**]。
4. 按一下底部的 [**上傳**]，並指定您已在前一個程序中建立的根憑證檔案。請等到憑證匯入。
5. 按一下頂端的 [**儀表板**]。虛擬圖表會顯示狀態。


#### 設定 VPN 用戶端



**下載與安裝用戶端 VPN 封裝**

1. 從快速瀏覽區段中虛擬網路的 [儀表板] 頁面，根據您的工作站 OS 版本按一下 [**下載 64 位元用戶端 VPN 封裝**] 或 [**下載 32 位元用戶端 VPN 封裝**]。
2. 按一下 [**執行**] 以安裝封裝。
3. 在安全性提示字元中，按一下 [**更多資訊**]，然後按一下 [**仍要執行**]。
4. 按兩下 [**是**]。

**連線到 VPN**

1. 在您的工作站桌面上，按一下工作列上的網路圖示。您會看到虛擬網路名稱的 VPN 連線。
2. 按一下 VPN 連線名稱。
3. 按一下 [連接]。

**測試 VPN 連線和網域名稱解析**

- 若 HBase 叢集的 DNS 尾碼為 myhbase.b7.internal.cloudapp.net，請從工作站開啟命令提示字元，並且 ping 下列其中一個名稱：

		zookeeper0.myhbase.b7.internal.cloudapp.net
		zookeeper0.myhbase.b7.internal.cloudapp.net
		zookeeper0.myhbase.b7.internal.cloudapp.net
		headnode0.myhbase.b7.internal.cloudapp.net
		headnode1.myhbase.b7.internal.cloudapp.net
		workernode0.myhbase.b7.internal.cloudapp.net

###安裝與設定您的工作站上的 SQuirreL

**安裝 SQuirreL**

1. 從 [http://squirrel-sql.sourceforge.net/#installation](http://squirrel-sql.sourceforge.net/#installation) 下載 SQuirrel SQL 用戶端 jar 檔案。
2. 開啟/執行 jar 檔案。它需要 [Java Runtime Environment](http://www.oracle.com/technetwork/java/javase/downloads/jre7-downloads-1880261.html)。
3. 按兩下 [**下一步**]。
4. 指定您具有寫入權限的路徑，然後按 [**下一步**]。
	>[AZURE.NOTE]預設的安裝資料夾位於 C:\\Program Files\\squirrel sql 3.6 資料夾中。若要寫入此路徑，必須將系統管理員權限授與安裝程式。您可以系統管理員身分開啟命令提示字元、瀏覽至 Java 的 bin 資料夾，然後再執行
	>
	>     java.exe -jar [the path of the SQuirreL jar file] 
5. 按一下 [**確定**] 以確認建立目標目錄。
6. 預設設定是安裝基底和標準封裝。按 [下一步]。
7. 依序按兩下 [**下一步**] 和 [**完成**]。


**安裝 Phoenix 驅動程式**

Phoenix 驅動程式 jar 檔案位於 HBase 叢集上。此路徑根據版本與下列項目相似：

	C:\apps\dist\phoenix-4.0.0.2.1.11.0-2316\phoenix-4.0.0.2.1.11.0-2316-client.jar
您必須將它複製到您的工作站中的 [SQuirreL 安裝資料夾]/lib 路徑下。最簡單的方法是 RDP 到叢集中，然後再使用檔案複製/貼上 (CTRL + C 和 CTRL + V 鍵)，將它複製到您的工作站。

**將 Phoenix 驅動程式新增至 SQuirreL**

1. 從您的工作站開啟 SQuirreL SQL 用戶端。
2. 按一下左邊的 [**驅動程式**] 索引標籤。
2. 從 [**驅動程式**] 功能表，按一下[ **新增驅動程式**]。
3. 輸入以下資訊：

	- **名稱**：Phoenix
	- **範例 URL**：jdbc:phoenix:zookeeper2.contoso-hbase-eu.f5.internal.cloudapp.net
	- **類別名稱**：org.apache.phoenix.jdbc.PhoenixDriver

	>[AZURE.WARNING]使用者在範例 URL 中全部小寫。您可以使用完整 zookeeper 仲裁，以免其中一項已關閉。主機名稱是 zookeeper0、zookeeper1 和 zookeeper2。

	![HDInsight HBase Phoenix SQuirreL 驅動程式][img-squirrel-driver]
4. 按一下 [確定]。

**建立 HBase 叢集的別名**

1. 從 SQuirreL，按一下左邊的 [**別名**] 索引標籤。
2. 從 [**別名**] 功能表，按一下 [**新增別名**]。
3. 輸入以下資訊：

	- **名稱**：HBase 叢集的名稱或您偏好的任何名稱。
	- **驅動程式**：Phoenix。必須符合您在上一個程序中建立的驅動程式名稱。
	- **URL**：從驅動程式組態複製的 URL。請確定使用者全部小寫。
	- **使用者名稱**：可以是任何文字。因為您在此使用 VPN 連線，所以完全不會使用使用者名稱。
	- **密碼**：可以是任何文字。

	![HDInsight HBase Phoenix SQuirreL 驅動程式][img-squirrel-alias]
4. 按一下 [**測試**]。 
5. 按一下 [連接]。當它建立連線時，SQuirreL 如下所示：

	![HBase Phoenix SQuirrel][img-squirrel]

**執行測試**

1. 按一下 [**物件**] 索引標籤旁邊的 [**SQL**] 索引標籤。
2. 複製並貼上下列程式碼：

		CREATE TABLE IF NOT EXISTS us_population (state CHAR(2) NOT NULL, city VARCHAR NOT NULL, population BIGINT  CONSTRAINT my_pk PRIMARY KEY (state, city))
3. 按一下 [執行] 按鈕。

	![HBase Phoenix SQuirrel][img-squirrel-sql]
4. 切換回 [**物件**] 索引標籤。
5. 展開別名名稱，然後再展開**資料表**。您會看到底下所列的新資料表。
 
##後續步驟
在本文中，您已經學會如何在 HDInsight 中使用 Apache Phoenix。若要深入了解，請參閱：

- [HDInsight HBase 概觀][hdinsight-hbase-overview]：HBase 是建置於 Hadoop 上的 Apache 開放原始碼 NoSQL 資料庫，可針對大量非結構化及半結構化資料，提供隨機存取功能和強大一致性。
- [在 Azure 虛擬網路上佈建 HBase 叢集][hdinsight-hbase-provision-vnet]：由於 HBase 叢集已與虛擬網路整合，因此能夠部署到和應用程式相同的虛擬網路，讓應用程式得以和 HBase 直接通訊。
- [設定 HDInsight 中的 HBase 複寫](hdinsight-hbase-geo-replication.md)：了解如何跨兩個 Azure 資料中心設定 HBase 複寫。 
- [利用 HDInsight 中的 HBase 分析 Twitter 情緒][hbase-twitter-sentiment]：了解如何使用 HDInsight 之 Hadoop 叢集中的 HBase，執行巨量資料的即時[情緒分析](http://en.wikipedia.org/wiki/Sentiment_analysis)。

[azure-portal]: https://manage.windowsazure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-hbase-get-started]: ../hdinsight-hbase-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

[hdinsight-hbase-phoenix-sqlline]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-phoenix-sqlline.png
[img-certificate]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vpn-certificate.png
[img-vnet-diagram]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vnet-point-to-site.png
[img-squirrel-driver]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-driver.png
[img-squirrel-alias]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-alias.png
[img-squirrel]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel.png
[img-squirrel-sql]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-sql.png


 

<!---HONumber=July15_HO2-->