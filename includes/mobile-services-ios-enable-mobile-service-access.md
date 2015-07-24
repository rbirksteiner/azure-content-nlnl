
現在讓我們更新應用程式，以將項目儲存在 Azure 行動服務中，而不是儲存在本機記憶體內部集合中。

* 在 **TodoService.h** 中，尋找下列程式碼行：

```
// TODO - create an MSClient property
```

以下列的程式碼行取代此註解。這會建立一個屬性，表示連線至此服務的 `MSClient`。

```
@property (nonatomic, strong)   MSClient *client;
```


* 在 **TodoService.m** 中，尋找下列程式碼行：

```
// TODO - create an MSTable property for your items
```

在 `@interface` 宣告內以下列的程式碼行取代此註解。這將建立行動服務資料表的代表屬性。

```
@property (nonatomic, strong)   MSTable *table;
```


* 在管理入口網站中按一下 [行動服務]，然後按一下行動服務。按一下 [儀表板] 索引標籤並記下 [網站 URL]。然後按一下 [管理金鑰]，然後記下 [應用程式金鑰]。從應用程式程式碼存取行動服務時，您將會用到這些值。


* 在 **TodoService.m** 中，尋找下列程式碼行：

```
// Initialize the Mobile Service client with your URL and key.
```

在此註解之後，加入下列程式碼行：以您在上個步驟取得的網站 URL 與應用程式金鑰取代 `APPURL` 與 `APPKEY`。

```
self.client = [MSClient clientWithApplicationURLString:@"APPURL" applicationKey:@"APPKEY"];
```


* 在 **TodoService.m** 中，尋找下列程式碼行：

```
// Create an MSTable instance to allow us to work with the TodoItem table.
```

以下列的程式碼行取代此註解。這會建立 TodoItem 資料表執行個體。

```
self.table = [self.client tableWithName:@"TodoItem"];
```


* 在 **TodoService.m** 中，尋找下列程式碼行：

```
// Create a predicate that finds items where complete is false
```

以下列的程式碼行取代此註解。This creates a query to return all tasks that have not yet been completed.

```
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
```


* 尋找下列程式碼行：

```
// Query the TodoItem table and update the items property with the results from the service
```

使用下列程式碼來取代註解和後續 **completion** 區塊引動：

```
[self.table readWhere:predicate completion:^(NSArray *results, NSInteger totalCount, NSError *error)
{
self.items = [results mutableCopy];
   completion();
}];
```


* 尋找 **addItem** 方法，並使用下列程式碼來取代其主體：此程式碼會傳送插入要求給行動服務。

```
// Insert the item into the TodoItem table and add to the items array on completion
[self.table insert:item completion:^(NSDictionary *result, NSError *error) {
    NSUInteger index = [items count];
    [(NSMutableArray *)items insertObject:item atIndex:index];

    // Let the caller know that we finished
    completion(index);
}];
```


* 尋找 **completeItem** 方法，並找出下列加上註解的程式碼行：

```
// Update the item in the TodoItem table and remove from the items array on completion
```

以下列程式碼取代方法的主體 (從該點至方法的結尾)。此程式碼會在待辦事項被標示為已完成時將它移除。

```
// Update the item in the TodoItem table and remove from the items array on completion
[self.table update:mutable completion:^(NSDictionary *item, NSError *error) {

    // Get a fresh index in case the list has changed
    NSUInteger index = [items indexOfObjectIdenticalTo:mutable];

    [mutableItems removeObjectAtIndex:index];

    // Let the caller know that we have finished
    completion(index);
}];
```


* 在 TodoListController.m 中，尋找 **onAdd** 方法並以下列程式碼進行覆寫：

```
- (IBAction)onAdd:(id)sender
{
    if (itemText.text.length  == 0)
    {
        return;
    }

    NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @NO };
    UITableView *view = self.tableView;
    [self.todoService addItem:item completion:^(NSUInteger index)
    {
        NSIndexPath *indexPath = [NSIndexPath indexPathForRow:index inSection:0];
        [view insertRowsAtIndexPaths:@[ indexPath ]
                    withRowAnimation:UITableViewRowAnimationTop];
    }];

    itemText.text = @"";
}
```

<!---HONumber=July15_HO2-->