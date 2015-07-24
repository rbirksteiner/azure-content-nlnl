<properties 
	pageTitle="如何使用 Xamarin 的 Blob 儲存體 (預覽) | Microsoft Azure" 
	description="適用於 Xamarin 的 Azure 儲存體用戶端程式庫預覽可讓開發人員使用其原生的使用者介面以建立 iOS、Android 和 Windows 市集應用程式。本教學課程會示範如何使用 Xamarin 以建立 Android 應用程式，該應用程式使用 Azure Blob 儲存體。" 
	services="storage" 
	documentationCenter="xamarin" 
	authors="tamram" 
	manager="carolz" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/15/2015" 
	ms.author="tamram"/>

# 如何使用 Xamarin 的 Blob 儲存體 (預覽)

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## 概觀

Xamarin 可讓開發人員使用共用的 C# 程式碼基底，使用其原生的使用者介面以建立 iOS、Android 和 Windows 市集應用程式。適用於 Xamarin 的 Azure 儲存體用戶端程式庫是預覽程式庫。請注意，它可能會在未來變更。

本教學課程會示範如何搭配 Xamarin Android 應用程式使用 Azure Blob 儲存體。若要在一頭栽進程式碼前了解 Azure 儲存體，請參閱本文件結尾的[後續步驟](#next-steps)。

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## 產生共用存取簽章

在使用適用於 Xamarin 的 Azure 儲存體用戶端程式庫進行開發時，您無法進行使用您的帳戶存取金鑰驗證 Azure 儲存體帳戶的存取權。這是為了防止將您的帳戶認證散發給可能會下載您的應用程式的使用者。相反地，我們鼓勵使用共用存取簽章 (SAS)，這不會公開您的帳戶認證。

在本指南中，我們將使用 Azure PowerShell 來產生 SAS 權杖。接著我們會建立 Xamarin 應用程式，該應用程式會使用產生的 SAS。

首先，您必須安裝 Azure PowerShell。如需指示，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md#Install)。

接著，開啟 Azure PowerShell 並執行下列命令。請記得將 `ACCOUNT_NAME` 和 `ACCOUNT_KEY== ` 取代為您的儲存體帳戶認證。將 `CONTAINER_NAME` 取代為您選擇的名稱。

    PS C:> $context = New-AzureStorageContext -StorageAccountName "ACCOUNT_NAME" -StorageAccountKey "ACCOUNT_KEY=="
	PS C:> New-AzureStorageContainer CONTAINER_NAME -Permission Off -Context $context
	PS C:> $now = Get-Date
	PS C:> New-AzureStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri

新容器的共用存取簽章 URI 應該會類似下列項目：

	https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2013-04-13T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss

您在容器上建立的共用存取簽章將在隔天內有效。簽章會授與容器內 Blob 的完整存取權限 (例如，讀取、寫入、刪除和清單)。

如需共用存取簽章的詳細資訊，請參閱[適用於 .NET 的 SAS 教學課程](storage-dotnet-shared-access-signature-part-2.md)。

## 建立新的 Xamarin 應用程式

對於本教學課程，我們會在 Visual Studio 中建立我們的 Xamarin 應用程式。請遵循下列步驟來建立應用程式：

1. 下載並安裝 [Visual Studio](https://www.visualstudio.com/)。
2. 下載並安裝 [Xamarin](http://xamarin.com/platform)。
3. 開啟 Visual Studio，然後選取 [**檔案 > 新增 > 專案 > Android > 空白應用程式 (Android)**]。
4. 以滑鼠右鍵按一下 [方案總管] 窗格中的專案，然後選取 [**管理 NuGet 封裝**]。然後搜尋 [**Azure 儲存體**] 並安裝 [**Azure 儲存體 4.4.0-preview**]。

您現在應該有應用程式，可讓您按一下按鈕，並遞增計數器。

## 使用共用存取簽章以執行容器作業

接下來，加入程式碼以使用您所產生的 SAS URI 來執行一系列的容器作業。

首先加入下列 **using** 陳述式：

	using System.IO;
	using System.Text;
	using System.Threading.Tasks;
	using Microsoft.WindowsAzure.Storage.Blob;


接著，對 SAS 權杖新增程式碼行。將 `"SAS_URI"` 字串取代為您在 Azure PowerShell 中產生的 SAS URI。然後對我們會在以下建立的 `UseContainerSAS` 方法的呼叫加入程式碼行。請注意，已在委派之前加入**非同步**關鍵字。


	public class MainActivity : Activity
	{
    	int count = 1;
    	string sas = "SAS_URI";
    	protected override void OnCreate(Bundle bundle)
    	{
        	base.OnCreate(bundle);

        	// Set our view from the "main" layout resource
        	SetContentView(Resource.Layout.Main);

        	// Get our button from the layout resource, and attach an event to it
        	Button button = FindViewById<Button>(Resource.Id.MyButton);

        	button.Click += async delegate	{
             	button.Text = string.Format("{0} clicks!", count++);
             	await UseContainerSAS(sas);
         	};
     }

在 `OnCreate` 方法底下加入新的方法 `UseContainerSAS`。

	static async Task UseContainerSAS(string sas)
	{
    	//Try performing container operations with the SAS provided.

    	//Return a reference to the container using the SAS URI.
    	CloudBlobContainer container = new CloudBlobContainer(new Uri(sas));
    	string date = DateTime.Now.ToString();
    	try
    	{
        	//Write operation: write a new blob to the container.
        	CloudBlockBlob blob = container.GetBlockBlobReference("sasblob_" + date + ".txt");

        	string blobContent = "This blob was created with a shared access signature granting write permissions to the container. ";
        	MemoryStream msWrite = new
        	MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        	msWrite.Position = 0;
        	using (msWrite)
         	{
             	await blob.UploadFromStreamAsync(msWrite);
         	}
         	Console.WriteLine("Write operation succeeded for SAS " + sas);
         	Console.WriteLine();
     	}
     	catch (Exception e)
     	{
        	Console.WriteLine("Write operation failed for SAS " + sas);
        	Console.WriteLine("Additional error information: " + e.Message);
        	Console.WriteLine();
     	}
     	try
     	{
        	//Read operation: Get a reference to one of the blobs in the container and read it.
        	CloudBlockBlob blob = container.GetBlockBlobReference("sasblob_” + date + “.txt");
        	string data = await blob.DownloadTextAsync();

        	Console.WriteLine("Read operation succeeded for SAS " + sas);
        	Console.WriteLine("Blob contents: " + data);
     	}
     	catch (Exception e)
     	{
        	Console.WriteLine("Additional error information: " + e.Message);
       		Console.WriteLine("Read operation failed for SAS " + sas);
        	Console.WriteLine();
     	}
     	Console.WriteLine();
     	try
     	{
        	//Delete operation: Delete a blob in the container.
         	CloudBlockBlob blob = container.GetBlockBlobReference("sasblob_” + date + “.txt");
         	await blob.DeleteAsync();

         	Console.WriteLine("Delete operation succeeded for SAS " + sas);
         	Console.WriteLine();
     	}
     	catch (Exception e)
     	{
        	Console.WriteLine("Delete operation failed for SAS " + sas);
        	Console.WriteLine("Additional error information: " + e.Message);
        	Console.WriteLine();
     	}
	}

## 執行應用程式

您現在可以在模擬器或 Android 裝置中執行這個應用程式。

雖然此快速入門著重於 Android，您也可以在 iOS 或 Windows 市集應用程式中使用 `UseContainerSAS` 方法。Xamarin 也可讓開發人員建立 Windows Phone 應用程式；不過，我們的程式庫尚未支援。

## 後續步驟

在本教學課程中，您會學習如何搭配 Xamarin 應用程式使用 Azure Blob 儲存體和 SAS。在進一步的練習中，會套用類似的模式，以產生 Azure 資料表或佇列的 SAS 權杖。

參閱下列連結以深入了解 Blob、資料表和佇列：

[Microsoft Azure 儲存體簡介](storage-introduction.md)  
[如何使用 .NET 的 Blob 儲存體](storage-dotnet-how-to-use-blobs.md)  
[如何使用 .NET 的資料表儲存體](storage-dotnet-how-to-use-tables.md)  
[如何使用 .NET 的佇列儲存體](storage-dotnet-how-to-use-queues.md)
 

<!---HONumber=July15_HO2-->