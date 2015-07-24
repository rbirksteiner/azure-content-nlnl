<properties
    pageTitle="設定工作區和管理設定"
    description="了解如何在 Microsoft Azure Operational Insights 中設定工作區和管理設定"
    services="operational-insights"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="operational-insights"
    ms.workload="operational-insights"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="06/09/2015"
    ms.author="banders"/>

# 設定工作區和管理設定

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

若要建立新的 Microsoft Azure Operational Insights 工作區，您要選擇工作區名稱、將工作區與您的帳戶關聯，然後選擇地理位置。Operational Insights 工作區本質上是一個容器，包含帳戶資訊和帳戶的簡單組態資訊。您或組織的其他成員可能會使用多個 Operational Insights 工作區來管理從所有或部份 IT 基礎結構收集而來的不同資料。

工作區建立之後，您就可以使用工作區執行其他工作，例如管理 Operational Insights、在儀表板中檢視您的使用方式資料、選取儲存體帳戶，以及直接連線代理程式或連線到 System Center Operations Manager。而且，您還可以管理每個工作區的設定。


## 我需要多少工作區？
在 Azure 管理入口網站中，一個工作區被視為是一項 Azure 資源。

您可以建立新的工作區，或連結到您先前使用 System Center Operations Manager 所開啟的現有工作區，但是您尚未與 Azure 訂用帳戶關聯 (必須關聯才能計費)。工作區代表資料在 Operational Insights 入口網站中進行收集、彙總、分析以及呈現的層級。您可以選擇有多個工作區，區分來自不同環境和系統的資料；每個 Operations Manager 管理群組 (和所有其代理程式) 或個別 VM/代理程式可以分別只與一個工作區連線。

每個工作區可以有多個相關聯的使用者帳戶，而每個使用者帳戶 (Microsoft 帳戶或組織帳戶) 可以存取多個 Operational Insights 工作區。根據預設，用來建立工作區的 Microsoft 帳戶或組織帳戶會變成工作區的管理員。然後管理員可以邀請其他 Microsoft 帳戶或從其 Azure Active Directory 挑選使用者。

## 將現有的工作區連結到 Azure 訂用帳戶

您可以從 [opinsights.azure.com](http://opinsights.azure.com) 建立工作區。不過，這些工作區有某些限制，如果您使用免費帳戶，最明顯的限制是一天最多上傳 500MB 的資料。若要對此工作區進行變更，您必須**將您現有的工作區連結到 Azure 訂用帳戶**。

>[AZURE.IMPORTANT]如果要連結工作區，您的 Azure 帳戶必須已經能夠存取您想要連結的工作區。換句話說，您用來存取 Azure 入口網站的帳戶必須與您用來存取 Operational Insights 工作區的帳戶**相同**。如果不是，請參閱[新增使用者到現有的工作區](#add-an-azure-active-directory-organization-to-an-existing-workspace)。

1. 登入 Azure 管理入口網站。
2. 在入口網站左下方按一下 [+ 新增]。
3. 按一下 [應用程式服務]，捲動到 [Operational Insights] 並加以選取。
4. 按一下 [快速建立]。
5. 在 [帳戶] 清單中，您應該會看到*尚未*連結到您 Azure 訂用帳戶的現有工作區清單。選取帳戶。

  >[AZURE.NOTE]如果您在這裡沒有看到想要連結的工作區，這表示您的 Azure 訂用帳戶沒有 Operational Insights 工作區的存取權。您必須從您 Operational Insights 工作區內部授與存取權給此帳戶。若要這樣做，請參閱[新增使用者到現有的工作區](#add-a-user-to-an-existing-workspace)。

  ![連結帳戶](./media/operational-insights-setup-workspace/link-account.png) <p> 6.填寫剩下的欄位，然後選取 [建立工作區]。

## 升級工作區為付費方案

Operational Insights 有三種工作區方案類型：**免費**、**標準**和**進階**。如果您使用*免費*方案，有可能會達到 500MB 的資料容量。此時您將需要升級工作區為 '**隨用隨付**'，才能收集超過此限制的資料。您隨時都可以轉換您的方案類型。如需 Operational Insights 定價的詳細資訊，請參閱[定價詳細資料](http://azure.microsoft.com/pricing/operational-insights/)

>[AZURE.IMPORTANT]工作區方案只有在*連結*到 Azure 訂用帳戶時才能變更。如果您在 Azure 中建立了工作區，或者如果您*已經*連結了工作區，可以忽略此訊息。如果您是從 [opinsights.azure.com](http://opinsights.azure.com) 建立工作區，就必須依照[連結現有的工作區到 Azure 訂用帳戶](#link-an-existing-workspace-to-an-Azure-subscription)的步驟。

### 變更方案類型

在 Azure 管理入口網站中，瀏覽到您想要升級的 Operational Insights 工作區：

![級別](./media/operational-insights-setup-workspace/find-workspace.png)

選取此工作區，然後從畫面頂端的索引標籤選取 [級別]

![選取級別](./media/operational-insights-setup-workspace/select-scale.png)

最後，選擇您想要升級的方案，然後按一下 [儲存]。您會在入口網站中看到反映的變更，現在可以收集超過「免費」資料容量的資料。

![選取方案](./media/operational-insights-setup-workspace/plan-select.png)

## 變更工作區名稱

如果您是 Microsoft Azure Operational Insights 工作區的管理員，可以變更工作區的名稱。

### 變更工作區名稱

1. 按一下您的工作區名稱。![工作區名稱](./media/operational-insights-setup-workspace/settings01.png)
2. 按一下設定圖示。![設定圖示](./media/operational-insights-setup-workspace/settings02.png)
3. 在 Operational Insights [設定] 頁面上的 [管理使用者帳戶] 區段，按一下 [管理使用者]。![管理使用者](./media/operational-insights-setup-workspace/settings03.png)
4. 在 Operational Insights 入口網站中，在 [設定] 頁面的 [工作區名稱] 欄位中輸入新的名稱。
5. 按一下 [儲存]。

## 變更使用者資訊

您可以變更與 Operational Insights 使用者關聯的名稱，但是您無法變更與該使用者關聯的 Microsoft 帳戶名稱。

如果是有 Microsoft 帳戶的使用者，您也可以變更通知設定。如果您在整個 Azure Active Directory 中使用了組織帳戶，則在 Operational Insights 中目前無法使用**通知**功能。

### 變更使用者資訊
1. 在 Operational Insights [設定] 頁面的 [使用者資訊] 區段，在 [名字] 和 [姓氏] 欄位中輸入名稱。

2. 如果是 Microsoft 帳戶使用者，變更您的通知設定。根據預設，當有警示產生時，會通知所有帳戶使用者。如果您要讓使用者停止接收這些通知，請清除 [接收新 Operational Insights 警示的電子郵件通知] 選項。

3. 按一下 [儲存]。

## 變更通知設定

根據預設，與 Operational Insights 工作區關聯的所有者都會接收到電子郵件，其中摘要出過去七天所產生的任何組態評估警示。在 [設定] 頁面上，使用者可以控制他們是否要接收這些電子郵件通知。

>[AZURE.NOTE]只有具有 Microsoft 帳戶的使用者才能使用通知。如果您在整個 Azure Active Directory 中使用了組織帳戶，則在 Operational Insights 中目前無法使用**通知**功能。

沒有看到認為應該有的電子郵件？ 請檢查垃圾郵件篩選器。確定來自 *operationalinsights@opinsights.azure.com* 的電子郵件沒有被篩選掉。

1. 在 Operational Insights [設定] 頁面的 [使用者資訊] 區段，清除 [接收新 Operational Insights 警示的電子郵件通知] 選項。

2. 按一下 [儲存]。

## 新增使用者到現有的工作區


使用下列步驟新增使用者或群組到 Operational Insights 工作區。使用者或群組就能夠檢視與此工作區關聯的所有警示並處理警示。

>[AZURE.NOTE]如果您想要從 Azure Active Directory 組織帳戶新增使用者或群組，您必須先確定您已經將 Operational Insights 帳戶與 Active Directory 網域關聯。請參閱[新增 Azure Active Directory 組織到現有的工作區](#)。

### 新增使用者到現有的工作區
1. 按一下您的工作區名稱。
2. 按一下設定圖示。
3. 在 Operational Insights [設定] 頁面上的 [管理使用者帳戶] 區段，按一下 [管理使用者]。![管理使用者](./media/operational-insights-setup-workspace/settings04.png)
4. 在 [管理使用者] 視窗中，按一下 [新增]。![設定頁面](./media/operational-insights-setup-workspace/manage-users01.png)
5. 如果您的 Operational Insights 帳戶與 Azure Active Directory 關聯，請指定 [組織帳戶]。>[AZURE.NOTE]如果您的 Operational Insights 帳戶只使用 Microsoft 帳戶，則不會看到這個步驟。 ![新增使用者帳戶類型](./media/operational-insights-setup-workspace/manage-users02.png)
6. 輸入 Microsoft 帳戶或組織帳戶的新使用者資訊。如果您要新增組織帳戶，可以輸入部份使用者或群組名稱或電子郵件別名，然後按一下 [檢查名稱] 找出特定的使用者或群組。>[AZURE.NOTE]為了獲得最佳的效能結果，請將與單一 Operational Insights 帳戶關聯的 Active Directory 群組數目限制為兩個，一個給管理員，一個給使用者。使用太多群組可能會影響 Operational Insights 的效能。
7. 為這位新的使用者選取角色：[管理員] 或 [使用者]。![新增使用者工作區角色](./media/operational-insights-setup-workspace/manage-users03.png)
8. 按一下 [確定]。

  如果您新增 Microsoft 帳戶，系統將會傳送一封加入您的帳戶的邀請至您提供的電子郵件。使用者依照邀請中的指示加入 Operational Insights 之後，使用者就可以檢視此 Operational Insights 帳戶的警示和帳戶資訊，而且您將能夠在 [管理使用者] 視窗中檢視使用者資訊。如果您新增組織帳戶，使用者就能夠立即存取 Operational Insights。![邀請](./media/operational-insights-setup-workspace/manage-users04.png)


## 新增 Azure Active Directory 組織到現有的工作區

您可以將您的 Operational Insights 工作區與 Azure Active Directory 網域關聯。這樣可讓您直接從 Active Directory 新增使用者到您的 Operational Insights 工作區，不需要另外有 Microsoft 帳戶。

### 新增 Azure Active Directory 組織到現有的工作區

1. 在 Operational Insights 的 [設定] 頁面上，按一下 [新增組織]。![邀請](./media/operational-insights-setup-workspace/add-org.png)
2. 複查有關組織帳戶的資訊，然後按 [下一步]。
3. 輸入您 Azure Active Directory 網域之管理員的識別資訊，然後按一下 [登入]。
4. 按一下 [授與存取權] 讓 Operational Insights 使用您 Active Directory 網域中的識別資訊。![已連結](./media/operational-insights-setup-workspace/ad-existing01.png)


## 編輯現有的使用者帳戶

您可以為與您的 Operational Insights 帳戶關聯的使用者變更帳戶角色。您有下列角色選項：

 - *管理員*：可以管理使用者、檢視和處理所有警示，以及新增和移除伺服器

 - *使用者*：可以檢視和處理所有警示，以及新增和移除伺服器

### 編輯帳戶
1. 在 Operational Insights [設定] 頁面上的 [管理使用者帳戶] 區段，按一下 [管理使用者]。

2. 在 [管理使用者] 視窗中，選取您要變更的使用者名稱，然後按一下 [編輯使用者]。

3. 為這位使用者選取角色：[管理員] 或 [使用者]。

4. 按一下 [確定]。

## 從 Operational Insights 工作區移除使用者

使用下列步驟從 Operational Insights 工作區移除使用者。請注意，這不會關閉使用者的工作區，而會移除使用者與工作區之間的關聯。如果使用者與多個工作區關聯，該使用者還是能夠登入 Operational Insights。

### 從工作區移除使用者

1. 在 Operational Insights [設定] 頁面上的 [管理使用者帳戶] 區段，按一下 [管理使用者]。

2. 在 [管理使用者] 視窗中，按一下您要移除的使用者名稱，然後按一下 [移除使用者]。

3. 按一下 [確定] 確認您要移除該使用者。

## 關閉 Operational Insights 工作區

當您關閉 Operational Insights 工作區時，與您的工作區相關的所有資料，在關閉工作區之後的 30 天內，都會從 Operational Insights 服務刪除。

如果您是管理員，而且有多位使用者與工作區關聯，則這些使用者與工作區之間的關聯將會中斷。如果使用者與其他工作區關聯，則他們可以繼續使用 Operational Insights 的其他工作區。不過，如果使用者沒有與其他工作區關聯，則他們必須建立新的工作區才能使用 Operational Insights。

### 關閉 Operational Insights 工作區

1. 在 Operational Insights [設定] 頁面上的 [關閉工作區] 區段，按一下 [關閉工作區]。

2. 選取其中一個關閉工作區的原因，或者在文字方塊中輸入其他原因。

3. 按一下 [關閉工作區]。
 

<!---HONumber=62-->