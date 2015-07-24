<properties 
	pageTitle="在 BizTalk 服務中建立和還原備份 |Azure" 
	description="BizTalk 服務包含備份與還原功能。了解如何建立和還原備份，以及判斷該備份什麼。MABS，WABS" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/24/2015" 
	ms.author="mandia"/>


# BizTalk 服務：備份與還原

Azure BizTalk 服務包含備份與還原功能。本主題說明如何使用 Azure 管理入口網站來備份和還原 BizTalk 服務。

您也可以使用 [BizTalk 服務 REST API](http://go.microsoft.com/fwlink/p/?LinkID=325584) 來備份 BizTalk 服務。

## 開始之前

- 備份與還原可能不適用於部分版本。請參閱「[BizTalk 服務：版本圖表](biztalk-editions-feature-chart.md)」(英文)。

	**注意事項**：混合式連線無法備份，與版本無關。

- 使用 Azure 管理入口網站可建立隨選備份或排定備份。

- 備份內容可以還原至相同的 BizTalk 服務，或還原至新的 BizTalk 服務。若要使用相同名稱來還原 BizTalk 服務，必須刪除現有的 BizTalk 服務，且名稱必須可用。刪除 BizTalk 服務之後，可能需要較長的時間，才能使用相同的名稱。如果無法等待相同的名稱可用，請還原至新的 BizTalk 服務。

- BizTalk 服務可以還原至相同版本或更高的版本。從建立備份之後，不支援將 BizTalk 服務還原至較低版本。

	例如，使用基本版本的備份可以還原至高級版本。使用高級版本的備份不能還原至標準版本。

- EDI 控制編號會備份以維持控制編號的連貫性。如果在上次備份之後處理訊息，則還原此備份內容會產生重複的控制編號。

- 如果批次中有作用中的訊息，請在執行備份前**先**處理批次。無論是建立隨選備份或排定備份，都不會儲存批次中的訊息。

	**建立備份時，如果批次中有作用中的訊息，則不會備份這些訊息，且這些訊息將會遺失。**

- 選用：在 BizTalk 服務入口網站中，停止任何管理作業。


## 建立備份

您隨時都可以建立備份，完全決由掌控。本節列出使用 Azure 管理入口網站建立備份的步驟，內容包括：

[隨選備份](#backupnow)

[排定備份](#backupschedule)

#### <a name="backupnow"></a>隨選備份
1. 在 Azure 管理入口網站上，選取 [**BizTalk 服務**]，然後選取要備份的 BizTalk 服務。
2. 在 [**儀表板**] 索引標籤中，選取頁面底部的 [**備份**]。
3. 輸入備份名稱。例如，輸入 *myBizTalkService*BU*Date*。
4. 選擇 Blob 儲存體帳戶，然後選取勾選記號開始備份。

備份完成時，儲存體帳戶內會以您輸入的備份名稱建立一個容器。此容器包含 BizTalk 服務備份組態。

#### <a name="backupschedule"></a>排定備份

1. 在 Azure 管理入口網站上，選取 [**BizTalk 服務**]，選取您要排定備份的 BizTalk 服務名稱，然後選取 [**設定**] 索引標籤。
2. 將 [**備份狀態**] 設為 [**自動**]。 
3. 選取要儲存備份的 [**儲存體帳戶**]，輸入建立備份的 [**頻率**]，以及備份的保留時間 (**保留天數**)：

	![][AutomaticBU]

	**注意** - 在 [**保留天數**] 中，保留期間必須大於備份頻率。-選取 [**永遠保留至少一個備份**]，即使它已超出保留期間。
	

4. 選取 [**儲存**]。


排定的備份工作執行時，會在您輸入的儲存體帳戶中建立容器 (以儲存備份資料)。容器名稱的命名方式為 *BizTalk Service Name-date-time*。

如果 BizTalk 服務儀表板顯示 [**失敗**] 狀態：

![上次排定的備份狀態][BackupStatus]

該連結可開啟 [管理服務作業記錄] 以協助進行疑難排解。請參閱 [BizTalk 服務：使用作業記錄進行疑難排解](http://go.microsoft.com/fwlink/p/?LinkId=391211)。

## 還原

您可以從 Azure 管理入口網站或從[還原 BizTalk 服務 REST API](http://go.microsoft.com/fwlink/p/?LinkID=325582) 來還原備份。本節列出使用管理入口網站進行還原的步驟。

#### 還原備份之前

- 還原 BizTalk 服務時可以輸入新的追蹤、封存和監視儲存區。

- 將還原相同的 EDI Runtime 資料。EDI Runtime 備份中儲存控制編號。還原的控制編號從備份時間開始按順序編排。如果在上次備份之後處理訊息，則還原此備份內容會產生重複的控制編號。

#### Restore a backup

1. 在 Azure 管理入口網站上，選取 [**新增**] > [**應用程式服務**] > [**BizTalk 服務**] > [**還原**]：

	![還原備份][Restore]

2. 在 [**備份 URL**] 中，選取資料夾圖示並展開儲存 BizTalk 服務設定備份的 Azure 儲存體帳戶。展開容器，然後在右窗格中，選取對應的 .txt 備份檔案。<br/><br/>選取 [**開啟**]。

3. 在 [**還原 BizTalk 服務**] 頁面上，輸入 [**BizTalk 服務名稱**] 並驗證要還原的 BizTalk 服務的 [**網域 URL**]、[**版本**] 和 [**區域**]。為追蹤資料庫 [**建立新的 SQL 資料庫執行個體**]：

	![][RestoreBizTalkService]

	選取下一個箭頭。

4. 	驗證 SQL 資料庫的名稱，輸入將建立 SQL 資料庫的實體伺服器，以及該伺服器的使用者名稱/密碼。


	如果您要設定 SQL 資料庫版本、大小和其他屬性，請選取 [設定進階資料庫設定]。

	選取下一個箭頭。

5. 為 BizTalk 服務建立新的儲存體帳戶或輸入現有的儲存體帳戶。

7. 選取勾選記號以啟動還原。

順利完成還原時，在 Azure 管理入口網站的 BizTalk 伺服器頁面上，新的 BizTalk 服務將以暫止狀態列出。



### <a name="postrestore"></a>還原備份之後

BizTalk 服務永遠還原成**暫止**狀態。在此狀態下，您可以在新環境開始運作前進行任何設定變更，包括：

- 若您使用 Azure BizTalk 服務 SDK 來建立 BizTalk 服務應用程式，您可能需要在這些應用程式中更新存取控制 (ACS) 認證，才能使用還原後的環境。

- 您可以還原 BizTalk 服務來複寫現有的 BizTalk 服務環境。在此情況下，如果在原始 BizTalk 服務入口網站中有設定的協議使用 FTP 來源資料夾，則您可能需要在剛還原的環境中更新協議，以使用其他 FTP 來源資料夾。否則，可能會有兩個不同的協議都嘗試提取相同的訊息。

- 如果您已進行還原而產生多個 BizTalk 服務環境，則在使用 Visual Studio 應用程式、PowerShell Cmdlet、REST API 或交易夥伴管理 OM API 時，請確定目標環境正確。

- 在剛還原的 BizTalk 服務環境上，建議設定自動備份。

若要在 Azure 管理入口網站上啟動 BizTalk 服務，請選取已還原的 BizTalk 服務，然後在工作列中選取 [**繼續**]。



## 備份什麼項目

建立備份時會備份下列項目：

<table border="1"> 
<tr bgcolor="FAF9F9">
<th> </th>
<TH>備份的項目</TH> 
</tr> 
<tr>
<td colspan="2">
 <strong>Azure BizTalk 服務入口網站</strong></td>
</tr> 
<tr>
<td>組態和執行階段</td> 
<td>
<ul>
<li>夥伴和設定檔詳細資料</li>
<li>夥伴協議</li>
<li>已部署的自訂組件</li>
<li>已部署的橋接器</li>
<li>憑證</li>
<li>已部署的轉換</li>
<li>管線</li>
<li>BizTalk 服務入口網站中建立和儲存的範本</li>
<li>X12 ST01 和 GS01 對應</li>
<li>控制編號 (EDI)</li>
<li>AS2 訊息 MIC 值</li>
</ul>
</td>
</tr> 
 
<tr>
<td colspan="2">
 <strong>Azure BizTalk 服務</strong></td>
</tr> 
<tr>
<td>SSL 憑證</td> 
<td>
<ul>
<li>SSL 憑證資料</li>
<li>SSL 憑證密碼</li>
</ul>
</td>
</tr> 
<tr>
<td>BizTalk 服務設定</td> 
<td>
<ul>
<li>調整單位計數</li>
<li>版本</li>
<li>產品版本</li>
<li>區域/資料中心</li>
<li>存取控制服務 (ACS) 命名空間和金鑰</li>
<li>追蹤資料庫連接字串</li>
<li>封存儲存體帳戶連接字串</li>
<li>監視儲存體帳戶連接字串</li>
</ul>
</td>
</tr> 
<tr>
<td colspan="2">
 <strong>其他項目</strong></td>
</tr> 
<tr>
<td>追蹤資料庫</td> 
<td>建立 BizTalk 服務時需要輸入追蹤資料庫詳細資料，包括 Azure SQL Database 伺服器和追蹤資料庫名稱。不會自動備份追蹤資料庫。
<br/><br/>
<strong>重要事項</strong><br/>
如果刪除了追蹤資料庫，且需要復原資料庫，先前的備份必須存在。如果備份不存在，則無法復原追蹤資料庫及其資料。在此情況下，請以相同的資料庫名稱建立新的追蹤資料庫。建議採用地理複寫。</td>
</tr> 
</table>

## 下一步

若要在 Azure 管理入口網站中建立 Azure BizTalk 服務，請移至 [BizTalk 服務：使用 Azure 管理入口網站進行佈建](http://go.microsoft.com/fwlink/p/?LinkID=302280)。若要開始建立應用程式，請移至 [Azure BizTalk 服務](http://go.microsoft.com/fwlink/p/?LinkID=235197)。

## 另請參閱
- [備份 BizTalk 服務](http://go.microsoft.com/fwlink/p/?LinkID=325584)
- [從備份還原 BizTalk 服務](http://go.microsoft.com/fwlink/p/?LinkID=325582)
- [BizTalk 服務：開發人員、基本、標準和高級版本圖表](http://go.microsoft.com/fwlink/p/?LinkID=302279)
- [BizTalk 服務：使用 Azure 管理入口網站進行佈建](http://go.microsoft.com/fwlink/p/?LinkID=302280)
- [BizTalk 服務：佈建狀態圖](http://go.microsoft.com/fwlink/p/?LinkID=329870)
- [BizTalk 服務：儀表板、監視和調整索引標籤](http://go.microsoft.com/fwlink/p/?LinkID=302281)
- [BizTalk 服務：節流](http://go.microsoft.com/fwlink/p/?LinkID=302282)
- [BizTalk 服務：簽發者名稱和簽發者金鑰](http://go.microsoft.com/fwlink/p/?LinkID=303941)
- [如何開始使用 Azure BizTalk 服務 SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[BackupStatus]: ./media/biztalk-backup-restore/status-last-backup.png
[Restore]: ./media/biztalk-backup-restore/restore-ui.png
[AutomaticBU]: ./media/biztalk-backup-restore/AutomaticBU.png
[RestoreBizTalkService]: ./media/biztalk-backup-restore/RestoreBizTalkServiceWindow.png
 

<!---HONumber=62-->