<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">資源</th>
   <th align="left" valign="middle">預設限制</th>
   <th align="left" valign="middle">上限</th>
</tr>
<tr>
   <td valign="middle"><p>每一訂用帳戶的<a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">虛擬網路</a><sup>1</sup></p></td>
   <td valign="middle"><p>10</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p>每一虛擬網路的機器總數<sup>2</sup></p></td>
   <td valign="middle"><p>2048</p></td>
   <td valign="middle"><p>2048</p></td>
</tr>
<tr>
   <td valign="middle"><p>虛擬機器或角色執行個體的並行 TCP 連線</p></td>
   <td valign="middle"><p>500K</p></td>
   <td valign="middle"><p>500K</p></td>
</tr>
<tr>
   <td valign="middle"><p>每一端點的存取控制清單 (ACL)<sup>3</sup></p></td>
   <td valign="middle"><p>50</p></td>
   <td valign="middle"><p>50</p></td>
</tr>
<tr>
   <td valign="middle"><p>每一虛擬網路的區域網路網站</p></td>
   <td valign="middle"><p>10</p></td>
   <td valign="middle"><p>10</p></td>
</tr>
</table>

<sup>1</sup>每個虛擬網路支援一個單一[虛擬網路閘道](http://msdn.microsoft.com/library/azure/jj156210.aspx)。

<sup>2</sup>電腦總數包括虛擬機器和 Web/背景工作角色執行個體。

<sup>3</sup>虛擬機器的輸入端點上支援 ACL。輸入和執行個體輸入端點上支援 Web/背景工作角色。

<!---HONumber=62-->