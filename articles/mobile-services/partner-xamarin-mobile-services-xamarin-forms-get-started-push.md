 <properties
	pageTitle="將推播通知新增至行動服務應用程式 (Xamarin.Forms) - 行動服務"
	description="了解如何在 Xamarin.Forms 應用程式中以 Azure 行動服務使用推播通知。"
	documentationCenter="xamarin"
	authors="normesta"
	manager="stevenpo"
	services="mobile-services"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.tgt_pltfrm="mobile-xamarin"
	ms.workload="mobile"
	ms.date="06/10/2015"
	ms.author="normesta"/>

# 將推播通知新增至 Xamarin.Forms 應用程式
[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

##概觀

本主題說明如何使用 Azure 行動服務，將推播通知傳送至 Xamarin.Forms 方案的 iOS、Android 和 Windows Phone 應用程式。由建立行動服務開始。然後，您將下載入門範例、利用適當的推播通知服務註冊，以及將程式碼加入至要從這些服務接收通知的方案。

完成本教學課程時，您的行動服務會在每次使用者於其中一個應用程式中新增工作時傳送推播通知。您可以在此找到完整的範例：[完整的 Xamarin.Forms Azure 推播通知範例]。

本教學課程需要下列各項：

+ iOS 8 裝置 (您無法在 iOS 模擬器中測試推播通知)
+ iOS Developer Program 成員資格
+ [Xamarin.iOS Studio]
+ [Azure 行動服務元件]
+ 有效的 Google 帳戶
+ [Google Cloud Messaging 用戶端元件]。您在教學課程中會新增此元件。

本主題內容：

1. [建立新的行動服務](#create-service)
2. [下載並設定入門範例](#download-starter-sample)
4. [將推播通知新增至 Xamarin.Forms.iOS 應用程式](#iOS)
5. [將推播通知新增至 Xamarin.Forms.Android 應用程式](#Android)
6. [將推播通知新增至 Xamarin.Forms.Windows 應用程式](#Windows)
7. [更新 Azure 資料表插入指令碼，以傳送推播通知給所有應用程式](#all-apps)

## <a name="create-service"></a>建立新的行動服務

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

若要能夠在新行動服務中儲存應用程式資料，您必須先建立新的資料表。

1. 在管理入口網站中，按一下 **[行動服務]**，然後按一下您剛剛建立的行動服務。

2. 按一下 **[資料]** 索引標籤，然後按一下 **[建立]**。

    ![][123]

   	這樣做會顯示 [建立新資料表] 對話方塊。

3. 在 **[資料表名稱]** 中輸入 _TodoItem_，然後按一下核取按鈕。

    ![][124]

  	這會建立含預設權限集的新儲存資料表 **TodoItem**，這表示應用程式的任何使用者都可以存取與變更該資料表中的資料。

    > [AZURE.NOTE]行動服務快速入門中使用相同的資料表名稱。However, each table is created in a schema that is specific to a given mobile service.目的是為了防止多個行動服務使用相同資料庫時產生資料衝突。

4. 按一下新的 **TodoItem** 資料表，並驗證其中不含資料列。

5. 按一下 [資料行] 索引標籤，確認只有一個自動為您建立的 [id] 資料行。

  	此為行動服務資料表的最低需求。

    > [AZURE.NOTE]若您的行動服務啟用動態結構描述，當行動服務透過插入或更新操作收到 JSON 物件時，便會自動建立新資料行。

現在您已準備好將新的行動服務作為應用程式的資料儲存區使用。

## <a name="download-starter-sample"></a>下載並設定入門範例
我們會將推播通知新增至現有範例。
  
1. 下載下列範例：[Xamarin.Forms Azure 推播通知入門範例]。

2. 在管理入口網站中按一下 [行動服務]，然後按一下行動服務。按一下 [儀表板] 索引標籤並記下 [網站 URL]。然後按一下 [管理金鑰]，然後記下 [應用程式金鑰]。當您從應用程式程式碼存取行動服務時，您將會用到這些值。

3. 在方案的 **ToDoAzure(Portable)** 專案中，開啟 **Constants.cs** 檔案中，以您在上一個步驟中取得的網站 URL 和應用程式金鑰取代 `ApplicationURL` 和 `ApplicationKey`。

## <a name="iOS"></a>將推播通知新增至 Xamarin.Forms.iOS 應用程式

您將使用 Apple Push Notification 服務 (APNS)，將推播通知加入至 iOS 應用程式。您需要作用中的 Google 帳戶和 [Google Cloud Messaging 用戶端元件]。

>[AZURE.IMPORTANT]基於 Apple Push Notification 服務 (APNS) 需求，您必須在 iOS 功能裝置 (iPhone 或 iPad) 而非在模擬器上部署和測試推播通知。

APNS 使用憑證來驗證您的行動服務。遵循這些指示建立必要的憑證，並將憑證上傳至您的行動服務。如需正式的 APNS 功能文件，請參閱 [Apple 推播通知服務] (英文)。

### <a name="certificates"></a>產生憑證簽署要求檔案

首先，您必須產生憑證簽署要求 (CSR) 檔案，這將由 Apple 用來產生簽署的憑證。

1. 在公用程式中，執行 Keychain Access 工具。

2. 按一下 [Keychain Access]，並展開 [Certificate Assistant]，然後按一下 [Request a Certificate from a Certificate Authority...]。

    ![][5]

3. 輸入您的 [使用者電子郵件地址]，輸入 [一般名稱] 值，並確定已選取 [儲存至磁碟]，然後按一下 [繼續]。

    ![][6]

4. 在 [另存新檔] 中輸入憑證簽署要求 (CSR) 檔案的名稱，並且在 [位置] 中選取位置，然後按一下 [儲存]。

    ![][7]

    請記住您所選擇的位置。

接下來，您要向 Apple 註冊應用程式、啟用推播通知，以及上傳這個匯出的 CSR 以建立推播憑證。

### <a name="register"></a>針對推播通知註冊應用程式

若要從行動服務將推播通知傳送至 iOS 應用程式，您必須向 Apple 註冊您的應用程式，並註冊推播通知。

1. 如果您尚未註冊應用程式，請瀏覽至 Apple 開發人員中心的 <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS 佈建入口網站</a>，然後使用您的 Apple ID 登入，並依序按一下 [識別碼]、[App ID] 及 **+** 號以建立應用程式的應用程式識別碼。

    ![][102]

2. 在 [描述] 中輸入您的應用程式名稱，輸入並記住唯一的 [套件組合識別碼]，勾選 [應用程式服務] 區段中的 [推播通知] 選項，然後按一下 [繼續]。此範例會使用識別碼 **MobileServices.Quickstart**，但您可能不會重複使用此相同識別碼，因為應用程式識別碼在所有使用者中必須是唯一的。因此，建議您在應用程式名稱之後附加您的全名或縮寫。

    ![][103]

    這將產生您的應用程式識別碼，並要求您 [提交] 這個資訊。按一下 [提交]。

    ![][104]

    按一下 [提交] 之後，您將看見 [註冊完成] 畫面，如下所示。按一下 [完成]。

    ![][105]

3. 找出剛才建立的應用程式識別碼，並且按一下該資料列。

    ![][106]

    按一下應用程式識別碼將顯示應用程式和應用程式識別碼的詳細資料。按一下 [設定] 按鈕。

    ![][107]

4. 捲動到畫面底部，然後按一下 [Development Push SSL Certificate] 區段下方的 [Create Certificate...] 按鈕。

    ![][108]

    這將顯示 [Add iOS Certificate] 助理。

    注意：本教學課程使用開發憑證。註冊生產憑證時，將使用同一個程序。您將憑證上傳至行動服務時，請確定設定相同的憑證類型。

5. 按一下 [選擇檔案]，瀏覽至您稍早儲存 CSR 檔案的位置，然後按一下 [產生]。

    ![][110]

6. 在入口網站建立憑證之後，依序按一下 [下載] 按鈕和 [完成]。

    ![][111]

    這會下載簽署憑證，並將它儲存到您電腦中的 [下載] 資料夾。

    ![][9]

    注意：根據預設，下載的檔案 (開發憑證) 的名稱會是 <strong>aps_development.cer</strong>。

7. 按兩下下載的推播憑證 **aps_development.cer**。

    這樣會將新的憑證安裝在金鑰鏈中，如下所示：

    ![][10]

    注意：憑證中的名稱可能會不同，不過字首會加上 <strong>Apple Development iOS Push Notification Services:</strong> 前置詞。

之後，您會使用該憑證來產生 .p12 檔案，並將該檔案上傳到行動服務以啟用 APNS 驗證。

### <a name="profile"></a>建立應用程式的佈建設定檔

1. 返回 <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS 佈建入口網站</a>，並依序選取 [**佈建設定檔**] 和 [**全部**]，然後按一下 [**+**] 按鈕建立新的設定檔。如此會啟動 **Add iOS Provisiong Profile** 精靈。

    ![][112]

2. 將 [Development] 下方的 [iOS App Development] 選為佈建設定檔類型，然後按一下 [Continue]。

3. 接著，從 [App ID] 下拉式清單中選取行動服務快速入門應用程式的應用程式識別碼，並按一下 [Continue]。

    ![][113]

4. 在 [Select certificates] 畫面中選取先前建立的憑證，然後按一下 [Continue]。

    ![][114]

5. 然後，在 [Devices] 選取要用來測試的裝置，然後按一下 [Continue]。

    ![][115]

6. 最後，在 [Profile Name] 中為設定檔挑選名稱，然後依序按一下 [Generate] 和 [Done]

    ![][116]

    這將建立新的佈建設定檔。

    ![][117]

7. 在 Xcode 中開啟 Organizer 並選取 Devices 檢視，在左窗格的 [程式庫] 區段中選取 [Provisioning Profiles]，然後按一下中間窗格底部的 [重新整理] 按鈕。

### <a name="configure-mobileServices"></a>設定行動服務以傳送推播要求

在向 APNS 註冊應用程式及設定專案後，接下來您必須設定行動服務以整合 APNS。

1. 在 Keychain Access 中，以滑鼠右鍵按一下新的憑證、按一下 [匯出]、為您的檔案命名、選取 [.p12] 格式，然後按一下 [儲存]。

    ![][28]

    記下匯出憑證的檔案名稱和位置。

2. 登入 [Azure 管理入口網站]，按一下 [行動服務]，然後按一下您的應用程式。

    ![][18]

3. 按一下 [推播] 索引標籤，然後按一下 [Apple 推播通知設定] 下的 [上傳]。

    ![][19]

    This displays the Upload Certificate dialog.

4. 按一下 [檔案]、選取匯出憑證 .p12 檔案、輸入 [密碼]、確定已選取正確的 [模式]、按一下核取圖示，然後按一下 [儲存]。

    ![][20]

您的行動服務現已設定為與 APNS 搭配運作。

### <a name="configure-app"></a>設定您的 Xamarin.iOS 應用程式

1. 在 Xamarin.Studio 或 Visual Studio 中，開啟 **Info.plist**，然後使用您稍早建立的識別碼更新 [**套件組合識別碼**]。

    ![][121]

2. 向下捲動到 [背景模式]，並勾選 [啟用背景模式] 方塊和 [遠端通知] 方塊。

    ![][122]

3. 按兩下 [方案面板] 中的專案，以開啟 [專案選項]。

4.  選擇 [建置] 下的 [iOS 套件組合簽署]，然後選取對應的 [身分識別] 以及您為此專案設定的 [佈建設定檔]。

    ![][120]

    這將確保 Xamarin 專案使用新的設定檔進行程式碼簽署。如需官方 Xamarin 裝置佈建文件，請參閱 [Xamarin 裝置佈建]。

### <a name="add-push"></a>將推播通知新增至應用程式

1. 在 Xamarin.Studio 或 Visual Studio 中，展開 **ToDoAzure.iOS** 專案，開啟 **AppDelegate** 類別，然後以下列程式碼取代 **FinishedLaunching** 事件：

        public override bool FinishedLaunching(UIApplication app, NSDictionary options)
        {
             // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert
                | UIUserNotificationType.Badge
                | UIUserNotificationType.Sound,
                new NSSet());

            global::Xamarin.Forms.Forms.Init();
            instance = this;
            CurrentPlatform.Init();
            
            todoItemManager = new ToDoItemManager();
            App.SetTodoItemManager(todoItemManager);


            UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications();
            
            LoadApplication(new App());
            return base.FinishedLaunching(app, options);
        }

6. 在 **AppDelegate** 中，覆寫 **RegisteredForRemoteNotifications** 事件：

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            // Modify device token
            string _deviceToken = deviceToken.Description;
            _deviceToken = _deviceToken.Trim('<', '>').Replace(" ", "");

            // Get Mobile Services client
            MobileServiceClient client = todoItemManager.GetClient;

            // Register for push with Mobile Services
            IEnumerable<string> tag = new List<string>() { "uniqueTag" };
            
            const string template = "{"aps":{"alert":"$(message)"}}";

            var expiryDate = DateTime.Now.AddDays(90).ToString
                (System.Globalization.CultureInfo.CreateSpecificCulture("en-US"));

            var push = client.GetPush();

            push.RegisterTemplateAsync(_deviceToken, template, expiryDate, "myTemplate", tag)
        }

7. 在 **AppDelegate** 中，覆寫 **ReceivedRemoteNotification** 事件：

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            NSObject inAppMessage;

            bool success = userInfo.TryGetValue(new NSString("inAppMessage"), out inAppMessage);

            if (success)
            {
                var alert = new UIAlertView("Got push notification", inAppMessage.ToString(), null, "OK", null);
                alert.Show();
            }
        }

您的應用程式現在已更新為支援推播通知。

### <a name="update-scripts"></a>在管理入口網站中更新已註冊的插入指令碼

1. 在管理入口網站中，按一下 [資料] 索引標籤，然後按一下 [TodoItem] 資料表。

    ![][21]

2. 在 [todoitem] 中，按一下 [指令碼] 索引標籤，然後選取 [插入]。

    ![][22]

    這會顯示 [TodoItem] 資料表中發生插入時所叫用的函數。

3. 使用下列程式碼來取代 insert 函數，然後按一下 **[儲存]**：

          function insert(item, user, request) {
          // Execute the request and send notifications.
             request.execute({
             success: function() {                      
              // Create a template-based payload.
              var payload = '{ "message" : "New item added: ' + item.text + '" }';            

              // Write the default response and send a notification
              // to all platforms.            
              push.send(null, payload, {               
                  success: function(pushResponse){
                  console.log("Sent push:", pushResponse);
                  // Send the default response.
                  request.respond();
                  },              
                  error: function (pushResponse) {
                      console.log("Error Sending push:", pushResponse);
                       // Send the an error response.
                      request.respond(500, { error: pushResponse });
                      }           
               });                 
              }
           });   
          }

    如此即會註冊新的插入指令碼，該指令碼會將推播通知 (插入的文字) 傳送給插入要求中所提供的裝置。

   >[AZURE.NOTE]此指令碼會延遲通知的傳送，讓您有時間關閉應用程式來接收快顯通知。

### <a name="test"></a>在應用程式中測試推播通知

1. 按 [執行] 按鈕以組建專案並在可執行 iOS 的裝置上啟動應用程式，然後按一下 [確定] 以接受推播通知。

   >[AZURE.NOTE]您必須明確地接受來自應用程式的推播通知。只有在應用程式第一次執行時，才會發生此要求。

2. 在應用程式中，按一下 [**新增**] 按鈕、新增工作標題，然後按一下 [**儲存**] 按鈕。 

3. 確認您已接收到通知，然後按一下 [確定] 以關閉通知。


您已成功完成此教學課程。

## <a name="Android"></a>將推播通知新增至 Xamarin.Forms.Android 應用程式

您會使用 Google Cloud Messaging (GCM) 服務將推播通知新增至 Android 應用程式。您需要作用中的 Google 帳戶和 [Google Cloud Messaging 用戶端元件]。

###<a id="register"></a>啟用 Google Cloud Messaging

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-Google-cloud-messaging.md)]

###<a id="configure"></a>設定行動服務以傳送推播要求

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

###<a id="update-scripts"></a>更新註冊的插入指令碼以傳送通知

>[AZURE.NOTE]下列步驟示範如何在 Azure 管理入口網站中對 TodoItem 資料表上的插入作業更新註冊的指令碼。您也可以直接在 Visual Studio 之伺服器總管的 Azure 節點中，直接存取和編輯此行動服務指令碼。

在管理入口網站中，按一下 [資料] 索引標籤，然後按一下 [TodoItem] 資料表。

   ![][21]

2. 在 [todoitem] 中，按一下 [指令碼] 索引標籤，然後選取 [插入]。

   ![][22]

    This displays the function that is invoked when an insert occurs in the **TodoItem** table.

3. 使用下列程式碼來取代 insert 函數，然後按一下 **[儲存]**：

          function insert(item, user, request) {
          // Execute the request and send notifications.
             request.execute({
             success: function() {                      
              // Create a template-based payload.
              var payload = '{ "message" : "New item added: ' + item.text + '" }';            

              // Write the default response and send a notification
              // to all platforms.            
              push.send(null, payload, {               
                  success: function(pushResponse){
                  console.log("Sent push:", pushResponse);
                  // Send the default response.
                  request.respond();
                  },              
                  error: function (pushResponse) {
                      console.log("Error Sending push:", pushResponse);
                       // Send the an error response.
                      request.respond(500, { error: pushResponse });
                      }           
               });                 
              }
           });   
          }


    如此即會註冊新的插入指令碼，該指令碼會將推播通知 (插入的文字) 傳送給插入要求中所提供的裝置。

   >[AZURE.NOTE]此指令碼會延遲通知的傳送，讓您有時間關閉應用程式來接收快顯通知。


###<a id="configure-app"></a>設定用於推播通知的現有專案

1. 在 [方案] 檢視中，展開 Xamarin.Android 應用程式中的 [元件] 資料夾，並確認已安裝 Azure 行動服務封裝。 

2. 在 [元件] 資料夾上按一下滑鼠右鍵，按一下 [取得更多元件...]，搜尋 [Google Cloud Messaging 用戶端] 元件，然後將它新增至專案。

1. 開啟 MainActivity.cs 專案檔案，然後將下列 using 陳述式加入至類別：

		using Gcm.Client;


4.	在 **MainActivity** 類別中，在呼叫 **LoadApplication** 方法之後，將下列程式碼加入至 **OnCreate** 方法：
            
            try
            {
                // Check to ensure everything's setup right
                GcmClient.CheckDevice(this);
                GcmClient.CheckManifest(this);

                // Register for push notifications
                System.Diagnostics.Debug.WriteLine("Registering...");
                GcmClient.Register(this, PushHandlerBroadcastReceiver.SENDER_IDS);
            }
            catch (Java.Net.MalformedURLException)
            {
                CreateAndShowDialog(new Exception("There was an error creating the Mobile Service. Verify the URL"), "Error");
            }
            catch (Exception e)
            {
                CreateAndShowDialog(e, "Error");
            }

您的 **MainActivity** 現在可供新增推播通知。

###<a id="add-push"></a>將推播通知程式碼新增至應用程式

5. 在ToDoAzure.Droid 專案中，建立名為 `GcmService` 的新類別。

5. 在 **GcmService** 中新增下列 using 陳述式：

		using Gcm.Client;
		using Microsoft.WindowsAzure.MobileServices;

6. 在 **using** 陳述式與 **namespace** 宣告之間新增下列權限要求：

		[assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

        //GET_ACCOUNTS is only needed for android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

7. 在 **GcmService.cs** 專案檔案中，新增下列類別：
 
        [BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]

        public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
        {
        
            public static string[] SENDER_IDS = new string[] { "<PROJECT_NUMBER>" };

        }

	在上述程式碼中，您必須使用在 Google 開發人員入口網站佈建您的應用程式時由 Google 指派給您的專案編號來取代 _`<PROJECT_NUMBER>`_。

8. 在 GcmService.cs 專案檔案中，加入下列可定義 **GcmService** 類別的程式碼：
 
         [Service]
         public class GcmService : GcmServiceBase
         {
             public static string RegistrationID { get; private set; }

             public GcmService()
                 : base(PushHandlerBroadcastReceiver.SENDER_IDS){}
         }


	請注意，此類別衍生自 **GcmServiceBase** 而且必須對此類別套用 **Service** 屬性。

	>[AZURE.NOTE]**GcmServiceBase** 類別實作 **OnRegistered()**、**OnUnRegistered()**、**OnMessage()** 及 **OnError()** 方法。您必須覆寫 **GcmService** 類別中的這些方法。

5. 在 **GcmService** 類別中加入下列程式碼，以覆寫 **OnRegistered** 事件處理常式。

        protected override void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose(PushHandlerBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            createNotification("GcmService Registered...", "The device has been Registered, Tap to View!");

            MobileServiceClient client =  MainActivity.DefaultService.todoItemManager.GetClient;
            
            var push = client.GetPush();

            MainActivity.DefaultService.RunOnUiThread(() => Register(push, null));

        }
        public async void Register(Microsoft.WindowsAzure.MobileServices.Push push, IEnumerable<string> tags)
        {
            try
            {
                const string template = "{"data":{"message":"$(message)"}}";

                await push.RegisterTemplateAsync(RegistrationID, template, "mytemplate", tags);
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(ex.Message);
                Debugger.Break();
            }
        }

	此方法使用傳回的 GCM 註冊識別碼，向 Azure 註冊以取得推送通知。

10. 以下列程式碼覆寫 **GcmService** 中的 **OnMessage** 方法：

        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info(PushHandlerBroadcastReceiver.TAG, "GCM Message Received!");

            var msg = new StringBuilder();

            if (intent != null && intent.Extras != null)
            {
                foreach (var key in intent.Extras.KeySet())
                    msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
            }

            //Store the message
            var prefs = GetSharedPreferences(context.PackageName, FileCreationMode.Private);
            var edit = prefs.Edit();
            edit.PutString("last_msg", msg.ToString());
            edit.Commit();

            string message = intent.Extras.GetString("message");
            if (!string.IsNullOrEmpty(message))
            {
                createNotification("New todo item!", "Todo item: " + message);
                return;
            }

            string msg2 = intent.Extras.GetString("msg");
            if (!string.IsNullOrEmpty(msg2))
            {
                createNotification("New hub message!", msg2);
                return;
            }

            createNotification("Unknown message details", msg.ToString());
        }

        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

            //Create an intent to show ui
            var uiIntent = new Intent(this, typeof(MainActivity));

            //Create the notification
            var notification = new Notification(Android.Resource.Drawable.SymActionEmail, title);

            //Auto cancel will remove the notification once the user touches it
            notification.Flags = NotificationFlags.AutoCancel;

            //Set the notification info
            //we use the pending intent, passing our ui intent over which will get called
            //when the notification is tapped.
            notification.SetLatestEventInfo(this, title, desc, PendingIntent.GetActivity(this, 0, uiIntent, 0));

            //Show the notification
            notificationManager.Notify(1, notification);
        }

12. 加入下列方法覆寫編譯專案所需的 **OnUnRegistered()** 與 **OnError()**。

        protected override void OnError(Context context, string errorId)
        {
              Log.Error(PushHandlerBroadcastReceiver.TAG, "GCM Error: " + errorId);
        }

###<a id="test"></a>在應用程式中測試推播通知

您可以使用 USB 纜線直接連接 Android 手機，或使用模擬器中的虛擬裝置，對應用程式進行測試。

當您在模擬器中執行此應用程式時，請務必使用支援 Google API 的 Android 虛擬裝置 (AVD)。

> [AZURE.IMPORTANT]若要接收推播通知，您必須在您的 Android 虛擬裝置上設定 Google 帳戶 (在模擬器中，瀏覽到 [設定]，並按一下 [新增帳戶])。另外，確定模擬器已連線到網際網路。

1. 從 [工具] 中，按一下 [Open Android Emulator Manager]，選取您的裝置，然後按一下 [Edit]。
    
    ![][125]

2. 在 [Target] 中選取 [Google API]，然後按一下 [OK]。
    
    ![][126]

3. 在頂端工具列上，按一下 [執行]，然後選取您的應用程式。這將啟動模擬器，並執行應用程式。

  應用程式將從 GCM 擷取 *registrationId*，並向通知中心註冊。

1. 在應用程式中加入新的工作。

2. 從螢幕頂端向下撥動將裝置的通知中心開啟，以檢視通知。

	![][127]

## <a name="Windows"></a>將推播通知新增至 Xamarin.Forms.Windows 應用程式

本節說明如何使用 Azure 行動服務，將推播通知傳送至 Xamarin.Forms 方案中包含的 Windows Phone Silverlight 應用程式。

###<a id="update-app"></a>更新應用程式以註冊通知

您必須先註冊通知通道，您的應用程式才能接收推播通知。

1. 在 Visual Studio 中，開啟 App.xaml.cs 檔案，並新增下列 `using` 陳述式：

        using Microsoft.Phone.Notification;

3. 新增下列程式碼至 App.xaml.cs：
	
        public static HttpNotificationChannel CurrentChannel { get; private set; }

        private void AcquirePushChannel()
        {
            CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");

            if (CurrentChannel == null)
            {
                CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                CurrentChannel.Open();
                CurrentChannel.BindToShellToast();
            }

            CurrentChannel.ChannelUriUpdated +=
                new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
                {

                   // Register for notifications using the new channel
                    const string template =
                    "<?xml version="1.0" encoding="utf-8"?><wp:Notification " +
                    "xmlns:wp="WPNotification"><wp:Toast><wp:Text1>$(message)</wp:Text1></wp:Toast></wp:Notification>";

                    await client.GetPush()
                        .RegisterTemplateAsync(CurrentChannel.ChannelUri.ToString(), template, "mytemplate");
                });
        }

    此程式碼會從 Windows Phone 8.x "Silverlight" 使用的 Microsoft 推播通知服務 (MPNS)，擷取適用於應用程式的 ChannelURI，然後註冊該 ChannelURI，以進行推播通知。

	>[AZURE.NOTE]在本教學課程中，行動服務會傳送快顯通知給裝置。傳送磚通知時，您必須在通道上改為呼叫 **BindToShellTile** 方法。

4. 在 App.xaml.cs 中的 **Application_Launching** 事件處理常式最前面，在新的 **AcquirePushChannel** 方法中新增下列呼叫：

        AcquirePushChannel();

	這會確使在每次載入頁面時都會要求註冊。在您的應用程式中，您可能只想定期進行此註冊，以確保註冊是最新的。

5. 按 **F5** 鍵以執行應用程式。包含註冊金鑰的快顯對話方塊隨即顯示。
  
6.	在 [方案總管] 中展開 [屬性]，開啟 WMAppManifest.xml 檔案，按一下 [功能] 索引標籤，確定已核取 **ID___CAP___PUSH_NOTIFICATION** 功能。

   	![在 VS 中啟用通知](./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-app-enable-push-wp8.png)

   	如此可確定您的應用程式可以提出快顯通知。

###<a id="update-scripts"></a>更新伺服器指令碼以傳送推播通知

最後，您必須在 TodoItem 資料表上更新對插入作業註冊的指令碼，以傳送通知。

1. 在管理入口網站中，按一下 [資料] 索引標籤，然後按一下 [TodoItem] 資料表。

    ![][21]

2. 在 [todoitem] 中，按一下 [指令碼] 索引標籤，然後選取 [插入]。

    ![][22]

    這會顯示 [TodoItem] 資料表中發生插入時所叫用的函數。

3. 使用下列程式碼來取代 insert 函數，然後按一下 **[儲存]**：
          
          function insert(item, user, request) {
          // Execute the request and send notifications.
             request.execute({
             success: function() {                      
              // Create a template-based payload.
              var payload = '{ "message" : "New item added: ' + item.text + '" }';            

              // Write the default response and send a notification
              // to all platforms.            
              push.send(null, payload, {               
                  success: function(pushResponse){
                  console.log("Sent push:", pushResponse);
                  // Send the default response.
                  request.respond();
                  },              
                  error: function (pushResponse) {
                      console.log("Error Sending push:", pushResponse);
                       // Send the an error response.
                      request.respond(500, { error: pushResponse });
                      }           
               });                 
              }
           });   
          }


    如此即會註冊新的插入指令碼，該指令碼會將推播通知 (插入的文字) 傳送給插入要求中所提供的裝置。

3. 按一下 [推播] 索引標籤，核取 [Enable unauthenticated push notifications]，然後按一下 [儲存]。

	這會使行動服務以未經驗證的模式連接到 MPNS，以傳送推播通知。

	>[AZURE.NOTE]本教學課程使用處於未通過驗證模式的 MPNS。在此模式中，MPNS 會限制可傳送至裝置通道的通知數。若要移除此限制，您必須按一下 [上傳] 並選取憑證，以產生憑證並加以上傳。如需產生憑證的詳細資訊，請參閱 [設定已驗證的 Web 服務以傳送 Windows Phone 的推播通知]。

###<a id="test"></a>在應用程式中測試推播通知

1. 在 Visual Studio 中，按 F5 鍵以執行此應用程式。

    >[AZURE.NOTE]在 Windows Phone 模擬器上測試時，可能會發生 401 未授權的 RegistrationAuthorizationException。因為 Windows Phone 模擬器將其時鐘與主機電腦同步的方式，可能會在 `RegisterNativeAsync()` 呼叫期間發生這種情形。這樣可能會產生將被拒絕的安全性權杖。若要解決這個問題，只要在測試之前，手動設定模擬器中的時鐘即可。

5. 在應用程式中，建立標題為 **Hello push** 的新工作，然後立即按一下啟動按鈕或返回按鈕，以離開應用程式。

  	如此會傳送插入要求給行動服務來儲存新增的項目。請注意，裝置會接收到顯示 **hello push** 的快顯通知。

	![接收到快顯通知](./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push5-wp8.png)

	>[AZURE.NOTE]如果您仍在應用程式中，就不會收到該通知。若要在應用程式作用中的情況下，接收快顯通知，您必須處理 [ShellToastNotificationReceived](http://msdn.microsoft.com/library/windowsphone/develop/microsoft.phone.notification.httpnotificationchannel.shelltoastnotificationreceived(v=vs.105).aspx) 事件。
   
<!-- Anchors. -->
[Generate the certificate signing request]: #certificates
[Register your app and enable push notifications]: #register
[Create a provisioning profile for the app]: #profile
[Configure Mobile Services]: #configure-mobileServices
[Configure the Xamarin.iOS App]: #configure-app
[Update scripts to send push notifications]: #update-scripts
[Add push notifications to the app]: #add-push
[Insert data to receive notifications]: #test

<!-- Images. -->

[5]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step5.png
[6]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step6.png
[7]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step7.png

[9]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step9.png
[10]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step10.png

[17]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-selection.png
[19]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-push-tab-ios.png
[20]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-portal-data-tables.png
[22]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-insert-script-push2.png
[23]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push1-ios.png
[24]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push2-ios.png
[25]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push4-ios.png
[28]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-02.png
[103]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-03.png
[104]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-04.png
[105]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-05.png
[106]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-06.png
[107]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-07.png
[108]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-08.png

[110]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-10.png
[111]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-11.png
[112]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-12.png
[113]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-13.png
[114]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-14.png
[115]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-15.png
[116]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-16.png
[117]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-17.png

[120]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-20.png
[121]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-21.png
[122]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-22.png
[123]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-data-tab-empty.png
[124]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-create-todoitem-table.png
[125]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/notification-hub-create-android-app7.png
[126]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/notification-hub-create-android-app8.png
[127]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/notification-area-received.png


[Xamarin.iOS Studio]: http://xamarin.com/platform
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Apple 推播通知服務]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Get started with Mobile Services]: mobile-services-ios-get-started.md

[Xamarin 裝置佈建]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/


[Azure 管理入口網站]: https://manage.windowsazure.com/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
[Azure 行動服務元件]: http://components.xamarin.com/view/azure-mobile-services/
[completed example project]: http://go.microsoft.com/fwlink/p/?LinkId=331303
[Xamarin.iOS]: http://xamarin.com/download
[Google Cloud Messaging 用戶端元件]: http://components.xamarin.com/view/GCMClient/
[Xamarin.Forms Azure 推播通知入門範例]: https://github.com/Azure/mobile-services-samples/tree/master/TodoListXamarinForms
[完整的 Xamarin.Forms Azure 推播通知範例]: https://github.com/Azure/mobile-services-samples/tree/master/GettingStartedWithPushXamarinForms
 

<!---HONumber=July15_HO2-->