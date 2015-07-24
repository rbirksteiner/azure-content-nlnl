儲存體模擬器支援共用金鑰驗證的單一固定帳戶及已知驗證金鑰。此帳戶和金鑰都是唯一允許搭配儲存體模擬器使用的共用金鑰認證。如下：

    Account name: devstoreaccount1
    Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
    
> [AZURE.NOTE]儲存體模擬器所支援的驗證金鑰僅可用來測試用戶端驗證碼的功能。它不提供任何安全性用途。您無法將生產儲存體帳戶和金鑰與儲存體模擬器搭配使用。也請注意，您不應該將開發帳戶與生產資料搭配使用。

從您的應用程式連線到儲存體模擬器最簡單的方法，就是從您應用程式內部參照捷徑 `UseDevelopmentStorage=true` 的組態檔中設定連接字串。以下是 app.config 檔中儲存體模擬器連接字串的範例：

    <appSettings>
      <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
    </appSettings>

若要建立參考模擬器帳戶名稱與金鑰的連接字串，請注意您必須針對每一個您希望從連接字串中的模擬器使用的服務指定端點。這是必要的，這樣一來連接字串將會參考模擬器端點 (不同於正式作業儲存體帳戶的模擬器端點)。例如，連接字串的值看起來會像這樣：

	DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
	AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
    BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
    TableEndpoint=http://127.0.0.1:10002/devstoreaccount1;
    QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1; 

此值等同於上面顯示的捷徑 `UseDevelopmentStorage=true`。

您也可以指定在對儲存體模擬器測試服務時要使用的 HTTP Proxy。當您正在對儲存體服務進行作業偵錯時，這對於觀察 HTTP 要求和回應非常有用。若要指定 Proxy，可將 `DevelopmentStorageProxyUri` 選項加入連接字串，並將其值設為 Proxy URI。例如，以下是指向儲存體模擬器並設定 HTTP Proxy 的連接字串：

    UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://myProxyUri

<!---HONumber=July15_HO2-->