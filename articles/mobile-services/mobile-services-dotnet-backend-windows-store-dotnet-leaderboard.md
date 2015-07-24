<properties 
	pageTitle="使用 .NET 後端建立 Windows 市集排行榜應用程式 | Azure 行動服務" 
	description="了解如何使用具有 .NET 後端的 Azure 行動服務建置 Windows 市集排行榜應用程式。" 
	documentationCenter="windows" 
	authors="MikeWasson" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/24/2015" 
	ms.author="glenga"/>

# 使用 Azure 行動服務 .NET 後端建立排行榜應用程式

本教學課程將說明如何使用具有 .NET 後端的 Azure 行動服務建置 Windows 市集應用程式。Azure 行動服務提供可擴充而安全的後端，具有內建的驗證、監控、推播通知和其他功能，以及用來建置行動應用程式的跨平台用戶端程式庫。行動服務的 .NET 後端以 [ASP.NET Web API](http://asp.net/web-api) 為基礎，可為 .NET 開發人員提供建立 REST API 的絕佳途徑。

## 概觀

Web API 是一個開放原始碼架構，可為 .NET 開發人員提供建立 REST API 的絕佳途徑。您可以在 Azure 網站或使用 .NET 後端的 Azure 行動服務上主控 Web API 方案，甚或以自訂程序自我主控。行動服務是特別針對行動應用程式設計的主控環境。當您在行動服務上主控 Web API 服務時，除了資料儲存以外，您還可獲得下列好處：

- 使用社交提供者和 Azure Active Directory (AAD) 的內建驗證。 
- 使用裝置特定通知服務推播通知至應用程式。
- 一組可讓您從任何應用程式輕鬆存取服務的完整用戶端程式庫。 
- 內建的記錄和診斷。

在本教學課程中，您將：

- 使用 Azure 行動服務建立 REST API。
- 將服務發行至 Azure。
- 建立取用服務的 Windows 市集應用程式。
- 使用 Entity Framework (EF) 建立外部索引鍵關聯和資料傳輸物件 (DTO)。
- 使用 ASP.NET Web API 定義自訂 API。

本教學課程使用 [Visual Studio 2013 的最新更新](http://go.microsoft.com/fwlink/p/?LinkID=390465)


## 關於範例應用程式

*排行榜*會顯示遊戲的玩家清單，以及每個玩家的分數和排名。排行榜可作為較大遊戲的一部分，或作為單獨的應用程式。排行榜是實際的應用程式，但簡單易懂而可用於教學課程。以下是此應用程式的螢幕擷取畫面：

![][1]

為了方便說明此應用程式，其中不含任何實際的遊戲。您可以新增玩家，以及提交每個玩家的分數。當您提交分數時，行動服務會計算新排名。在後端上，行動服務會建立具有兩個資料表的資料庫：

- Player。包含玩家識別碼和名稱。
- PlayerRank。包含玩家的分數和排名。

PlayerRank 具有 Player 的外部索引鍵。每個玩家各有零或一個 PlayerRank。

在實際的排行榜應用程式中，PlayerRank 還可能會有遊戲識別碼，讓玩家提交多個遊戲的分數。

![][2]

用戶端應用程式可對 Player 執行一組完整的 CRUD 作業。它可讀取或刪除現有的 PlayerRank 實體，但無法直接加以建立或更新。這是因為排名值是由服務所計算的。實際作法是，在用戶端提交分數後，由服務更新所有玩家的排名。

在[這裡](http://code.msdn.microsoft.com/Leaderboard-App-with-Azure-9acf63af)下載完成的專案。


## 建立專案

啟動 Visual Studio，然後建立新的 ASP.NET Web 應用程式專案。將專案命名為 Leaderboard。

![][3]

在 Visual Studio 2013 中，ASP.NET Web 應用程式專案包含 Azure 行動服務的範本。請選取此範本，然後按一下 [確定]。

![][4]
 
專案範本會包含範例控制器和資料物件。

![][5]
 
教學課程中並不需要這些項目，因此您可以從專案中加以刪除。同時，請在 WebApiConfig.cs 和 LeaderboardContext.cs 中移除對 TodoItem 的參考。

## 新增資料模型

您將使用 [EF Code First](http://msdn.microsoft.com/data/ee712907#codefirst) 來定義資料庫資料表。在 DataObjects 資料夾下，新增名為 `Player` 的類別。

	using Microsoft.WindowsAzure.Mobile.Service;
	
	namespace Leaderboard.DataObjects
	{
	    public class Player : EntityData
	    {
	        public string Name { get; set; }
	    }
	}

新增另一個名為 `PlayerRank` 的類別。

	using Microsoft.WindowsAzure.Mobile.Service;
	using System.ComponentModel.DataAnnotations.Schema;
	
	namespace Leaderboard.DataObjects
	{
	    public class PlayerRank : EntityData
	    {
	        public int Score { get; set; }
	        public int Rank { get; set; }
	
	        [ForeignKey("Id")]
	        public virtual Player Player { get; set; }
	    }
	}

請注意，這兩個類別皆衍生自 **EntityData** 類別。從 **EntityData** 衍生，可方便應用程式取用資料，而將跨平台用戶端程式庫用於 Azure 行動服務。**EntityData** 也可方便應用程式[處理資料庫寫入衝突](mobile-services-windows-store-dotnet-handle-database-conflicts.md)。

`PlayerRank` 類別具有指向相關 `Player` 實體的[導覽屬性](http://msdn.microsoft.com/data/jj713564.aspx)。**[ForeignKey]** 屬性 (attribute) 會向 EF 指出，`Player` 屬性 (property) 代表外部索引鍵。

## 新增 Web API 控制器

接著，您會為 `Player` 和 `PlayerRank` 新增 Web API 控制器。您所將新增的並不是一般 Web API 控制器，而是特別針對 Azure 行動服務而設計、名為*資料表控制器*的特殊控制器。

以滑鼠右鍵按一下 [控制器] 資料夾 > [新增] > [新增 Scaffolded 項目]。

![][6]

在 [新增 Scaffold] 對話方塊中，展開位於左側的 [一般]，然後選取 [Azure 行動服務]。接著，選取 [Azure 行動服務資料表控制器]。按一下 [新增]。

![][7]
 
在 [新增控制器] 對話方塊中：

1.	在 [模型類別] 下，選取 [Player]。 
2.	在 [資料內容類別] 下，選取 [MobileServiceContext]。
3.	將控制器命名為 "PlayerController"。
4.	按一下 [新增]。


此步驟會將名為 PlayerController.cs 的檔案新增至專案中。

![][8]

此控制器衍生自 **TableController<T>**。此類別會繼承 **ApiController**，但這是適用於 Azure 行動服務的特殊類別。
 
- 路由：**TableController** 的預設路由為 `/tables/{table_name}/{id}`，其中的 *table_name* 符合實體名稱。因此，「玩家」控制器的路徑為 */tables/player/{id}*。此路徑慣例會使 **TableController** 與行動服務 [REST API](http://msdn.microsoft.com/library/azure/jj710104.aspx) 一致。
- 資料存取：在資料庫作業中，**TableController** 類別會使用 **IDomainManager** 介面，而此介面會定義資料存取的抽象。scaffolding 會使用 **EntityDomainManager**，這是包裝 EF 內容之 **IDomainManager** 的固定實作。 

現在，請為 PlayerRank 實體新增第二個控制器。請遵循相同的步驟，但選擇 PlayerRank 作為模型類別。請使用相同的資料內容類別，不要建立新的。將控制器命名為 "PlayerRankController"。

## 使用 DTO 傳回相關實體

回收具有相關 `Player` 實體的 `PlayerRank`：

    public class PlayerRank : EntityData
    {
        public int Score { get; set; }
        public int Rank { get; set; }

        [ForeignKey("Id")]
        public virtual Player Player { get; set; }
    }

行動服務用戶端程式庫不支援導覽屬性，且將不會序列化。例如，以下是 GET `/tables/PlayerRank` 的原始 HTTP 回應：

	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Pragma: no-cache
	Content-Length: 97
	Content-Type: application/json; charset=utf-8
	Expires: 0
	Server: Microsoft-IIS/8.0
	Date: Mon, 21 Apr 2014 17:58:43 GMT
	
	[{"id":"1","rank":1,"score":150},{"id":"2","rank":3,"score":100},{"id":"3","rank":1,"score":150}]

請注意，`Player` 並未包含在物件圖形中。若要納入玩家，您可以定義*資料傳輸物件* (DTO)，將物件圖形平面化。

DTO 是定義資料如何透過網路傳送的物件。如果您想讓電傳格式看起來與資料庫模型不同，即可使用 DTO。若要為 `PlayerRank` 建立 DTO，請在 DataObjects 資料夾中新增名為 `PlayerRankDto` 的新類別。

	namespace Leaderboard.DataObjects
	{
	    public class PlayerRankDto
	    {
	        public string Id { get; set; }
	        public string PlayerName { get; set; }
	        public int Score { get; set; }
	        public int Rank { get; set; }
	    }
	}

在 `PlayerRankController` 類別中，我們將使用 LINQ **Select** 方法，將 `PlayerRank` 執行個體轉換為 `PlayerRankDto` 執行個體。依照下列方式更新 `GetAllPlayerRank` 和 `GetPlayerRank` 控制器方法：

	// GET tables/PlayerRank
	public IQueryable<PlayerRankDto> GetAllPlayerRank()
	{
	    return Query().Select(x => new PlayerRankDto()
	    {
	        Id = x.Id,
	        PlayerName = x.Player.Name,
	        Score = x.Score,
	        Rank = x.Rank
	    });
	}
	
	// GET tables/PlayerRank/48D68C86-6EA6-4C25-AA33-223FC9A27959
	public SingleResult<PlayerRankDto> GetPlayerRank(string id)
	{
	    var result = Lookup(id).Queryable.Select(x => new PlayerRankDto()
	    {
	        Id = x.Id,
	        PlayerName = x.Player.Name,
	        Score = x.Score,
	        Rank = x.Rank
	    });
	
	    return SingleResult<PlayerRankDto>.Create(result);
	}

經過這些變更後，這兩個 GET 方法會將 `PlayerRankDto` 物件傳回至用戶端。`PlayerRankDto.PlayerName` 屬性會設為玩家名稱。以下是執行此變更後的範例回應：

	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Pragma: no-cache
	Content-Length: 160
	Content-Type: application/json; charset=utf-8
	Expires: 0
	Server: Microsoft-IIS/8.0
	Date: Mon, 21 Apr 2014 19:57:08 GMT
	
	[{"id":"1","playerName":"Alice","score":150,"rank":1},{"id":"2","playerName":"Bob","score":100,"rank":3},{"id":"3","playerName":"Charles","score":150,"rank":1}]

請注意，JSON 裝載此時會包含玩家名稱。

除了使用 LINQ Select 陳述式以外，另一個選項是使用 AutoMapper。此選項需使用其他設定程式碼，但可啟用從網域實體到 DTO 的自動對應。如需詳細資訊，請參閱[在使用 AutoMapper 的 .NET 後端中對應資料庫類型與用戶端類型](http://blogs.msdn.com/b/azuremobile/archive/2014/05/19/mapping-between-database-types-and-client-type-in-the-net-backend-using-automapper.aspx)。

## 定義用來提交分數的自訂 API

`PlayerRank` 實體包含 `Rank` 屬性。此值由伺服器計算，而且我們不想讓用戶端加以設定。用戶端應使用自訂 API 提交玩家的分數。伺服器在取得新分數時，會更新所有的玩家排名。

首先，將名為 `PlayerScore` 的類別新增至 DataObjects 資料夾。

	namespace Leaderboard.DataObjects
	{
	    public class PlayerScore
	    {
	        public string PlayerId { get; set; }
	        public int Score { get; set; }
	    }
	}

在 `PlayerRankController` 類別中，將 `MobileServiceContext` 變數從建構函式移至類別變數：

    public class PlayerRankController : TableController<PlayerRank>
    {
        // Add this:
        MobileServiceContext context = new MobileServiceContext();

        protected override void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);

            // Delete this:
            // MobileServiceContext context = new MobileServiceContext();
            DomainManager = new EntityDomainManager<PlayerRank>(context, Request, Services);
        }


從 `PlayerRankController` 中刪除下列方法：

- `PatchPlayerRank` 
- `PostPlayerRank` 
- `DeletePlayerRank`

然後，將下列程式碼新增至 `PlayerRankController`：

    [Route("api/score")]
    public async Task<IHttpActionResult> PostPlayerScore(PlayerScore score)
    {
        // Does this player exist?
        var count = context.Players.Where(x => x.Id == score.PlayerId).Count();
        if (count < 1)
        {
            return BadRequest();
        }

        // Try to find the PlayerRank entity for this player. If not found, create a new one.
        PlayerRank rank = await context.PlayerRanks.FindAsync(score.PlayerId);
        if (rank == null)
        {
            rank = new PlayerRank { Id = score.PlayerId };
            rank.Score = score.Score;
            context.PlayerRanks.Add(rank);
        }
        else
        {
            rank.Score = score.Score;
        }

        await context.SaveChangesAsync();

        // Update rankings
        // See http://stackoverflow.com/a/575799
        const string updateCommand =
            "UPDATE r SET Rank = ((SELECT COUNT(*)+1 from {0}.PlayerRanks " +
            "where Score > (select score from {0}.PlayerRanks where Id = r.Id)))" +
            "FROM {0}.PlayerRanks as r";

        string command = String.Format(updateCommand, ServiceSettingsDictionary.GetSchemaName());
        await context.Database.ExecuteSqlCommandAsync(command);

        return Ok();
    }

`PostPlayerScore` 方法會以 `PlayerScore` 執行個體作為輸入。(用戶端會在 HTTP POST 要求中傳送 `PlayerScore`。) 此方法會執行下列動作：

1.	如果資料庫中尚無玩家的 `PlayerRank`，則會新增一個。
2.	更新玩家的分數。
3.	執行分批更新所有玩家排名的 SQL 查詢。

**[Route]** 屬性會定義此方法的自訂路由：

	[Route("api/score")]

您也可以將方法放入個別的控制器中。沒有哪種方法特別好，端視您想要如何組織程式碼而定。若要深入了解 **[Route]** 屬性，請參閱 [Web API 中的屬性路由](http://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2)。

## 建立 Windows 市集應用程式

在本節中，我將說明取用行動服務的 Windows 市集應用程式。但在 XAML 或 UI 方面並不會有太多著墨。我會將重點放在應用程式邏輯上。您可以在[這裡](http://code.msdn.microsoft.com/Leaderboard-App-with-Azure-9acf63af)下載完整專案。

將新的 Windows 市集應用程式專案新增至方案。我使用空白應用程式 (Windows) 範例。

![][10]
 
使用 NuGet Package Manager 新增行動服務用戶端程式庫。在 Visual Studio 中，從 [工具] 功能表中選取 [NuGet Package Manager]。接著，選取 [Package Manager 主控台]。在 [Package Manager 主控台] 視窗中，輸入下列命令。

	Install-Package WindowsAzure.MobileServices -Project LeaderboardApp

-Project 參數會指定要將封裝安裝到哪個專案。

## 新增模型類別

建立名為 Models 的資料夾，並新增下列類別：

	namespace LeaderboardApp.Models
	{
	    public class Player
	    {
	        public string Id { get; set; }
	        public string Name { get; set; }
	    }
	
	    public class PlayerRank
	    {
	        public string Id { get; set; }
	        public string PlayerName { get; set; }
	        public int Score { get; set; }
	        public int Rank { get; set; }
	    }
	
	    public class PlayerScore
	    {
	        public string PlayerId { get; set; }
	        public int Score { get; set; }
	    }
	}

這些類別會直接對應至行動服務中的資料實體。
 
## 建立檢視模型

Model-View-ViewModel (MVVM) 是 Model-View-Controller (MVC) 的變體。MVVM 模式有助於區隔應用程式邏輯與顯示。

- 此模型代表網域資料 (玩家、玩家排名和玩家分數)。
- 檢視模型是檢視的抽象呈現。 
- 此檢視會顯示檢視模型，並將使用者輸入傳送至檢視模型。就 Windows 市集應用程式而言，檢視會以 XAML 定義。

![][11]

新增名為 `LeaderboardViewModel` 的類別。

	using LeaderboardApp.Models;
	using Microsoft.WindowsAzure.MobileServices;
	using System.ComponentModel;
	using System.Net.Http;
	using System.Threading.Tasks;
	
	namespace LeaderboardApp.ViewModel
	{
	    class LeaderboardViewModel : INotifyPropertyChanged
	    {
	        MobileServiceClient _client;
	
	        public LeaderboardViewModel(MobileServiceClient client)
	        {
	            _client = client;
	        }
	    }
	}

在檢視模型上實作 **INotifyPropertyChanged**，讓檢視模型可參與資料繫結。

    class LeaderboardViewModel : INotifyPropertyChanged
    {
        MobileServiceClient _client;

        public LeaderboardViewModel(MobileServiceClient client)
        {
            _client = client;
        }

        // New code:
        // INotifyPropertyChanged implementation
        public event PropertyChangedEventHandler PropertyChanged;

        public void NotifyPropertyChanged(string propertyName)
        {
            if (PropertyChanged != null)
            {
                PropertyChanged(this,
                    new PropertyChangedEventArgs(propertyName));
            }
        }    
    }

接著，新增可觀察的屬性。XAML 會將資料繫結至這些屬性。

    class LeaderboardViewModel : INotifyPropertyChanged
    {
        // ...

        // New code:
        // View model properties
        private MobileServiceCollection<Player, Player> _Players;
        public MobileServiceCollection<Player, Player> Players
        {
            get { return _Players; }
            set
            {
                _Players = value;
                NotifyPropertyChanged("Players");
            }
        }

        private MobileServiceCollection<PlayerRank, PlayerRank> _Ranks;
        public MobileServiceCollection<PlayerRank, PlayerRank> Ranks
        {
            get { return _Ranks; }
            set
            {
                _Ranks = value;
                NotifyPropertyChanged("Ranks");
            }
        }

        private bool _IsPending;
        public bool IsPending
        {
            get { return _IsPending; }
            set
            {
                _IsPending = value;
                NotifyPropertyChanged("IsPending");
            }
        }

        private string _ErrorMessage = null;
        public string ErrorMessage
        {
            get { return _ErrorMessage; }
            set
            {
                _ErrorMessage = value;
                NotifyPropertyChanged("ErrorMessage");
            }
        }
    }

當服務的非同步作業擱置時，`IsPending` 屬性會是 true。`ErrorMessage` 屬性會包含來自服務的任何錯誤訊息。

最後，新增呼叫服務層的方法。

    class LeaderboardViewModel : INotifyPropertyChanged
    {
        // ...

        // New code:
        // Service operations
        public async Task GetAllPlayersAsync()
        {
            IsPending = true;
            ErrorMessage = null;

            try
            {
                IMobileServiceTable<Player> table = _client.GetTable<Player>();
                Players = await table.OrderBy(x => x.Name).ToCollectionAsync();
            }
            catch (MobileServiceInvalidOperationException ex)
            {
                ErrorMessage = ex.Message;
            }
            catch (HttpRequestException ex2)
            {
                ErrorMessage = ex2.Message;
            }
            finally
            {
                IsPending = false;
            }
        }

        public async Task AddPlayerAsync(Player player)
        {
            IsPending = true;
            ErrorMessage = null;

            try
            {
                IMobileServiceTable<Player> table = _client.GetTable<Player>();
                await table.InsertAsync(player);
                Players.Add(player);
            }
            catch (MobileServiceInvalidOperationException ex)
            {
                ErrorMessage = ex.Message;
            }
            catch (HttpRequestException ex2)
            {
                ErrorMessage = ex2.Message;
            }
            finally
            {
                IsPending = false;
            }
        }

        public async Task SubmitScoreAsync(Player player, int score)
        {
            IsPending = true;
            ErrorMessage = null;

            var playerScore = new PlayerScore()
            {
                PlayerId = player.Id,
                Score = score
            }; 
            
            try
            {
                await _client.InvokeApiAsync<PlayerScore, object>("score", playerScore);
                await GetAllRanksAsync();
            }
            catch (MobileServiceInvalidOperationException ex)
            {
                ErrorMessage = ex.Message;
            }
            catch (HttpRequestException ex2)
            {
                ErrorMessage = ex2.Message;
            }
            finally
            {
                IsPending = false;
            }
        }

        public async Task GetAllRanksAsync()
        {
            IsPending = true;
            ErrorMessage = null;

            try
            {
                IMobileServiceTable<PlayerRank> table = _client.GetTable<PlayerRank>();
                Ranks = await table.OrderBy(x => x.Rank).ToCollectionAsync();
            }
            catch (MobileServiceInvalidOperationException ex)
            {
                ErrorMessage = ex.Message;
            }
            catch (HttpRequestException ex2)
            {
                ErrorMessage = ex2.Message;
            }
            finally
            {
                IsPending = false;
            }
         }    
    }

## 新增 MobileServiceClient 執行個體

開啟 *App.xaml.cs* 檔案，並將 **MobileServiceClient** 執行個體新增至 `App` 類別。

	// New code:
	using Microsoft.WindowsAzure.MobileServices;
	
	namespace LeaderboardApp
	{
	    sealed partial class App : Application
	    {
	        // New code.
	        // TODO: Replace 'port' with the actual port number.
	        const string serviceUrl = "http://localhost:port/";
	        public static MobileServiceClient MobileService = new MobileServiceClient(serviceUrl);
	
	
	        // ...
	    }
	}

當您在本機上進行偵錯時，行動服務會在 IIS Express 上執行。Visual Studio 會指派隨機連接埠號碼，因此本機 URL 為 http://localhost:*port*，而其中的 *port* 為連接埠號碼。若要取得連接埠號碼，請按 F5 在 Visual Studio 中啟動服務，以進行偵錯。Visual Studio 會啟動瀏覽器，並導覽至服務 URL。您也可以在專案屬性中的 [Web] 下尋找本機 URL。

## 建立主頁面

在主頁面中，新增檢視模型的執行個體。接著，將檢視模型設為頁面的 **DataContext**。

    public sealed partial class MainPage : Page
    {
        // New code:
        LeaderboardViewModel viewModel = new LeaderboardViewModel(App.MobileService);

        public MainPage()
        {
            this.InitializeComponent();
            // New code:
            this.DataContext = viewModel;
        }

       // ...


如前所述，我不會說明應用程式的所有 XAML。MVVM 模式的優點之一是能夠區隔顯示和應用程式邏輯，因此，如果您不喜歡範例應用程式，您可以輕易變更 UI。

玩家清單會顯示在 [ListBox] 中：

	<ListBox Width="200" Height="400" x:Name="PlayerListBox" 
	    ItemsSource="{Binding Players}" DisplayMemberPath="Name"/>

排名會顯示在 [ListView] 中：

	<ListView x:Name="RankingsListView" ItemsSource="{Binding Ranks}" SelectionMode="None">
	    <!-- Header and styles not shown -->
	    <ListView.ItemTemplate>
	        <DataTemplate>
	            <Grid>
	                <Grid.ColumnDefinitions>
	                    <ColumnDefinition Width="*"/>
	                    <ColumnDefinition Width="2*"/>
	                    <ColumnDefinition Width="*"/>
	                </Grid.ColumnDefinitions>
	                <TextBlock Text="{Binding Path=Rank}"/>
	                <TextBlock Text="{Binding Path=PlayerName}" Grid.Column="1"/>
	                <TextBlock Text="{Binding Path=Score}" Grid.Column="2"/>
	            </Grid>
	        </DataTemplate>
	    </ListView.ItemTemplate>
	</ListView>

所有資料繫結都會透過檢視模型來執行。

## 發佈行動服務

在此步驟中，您會將行動服務發行至 Microsoft Azure，並修改應用程式以使用即時服務。

在 [方案總管] 中，以滑鼠右鍵按一下排行榜專案，然後選取 [發行]。
 
![][12]

在 [發佈] 對話方塊中，按一下 [Azure 行動服務]。

![][13]
 
如果您尚未登入 Azure 帳戶，請按一下 [登入]。

![][14]


選取現有的行動服務，或按一下 [新增] 以建立新的。然後按一下 [確定]，以執行發行。

![][15]
 
發行程序會自動建立資料庫。您無須設定連接字串。

現在，您已可將排行榜應用程式連接到即時服務。您需要兩個項目：

- 服務的 URL
- 應用程式金鑰

您可以從 Azure 管理入口網站取得這兩個項目。在管理入口網站中按一下 [行動服務]，然後按一下行動服務。服務 URL 會列示在排行榜索引標籤上。若要取得應用程式金鑰，請按一下 [管理金鑰]。

![][16]
 
在 [管理存取金鑰] 對話方塊中，複製應用程式金鑰的值。

![][17]

 
將服務 URL 和應用程式金鑰傳至 **MobileServiceClient** 建構函式。

    sealed partial class App : Application
    {
        // TODO: Replace these strings with the real URL and key.
        const string serviceUrl = "https://yourapp.azure-mobile.net/";
        const string appKey = "YOUR ACCESSS KEY";

        public static MobileServiceClient MobileService = new MobileServiceClient(serviceUrl, appKey);

       // ...

現在當您執行應用程式時，它將會與實際服務通訊。

## 後續步驟

* [深入了解 Azure 行動服務]
* [深入了解 Web API] (英文)
* [處理資料庫寫入衝突]
* [新增推播通知]；例如，當某人新增玩家或更新分數時。
* [開始使用驗證]

<!-- Anchors. -->
[Overview]: #overview
[About the sample app]: #about-the-sample-app
[Create the project]: #create-the-project
[Add data models]: #add-data-models
[Add Web API controllers]: #add-web-api-controllers
[Use a DTO to return related entities]: #use-a-dto-to-return-related-entities
[Define a custom API to submit scores]: #define-a-custom-api-to-submit-scores
[Create the Windows Store app]: #create-the-windows-store-app
[Add model classes]: #add-model-classes
[Create a view model]: #create-a-view-model
[Add a MobileServiceClient instance]: #add-a-mobileserviceclient-instance
[Create the main page]: #create-the-main-page
[Publish your mobile service]: #publish-your-mobile-service
[Next Steps]: #next-steps

<!-- Images. -->

[1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/01leaderboard.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/02leaderboard.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/03leaderboard.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/04leaderboard.png
[5]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/05leaderboard.png
[6]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/06leaderboard.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/07leaderboard.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/08leaderboard.png
[9]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/09leaderboard.png
[10]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/10leaderboard.png
[11]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/11leaderboard.png
[12]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/12leaderboard.png
[13]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/13leaderboard.png
[14]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/14leaderboard.png
[15]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/15leaderboard.png
[16]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/16leaderboard.png
[17]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/17leaderboard.png

<!-- URLs. -->

[深入了解 Azure 行動服務]: /develop/mobile/resources/
[深入了解 Web API]: http://asp.net/web-api
[處理資料庫寫入衝突]: mobile-services-windows-store-dotnet-handle-database-conflicts.md
[新增推播通知]: ../notification-hubs-windows-store-dotnet-get-started.md
[開始使用驗證]: /develop/mobile/tutorials/get-started-with-users-dotnet

 

<!---HONumber=July15_HO2-->