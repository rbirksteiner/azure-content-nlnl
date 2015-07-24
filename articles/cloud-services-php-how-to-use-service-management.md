<properties
	pageTitle="如何使用 Azure 服務管理 API (PHP)"
	description="了解如何使用 Azure PHP 服務管理 API 來管理雲端服務和其他 Azure 應用程式。"
	services="web-sites"
	documentationCenter="php"
	authors="tfitzmac"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="PHP"
	ms.topic="article"
	ms.date="11/17/2014"
	ms.author="tomfitz"/>

# 如何從 PHP 使用服務管理

本指南將示範如何從 PHP 透過程式設計方式執行一般服務管理工作。[Azure SDK for PHP] 中的 [ServiceManagementRestProxy][download-SDK-PHP] 類別支援透過程式設計方式存取[管理入口網站][management-portal]中可用的最多服務管理相關功能 (例如**建立、更新以及刪除雲端服務、部署、儲存體服務和同質群組**)。這項功能在建置需要以程式設計方式存取服務管理的應用程式時，將有所幫助。

## 什麼是服務管理
管理服務 API 可讓使用者以程式設計方式存取[管理入口網站][management-portal]所提供的多種服務管理功能。Azure SDK for PHP 可讓您管理雲端服務、儲存體帳戶和同質群組。

若要使用服務管理 API，您必須[建立 Azure 帳號][win-azure-account]。

## 概念
Azure SDK for PHP 會包裝 [Azure 服務管理 API][svc-mgmt-rest-api] (其為 REST API)。所有 API 作業都是透過 SSL 所執行，而且使用 X.509 v3 憑證進行互相驗證。管理服務可從執行於 Azure 的服務內存取，或直接透過網際網路，從任何可傳送 HTTPS 要求和接收 HTTPS 回應的應用程式存取。

## 建立 PHP 應用程式

若要建立 PHP 應用程式並使其使用 Azure 服務管理，唯一要求就是在您的程式碼中參考 Azure SDK for PHP 中的類別。您可以使用任何開發工具來建立應用程式 (包括 [記事本])。

在本指南中，您將使用可從 PHP 應用程式內本機呼叫的服務功能，或可在 Azure Web 角色、背景工作角色或網站內執行的程式碼中呼叫的服務功能。

## 取得 Azure 用戶端程式庫

[AZURE.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

## 作法：連線到服務管理

若要連線到服務管理端點，您需要有 Azure 訂閱 ID 和有效管理憑證的路徑。您可以透過[管理入口網站][management-portal]取得訂閱 ID，也可以使用數種方式來建立管理憑證。在本指南中，使用 [OpenSSL](http://www.openssl.org/)，其可從 [OpenSSL for Windows](http://www.openssl.org/related/binaries.html) 下載並在主控台中執行。

實際上您需要建立兩個憑證，一個用於伺服器 (`.cer` 檔案)，一個用於用戶端 (`.pem` 檔案)。若要建立 `.pem` 檔案，請執行下列命令：

	`openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem`

若要建立 `.cer` 憑證，請執行下列命令：

	`openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer`

如需 Azure 憑證的詳細資訊，請參閱 [Azure 憑證的概觀](http://msdn.microsoft.com/library/azure/gg981929.aspx)。如需 OpenSSL 參數的完整說明，請參閱 [http://www.openssl.org/docs/apps/openssl.html](http://www.openssl.org/docs/apps/openssl.html) 上的文件。

如果您已使用 [Azure 命令列介面][Azure CLI]下載並匯入發佈設定檔案，則可使用工具建立的 `.pem` 檔案，而不是建立您自己的檔案。工具會建立 `.cer` 並將它上傳至 Azure，而且它們會將對應的 `.pem` 檔案放到電腦的 `.azure` 目錄 (位於您的 user 目錄) 中。

建立這些檔案之後，需要透過[管理入口網站][management-portal]將 `.cer` 檔案上傳至 Azure，而且需要記下 `.pem` 檔案的儲存位置。

取得訂閱識別碼、建立憑證並將 `.cer` 檔案上傳至 Azure 之後，就可以建立連接字串並將它傳送至 **ServicesBuilder** 類別上的 **createServiceManagementService** 方法，以連接到 Azure 管理端點：

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;

	$conn_string = "SubscriptionID=<your_subscription_id>;CertificatePath=<path_to_.pem_certificate>";

	$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);

在上述範例中，`$serviceManagementRestProxy` 是 [ServiceManagementRestProxy] 物件。**ServiceManagementRestProxy** 類別是用來管理 Azure 服務的主要類別。

## 作法：列出可用位置

若要列出可用於裝載服務的位置，請使用 **ServiceManagementRestProxy->listLocations** 方法：

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	try{
		$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);

		$result = $serviceManagementRestProxy->listLocations();

		$locations = $result->getLocations();

		foreach($locations as $location){
		      echo $location->getName()."<br />";
		}
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/library/windowsazure/ee460801
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

在建立雲端服務、儲存服務或親和性群組時，您必須提供有效位置。**listLocations** 方法一律會傳回最新的目前可用位置清單。撰寫這份文件時，可用位置如下：

- 美國任何地方
- 歐洲任何地方
- 西歐
- 亞洲任何地方
- 東南亞
- 東亞
- 美國中北部
- 北歐
- 美國中南部
- 美國西部
- 美國東部

在下面的程式碼範例中，以字串形式將位置傳遞給方法。不過，您也可以使用 <code>WindowsAzure\ServiceManagement\Models\Locations</code> 類別，以列舉形式傳遞位置。例如，您可以傳遞 <code>Locations::WEST_US</code>，而不是將「美國西部」傳遞給接受位置的方法。

## 作法：建立雲端服務

當您建立應用程式並在 Azure 中執行它時，其程式碼和組態併稱為 Azure [雲端服務] (在舊版 Azure 中，稱為*託管服務* )。**createHostedServices** 方法可讓您透過提供託管服務名稱 (在 Azure 中必須是唯一的)、標籤 (Base 64 編碼託管服務名稱) 和 **CreateServiceOptions** 物件，來建立新的託管服務。[CreateServiceOptions] 物件可讓您設定服務的位置「或」同質群組。

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\ServiceManagement\Models\CreateServiceOptions;
	use WindowsAzure\Common\ServiceException;

	try{
		// Create REST proxy.
		$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);

        $name = "myhostedservice";
        $label = base64_encode($name);
        $options = new CreateServiceOptions();
        $options->setLocation('West US');
		// Instead of setLocation, you can use setAffinityGroup
		// to set an affinity group.

        $result = $serviceManagementRestProxy->createHostedService($name, $label, $options);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/library/windowsazure/ee460801
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

您可以使用 **listHostedServices** 方法列出訂閱的所有託管服務，而此方法傳回 [ListHostedServicesResult] 物件。呼叫 **getHostedServices** 方法則可讓您重複執行 [HostedServices] 物件的陣列，並擷取服務屬性：

	$listHostedServicesResult = $serviceManagementRestProxy->listHostedServices();

	$hosted_services = $listHostedServicesResult->getHostedServices();

	foreach($hosted_services as $hosted_service){
		echo "Service name: ".$hosted_service->getName()."<br />";
		echo "Management URL: ".$hosted_service->getUrl()."<br />";
		echo "Affinity group: ".$hosted_service->getAffinityGroup()."<br />";
		echo "Location: ".$hosted_service->getLocation()."<br />";
		echo "------<br />";
		}

如果您想要取得特定託管服務的相關資訊，則可以將託管服務名稱傳遞給 **getHostedServiceProperties** 方法：

	$getHostedServicePropertiesResult = $serviceManagementRestProxy->getHostedServiceProperties("myhostedservice");

	$hosted_service = $getHostedServicePropertiesResult->getHostedService();

	echo "Service name: ".$hosted_service->getName()."<br />";
	echo "Management URL: ".$hosted_service->getUrl()."<br />";
	echo "Affinity group: ".$hosted_service->getAffinityGroup()."<br />";
	echo "Location: ".$hosted_service->getLocation()."<br />";

在您建立雲端服務之後，就可以使用 [createDeployment](#CreateDeployment) 方法將程式碼部署至服務。

##<a id="DeleteCloudService"></a>作法：刪除雲端服務

您可以將服務名稱傳遞給 **deleteHostedService** 方法，以刪除雲端服務：

	$serviceManagementRestProxy->deleteHostedService("myhostedservice");

請注意，必須先刪除服務的所有部署，再刪除服務(請參閱[作法：刪除部署](#DeleteDeployment)，以取得詳細資料)。

## 作法：建立部署

**createDeployment** 方法會上傳新的[服務套件]，並在預備或生產環境中建立新的部署。此方法的參數如下：

* **$name**：託管服務的名稱。
* **$deploymentName**：部署的名稱。
* **$slot**：指出預備或生產位置的列舉。
* **$packageUrl**：部署封裝 (.cspgk 檔案) 的 URL。封裝檔必須在 Azure Blob 儲存帳號中，儲存於與封裝要上傳到的代管服務相同的訂閱下。您可以使用 [Azure PowerShell Cmdlet] 或使用 [cspack 命令列工具]來建立部署套件。
* **$configuration**：服務組態檔 (.cscfg 檔案)。
* **$label**：Base 64 編碼託管服務名稱。

下列範例會在名為 `myhostedservice` 之託管服務的生產位置中建立新部署：


	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\ServiceManagement\Models\DeploymentSlot;
	use WindowsAzure\Common\ServiceException;

	try{
		// Create REST proxy.
		$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);

        $name = "myhostedservice";
		$deploymentName = "v1";
        $slot = DeploymentSlot::PRODUCTION;
		$packageUrl = "URL_for_.cspkg_file";
		$configuration = base64_encode(file_get_contents('path_to_.cscfg_file'));
		$label = base64_encode($name);

        $result = $serviceManagementRestProxy->createDeployment($name,
														 $deploymentName,
														 $slot,
														 $packageUrl,
														 $configuration,
														 $label);

		$status = $serviceManagementRestProxy->getOperationStatus($result);
		echo "Operation status: ".$status->getStatus()."<br />";
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/library/windowsazure/ee460801
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

請注意，在上面的範例中，將 **createDeployment** 所傳回的結果傳遞給 **getOperationStatus** 方法，以擷取 **createDeployment** 作業的狀態。

您可以使用 **getDeployment** 方法來存取部署屬性。下列範例透過在 [GetDeploymentOptions] 物件中指定部署位置來擷取部署，但是您可以改為指定部署名稱。此範例也會逐一查看要部署的所有執行個體：

	$options = new GetDeploymentOptions();
	$options->setSlot(DeploymentSlot::PRODUCTION);

	$getDeploymentResult = $serviceManagementRestProxy->getDeployment("myhostedservice", $options);
	$deployment = $getDeploymentResult->getDeployment();

	echo "Name: ".$deployment->getName()."<br />";
	echo "Slot: ".$deployment->getSlot()."<br />";
	echo "Private ID: ".$deployment->getPrivateId()."<br />";
	echo "Status: ".$deployment->getStatus()."<br />";
	echo "Instances: <br />";
	foreach($deployment->getroleInstanceList() as $roleInstance){
		echo "Instance name: ".$roleInstance->getInstanceName()."<br />";
		echo "Instance status: ".$roleInstance->getInstanceStatus()."<br />";
		echo "Instance size: ".$roleInstance->getInstanceSize()."<br />";
	}
	echo "------<br />";

## 作法：更新部署

使用 **changeDeploymentConfiguration** 方法或 **updateDeploymentStatus** 方法，即可更新部署。

**changeDeploymentConfiguration** 方法可讓您上傳新的服務組態檔 (`.cscfg`)，這會變更數個服務設定的任何一個 (包括部署中的執行個體數目)。如需詳細資訊，請參閱 [Azure 服務組態結構描述 (.cscfg 檔)]。下列範例說明如何上傳新的服務組態檔：

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\ServiceManagement\Models\ChangeDeploymentConfigurationOptions;
	use WindowsAzure\ServiceManagement\Models\DeploymentSlot;
	use WindowsAzure\Common\ServiceException;

	try{
		// Create REST proxy.
		$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);

        $name = "myhostedservice";
		$configuration = base64_encode(file_get_contents('path to .cscfg file'));
		$options = new ChangeDeploymentConfigurationOptions();
		$options->setSlot(DeploymentSlot::PRODUCTION);

        $result = $serviceManagementRestProxy->changeDeploymentConfiguration($name, $configuration, $options);

		$status = $serviceManagementRestProxy->getOperationStatus($result);
		echo "Operation status: ".$status->getStatus()."<br />";
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/library/windowsazure/ee460801
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

請注意，在上面的範例中，將 **changeDeploymentConfiguration** 所傳回的結果傳遞給 **getOperationStatus** 方法，以擷取 **changeDeploymentConfiguration** 作業的狀態。

**updateDeploymentStatus** 方法可讓您將部署狀態設定為 RUNNING 或 SUSPENDED。下列範例示範如何在名為 `myhostedservice` 之託管服務的生產位置中，將部署的狀態設定為 RUNNING：

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\ServiceManagement\Models\DeploymentStatus;
	use WindowsAzure\ServiceManagement\Models\DeploymentSlot;
	use WindowsAzure\ServiceManagement\Models\GetDeploymentOptions;
	use WindowsAzure\Common\ServiceException;

	try{
		// Create REST proxy.
		$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);

		$options = new GetDeploymentOptions();
		$options->setSlot(DeploymentSlot::PRODUCTION);

        $result = $serviceManagementRestProxy->updateDeploymentStatus("myhostedservice", DeploymentStatus::RUNNING, $options);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/library/windowsazure/ee460801
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

## 作法：在執行與生產環境之間移動部署

Azure 提供兩個部署環境：預備和生產。一般而言，將服務部署至生產環境之前，會先將其部署至執行環境中進行測試。等到要將服務從執行環境升級至生產環境時，您就無須再重新部署服務。您可以交換部署來完成操作(如需交換部署的詳細資訊，請參閱[在 Azure 中管理部署概觀])。

下列範例示範如何使用 **swapDeployment** 方法來交換兩個部署 (部署名稱為 `v1` 和 `v2`)。在此範例中，呼叫 **swapDeployment** 之前，部署 `v1` 位於生產位置中，而部署 `v2` 位於預備位置中。呼叫 **swapDeployment** 之後，`v2` 位於生產位置中，而 `v1` 位於預備位置中。

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	try{
		// Create REST proxy.
		$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);

		$result = $serviceManagementRestProxy->swapDeployment("myhostedservice", "v2", "v1");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/library/windowsazure/ee460801
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

## 作法：刪除部署

若要刪除部署，請使用 **deleteDeployment** 方法。下列範例顯示如何在 **GetDeploymentOptions** 物件上使用 [setSlot] 方法，然後將它傳遞給 **deleteDeployment**，以刪除預備環境中的部署。您可以在 [GetDepolymentOptions] 類別上使用 **setName** 方法，以依部署名稱來指定部署，而不是依位置來指定部署。

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\ServiceManagement\Models\GetDeploymentOptions;
	use WindowsAzure\ServiceManagement\Models\DeploymentSlot;
	use WindowsAzure\Common\ServiceException;

	try{
		// Create REST proxy.
		$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);

		$options = new GetDeploymentOptions();
		$options->setSlot(DeploymentSlot::STAGING);

		$result = $serviceManagementRestProxy->deleteDeployment("myhostedservice", $options);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/library/windowsazure/ee460801
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

## 作法：建立儲存體服務

[儲存體服務]可讓您存取 Azure [Blob][azure-blobs]、[資料表][azure-tables]和[佇列][azure-queues]。若要建立儲存體服務，您需要服務的名稱 (3 到 24 個小寫字元，而且在 Azure 內是唯一的)、標籤 (服務的 Base 64 編碼名稱，最多 100 個字元)，以及位置或同質群組。提供服務的說明是選用作業。位置、同質群組和說明設定於 [CreateServiceOptions] 物件中，這會傳遞給 **createStorageService** 方法。下列範例顯示如何指定位置來建立儲存體服務。如果您想要使用同質群組，則需要先建立同質群組 (請參閱[作法：建立同質群組](#CreateAffinityGroup))，並使用 **CreateServiceOptions->setAffinityGroup** 方法加以設定。

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\ServiceManagement\Models\CreateServiceOptions;
	use WindowsAzure\Common\ServiceException;


	try{
		// Create REST proxy.
		$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);

        $name = "mystorageaccount";
        $label = base64_encode($name);
        $options = new CreateServiceOptions();
        $options->setLocation('West US');
		$options->setDescription("My storage account description.");

        $result = $serviceManagementRestProxy->createStorageService($name, $label, $options);

		$status = $serviceManagementRestProxy->getOperationStatus($result);
		echo "Operation status: ".$status->getStatus()."<br />";
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/library/windowsazure/ee460801
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

請注意，在上面的範例中，將 **createStorageService** 所傳回的結果傳遞給 **getOperationStatus** 方法，以擷取 **createStorageService** 作業的狀態。

您可以使用 **listStorageServices** 方法列出儲存體帳戶和其屬性：

	// Create REST proxy.
	$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
	$getStorageServicesResult = $serviceManagementRestProxy->listStorageServices();
	$storageServices = $getStorageServicesResult->getStorageServices();

	foreach($storageServices as $storageService){
		echo "Service name: ".$storageService->getName()."<br />";
		echo "Service URL: ".$storageService->getUrl()."<br />";
		echo "Affinity group: ".$storageService->getAffinityGroup()."<br />";
		echo "Location: ".$storageService->getLocation()."<br />";
		echo "------<br />";
	}

## 作法：刪除儲存體服務

您可以將儲存體服務名稱傳遞給 **deleteStorageService** 方法，以刪除儲存體服務。刪除儲存服務時，將同時刪除該服務中儲存的所有資料 (Blob、資料表和佇列)。

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	try{
		// Create REST proxy.
		$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);

		$serviceManagementRestProxy->deleteStorageService("mystorageservice");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/library/windowsazure/ee460801
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

## 作法：建立同質群組

親和性群組是 Azure 服務的邏輯群組功能，可指示 Azure 正確定位服務以達最佳化效能。例如，您可以在「美國西部」位置中建立同質群組，然後在此同質群組中建立[雲端服務](#CreateCloudService)。如果您後續又在同一個同質群組中建立儲存體服務，Azure 就知道要將其放在「美國西部」位置中，並在資料中心內進行最佳化，使同一個同質群組中的雲端服務能夠達到最佳效能。

若要建立同質群組，您需要名稱、標籤 (Base 64 編碼名稱) 和位置。您可以選擇性地提供描述：

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\ServiceManagement\Models\CreateAffinityGroupOptions;
	use WindowsAzure\Common\ServiceException;

	try{
		// Create REST proxy.
		$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);

        $name = "myAffinityGroup";
        $label = base64_encode($name);
        $location = "West US";

        $options = new CreateAffinityGroupOptions();
		$options->setDescription = "My affinity group description.";

        $serviceManagementRestProxy->createAffinityGroup($name, $label, $location, $options);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/library/windowsazure/ee460801
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

建立親和性群組後，您在[建立儲存服務](#CreateStorageService)時將可指定群組 (而非位置)。

呼叫 **listAffinityGroups** 方法，然後在 [AffinityGroup] 類別上呼叫適當的方法，就可以列出同質群組並檢查其屬性：

	$result = $serviceManagementRestProxy->listAffinityGroups();

	$groups = $result->getAffinityGroups();

	foreach($groups as $group){
		echo "Name: ".$group->getName()."<br />";
		echo "Description: ".$group->getDescription()."<br />";
		echo "Location: ".$group->getLocation()."<br />";
		echo "------<br />";
	}

## 作法：刪除同質群組

您可以將群組名稱傳遞給 **deleteAffinityGroup** 方法，以刪除同質群組。請注意，必須先中斷同質群組與任何服務的關聯 (或必須刪除使用同質群組的服務)，再刪除同質群組。

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	try{
		// Create REST proxy.
		$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);

		// An affinity group must be disassociated from all services
		// before it can be deleted.
		$serviceManagementRestProxy->deleteAffinityGroup("myAffinityGroup");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/library/windowsazure/ee460801
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

[ServiceManagementRestProxy]: https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/ServiceManagementRestProxy.php

[Azure SDK for PHP]: https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/ServiceManagementRestProxy.php
[management-portal]: https://manage.windowsazure.com/
[svc-mgmt-rest-api]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx
[win-azure-account]: /pricing/free-trial/
[storage-account]: storage/storage-create-storage-account.md

[download-SDK-PHP]: php-download-sdk.md
[Azure CLI]: virtual-machines/virtual-machines-command-line-tools.md
[Composer]: http://getcomposer.org/
[ServiceManagementSettings]: https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/ServiceManagementSettings.php

[雲端服務]: cloud-services-what-is.md
[CreateServiceOptions]: https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/CreateServiceOptions.php
[ListHostedServicesResult]: https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/ListHostedServicesResult.php

[服務套件]: http://msdn.microsoft.com/library/windowsazure/gg433093
[Azure PowerShell Cmdlet]: install-configure-powershell.md
[cspack 命令列工具]: http://msdn.microsoft.com/library/windowsazure/gg432988.aspx
[GetDeploymentOptions]: https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/GetDeploymentOptions.php
[setSlot]: https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/GetDeploymentOptions.php
[ListHostedServicesResult]: https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/GetDeploymentOptions.php

[在 Azure 中管理部署概觀]: http://msdn.microsoft.com/library/windowsazure/hh386336.aspx
[儲存體服務]: storage-whatis-account.md
[azure-blobs]: storage/storage-php-how-to-use-blobs.md
[azure-tables]: storage/storage-php-how-to-use-table-storage.md
[azure-queues]: storage/storage-php-how-to-use-queues.md
[AffinityGroup]: https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/AffinityGroup.php


[Azure 服務組態結構描述 (.cscfg 檔)]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx

<!---HONumber=July15_HO1-->