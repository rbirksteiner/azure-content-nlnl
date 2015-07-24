> [AZURE.NOTE]Storage Client Library for .NET 中的 ODataLib 相依性現已透過 ODataLib (5.0.2 版和更新版本) 封裝解決，該封裝是由 NuGet 而非透過 WCF Data Services 提供。您可以直接下載 ODataLib 程式庫，或是由您的程式碼專案透過 NuGet 參照這些程式庫。Storage Client Library 使用的特定 ODataLib 封裝有 [OData](http://nuget.org/packages/Microsoft.Data.OData/5.0.2)、[Edm](http://nuget.org/packages/Microsoft.Data.Edm/5.0.2)，以及 [Spatial](http://nuget.org/packages/System.Spatial/5.0.2)。
> 
> 這些程式庫雖由 Azure 資料表儲存體類別使用，它們同時也是使用 Storage Client Library 進行程式設計的必要相依項目。

<!---HONumber=July15_HO2-->