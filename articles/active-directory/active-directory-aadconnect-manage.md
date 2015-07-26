<properties 
	pageTitle="管理 Azure AD Connect" 
	description="了解如何針對 Azure AD Connect 擴充預設組態和作業工作。" 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/02/2015" 
	ms.author="billmath"/>

# 管理 Azure AD Connect 


<center><div class="dev-center-tutorial-selector sublanding">
<a href="/zh-tw/documentation/articles/active-directory-aadconnect/" title="它是什麼？" class="current">它是什麼？</a> <a href="/zh-tw/documentation/articles/active-directory-aadconnect-how-it-works/" title="運作方式">運作方式</a> <a href="/zh-tw/documentation/articles/active-directory-aadconnect-get-started/" title="開始使用">開始使用</a> <a href="/zh-tw/documentation/articles/active-directory-aadconnect-get-manage/" title="">title管理title管理</a></div></center>


這些主題是進階作業主題，可讓您自訂 Azure Active Directory Connect 以符合您的組織需要和需求。

## 變更預設組態
在大部分的情況下，Azure AD Connect 的預設組態就足以將內部部署目錄輕鬆地擴充到雲端。不過，有時，您可能需要修改預設值，並將其調整成符合您組織的商務邏輯。在這些情況下，您可以修改預設組態，不過，在這麼做之前，您需要注意一些事項。

如果您是從 Azure AD Sync 或 DirSync 升級或移動，請注意下列事項：

- 將 Azure AD Sync 升級到較新版本之後，大部分的設定將會重設回預設值。
- 套用升級之後，「非預設」同步處理規則的變更都會遺失。
- 在升級至較新版本期間，會重建已刪除的「非預設」同步處理規則。
- 套用較新版本的升級時，您已建立的自訂同步處理規則會保持不變。

需要變更預設組態時，請執行下列動作：

- 需要修改「非預設」同步處理規則的屬性流程時，請不要進行變更。而是，建立具有較高優先順序 (較低數值) 且包含必要屬性流程的新同步處理規則。
- 使用同步處理規則編輯器，匯出您的自訂同步處理規則。這提供可用來在災害復原情況下輕鬆地重建它們的 PowerShell 指令碼。
- 如果您需要變更「非預設」同步處理規則中的範圍或加入設定，請記錄該項目，並在升級至較新版本的 Azure AD Connect 之後重新套用變更。






 

## 使用同步處理規則編輯器

在 Azure AD Connect 中，設定同步處理規則，即可設定和微調 Azure AD 與內部部署目錄之間的物件和屬性流程。同步處理規則可以使用同步處理規則編輯器進行設定。安裝 Azure AD Connect 時，會安裝同步處理規則編輯器。若要使用編輯器，您必須是 ADSyncAdmins 群組或 Azure AD Connect 安裝期間所指定系統管理員群組的成員。

在下面的擷取畫面中，您會看到使用快速安裝安裝 Azure AD Connect 時，針對您組態所建立的所有同步處理規則。表格中的每一行都是一個同步處理規則。在 [規則類型] 的左邊，會列出兩種不同類型：[輸入] 和 [輸出]。[輸入] 和 [輸出] 來自 Metaverse 的檢視。也就是說，將我們目錄中的資訊帶到 Metaverse 中。「輸出」指的是用來將資訊和屬性傳送至目錄 (例如內部部署 Active Directory 或 Azure AD) 的規則。

<center>![同步處理規則編輯器](./media/active-directory-aadconnect-manage/Synch_Rule.png) </center>

若要建立新的規則，您可以選取 [新增規則]，然後設定規則。例如，假設我們想要建立加入規則，其中，將利用具有相同電話號碼的 Metaverse 物件加入內部部署目錄中的任何使用者。若要這麼做，請建立新規則，並指定已連線系統 (在我們的案例中，是 contoso.com)、已連線系統物件類型 (user)、Metaverse 物件類型 (person)，以及連結類型「加入」。

<center>![建立同步處理規則](./media/active-directory-aadconnect-manage/synch2.png) </center>


然後，在 [加入規則] 畫面上，於 [來源] 屬性下指定 telephoneNumber，並在 [目標] 屬性下指定 telephoneNumber。這樣就可以了。您現在已順利建立加入規則。

<center>![加入規則](./media/active-directory-aadconnect-manage/synch3.png) </center>

您可以使用同步處理規則編輯器套用非預設組態的其他商務邏輯，並將它調整成符合您的組織需求。如需同步處理規則編輯器的詳細資訊，請參閱[了解預設組態](https://msdn.microsoft.com/library/azure/dn800963.aspx)。


## 使用宣告式佈建 
宣告式佈建是「無程式碼」佈建，而且可以使用同步處理規則編輯器進行安裝和設定。您可以使用編輯器設定和建立自己的佈建規則。

「宣告式佈建」的必要部分是屬性流程中所使用的運算式語言。使用的語言是 Microsoft® Visual Basic® for Applications (VBA) 的子集。此語言用於 Microsoft Office，而且擁有 VBScript 經驗的使用者也可以辨識它。宣告式佈建運算式語言只會使用函數，而不是結構化語言；沒有任何方法或陳述式。函數會改為巢狀函數來表示程式流程。

如需運算式語言的詳細資訊，請參閱[了解宣告式佈建運算式](https://msdn.microsoft.com/library/azure/dn801048.aspx)

## 其他文件
針對 Azure AD Sync 所建立的一些文件仍然有關，而且適用於 Azure AD Connect。雖然致力於將本文件帶到 Azure.com，但是這份文件有些部分還是保留在 MSDN 範圍文件庫中。如需其他文件，請參閱 [MSDN 上的 Azure AD Connect](https://msdn.microsoft.com/library/azure/dn832695.aspx) 和 [MSDN 上的 Azure AD Sync](https://msdn.microsoft.com/library/azure/dn790204.aspx)。

**其他資源**

* [在雲端中使用內部部署身分識別基礎結構](../active-directory-aadconnect-whatis.md)
* [Azure AD Connect 運作方式](../active-directory-aadconnect-howitworks.md)
* [開始使用 Azure AD Connect](../active-directory-aadconnect-getstarted.md)
* [管理 Azure AD Connect](active-directory-aadconnect-manage.md)
* [MSDN 上的 Azure AD Connect](https://msdn.microsoft.com/library/azure/dn832695.aspx)
 

<!---HONumber=58-->