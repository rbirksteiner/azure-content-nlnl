<properties 
	pageTitle="撰寫邏輯應用程式定義" 
	description="了解如何撰寫邏輯應用程式的 JSON 定義。" 
	authors="stepsic-microsoft-com" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/23/2015"
	ms.author="stepsic"/>
	
#撰寫邏輯應用程式定義
本主題示範如何使用 [App Service Logic Apps](app-service-logic-what-are-logic-apps.md) 定義，這是一種簡單的宣告式 JSON 語言。請先看看[如何建立新的邏輯應用程式](../app-service-create-a-logic-app.md) (如果還沒看過)。您也可以閱讀 [MSDN 上關於此定義語言的完整參考資料](https://msdn.microsoft.com/library/azure/dn948512.aspx)。

## 清單上重複的幾個步驟

常見的模式是先有一個步驟取得項目清單，接著有您想在清單上執行的兩個以上的一系列動作。

![逐一查看清單](./media/app-service-logic-author-definitions/repeatoverlists.png)

在此範例中有 3 個動作：

1. 取得文章清單。這會傳回一個包含陣列的物件。

2. 移至每個文章上某個連結屬性的動作，將傳回文章的實際位置。

3. 在第二個動作的所有結果中，逐一查看結果以下載實際文章的動作。

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "triggers": {},
    "actions": {
        "getArticles": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.wired.com/wired/index"
            }
        },
        "readLinks": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@repeatItem().link"
            },
            "repeat": "@body('getArticles').responseData.feed.entries"
        },
        "downloadLinks": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@repeatOutputs().headers.location"
            },
            "conditions": [
                {
                    "expression": "@not(equals(actions('readLinks').status, 'Skipped'))"
                }
            ],
            "repeat": "@actions('readLinks').outputs.repeatItems"
        }
    },
    "outputs": {}
}
```

如[使用邏輯應用程式功能](app-service-logic-use-logic-app-features.md)所述，您在第二個動作上使用 `repeat:` 屬性逐一查看第一個清單。不過，在第三個動作中，您需要選取 `@actions('readLinks').outputs.repeatItems` 屬性，因為已對每一個文章執行第二個動作。

您在動作內可以使用：[`repeatItem()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#repeatItem)、[`repeatOutputs()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#repeatOutputs) 或 [`repeatBody()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#repeatBody) 函式。在此範例中，我想要取得 `location` 標頭，因此我使用 [`repeatOutputs()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#repeatOutputs) 函式，從我們現在反覆執行的第二個動作取得動作執行的輸出。

## 將清單中的項目對應至一些不同的組態

接下來，假設我們想要根據屬性的值取得完全不同的內容。我們可以建立值與目的地的對應做為參數。

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "specialCategories": {
            "defaultValue": [
                "science",
                "google",
                "microsoft",
                "robots",
                "NSA"
            ],
            "type": "Array"
        },
        "destinationMap": {
            "defaultValue": {
                "science": "http://www.nasa.gov",
                "microsoft": "https://www.microsoft.com/zh-tw/default.aspx",
                "google": "https://www.google.com",
                "robots": "https://en.wikipedia.org/wiki/Robot",
                "NSA": "https://www.nsa.gov/"
            },
            "type": "Object"
        }
    },
    "triggers": {},
    "actions": {
        "getArticles": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.wired.com/wired/index"
            },
            "conditions": []
        },
        "getSpecialPage": {
            "repeat": "@body('getArticles').responseData.feed.entries",
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(repeatItem().categories, parameters('specialCategories')))]"
            },
            "conditions": [
                {
                    "expression": "@greater(length(intersection(repeatItem().categories, parameters('specialCategories'))), 0)"
                }
            ]
        }
    }
}
```

在此案例中，我們先取得文章清單，然後第二個步驟根據已定義為參數的類別，在對應中查詢可取得內容的 URL。

在此要注意兩個項目：[`intersection()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#intersection) 函式用來檢查類別是否符合其中一個已定義的已知類別。其次，一旦得到類別，我們可以使用方括號提取對應的項目：`parameters[...]`。

## 逐一查看清單時鏈結/巢狀 Logic Apps

較嚴謹的 Logic Apps 通常比較容易管理。作法上可以將邏輯分解成多個定義，再從相同的父定義呼叫它們。在此範例中，有一個父邏輯應用程式接收訂單，也有一個子邏輯應用程式對每個訂單執行一些步驟。

在父邏輯應用程式中：

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "orders": {
            "defaultValue": [
                {
                    "quantity": 10,
                    "id": "myorder1"
                },
                {
                    "quantity": 200,
                    "id": "specialOrder"
                },
                {
                    "quantity": 5,
                    "id": "myOtherOrder"
                }
            ],
            "type": "Array"
        }
    },
    "triggers": {},
    "actions": {
        "iterateOverOrders": {
            "repeat": "@parameters('orders')",
            "type": "Workflow",
            "inputs": {
                "uri": "https://westus.logic.azure.com/subscriptions/xxxxxx-xxxxx-xxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Logic/workflows/xxxxxxx",
                "apiVersion": "2015-02-01-preview",
                "trigger": {
                    "name": "submitOrder",
                    "outputs": {
                        "body": "@repeatItem()"
                    }
                },
                "authentication": {
                    "type": "Basic",
                    "username": "default",
                    "password": "xxxxxxxxxxxxxx"
                }
            }
        },
        "sendInvoices": {
            "repeat": "@outputs('iterateOverOrders').repeatItems",
            "type": "Http",
            "inputs": {
                "uri": "http://www.example.com/?invoiceID=@{repeatOutputs().run.outputs.deliverTime.value}",
                "method": "GET"
            }
        }
    },
    "outputs": {}
}
```

然後，在子邏輯應用程式中，我們使用 [`triggerBody()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#triggerBody) 函式取得已傳遞至子工作流程的值。您將會在輸出中填入想要傳回至父資料流程的資料。

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "triggers": {},
    "actions": {
        "calulatePrice": {
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?action=calcPrice&id=@{triggerBody().id}&qty=@{triggerBody().quantity}"
            }
        },
        "calculateDeliveryTime": {
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?action=calcTime&id=@{triggerBody().id}&qty=@{triggerBody().quantity}"
            }
        }
    },
    "outputs": {
        "deliverTime": {
            "type": "String",
            "value": "@outputs('calculateDeliveryTime').headers.etag"
        }
    }
}
```

您可以閱讀 [MSDN 上的邏輯應用程式類型動作](https://msdn.microsoft.com/zh-tw/library/azure/dn948511.aspx)。

>[AZURE.NOTE]邏輯應用程式設計工具不支援邏輯應用程式類型的動作，因此您必須手動編輯定義。


## 發生錯誤時的失敗處理步驟

您通常想要撰寫*補救步驟* - 如果**且唯有當**一或多個呼叫失敗時執行的一些邏輯。在此範例中，我們從各種地方取得資料，但如果呼叫失敗，我想要在某處「張貼」訊息，方便稍後追蹤該失敗。

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "dataFeeds": {
            "defaultValue": [
                "https://www.microsoft.com/zh-tw/default.aspx",
                "https://gibberish.gibberish/"
            ],
            "type": "Array"
        }
    },
    "triggers": {},
    "actions": {
        "readData": {
            "repeat": "@parameters('dataFeeds')",
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@repeatItem()"
            }
        },
        "postToErrorMessageQueue": {
            "repeat": "@actions('readData').outputs.repeatItems",
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?noteAnErrorFor=@{repeatItem().inputs.uri}"
            },
            "conditions": [
                {
                    "expression": "@equals(actions('readData').status, 'Failed')"
                },
                {
                    "expression": "@equals(repeatItem().status, 'Failed')"
                }
            ]
        }
    },
    "outputs": {}
}
```

因為我在第一個步驟中逐一查看清單，所以我使用兩個條件。如果您只是有單一動作，您只需要一個條件 (第一個)。另外請注意，您可以在補救步驟中對失敗動作使用 *inputs* -- 在此我將失敗的 URL 傳給第二個步驟。

![補救](./media/app-service-logic-author-definitions/remediation.png)

最後，因為您現在已經處理錯誤，我們不再將執行結果標示為**失敗**。您在這裡可以看到，即使一個步驟失敗，此執行也**成功**，因為我撰寫步驟來處理這項失敗。

## 平行執行的兩個 (或更多) 步驟

若要讓多個動作平行執行，而非循序執行，您必須移除將兩個動作連結在一起的 `dependsOn` 條件。一旦移除相依性，動作將會自動平行執行，除非它們需要彼此的資料。

![分支](./media/app-service-logic-author-definitions/branches.png)

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "dataFeeds": {
            "defaultValue": [
                "https://www.microsoft.com/zh-tw/default.aspx",
                "https://office.live.com/start/default.aspx"
            ],
            "type": "Array"
        }
    },
    "triggers": {},
    "actions": {
        "readData": {
            "repeat": "@parameters('dataFeeds')",
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@repeatItem()"
            }
        },
        "branch1": {
            "repeat": "@actions('readData').outputs.repeatItems",
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?branch1Logic=@{repeatItem().inputs.uri}"
            }
        },
        "branch2": {
            "repeat": "@actions('readData').outputs.repeatItems",
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?branch2Logic=@{repeatItem().inputs.uri}"
            }
        }
    },
    "outputs": {}
}
```

如您在上述範例中所見，branch1 和 branch2 只相依於 readData 的內容。因此，這兩個分支會平行執行：

![平行](./media/app-service-logic-author-definitions/parallel.png)

您可以看到兩個分支的時間戳記完全相同。

## 結合邏輯的兩個條件式分支

您可以使用單一動作取得兩個分支的資料，以結合邏輯的兩個條件式流程 (可能已執行，也可能尚未執行)。

這方面的決策取決於您要處理一個項目或一組項目。如果是單一項目，您可以使用 [`coalesce()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#coalesce) 函式：

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "order": {
            "defaultValue": {
                "quantity": 10,
                "id": "myorder1"
            },
            "type": "Object"
        }
    },
    "triggers": {},
    "actions": {
        "handleNormalOrders": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?orderNormally=@{parameters('order').id}"
            },
            "conditions": [
                {
                    "expression": "@lessOrEquals(parameters('order').quantity, 100)"
                }
            ]
        },
        "handleSpecialOrders": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?orderSpecially=@{parameters('order').id}"
            },
            "conditions": [
                {
                    "expression": "@greater(parameters('order').quantity, 100)"
                }
            ]
        },
        "submitInvoice": {
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?invoice=@{coalesce(outputs('handleNormalOrders')?.headers?.etag,outputs('handleSpecialOrders')?.headers?.etag )}"
            },
            "conditions": [
                {
                    "expression": "@or(equals(actions('handleNormalOrders').status, 'Succeeded'), equals(actions('handleSpecialOrders').status, 'Succeeded'))"
                }
            ]
        }
    },
    "outputs": {}
}
```
 
或者，舉例來說，當您的前兩個分支都在訂單清單上執行時，您可以使用 [`union()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#union) 函式來結合兩個分支的資料。

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "orders": {
            "defaultValue": [
                {
                    "quantity": 10,
                    "id": "myorder1"
                },
                {
                    "quantity": 200,
                    "id": "specialOrder"
                },
                {
                    "quantity": 5,
                    "id": "myOtherOrder"
                }
            ],
            "type": "Array"
        }
    },
    "triggers": {},
    "actions": {
        "handleNormalOrders": {
            "repeat": "@parameters('orders')",
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?orderNormally=@{repeatItem().id}"
            },
            "conditions": [
                {
                    "expression": "@lessOrEquals(repeatItem().quantity, 100)"
                }
            ]
        },
        "handleSpecialOrders": {
            "repeat": "@parameters('orders')",
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?orderSpecially=@{repeatItem().id}"
            },
            "conditions": [
                {
                    "expression": "@greater(repeatItem().quantity, 100)"
                }
            ]
        },
        "submitInvoice": {
            "repeat": "@union(actions('handleNormalOrders').outputs.repeatItems, actions('handleSpecialOrders').outputs.repeatItems)",
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?invoice=@{repeatOutputs().headers.etag}"
            },
            "conditions": [
                {
                    "expression": "@equals(repeatItem().status, 'Succeeded')"
                }
            ]
        }
    },
    "outputs": {}
}
```
## 處理字串

有各種不同的函式可用來操作字串。我們來看一個範例，假設我們想要將一個字串傳遞到系統，但不確定字元編碼是否會正確處理。一種作法是以 base64 將此字串編碼。不過，為了避免在 URL 中逸出，我們要取代幾個字元。

我們也想要訂單名稱的子字串，因為不會用到前 5 個字元。

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "order": {
            "defaultValue": {
                "quantity": 10,
                "id": "myorder1",
                "orderer": "NAME=Stèphén__Šīçiłianö"
            },
            "type": "Object"
        }
    },
    "triggers": {},
    "actions": {
        "order": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').orderer,5,sub(length(parameters('order').orderer), 5) )),'+','-') ,'/' ,'_' )}"
            }
        }
    },
    "outputs": {}
}
```

詳細作法：

1. 取得訂單名稱的 [`length()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#length)，這會傳回字元總數

2. 減 5 (因為我們要較短的字串)

3. 實際取得 [`substring()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#substring)。我們從索引 `5` 開始，並取得字串的其餘部分。

4. 將這個子字串轉換成 [`base64()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#base64) 字串

5. 以 `-` [`replace()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#replace) 所有 `+` 字元

6. 以 `_` [`replace()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#replace) 所有 `/` 字元

## 使用日期時間

日期時間很有用，特別是在嘗試從不支援的**觸發程序**的資料來源提取資料時。您也可以使用日期時間算出各步驟花費的時間。

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "order": {
            "defaultValue": {
                "quantity": 10,
                "id": "myorder1"
            },
            "type": "Object"
        }
    },
    "triggers": {},
    "actions": {
        "order": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?id=@{parameters('order').id}"
            }
        },
        "timingWarning": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
        	},
            "conditions": [
                {
                    "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))"
                }
            ]
        }
    },
    "outputs": {}
}
```

在此範例中，我們擷取前一個步驟的 `startTime`。然後，我們取得目前的時間並減去一秒：[`addseconds(..., -1)`](https://msdn.microsoft.com/library/azure/dn948512.aspx#addseconds) (您可以使用其他的時間單位，例如 `minutes` 或 `hours`)。最後，我們可以比較這兩個值。如果第一個值小於第二個值，即表示自從訂單最初提交以來已超過一秒。

也請注意，我們可以使用字串格式子來格式化日期：我在查詢字串中使用 [`utcnow('r')`](https://msdn.microsoft.com/library/azure/dn948512.aspx#utcnow) 取得 RFC1123。所有日期格式[記載於 MSDN 上](https://msdn.microsoft.com/library/azure/dn948512.aspx#utcnow)。

## 在執行階段傳入值來改變行為

假設您想要根據用來啟動邏輯應用程式的某些值，以執行不同的行為。您可以使用 [`triggerOutputs()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#triggerOutputs) 函式，從您傳入的任何值中取出這些值：

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "triggers": {},
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@triggerOutputs().uriToGet"
            }
        },
        "extraStep": {
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/extraStep"
            },
            "conditions": [
                {
                    "expression": "@triggerOutputs().doMoreLogic"
                }
            ]
        },  
    },
    "outputs": {}
}
```

為了實際發揮作用，開始執行時，您需要傳遞您想要的屬性 (在上述範例中是 `uriToGet` 和 `doMoreLogic`)。以下是您可以[使用基本驗證](https://msdn.microsoft.com/library/azure/dn948513.aspx#basicAuth)的呼叫：

```
POST https://<<Logic app endpoint from the Essentials>>/run?api-version=2015-02-01-preview
Authorization: Basic <<Based 64 encoded username (default) : password (from the Settings blade)>>
Content-type: application/json
```

搭配下列內容。請注意，您現在已提供值給邏輯應用程式使用：

```
{
    "outputs": {
        "uriToGet" : "http://my.uri.I.want/",
        "doMoreLogic" : true
    }
}
``` 

此邏輯應用程式執行時會呼叫我傳入的 uri，並執行該額外步驟，因為我傳遞 `true`。如果您只想在部署階段改變參數 (不是*每次執行*)，則應該使用以下顯示的 `parameters`。

## 對不同的環境使用部署階段參數

部署生命週期中通常會有開發環境、預備環境及生產環境。在所有這些環境中，舉例來說，您可能想要有相同的定義，但使用不同的資料庫。同樣地，您可能想要跨許多不同的區域使用相同的定義，以發揮高可用性，但希望每個邏輯應用程式執行個體與該區域資料庫互動。

請注意，這不同於在*執行階段*採用不同的參數，對此您應該使用上述的 `trigger()` 函式。

您可以從非常簡單的定義開始，如下所示：

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "connection": {
            "type": "string"
        }
    },
    "triggers": {},
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('connection')"
            }
        }        
    },
    "outputs": {}
}
```

然後，在邏輯應用程式的實際 `PUT` 要求中，您可以提供參數 `connection`。請注意，由於已不再有預設值，邏輯應用程式內容中需要這個參數：

```
{
    "properties": {
        "sku": {
            "name": "Premium",
            "plan": {
                "id": "/subscriptions/xxxxx/resourceGroups/xxxxxx/providers/Microsoft.Web/serverFarms/xxxxxx"
            }
        },
        "definition": {
          // Use the definition from above here
        },
        "parameters": {
            "connection": {
                "value": "https://my.connection.that.is.per.enviornment"
            }
        }
    },
    "location": "westus"
}
``` 

然後，在每個環境中，您就可以提供不同的值給 `connection` 參數。如需有關建立及管理邏輯應用程式的所有可用選項，請參閱 [REST API 文件](https://msdn.microsoft.com/library/azure/dn948513.aspx)。

<!---HONumber=62-->