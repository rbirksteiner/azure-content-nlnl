<properties
   pageTitle="Azure 備份 - 離線備份或使用 Azure 匯入/匯出服務進行初始植入"
   description="了解 Azure 備份如何讓您使用 Azure 匯入/匯出服務在網路上傳送資料。此文章說明如何使用 Azure 匯入/匯出服務離線植入初始備份資料"
   services="backup"
   documentationCenter=""
   authors="prvijay"
   manager="shreeshd"
   editor=""/>
<tags
   ms.service="backup"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage-backup-recovery"
   ms.date="04/07/2015"
   ms.author="prvijay"/>

# 在 Azure 備份中離線備份工作流程

Azure 備份與 Azure 匯入/匯出服務密切整合，可讓您快速傳輸初始備份資料。如果您的初始備份資料大小達到 TB，且需要透過高延遲和低頻寬網路傳輸時，您可以使用 Azure 匯入/匯出服務將初始備份複本送至 Azure 資料中心的一個或多個硬碟上。本文章提供完成此工作流程所需步驟的概觀。

## 概觀

您可以使用 Azure 備份和 Azure 匯入/匯出，透過磁碟簡單且直接地將資料離線上傳至 Azure。您不需透過網路傳輸初始完整複本，備份資料會寫入至*預備位置*。預備位置可以是直接連接的儲存體或網路共用。一旦完成初始複本，則會使用 *Azure 匯入/匯出工具*，此資料會寫入最後送至 Azure 資料中心的 SATA 磁碟機。根據初始備份的大小，可能需要一個或多個 SATA 磁碟機才能完成此作業。Azure 匯入/匯出工具帳戶適用於三種案例。將備份寫入至磁碟後，您可以將其送至最近的資料中心位置，以便上傳至 Azure。接著 Azure 備份會將備份資料複製至備份保存庫，並排程增量備份。

## 必要條件

1. 請務必熟悉 Azure 匯入/匯出工作流程，相關資訊會在[這裡](../storage-import-export-service.md)列出。

2. 起始工作流程之前，請確定已建立 Azure 備份保存庫、已下載保存庫認證、Azure 備份代理程式已安裝於您的 Windows Server/Windows 用戶端或 System Center Data Protection Manager (SCDPM) 伺服器，且電腦已註冊 Azure 備份保存庫。

3. 在您打算備份我們資料的電腦上，從[這裡](https://manage.windowsazure.com/publishsettings)下載 Azure 發佈檔案設定。

4. 準備可能是網路共用或電腦上其他磁碟機的*預備位置*。請確定預備位置有足夠的磁碟空間來保存您的初始複本。例如：若您正在嘗試備份 500 GB 的檔案伺服器，請確定預備區域至少有 500 GB 的空間 (雖然使用量可能較少)。預備區域是「暫時性儲存體」，因此在此工作流程期間會暫時使用。

5. 外部 SATA 磁碟機寫入器和外部的 3.5 英吋的 SATA 磁碟機。只有 3.5 英吋的 SATA II/III 硬碟能夠用於匯入/匯出服務。不支援大於 4TB 的硬碟。您可以將 SATA II/III 磁碟附加至大多數使用 SATA II/III USB 介面卡的外部電腦。檢查 Azure 匯入/匯出文件以取得服務所支援的最新磁碟機組合。

5. 在 SATA 磁碟機寫入器所連接的電腦上啟用 BitLocker。

6. 從[這裡](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409)將 Azure 匯入/匯出工具下載至 SATA 磁碟機寫入器所連接的電腦上。


## 工作流程
本章節提供的資訊可讓您完成**離線備份**工作流程，以便將您的資料傳遞至 Azure 資料中心，並上傳至 Azure 儲存體。若您有關於匯入服務或處理程序任何層面的問題，請參閱[上方](../storage-import-export-service.md)參考的匯入服務概觀。

### 初始離線備份

1. 作為排程備份的一部分，您將會遇到下列畫面 (在 Windows Server、Windows 用戶端或 SCDPM 中)。<br/> ![匯入畫面][1]

  SCDPM 中的對應畫面如下所示。<br/> ![DPM 匯入畫面][8]

其中：

+ **預備位置** -這是指初始備份所寫入的暫時儲存體位置。這可能是網路共用或在本機電腦上。

+ **Azure 匯入工作名稱** - 作為完成此工作流程的一部分，您需要在 Azure 入口網站 (文件後續部分所涵蓋) 建立*匯入工作*。提供您打算在 Azure 入口網站中稍後使用的輸入。

+ **Azure 發佈設定** - 這是一個 XML 檔案，其中包含您的訂用帳戶設定檔的相關資訊。也包含與訂用帳戶相關聯的安全認證。檔案可從[這裡](https://manage.windowsazure.com/publishsettings)下載。提供發佈設定檔案的本機路徑。

+ **Azure 訂用帳戶識別碼** - 提供您打算初始化 Azure 匯入工作的 Azure 訂用帳戶識別碼。如果您有多個 Azure 訂用帳戶，請使用與匯入工作相關聯的識別碼。

+ **Azure 儲存體帳戶** - 輸入與此匯入工作相關聯的 Azure 儲存體帳戶名稱。

+ **Azure 儲存體容器** - 輸入此工作資料所匯入的目的地儲存體 blob 名稱。


完成工作流程，並在 Azure 備份 MMC 中選取 [**立即備份**]，以初始化離線備份複本。作為此步驟的一部分，初始備份會寫入預備區域。<br/>

  ![立即備份][2]

SCDPM 中的對應工作流程可透過按一下 [**保護群組**]，然後選擇 [**建立復原點**] 選項來啟用。接著選擇 [**線上保護**] 選項。<br/> ![立即備份 DPM][9]

當作業完成時，在預備位置中會建立 *.AIBBlob* 和 *.BaseBlob* 檔案。<br/> ![輸出][3]

### 準備 SATA 磁碟機

1. 您可以將 [Microsoft Azure 匯入/匯出工具](http://go.microsoft.com/fwlink/?linkid=301900&clcid=0x409)下載至*複本電腦*。請確定預備位置 (在上一個步驟中) 可從您打算執行下一組命令的電腦進行存取。必要時，複本電腦可以與來源電腦相同。

2. 解壓縮 *WAImportExport.zip* 檔案。執行 *WAImportExport* 工具，以格式化 SATA 磁碟機、將備份資料寫入 SATA 磁碟機並進行加密。執行下列命令之前請確定電腦上已啟用 BitLocker。<br/>

*.\WAImportExport.exe PrepImport /j:<*JournalFile*>.jrn /id: <*SessionId*> /sk:<*StorageAccountKey*> /BlobType:**PageBlob** /t:<*TargetDriveLetter*> /format /encrypt /srcdir:<*staging location*> /dstdir: <*DestinationBlobVirtualDirectory*>/*


| 參數 | 說明|
|-------------|-------------|
| /j:<*JournalFile*>| 日誌檔案的路徑。每個磁碟機必須只有一個日誌檔案。請注意，日誌檔案不得位於目標磁碟機上。日誌檔案的副檔名是 .jrn，且作為執行此命令的一部分而建立。|
|/id:<*SessionId*> | 工作階段識別碼會識別*複製工作階段*。其用來確保正確復原中斷的複製工作階段。在複製工作階段中複製的檔案會儲存在以目標磁碟機上工作階段識別碼命名的目錄。|
| /sk:<*StorageAccountKey*> | 資料將要匯入儲存體帳戶的帳戶金鑰。 |
| /BlobType | 指定 **PageBlob**，此工作流程僅在指定 PageBlob 選項時才會成功。這不是預設選項，且應該在此命令中進行描述。 |
|/t:<*TargetDriveLetter*> | 目前複製工作階段中目標硬碟的磁碟機代號，不包含結尾的冒號。|
|/format | 當磁碟機需要進行格式化時請指定此參數；否則請省略。此工具格式化磁碟機之前，會提示您從主控台進行確認。若要隱藏確認，請指定 /silentmode 參數。|
|/encrypt | 當磁碟機尚未使用 BitLocker 加密，且需要透過工具加密時，請指定此參數。若已使用 BitLocker 加密磁碟機，則省略此參數並指定 /bk 參數，以提供現有的 BitLocker 金鑰。若您指定 /format 參數，則您也必須指定 /encrypt 參數。 |
|/srcdir:<*SourceDirectory*> | 包含檔案的來源目錄會複製至目標磁碟機。目錄路徑必須是絕對路徑 (而非相對路徑)。|
|/dstdir:<*DestinationBlobVirtualDirectory*> | Microsoft Azure 儲存體帳戶中的目的地虛擬目錄路徑。指定目的地虛擬目錄或 blob 時，請確定使用有效的容器名稱。請記住容器名稱必須是小寫。|

  >[AZURE.NOTE]日誌檔案會在擷取整個工作流程資訊的 WAImportExport 資料夾中進行建立。在 Azure 入口網站中建立匯入工作時，您將需要此檔案。

  ![PowerShell 輸出][4]

### 在 Azure 入口網站中建立匯入工作
1. 在[管理入口網站](https://manage.windowsazure.com/)中瀏覽至您的儲存體帳戶，接著按一下 [**匯入/匯出**]，然後按一下工作窗格中的 [**建立匯入工作**]。<br/> ![入口網站][5]

2. 在精靈的步驟 1 中，指出您已備妥磁碟機並有可用的磁碟機日誌。在精靈的步驟 2 中，提供負責處理此匯入工作的人員連絡資訊。

3. 在步驟 3 中，上傳在上一個章節中您所取得的磁碟機日誌檔案。

4. 在步驟 4 中，輸入匯入工作的描述性名稱。請注意，您輸入的名稱只能包含小寫字母、數字、連字號和底線，必須以字母開頭，且不得包含空格。當工作正在進行中和一旦完成後，您將使用您所選的名稱來進行追蹤。

5. 接著，從清單中選取資料中心區域。資料中心區域將會指出您包裹必須送達的資料中心和地址。<br/> ![DC][6]

6. 在步驟 5 中，從清單中選取您的寄回貨運公司，並輸入貨運公司客戶編號。當匯入工作完成時，Microsoft 會透過此廠商將磁碟機寄還給您。

7. 運送磁碟，並輸入追蹤號碼以便追蹤運送狀態。一旦磁碟到達資料中心，其會複製到儲存體帳戶並更新狀態。<br/>

![完成狀態][7]

### 完成工作流程
一旦初始備份資料可供您的儲存體帳戶使用之後，Azure 備份代理程式會將此帳戶的資料內容複製至多個承租的備份儲存體帳戶中。在下一步的排程備份時間，Azure 備份代理程式會透過初始備份複本執行增量備份。

## 後續步驟
+ 若您有關於 Azure 匯入/匯出工作流程的任何問題，請參閱此[文章](../storage-import-export-service.md)。

+ 若您有關於工作流程的任何問題，請參閱 Azure 備份[常見問題集](backup-azure-backup-faq.md)的＜離線備份＞章節

<!--Image references-->
[1]: ./media/backup-azure-backup-import-export/importscreen.png
[2]: ./media/backup-azure-backup-import-export/backupnow.png
[3]: ./media/backup-azure-backup-import-export/opbackupnow.png
[4]: ./media/backup-azure-backup-import-export/psoutput.png
[5]: ./media/backup-azure-backup-import-export/azureportal.png
[6]: ./media/backup-azure-backup-import-export/dc.png
[7]: ./media/backup-azure-backup-import-export/complete.png
[8]: ./media/backup-azure-backup-import-export/dpmoffline.png
[9]: ./media/backup-azure-backup-import-export/dpmbackupnow.png
 

<!---HONumber=62-->