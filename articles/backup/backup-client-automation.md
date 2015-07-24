<properties
	pageTitle="使用 Azure PowerShell 部署和管理 Windows Server/用戶端的備份 | Microsoft Azure"
	description="了解如何使用 Azure PowerShell 部署和管理 Azure 備份"
	services="backup"
	documentationCenter=""
	authors="aashishr"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/23/2015"
	ms.author="aashishr"/>


# 使用 Azure PowerShell 部署和管理 Windows Server/Windows 用戶端的 Azure 備份
本文說明如何使用 Azure PowerShell 設定 Windows Server 或 Windows 用戶端上的 Azure 備份，以及管理備份和還原。

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

## 設定和註冊
Azure PowerShell 可以自動化下列設定和註冊工作：

- 安裝 Azure 備份代理程式
- 向 Azure 備份服務進行註冊
- 網路

### 安裝 Azure 備份代理程式
在安裝 Azure 備份代理程式之前，您必須先將安裝程式下載至 Windows Server 上。從 [Microsoft 下載中心](http://aka.ms/azurebackup_agent)可以取得最新版的安裝程式。請將此安裝程式儲存至容易存取的位置，例如 *C:\Downloads*。

若要安裝代理程式，請在已提升權限的 Azure PowerShell 主控台中執行下列命令：

```
PS C:\> MARSAgentInstaller.exe /q
```

這會以所有預設選項安裝代理程式。安裝作業會在背景中進行幾分鐘。如果您沒有指定 */nu* 選項，則安裝結束時會開啟 **Windows Update** 視窗以檢查是否有任何更新。安裝之後，代理程式會顯示在已安裝的程式清單中。

若要查看已安裝的程式清單，請移至 [**控制台**] > [**程式**] > [**程式和功能**]。

![已安裝代理程式](./media/backup-client-automation/installed-agent-listing.png)

#### 安裝選項

若要查看所有可透過命令列執行的選項，請使用下列命令：

```
PS C:\> MARSAgentInstaller.exe /?
```

可用的選項包括：

| 選項 | 詳細資料 | 預設值 |
| ---- | ----- | ----- |
| /q | 無訊息安裝 | - |
| /p:"location" | Azure 備份代理程式的安裝資料夾路徑。 | C:\Program Files\Microsoft Azure Recovery Services Agent |
| /s:"location" | Azure 備份代理程式的快取資料夾路徑。 | C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch |
| /m | 選擇加入 Microsoft Update | - |
| /nu | 安裝完成後不要檢查更新 | - |
| /d | 解除安裝 Microsoft Azure 復原服務代理程式 | - |
| /ph | Proxy 主機位址 | - |
| /po | Proxy 主機連接埠號碼 | - |
| /pu | Proxy 主機使用者名稱 | - |
| /pw | Proxy 密碼 | - |


### 向 Azure 備份服務進行註冊
在可註冊 Azure 備份服務之前，需要請您先確定已符合[先決條件](backup-try-azure-backup-in-10-mins.md)。您必須：

- 具備有效的 Azure 訂用帳戶
- 建立備份保存庫
- 下載保存庫認證並將它儲存在方便的位置 (例如 *C:\Downloads*)。為方便起見，您也可以重新命名保存庫認證。
使用 [Start-OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) Cmdlet 即可向保存庫註冊電腦：

```
PS C:\> Start-OBRegistration -VaultCredentials "C:\Downloads\register.vaultcredentials" -Confirm:$false

CertThumbprint      : 7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName : test-vault
Region              : Australia East

Machine registration succeeded.
```

> [AZURE.IMPORTANT]請勿使用相對路徑來指定保存庫認證檔。您必須提供絕對路徑做為 Cmdlet 的輸入。

### 網路
若 Windows 電腦是透過 Proxy 伺服器連線到網際網路，您也可以提供 Proxy 設定給代理程式。本範例未使用 Proxy 伺服器，因此會明確地清除任何 Proxy 相關資訊。

您也可以針對給定的一組當週天數，使用 [```work hour bandwidth```] 和 [```non-work hour bandwidth```] 的選項來控制頻寬使用情形。

使用 [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409%28v=wps.630%29.aspx) Cmdlet 即可設定 Proxy 和頻寬的詳細資料：

```
PS C:\> Set-OBMachineSetting -NoProxy
Server properties updated successfully.

PS C:\> Set-OBMachineSetting -NoThrottle
Server properties updated successfully.
```

### 加密設定
傳送至 Azure 備份的備份資料會進行加密來保護資料的機密性。加密複雜密碼是在還原時用來解密資料的「密碼」。

```
PS C:\> ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force | Set-OBMachineSetting
Server properties updated successfully
```

> [AZURE.IMPORTANT]一旦設定，就請保管好此複雜密碼。若沒有此複雜密碼，您將無法從 Azure 還原資料。

## 備份檔案和資料夾
Windows Server 和用戶端的所有 Azure 備份都經由原則來掌管。原則包含三個部分：

1. **備份排程**，指定何時進行備份並與服務同步。
2. **保留排程**可指定要在 Azure 中保留復原點多久時間。
3. **檔案包含/排除規格**，指出要備份的項目。

本文件中要說明如何將備份自動化，因此我們假設還未設定任何選項。一開始，請先使用 [New-OBPolicy](https://technet.microsoft.com/library/hh770416.aspx) Cmdlet 建立新的備份原則，並加以使用。

```
PS C:\> $newpolicy = New-OBPolicy
```

此時，原則是空的，需要使用其他 Cmdlet 來定義要包含或排除的項目、執行備份的時機，以及儲存備份的位置。

### 設定備份排程
原則 3 部分的第 1 個部分是備份排程，請使用 [New-OBSchedule](https://technet.microsoft.com/library/hh770401) Cmdlet 建立。備份排程會定義何時需要進行備份。建立排程時，您需要指定 2 個輸入參數：

- **星期幾**要執行備份。您可以只選一天或選擇一週的每天都執行備份工作，或任意選取要一週的哪幾天。
- 要執行備份的**時段**。您可以定義多達一天 3 個不同時段來觸發備份。

例如，您可以設定每週六和日下午 4 點執行備份原則。

```
PS C:\> $sched = New-OBSchedule -DaysofWeek Saturday, Sunday -TimesofDay 16:00
```

備份排程需要與原則建立關聯，您可以使用 [Set-OBSchedule](https://technet.microsoft.com/library/hh770407) Cmdlet 來達成。

```
PS C:\> Set-OBSchedule -Policy $newpolicy -Schedule $sched
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s) DsList : PolicyName : RetentionPolicy : State : New PolicyState : Valid
```
### 設定保留原則
保留原則會定義所建立備份工作的復原點保留時間長度。使用 [New-OBRetentionPolicy](https://technet.microsoft.com/library/hh770425) Cmdlet 建立新的保留原則時，您可以使用 Azure 備份來指定需要保留備份復原點的天數。下例將保留原則設為 7 天。

```
PS C:\> $retentionpolicy = New-OBRetentionPolicy -RetentionDays 7
```

> [AZURE.NOTE]PowerShell Cmdlet 目前不支援進行長期保留原則的設定。使用 Azure 備份 UI 主控台來設定長期保留原則。

保留原則必須與主要原則建立關聯，方法為使用 Cmdlet [Set-OBRetentionPolicy](https://technet.microsoft.com/library/hh770405)：

```
PS C:\> Set-OBRetentionPolicy -Policy $newpolicy -RetentionPolicy $retentionpolicy

BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          :
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```
### 包含及排除要備份的檔案
```OBFileSpec``` 物件會定義備份中要包含與排除的檔案。此組規則可劃分出電腦上要保護的檔案和資料夾。您可以設定所需數量的檔案包含或排除規則，並建立其與原則的關聯。建立新的 OBFileSpec 物件時，您可以：

- 指定要包含的檔案和資料夾
- 指定要排除的檔案和資料夾
- 指定要遞迴備份資料夾中的資料，或是否僅備份指定資料夾中最上層的檔案。

後者可利用在 New-OBFileSpec 命令中使用 -NonRecursive 旗標來達成。

在下例中，我們會備份磁碟區 C: 和 D:，並排除 Windows 資料夾和任何暫存資料夾中的作業系統二進位檔。若要這樣做，我們將使用 [New-OBFileSpec](https://technet.microsoft.com/library/hh770408) Cmdlet 建立二個檔案規格 - 一個包含和一個排除。一旦建立檔案規格之後，再使用 [Add-OBFileSpec](https://technet.microsoft.com/library/hh770424) Cmdlet 建立與原則的關聯。

```
PS C:\> $inclusions = New-OBFileSpec -FileSpec @("C:", "D:")

PS C:\> $exclusions = New-OBFileSpec -FileSpec @("C:\windows", "C:\temp") -Exclude

PS C:\> Add-OBFileSpec -Policy $newpolicy -FileSpec $inclusions

BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid


PS C:\> Add-OBFileSpec -Policy $newpolicy -FileSpec $exclusions

BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\windows
                  IsExclude:True
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\temp
                  IsExclude:True
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

### 套用原則
現在原則物件已完成，且具有關聯的備份排程、保留原則及包含/排除的檔案清單。此原則現在已經過認可，適合用於 Azure 備份。套用新建立的原則之前，請使用 [Remove-OBPolicy](https://technet.microsoft.com/library/hh770415) Cmdlet 確認沒有與伺服器未與現有的備份原則相關聯。移除原則時，系統會提示確認。若要略過確認，Cmdlet 中請使用 ```-Confirm:$false```。

```
PS C:\> Get-OBPolicy | Remove-OBPolicy
Microsoft Azure Backup Are you sure you want to remove this backup policy? This will delete all the backed up data. [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
```

若要認可原則物件已完成，請使用 [Set-OBPolicy](https://technet.microsoft.com/library/hh770421) Cmdlet。系統將提示您進行確認。若要略過確認，Cmdlet 中請使用 ```-Confirm:$false```。

```
PS C:\> Set-OBPolicy -Policy $newpolicy
Microsoft Azure Backup Do you want to save this backup policy ? [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s) DsList : {DataSource DatasourceId:4508156004108672185 Name:C:\ FileSpec:FileSpec FileSpec:C:\ IsExclude:False IsRecursive:True ,FileSpec FileSpec:C:\windows IsExclude:True IsRecursive:True ,FileSpec FileSpec:C:\temp IsExclude:True IsRecursive:True
              , DataSource
              DatasourceId:4508156005178868542
              Name:D:\
              FileSpec:FileSpec
              FileSpec:D:\
              IsExclude:False
              IsRecursive:True

              }
PolicyName : c2eb6568-8a06-49f4-a20e-3019ae411bac RetentionPolicy : Retention Days : 7
              WeeklyLTRSchedule :
              Weekly schedule is not set

              MonthlyLTRSchedule :
              Monthly schedule is not set

              YearlyLTRSchedule :
              Yearly schedule is not set
State : Existing PolicyState : Valid
```

您可以使用 [Get-OBPolicy](https://technet.microsoft.com/library/hh770406) Cmdlet 來檢視現有備份原則的詳細資料。您可以使用 [Get-OBSchedule](https://technet.microsoft.com/library/hh770423) Cmdlet (適用於備份排程) 和 [Get-OBRetentionPolicy](https://technet.microsoft.com/library/hh770427) Cmdlet (適用於保留原則) 向下鑽研

```
PS C:\> Get-OBPolicy | Get-OBSchedule
SchedulePolicyName : 71944081-9950-4f7e-841d-32f0a0a1359a ScheduleRunDays : {Saturday, Sunday} ScheduleRunTimes : {16:00:00} State : Existing
PS C:\> Get-OBPolicy | Get-OBRetentionPolicy
RetentionDays : 7 RetentionPolicyName : ca3574ec-8331-46fd-a605-c01743a5265e State : Existing
PS C:\> Get-OBPolicy | Get-OBFileSpec
FileName : * FilePath : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\ FileSpec : D:\ IsExclude : False IsRecursive : True
FileName : * FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\ FileSpec : C:\ IsExclude : False IsRecursive : True
FileName : * FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\windows FileSpec : C:\windows IsExclude : True IsRecursive : True
FileName : * FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\temp FileSpec : C:\temp IsExclude : True IsRecursive : True
```

### 執行臨機操作備份
設定備份原則之後，即會根據排程進行備份。您也可以使用 [Start-OBBackup](https://technet.microsoft.com/library/hh770426) Cmdlet 來觸發臨機操作備份：

```
PS C:\> Get-OBPolicy | Start-OBBackup
Taking snapshot of volumes... Preparing storage... Estimating size of backup items... Estimating size of backup items... Transferring data... Verifying backup... Job completed. The backup operation completed successfully.
```

## 從 Azure 備份還原資料
本節將引導您逐步完成自動化從 Azure 備份復原資料。此工作涉及下列步驟：

1. 挑選來源磁碟區
2. 選擇要還原的備份點
3. 選擇要還原的項目
4. 觸發還原程序

### 挑選來源磁碟區
若要從 Azure 備份還原項目，您必須先識別項目的來源。我們在 Windows Server 或 Windows 用戶端的內容中執行命令，則已經識別電腦。找出來源的下一步是識別包含它的磁碟區。執行 [Get-OBRecoverableSource](https://technet.microsoft.com/library/hh770410) Cmdlet 可以擷取一份這部電腦所備份磁碟區或來源清單。此命令會傳回這部伺服器/用戶端備份的所有來源陣列。

```
PS C:\> $source = Get-OBRecoverableSource
PS C:\> $source FriendlyName : C:\ RecoverySourceName : C:\ ServerName : myserver.microsoft.com
FriendlyName : D:\ RecoverySourceName : D:\ ServerName : myserver.microsoft.com
```

### 選擇要還原的備份點
執行 [Get-OBRecoverableItem](https://technet.microsoft.com/library/hh770399.aspx) Cmdlet 搭配適當的參數可以擷取備份點清單。範例中，我們會選擇來源磁碟區 *D:* 的最新備份點並加以使用來還原特定檔案。

```
PS C:\> $rps = Get-OBRecoverableItem -Source $source[1]
IsDir : False ItemNameFriendly : D:\ ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\ LocalMountPoint : D:\ MountPointName : D:\ Name : D:\ PointInTime : 18-Jun-15 6:41:52 AM ServerName : myserver.microsoft.com ItemSize : ItemLastModifiedTime :
IsDir : False ItemNameFriendly : D:\ ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\ LocalMountPoint : D:\ MountPointName : D:\ Name : D:\ PointInTime : 17-Jun-15 6:31:31 AM ServerName : myserver.microsoft.com ItemSize : ItemLastModifiedTime :
```
物件 ```$rps``` 是備份點陣列。第一個元素是最新備份點，且第 N 個元素是最舊的備份點。為了選擇最新的備份點，我們使用 ```$rps[0]```。

### 選擇要還原的項目
為了識別要還原的正確檔案或資料夾，遞迴地使用 [Get-OBRecoverableItem](https://technet.microsoft.com/library/hh770399.aspx) Cmdlet。如此一來，可單獨使用 ```Get-OBRecoverableItem``` 來瀏覽資料夾的階層。

此例中，如果我們要還原檔案 *finances.xls*，我們可以使用物件 ```$filesFolders[1]``` 來加以參考。

```
PS C:\> $filesFolders = Get-OBRecoverableItem $rps[0]
PS C:\> $filesFolders IsDir : True ItemNameFriendly : D:\MyData\ ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\ LocalMountPoint : D:\ MountPointName : D:\ Name : MyData PointInTime : 18-Jun-15 6:41:52 AM ServerName : myserver.microsoft.com ItemSize : ItemLastModifiedTime : 15-Jun-15 8:49:29 AM
PS C:\> $filesFolders = Get-OBRecoverableItem $filesFolders[0]
PS C:\> $filesFolders IsDir : False ItemNameFriendly : D:\MyData\screenshot.oxps ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\screenshot.oxps LocalMountPoint : D:\ MountPointName : D:\ Name : screenshot.oxps PointInTime : 18-Jun-15 6:41:52 AM ServerName : myserver.microsoft.com ItemSize : 228313 ItemLastModifiedTime : 21-Jun-14 6:45:09 AM
IsDir : False ItemNameFriendly : D:\MyData\finances.xls ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\finances.xls LocalMountPoint : D:\ MountPointName : D:\ Name : finances.xls PointInTime : 18-Jun-15 6:41:52 AM ServerName : myserver.microsoft.com ItemSize : 96256 ItemLastModifiedTime : 21-Jun-14 6:43:02 AM
```

您也可以使用 ```Get-OBRecoverableItem``` Cmdlet 來搜尋要還原的項目。在範例中，為了搜尋 *finances.xls*，我們可以執行下列命令來取得該檔案的控制代碼：

```
PS C:\> $item = Get-OBRecoverableItem -RecoveryPoint $rps[0] -Location "D:\MyData" -SearchString "finance*"
```

### 觸發還原程序
為了觸發還原程序，我們首先需要指定復原選項。使用 [New-OBRecoveryOption](https://technet.microsoft.com/library/hh770417.aspx) Cmdlet 可以完成這項工作。在此例中，我們假設要將檔案還原至 *C:\temp*。我們也假設要略過目的地資料夾 *C:\temp* 中已存在的檔案。為了建立此復原選項，使用下列命令：

```
PS C:\> $recovery_option = New-OBRecoveryOption -DestinationPath "C:\temp" -OverwriteType Skip
```

從 ```Get-OBRecoverableItem``` Cmdlet 輸出，對所選 ```$item``` 使用 [Start-OBRecovery](https://technet.microsoft.com/library/hh770402.aspx) 命令，立即觸發還原：

```
PS C:\> Start-OBRecovery -RecoverableItem $item -RecoveryOption $recover_option Estimating size of backup items... Estimating size of backup items... Estimating size of backup items... Estimating size of backup items... Job completed. The recovery operation completed successfully.
```


## 解除安裝 Azure 備份代理程式
使用下列命令即可解除安裝 Azure 備份代理程式：

```
PS C:\> .\MARSAgentInstaller.exe /d /q
```

若要從電腦解除安裝代理程式二進位檔，請考量下列後果：

- 這會從電腦移除檔案篩選器，並停止追蹤變更。
- 會從電腦移除所有原則資訊，但服務中會繼續保存這些原則資訊。
- 會移除所有備份排程，且不再進行任何備份。

不過，Azure 中儲存的資料會留著，根據您所設定的保留原則設定予以保留。較舊的時間點會自動過時。

## 遠端管理
關於 Azure 備份代理程式、原則和資料來源的所有管理工作，皆可透過 Azure PowerShell 遠端完成。要遠端管理的電腦必須經過正確準備。

依預設，WinRM 服務會設定為手動啟動。但您必須將啟動類型設定為 [*自動*]，並應該啟動該服務。若要驗證 WinRM 服務有在執行，[狀態] 屬性的值應該是 [*執行中*]。

```
PS C:\> Get-Service WinRM

Status   Name               DisplayName
------   ----               -----------
Running  winrm              Windows Remote Management (WS-Manag...
```

應該將 Azure PowerShell 設定為可以遠端執行。

```
PS C:\> Enable-PSRemoting -force
WinRM is already set up to receive requests on this computer.
WinRM has been updated for remote management.
WinRM firewall exception enabled.

PS C:\> Set-ExecutionPolicy unrestricted -force
```

現在可以遠端管理電腦 - 從代理程式的安裝開始。例如，下列指令碼會將代理程式複製到遠端電腦並進行安裝。

```
PS C:\> $dloc = "\REMOTESERVER01\c$\Windows\Temp"
PS C:\> $agent = "\REMOTESERVER01\c$\Windows\Temp\MARSAgentInstaller.exe"
PS C:\> $args = "/q"
PS C:\> Copy-Item "C:\Downloads\MARSAgentInstaller.exe" -Destination $dloc - force

PS C:\> $s = New-PSSession -ComputerName REMOTESERVER01
PS C:\> Invoke-Command -Session $s -Script { param($d, $a) Start-Process -FilePath $d $a -Wait } -ArgumentList $agent $args
```

## 後續步驟
如需 Windows Server/用戶端的 Azure 備份詳細資訊，請參閱 [Azure 備份的簡介](backup-introduction-to-azure-backup.md)

<!---HONumber=62-->