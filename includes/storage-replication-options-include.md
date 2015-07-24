Microsoft Azure 儲存體帳戶中的資料一律會進行複寫以確保持久性及高可用性，即使在面對暫時性的硬體故障時，仍可滿足 [Azure 儲存體 SLA](http://azure.microsoft.com/support/legal/sla/)。建立儲存體帳戶時，您必須選取下列其中一個複寫選項：

- **本地備援儲存體 (LRS)。** 本地備援儲存體可維護三個資料複本。LRS 會在單一區域的單一設備內複寫三次。LRS 可保護您的資料以避免一般的硬體故障，但無法避免單一設備的故障。  
  
	使用 LRS 可享有折扣費率。如需最高的持久性，建議您採用異地備援儲存體，如下所述。


- **區域備援儲存體 (ZRS)。** 區域備援儲存體可維護三個資料複本。ZRS 會在單一地區或兩個地區內的二或三個設備中複寫三次，提供比 LRS 更高的持久性。ZRS 可確保資料在單一地區內的持續性。

	ZRS 提供高於 LRS 等級的持久性；不過，如需最高的持久性，建議您採用地理區域備援儲存體，如下所述。

	> [AZURE.NOTE]ZRS 目前僅適用於區塊 Blob。
	> 
	> 建立儲存體帳戶並選取 ZRS 後，就無法轉換為採用任何其他類型的複寫，反之亦然。

- **異地備援儲存體 (GRS)**。建立儲存體帳戶時，依預設會啟用異地備援儲存體。GRS 可維護六個資料複本。有了 GRS，您的資料會在主要區域內複寫三次，並在與主要區域相距甚遠的次要區域內複寫三次，提供最高等級的持久性。在主要區域發生問題時，Azure 儲存體將會容錯移轉至次要區域。GRS 可確保兩個不同區域中的資料持續性。


- **讀取權限異地備援儲存體 (RA-GRS)**。讀取權限異地備援儲存體會將您的資料複寫到次要地理位置，並同時提供次要位置中資料的讀取權限。讀取權限異地備援儲存體可讓您在主要或次要任一位置無法使用的情況下，從另外一個位置存取資料。

	> [AZURE.IMPORTANT]除非您建立帳戶時指定了 ZRS，否則就可以在建立儲存體帳戶之後，變更資料的複寫方式。不過請注意，從 LRS 切換到 GRS 或 RA-GRS 時，可能必須支付額外的單次資料傳輸成本。
 
如需儲存體複寫選項相關的其他詳細資料，請參閱 [Azure 儲存體複寫](../articles/storage/storage-redundancy.md)。

如需儲存體帳戶複寫的定價資訊，請參閱[儲存體定價詳細資料](http://azure.microsoft.com/pricing/details/storage/)。

如需 Azure 儲存體持久性的架構詳細資訊，請參閱 [Azure 儲存體 SOSP 文件](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)。

<!---HONumber=July15_HO2-->