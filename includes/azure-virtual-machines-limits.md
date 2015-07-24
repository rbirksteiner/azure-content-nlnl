<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">資源</th>
   <th align="left" valign="middle">預設限制</th>
   <th align="left" valign="middle">上限</th>
</tr>
<tr>
   <td valign="middle"><p>每一雲端服務的<a href="http://azure.microsoft.com/documentation/services/virtual-machines/">虛擬機器</a><sup>1</sup></p></td>
   <td valign="middle"><p>50</p></td>
   <td valign="middle"><p>50</p></td>
</tr>
<tr>
   <td valign="middle"><p>每一雲端服務的輸入端點<sup>2</sup></p></td>
   <td valign="middle"><p>150</p></td>
   <td valign="middle"><p>150</p></td>
</tr>
</table>

<sup>1</sup>當您在 Azure 資源群組之外建立虛擬機器時，系統會自動建立雲端服務，包含此虛擬機器。之後您便可以在那個相同的雲端服務中加入多個虛擬機器。

<sup>2</sup>輸入端點的作用是可與對內含雲端服務而言為外部之虛擬機器進行通訊。相同雲端服務內的虛擬機器會自動允許在所有 UDP 和 TCP 通訊埠之間進行通訊，以實現內部通訊。

<!---HONumber=62-->