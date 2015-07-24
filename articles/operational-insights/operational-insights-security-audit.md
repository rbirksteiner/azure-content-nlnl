<properties
   pageTitle="探索 Operational Insights 安全性和稽核資料"
   description="了解如何使用「安全性和稽核」解決方案，針對值得您注意的問題使用內建的搜尋查詢，對您組織的 IT 安全性狀況獲得全面性的檢視"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/02/2015"
   ms.author="banders" />

# 探索 Operational Insights 安全性和稽核資料

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

「Operational Insights 安全性和稽核」解決方案針對值得您注意的問題，使用內建的搜尋查詢，為您組織的 IT 安全性狀況提供全面性的檢視。

在本文中，您將：

- 對可疑的可執行檔執行簡單的調查
- 了解取證分析的最佳作法
- 了解安全性缺口模式調查的最佳作法
- 了解稽核案例的最佳作法

## 對可疑的可執行檔執行簡單的調查

1. 登入 Operational Insights。
2. 在 [概觀] 頁面上，複查 [安全性和稽核] 磚中所顯示的資訊，然後按一下資訊。![[概觀] 頁面的影像](./media/operational-insights-security-audit/sec-audit-dash02.png)
3. 在 [安全性和稽核] 頁面上，複查 [值得注意的問題] 刀鋒視窗中的資訊。在範例影像中，您會看到今天有 6 個，而昨天有 2 個值得注意的問題。在此範例中，有 1 個可疑的可執行檔。在 [值得注意的問題] 刀鋒視窗中按一下 [可疑的可執行檔]。![[安全性和稽核] 頁面的影像](./media/operational-insights-security-audit/sec-audit-dash03.png)
4. 搜尋會針對您按一下的可疑可執行檔顯示查詢和結果。在範例中，有 1 個結果，並顯示其檔案雜湊。按一下 **FILEHASH** 識別碼。![搜尋結果 filehash 的影像](./media/operational-insights-security-audit/sec-audit-search01.png)
5. 搜尋會顯示有關可執行檔的額外資訊，包括其檔案路徑和處理序名稱。按一下**處理序 &lt;檔案名稱&gt;**。在範例中，這是 HEXEDIT.EXE。![搜尋結果處理序的影像](./media/operational-insights-security-audit/sec-audit-search02.png)
6. 搜尋會將加上引號的處理序名稱附加到查詢中。在此範例中為 "**HEXEDIT.EXE"**。![搜尋查詢的影像](./media/operational-insights-security-audit/sec-audit-search03.png)
7. 在 [搜尋] 查詢方塊中，刪除處理序名稱和引號以外的所有內容，然後按一下搜尋圖示。![詳細搜尋資訊的影像](./media/operational-insights-security-audit/sec-audit-search04.png)
8. 搜尋會顯示有關處理序的詳細資訊，包括處理序執行所在的電腦、執行處理序的使用者帳戶，以及針對處理序所建立之事件的日期和時間。
9. 利用您找到的資訊，可以視需要採取修正的動作。例如，如果您判斷某個可執行檔是惡意程式碼，您可能會想要從所有它影響的電腦系統中採取動作將它移除。移除該可執行檔且 Operational Insights 收到電腦系統的更新日誌和稽核事件之後，[值得注意的問題] 刀鋒視窗中的值就會在隔天變更。

## 取證分析的最佳作法

**尋找證據**

當您使用「安全性和稽核」解決方案執行取證分析時，您就是在尋找潛在惡意使用者所留下的證據。無論使用者在其 IT 環境中執行什麼動作，他們所參與的許多活動都會產生安全性構件。您必須注意，日誌經常會被攻擊者清除，而且這通常是為了隱藏其蹤跡所採取的第一步。

不過，無論使用者是存取自己的電腦或存取遠端的電腦，都會在事件日誌中儲存使用證據。Operational Insights 會在構件*發生時立即*收集這些構件，防止被任何人竄改，因此允許您將多台電腦之間的資料進行關聯，執行不同類型的分析。

**了解如何設定和收集稽核事件**

若要充分利用「安全性和稽核」解決方案，您必須使用下列資源，設定由 Windows 環境所收集、最符合您需求的事件層級。

- [如何設定安全性原則設定](https://technet.microsoft.com/library/dn135243(v=ws.10).aspx)

- [進階稽核原則組態](https://technet.microsoft.com/library/jj852202(v=ws.10).aspx)

- [稽核原則建議](https://technet.microsoft.com/library/dn487457.aspx)

**啟用 AppLocker**

您也應該啟用 AppLocker 事件，取得在 IT 環境中所發生之處理序可執行檔的相關豐富資訊。如需詳細資訊，請參閱[設定僅供稽核之用的 AppLocker 原則](https://technet.microsoft.com/library/hh994622.aspx)

**設定 Windows 事件的稽核層級**

Windows 運算環境可讓您設定安全性相關記錄的擷取層級。例如，您可以設定環境，任何時候只要有人存取檔案、讀取檔案或開啟檔案時，就會產生事件。您想要收集的詳細資料層級根據您的需求而定。不過，您啟用的每個選項都會需要某種程度的成本，因為您必須儲存您收集的所有資訊。因此，在許多組織的 IT 環境中，人們會決定不要啟用物件讀取或物件寫入的資料收集。每次一有人存取任何檔案，就會產生成千上萬的事件，其中一些只是無用的訊息。您決定要收集的層級則根據您最佳的判斷。

>[AZURE.NOTE]如果您使用直接代理程式，而且組織中有 Proxy 伺服器，您就應該設定 Proxy 伺服器允許代理程式存取 Operational Insights。如需詳細資訊，請參閱[設定 Proxy 和防火牆設定](operational-insights-proxy-firewall.md)。

## 安全性缺口模式調查的最佳作法

**調查不正常的活動模式**

安全性缺口通常從合法憑證開始，然後惡意使用者嘗試透過攻擊來取得提升的權限。「安全性和稽核」解決方案不會著重在入侵偵測，而是透過「值得注意的問題」確實協助您調查和探索不正常活動的模式。例如，您應該調查下列不正常的活動，以及在 [值得注意的問題] 下方出現的任何其他活動。

- 某位使用者在不常使用的電腦上有不尋常的登入

- 來自異常使用者或電腦有不尋常的網路列舉

- 使用管理權限建立新的使用者帳戶

- 日誌記錄或安全性原則有變更

- 可疑的可執行檔

## 稽核案例的最佳作法

您的組織可能會有一些必須遵守的電腦和網路法規原則和規章，需要大量稽核記錄。例如，如果您的組織是一家財務公司，您可能必須保留在任何特定時間點，能證明使用者在網路上執行特定操作的記錄。您可能也必須在需要時以及回溯幾個月前 (有時甚至多年前)，產生特定使用者或選定伺服器之詳細活動的報表。

此時，無論您的電腦是內部部署或位於雲端，您都可以使用「安全性和稽核」解決方案來收集您整個 IT 環境的稽核資料。所有稽核資料都會根據每個訂用帳戶加以儲存、編製索引，以及保留。如果對 Operational Insights 使用「進階」訂用帳戶，無限數量的資料都能儲存一年。然後您可以檢視稽核資料、執行搜尋，以及在不同的資料類型和電腦之間進行關聯，取得自收集資料以來任何時間區間的綜合性結果。

**使用群組原則收集稽核資料**

您想要收集並傳送至 Operational Insights 的任何稽核資料，都會使用群組原則完整進行管理。使用群組原則將安全性組態定義為「群組原則物件」(GPO) 的一部分，這些組態會連結到 Active Directory 容器 (例如站台、網域，以及組織單位)，而這些容器會協助您管理安全性設定。原則資料會進行記錄，然後傳送至 Operational Insights 服務。

**使用 AppLocker 收集稽核資料**

除了本機原則設定之外，如果您使用 AppLocker 收集稽核資料，Operational Insights 將會收集這些資料，然後您就可以加以檢視。

<!---HONumber=July15_HO2-->