<properties
   pageTitle="在邏輯應用程式中使用 Box 連接器"
   description="在邏輯應用程式中使用 Box 連接器"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="rajeshramabathiran"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="03/20/2015"
   ms.author="vagarw"/>

# 在邏輯應用程式中使用 Box 連接器



邏輯應用程式可以根據各種資料來源觸發，並提供連接器以取得及處理屬於流程一部分的資料。您有時候可能需要使用 Box，讓您與任何人安全地共用資料 – 即使這些人位於您的防火牆之外。



Box 資源庫應用程式提供「動作」當做與 Box 互動的機制：



1. **動作**：動作可讓您在使用邏輯應用程式設定的 Box 帳戶上執行預先定義的動作。以下是可使用 Box 連接器在 Box 帳戶上執行的動作：

	a.*列出檔案：*這項作業會傳回資料夾中所有檔案的資訊。以下是動作所需的參數清單：

	<table>
  <tr>
    <td><b>參數名稱</b></td>
    <td><b>說明</b></td>
    <td><b>必要</b></td>
  </tr>
  <tr>
    <td>資料夾路徑</td>
    <td>清單所在的資料夾路徑。</td>
    <td>是</td>
  </tr>
</table>

 >[AZURE.NOTE]它不會傳回任何檔案內容。

    b.*取得檔案：*這項作業會擷取檔案 (包括其內容和屬性)。以下是動作所需的參數清單：

  <table>
  <tr>
    <td><b>參數名稱</b></td>
    <td><b>說明</b></td>
    <td><b>必要</b></td>
  </tr>
  <tr>
    <td>檔案路徑</td>
    <td>檔案所在的資料夾路徑。</td>
    <td>是</td>
  </tr>
  <tr>
    <td>檔案類型</td>
    <td>指定檔案是文字或二進位檔。</td>
    <td>否</td>
  </tr>
</table>

 >[AZURE.NOTE]這項作業不會在讀取檔案後刪除檔案。

    c.上傳檔案：顧名思義，此動作會將檔案上傳至 Box 帳戶。如果檔案已存在，則不會加以覆寫，而且會擲回錯誤。以下是動作所需的參數清單：

	<table>
  <tr>
    <td><b>參數名稱</b></td>
    <td><b>說明</b></td>
    <td><b>必要</b></td>
  </tr>
  <tr>
    <td>檔案路徑</td>
    <td>檔案的路徑。</td>
    <td>是</td>
  </tr>
  <tr>
    <td>檔案內容</td>
    <td>即將上傳的檔案內容。</td>
    <td>是</td>
  </tr>
  <tr>
    <td>內容轉移編碼</td>
    <td>內容的編碼類型，可以是 [Base64] 或 [無]。</td>
    <td> </td>
  </tr>
</table>

    d.刪除檔案：此動作會從資料夾中刪除指定的檔案。如果找不到檔案/資料夾，則會擲回例外狀況。以下是動作所需的參數清單：

  <table>
  <tr>
    <td><b>參數名稱</b></td>
    <td><b>說明</b></td>
    <td><b>必要</b></td>
  </tr>
  <tr>
    <td>檔案路徑</td>
    <td>完整檔案路徑，包括資料夾。</td>
    <td>是</td>
  </tr>
</table>




## 建立邏輯應用程式的 Box 連接器

若要使用 Box 連接器，您必須先建立 Box 連接器 API 應用程式的執行個體。您可以從邏輯應用程式設計工具之內建立，也可以在工具外這樣做。在設計工具外建立的作法如下：

1. 從 Azure 入口網站的首頁開啟 Azure Marketplace。

2. 在 [全部內容] 下搜尋「Box 連接器」。

3. 設定 Box 連接器，然後按一下 [建立]：

	![][1]

4. 完成後，您現在即可在相同的資源群組中建立邏輯應用程式，以使用 Box 連接器。


## 在邏輯應用程式中使用 Box 連接器

建立 API 應用程式之後，您現在可以使用 Box 連接器做為邏輯應用程式的動作。若要這樣做，您需要：


1. 建立新的邏輯應用程式，並選擇具有 Box 連接器的相同資源群組。

2. 開啟 [觸發程序和動作] 以開啟邏輯應用程式設計工具，並設定您的流程。Box 連接器就會出現在右邊組件庫的 [最近使用] 區段中。選取該項目。

3. 如果在邏輯應用程式的開頭選取 Box 連接器，它的作用就像在使用此連接器的 Box 帳戶上採取的其他觸發程序動作。請注意，截至本文撰寫時，Box 連接器尚無任何觸發程序。

4. 第一個步驟是驗證及授權邏輯應用程式，以代表您執行作業。若要開始授權，請按一下 Box 連接器上的 [授權]。

	![][2]

5. 按一下 [授權] 會開啟 Box 的驗證對話方塊。提供您要執行作業之 Box 帳戶的登入詳細資料。

	![][3]

6. 對您的帳戶授與邏輯應用程式存取權限，以便代表您執行作業。

	![][4]

7. 動作的清單隨即顯示，您可以選擇您想要執行的適當作業。

	![][5]


<!--Image references-->
[1]: ./media/app-service-logic-connector-box/image_0.jpg
[2]: ./media/app-service-logic-connector-box/image_1.jpg
[3]: ./media/app-service-logic-connector-box/image_2.jpg
[4]: ./media/app-service-logic-connector-box/image_3.jpg
[5]: ./media/app-service-logic-connector-box/image_4.jpg

<!---HONumber=62-->