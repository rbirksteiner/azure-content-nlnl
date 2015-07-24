<properties 
   pageTitle="StorSimple 安全性" 
   description="說明保護 StorSimple 服務、裝置和資料的安全性和隱私權功能。" 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="AdinaH" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="03/19/2015"
   ms.author="v-sharos"/>

# StorSimple 安全性 

## 概觀

安全性是任何人在採用新技術時的主要考量，尤其是當技術用於機密或專屬資料時。評估不同的技術時，您必須考慮因為資料保護所增加的風險和成本。Microsoft Azure StorSimple 提供資料保護的安全性和隱私權解決方案，以協助確保：

- **機密性** – 只有已授權的實體才能檢視您的資料。 
- **完整性** – 只有已授權的實體才能修改或刪除您的資料。

Microsoft Azure StorSimple 解決方案包含四個彼此互動的主要元件：

- **在 Microsoft Azure 中託管的 StorSimple Manager 服務** – 可用來設定和佈建 StorSimple 裝置的管理服務。
- **StorSimple 裝置** – 可安裝在您的資料中心的實體裝置。所有產生資料的主機和用戶端都會連接至 StorSimple 裝置，此裝置會管理資料，並將它依適當情況移至 Azure 雲端。
- **連接至裝置的用戶端/主機** – 在基礎結構中，連接至 StorSimple 裝置並產生需要被保護的資料的用戶端。
- **雲端儲存體** – Azure 雲端中儲存資料的位置。

下列各節說明可協助保護各個元件及其中資料的 StorSimple 安全性功能。它還包含您可能會遇到的 Microsoft Azure StorSimple 安全性問題清單及其對應解答。

## StorSimple Manager 服務保護

StorSimple Manager 服務是裝載於 Microsoft Azure 的管理服務，可用來管理您組織所採購的所有 StorSimple 裝置。您可以透過網頁瀏覽器，並使用您的組織認證登入 Azure 管理入口網站的方式來存取 StorSimple Manager 服務。

若要存取 StorSimple Manager 服務，您的組織需有內含 StorSimple 的 Azure 訂閱。您的訂閱可控管您在 Azure 入口網站的存取功能。如果您的組織沒有 Azure 訂用帳戶，但您想要了解更多相關資訊，請參閱[以組織身分註冊 Azure](../sign-up-organization.md)。

因為 StorSimple Manager 服務裝載於 Azure 中，所以它會受到 Azure 安全性功能的保護。如需有關 Microsoft Azure 所提供的安全性功能的詳細資訊，請移至 [Microsoft Azure 信任中心](http://azure.microsoft.com/support/trust-center/security/)。

## StorSimple 裝置保護

StorSimple 裝置是包含固態硬碟 (SSD) 和硬碟 (HDD) 的內部部署混合式存放裝置，提供備援控制器和自動容錯移轉功能。控制器可管理儲存體分層、將目前使用的 (或熱) 資料放在本機儲存體 (在 StorSimple 裝置或在內部部署伺服器中)，而將不常使用的資料移到雲端。

## 透過裝置註冊控制 StorSimple 存取權

只有已授權的 StorSimple 裝置才能加入您在 Azure 訂閱中建立的 StorSimple Manager 服務。

若要授權裝置，您必須提供服務註冊金鑰才能向 StorSimple Manager 服務註冊該裝置。服務註冊金鑰是入口網站中所產生的 128 位元隨機金鑰。若要了解如何取得服務註冊金鑰，請移至[步驟 2：取得服務註冊金鑰](https://msdn.microsoft.com/library/azure/dn772346.aspx)。

> [AZURE.NOTE]服務登錄機碼是包含 100 個以上字元的長金鑰。建議您複製金鑰並將它以文字檔方式儲存在安全的位置中，如有必要，您可以使用此金鑰來授權其他裝置。
> 
> * 如果您在註冊完第一個裝置之後遺失服務註冊金鑰，您可以透過 StorSimple Manager 服務產生新的金鑰。這不會對現有裝置的作業有任何影響。 
> * 裝置註冊完後，它會使用權杖與 Microsoft Azure 通訊。裝置註冊後便不會用到服務註冊金鑰。


## 透過密碼保護您的 StorSimple 解決方案

密碼是電腦安全性重要的一環，且廣泛用於 Microsoft Azure StorSimple 解決方案，可協助確保只有授權使用者才能存取您的資料。StorSimple 可讓您設定下列密碼：

- Windows PowerShell for StorSimple 密碼
- StorSimple 裝置系統管理員密碼
- Challenge Handshake 驗證通訊協定 (CHAP) 啟動器和目標密碼
- StorSimple Snapshot Manager 密碼

### Windows PowerShell for StorSimple 和 StorSimple 裝置系統管理員密碼

Windows PowerShell for StorSimple 是一個可讓您管理 StorSimple 裝置的命令列介面。Windows PowerShell for StorSimple 的功能包括：可讓您註冊您的裝置、在您的裝置上設定網路介面、安裝特定類型的更新，以及透過存取支援工作階段及變更裝置狀態來疑難排解您的裝置。藉由連接至序列主控台或使用 Windows PowerShell 遠端處理，您可以存取 Windows PowerShell for StorSimple。

您可以透過 HTTPS 或 HTTP 執行 PowerShell 遠端處理。如果已啟用透過 HTTPS 進行遠端管理，則您必須從裝置下載遠端管理憑證，並將它安裝在遠端用戶端。

> [AZURE.IMPORTANT]

> * 遠端管理依預設為關閉。您可以使用 StorSimple Manager 服務將其啟用。最佳的安全性做法是，您應該只在真正需要遠端存取的期間才將它啟用。
> * 如果您變更密碼，請務必通知所有遠端存取使用者，他們才不會遇到非預期的連線中斷。
> * StorSimple Manager 服務無法擷取現有的密碼，只能重設密碼。建議您將所有密碼儲存在安全的地方，讓您在忘記密碼時無需重設密碼。如果您需要重設密碼，重設之前請務必通知所有使用者。 
> * 您可以使用裝置的序列連接存取 Windows PowerShell 介面。您也可以使用提供額外安全性的 HTTP 或 HTTPS 遠端存取該介面。HTTPS 提供比序列或 HTTP 連線更高層級的安全性。不過，若要使用 HTTPS，您必須首先在將存取裝置的用戶端電腦上安裝憑證。您可以從 StorSimple Manager 服務中的 [裝置設定] 頁面下載遠端存取憑證。如果遠端存取的憑證遺失，則您必須下載新的憑證，並將它散佈到所有授權使用遠端管理的用戶端。

使用 Windows PowerShell for StorSimple 連接至裝置之後，您必須提供裝置系統管理員密碼才能登入裝置。

### Challenge Handshake 驗證通訊協定 (CHAP) 啟動器和目標密碼

CHAP 是 StorSimple 裝置用來驗證遠端用戶端身分識別的一種驗證配置。此驗證會以共用密碼為基礎。CHAP 可以是單向 (單向) 或相互 (雙向)。使用單向 CHAP 時，目標 (StorSimple 裝置) 會驗證啟動器 (主機)。相互 CHAP 會要求目標驗證啟動器，然後啟動器驗證目標。您可以設定 Azure StorSimple 使用任一種方法。

> [AZURE.IMPORTANT]
> 
> * CHAP 使用者名稱不得超過 233 個字元。
> * CHAP 密碼必須介於 12 到 16 個字元。嘗試使用較長的使用者名稱或密碼將會導致 Windows 主機上發生驗證錯誤。
> * 您不能針對 CHAP 啟動器和 CHAP 目標使用相同的密碼。
> * 設定密碼之後，您可以加以變更但無法擷取。如果密碼已變更，請務必通知所有遠端存取使用者，好讓他們能夠順利連接至 StorSimple 裝置。

### StorSimple Snapshot Manager 密碼

StorSimple Snapshot Manager 是一個 Microsoft Management Console (MMC) 嵌入式管理單元，可使用磁碟區群組和 Windows 磁碟區陰影複製服務產生應用程式一致備份。此外，您可以使用 StorSimple Snapshot Manager 建立備份排程及複製或還原磁碟區。

設定裝置使用 StorSimple Snapshot Manager 時，您必須提供 StorSimple Snapshot Manager 密碼。此密碼是註冊期間，您在 Windows PowerShell for StorSimple 中設定的第一個密碼。您也可以從 StorSimple Manager 服務來設定及變更此密碼。此密碼可向 StorSimple Snapshot Manager 驗證裝置。

> [AZURE.IMPORTANT]<ul><li>此密碼必須介於 14 到 15 個字元，且必須包含 3 個以上的大寫、小寫、數字和特殊字元的組合。</li><li>設定 StorSimple Snapshot Manager 密碼之後，您可加以變更，但無法擷取。如果您變更密碼，請務必通知所有遠端使用者。</li></ul>


### 密碼最佳作法

建議您使用下列指導方針，以協助確保 Azure StorSimple 密碼強度夠強並且受到嚴密保護：

- 每三個月變更您的密碼。
- 使用強式密碼。如需詳細資訊，請移至 [建立強式密碼]。
- 針對不同的存取機制一律使用不同的密碼。您所指定的每個密碼都應該是唯一的。
- 請勿與未經授權存取 StorSimple 裝置的任何人分享密碼。
- 請勿在其他人面前談論密碼或提示密碼的格式。
- 如果您懷疑帳戶或密碼被盜，請向資訊安全部門回報此事件。
- 將所有密碼視為敏感的機密資訊。 

## StorSimple 資料保護

本節說明可保護傳輸中和儲存資料的 Azure StorSimple 安全性功能。

如其他章節所述，在使用密碼授權和驗證使用者之後，這些使用者才能存取您的 Microsoft Azure StorSimple 解決方案。另一個安全性考量是，在儲存系統之間傳輸以及儲存資料時，如何保護資料以防止未經授權使用者進行存取。下列各節說明 Azure StorSimple 所提供的資料保護功能。

> [AZURE.NOTE]針對儲存在 StorSimple 裝置和 Microsoft Azure 儲存體中的資料，重複資料刪除提供額外的保護。刪除重複資料時，資料物件會與用來對應和存取這些資料物件的中繼資料分開儲存：沒有可用的儲存層級內容可用來根據磁碟區結構、檔案系統或檔案名稱重新建構資料。

## 保護流經服務的資料

StorSimple Manager 服務的主要目的是管理和設定 StorSimple 裝置。StorSimple Manager 服務可在 Microsoft Azure 中執行。您可以使用 Azure 管理入口網站來輸入裝置設定資料，接著 Microsoft Azure 會使用 StorSimple Manager 服務將資料傳送到裝置。StorSimple Manager 服務會使用一個非對稱金鑰組的系統，以協助您確保 Azure 服務遭到入侵並不會導致儲存資訊的洩漏。非對稱金鑰系統可協助保護流經服務的資料，如下所示：

1. 使用非對稱公開和私用金鑰組的資料加密憑證會在裝置上產生，並用來保護資料的安全。註冊第一個裝置時即會產生金鑰。 
2. 資料加密憑證金鑰會匯出成為受服務資料加密金鑰 (也就是強式 128 位元金鑰，會在註冊期間由裝置隨機產生) 保護的個人資訊交換 (.pfx) 檔案。
3. 憑證的公開金鑰會以安全的方式提供給 StorSimple Manager 服務，而私密金鑰仍屬裝置所有。
4. 輸入服務的資料會使用公開金鑰進行加密，並使用儲存在裝置上的私密金鑰進行解密，以確保 Azure 服務無法對流向裝置的資料進行解密。

> [AZURE.IMPORTANT]
> 
> * 只會針對第一個向服務註冊的裝置產生服務資料加密金鑰。向服務註冊的所有後續裝置則必須使用相同的服務資料加密金鑰。請務必製作一份此金鑰的副本，並將它儲存在安全的位置。儲存服務資料加密金鑰的副本時，必須讓已經授權的人員可以進行存取，並可以輕鬆地將它傳送到裝置系統管理員。
> * 您可以透過選取服務儀表板上的 [變更服務資料加密金鑰] 選項，變更服務資料加密金鑰及對應的資料加密憑證。變更加密金鑰時，必須使用新的金鑰更新所有裝置。因此，建議您在所有裝置都在線上時變更金鑰。如果裝置處於離線狀態，您可以在其他時間變更金鑰。金鑰已過期的裝置仍然可以執行備份，但在金鑰更新之前將無法還原資料。如需詳細資訊，請移至[服務儀表板](https://msdn.microsoft.com/library/azure/dn772326.aspx)。
> * 若要確保資料安全性不會受到危害，您必須使用實體 StorSimple 裝置變更服務資料加密金鑰。
> * 如果服務資料加密金鑰遺失，Microsoft 支援人員可協助您擷取該金鑰，但前提是您至少要有一個裝置處於線上狀態。建議您在擷取服務資料加密金鑰後將其變更。如需相關指示，請移至[變更服務資料加密金鑰](https://msdn.microsoft.com/library/azure/8158cbe9-1f26-4513-a031-49f88bb3d481#sec01)。
> * 服務資料加密金鑰和資料加密憑證不會過期。不過，建議您定期變更服務資料加密金鑰，以防金鑰洩露。</li></ul>


## 保護靜態資料的安全

StorSimple 裝置會根據使用頻率，將資料儲存在本機階層和雲端中加以管理。連線到裝置的所有主機電腦會將資料都傳送到裝置，然後裝置會視需要將資料移至雲端。透過 iSCSI 通訊協定，將資料從裝置傳送至雲端。每個裝置都會有一個可瀏覽其上所有共用磁碟區的 iSCSI 目標。所有資料在傳送至雲端儲存體之前都會進行加密。為了協助確保移至雲端資料的安全性和完整性，Azure StorSimple 可讓您定義雲端儲存體加密金鑰，如下所示：

- 建立磁碟區容器時，您可以指定雲端儲存體加密金鑰。您無法修改或稍後新增金鑰。 
- 磁碟區容器中的所有磁碟區會共用相同的加密金鑰。如果您要對特定磁碟區使用不同加密形式，建議您建立新的磁碟區容器來裝載該磁碟區。
- 在 StorSimple Manager 服務中輸入雲端儲存體加密金鑰時，此金鑰會使用服務資料加密金鑰的公開部分進行加密，然後傳送至裝置。
- 雲端儲存體加密金鑰不會儲存在服務中的任何地方，只有此裝置才知道儲存位置。
- 指定雲端儲存體加密金鑰是選用選項。您可以將已在主機上加密的資料傳送至裝置。

## 透過儲存體帳戶保護資料安全

每個 Microsoft Azure 訂閱可以建立一或多個儲存體帳戶。(儲存體帳戶會提供唯一的命名空間，以供儲存在 Azure 雲端中的資料使用)。 儲存體帳戶的存取權會受到與該儲存體帳戶相關聯的訂閱和存取金鑰控制。

建立儲存體帳戶時，Microsoft Azure 會產生兩個 512 位元儲存體存取金鑰，當 StorSimple 裝置存取儲存體帳戶時，可以使用其中一個進行驗證。請注意，這些金鑰中只有一個會是使用中狀態。其他金鑰會被保留，讓您可以定期輪替金鑰。若要輪替金鑰，您必須將次要金鑰的狀態設定為使用中，然後刪除主要金鑰。然後，您可以建立要在下一個輪替期間使用的新金鑰。(基於安全性理由，許多資料中心需要金鑰輪替)。

> [AZURE.IMPORTANT]
> 
> 建議您按照這些最佳作法進行金鑰輪替：
> 
> * 請時常輪替儲存體帳戶金鑰，以確保未經授權的使用者無法存取您的儲存體帳戶。
> * Azure 系統管理員應該定期變更或重新產生主要或次要金鑰，方法是使用管理入口網站的 [儲存體] 區段，直接存取儲存體帳戶。


## 透過加密保護資料安全

StorSimple 會使用下列加密演算法，來保護儲存在 StorSimple 解決方案中或在 StorSimple 解決方案元件之間流動的資料。

| 演算法 | 金鑰長度 | 通訊協定/應用程式/註解 |
| --------- | ---------- | ------------------------------- |
| RSA | 2048 | 管理入口網站會使用 RSA PKCS 1 v1.5 加密傳送至裝置的組態資料：例如儲存體帳戶認證、StorSimple 裝置組態，及雲端儲存體加密金鑰。 |
| AES | 256 | AES with CBC 可用來加密服務資料加密金鑰的公開部分，之後才將該金鑰從 StorSimple 裝置傳送至管理入口網站。StorSimple 裝置也會用它來加密資料，之後才將資料傳送至雲端儲存體帳戶。 |
| RSA | 2048 | 管理入口網站會使用 RSA PKCS 1 v1.7 加密服務資料加密金鑰的公開部分，之後才將該金鑰儲存在本機入口網站的儲存體中 |


## 常見問題集 (FAQ)

下面是一些有關安全性與 Microsoft Azure StorSimple 的問題和解答。

**問：**我的服務遭到入侵。我接下來該怎麼做？

**答：**請立即變更服務資料加密金鑰和用於階層資料的儲存體帳戶金鑰。如需相關指示，請移至：

- [變更服務資料加密金鑰](https://msdn.microsoft.com/library/azure/8158cbe9-1f26-4513-a031-49f88bb3d481#sec01)
- [儲存體帳戶的金鑰輪替](https://msdn.microsoft.com/library/azure/1747f56e-858a-4cfe-a020-949d7db23b8b#rotate)

**問：**我的新 StorSimple 裝置要求提供服務註冊金鑰。該如何擷取此金鑰？

**答：**當您最初建立 StorSimple Manager 服務時，此金鑰就已建立。使用 StorSimple Manager 服務連接至裝置時，您可以透過快速啟動頁面來檢視或重新產生服務註冊金鑰。產生新的服務註冊金鑰不會影響現有的已註冊裝置。如需相關指示，請移至：

- [檢視或重新產生服務註冊金鑰](https://msdn.microsoft.com/library/azure/8158cbe9-1f26-4513-a031-49f88bb3d481#BKMK_viewsrk)

**問：**我的服務資料加密金鑰不見了。該怎麼辦？

**答：**請連絡 Microsoft 支援服務。他們可以登入您裝置上的支援工作階段，協助您擷取金鑰。您取得服務資料加密金鑰之後，請立即變更，以確保只有您自己知道新的金鑰。如需相關指示，請移至：

- [變更服務資料加密金鑰](https://msdn.microsoft.com/library/azure/8158cbe9-1f26-4513-a031-49f88bb3d481#sec01)

**問：**我授權某個裝置進行服務資料加密金鑰變更，但無法開始金鑰變更程序。我該怎麼辦？

**答：**如果逾時期間已到期，您必須對該裝置重新授權，它才能進行服務資料加密金鑰變更，並重新開始程序。

**問：**我變更了服務資料加密金鑰，但 4 小時內無法更新其他裝置。是否必須重新啟動？

**答：**4 小時的期間僅適用於啟動變更。在已經授權的 StorSimple 裝置上啟動更新程序之後，在所有裝置更新之前此授權都是有效的。

**問：**我們的 StorSimple 系統管理員已離職。我該怎麼辦？

**答：**變更和重設允許存取 StorSimple 裝置的密碼，並變更服務資料加密金鑰，以確保未經授權的人員不會知道新的資訊。如需相關指示，請移至：

- [設定 StorSimple Snapshot Manager](https://msdn.microsoft.com/library/azure/02f1412f-e196-4a88-8eda-2113247ea47c#sec08)
- [設定裝置系統管理員密碼](https://msdn.microsoft.com/library/azure/02f1412f-e196-4a88-8eda-2113247ea47c#sec09)
- [設定遠端管理](https://msdn.microsoft.com/library/azure/02f1412f-e196-4a88-8eda-2113247ea47c#sec10)
- [設定 CHAP 起始端](https://msdn.microsoft.com/library/azure/02f1412f-e196-4a88-8eda-2113247ea47c#sec06)
- [設定 CHAP 目標](https://msdn.microsoft.com/library/azure/02f1412f-e196-4a88-8eda-2113247ea47c#sec07)

**問：**我想要提供 StorSimple Snapshot Manager 的密碼給連接至 StorSimple 裝置的主機，但找不到密碼。我該怎麼辦？

**答：**如果忘記密碼，請建立新密碼。然後，請務必通知所有現有的使用者密碼已變更，並要求他們更新其用戶端以使用新密碼。如需相關指示，請移至：

- [設定快照集管理員](https://msdn.microsoft.com/library/azure/02f1412f-e196-4a88-8eda-2113247ea47c#sec08)
- [驗證裝置](https://msdn.microsoft.com/library/azure/dn790537.aspx)

**問：**裝置上用於遠端存取 Windows PowerShell for StorSimple 的憑證已變更。如何更新遠端存取用戶端？

**答：**您可以從 StorSimple Manager 服務下載新的憑證，然後提供它以安裝在遠端存取用戶端的憑證存放區。如需相關指示，請移至：

- [Import-Certificate cmdlet](https://technet.microsoft.com/library/hh848630.aspx)

**問：**如果 StorSimple Manager 服務遭到入侵，資料是否仍受到保護？

**答：**當您在網頁瀏覽器中檢視服務組態資料時，該資料一律會以您的公開金鑰加密。因為服務無私密金鑰的存取權，所以無法看到任何資料。StorSimple Manager 服務遭到入侵也不會有任何影響，因為 StorSimple Manager 服務中不會儲存任何金鑰。

**問：**如果有人取得資料加密憑證的存取權，是否會洩露我的資料？

**答：**Microsoft Azure 會以加密格式儲存客戶的資料加密金鑰 (.pfx 檔案)。因為 .pfx 檔案已加密，且 StorSimple 服務沒有服務資料加密金鑰可解密 .pfx 檔案，所以只是存取 .pfx 檔案並不會曝露任何機密。

**問：**如果政府機構要求 Microsoft 提供我的資料會如何？

**答：**因為服務上的所有資料都已加密，且私密金鑰會和裝置一起保管，所以政府機構必須要求客戶提供資料。

## 後續步驟

[開始使用實體裝置](https://msdn.microsoft.com/library/azure/dn772410.aspx)
 

<!---HONumber=July15_HO2-->