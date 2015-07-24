
#如何使用 CLI 從虛擬機器卸離資料磁碟

當不再需要某個連接至虛擬機器的資料磁碟時，卸離此資料磁碟很簡單。這會將磁碟從虛擬機器中卸離，但這不會將它從儲存體中移除。如果您想要再次使用磁碟上現有的資料，您可以將磁碟重新連接至相同或其他虛擬機器。

> [AZURE.NOTE]Azure 中的虛擬機器使用不同類型的磁碟：例如作業系統磁碟、本機暫存磁碟和選擇性資料磁碟。建議您使用資料磁碟來儲存虛擬機器的資料。如需磁碟的詳細資訊，請參閱[關於磁碟和映像](http://go.microsoft.com/fwlink/p/?LinkId=263439)。目前無法卸離作業系統磁碟。


1. 取得連接至您 VM 的磁碟清單：

        vm disk list <vm-name>

    如果省略 `<vm-name>`，則您將會取得訂用帳戶中的所有磁碟清單。


2. 卸離磁碟：

        vm disk detach <vm-name> <lun>

    `lun` 識別要卸離的磁碟，而且會是可以在您的 VM 磁碟清單中找到的數字。

範例逐步解說 (包括終端機輸出)：

    ~$ azure vm disk list kmlinux
    info:    Executing command vm disk list
    + Fetching disk images
    + Getting virtual machines
    + Getting VM disks
    data:    Lun  Size(GB)  Blob-Name                               OS
    data:    ---  --------  --------------------------------------  -----
    data:         30        kmlinux-kmlinux-2015-02-05.vhd          Linux
    data:    1    5         kmlinux-f8ef0006ab182209.vhd
    data:    2    7         kmlinux-602362868dbb7439.vhd
    info:    vm disk list command OK
    ~$ azure vm disk detach kmlinux 2
    info:    Executing command vm disk detach
    + Getting virtual machines
    + Removing Data-Disk
    info:    vm disk detach command OK
    ~$ azure vm disk list kmlinux
    info:    Executing command vm disk list
    + Fetching disk images
    + Getting virtual machines
    + Getting VM disks
    data:    Lun  Size(GB)  Blob-Name                               OS
    data:    ---  --------  --------------------------------------  -----
    data:         30        kmlinux-kmlinux-2015-02-05.vhd          Linux
    data:    1    5         kmlinux-f8ef0006ab182209.vhd
    info:    vm disk list command OK

<!---HONumber=July15_HO2-->