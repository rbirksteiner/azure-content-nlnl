<properties title="Miscellaneous Considerations for Oracle Virtual Machine Images" pageTitle="針對 Oracle 虛擬機器映像的其他考量" description="在 Microsoft Azure 中部署 Oracle 虛擬機器前先了解其他考量。" services="virtual-machines" authors="bbenz" documentationCenter=""/>
<tags ms.service="virtual-machines" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="infrastructure-services" ms.date="06/22/2015" ms.author="bbenz" />
#針對 Oracle 虛擬機器映像的其他考量
本文章涵蓋針對 Azure 上 Oracle 虛擬機器的考量，此虛擬機器以 Microsoft 提供的 Oracle 軟體映像為基礎，且作業系統為 Windows Server。

-  Oracle 資料庫虛擬機器映像
-  Oracle WebLogic Server 虛擬機器映像
-  Oracle JDK 虛擬機器映像

##Oracle 資料庫虛擬機器映像
### 不支援叢集 (RAC)

Azure 目前不支援 Oracle 資料庫叢集。僅支援獨立 Oracle 資料庫執行個體。因為 Azure 目前不支援多個 VM 之間讀取/寫入方式的虛擬磁碟機共用。也不支援 UDP 多點傳送。

### 無靜態內部 IP

Azure 會指派每部虛擬機器各一個內部 IP 位址。除非 VM 屬於虛擬網路的一部分，否則 VM 的 IP 位址將是動態位址，且 VM 重新啟動之後可能會改變。這可能會造成問題，因為 Oracle 資料庫預期的是靜態 IP 位址。若要避免這個問題，請考慮將 VM 加入 Azure 虛擬網路。如需詳細資訊，請參閱[虛擬網路](http://azure.microsoft.com/documentation/services/virtual-network/)和[在 Azure 中建立虛擬網路](create-virtual-network.md)。

### 連接的磁碟組態選項

您可以將資料檔案放在 VM 的作業系統 (OS) 磁碟，或連接的磁碟 (又稱資料磁碟)。相較於 OS 磁碟，連接的磁碟可能會提供更好的效能和具彈性的大小。OS 磁碟可能只適合 10 GB 以下的資料庫。

連接的磁碟依賴 Azure Blob 儲存體服務。理論上每個磁碟最多都能達約每秒 500 輸入/輸出作業 (IOPS)。連接的磁碟一開始可能未達最佳效能，經過持續作業約 60-90 分鐘的燒錄 (burn-in) 期間後，IOPS 效能可能會大幅改善。若磁碟之後保持閒置，IOPS 效能可能會縮減，直到另一個持續作業的燒錄期間。簡單地說，磁碟越活躍就越接近最佳 IOPS 效能。

雖然最簡單的方法是將單一磁碟連接至 VM，並將資料庫檔案放在該磁碟，但這個方法也對效能產生較多限制。反之，透過使用多個連接的磁碟，並將資料庫資料分散至其間，然後使用 Oracle Automatic Storage Management (ASM)，通常就能改善有效的 IOPS 效能。如需詳細資訊，請參閱 [Oracle Automatic Storage 概觀](http://www.oracle.com/technetwork/database/index-100339.html) (英文)。雖然可以使用作業系統層級的多重磁碟等量，但因為它對 IOPS 效能的改善尚屬未知，所以不建議此方法。

請根據您希望最佳化資料庫讀取作業或寫入作業的效能，考慮兩種連接多個磁碟的方法：

- 相較於使用 Windows Server 2012 儲存體集區的方法，「獨立的 Oracle ASM」可能會有較佳的寫入作業效能，但讀取作業的 IOPS 較差。下圖以邏輯方式說明這種排列方式。![](media/virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images/image2.png)

- 如果您的資料庫主要執行讀取作業，或者您注重讀取作業效能勝於寫入作業，「搭配 Windows Server 2012 儲存體集區的 Oracle ASM」可能會有較佳的讀取作業 IOPS 效能。需要以 Windows Server 2012 作業系統為基礎的映像。如需詳細資訊，請參閱[在獨立伺服器上部署儲存空間](http://technet.microsoft.com/library/jj822938.aspx)。在這種排列方式下，兩個相等之連接的磁碟子集，會先在兩個儲存體集區磁碟區中一併視為實體磁碟進行等量，然後磁碟區會加入到 ASM 磁碟群組。下圖以邏輯方式說明這種排列方式。

	![](media/virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images/image3.png)

>[AZURE.IMPORTANT]依個別情況評估寫入效能與讀取效能之間的取捨。使用這些方法的實際結果可能會有所不同。

### 高可用性和災害復原考量

在 Azure 虛擬機器中使用 Oracle 資料庫時，您必須負責實作高可用性和災害復原解決方案，以避免任何停機。您也需負責備份自己的資料和應用程式。

Azure 上的 Oracle Database Enterprise Edition (不含 RAC) 高可用性和災害復原可以使用 [Data Guard、Active Data Guard](http://www.oracle.com/technetwork/articles/oem/dataguardoverview-083155.html) (英文) 或 [Oracle Golden Gate](http://www.oracle.com/technetwork/middleware/goldengate) (英文) 來達成，且兩個資料庫位在不同的虛擬機器 (VM)。這兩個虛擬機器都應該在相同的[雲端服務](cloud-services-connect-virtual-machine.md)和[虛擬網路](http://azure.microsoft.com/documentation/services/virtual-network/)，以確保它們可以透過永續私人 IP 位址互相存取。此外，建議您將 VM 放在相同的[可用性設定組](manage-availability-virtual-machines.md)中，讓 Azure 將其放置於不同的容錯網域和升級網域。請注意，只有相同雲端服務內的 VM 可以參與相同的可用性設定組。每個 VM 必須至少有 2 GB 的記憶體和 5 GB 的磁碟空間。

使用 Oracle Data Guard，可以藉由某個 VM 中的主要資料庫、另一個 VM 中的次要 (待命) 資料庫以及它們之間的單向複寫設定，達到高可用性。這樣讀取作業存取的會是資料庫的複本。使用 Oracle GoldenGate，您則可以設定兩個資料庫之間的雙向複寫。若要了解如何使用這些工具為資料庫設定高可用性解決方案，請參閱 Oracle 網站上的 [Active Data Guard](http://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) 和 [GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html) 文件 (英文)。如需資料庫複本的讀取-寫入存取權，您可以使用 [OracleActive Data Guard](http://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html)。

##Oracle WebLogic Server 虛擬機器映像

-  **只有 Enterprise Edition 支援叢集。** 如果您使用 Microsoft 授權的 WebLogic Server 映像 (尤其作業系統是 Windows Server)，則只在使用 Enterprise Edition 的 WebLogic Server 時，您才有權使用 WebLogic 叢集。請勿在使用 WebLogic Server Standard Edition 時使用叢集。

-  **連線逾時：**如果您的應用程式依賴另一個 Azure 雲端服務 (例如，資料庫層服務) 的公用端點連線，請注意 Azure 會在無活動達 4 分鐘之後關閉這些開放連線。這可能會影響依賴連接集區的功能和應用程式，因為無活動時間超過該限制的連接將不再有效。如果這樣會影響您的應用程式，請考慮在您的連接集區上啟用「Keep-Alive」邏輯。

	請注意，如果端點是 Azure 雲端服務部署的「內部」端點 (例如位於與您的 WebLogic 虛擬機器「相同」雲端服務內的獨立資料庫虛擬機器)，則連接是直接的而不需依賴 Azure 負載平衡器，因此不受限於連接逾時。

-  **不支援 UDP 多點傳送。** Azure 支援 UDP 單點傳播，但不支援多點傳送或廣播。WebLogic Server 可以依賴 Azure 的 UDP 單點傳播功能。若要依賴 UDP 單點傳播獲得最佳結果，建議 WebLogic 叢集大小保持靜態，或叢集中保持不超過 10 個受管理伺服器。

-  **WebLogic Server 預期針對 T3 存取的公用和私人連接埠是相同的 (例如，使用 Enterprise JavaBeans 時)。** 考慮一多層式案例，其中服務層 (EJB) 應用程式是在 WebLogic Server 叢集上執行，該叢集在名為 **SLWLS** 的雲端服務包含兩個或多個受管理伺服器。用戶端層是在不同的雲端服務中，執行簡單的 Java 程式，嘗試呼叫服務層中的 EJB。由於負載平衡服務層是必要動作，所以必須先為 WebLogic Server 叢集中的虛擬機器建立公用負載平衡端點。如果您為該端點指定的私人連接埠與公用連接埠不同 (例如，7006:7008)，則會發生下列錯誤：

		[java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

		Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

	這是因為對於任何遠端 T3 存取，WebLogic Server 預期負載平衡器連接埠和 WebLogic 受管理伺服器連接埠是相同的。在上述案例中，用戶端正在存取連接埠 7006 (負載平衡器連接埠)，而受管理伺服器正在接聽 7008 (私人連接埠)。請注意，這項限制只適用於 T3 存取，不適用於 HTTP。

	若要避免此問題，請使用下列其中一種因應措施：

	-  針對 T3 存取專用的負載平衡端點使用相同的私人和公用連接埠號碼。

	-  啟動 WebLogic Server 時包含下列 JVM 參數：

			-Dweblogic.rjvm.enableprotocolswitch=true

如需相關資訊，請參閱知識庫文章 **860340.1**，位於：<http://support.oracle.com>。

-  **動態叢集和負載平衡限制。** 假設您想要在 WebLogic Server 中使用動態叢集，並透過 Azure 中的單一、公用負載平衡端點將它公開。做法是只要您使用各個受管理伺服器的固定連接埠號碼 (不是從範圍動態指派)，且不要啟動比系統管理員正在追蹤之機器的數量還多的受管理伺服器 (也就是每台虛擬機器不超過一個受管理伺服器)。如果您的設定會導致啟動比 VM 更多的 WebLogic Server (亦即，多個 WebLogic Server 執行個體共用相同的 VM)，將無法讓一個以上的 WebLogic Server 執行個體伺服器繫結至指定的連接埠號碼 – 該 VM 上的其他執行個體將會失敗。 

	另一方面，如果您設定管理伺服器自動將唯一的連接埠號碼指派給受管理伺服器，則無法進行負載平衡，因為 Azure 不支援從單一公用連接埠對應至多個私人連接埠，而這是此組態的必要設定。

-  **單一 VM 上的多個 WebLogic 執行個體。** 根據您的部署需求，如果 VM 夠大，您可以考慮在相同虛擬機器上執行多個 WebLogic Server 的執行個體。例如，在中型大小 VM 上 (包含 2 個核心)，您可以選擇執行兩個 WebLogic Server 的執行個體。不過請注意，仍然建議您避免將單一失敗點引入您的架構，如果您僅使用一個執行多個 WebLogic Server 執行個體的 VM 也是如此。使用至少兩個 VM 可能是較好的方法，每個 VM 可以執行多個 WebLogic Server 執行個體。每個 WebLogic Server 執行個體仍可以是相同叢集的一部分。但是請注意，目前無法使用 Azure 對相同 VM 內此類 WebLogic Server 部署所公開的端點進行負載平衡，因為 Azure 負載平衡器需要負載平衡伺服器在唯一的 VM 內散佈。

##Oracle JDK 虛擬機器映像

-  **JDK 6 和 7 最新更新。** 雖然建議使用 Java 的最新公開支援版本 (目前為 Java 8)，Azure 也提供 JDK 6 和 7 映像。這是針對尚未準備升級至 JDK 8 的舊版應用程式。雖然舊版 JDK 的更新已不再提供一般大眾使用，憑藉 Microsoft 與 Oracle 的合作夥伴關係，Azure 提供的 JDK 6 和 7 映像會包含最新的非公用更新，該更新通常是由 Oracle 僅提供給 Oracle 支援客戶的選取群組。新版本的 JDK 映像與 JDK 6 和 7 的更新版本會在一段時間內供取用。

	請注意，此 JDK 6 和 7 映像中可用的 JDK，以及從中衍生的虛擬機器和映像，只能在 Azure 內使用。

-  **64 位元 JDK。** Oracle WebLogic Server 虛擬機器映像和 Azure 所提供的 Oracle JDK 虛擬機器映像包含 Windows Server 和 JDK 的 64 位元版本。

##其他資源
[Azure 的 Oracle 虛擬機器影像](virtual-machines-oracle-list-oracle-virtual-machine-images.md)

<!---HONumber=July15_HO2-->