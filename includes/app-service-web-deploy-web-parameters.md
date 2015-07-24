透過 Azure 資源管理員，您可以定義在部署範本時想要指定之值的參數。此範本有一個 Parameters 區段，內含所有參數值。您應該為會隨著要部署的專案或要部署到的環境而變化的值定義參數。請不要為永遠保持不變的值定義參數。每個參數值都可在範本中用來定義所部署的資源。

在定義參數時，請使用 [**allowedValues**] 欄位來指定使用者可以在部署期間提供的值。使用 [**defaultValue**] 欄位來指派部署期間未提供任何值時的參數值。

我們將說明範本中的每個參數。

### siteName

想要建立之 Web 應用程式的名稱。

    "siteName":{
      "type":"string"
    }

### hostingPlanName

用來主控 Web 應用程式之 App Service 方案的名稱。
    
    "hostingPlanName":{
      "type":"string"
    }

### siteLocation

用來建立 Web 應用程式和主控方案的位置。它必須是其中一個支援 Web 應用程式的 Azure 位置。

    "siteLocation":{
      "type":"string"
    }

### sku

主控方案的定價層。

    "sku":{
      "type":"string",
      "allowedValues":[
        "Free",
        "Shared",
        "Basic",
        "Standard"
      ],
      "defaultValue":"Free"
    }

範本會定義此參數允許使用的值 (免費、共用、基本或標準)，並指派未指定任何值時的預設值 (免費)。

### workerSize

主控方案的執行個體大小 (小型、中型或大型)。

    "workerSize":{
      "type":"string",
      "allowedValues":[
        "0",
        "1",
        "2"
      ],
      "defaultValue":"0"
    }
    
範本會定義此參數允許使用的值 (0、1 或 2)，並指派未指定任何值時的預設值 (0)。這些值分別對應到小型、中型和大型。

<!---HONumber=July15_HO1-->