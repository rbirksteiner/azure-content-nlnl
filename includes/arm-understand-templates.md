## 了解 Azure 資源範本和資源群組

在 Microsoft Azure 中部署和執行的應用程式，大部分在建立時會使用不同雲端資源類型的組合 (例如一或多個 VM 和儲存體帳戶、SQL 資料庫、虛擬網路、CDN 等等)。有了 [Azure 資源管理員範本](https://msdn.microsoft.com/library/azure/dn835138.aspx)之後，您就可以使用 JSON 的資源說明、相關設定和部署參數，來部署和管理這些不同的資源。

定義好 JSON 資源範本後，您可以使用 PowerShell 命令來執行這個範本，以及將範本中定義的資源部署到 Azure。您可以在 PowerShell 命令殼層中獨立執行此 PowerShell 命令，或者合併到內含其他自動化還輯的 PowerShell 指令碼中。

您使用 Azure 資源管理員範本建立的資源，會部署到新的或現有 Azure 資源群組。Azure 資源群組可讓您將多個已部署的資源當成一個邏輯群組來管理。一般而言，一個群組會包含一個特定應用程式的相關資源。Azure 資源群組提供方法來管理群組/應用程式的整體生命週期，並提供管理 API 讓您一次停止/啟動/刪除群組內的所有資源、套用角色型存取控制 (RBAC) 規則來嚴格管制它們的安全性權限、稽核作業，以及加上其他中繼資料來標記資源以利追蹤。若要深入了解 Azure 資源群組，請參閱 [Azure 資源管理員概觀](https://azure.microsoft.com/documentation/articles/resource-group-overview/)。

下列的自動化範例示範如何利用 PowerShell 或 CLI 來使用 Azure 資源管理員範本和部署資源群組。

<!---HONumber=62-->