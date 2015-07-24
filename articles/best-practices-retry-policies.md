<properties
   pageTitle="NuGet 封裝 | Microsoft Azure"
   description="NuGet 封裝上適用於一般重試原則工作的指引。"
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/09/2015"
   ms.author="masashin"/>

# NuGet 封裝

<p class="lead">當多個元件開始進行通訊時，暫時性失敗對於智慧型處理而言變得更加重要。重試原則 NuGet 封裝會處理暫時性錯誤處理的工作，以協助處理單一執行個體中的重試。</p>

> 這份文件的基礎在於作為概念證明的草稿。它不是實際經過檢閱的指引。

模式與作法 `TransientFaultHandling` 程式碼建議用於一般重試原則工作。

```
Install-Package EnterpriseLibrary.WindowsAzure.TransientFaultHandling
```

## 組態

區段包含重試功能的組態資訊：

參數 | 說明
-------------------- | ----------------------
MaximumExecutionTime | 要求的最大執行時間，包含所有可能的重試嘗試。
ServerTimeOut | 要求的伺服器逾時間隔
RetryPolicy | 重試原則。請參閱下方 [原則] 區段

```csharp
/// <summary>
/// An interface required for request option types.
/// </summary>
public interface IRequestOptions
{
    IRetryPolicy RetryPolicy { get; set; }

    TimeSpan? ServerTimeout { get; set; }

    TimeSpan? MaximumExecutionTime { get; set; }
}
```

程式設計：

- 支援用戶端上設定。
- 啟用覆寫用戶端所提供的作業

組態檔：

```xml
<RetryPolicyConfiguration defaultRetryStrategy="Fixed Interval Retry Strategy">
    <linearInterval name="Fixed Interval Retry Strategy"
	retryInterval="00:00:01" maxRetryCount="10" />
    <exponentialBackoff name="Backoff Retry Strategy" minBackoff="00:00:01"
        maxBackoff="00:00:30" deltaBackoff="00:00:10" maxRetryCount="10"
        fastFirst="false"/>
</RetryPolicyConfiguration>
```

## 原則

### 指數

用於以指數方式區隔服務引動過程的重複嘗試以避免服務節流。

__方法：__

以指數方式增加後續嘗試之間的輪詢間隔。將隨機載入 (+/-20%) 新增至輪詢間隔以避免所有用戶端同步重試

__組態：__

參數 | 說明
-------------------- | -------------------------------------------------------
maxAttempt | 重試嘗試的次數。
deltaBackoff | 重試之間的輪詢間隔。此時間範圍的倍數將用於後續的重試次數。
MinBackoff | 新增從 deltaBackoff 計算的所有重試間隔。
FastFirst | 立即進行第一次重試
MaxBackoff | 如果計算的重試間隔大於 MaxBackoff，則會使用 MaxBackoff。無法變更此值。

__實作邏輯：__

```csharp
if(!ExponentialRetry.FastFirst){
    Random r = new Random();
    double increment = (Math.Pow(2, currentRetryCount) - 1) * r.Next((int)(this.deltaBackoff.TotalMilliseconds * 0.8), (int)(this.deltaBackoff.TotalMilliseconds * 1.2));
    retryInterval = (increment < 0) ? ExponentialRetry.MaxBackoff :
    TimeSpan.FromMilliseconds(Math.Min(ExponentialRetry.MaxBackoff.TotalMilliseconds, ExponentialRetry.MinBackoff.TotalMilliseconds + increment));
} else {
    retryInterval = TimeSpan.Zero;
}
```

## 線性

用於以線性方式區隔服務引動過程的重複嘗試以避免服務節流。

__方法：__

執行指定的重試次數，使用重試之間的指定固定時間間隔。將隨機載入 (+/-20%) 新增至輪詢間隔以避免所有用戶端同步重試。

__組態：__

參數 | 說明
-------------------- | -------------------------------------------------------
maxAttempt | 重試嘗試的次數。
deltaBackoff | 重試之間的輪詢間隔。
FastFirst | 立即進行第一次重試

__實作邏輯：__

```csharp
if(!ExponentialRetry.FastFirst) {
    Random r = new Random();
    retryInterval = TimeSpan.FromMilliseconds(r.Next((int)(
    this.deltaBackoff.TotalMilliseconds * 0.8), (int)(this.deltaBackoff.TotalMilliseconds * 1.2)));
} else {
    retryInterval = TimeSpan.Zero;
}
```

## 調適性

用於根據回應標頭中服務所傳遞的錯誤程式碼/中繼資料，區隔服務引動過程的重複嘗試。

__方法：__

執行指定的重試次數，並使用根據回應標頭中服務所傳遞的錯誤程式碼/中繼資料所計算的輪詢間隔


__組態：__

無法設定

__實作邏輯：__

根據回應標頭中服務所傳遞的錯誤程式碼/中繼資料

__斷路：__

根據[斷路器](http://msdn.microsoft.com/library/dn589784.aspx)

## 擴充性

可以實作以提供自訂重試原則的公用介面

```csharp
public interface IRetryPolicy
{
    /// <summary>
    /// Generates a new retry policy for the current request attempt.
    /// </summary>
    IRetryPolicy CreateInstance();

    /// <summary>
    /// Determines whether the operation should be retried and the interval until the next retry.
    /// </summary>
    /// <param name="currentRetryCount">An integer specifying the number of retries for the given operation. A value of zero signifies this is the first error encountered.</param>
    /// <param name="statusCode">An integer containing the status code for the last operation.</param>
    /// <param name="retryInterval">A <see cref="TimeSpan"/> indicating the interval to wait until the next retry.</param>
    /// <returns><c>true</c> if the operation should be retried; otherwise, <c>false</c>.</returns>
    bool ShouldRetry(int currentRetryCount, int statusCode, out TimeSpan retryInterval);
}
```

## 遙測

使用 EventSource 作為 ETW 事件的記錄重試。以下是應該針對每次重試嘗試記錄的欄位

參數 | 說明
-------------------- | -------------------------------------------------------
requestId | ""
policyType | "RetryExponential"
operation | "取得：https://retry-guidance-tests.servicebus.windows.net/TestQueue/?api-version=2014-05"
operationStartTime | "9/5/2014 10:00:13 PM"
operationEndTime | "9/5/2014 10:00:14 PM"
iteration | "0"
iterationSleep | "00:00:00.1000000"
lastExceptionType | "Microsoft.ServiceBus.Messaging.MessagingCommunicationException"
exceptionMessage | "無法解析遠端名稱：'retry-guidance-tests.servicebus.windows.net'.TrackingId:6a26f99c-dc6d-422e-8565-f89fdd0d4fe3,TimeStamp:9/5/2014 10:00:13 PM"

<!---HONumber=62-->