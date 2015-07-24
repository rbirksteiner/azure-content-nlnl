<properties 
	pageTitle="如何在 Windows 通用上使用 Engagement API" 
	description="如何在 Windows 通用上使用 Engagement API"			
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="07/07/2015" 
	ms.author="piyushjo" />

#如何在 Windows 通用上使用 Engagement API

此文件為[如何在 Windows 通用上整合 Engagement](../mobile-engagement-windows-store-integrate-engagement/) 的附加說明：它提供有關如何使用 Engagement API 來報告應用程式的統計資料之詳細資訊。

請記住，如果您只想要 Engagement 向您報告應用程式的工作階段、活動、當機和技術資訊，那麼最簡單的方法是讓所有 `Page` 子類別繼承自 `EngagementPage` 類別。

如果您想要執行更多工作 (例如，若您需要報告應用程式的特定事件、錯誤和作業，或者您需要以不同於 `EngagementPage` 類別中的方式來報告應用程式的活動)，則您需要使用 Engagement API。

Engagement API 是由 `EngagementAgent` 類別提供。您可以透過 `EngagementAgent.Instance` 取得這些方法。

檔代理程式模組尚未初始化時，每個對 API 的呼叫會被延後，並且將於代理程式可使用時再次執行。

##Engagement 概念

以下部分簡要說明適用於 Windows 通用平台的常見 [Mobile Engagement 概念](../mobile-engagement-concepts/)。

### `Session`和`Activity`

「活動」通常與應用程式的某個頁面關聯，也就是說，「活動」會在頁面顯示時啟動，當頁面關閉時就停止：使用 `EngagementPage` 類別來整合 Engagement SDK 的情況便是如此。

但您也可以透過 Engagement API 手動控制「活動」。這樣可允許將指定的頁面分割成多個部分，以獲得有關該頁面使用方式的詳細資訊 (例如，對話方塊在此頁面的使用平率和使用時間)。

##報告活動

### 使用者啟動新的活動

#### 參考

			void StartActivity(string name, Dictionary<object, object> extras = null)

每當使用者活動變更，您就需要呼叫 `StartActivity()`。第一次呼叫此函式會啟動新的使用者工作階段。

> [AZURE.IMPORTANT]當應用程式關閉時，SDK 會自動呼叫 EndActivity 方法。因此，「強烈」建議每當使用者的活動變更時便叫呼叫 StartActivity 方法，並且「絕對不要」呼叫 EndActivity 方法，因為呼叫此方法會強制結束目前的工作階段。

#### 範例

			EngagementAgent.Instance.StartActivity("main", new Dictionary<object, object>() {{"example", "data"}});

### 使用者結束其目前的活動

#### 參考

			void EndActivity()

這樣會結束活動和工作階段。除非您真的清楚您的目的，否則不應該呼叫這個方法。

#### 範例

			EngagementAgent.Instance.EndActivity();

##報告作業

### 啟動作業

#### 參考

			void StartJob(string name, Dictionary<object, object> extras = null)

您可以使用作業在一段時間內追蹤某些工作。

#### 範例

			// An upload begins...
			
			// Set the extras
			var extras = new Dictionary<object, object>();
			extras.Add("title", "avatar");
			extras.Add("type", "image");
			
			EngagementAgent.Instance.StartJob("uploadData", extras);

### 結束作業

#### 參考

			void EndJob(string name)

一旦作業追蹤的工作被終止，您就應該針對該作業呼叫 EndJob 方法 (透過提供該作業名稱)。

#### 範例

			// In the previous section, we started an upload tracking with a job
			// Then, the upload ends
			
			EngagementAgent.Instance.EndJob("uploadData");

##報告事件

事件有三種類型：

-   獨立事件
-   工作階段事件
-   作業事件

### 獨立事件

#### 參考

			void SendEvent(string name, Dictionary<object, object> extras = null)

獨立事件可以出現在工作階段的內容之外。

#### 範例

			EngagementAgent.Instance.SendEvent("event", extra);

### 工作階段事件

#### 參考

			void SendSessionEvent(string name, Dictionary<object, object> extras = null)

工作階段事件通常用來報告在其工作階段期間由使用者所執行的動作。

#### 範例

**沒有資料：**

			EngagementAgent.Instance.SendSessionEvent("sessionEvent");
			
			// or
			
			EngagementAgent.Instance.SendSessionEvent("sessionEvent", null);

**有資料：**

			Dictionary<object, object> extras = new Dictionary<object,object>();
			extras.Add("name", "data");
			EngagementAgent.Instance.SendSessionEvent("sessionEvent", extras);

### 作業事件

#### 參考

			void SendJobEvent(string eventName, string jobName, Dictionary<object, object> extras = null)

作業事件通常用來報告在作業期間由使用者所執行的動作。

#### 範例

			EngagementAgent.Instance.SendJobEvent("eventName", "jobName", extras);

##報告錯誤

錯誤有三種類型：

-   獨立錯誤
-   工作階段錯誤
-   作業錯誤

### 獨立錯誤

#### 參考

			void SendError(string name, Dictionary<object, object> extras = null)

不同於工作階段錯誤，獨立錯誤可以出現在工作階段的內容之外。

#### 範例

			EngagementAgent.Instance.SendError("errorName", extras);

### 工作階段錯誤

#### 參考

			void SendSessionError(string name, Dictionary<object, object> extras = null)

工作階段錯誤通常用來報告在其工作階段期間影響使用者的錯誤。

#### 範例

			EngagementAgent.Instance.SendSessionError("errorName", extra);

### 作業錯誤

#### 參考

			void SendJobError(string errorName, string jobName, Dictionary<object, object> extras = null)

錯誤可能與正在執行的工作關聯，而不是與目前的使用者工作階段關聯。

#### 範例

			EngagementAgent.Instance.SendJobError("errorName", "jobname", extra);

##報告當機

代理程式提供兩種處理當機的方法。

### 傳送例外狀況

#### 參考

			void SendCrash(Exception e, bool terminateSession = false)

#### 範例

透過呼叫下列函式您可以隨時傳送例外狀況：

			EngagementAgent.Instance.SendCrash(aCatchedException);

您也可以使用選擇性的參數來同時結束參與工作階段並傳送當機。若要這樣做，請呼叫：

			EngagementAgent.Instance.SendCrash(new Exception("example"), terminateSession: true);

如果您這樣做，工作階段和作業將於傳送當機後立即關閉。

### 傳送未處理的例外狀況

#### 參考

			void SendCrash(Exception e)

如果您「已經停用」Engagement 自動當機報告，Engagement 也會提供傳送未處理例外狀況的方法。在應用程式的 UnhandledException 事件處理常式內此方法特別有用。

此方法「一律」會在被呼叫之後終止 Engagement 工作階段和作業。

#### 範例

您可以使用它來實作您自己的 UnhandledExceptionEventArgs 處理常式。例如，新增 `App.xaml.cs` 檔案的 `Current_UnhandledException` 方法：

			// In your App.xaml.cs file
			
			// Code to execute on Unhandled Exceptions
			void Current_UnhandledException(object sender, UnhandledExceptionEventArgs e)
			{
			   EngagementAgent.Instance.SendCrash(e.Exception,false);
			}

在 App.xaml.cs 中的 "Public App(){}" 新增：

			Application.Current.UnhandledException += Current_UnhandledException;

##裝置識別碼

			String EngagementAgent.Instance.GetDeviceId()

您可以藉由呼叫這個方法來取得 Egagement 的裝置識別碼。

##額外的參數

可以附加任意資料到事件、錯誤、活動或作業。可以使用字典來結構化這些資料。索引鍵和值可以是任何型別。

額外的資料已經序列化，因此如果您想要在額外資料中插入您自己的型別，您必須針對此型別新增資料合約。

### 範例

我們建立一個新的類別叫 "Person"。

			using System.Runtime.Serialization;
			
			namespace Microsoft.Azure.Engagement
			{
			  [DataContract]
			  public class Person
			  {
			    public Person(string name, int age)
			    {
			      Age = age;
			      Name = name;
			    }
			
			    // Properties
			
			    [DataMember]
			    public int Age
			    {
			      get;
			      set;
			    }
			
			    [DataMember]
			    public string Name
			    {
			      get;
			      set; 
			    }
			  }
			}

然後將 `Person` 執行個體新增至額外資料。

			Person person = new Person("Engagement Haddock", 51);
			var extras = new Dictionary<object, object>();
			extras.Add("people", person);
			
			EngagementAgent.Instance.SendEvent("Event", extras);

> [AZURE.WARNING]如果您新增其他類型的物件，請確定已實作它們的 ToString() 方法，以傳回使用者可閱讀的字串。

### 限制

#### 之間的信任

物件中的每個索引鍵都必須符合下列規則運算式：

`^[a-zA-Z][a-zA-Z_0-9]*$`

這表示索引鍵必須至少以一個字母開頭，後面連接字母、數字或底線 (_)。

#### 大小

每次呼叫的額外資料限制為 **1024** 個字元。

##報告應用程式資訊

### 參考

			void SendAppInfo(Dictionary<object, object> appInfos)

您可以使用 SendAppInfo() 函式來報告追蹤資訊 (或任何其他應用程式相關的資訊)。

請注意，這些資訊可以累加地傳送：只有指定的索引鍵的最新值會保留給指定的裝置。和事件額外資料一樣，請使用 Dictionary<object, object> 來附加資訊。

### 範例

			Dictionary<object, object> appInfo = new Dictionary<object, object>()
			  {
			    {"birthdate", "1983-12-07"},
			    {"gender", "female"}
			  };
			
			EngagementAgent.Instance.SendAppInfo(appInfo);

### 限制

#### 之間的信任

物件中的每個索引鍵都必須符合下列規則運算式：

`^[a-zA-Z][a-zA-Z_0-9]*$`

這表示索引鍵必須至少以一個字母開頭，後面連接字母、數字或底線 (_)。

#### 大小

每次呼叫的應用程式資訊限制為 **1024** 個字元。

在上述範例中，傳送到伺服器的 JSON 會是 44 個字元：

			{"birthdate":"1983-12-07","gender":"female"}
 

<!---HONumber=July15_HO2-->