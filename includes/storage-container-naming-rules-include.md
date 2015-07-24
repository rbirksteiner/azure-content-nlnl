儲存體 Blob 中的每個 Blob 必須位於一個容器中。此容器會組成 blob 名稱的一部分。例如，`mycontainer` 是這些範例 blob URI 中容器的名稱：

	https://storagesample.blob.core.windows.net/mycontainer/blob1.txt
	https://storagesample.blob.core.windows.net/mycontainer/photos/myphoto.jpg
 
> [AZURE.IMPORTANT]請注意容器的名稱一律必須小寫。如果在容器名稱中含有大寫字母，或其他違反容器命名規則，您可能會收到「400 錯誤 (不正確的要求)」錯誤訊息。關於容器的命名規則，請參閱[命名和參考容器、Blob 及中繼資料](https://msdn.microsoft.com/library/azure/dd135715.aspx)。

<!---HONumber=July15_HO2-->