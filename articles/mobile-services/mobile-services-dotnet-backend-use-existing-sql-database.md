<properties 
	pageTitle="使用現有的 SQL Database 和行動服務 .NET 後端建置服務 - Azure 行動服務" 
	description="了解如何對於 .NET 型行動服務使用現有雲端或內部部署 SQL 資料庫。" 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor="mollybos"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="05/20/2015" 
	ms.author="glenga"/>


# 使用現有的 SQL Database 和行動服務 .NET 後端建置服務

行動服務 .NET 後端可方便您運用現有的資產來建置行動服務。在內部部署或雲端中使用現有 SQL Database (可能已有其他應用程式正在使用)，讓現有資料可供行動用戶端使用，是特別有趣的案例之一。在此情況下，資料庫模型 (或*結構描述*) 必須保持不變，現有的方案才能繼續運作。

<a name="ExistingModel"></a>
## 探索現有的資料庫模型

在本教學課程中，我們將使用以您的行動服務建立的資料庫，但不會使用已建立的預設模型。我們將手動建立任意模型，以代表您可能會有的現有應用程式。如需如何改為連接到內部部署資料庫的完整相關資訊，請參閱[使用混合式連線從 Azure 行動服務連線至內部佈署 SQL Server](mobile-services-dotnet-backend-hybrid-connections-get-started.md)。

1. 首先，請在 **Visual Studio 2013 Update 2** 中建立行動服務伺服器專案，或使用您可以在服務的 [行動服務] 索引標籤上 (在 [Azure 管理入口網站](http://manage.windowsazure.com)中) 下載的快速入門專案。基於本教學課程的用途，我們假設您的伺服器專案名稱是 **ShoppingService**。

2. 在 **Models** 資料夾中建立 **Customer.cs** 檔案，然後使用下列實作方式。您必須將 **System.ComponentModel.DataAnnotations** 的組件參考新增至您的專案。

        using System.Collections.Generic;
        using System.ComponentModel.DataAnnotations;

        namespace ShoppingService.Models
        {
            public class Customer
            {
                [Key]
                public int CustomerId { get; set; }
                
                public string Name { get; set; }

                public virtual ICollection<Order> Orders { get; set; }

            }
        }

3. 在 **Models** 資料夾中建立 **Order.cs** 檔案，然後使用下列實作方式：
    
        using System.ComponentModel.DataAnnotations;

        namespace ShoppingService.Models
        {
            public class Order
            {
                [Key]
                public int OrderId { get; set; }

                public string Item { get; set; }

                public int Quantity { get; set; }

                public bool Completed { get; set; }

                public int CustomerId { get; set; }
              
                public virtual Customer Customer { get; set; }

            }
        }

    您會發現這兩個類別有此關係：每個 **Order** 會與單一 **Customer** 產生關聯，而一個 **Customer** 可與多個 **Orders** 產生關聯。互相有關係在現有的資料模型中是很常見的。

4. 在 **Models** 資料夾中建立 **ExistingContext.cs** 檔案，並以下列方式實作：

        using System.Data.Entity;

        namespace ShoppingService.Models
        {
            public class ExistingContext : DbContext
            {
                public ExistingContext()
                    : base("Name=MS_TableConnectionString")
                {
                }

                public DbSet<Customer> Customers { get; set; }

                public DbSet<Order> Orders { get; set; }

            }
        }

前述結構很接近您可能已用於現有應用程式的現有 Entity Framework 模型。請注意，在此階段中，模型無法以任何方式辨識行動服務。

<a name="DTOs"></a>
## 為您的行動服務建立資料傳輸物件 (DTO)

您想要在行動服務中使用的資料模型可能很複雜；其中可能包含數百個具有各種相互關係的實體。在建置行動應用程式時，我們通常會想要簡化資料模型並消除關係 (或手動加以處理)，以盡可能減少在應用程式與服務之間來回傳送的裝載。在本節中，我們將建立一組簡化的物件 (稱為「資料傳輸物件」或 "DTO")，並將其對應至您在資料庫中擁有的資料，但僅包含您的行動應用程式所需的基本屬性集。

1. 在您服務專案的 **DataObjects** 資料夾中建立 **MobileCustomer.cs** 檔案，然後使用下列實作方式：

        using Microsoft.WindowsAzure.Mobile.Service;

        namespace ShoppingService.DataObjects
        {
            public class MobileCustomer : EntityData
            {
                public string Name { get; set; }
            }
        }

    請注意，此類別會與模型中的 **Customer** 類別相類似，差別在於 **Order** 的關係屬性已移除。一個物件要能夠正常使用行動服務離線同步，必須要有一組*系統屬性*來維護開放式並行性，因此您會發現 DTO 繼承自 [**EntityData**](http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.entitydata.aspx)，而其中包含這些屬性。原始模型中的 int 型 **CustomerId** 屬性會取代為 **EntityData** 中的字串型 **Id** 屬性，而這將是行動服務所使用的 [識別碼]。

2. 在您服務專案的 **DataObjects** 資料夾中建立 **MobileOrder.cs** 檔案。

        using Microsoft.WindowsAzure.Mobile.Service;
        using Newtonsoft.Json;
        using System.ComponentModel.DataAnnotations;
        using System.ComponentModel.DataAnnotations.Schema;

        namespace ShoppingService.DataObjects
        {
            public class MobileOrder : EntityData
            {
                public string Item { get; set; }

                public int Quantity { get; set; }

                public bool Completed { get; set; }

                [NotMapped]
                public int CustomerId { get; set; }

                [Required]
                public string MobileCustomerId { get; set; }

                public string MobileCustomerName { get; set; }
            }
        }

    **Customer** 關係屬性已取代為**客戶**名稱，以及可用來為用戶端上的關係手動建立模型的 **MobileCustomerId** 屬性。現在您可以忽略 **CustomerId** 屬性，後續才會使用此屬性。

3. 您可能會發現，在 **EntityData** 基底類別上加上系統屬性後，我們的 DTO 此時的屬性數目比模型類型還多。顯然，我們需要一個位置來儲存這些屬性，因此我們將在原始資料庫中額外新增一些資料行。雖然這樣會變更資料庫，但並不會中斷現有的應用程式，因為這些變更只是附加的 (將新的資料行新增至結構描述)。若要這麼做，請將下列陳述式新增至 **Customer.cs** 和 **Order.cs** 的最上方：
    
        using System.ComponentModel.DataAnnotations.Schema;
        using Microsoft.WindowsAzure.Mobile.Service.Tables;
        using System.ComponentModel.DataAnnotations;
        using System;

4. 接著，將這些額外的屬性新增至各個類別：

        [DatabaseGenerated(DatabaseGeneratedOption.Identity)]
        [Index]
        [TableColumn(TableColumnType.CreatedAt)]
        public DateTimeOffset? CreatedAt { get; set; }

        [TableColumn(TableColumnType.Deleted)]
        public bool Deleted { get; set; }

        [Index]
        [TableColumn(TableColumnType.Id)]
        [MaxLength(36)]
        public string Id { get; set; }

        [DatabaseGenerated(DatabaseGeneratedOption.Computed)]
        [TableColumn(TableColumnType.UpdatedAt)]
        public DateTimeOffset? UpdatedAt { get; set; }

        [TableColumn(TableColumnType.Version)]
        [Timestamp]
        public byte[] Version { get; set; }

4. 剛剛新增的系統屬性具有某些會在資料庫作業期間明確產生的內建行為 (例如，自動更新建立/更新時間)。若要啟用這些行為，我們必須變更 **ExistingContext.cs**。在檔案的最上方，新增下列項目：
    
        using System.Data.Entity.ModelConfiguration.Conventions;
        using Microsoft.WindowsAzure.Mobile.Service.Tables;
        using System.Linq;

5. 在 **ExistingContext** 的本文中覆寫 [**OnModelCreating**](http://msdn.microsoft.com/library/system.data.entity.dbcontext.onmodelcreating.aspx)：

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
            modelBuilder.Conventions.Add(
                new AttributeToColumnAnnotationConvention<TableColumnAttribute, string>(
                    "ServiceTableColumn", (property, attributes) => attributes.Single().ColumnType.ToString()));

            base.OnModelCreating(modelBuilder);
        } 

5. 我們將在資料庫中填入某些範例資料。開啟檔案 **WebApiConfig.cs**。建立新的 [**IDatabaseInitializer**](http://msdn.microsoft.com/library/gg696323.aspx)，並依照下列方式在 **Register** 方法中加以設定。

        using Microsoft.WindowsAzure.Mobile.Service;
        using ShoppingService.Models;
        using System;
        using System.Collections.Generic;
        using System.Collections.ObjectModel;
        using System.Data.Entity;
        using System.Web.Http;

        namespace ShoppingService
        {
            public static class WebApiConfig
            {
                public static void Register()
                {
                    ConfigOptions options = new ConfigOptions();

                    HttpConfiguration config = ServiceConfig.Initialize(new ConfigBuilder(options));

                    Database.SetInitializer(new ExistingInitializer());
                }
            }

            public class ExistingInitializer : ClearDatabaseSchemaIfModelChanges<ExistingContext>
            {
                protected override void Seed(ExistingContext context)
                {
                    List<Order> orders = new List<Order>
                    {
                        new Order { OrderId = 10, Item = "Guitars", Quantity = 2, Id = Guid.NewGuid().ToString()},
                        new Order { OrderId = 20, Item = "Drums", Quantity = 10, Id = Guid.NewGuid().ToString()},
                        new Order { OrderId = 30, Item = "Tambourines", Quantity = 20, Id = Guid.NewGuid().ToString() }
                    };

                    List<Customer> customers = new List<Customer>
                    {
                        new Customer { CustomerId = 1, Name = "John", Orders = new Collection<Order> { 
                            orders[0]}, Id = Guid.NewGuid().ToString()},
                        new Customer { CustomerId = 2, Name = "Paul", Orders = new Collection<Order> { 
                            orders[1]}, Id = Guid.NewGuid().ToString()},
                        new Customer { CustomerId = 3, Name = "Ringo", Orders = new Collection<Order> { 
                            orders[2]}, Id = Guid.NewGuid().ToString()},
                    };

                    foreach (Customer c in customers)
                    {
                        context.Customers.Add(c);
                    }

                    base.Seed(context);
                }
            }
        }

<a name="Mapping"></a>
## 在 DTO 與模型之間建立對應

現在我們已有模型類型 **Customer** 和 **Order** 以及 DTO **MobileCustomer** 和 **MobileOrder**，但我們必須指示後端自動在兩者之間轉換。此時，行動服務需倚賴 [**AutoMapper**](http://automapper.org/)，這是一個物件關聯式對應程式，已在專案中受到參考。

1. 在 **WebApiConfig.cs** 的最上方新增下列項目：

        using AutoMapper;
        using ShoppingService.DataObjects;

2. 若要定義對應，請將下列項目新增至 **WebApiConfig** 類別的 **Register** 方法。

        Mapper.Initialize(cfg =>
        {
            cfg.CreateMap<MobileOrder, Order>();
            cfg.CreateMap<MobileCustomer, Customer>();
            cfg.CreateMap<Order, MobileOrder>()
                .ForMember(dst => dst.MobileCustomerId, map => map.MapFrom(x => x.Customer.Id))
                .ForMember(dst => dst.MobileCustomerName, map => map.MapFrom(x => x.Customer.Name));
            cfg.CreateMap<Customer, MobileCustomer>();

        });

AutoMapper 此時會將物件互相對應。所有具有對應名稱的屬性都將相符，例如，**MobileOrder.CustomerId** 將會自動對應至 **Order.CustomerId**。自訂對應可依照下列方式設定，其中，我們將 **MobileCustomerName** 屬性對應至 **Customer** 關係屬性的 **Name** 屬性。

<a name="DomainManager"></a>
## 實作網域特定邏輯

下一個步驟是實作 [**MappedEntityDomainManager**](http://msdn.microsoft.com/library/dn643300.aspx)，這會作為我們對應的資料存放區與將會從我們的用戶端提供 HTTP 流量的控制器之間的抽象層。我們將可在下一個區段中完全以 DTO 的形式撰寫我們的控制器，而我們在此處新增的 **MappedEntityDomainManager**，將會處理與原始資料存放區之間的通訊，同時讓我們有地方可以實作其任何的特定邏輯。

1. 將 **MobileCustomerDomainManager.cs** 新增至您專案的 **Models** 資料夾。貼上下列實作方式：

        using AutoMapper;
        using Microsoft.WindowsAzure.Mobile.Service;
        using ShoppingService.DataObjects;
        using System.Data.Entity;
        using System.Linq;
        using System.Net.Http;
        using System.Threading.Tasks;
        using System.Web.Http;
        using System.Web.Http.OData;

        namespace ShoppingService.Models
        {
            public class MobileCustomerDomainManager : MappedEntityDomainManager<MobileCustomer, Customer>
            {
                private ExistingContext context;

                public MobileCustomerDomainManager(ExistingContext context, HttpRequestMessage request, ApiServices services)
                    : base(context, request, services)
                {
                    Request = request;
                    this.context = context;
                }

                public static int GetKey(string mobileCustomerId, DbSet<Customer> customers, HttpRequestMessage request)
                {
                    int customerId = customers
                       .Where(c => c.Id == mobileCustomerId)
                       .Select(c => c.CustomerId)
                       .FirstOrDefault();

                    if (customerId == 0)
                    {
                        throw new HttpResponseException(request.CreateNotFoundResponse());
                    }
                    return customerId;
                }

                protected override T GetKey<T>(string mobileCustomerId)
                {
                    return (T)(object)GetKey(mobileCustomerId, this.context.Customers, this.Request);
                }
                
                public override SingleResult<MobileCustomer> Lookup(string mobileCustomerId)
                {
                    int customerId = GetKey<int>(mobileCustomerId);
                    return LookupEntity(c => c.CustomerId == customerId);
                }

                public override async Task<MobileCustomer> InsertAsync(MobileCustomer mobileCustomer)
                {
                    return await base.InsertAsync(mobileCustomer);
                }

                public override async Task<MobileCustomer> UpdateAsync(string mobileCustomerId, Delta<MobileCustomer> patch)
                {
                    int customerId = GetKey<int>(mobileCustomerId);

                    Customer existingCustomer = await this.Context.Set<Customer>().FindAsync(customerId);
                    if (existingCustomer == null)
                    {
                        throw new HttpResponseException(this.Request.CreateNotFoundResponse());
                    }

                    MobileCustomer existingCustomerDTO = Mapper.Map<Customer, MobileCustomer>(existingCustomer);
                    patch.Patch(existingCustomerDTO);
                    Mapper.Map<MobileCustomer, Customer>(existingCustomerDTO, existingCustomer);

                    await this.SubmitChangesAsync();

                    MobileCustomer updatedCustomerDTO = Mapper.Map<Customer, MobileCustomer>(existingCustomer);

                    return updatedCustomerDTO;
                }

                public override async Task<MobileCustomer> ReplaceAsync(string mobileCustomerId, MobileCustomer mobileCustomer)
                {
                    return await base.ReplaceAsync(mobileCustomerId, mobileCustomer);
                }

                public override async Task<bool> DeleteAsync(string mobileCustomerId)
                {
                    int customerId = GetKey<int>(mobileCustomerId);
                    return await DeleteItemAsync(customerId);
                }
            }
        }

    此類別的重要部分是 **GetKey** 方法；我們可在其中指出如何在原始資料模型中找出物件的 ID 屬性。

2. 將 **MobileOrderDomainManager.cs** 新增至您專案的 **Models** 資料夾：

        using AutoMapper;
        using Microsoft.WindowsAzure.Mobile.Service;
        using ShoppingService.DataObjects;
        using System.Linq;
        using System.Net.Http;
        using System.Threading.Tasks;
        using System.Web.Http;
        using System.Web.Http.OData;

        namespace ShoppingService.Models
        {
            public class MobileOrderDomainManager : MappedEntityDomainManager<MobileOrder, Order>
            {
                private ExistingContext context;

                public MobileOrderDomainManager(ExistingContext context, HttpRequestMessage request, ApiServices services)
                    : base(context, request, services)
                {
                    Request = request;
                    this.context = context;
                }

                protected override T GetKey<T>(string mobileOrderId)
                {
                    int orderId = this.context.Orders
                        .Where(o => o.Id == mobileOrderId)
                        .Select(o => o.OrderId)
                        .FirstOrDefault();

                    if (orderId == 0)
                    {
                        throw new HttpResponseException(this.Request.CreateNotFoundResponse());
                    }
                    return (T)(object)orderId;
                }

                public override SingleResult<MobileOrder> Lookup(string mobileOrderId)
                {
                    int orderId = GetKey<int>(mobileOrderId);
                    return LookupEntity(o => o.OrderId == orderId);
                }

                private async Task<Customer> VerifyMobileCustomer(string mobileCustomerId, string mobileCustomerName)
                {
                    int customerId = MobileCustomerDomainManager.GetKey(mobileCustomerId, this.context.Customers, this.Request);
                    Customer customer = await this.context.Customers.FindAsync(customerId);
                    if (customer == null)
                    {
                        throw new HttpResponseException(Request.CreateBadRequestResponse("Customer with name '{0}' was not found", mobileCustomerName));
                    }
                    return customer;
                }

                public override async Task<MobileOrder> InsertAsync(MobileOrder mobileOrder)
                {
                    Customer customer = await VerifyMobileCustomer(mobileOrder.MobileCustomerId, mobileOrder.MobileCustomerName);
                    mobileOrder.CustomerId = customer.CustomerId;
                    return await base.InsertAsync(mobileOrder);
                }

                public override async Task<MobileOrder> UpdateAsync(string mobileOrderId, Delta<MobileOrder> patch)
                {
                    Customer customer = await VerifyMobileCustomer(patch.GetEntity().MobileCustomerId, patch.GetEntity().MobileCustomerName);

                    int orderId = GetKey<int>(mobileOrderId);

                    Order existingOrder = await this.Context.Set<Order>().FindAsync(orderId);
                    if (existingOrder == null)
                    {
                        throw new HttpResponseException(this.Request.CreateNotFoundResponse());
                    }

                    MobileOrder existingOrderDTO = Mapper.Map<Order, MobileOrder>(existingOrder);
                    patch.Patch(existingOrderDTO);
                    Mapper.Map<MobileOrder, Order>(existingOrderDTO, existingOrder);

                    // This is required to map the right Id for the customer
                    existingOrder.CustomerId = customer.CustomerId;

                    await this.SubmitChangesAsync();

                    MobileOrder updatedOrderDTO = Mapper.Map<Order, MobileOrder>(existingOrder);

                    return updatedOrderDTO;
                }

                public override async Task<MobileOrder> ReplaceAsync(string mobileOrderId, MobileOrder mobileOrder)
                {
                    await VerifyMobileCustomer(mobileOrder.MobileCustomerId, mobileOrder.MobileCustomerName);

                    return await base.ReplaceAsync(mobileOrderId, mobileOrder);
                }

                public override Task<bool> DeleteAsync(string mobileOrderId)
                {
                    int orderId = GetKey<int>(mobileOrderId);
                    return DeleteItemAsync(orderId);
                }
            }
        }

    在此情況下，**InsertAsync** 和 **UpdateAsync** 方法是很值得了解的；我們會在其中強制執行每個 **Order** 都必須有一個有效相關 **Customer** 的關係。在 **InsertAsync** 中，您會看見我們填入 **MobileOrder.CustomerId** 屬性，而此屬性對應至 **Order.CustomerId** 屬性。我們藉由基底查閱具有相符 **MobileOrder.MobileCustomerId** 的 **Customer**，來取得該值。這是因為，依預設用戶端只能辨識 **Customer** 的行動服務識別碼 (**MobileOrder.MobileCustomerId**)，而此識別碼與將外部索引鍵 (**MobileOrder.CustomerId**) 從 **Order** 設為 **Customer** 所需的實際主要索引鍵並不相同。此識別碼僅供服務內部使用，以加快插入作業的速度。

我們現在已可建立控制器，以將 DTO 公開至我們的用戶端。

<a name="Controller"></a>
## 使用 DTO 實作 TableController

1. 在 **Controllers** 資料夾中，新增檔案 **MobileCustomerController.cs**：

        using Microsoft.WindowsAzure.Mobile.Service;
        using Microsoft.WindowsAzure.Mobile.Service.Security;
        using ShoppingService.DataObjects;
        using ShoppingService.Models;
        using System.Linq;
        using System.Threading.Tasks;
        using System.Web.Http;
        using System.Web.Http.Controllers;
        using System.Web.Http.OData;

        namespace ShoppingService.Controllers
        {
            public class MobileCustomerController : TableController<MobileCustomer>
            {
                protected override void Initialize(HttpControllerContext controllerContext)
                {
                    base.Initialize(controllerContext);
                    var context = new ExistingContext();
                    DomainManager = new MobileCustomerDomainManager(context, Request, Services);
                }

                public IQueryable<MobileCustomer> GetAllMobileCustomers()
                {
                    return Query();
                }

                public SingleResult<MobileCustomer> GetMobileCustomer(string id)
                {
                    return Lookup(id);
                }

                [AuthorizeLevel(AuthorizationLevel.Admin)]
                protected override Task<MobileCustomer> PatchAsync(string id, Delta<MobileCustomer> patch)
                {
                    return base.UpdateAsync(id, patch);
                }

                [AuthorizeLevel(AuthorizationLevel.Admin)]
                protected override Task<MobileCustomer> PostAsync(MobileCustomer item)
                {
                    return base.InsertAsync(item);
                }

                [AuthorizeLevel(AuthorizationLevel.Admin)]
                protected override Task DeleteAsync(string id)
                {
                    return base.DeleteAsync(id);
                }
            }
        }

    您將必須記下 AuthorizeLevel 屬性用來對控制器上的插入/更新/刪除作業進行公用存取限定的使用情形。基於此案例的用途，Customer 的清單將是唯讀的，但我們將允許建立新 Order，並將其與現有客戶產生關聯。

2. 在 **Controllers** 資料夾中，新增檔案 **MobileOrderController.cs**：

        using Microsoft.WindowsAzure.Mobile.Service;
        using ShoppingService.DataObjects;
        using ShoppingService.Models;
        using System.Linq;
        using System.Threading.Tasks;
        using System.Web.Http;
        using System.Web.Http.Controllers;
        using System.Web.Http.Description;
        using System.Web.Http.OData;

        namespace ShoppingService.Controllers
        {
            public class MobileOrderController : TableController<MobileOrder>
            {
                protected override void Initialize(HttpControllerContext controllerContext)
                {
                    base.Initialize(controllerContext);
                    ExistingContext context = new ExistingContext();
                    DomainManager = new MobileOrderDomainManager(context, Request, Services);
                }

                public IQueryable<MobileOrder> GetAllMobileOrders()
                {
                    return Query();
                }

                public SingleResult<MobileOrder> GetMobileOrder(string id)
                {
                    return Lookup(id);
                }

                public Task<MobileOrder> PatchMobileOrder(string id, Delta<MobileOrder> patch)
                {
                    return UpdateAsync(id, patch);
                }

                [ResponseType(typeof(MobileOrder))]
                public async Task<IHttpActionResult> PostMobileOrder(MobileOrder item)
                {
                    MobileOrder current = await InsertAsync(item);
                    return CreatedAtRoute("Tables", new { id = current.Id }, current);
                }

                public Task DeleteMobileOrder(string id)
                {
                    return DeleteAsync(id);
                }
            }
        }

3. 您現在已可執行您的服務。按 **F5** 鍵，然後使用內建於說明頁面中的測試用戶端來修改資料。

請注意，這兩個控制器實作方式會互斥使用 DTO **MobileCustomer** 和 **MobileOrder**，且無從驗證基礎模型。這些 DTO 已序列化為 JSON，並可用來與所有平台上的行動服務用戶端 SDK 交換資料。例如，如果建置 Windows 市集應用程式，對應的用戶端類型將如下所示。此類型將與其他用戶端平台上的類型相似。

    using Microsoft.WindowsAzure.MobileServices;
    using System;

    namespace ShoppingClient
    {
        public class MobileCustomer
        {
            public string Id { get; set; }

            public string Name { get; set; }

            [CreatedAt]
            public DateTimeOffset? CreatedAt { get; set; }

            [UpdatedAt]
            public DateTimeOffset? UpdatedAt { get; set; }

            public bool Deleted { get; set; }
            
            [Version]
            public string Version { get; set; }

        }

    }

您可在此時建置用來存取服務的用戶端應用程式，以執行下一個步驟。如需詳細資訊，請參閱[將行動服務新增至現有的應用程式](mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data.md#update-the-app-to-use-the-mobile-service)。

<!---HONumber=July15_HO2-->