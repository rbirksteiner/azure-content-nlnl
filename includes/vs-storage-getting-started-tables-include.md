#####建立資料表
**CloudTableClient** 物件可讓您取得資料表和實體的參照物件。下列程式碼會建立 **CloudTableClient** 物件，並使用該物件建立新資料表。此程式碼會嘗試參考名為 “people” 的資料表。 如果找不到該名稱的資料表，則會建立此資料表。

**注意：**本指南的所有程式碼都假設正在建置的應用程式為 Azure 雲端服務專案，並使用 Azure 應用程式的服務設定中所儲存的儲存體連接字串。

	// Create the table client.
	CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
	
	// Create the table if it doesn't exist.
	CloudTable table = tableClient.GetTableReference("people");
	table.CreateIfNotExists();

#####將實體加入至資料表
若要將實體新增至資料表，請建立一個類別來定義實體的屬性。下列程式碼使用客戶名字作為資料列索引鍵、並使用姓氏作為資料分割索引鍵的，定義一個稱為 **CustomerEntity** 的實體類別。

	public class CustomerEntity : TableEntity
	{
	    public CustomerEntity(string lastName, string firstName)
	    {
	        this.PartitionKey = lastName;
	        this.RowKey = firstName;
	    }
	
	    public CustomerEntity() { }
	
	    public string Email { get; set; }
	
	    public string PhoneNumber { get; set; }
	}

涉及實體的資料表操作是利用您稍早在「建立資料表」中建立的 **CloudTable** 物件來執行。 **TableOperation** 物件要執行的操作。下列程式碼範例顯示如何建立 **CloudTable** 物件及 **CustomerEntity** 物件。為了準備這項操作，其建立了 **TableOperation**，以便在資料表中插入客戶實體。最後，呼叫 CloudTable.Execute 來執行操作。

	// Create the CloudTable object that represents the "people" table.
	CloudTable table = tableClient.GetTableReference("people");
	
	// Create a new customer entity.
	CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
	customer1.Email = "Walter@contoso.com";
	customer1.PhoneNumber = "425-555-0101";
	
	// Create the TableOperation that inserts the customer entity.
	TableOperation insertOperation = TableOperation.Insert(customer1);
	
	// Execute the insert operation.
	table.Execute(insertOperation);

#####插入實體批次
您可以在單一寫入操作中將多個項目插入至資料表。下列程式碼範例會建立兩個實體物件 ("Jeff Smith" 和 "Ben Smith")，並利用 Insert 方法將它們加入至 **TableBatchOperation** 物件，然後呼叫 CloudTable.Execute 開始執行操作。

	// Create the CloudTable object that represents the "people" table.
	CloudTable table = tableClient.GetTableReference("people");
	
	// Create the batch operation.
	TableBatchOperation batchOperation = new TableBatchOperation();
	
	// Create a customer entity and add it to the table.
	CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
	customer1.Email = "Jeff@contoso.com";
	customer1.PhoneNumber = "425-555-0104";
	
	// Create another customer entity and add it to the table.
	CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
	customer2.Email = "Ben@contoso.com";
	customer2.PhoneNumber = "425-555-0102";
	
	// Add both customer entities to the batch insert operation.
	batchOperation.Insert(customer1);
	batchOperation.Insert(customer2);
	
	// Execute the batch operation.
	table.ExecuteBatch(batchOperation);

#####取得資料分割中的所有實體
若要向資料表查詢資料分割中的所有實體，請使用 **TableQuery** 物件。下列程式碼範例會指定篩選器來篩選出資料分割索引鍵為 'Smith' 的實體。此範例會將查詢結果中每個實體的欄位列印至主控台。

	// Create the CloudTable object that represents the "people" table.
	CloudTable table = tableClient.GetTableReference("people");
	
	// Construct the query operation for all customer entities where PartitionKey="Smith".
	TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));
	
	// Print the fields for each customer.
	foreach (CustomerEntity entity in table.ExecuteQuery(query))
	{
	    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
	        entity.Email, entity.PhoneNumber);
	}

#####取得單一實體
您可以撰寫查詢來取得單一特定實體。下列程式碼使用 **TableOperation** 物件來指定名為 'Ben Smith' 的客戶。此方法只會傳回一個實體而非集合，且 TableResult.Result 中的傳回值是 **CustomerEntity** 物件。若要從「資料表」服務中擷取單一實體，最快的方法是在查詢中同時指定資料分割索引鍵和資料列索引鍵。

	// Create the CloudTable object that represents the "people" table.
	CloudTable table = tableClient.GetTableReference("people");
	
	// Create a retrieve operation that takes a customer entity.
	TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
	
	// Execute the retrieve operation.
	TableResult retrievedResult = table.Execute(retrieveOperation);
	
	// Print the phone number of the result.
	if (retrievedResult.Result != null)
	   Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
	else
	   Console.WriteLine("The phone number could not be retrieved.");

#####刪除實體
找到實體之後，您可以刪除它。下列程式碼會尋找名為 "Ben Smith" 的客戶實體，如果找到，就刪除它。

	// Create the CloudTable that represents the "people" table.
	CloudTable table = tableClient.GetTableReference("people");
	
	// Create a retrieve operation that expects a customer entity.
	TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
	
	// Execute the operation.
	TableResult retrievedResult = table.Execute(retrieveOperation);
	
	// Assign the result to a CustomerEntity object.
	CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;
	
	// Create the Delete TableOperation and then execute it.
	if (deleteEntity != null)
	{
	   TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
	
	   // Execute the operation.
	   table.Execute(deleteOperation);
	
	   Console.WriteLine("Entity deleted.");
	}
	
	else
	   Console.WriteLine("Couldn't delete the entity.");

[深入了解 Azure 儲存體](http://azure.microsoft.com/documentation/services/storage/)，另請參閱[在伺服器總管中瀏覽儲存體資源](http://msdn.microsoft.com/library/azure/ff683677.aspx)。

<!---HONumber=62-->