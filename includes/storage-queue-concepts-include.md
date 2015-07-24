## 什麼是佇列儲存體？

Azure 佇列儲存體是一項儲存大量訊息的服務，全球任何地方都可利用 HTTP 或 HTTPS 並透過驗證的呼叫來存取這些訊息。單一佇列訊息的大小上限為 64 KB，而一個佇列可以包含數百萬個訊息，以儲存體帳戶的總容量為限。一個儲存體帳戶可包含多達 500 TB 的 Blob、佇列和資料表資料。如需關於儲存體帳戶容量的詳細資訊，請參閱＜[Azure 儲存體延展性和效能目標](http://msdn.microsoft.com/library/azure/dn249410.aspx)＞(英文)。

佇列儲存體的一般用途包括：

-   <span>建立工作的待處理項目以進行非同步處理</span>
-   將訊息從 Azure Web 角色傳遞至 Azure 背景工作角色

## 佇列服務概念

佇列服務包含下列元件：

![Queue1](./media/storage-queue-concepts-include/queue1.png)


- **URL 格式：** 可利用下列 URL 格式來定址佇列：http://`<storage account>`.queue.core.windows.net/`<queue>` 
      
下列 URL 可定址圖中的其中一個佇列：http://myaccount.queue.core.windows.net/imagesToDownload

-**儲存體帳戶：**一律透過儲存體帳戶來存取 Azure 儲存體。如需關於儲存體帳戶容量的詳細資訊，請參閱＜[Azure 儲存體延展性和效能目標](../articles/storage/storage-scalability-targets.md)＞(英文)。

- **佇列：**佇列包含一組訊息。所有訊息都必須放在佇列中。

- **訊息：**訊息 (任何格式) 的大小上限為 64 KB。

<!---HONumber=July15_HO2-->