<properties 
	pageTitle="如何在 Azure API 管理中使用 API 偵測器來追蹤呼叫" 
	description="了解如何在 Azure API 管理中使用 API 偵測器來追蹤呼叫。" 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/24/2015" 
	ms.author="sdanie"/>

# 如何在 Azure API 管理中使用 API 偵測器來追蹤呼叫

API 管理提供 API 偵測器工具協助您進行 API 的偵錯和疑難排解。API 偵測器可以在應用程式的程式設計中，也可以直接從開發人員入口網站中使用。本指南提供使用 API 偵測器的逐步解說。

>[AZURE.NOTE]除了追蹤作業，API 偵測器也會追蹤[原則運算式](https://msdn.microsoft.com/library/azure/dn910913.aspx)評估。如需示範，請參閱 [Cloud Cover 第 177 集：更多 API 管理功能與 Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) 並向前快轉到 21:00。

## <a name="trace-call"> </a> 使用 API 偵測器來追蹤呼叫

若要使用 API 偵測器，請將 **ocp-apim-trace: true** 要求標頭新增至作業呼叫，然後使用 **ocp-apim-trace-location** 回應標頭所指出的 URL 來下載並檢查追蹤。這可以透過程式設計來進行，也可以直接從開發人員入口網站來執行。

本教學課程示範如何使用 API 偵測器來追蹤作業，方法是使用[管理您的第一個 API](api-management-get-started.md) 入門教學課程中設定的基本計算機 API。如果您尚未完成該教學課程，匯入基本計算機 API 只需要幾分鐘的時間，或可以使用您選擇的另一個 API (例如 Echo API)。每個 API 管理服務執行個體隨附預先設定的範例 Echo API，可供您試驗與了解 API 管理。Echo API 會將任何傳送給它的輸入傳回。若要使用該 API，您可以叫用任何 HTTP 指令動詞，而傳回值就是您傳送的值。



若要開始，請在 API 管理服務的 Azure 入口網站中按一下 [**開發人員入口網站**]。您可以從開發人員入口網站直接呼叫作業，以便檢視和測試 API 的操作。

>如果您尚未建立 API 管理服務執行個體，請參閱[開始使用 Azure API 管理教學課程][]中的[建立 API 管理服務執行個體][]。

![API Management developer portal][api-management-developer-portal-menu]

從上方功能表中按一下 [**API**]，然後按一下 [**基本計算機**]。

![Echo API][api-management-api]

按一下 [**試試看**] 來嘗試 [**加入兩個整數**] 作業。

![試試看][api-management-open-console]

保留預設的參數值，然後從 **subscription-key** 下拉式清單選取您想要使用的產品的訂閱金鑰。

根據預設，開發人員入口網站中已將 **Ocp-Apim-Trace** 標頭設為 **true**。此標頭會設定是否產生追蹤。

![傳送][api-management-http-get]

按一下 [**傳送**] 來叫用作業。

![傳送][api-management-send-results]

在回應標頭中，將會有一個 **ocp-apim-trace-location**，且值類似下列範例。

	ocp-apim-trace-location : https://contosoltdxw7zagdfsprykd.blob.core.windows.net/apiinspectorcontainer/ZW3e23NsW4wQyS-SHjS0Og2-2?sv=2013-08-15&sr=b&sig=Mgx7cMHsLmVDv%2B%2BSzvg3JR8qGTHoOyIAV7xDsZbF7%2Bk%3D&se=2014-05-04T21%3A00%3A13Z&sp=r&verify_guid=a56a17d83de04fcb8b9766df38514742

可從指定的位置下載追蹤來檢閱，如下一個步驟所示。

## <a name="inspect-trace"> </a>檢查追蹤

若要檢閱追蹤裡的值，請從 **ocp-apim-trace-location** URL 下載追蹤檔案。它是一個 JSON 格式的文字檔，且包含類似下列範例的項目。

	{
	    "traceId": "abcd8ea63d134c1fabe6371566c7cbea",
	    "traceEntries": {
	        "inbound": [
	            {
	                "source": "handler",
	                "timestamp": "2015-06-23T19:51:35.2998610Z",
	                "elapsed": "00:00:00.0725926",
	                "data": {
	                    "request": {
	                        "method": "GET",
	                        "url": "https://contoso5.azure-api.net/calc/add?a=51&b=49",
	                        "headers": [
	                            {
	                                "name": "Ocp-Apim-Subscription-Key",
	                                "value": "5d7c41af64a44a68a2ea46580d271a59"
	                            },
	                            {
	                                "name": "Connection",
	                                "value": "Keep-Alive"
	                            },
	                            {
	                                "name": "Host",
	                                "value": "contoso5.azure-api.net"
	                            }
	                        ]
	                    }
	                }
	            },
	            {
	                "source": "mapper",
	                "timestamp": "2015-06-23T19:51:35.2998610Z",
	                "elapsed": "00:00:00.0726213",
	                "data": {
	                    "configuration": {
	                        "api": {
	                            "from": "/calc",
	                            "to": {
	                                "scheme": "http",
	                                "host": "calcapi.cloudapp.net",
	                                "port": 80,
	                                "path": "/api",
	                                "queryString": "",
	                                "query": {},
	                                "isDefaultPort": true
	                            }
	                        },
	                        "operation": {
	                            "method": "GET",
	                            "uriTemplate": "/add?a={a}&b={b}"
	                        },
	                        "user": {
	                            "id": 1,
	                            "groups": [
	                                "Administrators",
	                                "Developers"
	                            ]
	                        },
	                        "product": {
	                            "id": 1
	                        }
	                    }
	                }
	            },
	            {
	                "source": "handler",
	                "timestamp": "2015-06-23T19:51:35.2998610Z",
	                "elapsed": "00:00:00.0727522",
	                "data": {
	                    "message": "Request is being forwarded to the backend service.",
	                    "request": {
	                        "method": "GET",
	                        "url": "http://calcapi.cloudapp.net/api/add?a=51&b=49",
	                        "headers": [
	                            {
	                                "name": "Ocp-Apim-Subscription-Key",
	                                "value": "5d7c41af64a44a68a2ea46580d271a59"
	                            },
	                            {
	                                "name": "X-Forwarded-For",
	                                "value": "33.52.215.35"
	                            }
	                        ]
	                    }
	                }
	            }
	        ],
	        "outbound": [
	            {
	                "source": "handler",
	                "timestamp": "2015-06-23T19:51:35.4256650Z",
	                "elapsed": "00:00:00.1960601",
	                "data": {
	                    "response": {
	                        "status": {
	                            "code": 200,
	                            "reason": "OK"
	                        },
	                        "headers": [
	                            {
	                                "name": "Pragma",
	                                "value": "no-cache"
	                            },
	                            {
	                                "name": "Content-Length",
	                                "value": "124"
	                            },
	                            {
	                                "name": "Cache-Control",
	                                "value": "no-cache"
	                            },
	                            {
	                                "name": "Content-Type",
	                                "value": "application/xml; charset=utf-8"
	                            },
	                            {
	                                "name": "Date",
	                                "value": "Tue, 23 Jun 2015 19:51:35 GMT"
	                            },
	                            {
	                                "name": "Expires",
	                                "value": "-1"
	                            },
	                            {
	                                "name": "Server",
	                                "value": "Microsoft-IIS/8.5"
	                            },
	                            {
	                                "name": "X-AspNet-Version",
	                                "value": "4.0.30319"
	                            },
	                            {
	                                "name": "X-Powered-By",
	                                "value": "ASP.NET"
	                            }
	                        ]
	                    }
	                }
	            },
	            {
	                "source": "handler",
	                "timestamp": "2015-06-23T19:51:35.4256650Z",
	                "elapsed": "00:00:00.1961112",
	                "data": {
	                    "message": "Response headers have been sent to the caller. Starting to stream the response body."
	                }
	            },
	            {
	                "source": "handler",
	                "timestamp": "2015-06-23T19:51:35.4256650Z",
	                "elapsed": "00:00:00.1963155",
	                "data": {
	                    "message": "Response body streaming to the caller is complete."
	                }
	            }
	        ]
	    }
	}

## <a name="next-steps"> </a>後續步驟

-	在[開始使用進階 API 組態][]教學課程中查看其他主題。
-	在 [Cloud Cover 第 177 集：更多 API 管理功能與 Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) 中觀看追蹤原則運算式的示範。向前快轉到 21:00 來查看示範。

>[AZURE.VIDEO episode-177-more-api-management-features-with-vlad-vinogradsky]

[Use API Inspector to trace a call]: #trace-call
[Inspect the trace]: #inspect-trace
[Next steps]: #next-steps

[Configure API settings]: api-management-howto-create-apis.md#configure-api-settings
[Responses]: api-management-howto-add-operations.md#responses
[How create and publish a product]: api-management-howto-add-products.md

[開始使用 Azure API 管理教學課程]: api-management-get-started.md
[建立 API 管理服務執行個體]: api-management-get-started.md#create-service-instance
[開始使用進階 API 組態]: api-management-get-started-advanced.md
[Management Portal]: https://manage.windowsazure.com/


[api-management-developer-portal-menu]: ./media/api-management-howto-api-inspector/api-management-developer-portal-menu.png
[api-management-api]: ./media/api-management-howto-api-inspector/api-management-api.png
[api-management-echo-api-get]: ./media/api-management-howto-api-inspector/api-management-echo-api-get.png
[api-management-developer-key]: ./media/api-management-howto-api-inspector/api-management-developer-key.png
[api-management-open-console]: ./media/api-management-howto-api-inspector/api-management-open-console.png
[api-management-http-get]: ./media/api-management-howto-api-inspector/api-management-http-get.png
[api-management-send-results]: ./media/api-management-howto-api-inspector/api-management-send-results.png




 

<!---HONumber=62-->