<properties
	pageTitle="Hyper-V 複寫的容量規劃"
	description="本文章包含有關如何使用 Azure Site Recovery 的容量規劃工具的指示，並且包含 Hyper-V 複寫的容量規劃的資源"
	services="site-recovery"
	documentationCenter="na"
	authors="csilauraa"
	manager="jwhit"
	editor="tysonn" />
<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="06/01/2015"
	ms.author="lauraa" />

# Hyper-V 複寫的容量規劃

Azure Site Recovery 會在兩個內部部署網站之間或內部部署 VMM 伺服器與 Azure 儲存體之間使用 Hyper-V 複本進行複寫。本文章包含有關使用適用於 Azure Site Recovery (ASR) 的容量規劃工具 - Hyper-V 複本工具的逐步指示。ASR 的容量規劃工具 - Hyper-V 複本工具會引導 IT 系統管理員設計伺服器、儲存體和成功部署 Hyper-V 複本所需的網路基礎結構，並且驗證兩個網站之間的網路連線。

## 系統需求
- 作業系統：Windows Server® 2012 或 Windows Server® 2012 R2
- 記憶體：20 MB (最低要求)
- CPU：5% 額外負荷 (最低要求)
- 磁碟空間：5 MB (最低要求)

## 教學課程步驟
- 步驟 1：準備主要站台
- 步驟 2：若您的復原站台是內部部署，準備復原站台
- 步驟 3：執行容量規劃工具
- 步驟 4：解譯結果

## 步驟 1：準備主要站台
1. 建立需要啟用複寫與對應主要 Hyper-V 主機/叢集的所有 Hyper-V 虛擬機器清單。
2. 分組主要 Hyper-V 主機和叢集到下列其中一項：

  - Windows Server ® 2012 獨立伺服器
  - Windows Server ® 2012 叢集
  - Windows Server ® 2012 R2 獨立伺服器
  - Windows Server ® 2012 R2 叢集

3. 您必須對每個獨立伺服器群組和每個叢集執行一次容量規劃工具。
4. 在所有主要主機和叢集上啟用 WMI 遠端存取。確定已正確設定防火牆規則和使用者權限。

        netsh firewall set service RemoteAdmin enable

5. 在主要主機上啟用效能監視。

  - 開啟 [**Windows 防火牆**] 與 [**進階安全性**] 嵌入式管理單元，然後啟用下列輸入規則：
    - COM+ Network Access (DCOM-IN)
    - 遠端事件記錄檔管理群組中的所有規則

## 步驟 2：準備復原站台
如果您使用 Azure 做為復原站台，或尚未準備好您的內部部署復原站台，則您可以略過本節。但是如果您略過它，您無法測量兩個站台之間可用的頻寬。

1. 識別驗證方法

	a.Kerberos：用於當主要和復原 Hyper-V 主機都在相同網域或位於互相信任的網域。

	b.憑證：用於當主要和復原 Hyper-V 主機位於不同網域。可以使用 makecert 建立憑證。如需使用這項技術部署憑證所需之步驟的相關資訊，請參閱 [Hyper-V 複本憑證型驗證 - makecert](http://blogs.technet.com/b/virtualization/archive/2013/04/13/hyper-v-replica-certificate-based-authentication-makecert.aspx) 部落格文章。

2. 識別復原站台的**單一**復原 Hyper-V 主機/叢集。

	a.此復原主機/叢集將用於複寫虛擬的虛擬機器，以及估計主要和次要站台之間可用的頻寬。

	b.**建議**：使用單一復原 Hyper-V 主機以執行測試。

### 準備一部 Hyper-V 主機做為復原伺服器
1. 在 [Hyper-V 管理員]中，按一下 [**動作**] 窗格中的 [**Hyper-V 設定**]。
2. 在 [**Hyper-V 設定**] 對話方塊中，按一下 [**複寫組態**]。
3. 在 [詳細資料] 窗格中，選取 [**啟用這台電腦做為複本伺服器**]。
4. 在 [**驗證和連接埠**] 區段中，選取您先前選擇的驗證方法。對於驗證方法，指定要使用的連接埠 (透過 HTTP 之 kerberos 的預設連接埠是 80，透過 HTTPS 的憑證型驗證是 443)。
5. 如果您使用憑證型驗證，按一下 [**選取憑證**]，然後提供憑證資訊。
6. 在 [**授權與存放裝置**] 區段中，指定以允許**任何**驗證 (主要) 伺服器將複寫資料傳送到這個複本伺服器。
7. 按一下 [**確定**] 或 [**套用**]。

![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image1.png)

8. 藉由執行下列命令，驗證 https 接聽程式是否正在執行：netsh http show servicestate
9. 開啟防火牆連接埠：

        Port 443 (certificae-based authentication):
          Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"


        Port 80 (Kerberos):
          Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"

### 準備一個 Hyper-V 叢集做為復原目標
如果您已經準備好獨立 Hyper-V 主機復原伺服器，則您可以略過本節。

1. 設定 Hyper-V 複本代理人：

	a.在 [**伺服器管理員**] 中，開啟 [**容錯移轉叢集管理員**]。

	b.在左窗格中，連接到叢集，並在叢集名稱反白顯示時，按一下 [**動作**] 窗格中的 [**設定角色**]。這會開啟 [**高可用性精靈**]。

	c.在 [**選取角色**] 畫面上，選取 [**Hyper-V 複本代理人**]。

	d.完成精靈，提供 [**NetBIOS 名稱**] 和 [**IP 位址**] 做為叢集的連接點 (稱為用戶端存取點)。[**Hyper-V 複本代理人**] 已設定，會產生用戶端存取點名稱。請記下用戶端存取點名稱 - 稍後您將會用它來設定複本。

	e.確認 Hyper-V 複本代理人角色順利連線，而且可以進行叢集所有節點之間的容錯移轉。若要這樣做，請以滑鼠右鍵按一下角色，指向 [**移動**]，然後按一下 [**選取節點**]。選取節點 > [**確定**]。

	f.如果您使用憑證型驗證，請確定每個叢集節點與 Hyper-V 複本代理人的用戶端存取點都有安裝憑證。

2. 設定複本設定：

	a.在 [**伺服器管理員**] 中，開啟 [**容錯移轉叢集管理員**]。
	
	b.在左窗格中，連接到叢集，並在叢集名稱反白顯示時，按一下 [**詳細資料**] 窗格之 [**瀏覽**] 類別中的 [**角色**]。
	
	c.以滑鼠右鍵按一下角色，然後選擇 [**複寫設定**]。
	
	d.在 [**詳細資料**] 窗格中，選取 [**啟用此叢集做為複本伺服器**]。

	e.在 [**驗證和連接埠**] 區段中，選取您先前選擇的驗證方法。對於驗證方法，指定要使用的連接埠 (透過 HTTP 之 Kerberos 的預設連接埠是 80，透過 HTTPS 的憑證型驗證是 443)。

	f.如果您使用憑證型驗證，按一下 [**選取憑證**]，然後提供要求的憑證資訊。

	g.在 [**授權與存放裝置**] 區段中，指定以允許**任何**驗證 (主要) 伺服器將複寫資料傳送到這個複本伺服器，或限制接受來自特定主要伺服器的資料。您可以使用萬用字元以限制接受來自特定網域的伺服器，而不必個別指定 (例如，*.contoso.com)。

	h.在所有復原 Hyper-V 主機上開啟防火牆連接埠：連接埠 443 (憑證驗證)：Get-ClusterNode | ForEach-Object {Invoke-command -computername \\$_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"}}


          Port 80 (Kerberos auth):
              Get-ClusterNode | ForEach-Object {Invoke-command -computername \$_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"}}


## 步驟 3：執行容量規劃工具
1. 下載容量規劃工具。
2. 從其中一個主要伺服器 (或主要叢集的其中一個節點) 執行工具。以滑鼠右鍵按一下 .exe 檔案，然後選擇 [ **以系統管理員身分執行**]。
3. 如果您選擇這麼做，請接受 [**授權條款**]，然後按 [**下一步**]。
4. 選取 [**度量集合的持續時間**]。強烈建議在實際執行期間執行工具，以確保會收集最具代表性的資料。度量收集的建議持續時間為 30 分鐘。如果您只想要驗證網路連線，您可以選擇一分鐘做為持續時間。

![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image2.png)

5. 指定 [**主要站台詳細資料**]，如下所示，然後按 [**下一步**]。

	對於獨立主機，輸入伺服器名稱或 FQDN。

	如果您的主要主機是叢集的一部分，您可以輸入下列其中一項的 FQDN：

	a.Hyper-V 複本代理人用戶端存取點 (CAP)

	b.叢集名稱

	c.叢集中的任何節點

  ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image3.png)


6. 輸入 [**複本站台詳細資料**] (僅適用於內部部署站台到內部部署站台複寫)

  如果您想要啟用 Azure 的複寫或您尚未準備 Hyper-V 主機或叢集做為復原伺服器 (如步驟 2 所述)，您應該略過複本站台的相關測試。

  指定 [**複本站台**] 詳細資料，然後按 [**下一步**]。

i.對於獨立主機，輸入伺服器名稱或 FQDN

ii.如果您的主要主機是叢集的一部分，您可以輸入下列其中一項的 FQDN：

a.Hyper-V 複本代理人用戶端存取點 (CAP)

b.叢集名稱

c.叢集中的任何節點

   ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image4.png)

7. 略過 [**擴充複本站台**] 的相關測試。它們不受 ASR 支援。
8. 將虛擬機器選取至設定檔。此工具會連接到 [**主要站台詳細資料**] 中指定的叢集或獨立伺服器，並列舉執行的虛擬機器。對於您想要收集的度量選取虛擬機器和虛擬磁碟。

下列虛擬機器將不會列舉或顯示：

- 已針對複寫啟用的虛擬機器
- 未執行的虛擬機器

9. 輸入 [**網路資訊**] (這只適用於內部部署站台到內部部署站台複寫，和提供複本站台詳細資料時)。

指定要求的網路資訊，然後按 [**下一步**]。

- 估計的 WAN 頻寬
- 要用於驗證的憑證 (選擇性)：如果您打算使用憑證型驗證，您應該在這個頁面提供所需的憑證。

   ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image5.png)

10. 在下一組畫面上，按 [**下一步**] 以啟動工具。

![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image6.png)

11. 此工具完成執行時，請按一下 [**檢視報告**] 以檢閱輸出。

    預設報告位置：

    %systemdrive%\\Users\\Public\\Documents\\Capacity Planner

    記錄檔位置：

    %systemdrive%\\Users\\Public\\Documents\\CapacityPlanner

## 步驟 4：解譯結果
因為它們與此案例不相關，所以您可以忽略未列在下列兩個案例中任一案例底下的度量。

### 內部部署站台到內部部署站台複寫
  - 主要主機計算、記憶體上複寫的影響
  - 主要、復原主機儲存體磁碟空間、IOPS 上複寫的影響
  - 差異複寫所需的總頻寬 (Mbps)
  - 主要主機和復原主機之間觀察到的網路頻寬 (Mbps)
  - 兩個主機/叢集之間作用中平行傳輸理想數目的建議

### 內部部署站台到 Azure 複寫
  - 主要主機計算、記憶體上複寫的影響
  - 主要主機儲存體磁碟空間、IOPS 上複寫的影響
  - 差異複寫所需的總頻寬 (Mbps)

更詳細的指引，請參閱 [Hyper-V 複本的容量規劃工具下載頁面](http://go.microsoft.com/?linkid=9876170)。

## 其他資源
下列資源可協助您進行 Hyper-V 複寫的容量規劃：

- [更新：Hyper-V 複本的容量規劃工具](http://go.microsoft.com/fwlink/?LinkId=510891) — 閱讀此部落格項目以了解這項新工具的概觀。

- [Hyper-V 複本的容量規劃工具](http://go.microsoft.com/fwlink/?LinkId=510892) — 下載此工具的最新版本。

- [引導式實際操作實驗室](http://go.microsoft.com/fwlink/?LinkId=510893) — 在 Keith Mayer 的 TechNet 部落格上取得容量規劃的絕佳逐步解說。

- [效能和調整測試 - 內部部署到內部部署](https://msdn.microsoft.com/library/azure/dn760892.aspx) — 讀取內部部署到內部部署複寫測試的結果。


## 後續步驟

若要開始部署 ASR：

- [設定內部部署 VMM 站台和 Azure 之間的保護](site-recovery-vmm-to-azure.md)
- [設定內部部署 Hyper-V 站台和 Azure 之間的保護](site-recovery-hyper-v-site-to-azure)
- [設定兩個內部部署 VMM 站台之間的保護](site-recovery-vmm-to-vmm)
- [利用 SAN 設定兩個內部部署 VMM 站台之間的保護](site-recovery-vmm-san)
- [利用單一 VMM 伺服器設定保護](site-recovery-single-vmm)
 

<!---HONumber=July15_HO2-->