<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">資源</th>
   <th align="left" valign="middle">預設限制</th>
   <th align="left" valign="middle">上限</th>
</tr>
<tr>
   <td valign="middle"><p>在單一訂用帳戶的 Azure 媒體服務 (AMS) 帳戶</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>25</p></td>
</tr>
<tr>
   <td valign="middle"><p>每個 AMS 帳戶的資產</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>1,000,000</p></td>
</tr>
<tr>
   <td valign="middle"><p>每個作業的鏈結工作</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>30</p></td>
</tr>
<tr>
   <td valign="middle"><p>每個工作的資產</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>50</p></td>
</tr>
<tr>
   <td valign="middle"><p>每個作業的資產</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p>每個 AMS 帳戶的作業 </p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>50,000<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>一次與資產相關聯的唯一定位器</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>5<sup>4</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>每個 AMS 帳戶的即時通道 </p></td>
   <td valign="middle"><p>5</p></td>
   <td valign="middle"><p>N/A<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>每個通道中已停止狀態的程式 </p></td>
   <td valign="middle"><p>50</p></td>
   <td valign="middle"><p>N/A<sup>1</sup></p></td>
</tr><tr>
   <td valign="middle"><p>每個通道中執行中的程式 </p></td>
   <td valign="middle"><p>3</p></td>
   <td valign="middle"><p>N/A<sup>1</sup></p></td>
</tr><tr>
   <td valign="middle"><p>每個 AMS 帳戶執行中的串流端點</p></td>
   <td valign="middle"><p>2</p></td>
   <td valign="middle"><p>N/A<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>每個串流端點的資料流單位 </p></td>
   <td valign="middle"><p>10 </p></td>
   <td valign="middle"><p>N/A<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>每個 AMS 帳戶的編碼單元 </p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>N/A<sup>1</sup></p></td>
</tr>
</table>

<sup>1</sup> 您可以開啟支援票證來要求更新此配額的限制。請勿建立多個 AMS 帳戶來提高限制，而是應提交支援票證。

<sup>2</sup> 這個數目包括佇列、已完成、作用中和已取消的工作。不包含已刪除的工作。您可以使用 **IJob.Delete** 或 **DELETE** HTTP 要求刪除舊工作。

<sup>3</sup> 提出要求來列出工作實體時，每個要求會傳回最多 1000 個。如果您需要追蹤所有已送出的工作，您可以如 [OData 系統查詢選項](http://msdn.microsoft.com/library/gg309461.aspx)中所述，使用 top/skip。

<sup>4</sup> 定位器並非設計來管理每個使用者的存取控制。若要給予個別使用者不同的存取權限，請使用數位版權管理 (DRM) 方案。

<!---HONumber=62-->