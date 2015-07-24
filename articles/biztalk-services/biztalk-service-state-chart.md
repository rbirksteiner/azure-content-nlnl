<properties 
	pageTitle="BizTalk 服務在每個狀態下可執行的工作 |Azure" 
	description="不同 MABS 狀態允許的動作/作業：停止、啟動、重新啟動、暫停、繼續、刪除、調整、更新組態和備份" 
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
	ms.date="06/07/2015" 
	ms.author="mandia"/>



# BizTalk 服務：服務狀態圖
視 BizTalk 服務的目前狀態而定，有些作業是可以或無法在 BizTalk 服務上執行。

例如，您在 Azure 管理入口網站中佈建新的 BizTalk 服務。順利完成時，BizTalk 服務會處於「作用中」狀態。在「作用中」狀態下，您可以停止 BizTalk 服務。如果順利停止，則 BizTalk 服務會進入「已停止」狀態。如果無法停止，則 BizTalk 服務會進入「停止失敗」狀態。在「停止失敗」狀態下，您可以重新啟動 BizTalk 服務。如果您嘗試不允許的作業，例如繼續 BizTalk 服務，則會發生下列錯誤：

**不允許此作業**

若要佈建 BizTalk 服務，請移至「[BizTalk 服務：使用 Azure 管理入口網站進行佈建](http://go.microsoft.com/fwlink/p/?LinkID=302280)」。

下表列出當 BizTalk 服務處於特定狀態時可執行的作業或動作。核取記號表示可在該狀態下執行作業。空白項目表示無法在此狀態下執行作業。

## 啟動、停止、重新啟動、暫止、繼續和刪除作業
<table border="1">
<tr>
        <th colspan="15">作業或動作</th>
</tr>

<tr>
        <th rowspan="18">BizTalk 服務狀態</th>
</tr>
<tr bgcolor="FAF9F9">
        <th> </th>
        <th>啟動</th>
        <th>停止</th>
        <th>重新啟動</th>
        <th>暫止</th>
        <th>繼續</th>
        <th>刪除</th>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>使用中</b></td>
<td> </td>
<td><center>x</center></td>
<td><center>x</center></td>
<td><center>x</center></td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>停用</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>暫止</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>已停止</b></td>
<td><center>x</center></td>
<td> </td>
<td><center>x</center></td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>服務更新失敗</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>停用失敗</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>啟用失敗</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>啟動失敗<br/>
停止失敗<br/>
重新啟動失敗</b></td>
<td><center>x</center></td>
<td><center>x</center></td>
<td><center>x</center></td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>暫止失敗<br/>
繼續失敗</b></td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
<td><center>x</center></td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>建立失敗<br/>
還原失敗<br/></b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>設定更新失敗</b></td>
<td> </td>
<td> </td>
<td><center>x</center></td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>調整失敗</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
</table>
<br/>

## 調整、更新組態和備份作業
<table border="1">
<tr>
        <th colspan="15">作業或動作</th>
</tr>

<tr>
        <th rowspan="18">BizTalk 服務狀態</th>
</tr>
<tr bgcolor="FAF9F9">
        <th> </th>
        <th>調整</th>
        <th>更新組態</th>
        <th>備份</th>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>使用中</b></td>
<td><center>x</center></td>
<td><center>x</center></td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>停用</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>暫止</b></td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>已停止</b></td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>服務更新失敗</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>停用失敗</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>啟用失敗</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>啟動失敗<br/>
停止失敗<br/>
重新啟動失敗</b></td>
<td> </td>
<td><center>x</center></td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>暫止失敗<br/>
繼續失敗</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>建立失敗<br/>
還原失敗<br/></b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>設定更新失敗</b></td>
<td> </td>
<td><center>x</center></td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>調整失敗</b></td>
<td><center>x</center></td>
<td> </td>
<td> </td>
</tr>
</table>

## 另請參閱
- [BizTalk 服務：使用 Azure 管理入口網站進行佈建](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [BizTalk 服務：儀表板、監視和調整索引標籤](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [BizTalk 服務：開發人員、基本、標準和高級版本圖表](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [BizTalk 服務：備份與還原](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [BizTalk 服務：節流](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
- [BizTalk 服務：簽發者名稱和簽發者金鑰](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
- [如何開始使用 Azure BizTalk 服務 SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)


 

<!---HONumber=62-->