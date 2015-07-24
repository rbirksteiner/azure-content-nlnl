<properties 
	pageTitle="如何搭配使用 AzCopy 與 Microsoft Azure 儲存體" 
	description="了解如何使用 AzCopy 公用程式來上傳、下載和複製 Blob 與檔案內容。" 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor="cgronlun"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/22/2015" 
	ms.author="tamram"/>

# 開始使用 AzCopy 命令列公用程式

## 概觀

AzCopy 是個命令列公用程式，專為高效能上傳、下載，以及將資料複製到和複製出 Microsoft Azure Blob、檔案和資料表儲存體所設計。本指南提供使用 AzCopy 的概觀。

> [AZURE.NOTE]本指南假設您已安裝 AzCopy 3.1.0 或更新版本。AzCopy 3.x 現已正式發行。
> 
> 本指南也說明如何使用 AzCopy 4.1.0，這是 AzCopy 的預覽版本。在本指南中，僅在預覽版本提供的功能皆稱為*預覽*。
> 
> 請注意，AzCopy 4.x 未來版本中的命令列選項及其功能有可能會有更動。

## 下載並安裝 AzCopy

1. 下載[最新版本的 AzCopy](http://aka.ms/downloadazcopy) 或[最新預覽版本](http://aka.ms/downloadazcopypr)。
2. 執行安裝。依預設，AzCopy 安裝會在 `%ProgramFiles(x86)%\Microsoft SDKs\Azure` (在執行 64 位元 Windows 的電腦上) 或 `%ProgramFiles%\Microsoft SDKs\Azure` (在執行 32 位元 Windows 的電腦上) 下建立一個名為 `AzCopy` 的資料夾。不過，您可以在安裝精靈中變更安裝路徑。
3. 若有需要，您可以在您的系統路徑中加入 AzCopy 安裝位置。

## 了解 AzCopy 命令列語法

接下來，開啟命令視窗，並瀏覽至電腦上的 AzCopy 安裝目錄，也就是 `AzCopy.exe` 可執行檔的位置。AzCopy 命令的基本語法是：

	AzCopy /Source:<source> /Dest:<destination> /Pattern:<filepattern> [Options]

> [AZURE.NOTE]自 AzCopy 3.0.0 版起，AzCopy 命令列語法規定每個指定的參數都必須包含參數名稱，*例如* `/ParameterName:ParameterValue`。

下表說明 AzCopy 的參數。您也可以從命令列中輸入下列其中一個命令，以取得 AzCopy 的使用說明：

- 如需 AzCopy 的詳細命令列說明：`AzCopy /?`
- 如需任何 AzCopy 參數的詳細說明：`AzCopy /?:SourceKey`
- 如需命令列範例：`AzCopy /?:Samples` 


<table>
  <tr>
    <th>選項名稱</th>
    <th>說明</th>
    <th>適用於 Blob 儲存體 (Y/N)</th>
    <th>適用於檔案儲存體 (Y/N) (僅限預覽版本)</th>
    <th>適用於資料表儲存體 (Y/N) (僅限預覽版本)</th>
  </tr>
  <tr>
    <td><b>/Source:&lt;source></b></td>
    <td>指定要複製的來源資料。來源可以是檔案系統目錄、Blob 容器、Blob 虛擬目錄、儲存體檔案共用、儲存體檔案目錄或 Azure 資料表。</td>
    <td>Y</td>
    <td>Y<br /> (僅限預覽)</td>
    <td>Y<br /> (僅限預覽)</td>
  </tr>
  <tr>
    <td><b>/Dest:&lt;destination></b></td>
    <td>指定複製的目的地。目的地可以是檔案系統目錄、Blob 容器、Blob 虛擬目錄、儲存體檔案共用、儲存體檔案目錄或 Azure 資料表。</td>
    <td>Y</td>
    <td>Y<br /> (僅限預覽)</td>
    <td>Y<br /> (僅限預覽)</td>
  </tr>
  <tr>
    <td><b>/Pattern:&lt;file-pattern></b></td>
      <td>
          指定一個檔案模式以指出所要複製的檔案。/Pattern 參數的行為取決於來源資料的位置以及是否有遞迴模式選項。遞迴模式可透過選項 /S 來指定。
          <br />
          如果指定來源是檔案系統中的目錄，則標準萬用字元便會立即生效，且所提供的檔案模式會與目錄中的檔案做比對。如果已指定選項 /S，則 AzCopy 也會將指定模式與該目錄下任何子資料夾中的所有檔案做比對。
          <br />
          如果指定來源是 Blob 容器或虛擬目錄，則萬用字元並不適用。如果已指定選項 /S，則 AzCopy 會將指定的檔案模式解譯為 Blob 首碼。如果未指定選項 /S，則 AzCopy 會將檔案模型與確切 Blob 名稱做比對。
          <br />
          如果指定來源是 Azure 檔案共用，則您必須指定確切檔案名稱 (例如 abc.txt) 以複製單一檔案，或指定選項 /S 以遞迴方式複製共用中的所有檔案。嘗試同時指定檔案模式和選項 /S，將會造成錯誤。
          <br />
          未指定檔案模式時所使用的預設檔案模式如下：若是檔案系統位置，則是 *.*，若是 Azure 儲存體位置，則是空白首碼。不支援指定多個檔案模式。</td>
    <td>Y</td>
    <td>Y<br /> (僅限預覽)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/DestKey:&lt;storage-key></b></td>
    <td>指定目的地資源的儲存體帳戶金鑰。</td>
    <td>Y</td>
    <td>Y<br /> (僅限預覽)</td>
    <td>Y<br /> (僅限預覽)</td>
  </tr>
  <tr>
    <td class="auto-style1"><b>/DestSAS:&lt;sas-token></b></td>
    <td class="auto-style1">指定目的地容器的共用存取簽章 (SAS) (如果適用的話)。為 SAS 加上雙引號，因為它可能包含特殊命令列字元。<br />
        如果目的地資源是 Blob 容器或資料表，您可以指定此選項後面接著 SAS 權杖，或者您可以不使用此選項，直接將 SAS 指定為目的地 Blob URI 的一部分。<br />
        如果來源和目的地都是 Blob，則目的地 Blob 必須與來源 Blob 位於相同的儲存體帳戶中。</td>
    <td class="auto-style1">Y</td>
    <td class="auto-style1">N</td>
    <td class="auto-style1">Y<br /> (僅限預覽)</td>
  </tr>
  <tr>
    <td><b>/SourceKey:&lt;storage-key></b></td>
    <td>指定來源資源的儲存體帳戶金鑰。</td>
    <td>Y</td>
    <td>Y<br /> (僅限預覽)</td>
    <td>Y<br /> (僅限預覽)</td>
  </tr>
  <tr>
    <td><b>/SourceSAS:&lt;sas-token></b></td>
    <td>指定來源容器的共用存取簽章 (如果適用的話)。為 SAS 加上雙引號，因為它可能包含特殊命令列字元。
        <br />
        如果來源資源是 Blob 容器，且未提供金鑰及 SAS，則可透過匿名存取讀取該 Blob 容器。
        <br />
        如果來源是資料表，則必須提供金鑰或 SAS。</td>
    <td>Y</td>
    <td>N</td>
    <td>Y<br /> (僅限預覽)</td>
  </tr>
  <tr>
    <td><b>/S</b></td>
    <td>指定複製作業的遞迴模式。在遞迴模式中，AzCopy 將複製所有符合指定檔案模式的 Blob 或檔案，包括子資料夾中的 Blob 或檔案。</td>
    <td>Y</td>
    <td>Y<br /> (僅限預覽)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/BlobType:&lt;block | page></b></td>
    <td>指定目的地 Blob 是區塊 Blob 還是分頁 Blob。此選項僅在上傳 Blob 時才適用；否則將會產生錯誤。如果目的地是 Blob 且未指定此選項，則 AzCopy 依預設將會建立區塊 Blob。</td>
    <td>Y</td>
    <td>N</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/CheckMD5</b></td>
    <td>計算下載資料的 MD5 雜湊，並驗證 MD5 雜湊是否儲存在符合計算之雜湊的 Blob 或檔案的 Content-MD5 屬性中。MD5 檢查依預設為關閉，因此您必須指定此選項，才能在下載資料時執行 MD5 檢查。
	<br />
    請注意，Azure 儲存體並不保證儲存供 Blob 或檔案使用的 MD5 雜湊是最新的版本。每次修改 Blob 或檔案時將 MD5 更新是用戶端的責任。
	<br />
    在上傳至服務之後，AzCopy 一定會為 Azure Blob 或檔案設定 Content-MD5 屬性。</td>
    <td>Y</td>
    <td>Y<br /> (僅限預覽)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/Snapshot</b></td>
    <td>指出是否傳輸快照。此選項僅在來源是 Blob 才有效。
        <br />
        傳輸的 Blob 快照集會以下列格式重新命名：[blob-name] (snapshot-time)[extension]。
        <br />
        依預設，不會複製快照。</td>
    <td>Y</td>
    <td>N</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/V:[verbose log-file]</b></td>
    <td>將詳細資訊狀態訊息輸出至記錄檔。根據預設，詳細資訊記錄檔在 <code>%LocalAppData%\Microsoft\Azure\AzCopy</code> 中命名為 <code>AzCopyVerbose.log</code>。如果您在此選項中指定現有檔案位置，則詳細資訊記錄將會被附加到該檔案。</td>
    <td>Y</td>
    <td>Y<br /> (僅限預覽)</td>
    <td>Y<br /> (僅限預覽)</td>
  </tr>
  <tr>
    <td><b>/Z:[journal-file-folder]</b></td>
    <td>指定用於繼續作業的日誌檔案資料夾。<br />
        如果作業遭到中斷，AzCopy 絕對支援繼續作業。<br />
        如果未指定此選項，或指定此選項但沒有指定資料夾路徑，則 AzCopy 將在預設位置上建立日誌檔案，預設位置是 <code>%LocalAppData%\Microsoft\Azure\AzCopy</code>。<br />
        每次發佈命令至 AzCopy 時，它會檢查預設資料夾或透過此選項指定的資料夾中是否有日誌檔案存在。如果在這兩個地方都找不到日誌檔案，AzCopy 會將此作業視為新的作業，並產生新的日誌檔案。
        <br />
		如果找到日誌檔案，則 AzCopy 將檢查所輸入的命令列是否符合日誌檔案中的命令列。如果這兩個命令列相符，AzCopy 便會繼續未完成的作業。如果這兩個命令列不符，系統將提示您覆寫日誌檔案並開始新的作業，或取消目前作業。
        <br />
        成功完成作業時，系統便會將日誌檔案刪除。
		<br />
		請注意，不支援根據舊版的 AzCopy 所建立的日誌檔案繼續作業。</td>
    <td>Y</td>
    <td>Y<br /> (僅限預覽)</td>
    <td>Y<br /> (僅限預覽)</td>
  </tr>
  <tr>
    <td><b>/@:parameter-file</b></td>
    <td>指定包含參數的檔案。AzCopy 處理檔案中的參數，就好像在命令列上指定這些參數一様。<br /> 
		在回應檔案中，您可以在單行中指定多個參數，或每一行各自指定一個參數。請注意，各個參數無法橫跨多行。
        <br />
		回應檔案可包含開頭為 <code>#</code> 符號的命令列。
        <br />
        您可以指定多個回應檔案。不過，請記住 AzCopy 不支援巢狀回應檔案。</td>
    <td>Y</td>
    <td>Y<br /> (僅限預覽)</td>
    <td>Y<br /> (僅限預覽)</td>
  </tr>
  <tr>
    <td><b>/Y</b></td>
    <td>隱藏所有 AzCopy 確認提示。</td>
    <td>Y</td>
    <td>Y<br /> (僅限預覽)</td>
    <td>Y<br /> (僅限預覽)</td>
  </tr>
  <tr>
    <td><b>/L</b></td>
    <td>僅指定清單作業，不會複製資料。</td>
    <td>Y</td>
    <td>Y<br /> (僅限預覽)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/MT</b></td>
    <td>將下載檔案的最後修改時間設定為與來源 Blob 或檔案相同的最後修改時間。</td>
    <td>Y</td>
    <td>Y<br /> (僅限預覽)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/XN</b></td>
    <td>排除較新的來源資源。如果來源比目的地還要新，則不會複製資源。</td>
    <td>Y</td>
    <td>Y<br /> (僅限預覽)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/XO</b></td>
    <td>排除較舊的來源資源。如果來源資源比目的地還要舊，則不會複製資源。</td>
    <td>Y</td>
    <td>Y<br /> (僅限預覽)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/A</b></td>
    <td>僅上傳已設定 [封存] 屬性的檔案。</td>
    <td>Y</td>
    <td>Y<br /> (僅限預覽)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/IA:[RASHCNETOI]</b></td>
    <td>僅上傳已設定任何指定屬性的檔案。<br />
        可用屬性包括：  
        <br />
        R&#160;&#160;&#160;唯讀檔案
        <br />
        A&#160;&#160;&#160;準備封存的檔案
        <br />
        S&#160;&#160;&#160;系統檔案
        <br />
        H&#160;&#160;&#160;隱藏檔案
        <br />
        C&#160;&#160;&#160;壓縮檔案
        <br />
        N&#160;&#160;&#160;正常檔案
        <br />
        E&#160;&#160;&#160;加密檔案
        <br />
        T&#160;&#160;&#160;暫存檔案
        <br />
        O&#160;&#160;&#160;離線檔案
        <br />
        I&#160;&#160;&#160;未編製索引的檔案</td>
    <td>Y</td>
    <td>Y<br /> (僅限預覽)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/XA:[RASHCNETOI]</b></td>
    <td>排除已設定任何指定屬性的檔案。<br />
        可用屬性包括：  
        <br />
        R&#160;&#160;&#160;唯讀檔案  
        <br />
        A&#160;&#160;&#160;準備封存的檔案  
        <br />
        S&#160;&#160;&#160;系統檔案  
        <br />
        H&#160;&#160;&#160;隱藏檔案  
        <br />
        C&#160;&#160;&#160;壓縮檔案  
        <br />
        N&#160;&#160;&#160;正常檔案  
        <br />
        E&#160;&#160;&#160;加密檔案  
        <br />
        T&#160;&#160;&#160;暫存檔案  
        <br />
        O&#160;&#160;&#160;離線檔案  
        <br />
        I&#160;&#160;&#160;未編製索引的檔案</td>
    <td>Y</td>
    <td>Y<br /> (僅限預覽)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/Delimiter:&lt;delimiter></b></td>
    <td>指出在 Blob 名稱中，用來分隔虛擬目錄的分隔符號字元。<br />
        依預設，AzCopy 會使用 / 作為分隔符號字元。不過，AzCopy 支援使用任何常見字元 (例如 @、# 或 %) 作為分隔符號。如果您必須在命令列中包含其中一個特殊字元，請為檔案名稱加上雙引號。
        <br />
        此選項僅適用於下載 Blob。</td>
    <td>Y</td>
    <td>N</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/NC:&lt;number-of-concurrents></b></td>
    <td>指定並行作業的數目。
        <br />
        AzCopy 依預設會啟動特定數量的並行作業，以提高資料傳輸的輸送量。請注意，在低頻寬環境中的大量並行作業有可能會拖垮網路連線，並使作業無法完成。根據實際可用網路頻寬來節流處理並行作業。
        <br />
		並行作業數的上限為 512。</td>
    <td>Y</td>
    <td>Y<br /> (僅限預覽)</td>
    <td>Y<br /> (僅限預覽)</td>
  </tr>
  <tr>
    <td><b>/SourceType:Blob|Table</b></td>
    <td>指定 <code>source</code> 資源是可在本機開發環境中使用，並在儲存體模擬器中執行的 blob。</td>
    <td>Y</td>
    <td>N</td>
    <td>Y<br /> (僅限預覽)</td>
  </tr>
  <tr>
    <td><b>/DestType:Blob|Table</b></td>
    <td>指定 <code>destination</code> 資源是可在本機開發環境中使用，並在儲存體模擬器中執行的 blob。</td>
    <td>Y</td>
    <td>N</td>
    <td>Y<br /> (僅限預覽)</td>
  </tr>
  <tr>
    <td><strong>/PKRS:&lt;"key1#key2#key3#..."></strong></td>
    <td>分割資料分割索引鍵範圍以支援平行匯出資料表資料的作業，這樣可以加快匯出作業的速度。
        <br />
        若未指定此選項，AzCopy 會使用單一執行緒來匯出資料表實體。例如，如果使用者指定 /PKRS:"aa#bb"，則 AzCopy 會啟動三個並行作業。
        <br />
        每個作業分別會匯出三個資料分割索引鍵範圍的其中一個，如下所示： 
        <br />
        &#160;&#160;&#160;[&lt;first partition key>, aa) 
        <br />
        &#160;&#160;&#160;[aa, bb)
        <br />
        &#160;&#160;&#160;[bb, &lt;last partition key>] </td>
    <td>N</td>
    <td>N</td>
    <td>Y<br /> (僅限預覽)</td>
  </tr>
  <tr>
    <td><strong>/SplitSize:</strong><file-size><strong>&lt;file-size></strong></td>
    <td>指定匯出的檔案分割大小 (以 MB 為單位)，允許的最小值為 32。
        <br />
        若未指定此選項，則 AzCopy 會將資料表資料匯出至單一檔案。
        <br />
        如果資料表資料匯出至 Blob，且匯出的檔案大小達到 Blob 的大小限制 200 GB，則 AzCopy 會分割匯出的檔案，即使未指定此選項亦然。</td>
    <td>N</td>
    <td>N</td>
    <td>Y<br /> (僅限預覽)</td>
  </tr>
  <tr>
    <td><b>/EntityOperation:&lt;InsertOrSkip | InsertOrMerge | InsertOrReplace> </b>
</td>
    <td>指定資料表資料匯入行為。
        <br />
        InsertOrSkip - 略過現有實體，或插入新實體 (若不存在於資料表中)。
        <br />
        InsertOrMerge - 合併現有實體，或插入新實體 (若不存在於資料表中)。
        <br />
        InsertOrReplace - 取代現有實體，或插入新實體 (若不存在於資料表中)。</td>
    <td>N</td>
    <td>N</td>
    <td>Y<br /> (僅限預覽)</td>
  </tr>
  <tr>
    <td><b>/Manifest:&lt;manifest-file></b></td>
    <td>指定匯入作業的資訊清單檔。<br />
        資訊清單檔會在匯出作業期間產生。</td>
    <td>N</td>
    <td>N</td>
    <td>Y<br /> (僅限預覽)</td>
  </tr>
  <tr>
    <td><b>/SyncCopy</b></td>
    <td>指出在兩個 Azure 儲存體端點之間是否以同步方式複製 blob 或檔案。<br />
		AzCopy 依預設會使用伺服器端的非同步複本。指定此選項可執行同步複製，也就是將 blog 或檔案下載至本機記憶體，再上傳至 Azure 儲存體。當您複製檔案是在 Blob 儲存體內、在檔案儲存體內，或從 Blob 儲存體到檔案儲存體 (或反過來) 時，您可以使用此選項。</td>
    <td>Y</td>
    <td>Y<br /> (僅限預覽)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/SetContentType:&lt;content-type></b></td>
    <td>指定目的地 blob 或檔案的 MIME 內容類型。<br />
		AzCopy 預設會將 blob 或檔案的內容類型設定為 <code>application/octet-stream</code>。您可以明確指定這個選項的值來設定所有 blob 或檔案的內容類型。如果您指定這個選項但未指定任何值，則 AzCopy 會根據副檔名來設定每個 blob 或檔案的內容類型。</td>
    <td>Y</td>
    <td>Y<br /> (僅限預覽)</td>
    <td>N</td>
  </tr>
</table>

<br/>

## 限制複製資料時的並行寫入

使用 AzCopy 複製 Blob 或檔案時，請留意當您在複製資料時，另一個應用程式可能正在修改該資料。請儘可能地確定在複製作業過程中，您正要複製的資料並不在修改中。例如，當複製與 Azure 虛擬機器相關聯的 VHD 時，請確定目前沒有其他應用程式正在寫入此 VHD。此外，您可以首先建立 VHD 的快照，然後複製此快照。

如果您無法在複製時防止其他應用程式寫入 Blob 或檔案，請記住，工作完成時，複製的資源可能不再與來源資源完全相同。

## 使用 AzCopy 複製 Azure Blob

下列範例說明使用 AzCopy 複製 Blob 的各種案例。

### 複製單一 Blob

**將檔案從檔案系統上傳至 Blob 儲存體：**
	
	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:abc.txt

**將 Blob 從 Blob 儲存體下載到檔案系統：**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

如需有關使用儲存體存取金鑰的詳細資訊，請參閱[檢視、複製和重新產生儲存體存取金鑰](../storage-create-storage-account/#regeneratestoragekeys)。

### 透過伺服器端複製來複製 Blob

當您在儲存體帳戶內或跨儲存體帳戶複製 Blob 時，系統便會執行伺服器端複製作業。如需伺服器端複製作業的詳細資訊，請參閱[非同步跨帳戶複製 Blob 簡介](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) (英文)。

**在儲存體帳戶內複製 blob：**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceKey:key /DestKey:key /Pattern:abc.txt 

**在儲存體帳戶之間複製 blob：**

	AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt
 
### 從次要區域複製 Blob 

如果您的儲存體帳戶已啟用讀取存取地理區域備援儲存體，則您可以複製次要區域的資料。

**將 blob 從次要帳戶複製到主要帳戶：**

	AzCopy /Source:https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 /Dest:https://myaccount2.blob.core.windows.net/mynewcontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt

**將次要區域中的 blob 下載到檔案系統中的某個檔案：**

	AzCopy /Source:https://myaccount-secondary.blob.core.windows.net/mynewcontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

### 將檔案上傳至新的 Blob 容器或虛擬目錄

**將檔案上傳至新的 blob 容器**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mynewcontainer /DestKey:key /Pattern:abc.txt

請注意，如果指定的目的地容器不存在，則 AzCopy 將建立此容器並將檔案上傳至該容器中。

**將檔案上傳至新的 blob 虛擬目錄**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer/vd /DestKey:key /Pattern:abc.txt

請注意，如果指定的虛擬目錄不存在，則 AzCopy 將上傳檔案並在其名稱中加上此虛擬目錄 (*例如*，上述範例中的 `vd/abc.txt`)。

### 將 Blob 下載到新的資料夾

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

如果資料夾 `C:\myfolder` 尚未存在，AzCopy 會在檔案系統中建立它，並將 `abc.txt ` 下載到新資料夾。

### 將目錄中的檔案和子資料夾以遞迴方式上傳至容器

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /S

指定 `/S` 選項以遞迴方式將指定目錄的內容複製到 Blob 儲存體，這表示也會複製所有的子資料夾及其檔案。例如，假設下列檔案位於 `C:\myfolder` 資料夾內：

	C:\myfolder\abc.txt
	C:\myfolder\abc1.txt
	C:\myfolder\abc2.txt
	C:\myfolder\subfolder\a.txt
	C:\myfolder\subfolder\abcd.txt

複製作業之後，容器將包含下列檔案：

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

### 將檔案從目錄以非遞迴方式上傳至容器

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key

如果您未在命令列上指定 `/S` 選項，則 AzCopy 將不會以遞迴方式複製。只有指定目錄中的檔案會被複製；任何子資料夾及其檔案「不會」被複製。例如，假設下列檔案位於 `C:\myfolder` 資料夾內：

	C:\myfolder\abc.txt
	C:\myfolder\abc1.txt
	C:\myfolder\abc2.txt
	C:\myfolder\subfolder\a.txt
	C:\myfolder\subfolder\abcd.txt

複製作業之後，容器將包含下列檔案：

	abc.txt
	abc1.txt
	abc2.txt

### 將容器中的所有 Blob 以遞迴方式下載到檔案系統中的目錄

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /S

假設下列 Blob 位於指定容器中：

	abc.txt
	abc1.txt
	abc2.txt
	vd1\a.txt
	vd1\abcd.txt

複製作業之後，目錄 `C:\myfolder` 將包含下列檔案：

	C:\myfolder\abc.txt
	C:\myfolder\abc1.txt
	C:\myfolder\abc2.txt
	C:\myfolder\vd1\a.txt
	C:\myfolder\vd1\abcd.txt

### 將虛擬 Blob 目錄中的 Blob 以遞迴方式下載到檔案系統中的目錄

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer/vd1/ /Dest:C:\myfolder /SourceKey:key /S

假設下列 Blob 位於指定容器中：

	abc.txt
	abc1.txt
	abc2.txt
	vd1\a.txt
	vd1\abcd.txt

複製作業之後，目錄 `C:\myfolder` 將包含下列檔案。請注意，只有虛擬目錄中的 Blob 會被複製：

	C:\myfolder\a.txt
	C:\myfolder\abcd.txt

### 將符合指定檔案模式的檔案以遞迴方式上傳至容器 

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:a* /S

假設下列檔案位於 `C:\myfolder` 資料夾內：

	C:\myfolder\abc.txt
	C:\myfolder\abc1.txt
	C:\myfolder\abc2.txt
	C:\myfolder\xyz.txt
	C:\myfolder\subfolder\a.txt
	C:\myfolder\subfolder\abcd.txt

複製作業之後，容器將包含下列檔案：

	abc.txt
	abc1.txt
	abc2.txt
	subfolder\a.txt
	subfolder\abcd.txt
	
### 將具有指定首碼的 Blob 以遞迴方式下載到檔案系統

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:a /S

假設下列 Blob 位於指定容器中。將會複製所有以首碼 `a` 開頭的 blob：

	abc.txt
	abc1.txt
	abc2.txt
	xyz.txt
	vd1\a.txt
	vd1\abcd.txt

複製作業之後，資料夾 `C:\myfolder` 將包含下列檔案：

	C:\myfolder\abc.txt
	C:\myfolder\abc1.txt
	C:\myfolder\abc2.txt

請注意，首碼會套用到虛擬目錄，虛擬目錄會構成第一部分的 Blob 名稱。在上述範例中，虛擬目錄不符合指定的首碼，所以不會被複製。


### 將 Blob 及其快照複製到另一個儲存體帳戶

	AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt /Snapshot

複製作業之後，目標容器將包含 Blob 及其快照。假設上述範例中的 Blob 有兩份快照，則容器將包含下列 Blob 及快照：

	abc.txt
	abc (2013-02-25 080757).txt
	abc (2014-02-21 150331).txt


### 使用回應檔案指定命令列參數

	AzCopy /@:"C:\myfolder\abc.txt"

您可以在回應檔案中包含任何 AzCopy 命令列參數。AzCopy 處理檔案中的參數，就好像在命令列上指定這些參數一様，執行使用檔案內容的直接取代。

**指定一或多個單行回應檔案**

假設名為 `source.txt` 且指定來源容器的回應檔案：

	/Source:http://myaccount.blob.core.windows.net/mycontainer

及名為 `dest.txt` 且在檔案系統中指定目的地資料夾的回應檔案：

	/Dest:C:\myfolder

及名為 `options.txt` 且指定 AzCopy 選項的回應檔案：

	/S /Y

若要使用這些回應檔案 (所有這些檔案皆位於 `C:\responsefiles` 目錄內) 呼叫 AzCopy，請使用此命令：

	AzCopy /@:"C:\responsefiles\source.txt" /@:"C:\responsefiles\dest.txt" /SourceKey:<sourcekey> /@:"C:\responsefiles\options.txt"   

AzCopy 處理此命令，就好像您在命令列上包含所有個別參數一樣：

	AzCopy /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

**指定多行回應檔案**

假設名為 `copyoperation.txt` 且包含下列資料行的回應檔案。每一行會各自指定一個 AzCopy 參數：

	/Source:http://myaccount.blob.core.windows.net/mycontainer
	/Dest:C:\myfolder
	/SourceKey:<sourcekey>
	/S 
	/Y

若要使用這個回應檔案呼叫 AzCopy，請使用此命令：

	AzCopy /@:"C:\responsefiles\copyoperation.txt"

AzCopy 處理此命令，就好像您在命令列上包含所有個別參數一樣：

	AzCopy /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

請注意，必須在一行內指定完一個 AzCopy 參數。例如，如果您將參數分割成兩行，如以下的 `/sourcekey` 參數所示，則 AzCopy 將會失敗：

	http://myaccount.blob.core.windows.net/mycontainer
 	C:\myfolder
	/sourcekey:
	[sourcekey]
	/S 
	/Y

### 指定共用存取簽章 (SAS)
	
**使用 /sourceSAS 選項為來源容器指定 SAS**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /DestC:\myfolder /SourceSAS:SAS /S

**為來源容器 URI 上的來源容器指定 SAS**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken /Dest:C:\myfolder /S

**使用 /destSAS 選項為目的地容器指定 SAS**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer1 /DestSAS:SAS /Pattern:abc.txt

**為來源和目的地容器指定 SAS**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceSAS:SAS1 /DestSAS:SAS2 /Pattern:abc.txt

### 指定日誌檔案資料夾

每次發佈命令至 AzCopy 時，它會檢查預設資料夾或透過此選項指定的資料夾中是否有日誌檔案存在。如果在這兩個地方都找不到日誌檔案，AzCopy 會將此作業視為新的作業，並產生新的日誌檔案。

如果找到日誌檔案，則 AzCopy 將檢查所輸入的命令列是否符合日誌檔案中的命令列。如果這兩個命令列相符，AzCopy 便會繼續未完成的作業。如果這兩個命令列不符，系統將提示您覆寫日誌檔案並開始新的作業，或取消目前作業。

**使用日誌檔案的預設位置**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z

如果省略 `/Z`，或指定 `/Z` 選項但沒有指定資料夾路徑 (如上所示)，則 AzCopy 會在預設位置上建立日誌檔案，預設位置是 `%SystemDrive%\Users%username%\AppData\Local\Microsoft\Azure\AzCopy`。如果日誌檔案已存在，則 AzCopy 會根據此日誌檔案繼續作業。

**指定日誌檔案的自訂位置**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z:C:\journalfolder\

如果日誌檔案不存在，本範例將建立日誌檔案。如果日誌檔案已存在，則 AzCopy 會根據此日誌檔案繼續作業。

**繼續 AzCopy 作業**

	AzCopy /Z:C:\journalfolder\

本範例會繼續最後一個無法完成的作業。


### 產生記錄檔

**在預設位置中寫入詳細資訊記錄檔**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V

如果指定 `/V` 選項，但沒有提供詳細資訊記錄的檔案路徑，則 AzCopy 會在預設位置上建立記錄檔，預設位置是 `%SystemDrive%\Users%username%\AppData\Local\Microsoft\Azure\AzCopy`。

**在自訂位置中寫入詳細資訊記錄檔**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V:C:\myfolder\azcopy1.log

請注意，如果您在 `/V` 選項後面指定相對路徑 (例如 `/V:test/azcopy1.log`)，則系統會在目前工作目錄中的一個名為 `test` 的子資料夾內建立詳細資訊記錄。


### 將下載檔案的最後修改時間設定為與來源 Blob 相同的最後修改時間

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT

### 根據其最後修改時間，將 Blob 從複製作業中排除

指定 `/MT` 選項可比較來源 blob 和目的地檔案的上次修改時間。

**將比目的地檔案還要新的 blob 排除**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XN

**將比目的地檔案還要舊的 blob 排除**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XO

### 指定要啟動的並行作業數目

`/NC` 選項可指定並行複製作業的數目。AzCopy 預設的開始並行作業數目是 8 倍的所擁有核心處理器數目。如果您在低頻寬的網路上執行 AzCopy，則您可以在此選項中指定較低的數字，以避免因為資源競爭所導致的失敗。


### 	在儲存體模擬器中針對 Blob 資源執行 AzCopy

	AzCopy /Source:https://127.0.0.1:10004/myaccount/myfileshare/ /Dest:C:\myfolder /SourceKey:key /SourceType:Blob /S

### 在兩個 Azure 儲存體端點之間以同步方式複製 blob

根據預設，AzCopy 會以非同步方式在兩個儲存體端點之間複製資料。因此，複製作業會在背景中執行，並使用在 blob 複製速度方面沒有 SLA 的備用頻寬容量，而 AzCopy 會定期檢查複製狀態，直到複製完成或失敗為止。

3.1.0 版中新增的 `/SyncCopy` 選項可確保複製作業達到一致的速度。AzCopy 執行同步複製的方式是先將要複製的 blob，從指定的來源下載到本機記憶體，再上傳至 Blob 儲存體目的地。

	AzCopy /Source:https://myaccount1.blob.core.windows.net/myContainer/ /Dest:https://myaccount2.blob.core.windows.net/myContainer/ /SourceKey:key1 /DestKey:key2 /Pattern:ab /SyncCopy

請注意，相較於非同步複製，/SyncCopy 可能會產生額外的輸出成本，建議的方法是在與來源儲存體帳戶位於同一區域的 Azure VM 中使用這個選項，以避免產生輸出成本。

### 指定目的地 blob 的 MIME 內容類型

根據預設，AzCopy 會將目的地 blob 的內容類型設定為 `application/octet-stream`。從 3.1.0 版開始，您可以透過 `/SetContentType:[content-type]` 選項明確指定內容類型。此語法在複製作業中設定所有 blob 的內容類型。

	AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType:video/mp4

如果您指定 `/SetContentType` 但未指定任何值，則 AzCopy 會根據副檔名來設定每個 blob 或檔案的內容類型。

	AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType

## 使用 AzCopy 複製 Azure 檔案儲存體中的檔案 (僅限預覽版本)

下列範例說明使用 AzCopy 複製 Azure 檔案的各種案例。

### 將檔案從 Azure 檔案共用下載到檔案系統

	AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/myfolder1/ /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

請注意，如果指定的來源是 Azure 檔案共用，則您必須指定確切檔案名稱 (*例如* `abc.txt`) 以複製單一檔案，或指定 `/S` 選項以遞迴方式複製共用中的所有檔案。嘗試同時指定檔案模式和 `/S` 選項將會造成錯誤。

### 將 Azure 檔案共用中的檔案和資料夾以遞迴方式下載到檔案系統

	AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/ /Dest:C:\myfolder /SourceKey:key /S

請注意，將不會複製任何空白資料夾。


### 將檔案和資料夾從檔案系統以遞迴方式上傳至 Azure 檔案共用

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /S

請注意，將不會複製任何空白資料夾。


### 將符合指定檔案模式的檔案以遞迴方式上傳至 Azure 檔案共用

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:ab* /S


### 以同步方式複製 Azure 檔案儲存體中的檔案

使用者可以使用 4.1.0 預覽版中新的 /SyncCopy 選項，將檔案從檔案儲存體複製到檔案儲存體、從檔案儲存體複製到 Blob 儲存體，以及從 Blob 儲存體複製到檔案儲存體。

	AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S /SyncCopy

	AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare/ /Dest:https://myaccount2.blob.core.windows.net/mycontainer/ /SourceKey:key1 /DestKey:key2 /S /SyncCopy
	
	AzCopy /Source:https://myaccount1.blob.core.windows.net/mycontainer/ /Dest:https://myaccount2.file.core.windows.net/myfileshare/ /SourceKey:key1 /DestKey:key2 /S /SyncCopy

從檔案儲存體複製到 Blob 儲存體時，預設的 blob 類型是區塊 blob，使用者可以指定 /BlobType:page 選項來變更目的地 blob 類型。

請注意，當 AzCopy 4.1.0 預覽版發行時，Azure 儲存體服務尚不支援非同步複製，因此，如果未指定 /SyncCopy 選項，則上述複製作業會失敗。


## 使用 AzCopy 複製 Azure 資料表中的實體 (僅限預覽版本)

下列範例說明使用 AzCopy 複製 Azure 資料表實體的各種案例。

### 將實體匯出至本機檔案系統

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key

### 將實體匯出至 Azure Blob

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:https://myaccount.blob.core.windows.net/mycontainer/ /SourceKey:key1 /Destkey:key2

AzCopy 將會使用下列命令慣例，在本機資料夾或 Blob 容器中產生 JSON 資料檔案：

	<account name>_<table name>_<timestamp>_<volume index>_<CRC>.json

產生的 JSON 資料檔案會遵循基本中繼資料的裝載格式。如需此裝載格式的詳細資訊，請參閱[資料表服務作業的裝載格式](http://msdn.microsoft.com/library/azure/dn535600.aspx)。

請注意，將儲存體資料表實體匯出到儲存體 Blob 時，AzCopy 會先將資料表實體匯出到本機暫存資料檔，然後再上傳至 Blob，這些暫存資料檔會放入日誌檔案資料夾中，預設路徑為 “<code>%LocalAppData%\Microsoft\Azure\AzCopy</code>”，您可以指定 /Z:[journal-file-folder] 選項來變更日誌檔案資料夾位置，從而變更暫存資料檔位置。暫存資料檔大小取決於資料表實體大小和您使用 /SplitSize 選項所指定的大小，雖然本機磁碟中的暫存資料檔在上傳至 Blob 之後就立即刪除，但請確定您有足夠的本機磁碟空間，可儲存這些尚未刪除的暫存資料檔。

### 分割匯出檔案

	AzCopy /Source:https://myaccount.table.core.windows.net/mytable/ /Dest:C:\myfolder /SourceKey:key /S /SplitSize:100

AzCopy 會在分割資料檔案名稱中使用*磁碟區索引*，以區分多個檔案。磁碟區索引由兩部分組成：*資料分割索引鍵範圍索引*和*分割檔案索引*。兩個索引皆以零為基礎。

如果使用者未指定 `/PKRS` 選項 (將在下節中介紹)，資料分割索引鍵範圍索引將會是 0。

例如，在使用者指定 `/SplitSize` 選項之後，假設 AzCopy 產生兩個資料檔。產生的資料檔案名稱可能會是：

	myaccount_mytable_20140903T051850.8128447Z_0_0_C3040FE8.json
	myaccount_mytable_20140903T051850.8128447Z_0_1_0AB9AC20.json

請注意，`/SplitSize` 選項的最小可能值為 32MB。如果指定的目的地是 Blob 儲存體，則 AzCopy 會分割已達到 Blob 大小限制 (200GB) 的資料檔，而不論使用者是否已指定 `/SplitSize` 選項。

### 並行匯出實體

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PKRS:"aa#bb"

當使用者指定 `/PKRS` 選項時，AzCopy 將會啟動並行作業以匯出實體。每個作業分別會匯出一個資料分割索引鍵範圍。

請注意，並行作業的數目也由 `/NC` 選項控制。在複製資料表實體時，即使未指定 `/NC`，AzCopy 會以核心處理器的數目作為 `/NC` 的預設值。當使用者指定 `/PKRS` 選項時，AzCopy 會從兩個值 (資料分割索引鍵範圍和隱含或明確指定的並行作業) 中選擇較小者來使用，以決定要啟動的並行作業數目。如需詳細資訊，請在命令列上輸入 `AzCopy /?:NC`。

### 並行匯入實體

	AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.core.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace 

當您匯出資料表實體時，AzCopy 會將資訊清單檔寫入至指定的目的地資料夾或 Blob 容器。匯入程序會使用資訊清單檔來尋找必要的資料檔案，並在匯入程序期間執行資料驗證。資訊清單檔會使用下列命令慣例：

	<account name>_<table name>_<timestamp>.manifest

選項 `/EntityOperation` 指出如何將實體插入資料表。可能的值包括：

- `InsertOrSkip`：略過現有實體，或插入新實體 (若不存在於資料表中)。
- `InsertOrMerge`：合併現有實體，或插入新實體 (若不存在於資料表中)。
- `InsertOrReplace`：取代現有實體，或插入新實體 (若不存在於資料表中)。

請注意，您無法在匯入案例中指定 `/PKRS` 選項。不同於必須指定 `/PKRS` 選項以啟動並行作業的匯出案例，AzCopy 依預設會在您匯入實體時啟動並行作業。依預設啟動的並行作業數目等於核心處理器的數目；但您可以使用 `/NC` 選項指定不同的並行數目。如需詳細資訊，請在命令列上輸入 `AzCopy /?:NC`。


## 已知問題和最佳作法

#### 在一部電腦上執行一個 AzCopy 執行個體。
AzCopy 設計為充分利用電腦資源來加速資料傳輸，建議您在一部電腦上只執行一個 AzCopy 執行個體，如果需要更多並行作業，您可以指定 `/NC` 選項。如需詳細資訊，請在命令列上輸入 `AzCopy /?:NC`。

#### 使用 AzCopy 時，請確定 [使用 FIPS 相容演算法於加密，雜湊，以及簽章] 已停用，請注意預設會停用此選項。
您可以在 `Run` 視窗中輸入 `secpol.msc`，並在 `Security Setting->Local Policy->Security Options->System cryptography: Use FIPS compliant algorithms for encryption, hashing and signing` 中檢查此參數。請注意，在您使用的 Windows 作業系統上，此設定路徑可能不同。


## AzCopy 版本

| 版本 | 新功能 |
|---------|-----------------------------------------------------------------------------------------------------------------|
| **4.1.0 版** | **目前的預覽版本。包含從 3.1.0 版開始的所有功能。支援以同步方式複製 blob 和檔案，以及指定目的地 blob 和檔案的內容類型**	
| **3.1.0 版** | **目前版本。支援以同步方式複製 blob，以及指定目的地 blob 的內容類型。**
| V4.0.0 | 包含 V3.0.0 中的所有功能。也支援對 Azure 檔案儲存體的出入檔案複製，以及對 Azure 資料表儲存體的出入實體複製。
| V3.0.0 | 修改 AzCopy 命令列語法以要求參數名稱，並重新設計命令列說明。此版本僅支援對 Azure Blob 儲存體的出入複製。	
| V2.5.1 | 最佳化使用選項 /xo 和 /xn 時的效能。修正與來源檔案名稱中的特殊字元有關的錯誤，以及在使用者輸入錯誤命令列語法後發生的日誌檔案損毀。	
| V2.5.0 | 針對大規模複製案例最佳化效能，並介紹數個重要的可用性改良。	
| V2.4.1 | 支援在安裝精靈中指定目的地資料夾。                     			
| V2.4.0 | 支援上傳與下載 Azure 檔案儲存體的檔案。                       				                              
| V2.3.0 | 支援讀取存取地理區域備援儲存體帳戶。 |
| V2.2.2 | 已升級並使用 Azure 儲存體用戶端程式庫 3.0.3 版。                                            				                    
| V2.2.1 | 已修正在相同儲存體帳戶中複製大量檔案時的效能問題。            				                                                
| V2.2 | 支援設定 Blob 名稱的虛擬目錄分隔符號。支援指定日誌檔案路徑。 |
| V2.1 | 提供超過 20 個選項，以有效的方式支援 Blob 上傳、下載及複製作業。 |


## 後續步驟

如需關於 Azure 儲存體和 AzCopy 的詳細資訊，請參閱下列資源。

### Azure 儲存體文件：

- [Azure 儲存體簡介](storage-introduction.md)
- [在 Blob 儲存體中儲存檔案](storage-dotnet-how-to-use-blobs.md)
- [使用檔案儲存體在 Azure 中建立 SMB 檔案共用](storage-dotnet-how-to-use-files.md)

### Azure 儲存體部落格文章：
- [AzCopy：簡介同步複製和自訂內容類型](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
- [AzCopy: 宣布正式發行 AzCopy 3.0 和具有資料表和檔案支援的 AzCopy 4.0 預覽版本](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
- [AzCopy: 針對大規模複製案例最佳化](http://go.microsoft.com/fwlink/?LinkId=507682)
- [Microsoft Azure 檔案服務簡介](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
- [AzCopy: 支援讀取存取異地備援儲存體](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
- [AzCopy: 使用可重新啟動模式和 SAS 權杖傳輸資料](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
- [AzCopy: 使用跨帳戶複製 Blob](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
- [AzCopy: 上傳/下載 Azure Blob 的檔案](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)

 

<!---HONumber=July15_HO1-->