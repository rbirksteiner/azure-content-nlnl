透過 Azure 資源管理員，您可以定義在部署範本時想要指定之值的參數。此範本有一個 Parameters 區段，內含所有參數值。您應該為會隨著要部署的專案或要部署到的環境而變化的值定義參數。請不要為永遠保持不變的值定義參數。每個參數值都可在範本中用來定義所部署的資源。

我們將說明範本中的每個參數。

### gatewayName

想要建立之閘道的名稱。API 應用程式會向此閘道註冊。

    "gatewayName": {
      "type": "string"
    }

### apiAppName

要建立之 API 應用程式的名稱。
    
    "apiAppName": {
      "type": "string"
    }

### apiAppSecret

API 應用程式的密碼。此值必須是 base64 編碼的字串。

    "apiAppSecret": {
      "type": "securestring"
    }

### location

新 API 應用程式的位置。

    "location": {
      "type": "string"
    }

<!---HONumber=62-->