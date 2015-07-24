##<a name="add-select-images"></a>更新快速入門用戶端應用程式以擷取和上傳影像

1. 在 Visual Studio 中，開啟 Package.appxmanifest 檔案，並在 [功能] 索引標籤中，啟用 [網路攝影機] 和 [麥克風] 功能。

   	![](./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-app-manifest-camera.png)
 
   	如此能確保您的應用程式可以使用連接至電腦的相機。Users will be requested to allow camera access the first time that the app is run.

1. 開啟 MainPage.xaml 檔案，然後以下列程式碼取代第一個 **Task** 元素後面所緊接的 **StackPanel** 元素：

        <StackPanel Orientation="Horizontal" Margin="72,0,0,0">
            <TextBox Name="TextInput" Margin="5" MaxHeight="40" MinWidth="300"></TextBox>
            <Button Name="btnTakePhoto" Style="{StaticResource PhotoAppBarButtonStyle}"
                    Click="OnTakePhotoClick" />
            <Button Name="ButtonSave" Style="{StaticResource UploadAppBarButtonStyle}" 
                    Click="ButtonSave_Click"/>
        </StackPanel>

2. 以下列程式碼取代 **DataTemplate** 中的 **StackPanel** 元素：

        <StackPanel Orientation="Vertical">
            <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" 
                        Checked="CheckBoxComplete_Checked" Content="{Binding Text}" 
                        Margin="10,5" VerticalAlignment="Center"/>
            <Image Name="ImageUpload" Source="{Binding ImageUri, Mode=OneWay}"
                    MaxHeight="250"/>
        </StackPanel> 

   	這樣可以在 **ItemTemplate** 中新增一個映像，並將其繫結來源設為 Blob 儲存體服務中已上傳映象的 URI。

3. 開啟 MainPage.xaml.cs 專案檔案並新增下列 **using** 陳述式：
	
		using Windows.Media.Capture;
		using Windows.Storage;
		using Windows.UI.Popups;
		using Microsoft.WindowsAzure.Storage.Auth;
		using Microsoft.WindowsAzure.Storage.Blob;
    
4. 在 TodoItem 類別中，新增下列屬性：

        [JsonProperty(PropertyName = "containerName")]
        public string ContainerName { get; set; }
		
        [JsonProperty(PropertyName = "resourceName")]
        public string ResourceName { get; set; }
		
        [JsonProperty(PropertyName = "sasQueryString")]
        public string SasQueryString { get; set; }
		
        [JsonProperty(PropertyName = "imageUri")]
        public string ImageUri { get; set; } 

   	>[AZURE.NOTE]如果要在 TodoItem 物件中新增屬性，您必須在行動服務中啟用動態結構描述。When Dynamic Schema is enabled, new columns are automatically added to the TodoItem table that map to these new properties.

5. 在 MainPage 類別中，新增下列程式碼：

        // Use a StorageFile to hold the captured image for upload.
        StorageFile media = null;
		
		private async void OnTakePhotoClick(object sender, RoutedEventArgs e)
		{
			// Capture a new photo or video from the device.
			CameraCaptureUI cameraCapture = new CameraCaptureUI();
			media = await cameraCapture
				.CaptureFileAsync(CameraCaptureUIMode.PhotoOrVideo);
        }

  	此程式碼顯示相機 UI 來捕捉影像，並將影像儲存至儲存檔案。

6. 以下列程式碼取代現有 `InsertTodoItem` 方法：
 
        private async void InsertTodoItem(TodoItem todoItem)
        {
            string errorString = string.Empty;
			
            if (media != null)
            {
                // Set blob properties of TodoItem.
                todoItem.ContainerName = "todoitemimages";
                todoItem.ResourceName = media.Name;
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

                // Get the new image as a stream.
                using (var fileStream = await media.OpenStreamForReadAsync())
                {                   					
                    // Upload the new image as a BLOB from the stream.
                    CloudBlockBlob blobFromSASCredential =
                        container.GetBlockBlobReference(todoItem.ResourceName);
                    await blobFromSASCredential.UploadFromStreamAsync(fileStream.AsInputStream());
                }
				
				// When you request an SAS at the container-level instead of the blob-level,
				// you are able to upload multiple streams using the same container credentials.
            }
			
            // Add the new item to the collection.
            items.Add(todoItem);
        }

	此程式碼會將要求傳送給行動服務以插入新 TodoItem，包括影像檔案名稱。回應包含 SAS，接著可在 Blob 存放區中插入影像，及影像的 URI 以進行資料繫結。

最後一個步驟是測試應用程式並驗證已成功上傳。
		
##<a name="test"></a>在應用程式中測試上傳影像

1. 在 Visual Studio 中，按 F5 鍵以執行此應用程式。

2. 在 [Insert a TodoItem] 下方的文字方塊中輸入文字，然後按一下 [相片]。

   	![](./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-quickstart-blob-appbar.png)

  	這會顯示相機擷取 UI。

3. 按一下影像以拍照，然後按 [確定]。
  
   	![](./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-quickstart-blob-camera.png)

4. 按一下 [上傳] 插入新項目並上傳影像。

	![](./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-quickstart-blob-appbar2.png)

5. 新項目以及上傳的影像會顯示在清單檢視中。

	![](./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-quickstart-blob-ie.png)

   	>[AZURE.NOTE]將新項目的 <code>imageUri</code> 屬性繫結至 <strong>Image</strong> 控制項時，會從 Blob 儲存體服務自動下載影像。

<!---HONumber=July15_HO2-->