<properties 
	pageTitle="使用 Pusher 建立即時應用程式 (iOS) - 行動服務" 
	description="了解如何使用 Pusher 將通知傳送到 iOS 上的 Azure 媒體服務應用程式。" 
	services="mobile-services" 
	documentationCenter="ios" 
	authors="lindydonna" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="06/05/2015" 
	ms.author="donnam"/>


# 使用行動服務和 Pusher 建立即時應用程式
<div class="dev-center-tutorial-selector sublanding">
	<a href="" title="iOS" class="current">iOS</a>
</div>

本主題將示範如何在您的 Azure 行動服務型應用程式中新增即時功能。完成時，您的 TodoList 資料將在您應用程式所有執行中的執行個體上即時同步。

[推播通知給使用者][]教學課程將示範如何使用推播通知來通知使用者 Todo 清單中有新項目。推播通知相當適合用於顯示非經常性變更。不過，應用程式有時候需要經常性即時通知。使用 Pusher API 即可將即時通知新增至行動服務。在本教學課程中，我們將行動服務搭配 Pusher 使用，以便在應用程式的任何執行中執行個體發生變更時，保持 Todo 清單同步。

Pusher 是一種雲端型服務，和行動服務一樣，能夠使得建立即時應用程式變得相當輕鬆容易。您可以使用 Pusher 快速建立即時民調、聊天室、多人遊戲和共同作業應用程式 (以廣播即時資料和內容)，不過優點不僅止於此！ 如需詳細資訊，請參閱 [http://pusher.com](http://pusher.com) (英文)。

本教學課程將逐步引導您完成下列基本步驟，將即時共同作業新增至 Todo 清單應用程式：

1. [建立 Pusher 帳戶][]
2. [更新應用程式][]
3. [安裝伺服器指令碼][]
4. [測試應用程式][]

本教學課程會以行動服務快速入門為基礎。在開始本教學課程之前，您必須首先完成[開始使用行動服務][]。

## <a name="sign-up"></a>建立新的 Pusher 帳戶

[AZURE.INCLUDE [pusher-sign-up](../../includes/pusher-sign-up.md)]

## <a name="update-app"></a>更新應用程式

設定 Pusher 帳戶後，下一步是修改 iOS 應用程式的程式碼來加入新功能。

###安裝 libPusher 程式庫

[libPusher][] 程式庫可以讓您從 iOS 存取 Pusher。

1. [從這裡][libPusherDownload] (英文) 下載 libPusher 程式庫。

2. 在您的專案中建立名稱為 _libPusher_ 的群組。

3. 在尋找程式中，將下載的 zip 檔解壓縮，並選取 **libPusher-combined.a** 及 **/headers** 資料夾，然後將這些項目拖曳至您專案的 **libPusher** 群組中。

4. 勾選 [Copy items into destination group's folder]，然後按一下 [完成]

	![][add-files-to-group]

   這會將 libPusher 檔案複製到您的專案。

5. 在專案總管的專案根目錄中，按一下 [Build Phases]，然後按一下 [Add Build Phase] 及 [Add Copy Files]。

6. 從專案總管將 **libPusher-combined.a** 檔案拖曳至新的組建階段。

7. 將 [目的地] 變更為 [Frameworks]，並按一下 [Copy only when installing]。

	![][add-build-phase]

8. 在 [Link Binary With Libraries] 區域中，新增下列程式庫：

	- libicucore.dylib
	- CFNetwork.framework
	- Security.framework
	- SystemConfiguration.framework

9. 最後，在 [組建設定] 中，找出目標組建設定 [Other Linker Flags]，並新增 **-all_load** 旗標。

	![][add-linker-flag]

	這會顯示對偵錯組建目標設定的 **-all_load** 旗標。

程式庫現已安裝好可供使用。

### 新增程式碼至應用程式

1. 在 Xcode 中，開啟 **QSTodoService.h** 檔案並新增下列方法宣告：

        // Allows retrieval of items by id
        - (NSUInteger) getItemIndex:(NSDictionary *)item;

        // To be called when items are added by other users
        - (NSUInteger) itemAdded:(NSDictionary *)item;

        // To be called when items are completed by other users
        - (NSUInteger) itemCompleted:(NSDictionary *)item;

2. 以下列內容取代現有的 **addItem** 和 **completeItem** 宣告：

		- (void) addItem:(NSDictionary *) item;
		- (void) completeItem: (NSDictionary *) item;

3. 在 **QSTodoService.m** 中，新增下列程式碼來實作新方法：

        // Allows retrieval of items by id
		- (NSUInteger) getItemIndex:(NSDictionary *)item
		{
		    NSInteger itemId = [[item objectForKey: @"id"] integerValue];

		    return [items indexOfObjectPassingTest:^BOOL(id currItem, NSUInteger idx, BOOL *stop)
                 {
                     return ([[currItem objectForKey: @"id"] integerValue] == itemId);
                 }];
		}

        // To be called when items are added by other users
        -(NSUInteger) itemAdded:(NSDictionary *)item
		{
		    NSUInteger index = [self getItemIndex:item];

		    // Only complete action if item not already in list
		    if(index == NSNotFound)
		    {
        		NSUInteger newIndex = [items count];
		        [(NSMutableArray *)items insertObject:item atIndex:newIndex];
		        return newIndex;
		    }
		    else
        		return -1;
		}

        // To be called when items are completed by other users
		- (NSUInteger) itemCompleted:(NSDictionary *)item
		{
		    NSUInteger index = [self getItemIndex:item];

		    // Only complete action if item exists in items list
		    if(index != NSNotFound)
		    {
		        NSMutableArray *mutableItems = (NSMutableArray *) items;
		        [mutableItems removeObjectAtIndex:index];
		    }
		    return index;
		}

	QSTodoService 現在可讓您依 **id** 尋找項目，而且在本機新增並完成項目，完全不需要向遠端服務傳送明確要求。

4. 以下列程式碼取代現有的 **addItem** 和 **completeItem** 方法：

		-(void) addItem:(NSDictionary *)item
		{
		    // Insert the item into the TodoItem table and add to the items array on completion
		    [self.table insert:item completion:^(NSDictionary *result, NSError *error) {
        		[self logErrorIfNotNil:error];
		    }];
		}

		-(void) completeItem:(NSDictionary *)item
		{
		    // Set the item to be complete (we need a mutable copy)
		    NSMutableDictionary *mutable = [item mutableCopy];
		    [mutable setObject:@(YES) forKey:@"complete"];

		    // Update the item in the TodoItem table and remove from the items array on completion
		    [self.table update:mutable completion:^(NSDictionary *item, NSError *error) {
		        [self logErrorIfNotNil:error];
		    }];
		}


	請注意，當收到來自 Pusher 的事件時 (而非當資料表有更新時)，就會新增並完成項目，並連 UI 一併更新。

5. 在 **QSTodoListViewController.h** 檔案中，新增下列 import 陳述式：

		#import "PTPusherDelegate.h"
		#import "PTPusher.h"
		#import "PTPusherEvent.h"
		#import "PTPusherChannel.h"

6. 修改介面宣告為新增 **PTPusherDelegate**，如下所示：

		@interface QSTodoListViewController : UITableViewController<UITextFieldDelegate, PTPusherDelegate>

7. 新增下列新屬性：

		@property (nonatomic, strong) PTPusher *pusher;

8. 新增下列程式碼來宣告新方法：

		// Sets up the Pusher client
		- (void) setupPusher;

9. 在 **QSTodoListViewController.m** 中，將下列一行新增至其餘 **@synthesise** 行之下，以便實作新屬性：

		@synthesize pusher = _pusher;

10. 現在，新增下列程式碼來實作新方法：

		// Sets up the Pusher client
		- (void) setupPusher {

			// Create a Pusher client, using your Pusher app key as the credential
		    // TODO: Move Pusher app key to configuration file
		    self.pusher = [PTPusher pusherWithKey:@"**your_app_key**" delegate:self encrypted:NO];
		    self.pusher.reconnectAutomatically = YES;

    		// Subscribe to the 'todo-updates' channel
		    PTPusherChannel *todoChannel = [self.pusher subscribeToChannelNamed:@"todo-updates"];

    		// Bind to the 'todo-added' event
		    [todoChannel bindToEventNamed:@"todo-added" handleWithBlock:^(PTPusherEvent *channelEvent) {

		    	// Add item to the todo list
        		NSUInteger index = [self.todoService itemAdded:channelEvent.data];

        		// If the item was not already in the list, add the item to the UI
		        if(index != -1)
        		{
		            NSIndexPath *indexPath = [NSIndexPath indexPathForRow:index inSection:0];
        		    [self.tableView insertRowsAtIndexPaths:@[ indexPath ]
                                  withRowAnimation:UITableViewRowAnimationTop];
		        }
		    }];

    		// Bind to the 'todo-completed' event
		    [todoChannel bindToEventNamed:@"todo-completed" handleWithBlock:^(PTPusherEvent *channelEvent) {

		    	// Update the item to be completed
		        NSUInteger index = [self.todoService itemCompleted:channelEvent.data];

        		// As long as the item did exit in the list, update the UI
		        if(index != NSNotFound)
		        {
		            NSIndexPath *indexPath = [NSIndexPath indexPathForRow:index inSection:0];
        		    [self.tableView deleteRowsAtIndexPaths:@[ indexPath ]
                                  withRowAnimation:UITableViewRowAnimationTop];
		        }
		    }];
		}

11. 以稍早從 [連線資訊] 對話方塊複製的 app_key 值，來取代 `**your_app_key**` 預留位置。

12. 以下列程式碼取代 **onAdd** 方法：

		- (IBAction)onAdd:(id)sender
		{
		    if (itemText.text.length  == 0) {
        		return;
		    }

		    NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @(NO) };
		    [self.todoService addItem:item];

		    itemText.text = @"";
		}

13. 在 **QSTodoListViewController.m** 檔案中，找出 (void)viewDidLoad 方法並新增 **setupPusher** 方法的呼叫，使前幾行變成：

		- (void)viewDidLoad
		{
		    [super viewDidLoad];
		    [self setupPusher];

14. 在 **tableView:commitEditingStyle:forRowAtIndexPath** 方法的結尾，以下列程式碼取代 **completeItem** 的呼叫：

		// Ask the todoService to set the item's complete value to YES
	    [self.todoService completeItem:item];

應用程式現在可以接收來自 Pusher 的事件，並據以更新本機 Todo 清單。



##<a name="install-scripts"></a>安裝伺服器指令碼



現在剩下要做的就是設定服務指令碼。我們會插入程式碼，以在 TodoList 資料表中插入或更新了項目時套用。



1. 登入 [Azure 管理入口網站]，按一下 [行動服務]，然後按一下您的行動服務。


2. 在管理入口網站中，按一下 [資料] 索引標籤，然後按一下 [TodoItem] 資料表。

	![][1]



3. 在 [TodoItem] 中，按一下 [指令碼] 索引標籤，然後選取 [插入]。


	![][2]



	這會顯示 [TodoItem] 資料表中發生插入時所叫用的函數。


4. 使用下列程式碼來取代插入函數：


		var Pusher = require('pusher');

		function insert(item, user, request) {

			request.execute({
	    		success: function() {
			        // After the record has been inserted, trigger immediately to the client
	    		    request.respond();

			        // Publish event for all other active clients
			        publishItemCreatedEvent(item);
			    }
			});

			function publishItemCreatedEvent(item) {

				// Ideally these settings would be taken from config
			    var pusher = new Pusher({
				  appId: '**your_app_id**',
				  key: '**your_app_key**',
				  secret: '**your_app_secret**'
				});

				// Publish event on Pusher channel
			    pusher.trigger( 'todo-updates', 'todo-added', item );
			}
		}



5. 以先前從 [連線資訊] 對話方塊中複製的值取代上述指令碼中的預留位置。

	- **`**your_app_id**`**：app&#95;id 值
	- **`**your_app_key**`**：app&#95;key 值
	- **`**your_app_key_secret**`**：app&#95;key&#95;secret


6. 按一下 [儲存] 按鈕。您現在已設定指定碼，可在每次有新項目插入至 **TodoItem** 資料表時，就發佈事件至 Pusher。


7. 從 [作業] 下拉式功能表中，選取 [更新]。


8. 以下列程式碼取代 update 函數：

		var Pusher = require('pusher');

		function update(item, user, request) {

			request.execute({
	    		success: function() {
			        // After the record has been updated, trigger immediately to the client
	    		    request.respond();

			        // Publish event for all other active clients
			        publishItemUpdatedEvent(item);
			    }
			});

			function publishItemUpdatedEvent(item) {

				// Ideally these settings would be taken from config
			    var pusher = new Pusher({
				  appId: '**your_app_id**',
				  key: '**your_app_key**',
				  secret: '**your_app_secret**'
				});

				// Publish event on Pusher channel
			    pusher.trigger( 'todo-updates', 'todo-completed', item );

			}
		}



9. 重複步驟 5，讓此指令碼取代預留位置。


10. 按一下 [儲存] 按鈕。您現在已設定指定碼，可在每次有新項目更新時，就發佈事件至 Pusher。



##<a name="test-app"></a>測試應用程式



若要測試應用程式，您需要執行兩個執行個體。您可以在 iOS 裝置上執行一個執行個體，並在 iOS 模擬器上執行另一個執行個體。

1. 連接 iOS 裝置，按 [執行] 按鈕 (或 Command+R 鍵) 在裝置上啟動應用程式，然後停止偵錯。

	您的應用程式現在已安裝到裝置上。

2. 在 iOS 模擬器上執行應用程式，並同時在 iOS 裝置上啟動應用程式。

	現在這個應用程式有兩個執行個體正在執行。

3. 在其中一個應用程式執行個體中新增一個新的 Todo 項目。

	確認新增的項目出現在另一個執行個體中。

4. 在其中一個應用程式執行個體中查看 Todo 項目以將它標記為完成。

	確認該項目在另一個執行個體中消失。

恭喜，您已經成功設定行動服務應用程式在所有用戶端上即時同步。

## <a name="nextsteps"> </a>後續步驟

您已看見對行動服務使用 Pusher 服務有多輕鬆，現在請參考下列連結深入了解 Pusher。

-   Pusher API 文件：<http://pusher.com/docs>
-   Pusher 教學課程：<http://pusher.com/tutorials>

若要深入了解註冊和使用服務指令碼，請參閱[行動服務伺服器指令碼參照]。

<!-- Anchors. -->
[建立 Pusher 帳戶]: #sign-up
[更新應用程式]: #update-app
[安裝伺服器指令碼]: #install-scripts
[測試應用程式]: #test-app

<!-- Images. -->
[1]: ./media/mobile-services-ios-build-realtime-apps-pusher/mobile-portal-data-tables.png
[2]: ./media/mobile-services-ios-build-realtime-apps-pusher/mobile-insert-script-push2.png

[add-files-to-group]: ./media/mobile-services-ios-build-realtime-apps-pusher/pusher-ios-add-files-to-group.png
[add-build-phase]: ./media/mobile-services-ios-build-realtime-apps-pusher/pusher-ios-add-build-phase.png
[add-linker-flag]: ./media/mobile-services-ios-build-realtime-apps-pusher/pusher-ios-add-linker-flag.png

<!-- URLs. -->
[推播通知給使用者]: /develop/mobile/tutorials/push-notifications-to-users-ios
[開始使用行動服務]: /develop/mobile/tutorials/get-started
[libPusher]: http://go.microsoft.com/fwlink/p?LinkId=276999
[libPusherDownload]: http://go.microsoft.com/fwlink/p/?LinkId=276998


[Azure 管理入口網站]: https://manage.windowsazure.com/

[行動服務伺服器指令碼參照]: http://go.microsoft.com/fwlink/p/?LinkId=262293
 

<!---HONumber=July15_HO1-->