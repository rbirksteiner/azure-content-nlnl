
<properties 
	pageTitle="使用 Azure AD Connect 同步處理規則編輯器" 
	description="了解如何使用 Azure AD Connect 同步處理規則編輯器。" 
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
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# Azure AD Connect 同步處理規則編輯器


## 使用同步處理規則編輯器

在 Azure AD Connect 中，設定同步處理規則，即可設定和微調 Azure AD 與內部部署目錄之間的物件和屬性流程。

同步處理規則是一個組態物件，當滿足條件時會有一組屬性進行流動。此規則也會用來說明連接器空間中物件與 Metaverse 中物件的關係 (稱為聯結或相符項目)。同步處理規則具有優先順序，表示它們彼此之間的關係。在優先順序中，具有較低數值的同步處理規則有較高的優先順序；發生屬性流程衝突時，較高的優先順序將會在衝突解決過程中勝出。您可以使用同步處理規則編輯器設定同步處理規則。

在此範例中，我們將了解同步處理規則 “In from AD – User AccountEnabled”。我們將在 SRE 中標示這一行，並選取 [編輯]。同步處理規則具有四個組態區段：說明、範圍篩選器、聯結規則及轉換。

### 說明
第一個區段提供基本資訊，例如名稱和說明。

<center>![聯結規則](./media/active-directory-aadconnect-whats-next-synch-rules-editor/synch1.png) </center>

我們也會尋找一些資訊，例如哪個已連線系統與此規則有關、哪個已連線系統的物件類型適用於此規則，以及 Metaverse 物件的類型。無論來源物件類型為使用者、iNetOrgPerson 或連絡人，Metaverse 物件類型一律須與個人有關。Metaverse 物件類型永遠不會改變，所以它必須以一般類型建立。您可以將連結類型設為 Join、StickyJoin 或 Provision。此設定會與聯結規則共同運作，稍後我們將討論其運作方式。

### 範圍篩選器

範圍篩選器區段是用來設定同步處理規則套用的時機。由於我們目前看到的同步處理規則名稱表示只應針對已啟用使用者套用，因此您必須設定該範圍，切勿將 AD 屬性 userAccountControl 設為位元 2。當我們在 AD 中找到使用者時，如果 userAccountControl 設為十進位值 512 (已啟用一般使用者)，將會套用此規則；但如果發現使用者將 userAccountControl 設為 514 (停用一般使用者) 時，將不會套用此規則。

<center>![聯結規則](./media/active-directory-aadconnect-whats-next-synch-rules-editor/sync2.png) </center>

範圍篩選器具有可以巢狀的群組和子句。必須滿足群組中的所有子句，才能套用同步處理規則。如果多個群組已經過定義時，則必須至少滿足一個群組才能套用該規則。換句話說，系統會在群組間評估邏輯 OR ，而在單一群組中評估邏輯 AND。您可以在輸出至 AAD - 群組聯結的輸出同步處理規則中找到此範例，如下所示。同步處理篩選器有兩個群組：一個適用於安全性群組 (securityEnabled EQUAL True)，另一個則適用於發佈群組 (securityEnabled EQUAL False)。

<center>![聯結規則](./media/active-directory-aadconnect-whats-next-synch-rules-editor/sync3.png) </center>

此規則是用來定義哪些群組應該佈建至 AAD。發佈群組必須啟用郵件，才可使用 AAD 進行同步處理；但是安全性群組不須啟用郵件，即可進行同步處理。此外，您還可以看到其他多個屬性也接受過評估。

###聯結規則
第三個區段是用來設定連接器空間中物件與 Metaverse 中物件的關係。我們先前看過的規則中並沒有任何適用於聯結規則的組態，因此我們將繼續了解 In from AD - User Join。

<center>![聯結規則](./media/active-directory-aadconnect-whats-next-synch-rules-editor/sync4.png) </center>

聯結規則的內容將取決於安裝精靈中選取的對應選項。針對輸入規則，評估作業將會從來源連接器空間中的物件開始進行，接著再評估聯結規則中的各個群組。如果來源物件經評估確實符合 Metaverse 中的一個物件，且 Metaverse 使用其中一個聯結規則，所有物件就會聯結在一起。如果所有規則經評估後沒有任何相符項目，則會使用說明頁面上的連結類型。如果您將此設定設為 Provision，則目標 (Metaverse) 中會建立新的物件。將新物件佈建至 Metaverse，也等同於將物件投影至 Metaverse。只會對聯結規則評估一次。當連接器空間物件和 Metaverse 物件聯結在一起時，只要仍滿足同步處理規則的範圍，兩者就會維持聯結狀態。評估同步處理規則時，只有一個具有已定義聯結規則的同步處理規則必須在範圍內。如果發現多個具有聯結規則的同步處理規則用於單一物件，就會擲回錯誤。基於這個原因，最佳作法就是在多個同步處理規則都在同一個範圍內用於單一物件時，只有一個具有已定義聯結的同步處理規則。在 Azure AD Connect 的現成組態中，您可以查看規則名稱來找到這些規則，並發現它們的名稱結尾都有 Join 一詞。如果其他同步處理規則將物件聯結在一起，或在目標中佈建新物件，則不具聯結規則的同步處理規則就會套用屬性流程。

###轉換
轉換區段會定義所有屬性流程，且當物件呈現聯結狀態並滿足範圍篩選器時，該流程就會套用至目標物件。回到 In from AD - User AccountEnabled 同步處理規則時，我們會看見下列轉換：

<center>![聯結規則](./media/active-directory-aadconnect-whats-next-synch-rules-editor/sync5.png) </center>

若要將此轉換放在內容中，則在 Account-Resource 樹系部署中，我們預期會在帳戶樹系中找到啟用的帳戶，並在具有 Exchange 和 Lync 設定的資源樹系中找到停用的帳戶。我們目前看到的同步處理規則包含登入所需的屬性，且想要讓這些屬性從找到已啟用帳戶的樹系流出。這些屬性流程會一起放在單一同步處理規則中。轉換可具有不同類型：Constant、Direct 和 Expression。Constant 流程會一律流動特定值，所以在上述例子中，我們會一律將 Metaverse 屬性中名為 accountEnabled 的值設為 True。Direct 流程會將來源中的屬性值流動至目標屬性。第三個流程類型是 Expression，可讓您使用更為進階的組態。由於運算式語言為 VBA (Visual Basic for Applications)，因此具有 Microsoft Office 或 VBScript 使用經驗的使用者就能辨認其格式。屬性會包在方括號之中，例如 [attributeName]。屬性名稱與函數名稱有區分大小寫，但是當運算式無效時，同步處理規則會對運算式進行評估，並且發出警告。所有運算式都會以一行巢狀函式表示。若要發揮組態語言的功能，請使用插入其他註解的 pwdLastSet 流程：

		// If-then-else
		IIF(
		// (The evaluation for IIF) Is the attribute pwdLastSet present in AD? 
		IsPresent([pwdLastSet]),
		// (The True part of IIF) If it is, then from right to left, convert the AD time format to a .Net datetime, change it to the time format used by AAD, and finally convert it to a string.
		CStr(FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")),
		// (The False part of IIF) Nothing to contribute
		NULL
		)

轉換主題相當龐大，它會盡可能使用 Azure AD Connect 提供大部分的自訂組態。在此概觀文件中，我們將不會討論自訂組態；但稍後我們會在此文件中討論其他屬性流程。
 

<!---HONumber=62-->