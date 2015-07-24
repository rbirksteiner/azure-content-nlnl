<properties 
	pageTitle="使用行動服務 Android 用戶端程式庫" 
	description="了解如何使用適用於 Azure 行動服務的 Android 用戶端。" 
	services="mobile-services" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="ricksal"/>


# 如何使用行動服務的 Android 用戶端程式庫

[AZURE.INCLUDE [mobile-services-selector-client-library](../../includes/mobile-services-selector-client-library.md)]

本指南將說明如何使用 Azure 行動服務的 Android 用戶端執行一般案例。所涵蓋的案例包括查詢資料，以及插入、更新和刪除資料、驗證使用者、處理錯誤和自訂用戶端。

如果您不熟悉行動服務，應先完成[開始使用行動服務]的快速入門教學課程。成功完成該教學課程可確保您將安裝 Android Studio；它會幫助您設定帳戶並建立您的第一個行動服務，並安裝支援 Android 2.2 或更新版本的行動服務 SDK，但我們建議您針對 Android 4.2 版或更新版本進行建置。

您可以在[這裡](http://go.microsoft.com/fwlink/p/?LinkId=298735)找到 Android 用戶端程式庫的 Javadocs API 參考。

[AZURE.INCLUDE [mobile-services-concepts](../../includes/mobile-services-concepts.md)]

##<a name="setup"></a>設定和必要條件

我們假設您已建立行動服務和資料表。如需詳細資訊，請參閱[建立資料表](http://go.microsoft.com/fwlink/p/?LinkId=298592)。在本主題所使用的程式碼中，我們假設資料表的名稱為 *ToDoItem*，且其中包含下列資料行：

- id
- text
- 完成

對應的型別用戶端物件如下：

	public class ToDoItem {
		private String id;
		private String text;
		private Boolean complete;
	}
	
啟用動態結構描述時，Azure 行動服務會根據插入或更新要求中的物件自動產生新資料行。如需詳細資訊，請參閱[動態結構描述](http://go.microsoft.com/fwlink/p/?LinkId=296271)。

##<a name="create-client"></a>作法：建立行動服務用戶端
下列程式碼將建立用來存取行動服務的 [MobileServiceClient](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html) 物件。程式碼會進入在 *AndroidManifest.xml* 中指定為 **MAIN** 動作和 **LAUNCHER** 類別目錄之 Activity 類別的 `onCreate` 方法。

		MobileServiceClient mClient = new MobileServiceClient(
				"MobileServiceUrl", // Replace with the above Site URL
				"AppKey", 			// replace with the Application Key 
				this)

在上述程式碼中，請以行動服務 URL 和應用程式金鑰依序取代 `MobileServiceUrl` 和 `AppKey`。這兩個項目都可從管理入口網站取得，只要選取您的行動服務，再按一下 [儀表板] 即可。

##<a name="instantiating"></a>作法：建立資料表參考

要查詢或修改行動服務中的資料，最簡單的方式就是使用*型別程式設計模型*，因為 Java 屬於強型別語言 (後續我們將討論*不具型別的*模型)。這個模型會在用戶端與行動服務之間傳送資料時，使用 [gson](http://go.microsoft.com/fwlink/p/?LinkId=290801) 程式庫提供順暢的 JSON 序列化和還原序列化：開發人員不需要執行任何動作，架構會處理一切。

查詢或修改資料的第一步，是要在 [**MobileServiceClient**](http://go.microsoft.com/fwlink/p/?LinkId=296835) 上呼叫 [getTable](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html) 方法，以建立 **MobileServiceTable** 物件。我們將考量此方法的兩個多載：

	public class MobileServiceClient {
	    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
	    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
	}

在下列程式碼中，*mClient* 是對您的行動服務用戶端的參考。

[第一個多載](http://go.microsoft.com/fwlink/p/?LinkId=296839)會在類別名稱與資料表名稱相同的情況下使用：

		MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);


[第二個多載](http://go.microsoft.com/fwlink/p/?LinkId=296840)會在資料表名稱與類型名稱不同時使用。

		MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

## <a name="api"></a>API 結構

從 2.0 版用戶端程式庫起，行動服務資料表作業會在所有非同步作業 (例如涉及查詢的方法) 和作業 (像是插入、更新和刪除) 中使用 [Future](http://developer.android.com/reference/java/util/concurrent/Future.html) 和 [AsyncTask](http://developer.android.com/reference/android/os/AsyncTask.html) 物件。如此可使得執行多個多個作業 (在背景執行緒時) 變得更容易，而無需處理多個巢狀回呼。


##<a name="querying"></a>作法：查詢行動服務中的資料

本節將說明如何對行動服務發出查詢。各小節將說明不同的層面，例如排序、篩選和分頁等。最後，我們將討論如何將這些作業串連在一起。

### <a name="showAll"></a>作法：從資料表傳回所有項目

下列程式碼會傳回 *ToDoItem* 資料表中的所有項目。新增項目到配接器，即會在 UI 中顯示。此程式碼類似於[開始使用行動服務]快速入門教學課程中的程式碼。

		new AsyncTask<Void, Void, Void>() {

            @Override
            protected Void doInBackground(Void... params) {
                try {

					final MobileServiceList<ToDoItem> result = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : result) {
                                mAdapter.add(item);
                            }
                        }
                    });
               } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
               }
               return result;
            }
		}.execute();


與此類似的查詢會使用 [AsyncTask](http://developer.android.com/reference/android/os/AsyncTask.html) 物件。

*result* 變數會傳回查詢的結果，而 `mToDoTable.execute().get()` 陳述式後面的程式碼會示範如何顯示個別的資料列。


### <a name="filtering"></a>作法：篩選傳回的資料

下列程式碼會從 *ToDoItem* 資料表傳回 *complete* 欄位等於 *false* 的所有項目。*mToDoTable* 是我們先前建立的行動服務資料表的參考。

        new AsyncTask<Void, Void, Void>() {
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final MobileServiceList<ToDoItem> result = 
						mToDoTable.where().field("complete").eq(false).execute().get();
					for (ToDoItem item : result) {
                		Log.i(TAG, "Read object with ID " + item.id);  
					}
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();



您使用 [**where**](http://go.microsoft.com/fwlink/p/?LinkId=296867) 方法呼叫，對資料表參考執行篩選。接著您又依序使用 [**field**](http://go.microsoft.com/fwlink/p/?LinkId=296869) 方法呼叫，以及指定邏輯述詞的方法呼叫。可能的數詞方法包括 [**eq**](http://go.microsoft.com/fwlink/p/?LinkId=298461)、[**ne**](http://go.microsoft.com/fwlink/p/?LinkId=298462)、[**gt**](http://go.microsoft.com/fwlink/p/?LinkId=298463)、[**ge**](http://go.microsoft.com/fwlink/p/?LinkId=298464)、[**lt**](http://go.microsoft.com/fwlink/p/?LinkId=298465)、[**le**](http://go.microsoft.com/fwlink/p/?LinkId=298466) 等。

這已足以用來比較數字/字串欄位與特定值。但您能做的絕對不僅止於此。

例如，您可以依日期進行篩選。您可以比較整個日期欄位，也可以使用下列方法比較日期的某些部分：[**year**](http://go.microsoft.com/fwlink/p/?LinkId=298467)、[**month**](http://go.microsoft.com/fwlink/p/?LinkId=298468)、[**day**](http://go.microsoft.com/fwlink/p/?LinkId=298469)、[**hour**](http://go.microsoft.com/fwlink/p/?LinkId=298470)、[**minute**](http://go.microsoft.com/fwlink/p/?LinkId=298471) 和 [**second**](http://go.microsoft.com/fwlink/p/?LinkId=298472)。下列節錄的程式碼會為*到期日*為 2013 年的項目新增篩選器。

		mToDoTable.where().year("due").eq(2013).execute().get();

您可以使用 [**startsWith**](http://go.microsoft.com/fwlink/p/?LinkId=298473)、[**endsWith**](http://go.microsoft.com/fwlink/p/?LinkId=298474)、[**concat**](http://go.microsoft.com/fwlink/p/?LinkId=298475)、[**subString**](http://go.microsoft.com/fwlink/p/?LinkId=298477)、[**indexOf**](http://go.microsoft.com/fwlink/p/?LinkId=298488)、[**replace**](http://go.microsoft.com/fwlink/p/?LinkId=298491)、[**toLower**](http://go.microsoft.com/fwlink/p/?LinkId=298492)、[**toUpper**](http://go.microsoft.com/fwlink/p/?LinkId=298493)、[**trim**](http://go.microsoft.com/fwlink/p/?LinkId=298495) 和 [**length**](http://go.microsoft.com/fwlink/p/?LinkId=298496) 等方法，對字串欄位執行多種複雜的篩選。下列節錄的程式碼會對 *text* 資料行的開頭為 "PRI0" 的資料表資料列進行篩選。

		mToDoTable.where().startsWith("text", "PRI0").execute().get();

數字欄位也可透過 [**add**](http://go.microsoft.com/fwlink/p/?LinkId=298497)、[**sub**](http://go.microsoft.com/fwlink/p/?LinkId=298499)、[**mul**](http://go.microsoft.com/fwlink/p/?LinkId=298500)、[**div**](http://go.microsoft.com/fwlink/p/?LinkId=298502)、[**mod**](http://go.microsoft.com/fwlink/p/?LinkId=298503)、[**floor**](http://go.microsoft.com/fwlink/p/?LinkId=298505)、[**ceiling**](http://go.microsoft.com/fwlink/p/?LinkId=298506) 和 [**round**](http://go.microsoft.com/fwlink/p/?LinkId=298507) 等方法，進行多種更複雜的篩選。下列節錄的程式碼會對 *duration* 為偶數的資料表資料列進行篩選。

		mToDoTable.where().field("duration").mod(2).eq(0).execute().get();


您可以將述詞與 [**and**](http://go.microsoft.com/fwlink/p/?LinkId=298512)、[**or**](http://go.microsoft.com/fwlink/p/?LinkId=298514)、[**not**](http://go.microsoft.com/fwlink/p/?LinkId=298515) 等方法相結合。下列節錄的程式碼會結合前述的兩個範例。

		mToDoTable.where().year("due").eq(2013).and().startsWith("text", "PRI0")
					.execute().get();

此外，您也可以將邏輯運算子分組和內嵌，如下列節錄的程式碼所示：

		mToDoTable.where()
					.year("due").eq(2013)
						.and
					(startsWith("text", "PRI0").or().field("duration").gt(10))
					.execute().get();

如需篩選的詳細討論與範例，請參閱[探索功能豐富的行動服務 Android 用戶端查詢模型](http://hashtagfail.com/post/46493261719/mobile-services-android-querying) (英文)。

### <a name="sorting"></a>作法：排序傳回的資料

下列程式碼會從 *ToDoItems* 資料表傳回依 *text* 欄位遞增排序的所有項目。*mToDoTable* 是您先前建立的行動服務資料表的參考。

		mToDoTable.orderBy("text", QueryOrder.Ascending).execute().get();

[**orderBy**](http://go.microsoft.com/fwlink/p/?LinkId=298519) 方法的第一個參數是一個字串，代表作為排序依據的欄位名稱。

第二個參數會使用 [**QueryOrder**](http://go.microsoft.com/fwlink/p/?LinkId=298521) 列舉，指定要進行遞增還是遞減排序。

請注意，如果您使用 ***where*** 方法進行篩選，***where*** 方法必須要在 ***orderBy*** 方法之前叫用。

### <a name="paging"></a>作法：以分頁方式傳回資料

第一個範例將說明如何從資料表中選取前 5 個項目。查詢會傳回 *ToDoItems* 資料表中的項目。*mToDoTable* 是您先前建立的行動服務資料表的參考。

       final MobileServiceList<ToDoItem> result = mToDoTable.top(5).execute().get();


接著，我們定義略過前 5 個項目，而傳回後續 5 個項目的查詢。

		mToDoTable.skip(5).top(5).execute().get();


### <a name="selecting"></a>作法：選取特定資料行

下列程式碼說明如何傳回 *ToDoItems* 資料表中的所有項目，但僅顯示 *complete* 和 *text* 欄位。*mToDoTable* 是我們先前建立的行動服務資料表的參考。

		mToDoTable.select("complete", "text").execute().get();

	
在此，select 函數的參數是您要傳回之資料表資料行的字串名稱。

如果有 [**where**](http://go.microsoft.com/fwlink/p/?LinkId=290689) 和 [**orderBy**](http://go.microsoft.com/fwlink/p/?LinkId=296296) 等方法存在，[**select**](http://go.microsoft.com/fwlink/p/?LinkId=296313) 方法就必須跟隨在這些方法之後。而其後可以跟隨 [**top**](http://go.microsoft.com/fwlink/p/?LinkId=298731) 之類的方法。

### <a name="chaining"></a>作法：串連查詢方法 

用來查詢行動服務資料表的方法是可以串連的。這可讓您從排序和分頁的篩選資料列中選取特定資料行，或執行類似作業。您可以建立複雜的邏輯篩選器。

之所以能有此效用，是因為您所使用的查詢方法會傳回 [**MobileServiceQuery&lt;T&gt;**](http://go.microsoft.com/fwlink/p/?LinkId=298551) 物件，而這些物件後續又可供其他方法加以叫用。若要結束這一系列的方法，而實際執行查詢，您可以呼叫 [**execute**](http://go.microsoft.com/fwlink/p/?LinkId=298554) 方法。

在下列程式碼範例中，*mToDoTable* 是行動服務 *ToDoItem* 資料表的參考。

		mToDoTable.where().year("due").eq(2013)
						.and().startsWith("text", "PRI0")
						.or().field("duration").gt(10)
					.select("id", "complete", "text", "duration")
					.orderBy(duration, QueryOrder.Ascending).top(20)				
					.execute().get();

要將方法鏈結在一起，最主要的要求是必須先使用 *where* 方法和述詞。其後，您可以依據應用程式的需求，以最適當的順序呼叫後續方法。


##<a name="inserting"></a>作法：將資料插入行動服務

下列程式碼說明如何在資料表中插入新的資料列。

首先，您將 *ToDoItem* 類別的執行個體具現化，並設定其屬性。

		ToDoItem mToDoItem = new ToDoItem();
		mToDoItem.text = "Test Program";
		mToDoItem.complete = false;
		
 接下來，您會執行下列程式碼：

		// Insert the new item
	    new AsyncTask<Void, Void, Void>() {
	
	        @Override
	        protected Void doInBackground(Void... params) {
	            try {
	                mToDoTable.insert(item).get();
	                if (!item.isComplete()) {
	                    runOnUiThread(new Runnable() {
	                        public void run() {
	                            mAdapter.add(item);
	                        }
	                    });
	                }
	            } catch (Exception exception) {
	                createAndShowDialog(exception, "Error");
	            }
	            return null;
	        }
	    }.execute();


此程式碼會插入新的項目，並將它加入至配接器，使得它顯示在 UI 中。

行動服務支援使用唯一自訂字串值的資料表 ID。這可讓應用程式在行動服務資料表的 ID 資料行中使用電子郵件地址或使用者名稱之類的自訂值。例如，如果您想要根據電子郵件地址來識別每筆記錄，您可以使用下列 JSON 物件。

		ToDoItem mToDoItem = new ToDoItem();
		mToDoItem.id = "myemail@mydomain.com";
		mToDoItem.text = "Test Program";
		mToDoItem.complete = false;

如果將新記錄插入至資料表時未提供字串識別碼值，則行動服務會產生唯一值做為識別碼。

支援字串識別碼對開發人員而言有下列好處

+ 不需要往返存取資料庫就能產生識別碼。
+ 輕鬆合併不同資料表或資料庫的記錄。
+ 識別碼值與應用程式邏輯的整合更理想。

您也可以使用伺服器指令碼來設定識別碼值。下列指令碼範例將產生自訂 GUID，並將其指派給新記錄的識別碼。此識別碼與行動服務在您未傳入記錄識別碼值時所將產生的識別碼值相類似。

	//Example of generating an id. This is not required since Mobile Services
	//will generate an id if one is not passed in.
	item.id = item.id || newGuid();
	request.execute();

	function newGuid() {
		var pad4 = function(str) { return "0000".substring(str.length) + str; };
		var hex4 = function () { return pad4(Math.floor(Math.random() * 0x10000 /* 65536 */ ).toString(16)); };
		return (hex4() + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + hex4() + hex4());
	}


如果應用程式提供識別碼的值，則行動服務會原封不動地儲存它。這包括開頭或結尾的空白字元。值中的空白字元將不會被去除。

`id` 的值必須是唯一的，且不可包含下列字元集中的字元：

+ 控制字元：[0x0000-0x001F] 和 [0x007F-0x009F]。如需詳細資訊，請參閱 [ASCII 控制碼 C0 和 C1] (英文)。
+  可列印的字元：**"**(0x0022)、**+** (0x002B)、**/** (0x002F)、**?** (0x003F)、**** (0x005C)、**`** (0x0060)
+  識別碼 "." 和 ".."

另外，您也可以在資料表中使用整數識別碼。若要使用整數識別碼，您必須使用 `--integerId` 選項，以 `mobile table create` 命令建立資料表。此命令需要在 Azure 的命令列介面 (CLI) 中執行。如需關於使用 CLI 的詳細資訊，請參閱「使用 CLI 管理行動服務資料表」。


##<a name="updating"></a>作法︰更新行動服務中的資料

下列程式碼說明如何更新資料表中的資料。在此範例中，*item* 是 *ToDoItem* 資料表中某個資料列的參考，其中已有一些變更。下列方法會更新資料表和 UI 配接器。

	private void updateItem(final ToDoItem item) {
	    if (mClient == null) {
	        return;
	    }
	
	    new AsyncTask<Void, Void, Void>() {
	
	        @Override
	        protected Void doInBackground(Void... params) {
	            try {
	                mToDoTable.update(item).get();
	                runOnUiThread(new Runnable() {
	                    public void run() {
	                        if (item.isComplete()) {
	                            mAdapter.remove(item);
	                        }
	                        refreshItemsFromTable();
	                    }
	                });
	            } catch (Exception exception) {
	                createAndShowDialog(exception, "Error");
	            }
	            return null;
	        }
	    }.execute();
	}

##<a name="deleting"></a>作法：刪除行動服務中的資料

下列程式碼說明如何刪除資料表中的資料。它會從 ToDoItem 資料表中將 UI 上已核取 [已完成] 核取方塊的現有項目刪除。

	public void checkItem(final ToDoItem item) {
		if (mClient == null) {
			return;
		}

		// Set the item as completed and update it in the table
		item.setComplete(true);
		
		new AsyncTask<Void, Void, Void>() {

            @Override
            protected Void doInBackground(Void... params) {
                try {
                    mToDoTable.delete(item);
                    runOnUiThread(new Runnable() {
                        public void run() {
                            if (item.isComplete()) {
                                mAdapter.remove(item);
                            }
                            refreshItemsFromTable();
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
	}


下列程式碼將說明另一種執行方式。此方式會指定要刪除之資料列的 ID 欄位值 (假設等於 "2FA404AB-E458-44CD-BC1B-3BC847EF0902")，以刪除 ToDoItem 資料表中的現有項目。在實際的應用程式中，您會以某種方式取得識別碼，並將它傳入做為變數。在這裡，為簡化測試，您可以移至服務的 Azure 行動服務入口網站，按一下 [資料] 並複製您想要測試的識別碼。

    public void deleteItem(View view) {

        final String ID = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
        new AsyncTask<Void, Void, Void>() {

            @Override
            protected Void doInBackground(Void... params) {
                try {
                    mToDoTable.delete(ID);
                    runOnUiThread(new Runnable() {
                        public void run() {
                            refreshItemsFromTable();
                        }
               });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
    }

##<a name="lookup"></a>作法：查閱特定項目
有時候，您會想要依據 *id* 來查閱特定項目；此方式不像查詢通常會產生符合某個條件的項目集合。下列程式碼說明如何執行此作業，假設 *id* 值為 `0380BAFB-BCFF-443C-B7D5-30199F730335`。在實際的應用程式中，您會以某種方式取得識別碼，並將它傳入做為變數。在這裡，為簡化測試，您可以移至服務的 Azure 行動服務入口網站，按一下 [資料] 索引標籤並複製您想要測試的識別碼。

    /**
     * Lookup specific item from table and UI
     */
    public void lookup(View view) {

        final String ID = "0380BAFB-BCFF-443C-B7D5-30199F730335";
        new AsyncTask<Void, Void, Void>() {

            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final ToDoItem result = mToDoTable.lookUp(ID).get();
                    runOnUiThread(new Runnable() {
                        public void run() {
                            mAdapter.clear();
                            mAdapter.add(result);
                        }
               });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
    }

##<a name="untyped"></a>作法：使用不具類型的資料

不具型別的程式設計模型可讓您精確掌控 JSON 序列化，因此在某些情況下，您可能會想加以使用，例如：您的行動服務資料表包含大量資料行，但您只需要參考其中幾個而已。使用型別模型時，您必須在資料類別中定義所有行動服務資料表的資料行。但在使用不具型別的模型時，您只需定義需要使用的資料行即可。

用來存取資料的 API 呼叫大多會與型別程式設計呼叫相類似。主要的差別在於，在不具型別的模型中，您會叫用 **MobileServiceJsonTable** 物件的方法，而不是 **MobileServiceTable** 物件。


### <a name="json_instance"></a>作法：建立不具型別的資料表執行個體

和型別模型一樣，首先您必須取得資料表參考，但在此案例中這會是 [MobileServicesJsonTable](http://go.microsoft.com/fwlink/p/?LinkId=298733) 物件。您在行動服務用戶端的執行個體上呼叫 [getTable()](http://go.microsoft.com/fwlink/p/?LinkId=298734) 方法，以取得參考。

首先，您會定義變數：

    /**
     * Mobile Service Json Table used to access untyped data
     */
    private MobileServiceJsonTable mJsonToDoTable;



在您於 **onCreate** 方法中建立行動服務用戶端的執行個體後 (此範例中為 *mClient* 變數)，接下來就可以使用下列程式碼建立 **MobileServiceJsonTable** 的執行個體。


            // Get the Mobile Service Json Table to use
            mJsonToDoTable = mClient.getTable("ToDoItem");

在您建立 **MobileServiceJsonTable** 的執行個體後，您在使用型別程式設計模型時所能呼叫的各種方法，幾乎都可在此執行個體上呼叫。但在某些情況下，方法會採用不具型別的參數，如下列範例所示。

### <a name="json_insert"></a>作法：插入不具型別的資料表中

下列程式碼將說明如何執行插入。第一個步驟是建立屬於 <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> 程式庫的 [**JsonObject**](http://google-gson.googlecode.com/svn/trunk/gson/docs/javadocs/com/google/gson/JsonObject.html)。

		JsonObject item = new JsonObject();
		item.addProperty("text", "Wake up");
		item.addProperty("complete", false);

下一個步驟是插入物件。傳至 [**insert**](http://go.microsoft.com/fwlink/p/?LinkId=298535) 方法的回呼函數是 [**TableJsonOperationCallback**](http://go.microsoft.com/fwlink/p/?LinkId=298532) 類別的執行個體。請留意 *insert* 方法的參數為何是 JsonObject。
		 
        // Insert the new item
        new AsyncTask<Void, Void, Void>() {

            @Override
            protected Void doInBackground(Void... params) {
                try {
                    mJsonToDoTable.insert(item).get();
                    refreshItemsFromTable();
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();


如果您需要取得所插入之物件的識別碼，請使用這個方法呼叫：

		        jsonObject.getAsJsonPrimitive("id").getAsInt());


### <a name="json_delete"></a>作法：在不具型別的資料表中進行刪除

下列程式碼將說明如何刪除執行個體 (在此案例中，即為在前述**插入**範例中建立的同一個 *JsonObject* 執行個體)。請注意程式碼與典型案例相同，但方法具有不同的簽章，因為它會參考 **JsonObject**。


         mToDoTable.delete(item);


您也可以直接使用 ID 來刪除執行個體：
		
		 mToDoTable.delete(ID);



### <a name="json_get"></a>作法：從不具型別的資料表傳回所有資料列

下列程式碼將說明如何擷取整個資料表。由於您使用 JSON 資料表，因此可以選擇性地只擷取資料表的某些資料行。

    public void showAllUntyped(View view) {
        new AsyncTask<Void, Void, Void>() {
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final JsonElement result = mJsonToDoTable.execute().get();
                    final JsonArray results = result.getAsJsonArray();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (JsonElement item : results) {
                                String ID = item.getAsJsonObject().getAsJsonPrimitive("id").getAsString();
                                String mText = item.getAsJsonObject().getAsJsonPrimitive("text").getAsString();
                                Boolean mComplete = item.getAsJsonObject().getAsJsonPrimitive("complete").getAsBoolean();
                                ToDoItem mToDoItem = new ToDoItem();
                                mToDoItem.setId(ID);
                                mToDoItem.setText(mText);
                                mToDoItem.setComplete(mComplete);
                                mAdapter.add(mToDoItem);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
    }

如果方法的名稱與型別程式設計模型中所使用的相同，您即可串連這些方法，以執行篩選、排序和分頁。


##<a name="binding"></a>作法：將資料繫結到使用者介面

資料繫結牽涉到三項要件：

- 資料來源
- 畫面配置
- 將這兩個項目連結在一起的配接器。

在範例程式碼中，我們會將行動服務資料表 *ToDoItem* 中的資料傳回陣列中。這是一個非常常見的資料應用程式模式：資料庫查詢通常會傳回資料列集合，讓用戶端在清單或陣列中取得。在此範例中，陣列是資料來源。

程式碼會指定一個畫面配置，以定義裝置上將會出現的資料檢視。

此外，這兩個項目會透過配接器繫結在一起；在此程式碼中，配接器會是 *ArrayAdapter&lt;ToDoItem&gt;* 類別的擴充功能。

### <a name="layout"></a>作法：定義配置
 
配置可使用數個 XML 程式碼片段來定義。在現有配置下，我們假設下列程式碼會顯示我們要以伺服器資料填入的 **ListView**。

    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>
	

在上述程式碼中，*listitem* 屬性會指定清單中個別資料列的配置 ID。以下提供會指定核取方塊及其相關文字的程式碼。清單中的每個項目會分別使其具現化一次。更複雜的配置將會指定顯示畫面中的其他欄位。此程式碼位於 *row_list_to_do.xml* 檔案中。

	<?xml version="1.0" encoding="utf-8"?>
	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
	    android:layout_width="match_parent"
	    android:layout_height="match_parent"
	    android:orientation="horizontal">		    
	    <CheckBox
	        android:id="@+id/checkToDoItem"
	        android:layout_width="wrap_content"
	        android:layout_height="wrap_content"
	        android:text="@string/checkbox_text" />
	</LinearLayout>
		

### <a name="adapter"></a>作法：定義配接器
	
由於我們的檢視資料來源是 *ToDoItem* 的陣列，因此我們將配接器設為 *ArrayAdapter&lt;ToDoItem&gt;* 類別的子類別。這個子類別會為每個使用 *row_list_to_do* 配置的 *ToDoItem* 產生一個檢視。

我們在程式碼中定義了下列類別，這是 *ArrayAdapter&lt;E&gt;* 類別的擴充功能：

	public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {


您必須覆寫配接器的 *getView* 方法。此範例程式碼是如何執行此動作的其中一個範例：詳細資料會隨著您的應用程式而有所不同。

	public View getView(int position, View convertView, ViewGroup parent) {
		View row = convertView;
	
		final ToDoItem currentItem = getItem(position);
	
		if (row == null) {
			LayoutInflater inflater = ((Activity) mContext).getLayoutInflater();
			row = inflater.inflate(R.layout.row_list_to_do, parent, false);
		}
	
		row.setTag(currentItem);
	
		final CheckBox checkBox = (CheckBox) row.findViewById(R.id.checkToDoItem);
		checkBox.setText(currentItem.getText());
		checkBox.setChecked(false);
		checkBox.setEnabled(true);
	
		return row;
	}

我們在「活動」中建立此類別的執行個體，如下所示：

	ToDoItemAdapter mAdapter;
	mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);

請留意到，ToDoItemAdapter 建構函式的第二個參數是配置的參考。對建構函式的呼叫會在下列程式碼之後執行，而此程式碼會先取得 **ListView** 的參考，然後呼叫 *setAdapter*，以將本身設定成使用我們剛建立的配接器：

	ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
	listViewToDo.setAdapter(mAdapter);


### <a name="use-adapter"></a>作法：使用配接器

您現在已可使用資料繫結。下列程式碼將說明如何取得行動服務資料表中的項目、清除配接器，然後呼叫配接器的 *add* 方法以填入傳回的項目。

    public void showAll(View view) {
        new AsyncTask<Void, Void, Void>() {
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final MobileServiceList<ToDoItem> result = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : result) {
                                mAdapter.add(item);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
    }

您也必須在每次修改過 *ToDoItem* 資料表後呼叫配接器 (如果您要顯示其執行結果)。修改是對個別記錄逐一執行的，因此您將會處理單一資料列，而不是集合。在插入項目時，您會對配接器呼叫 *add* 方法，刪除時則呼叫 *remove* 方法。


##<a name="authentication"></a>作法：驗證使用者

行動服務支援使用各種外部識別提供者 (Facebook、Google、Microsoft 帳戶、Twitter 以及 Azure Active Directory) 來驗證與授權應用程式使用者。您可以在資料表上設定權限，以限制僅有通過驗證使用者可以存取特定操作。您也可以使用經驗證使用者的身分識別，以在後端實作授權規則。如需詳細資訊，請參閱[開始使用驗證](http://go.microsoft.com/fwlink/p/?LinkId=296316) (英文)。

支援兩個驗證流程：伺服器流程和用戶端流程。由於伺服器流程採用提供者的 Web 驗證介面，因此所提供的驗證體驗也最為簡單。用戶端流程可支援裝置特定功能 (例如單一登入) 的深入整合，因為此流程使用的是提供者特定裝置的專用 SDK。

要在您的應用程式中啟用驗證，必須執行三個步驟：

- 對提供者註冊應用程式以進行驗證，並設定行動服務
- 限制只有經驗證的使用者具有資料表的權限
- 將驗證碼新增至您的應用程式


行動服務支援下列可讓您用來驗證使用者的現有身分識別提供者：

- Microsoft 帳戶
- Facebook
- Twitter
- Google 
- Azure Active Directory

您可以設定資料表的權限，以限定只有已驗證的使用者才能存取特定作業。您也可以使用已驗證的使用者 ID 來修改要求。

前兩項工作可使用 [Azure 管理入口網站](https://manage.windowsazure.com/)來完成。如需詳細資訊，請參閱[開始使用驗證](http://go.microsoft.com/fwlink/p/?LinkId=296316) (英文)。

### <a name="caching"></a>作法：將驗證碼新增至您的應用程式

1.  將下列 Import 陳述式新增至應用程式的活動檔案。

		import java.util.concurrent.ExecutionException;
		import java.util.concurrent.atomic.AtomicBoolean;

		import android.content.Context;
		import android.content.SharedPreferences;
		import android.content.SharedPreferences.Editor;

		import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
		import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;

2. 在活動類別的 **onCreate** 方法中，將下列一行程式碼加至可建立 `MobileServiceClient` 物件的程式碼之後︰我們假設 `MobileServiceClient` 物件的參考是 *mClient*。
	
	    // Login using the Google provider.
	    
		ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);

    	Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
    		@Override
    		public void onFailure(Throwable exc) {
    			createAndShowDialog((Exception) exc, "Error");
    		}   		
    		@Override
    		public void onSuccess(MobileServiceUser user) {
    			createAndShowDialog(String.format(
                        "You are now logged in - %1$2s",
                        user.getUserId()), "Success");
    			createTable();	
    		}
    	}); 

    此程式碼會使用 Google 登入來驗證使用者。此時會出現對話方塊，顯示已驗證的使用者 ID。必須通過驗證才能繼續。

    > [AZURE.NOTE]如果您使用的身分識別提供者不是 Google，請將傳給上述 **login** 方法的值變更為下列其中一個：_MicrosoftAccount_、_Facebook_、_Twitter_ 或 _WindowsAzureActiveDirectory_。


3. 當您執行應用程式時，請以您選擇的身分識別提供者登入。


### <a name="caching"></a>作法：快取驗證權杖

本節將說明如何快取驗證權杖。執行這項操作可讓使用者在應用程式「休眠」、但語彙基元仍有效的情況下，無須再次驗證。

您必須將使用者 ID 和驗證語彙基元儲存在本機裝置上，才能快取驗證語彙基元。當應用程式下次啟動時，您只需確認這些值仍存在於快取中，即可略過登入程序，並使用這項資料還原用戶端。但這項資料具有敏感性，因此應加密儲存，以確保在手機失竊的狀況下仍保有安全性。

下列程式碼片段示範如何取得 Microsoft 帳戶登入的權杖系統會快取權杖，並在找到快取時重新載入權杖。

	private void authenticate() {
		if (LoadCache())
		{
			createTable();
		}
		else
		{
			    // Login using the Google provider.    
				ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);
		
		    	Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
		    		@Override
		    		public void onFailure(Throwable exc) {
		    			createAndShowDialog("You must log in. Login Required", "Error");
		    		}   		
		    		@Override
		    		public void onSuccess(MobileServiceUser user) {
		    			createAndShowDialog(String.format(
		                        "You are now logged in - %1$2s",
		                        user.getUserId()), "Success");
		    			cacheUserToken(mClient.getCurrentUser());
		    			createTable();	
		    		}
		    	});		}
	}	


	private boolean LoadCache()
	{
		SharedPreferences prefs = getSharedPreferences("temp", Context.MODE_PRIVATE);
		String tmp1 = prefs.getString("tmp1", "undefined"); 
		if (tmp1 == "undefined")
			return false;
		String tmp2 = prefs.getString("tmp2", "undefined"); 
		if (tmp2 == "undefined")
			return false;
		MobileServiceUser user = new MobileServiceUser(tmp1);
		user.setAuthenticationToken(tmp2);
		mClient.setCurrentUser(user);		
		return true;
	}


	private void cacheUser(MobileServiceUser user)
	{
		SharedPreferences prefs = getSharedPreferences("temp", Context.MODE_PRIVATE);
		Editor editor = prefs.edit();
		editor.putString("tmp1", user.getUserId());
		editor.putString("tmp2", user.getAuthenticationToken());
		editor.commit();
	}


權杖過期將有何影響？ 在此情況下，當您嘗試使用權杖進行連接時，將會出現 *401 未授權*的回應。使用者將必須登入，以取得新權杖。您可以使用篩選器攔截對行動服務的呼叫和行動服務的回應，如此，您即無須在呼叫行動服務的應用程式中逐一撰寫處理此狀況的程式碼。篩選器程式碼將會測試 401 的回應，並視需要觸發登入程序，然後繼續執行產生 401 的要求。


##<a name="customizing"></a>作法：自訂用戶端

有幾種方式可以自訂行動服務用戶端的預設行為。

### <a name="headers"></a>作法：自訂要求標頭

您可能會想要將自訂標頭附加至每個外送要求。您只需依照下列方式設定 **ServiceFilter** 即可：

	private class CustomHeaderFilter implements ServiceFilter {

        @Override
        public ListenableFuture<ServiceFilterResponse> handleRequest(
                	ServiceFilterRequest request, 
					NextServiceFilterCallback next) {

            runOnUiThread(new Runnable() {

                @Override
                public void run() {
	        		request.addHeader("My-Header", "Value");	                }
            });

            SettableFuture<ServiceFilterResponse> result = SettableFuture.create();
            try {
                ServiceFilterResponse response = next.onNext(request).get();
                result.set(response);
            } catch (Exception exc) {
                result.setException(exc);
            }
        }

### <a name="serialization"></a>作法：自訂序列化

根據預設，行動服務會假設伺服器上的資料表名稱、資料行名稱和資料類型，全都會與用戶端上的對應項目完全相符。但實際上卻有各種原因可能導致伺服器與用戶端的名稱不相符。例如，如果您要變更現有的用戶端，使其改用行動服務，而不再使用其他品牌的產品，就會造成名稱不符。

您可能會想要執行下列類型的自訂：

- 行動服務資料表中的資料行名稱不符合您在用戶端中使用的名稱
- 使用名稱與用戶端中的對應類別不同的行動服務資料表
- 開啟自動使用大寫屬性
- 將複雜屬性新增至物件

### <a name="columns"></a>作法：對應不同的用戶端和伺服器名稱

假設您的 Java 用戶端程式碼對 *ToDoItem* 物件屬性使用標準 Java 樣式名稱，如下所示。

- mId
- mText
- mComplete
- mDuration


您必須將用戶端名稱序列化為 JSON 名稱，且這些名稱必須與伺服器上的 *ToDoItem* 資料表的資料行名稱相符。下列使用 <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> 程式庫的程式碼會執行此動作。

	@com.google.gson.annotations.SerializedName("text")
	private String mText;

	@com.google.gson.annotations.SerializedName("id")
	private int mId;

	@com.google.gson.annotations.SerializedName("complete")
	private boolean mComplete;
 
	@com.google.gson.annotations.SerializedName("duration")
	private String mDuration;

### <a name="table"></a>作法：在用戶端與行動服務之間對應不同的資料表名稱

要將用戶端資料表名稱對應至不同的行動服務資料表名稱並不難，只要使用 <a href="http://go.microsoft.com/fwlink/p/?LinkId=296840" target="_blank">getTable()</a> 函數的其中一項覆寫即可，如下列程式碼所示。

	mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);


### <a name="conversions"></a>作法：自動化資料行名稱對應

在上一節中我們了解到，要為一個只有幾個資料行的小型資料表對應資料行名稱，並不困難。但假設我們的資料表有許多資料行，例如 20 或 30 個。此時，我們可以呼叫 <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> API，並指定會套用至每個資料行的轉換策略，而無須為每一個資料行名稱加註。

若要這麼做，我們可以使用 Android 用戶端程式庫在背景用來將 Java 物件序列化為 JSON 資料 (會傳送至 Azure 行動服務) 的 <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> 程式庫。

下列程式碼會使用 *setFieldNamingStrategy()* 方法 (在此方法中可定義 *FieldNamingStrategy()* 方法)。此方法會刪除每個欄位名稱的起始字元 ("m")，然後將下一個字元轉換為小寫。此程式碼也會啟用輸出 JSON 的美化顯示功能。

	client.setGsonBuilder(
	    MobileServiceClient
	    .createMobileServiceGsonBuilder()
	    .setFieldNamingStrategy(new FieldNamingStrategy() {
	        public String translateName(Field field) {
	            String name = field.getName();
	            return Character.toLowerCase(name.charAt(1))
	                + name.substring(2);
	            }
	        })
	        .setPrettyPrinting());
	


此程式碼必須在對行動服務用戶端物件呼叫任何方法之前執行。

### <a name="complex"></a>作法：將物件或陣列屬性儲存在資料表中 

到目前為止，我們的序列化範例所示範的，都是能夠輕易序列化為 JSON 和行動服務資料表的基本類型，例如整數和字串。假設我們想要將複雜的物件新增至用戶端類型，而該物件無法自動序列化為 JSON 和資料表。例如，我們可能想要將字串陣列新增至用戶端物件。現在，要如何執行序列化，以及如何將陣列儲存到行動服務資料表中，全都可由我們指定。

若要檢視執行此作業的範例，請參閱部落格貼文<a href="http://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson" target="_blank">在行動服務 Android 用戶端中使用 <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> 程式庫自訂序列化</a> (英文)。

只要有無法自動序列化為 JSON 和行動服務資料表的複雜物件出現時，我們即可使用此一般方法。

<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #instantiating
[The API structure]: #api
[How to: Query data from a mobile service]: #querying
[Return all Items]: #showAll
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[How to: Concatenate query methods]: #chaining
[How to: Bind data to the user interface]: #binding
[How to: Define the layout]: #layout
[How to: Define the adapter]: #adapter
[How to: Use the adapter]: #use-adapter
[How to: Insert data into a mobile service]: #inserting
[How to: update data in a mobile service]: #updating
[How to: Delete data in a mobile service]: #deleting
[How to: Look up a specific item]: #lookup
[How to: Work with untyped data]: #untyped
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching
[How to: Handle errors]: #errors
[How to: Design unit tests]: #tests
[How to: Customize the client]: #customizing
[Customize request headers]: #headers
[Customize serialization]: #serialization
[Next Steps]: #next-steps
[Setup and Prerequisites]: #setup

<!-- Images. -->



<!-- URLs. -->
[開始使用行動服務]: mobile-services-android-get-started.md
[ASCII 控制碼 C0 和 C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
 

<!---HONumber=July15_HO1-->