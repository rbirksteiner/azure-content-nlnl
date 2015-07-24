<properties 
	pageTitle="使用開放式並行存取處理資料庫寫入衝突 (Windows 市集) | 行動開發人員中心" 
	description="了解如何處理伺服器與 Windows 市集應用程式之間的資料庫寫入衝突。" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/18/2015" 
	ms.author="wesmc"/>

# 處理資料庫寫入衝突



##概觀

本教學課程可協助您深入了解如何處理在兩個或更多用戶端寫入至 Windows 市集應用程式中的相同資料庫記錄時所發生的衝突。在部分案例中，兩個或多個用戶端可能會同時對相同項目寫入變更。在沒有偵測到任何衝突的情況下，最後寫入將覆寫任何先前的更新，即使這不是您想要的結果。Azure 行動服務可支援偵測及解決這些衝突的作業。本主題將逐步引導您完成下列步驟，讓您處理伺服器上與應用程式中的資料庫寫入衝突。

在本教學課程中，您會在快速入門應用程式中新增功能，以處理更新 TodoItem 資料庫時所發生的爭用情況。


##必要條件

本教學課程需要下列各項

+ Microsoft Visual Studio 2013 或更新版本。
+ 本教學課程會以行動服務快速入門為基礎。在開始本教學課程之前，您必須首先完成[開始使用行動服務]。 
+ [Azure 帳戶]
+ Azure 行動服務 NuGet 封裝 1.1.0 或更新版本。若要取得最新版本，請遵循下列步驟：
	1. 在 Visual Studio 中開啟專案，在 [方案總管] 中以滑鼠右鍵按一下專案，然後按一下 [管理 NuGet 封裝]。 

		![][19]

	2. 展開 [線上]，然後按一下 [Microsoft and .NET]。在搜尋文字方塊中，輸入 [Azure 行動服務]。在 [Azure 行動服務] NuGet 封裝上，按一下 [安裝]。

		![][20]


 

##更新應用程式以允許更新

在本節中您將會更新 TodoList 使用者介面，使 ListBox 控制項中各個項目的文字能夠進行更新。ListBox 將包含資料庫資料表中各個項目的 CheckBox 和 TextBox 控制項。您將可更新 TodoItem 的文字欄位。應用程式會處理來自該 TextBox 的 `LostFocus` 事件，以更新資料庫中的項目。


1. 在 Visual Studio 中，開啟您在[開始使用行動服務]教學課程中下載的 TodoList 專案。
2. 在 Visual Studio 的 [方案總管] 中開啟 MainPage.xaml，將 `ListView` 定義以下方顯示的 `ListView` 取代，然後儲存變更。

		<ListView Name="ListItems" Margin="62,10,0,0" Grid.Row="1">
			<ListView.ItemTemplate>
				<DataTemplate>
					<StackPanel Orientation="Horizontal">
						<CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" Checked="CheckBoxComplete_Checked" Margin="10,5" VerticalAlignment="Center"/>
						<TextBox x:Name="ToDoText" Height="25" Width="300" Margin="10" Text="{Binding Text, Mode=TwoWay}" AcceptsReturn="False" LostFocus="ToDoText_LostFocus"/>
					</StackPanel>
				</DataTemplate>
			</ListView.ItemTemplate>
		</ListView>


4. 在 Visual Studio 方案總管中，開啟共用專案中的 MainPage.cs。將事件處理常式新增至 TextBox `LostFocus` 事件的 MainPage，如下所示。


        private async void ToDoText_LostFocus(object sender, RoutedEventArgs e)
        {
            TextBox tb = (TextBox)sender;
            TodoItem item = tb.DataContext as TodoItem;
            //let's see if the text changed
            if (item.Text != tb.Text)
            {
                item.Text = tb.Text;
                await UpdateToDoItem(item);
            }
        }

4. 在共用專案的 MainPage.cs 中，為事件處理常式中參考的 MainPage `UpdateToDoItem()` 方法加入定義，如下所示。

        private async Task UpdateToDoItem(TodoItem item)
        {
            Exception exception = null;			
            try
            {
                //update at the remote table
                await todoTable.UpdateAsync(item);
            }
            catch (Exception ex)
            {
                exception = ex;
            }			
            if (exception != null)
            {
                await new MessageDialog(exception.Message, "Update Failed").ShowAsync();
            }
        }

現在，當 TextBox 失去焦點時，應用程式會將每個項目的文字變更重新寫入至資料庫。

##在應用程式中啟用衝突偵測

在部分案例中，兩個或多個用戶端可能會同時對相同項目寫入變更。在沒有偵測到任何衝突的情況下，最後寫入將覆寫任何先前的更新，即使這不是您想要的結果。[開放式並行存取控制項]會假設每筆交易都可以認可，因此不會使用任何資源鎖定。在認可交易之前，開放式並行存取控制項會驗證沒有其他交易已修改此資料。如果資料已修改，則會復原認可的交易。Azure 行動服務支援開放式並行存取控制項，方法是使用新增至每個資料表的 `__version` 系統屬性資料欄來追蹤對每個項目的變更。在本節中，我們將使應用程式能夠透過 `__version` 系統屬性偵測這些寫入衝突。在嘗試更新期間，如果記錄自前次查詢後有所變更，系統將會以 `MobileServicePreconditionFailedException` 通知應用程式。此時，應用程式將可選擇是要認可它對資料庫的變更，還是保留資料庫的最後變更。如需行動服務之系統屬性的詳細資訊，請參閱[系統屬性]。

1. 開啟共用專案中的 TodoItem.cs，以下列程式碼更新 `TodoItem` 類別定義，以加入可用來支援寫入衝突偵測的 `__version` 系統屬性。

		public class TodoItem
		{
			public string Id { get; set; }			
			[JsonProperty(PropertyName = "text")]
			public string Text { get; set; }			
			[JsonProperty(PropertyName = "complete")]
			public bool Complete { get; set; }			
			[JsonProperty(PropertyName = "__version")]
			public string Version { set; get; }
		}

	> [AZURE.NOTE]使用不具型別的資料表時，開放式並行存取的啟用方式，是在資料表的 SystemProperties 中新增 Version 旗標。
	>
	>````` 
	//Enable optimistic concurrency by retrieving __version
todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
`````


2. 將 `Version` 屬性新增至 `TodoItem` 類別後，如果在更新期間發現自前次查詢後有所變更的記錄，系統將會以 `MobileServicePreconditionFailedException` 例外狀況通知應用程式。此例外狀況會包含來自伺服器的最新版項目。在共用專案的 MainPage.cs 中新增下列程式碼，以處理 `UpdateToDoItem()` 方法中的例外狀況。

        private async Task UpdateToDoItem(TodoItem item)
        {
            Exception exception = null;			
            try
            {
                //update at the remote table
                await todoTable.UpdateAsync(item);
            }
            catch (MobileServicePreconditionFailedException<TodoItem> writeException)
            {
                exception = writeException;
            }
            catch (Exception ex)
            {
                exception = ex;
            }			
            if (exception != null)
            {
                if (exception is MobileServicePreconditionFailedException)
                {
                    //Conflict detected, the item has changed since the last query
                    //Resolve the conflict between the local and server item
                    await ResolveConflict(item, ((MobileServicePreconditionFailedException<TodoItem>) exception).Item);
                }
                else
                {
                    await new MessageDialog(exception.Message, "Update Failed").ShowAsync();
                }
            }
        }


3. 在 MainPage.cs 中，為 `UpdateToDoItem()` 中參考的 `ResolveConflict()` 方法新增定義。請注意，若要解決衝突，您必須先將本機項目的版本設為來自伺服器的更新版本，再認可使用者的決策。否則將會持續發生衝突。


        private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
        {
            //Ask user to choose the resolution between versions
            MessageDialog msgDialog = new MessageDialog(String.Format("Server Text: "{0}" \nLocal Text: "{1}"\n", 
                                                        serverItem.Text, localItem.Text), 
                                                        "CONFLICT DETECTED - Select a resolution:");
            UICommand localBtn = new UICommand("Commit Local Text");
            UICommand ServerBtn = new UICommand("Leave Server Text");
            msgDialog.Commands.Add(localBtn);
            msgDialog.Commands.Add(ServerBtn);			
            localBtn.Invoked = async (IUICommand command) =>
            {
                // To resolve the conflict, update the version of the 
                // item being committed. Otherwise, you will keep
                // catching a MobileServicePreConditionFailedException.
                localItem.Version = serverItem.Version;				
                // Updating recursively here just in case another 
                // change happened while the user was making a decision
                await UpdateToDoItem(localItem);
            };			
            ServerBtn.Invoked = async (IUICommand command) =>
            {
				RefreshTodoItems();
            };			
            await msgDialog.ShowAsync();
        }



##在應用程式中測試資料庫寫入衝突

在本節中您將會建置 Windows 市集應用程式封裝，以將應用程式安裝至第二部機器或虛擬機器。接著，您將同時在兩部機器上執行應用程式而產生寫入衝突，以測試程式碼。兩個應用程式執行個體將會嘗試更新相同項目的 `text` 屬性，而讓使用者必須解決衝突。


1. 建立 Windows 市集應用程式封裝，以安裝在第二部機器或虛擬機器上。若要執行此動作，請在 Visual Studio 中依序按一下 [專案] -> [市集] -> [建立應用程式套件]。

	![][0]

2. 在 [建立您的套件] 畫面上按一下 [否]，因為此封裝將不會上傳至 Windows 市集。然後按 [下一步]。

	![][1]

3. 在 [選取和設定套件] 畫面上接受預設值，然後按一下 [建立]。

	![][10]

4. 在 [套件建立完成] 畫面上按一下 [輸出位置] 連結，以開啟封裝位置。

   	![][11]

5. 將封裝資料夾 "todolist_1.0.0.0_AnyCPU_Debug_Test" 複製到第二部機器。在該機器上開啟封裝資料夾，以滑鼠右鍵按一下 **Add-AppDevPackage.ps1** PowerShell 指令碼，然後按一下 [用 PowerShell 執行]，如下所示。依照提示安裝應用程式。

	![][12]
  
5. 依序按一下 [偵錯] -> [開始偵錯]，在 Visual Studio 中執行應用程式的執行個體 1。在第二部機器的 [開始] 畫面上按一下向下箭頭，以依名稱檢視應用程式。接著，按一下 **todolist** 應用程式，以執行應用程式的執行個體 2。

	應用程式執行個體 1 ![][2]

	應用程式執行個體 2 ![][2]


6. 在應用程式的執行個體 1 中，將最後一個項目的文字更新為 **Test Write 1**，然後按一下另一個文字方塊，使 `LostFocus` 事件處理常式更新資料庫。其範例如下列螢幕擷取畫面所示。
	
	應用程式執行個體 1 ![][3]

	應用程式執行個體 2 ![][2]

7. 此時，應用程式執行個體 2 中的對應項目具有舊版的項目。在此應用程式執行個體中，針對 `text` 屬性寫入 **Test Write 2**。然後按一下另一個文字方塊，使 `LostFocus` 事件處理常式嘗試以舊的 `_version` 屬性更新資料庫。

	應用程式執行個體 1 ![][4]

	應用程式執行個體 2 ![][5]

8. 因為更新嘗試所用的 `__version` 值與伺服器的 `__version` 值不符，行動服務 SDK 會擲回 `MobileServicePreconditionFailedException`，讓應用程式解決此衝突。若要解決衝突，您可以按一下 [Commit Local Text]，以認可執行個體 2 中的值。或者，您可以按一下 [Leave Server Text] 捨棄執行個體 2 中的值，而保留已認可的應用程式執行個體 1 中的值。

	應用程式執行個體 1 ![][4]

	應用程式執行個體 2 ![][6]



##以伺服器指令碼自動處理衝突的解決方式

您可以使用伺服器指令碼來偵測及解決寫入衝突。當您使用指令碼邏輯 (而非使用者互動) 來解決衝突時，這將是理想的作法。在本節中，您會在應用程式的 TodoItem 資料表中新增伺服器端指令碼。此指令碼將使用下列邏輯來解決衝突：

+  如果 TodoItem 的 ` complete` 欄位設為 true，則會被視為已完成，且 `text` 無法再變更。
+  如果 TodoItem 的 ` complete` 欄位仍為 false，則更新 `text` 的嘗試將會被認可。

下列步驟將引導您新增伺服器更新指令碼並加以測試。

1. 登入 [Azure 管理入口網站]，按一下 [行動服務]，然後按一下您的應用程式。 

   	![][7]

2. 按一下 [資料] 索引標籤，然後按一下 [TodoItem] 資料表。

   	![][8]

3. 按一下 [指令碼]，然後選取 [更新] 作業。

   	![][9]

4. 以下列函數取代現有的指令碼，然後按一下 [儲存]。

		function update(item, user, request) { 
			request.execute({ 
				conflict: function (serverRecord) {
					// Only committing changes if the item is not completed.
					if (serverRecord.complete === false) {
						//write the updated item to the table
						request.execute();
					}
					else
					{
						request.respond(statusCodes.FORBIDDEN, 'The item is already completed.');
					}
				}
			}); 
		}   
5. 在兩部機器上執行 **todolist** 應用程式。變更執行個體 2 中最後一個項目的 TodoItem `text`。接著，按一下另一個文字方塊，使 `LostFocus` 事件處理常式更新資料庫。

	應用程式執行個體 1 ![][4]

	應用程式執行個體 2 ![][5]

6. 在應用程式的執行個體 1 中，為最後一個文字屬性輸入不同的值。然後按一下另一個文字方塊，使 `LostFocus` 事件處理常式嘗試以不正確的 `__version` 屬性更新資料庫。

	應用程式執行個體 1 ![][13]

	應用程式執行個體 2 ![][14]

7. 請留意到，由於伺服器指令碼已解決衝突，而使更新得以進行 (因為項目未標示為完成)，因此應用程式中未發生例外狀況。若要確認更新已順利完成，請在執行個體 2 中按一下 [重新整理]，以重新查詢資料庫。

	應用程式執行個體 1 ![][15]

	應用程式執行個體 2 ![][15]

8. 在執行個體 1 中，按一下可完成最後一個待辦事項的核取方塊。

	應用程式執行個體 1 ![][16]

	應用程式執行個體 2 ![][15]

9. 在執行個體 2 中，嘗試更新最後一個 TodoItem 的文字，並觸發 `LostFocus` 事件。指令碼對衝突的因應方式是拒絕更新以解決衝突，因為項目已完成。

	應用程式執行個體 1 ![][17]

	應用程式執行個體 2 ![][18]

##後續步驟

本教學課程示範了如何讓 Windows 市集應用程式處理在行動服務中使用資料時所發生的寫入衝突。接著，請考慮完成下列其中一個 Windows 市集教學課程：

* [將驗證新增至您的應用程式] <br/>了解如何驗證應用程式的使用者。

* [將推播通知新增至您的應用程式] <br/>了解如何使用行動服務將非常基本的推播通知傳送到應用程式。
 


<!-- Images. -->
[0]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package1.png
[1]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package2.png
[2]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1.png
[3]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1-write1.png
[4]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1-write2.png
[5]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app2-write2.png
[6]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app2-write2-conflict.png
[7]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-services-selection.png
[8]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-portal-data-tables.png
[9]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-insert-script-users.png
[10]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package3.png
[11]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package4.png
[12]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-install-app-package.png
[13]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1-write3.png
[14]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app2-write3.png
[15]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-write3.png
[16]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-checkbox.png
[17]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-2-items.png
[18]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-already-complete.png
[19]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-manage-nuget-packages-VS.png
[20]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-manage-nuget-packages-dialog.png

<!-- URLs. -->
[開放式並行存取控制項]: http://go.microsoft.com/fwlink/?LinkId=330935
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Azure 帳戶]: http://www.windowsazure.com/pricing/free-trial/
[Validate and modify data with scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Refine queries with paging]: /develop/mobile/tutorials/add-paging-to-data-dotnet
[開始使用行動服務]: /develop/mobile/tutorials/get-started
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-dotnet
[將驗證新增至您的應用程式]: /develop/mobile/tutorials/get-started-with-users-dotnet
[將推播通知新增至您的應用程式]: /develop/mobile/tutorials/get-started-with-push-dotnet

[Azure 管理入口網站]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268374
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268375
[Developer Code Samples site]: http://go.microsoft.com/fwlink/p/?LinkId=271146
[系統屬性]: http://go.microsoft.com/fwlink/?LinkId=331143
 

<!---HONumber=July15_HO1-->