
##<a name="add-select-images"></a>更新快速入門用戶端應用程式以擷取和上傳影像

在本節中，您會從[開始使用行動服務] (英文) 教學課程中更新專案，以便拍照並將相片上傳至 Azure Blob 儲存體。為了擷取影像，此教學課程從 `Microsoft.Phone.Tasks` 命名空間使用 [CameraCaptureTask]。此類別會啟動 Windows Phone 裝置的相機 UI 以擷取相片，並將影像自動儲存到 Windows Phone 裝置上的相機相簿。如果不希望將影像儲存至相機相簿，請改用 `Microsoft.Devices` 命名空間的 [PhotoCamera] 類別。

1. 在 Visual Studio 的 [方案總管] 中，展開專案下方的 [屬性]。接下來開啟 WMAppManifest.xml 檔案，然後在 [功能] 索引標籤上按一下 [ID_CAP_ISV_CAMERA] 以啟用相機。關閉檔案以儲存您的變更。

   	![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-WMAppmanifest-wp8.png)

   	如此能確保您的應用程式可以使用連接至電腦的相機。第一次執行該應用程式時，系統會要求使用者允許u30456 相機進行存取。

2. 開啟 MainPage.xaml 檔案，然後以下列程式碼取代名為 **ContentPanel** 的 **Grid** 元素：

        <!--ContentPanel - place additional content here-->
        <Grid x:Name="ContentPanel" Grid.Row="1" Margin="12,0,12,0">
            <Grid.RowDefinitions>
                <RowDefinition Height="Auto" />
                <RowDefinition Height="Auto" />
                <RowDefinition Height="Auto" />
                <RowDefinition Height="Auto" />
                <RowDefinition Height="Auto" />
                <RowDefinition Height="*" />
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="2*" />
                <ColumnDefinition Width="2*" />
            </Grid.ColumnDefinitions>
            <TextBlock Grid.Row="0" Grid.ColumnSpan="2" Text="Enter some text below, click Capture Image to add a captured image. Then click Save to insert a new TodoItem item into your database" TextWrapping="Wrap" Margin="12"/>
            <TextBox Grid.Row="1" Grid.ColumnSpan="2" Name="TextInput" Text="" />
            <Button Name="ButtonCaptureImage" Grid.Row="2" Click="ButtonCaptureImage_Click">Capture Image</Button>
            <Button Grid.Row ="2" Grid.Column="1" Name="ButtonSave" Click="ButtonSave_Click">Save</Button>
            <TextBlock Grid.Row="3" Grid.ColumnSpan="2" Text="Click refresh below to load the unfinished TodoItems from your database. Use the checkbox to complete and update your TodoItems" TextWrapping="Wrap" Margin="12" />
            <Button Grid.Row="4" Grid.ColumnSpan="2" Name="ButtonRefresh" Click="ButtonRefresh_Click">Refresh</Button>
            <phone:LongListSelector Grid.Row="5" Grid.ColumnSpan="2" Name="ListItems">
                <phone:LongListSelector.ItemTemplate>
                    <DataTemplate>
                        <StackPanel Orientation="Vertical">
                            <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" Checked="CheckBoxComplete_Checked" Content="{Binding Text}" Margin="10,5" VerticalAlignment="Center"/>
                            <Image Name="ImageUpload" Source="{Binding ImageUri, Mode=OneWay}" MaxHeight="150"/>
                        </StackPanel>
                    </DataTemplate>
                </phone:LongListSelector.ItemTemplate>
            </phone:LongListSelector>
        </Grid>


   	這樣可以新增一個按鈕來啟動 [CameraCaptureTask]，並在 **ItemTemplate** 中新增一個影像，將其繫結來源設為 Blob 儲存體服務中已上傳影像的 URI。

3. 開啟 MainPage.xaml.cs 專案檔案並新增下列 **using** 陳述式：
	
		using Microsoft.Phone.Tasks;
		using System.IO;
		using Microsoft.WindowsAzure.Storage.Auth;
		using Microsoft.WindowsAzure.Storage.Blob;
    
4. 在 MainPage.xaml.cs 專案檔案中，新增下列屬性以更新 TodoItem 類別：

        [JsonProperty(PropertyName = "containerName")]
        public string ContainerName { get; set; }
		
        [JsonProperty(PropertyName = "resourceName")]
        public string ResourceName { get; set; }
		
        [JsonProperty(PropertyName = "sasQueryString")]
        public string SasQueryString { get; set; }
		
        [JsonProperty(PropertyName = "imageUri")]
        public string ImageUri { get; set; } 

5. 在 MainPage.xaml.cs 專案檔案中更新 MainPage 類別。新增下列程式碼以宣告 [CameraCaptureTask] ，以及將參照擷取之影像的串流物件：

        // Using the CameraCaptureTask to allow the user to capture a todo item image //
        CameraCaptureTask cameraCaptureTask;
		
        // Using a stream reference to upload the image to blob storage.
        Stream imageStream = null;

6. 在 MainPage.xaml.cs 專案檔案中更新 MainPage 類別。新增下列程式碼，更新建構函式來建立 CameraCaptureTask，並新增 Completed 事件的事件處理常式：

        // Constructor
        public MainPage()
        {
            InitializeComponent();
			
            cameraCaptureTask = new CameraCaptureTask();
            cameraCaptureTask.Completed += cameraCaptureTask_Completed;
        }
		
        void cameraCaptureTask_Completed(object sender, PhotoResult e)
        {
            imageStream = e.ChosenPhoto;
        }

7. 在 MainPage.xaml.cs 專案檔案中更新 MainPage 類別。新增下列程式碼來顯示相機 UI，讓使用者按一下 [擷取影像] 按鈕來擷取影像。

        private void ButtonCaptureImage_Click(object sender, RoutedEventArgs e)
        {
            cameraCaptureTask.Show();
        }


8. 在 MainPage.xaml.cs 專案檔案中更新 MainPage 類別。以下列程式碼取代現有 `InsertTodoItem` 方法：
 
        private async void InsertTodoItem(TodoItem todoItem)
        {
            string errorString = string.Empty;            
			
            if (imageStream != null)
            {
                // Set blob properties of TodoItem.
                todoItem.ContainerName = "todoitemimages";
                todoItem.ResourceName = Guid.NewGuid().ToString() + ".jpg";
            }                       
			
            // Send the item to be inserted. When blob properties are set this
            // generates an SAS in the response.
            await todoTable.InsertAsync(todoItem);  
			
            // If we have a returned SAS, then upload the blob.
            if (!string.IsNullOrEmpty(todoItem.SasQueryString))
            {
                // Get the URI generated that contains the SAS 
                // and extract the storage credentials.
                StorageCredentials cred = new StorageCredentials(todoItem.SasQueryString);
                var imageUri = new Uri(todoItem.ImageUri);
				
                // Instantiate a Blob store container based on the info in the returned item.
                CloudBlobContainer container = new CloudBlobContainer(
                    new Uri(string.Format("https://{0}/{1}",
                        imageUri.Host, todoItem.ContainerName)), cred);                
				
                // Upload the new image as a BLOB from the stream.
                CloudBlockBlob blobFromSASCredential =
                    container.GetBlockBlobReference(todoItem.ResourceName);
                await blobFromSASCredential.UploadFromStreamAsync(imageStream);
				
				// When you request an SAS at the container-level instead of the blob-level,
				// you are able to upload multiple streams using the same container credentials.

                imageStream = null;
            }              
			
            // Add the new item to the collection.
            items.Add(todoItem);
            TextInput.Text = "";
        }


	此程式碼會將要求傳送給行動服務以插入新 TodoItem，包括影像檔案名稱。回應包含 SAS，接著可在 Blob 存放區中插入影像，及影像的 URI 以進行資料繫結。

最後一個步驟是測試應用程式並驗證已成功上傳。
		
##<a name="test"></a>在應用程式中測試上傳影像

1. 您可以在 Visual Studio 中按 F5 鍵，在模擬器或選定的實際裝置中測試應用程式。

2. 在文字方塊中輸入部分文字，然後按一下 [擷取影像]。

   	![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-view-wp8.png)

  	這會顯示相機擷取 UI。

3. 按一下相機上的影像或快照按鈕來拍照。
  
   	![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-view-camera-wp8.png)

4. 按一下 [接受] 以接受影像並結束相機 UI。

    ![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-view-camera-accept-wp8.png)

5. 按一下 [儲存] 插入新項目並上傳影像。

	![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-view-save-wp8.png)

6. 新項目以及上傳的影像會顯示在清單檢視中。

	![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-view-final-wp8.png)

   >[AZURE.NOTE]將新項目的 <code>imageUri</code> 屬性繫結至 <strong>Image</strong> 控制項時，會從 Blob 儲存體服務自動下載影像。


[開始使用行動服務]: ../articles/mobile-services-windows-phone-get-started.md
[CameraCaptureTask]: http://msdn.microsoft.com/library/windowsphone/develop/microsoft.phone.tasks.cameracapturetask(v=vs.105).aspx
[PhotoCamera]: http://msdn.microsoft.com/library/windowsphone/develop/microsoft.devices.photocamera(v=vs.105).aspx

<!---HONumber=July15_HO2-->