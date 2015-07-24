
如需有關磁碟的詳細資訊，請參閱[關於 Azure 中的虛擬機器磁碟](http://go.microsoft.com/fwlink/p/?LinkId=403697)。

##<a id="cliattachempty"></a>做法：連接空的磁碟
連接空的磁碟是新增資料磁碟較為簡易的方式。執行下列命令連接空的新磁碟：

    vm disk attach-new <vm-name> <size-in-gb> [blob-url]

使用您的虛擬機器名稱來取代 `vm-name`，並使用新硬碟的大小來取代 `size-in-gb`。您可以選擇性地使用 Blob URL 作為最後一個引數，明確指定要建立的目標 Blob。如果不指定 Blob URL，則會自動產生 Blob 物件。

執行下列命令以確認是否已建立您的磁碟：

    vm disk list <vm-name>

以下是上述命令的逐步解說範例 (包含終端機輸出)：

    ~$ azure vm disk attach-new pinkylinux 20 http://pinkylinux.blob.core.windows.net/vhds/pinkydisk1.vhd
    info:   Executing command vm disk attach-new
    + Getting virtual machines
    + Adding Data-Disk
    info:   vm disk attach-new command OK
    ~$ azure vm disk list pinkylinux
    info:    Executing command vm disk list
    + Fetching disk images
    + Getting virtual machines
    + Getting VM disks
    data:    Lun  Size(GB)  Blob-Name                               OS
    data:    ---  --------  --------------------------------------  -----
    data:         30        pinkylinux-pinkylinux-2015-02-05.vhd    Linux
    data:    0    5         pinkydisk1.vhd
    data:    1    20        pinkylinux-f8ef0006ab182209.vhd
    info:    vm disk list command OK

<!---HONumber=July15_HO2-->