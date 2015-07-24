<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">資源<sup>1</sup></th>
   <th align="left" valign="middle">預設限制</th>
</tr>
<tr>
   <td valign="middle"><p>每一儲存體帳戶的 TB</p></td>
   <td valign="middle"><p>500 TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>單一 Blob 容器、資料表或佇列的大小上限</p></td>
   <td valign="middle"><p>500 TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>每一儲存體帳戶的 Blob 容器、Blob、檔案共用、資料表、佇列、實體或訊息的數目上限</p></td>
   <td valign="middle"><p>唯一的限制是 500 TB 儲存體帳戶容量</p></td>
</tr>
<tr>
   <td valign="middle"><p>檔案共用的大小上限</p></td>
   <td valign="middle"><p>5 TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>檔案共用中的檔案數目上限</p></td>
   <td valign="middle"><p>檔案共用的唯一限制是 5 TB 總容量</p></td>
</tr>
<tr>
   <td valign="middle"><p>每一永久性磁碟最多 8 KB IOPS (基本層)</p></td>
   <td valign="middle"><p>300<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>每一永久性磁碟最多 8 KB IOPS (標準層)</p></td>
   <td valign="middle"><p>500<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>每一儲存體帳戶的總要求率 (假設 1KB 物件大小)</p></td>
   <td valign="middle"><p>每秒最多 20,000 個實體或訊息</p></td>
</tr>
<tr>
   <td valign="middle"><p>單一 Blob 的目標輸送量</p></td>
   <td valign="middle"><p>每秒最多 60 MB，或每秒最多 500 個要求</p></td>
</tr>
<tr>
   <td valign="middle"><p>單一佇列的目標輸送量 (1 KB 訊息)</p></td>
   <td valign="middle"><p>每秒最多 2000 個訊息</p></td>
</tr>
<tr>
   <td valign="middle"><p>單一資料表分割的目標輸送量 (1 KB 實體)</p></td>
   <td valign="middle"><p>每秒最多 2000 個實體</p></td>
</tr>
<tr>
   <td valign="middle"><p>每一儲存體帳戶的輸入上限 (美國地區)</p></td>
   <td valign="middle"><p>如果啟用 GRS<sup>3</sup>，則為 10 Gbps，LRS 為 20 Gbps</p></td>
</tr>
<tr>
   <td valign="middle"><p>每一儲存體帳戶的輸出上限 (美國地區)</p></td>
   <td valign="middle"><p>如果啟用 GRS<sup>3</sup>，則為 20 Gbps，LRS 為 30 Gbps</p></td>
</tr>
<tr>
   <td valign="middle"><p>每一儲存體帳戶的輸入上限 (歐洲和亞洲地區)</p></td>
   <td valign="middle"><p>如果啟用 GRS<sup>3</sup>，則為 5 Gbps，LRS 為 10 Gbps</p></td>
</tr>
<tr>
   <td valign="middle"><p>每一儲存體帳戶的輸出上限 (歐洲和亞洲地區)</p></td>
   <td valign="middle"><p>如果啟用 GRS<sup>3</sup>，則為 10 Gbps，LRS 為 15 Gbps</p></td>
</tr>
</table>

<sup>1</sup> 如需這些限制的詳細資訊，請參閱 [Azure 儲存體延展性和效能目標](../articles/storage/storage-scalability-targets.md)。

<sup>2</sup> 對於基本層中的虛擬機器，請勿在儲存體帳戶中放入超過 66 個高度使用的 VHD，以避免達到 20,000 總要求率的限制 (20,000/300)。對於標準層中的虛擬機器，請勿在儲存體帳戶中放入超過 40 個高度使用的 VHD (20,000/500)。如需詳細資訊，請參閱＜[Azure 的虛擬機器和雲端服務大小](http://msdn.microsoft.com/library/azure/dn197896.aspx)＞。

<sup>3</sup> GRS 的全名是 [Geo Redundant Storage](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx) (異地備援儲存體)。LRS 的全名是 [Locally Redundant Storage](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/08/introducing-locally-redundant-storage-for-windows-azure-storage.aspx) (本地備援儲存體)。請注意，GRS 也是本地備援。

<!---HONumber=62-->