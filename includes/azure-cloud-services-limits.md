<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">資源</th>
   <th align="left" valign="middle">預設限制</th>
   <th align="left" valign="middle">上限</th>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/documentation/articles/cloud-services-what-is/">每一部署的 Web 背景工作角色<sup>1</sup></a></p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>25</p></td>
</tr>
<tr>
   <td valign="middle"><p>每一部署的<a href="http://msdn.microsoft.com/library/gg557552.aspx#InstanceInputEndpoint">執行個體輸入端點</a></p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>25</p></td>
</tr>
<tr>
   <td valign="middle"><p>每一部署的<a href="http://msdn.microsoft.com/library/gg557552.aspx#InputEndpoint">輸入端點</a></p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>25</p></td>
</tr>
<tr>
   <td valign="middle"><p>每一部署的<a href="http://msdn.microsoft.com/library/gg557552.aspx#InternalEndpoint">內部端點</a></p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>25</p></td>
</tr>
</table>

<sup>1</sup>具有背景工作角色的每個雲端服務均可有兩個部署，一個供生產環境使用，另一個供接移環境使用。另請注意，此限制是指個別角色的數目 (組態)，而不是每一角色的執行個體數目 (調整)。

<!---HONumber=62-->