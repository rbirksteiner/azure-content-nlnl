<properties 
   pageTitle="BizTalk 一般檔案編碼器" 
   description="BizTalk 一般檔案編碼器" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/20/2015"
   ms.author="rajram"/>

# BizTalk 一般檔案編碼器

BizTalk 一般檔案編碼解碼連接器可協助您的應用程式在一般檔案資料 (例如 excel、csv) 與 XML 資料之間交互操作。它可以將指定的一般檔案執行個體轉換成 XML，反之亦然。

##使用 BizTalk 一般檔案編碼器
1. 若要使用 BizTalk 一般檔案編碼器，您必須先建立 BizTalk 一般檔案編碼器 API 應用程式的執行個體。在建立邏輯應用程式時進行內嵌，或從 Azure Marketplace 選取 BizTalk 一般檔案編碼器 API 應用程式，即可完成此作業。

###設定 BizTalk 一般檔案編碼器
BizTalk 一般檔案編碼器會將結構描述視為其組態的一部分。直接從 Azure 入口網站啟動 API 應用程式，或按兩下設計工具介面上的 API 應用程式，使用者即可啟動 API 應用程式組態刀鋒視窗。

![BizTalk 一般檔案編碼器的組態][1]

在 [API 應用程式] 刀鋒視窗中，使用者可以按一下 [*結構描述*] 組件來設定結構描述。

![BizTalk 一般檔案編碼器的結構描述組件][2]

使用者可以從磁碟上傳結構描述，或從一般檔案執行個體或 JSON 執行個體產生一個結構描述。

![BizTalk 一般檔案編碼器的結構描述組件][3]


###在設計介面中使用 BizTalk 一般檔案編碼器
一旦設定，使用者可以按一下 *->* 並從動作清單中選擇動作。

![BizTalk 一般檔案編碼器的動作清單][4]

####一般檔案至 Xml

![BizTalk 一般檔案編碼器的動作清單][5]

<table>
	<tr>
		<th>參數</th>
		<th>類型</th>
		<th>參數說明</th>
	</tr>
	<tr>
		<td>一般檔案</td>
		<td>字串</td>
		<td>輸入一般檔案的內容</td>
	</tr>
	<tr>
		<td>結構描述名稱</td>
		<td>字串</td>
		<td>表示輸入一般檔案的結構描述名稱</td>
	</tr>
	<tr>
		<td>根節點名稱</td>
		<td>字串</td>
		<td>一般檔案結構描述的根節點名稱</td>
	</tr>
</table>


此動作會以字串形式傳回輸出 - 輸出 Xml。輸出 Xml 包含輸入一般檔案內容的 xml 表示法。

####Xml 至一般檔案

![BizTalk 一般檔案編碼器的動作清單][6]

<table>
	<tr>
		<th>參數</th>
		<th>類型</th>
		<th>參數說明</th>
	</tr>
	<tr>
		<td>輸入 Xml</td>
		<td>字串</td>
		<td>輸入 Xml 內容</td>
	</tr>
</table>

此動作會以字串形式傳回輸出 - 一般檔案。輸出包含輸入 xml 內容的一般檔案表示法。

<!-- References -->
[1]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.ClickToConfigure.PNG
[2]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.SchemasPart.PNG
[3]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.SchemaUpload.PNG
[4]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.ListOfActions.PNG
[5]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.FlatFileToXml.PNG
[6]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.XmlToFlatFile.PNG
 

<!---HONumber=62-->