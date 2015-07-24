<properties 
	pageTitle="透過 Twilio 使用語音和簡訊功能 | 行動開發人員中心" 
	description="了解如何使用 Twilio API 和 Azure 行動服務執行一般工作。" 
	services="mobile-services" 
	documentationCenter="" 
	authors="devinrader" 
	manager="twilio" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="04/24/2015" 
	ms.author="devinrader"/>


# 如何從行動服務透過 Twilio 使用語音和簡訊功能

本主題將說明如何使用 Twilio API 和 Azure 行動服務執行一般工作。在本教學課程中，您將了解如何建立自訂 API 指令碼，以使用 Twilio API 起始電話及傳送簡訊 (SMS)。

## <a id="WhatIs"></a>什麼是 Twilio？
Twilio 正在形塑商業環境的未來，可讓開發人員將語音、VoIP 和訊息傳送內嵌到應用程式中。它們將雲端、全球化環境中所需的整個基礎結構虛擬化，透過 Twilio 通訊 API 平台來揭露基礎結構。輕鬆就可建立和擴充應用程式。享受隨收隨付定價的彈性和雲端可靠性的好處。

**Twilio 語音**可讓應用程式撥打和接聽電話。**Twilio 簡訊**可讓應用程式收發簡訊。**Twilio 用戶端**可讓您從任何電話、平板電腦或瀏覽器撥打 VoIP 電話，且支援 WebRTC。

## <a id="Pricing"></a>Twilio 定價和特別優惠
升級 Twilio 帳戶的 Azure 客戶，可[特別獲贈][special_offer]價值 $10 的 Twilio 點數。此 Twilio 點數可用來折抵任何 Twilio 使用量 ($10 點數相當於最多傳送 1,000 則簡訊，或最多接收 1000 分鐘的撥入語音，視電話號碼所在地點或通話目的地而定)。請至 [ahoy.twilio.com/azure][special_offer] 兌換 Twilio 點數來開始使用。

Twilio 是隨收隨付的服務。不需要設定費，隨時都可結清帳戶。如需詳細資訊，請參閱＜[Twilio 定價][twilio_pricing]＞(英文)。

## <a id="Concepts"></a>概念
Twilio API 是一套為應用程式提供語音和簡訊功能的 RESTful API。用戶端程式庫有多種語言版本，相關清單請參閱＜[Twilio API 程式庫][twilio_libraries]＞(英文)。其他教學課程會說明如何使用 Twilio 和以 [.NET][azure_twilio_howto_dotnet]、[node.js][azure_twilio_howto_node]、[Java][azure_twilio_howto_java]、[PHP][azure_twilio_howto_php]、[Python][azure_twilio_howto_python] 或 [Ruby][azure_twilio_howto_ruby] 撰寫的 Azure 應用程式。

Twilio API 的兩大重點是 Twilio 動詞和 Twilio 標記語言 (TwiML)。

### <a id="Verbs"></a>Twilio 動詞
API 採用 Twilio 動詞。例如，**&lt;Say&gt;** 動詞指示 Twilio 在通話中用語音傳遞訊息。

以下是 Twilio 動詞清單。如需了解其他動詞和功能，請參閱＜[Twilio 標記語言文件](http://www.twilio.com/docs/api/twiml)＞(英文)。

* **&lt;Dial&gt;**：使撥號者接通另一支電話。
* **&lt;Gather&gt;**：收集電話按鍵上輸入的號碼。
* **&lt;Hangup&gt;**：結束通話。
* **&lt;Play&gt;**：播放音訊檔案。
* **&lt;Pause&gt;**：靜候一段指定的秒數。
* **&lt;Record&gt;**：錄製來電者的語音並傳回含有錄音的檔案 URL。
* **&lt;Redirect&gt;**：將通話或簡訊的控制權移轉至不同 URL 的 TwiML。
* **&lt;Reject&gt;**：拒絕 Twilio 號碼的來電而不計費
* **&lt;Say&gt;**：將來電的文字轉換成語音。
* **&lt;Sms&gt;**：傳送簡訊。

### <a id="TwiML"></a>TwiML
TwiML 是以 Twilio 動詞為基礎的一組 XML 指令，可指示 Twilio 如何處理來電或簡訊。

例如，下列 TwiML 會將 **Hello World** 文字轉換成語音。

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

當應用程式呼叫 Twilio API 時，其中一個 API 參數是傳回 TwiML 回應的 URL。在開發用途上，您可以使用 Twilio 提供的 URL 來提供應用程式所使用的 TwiML 回應。您也可以裝載您自己的 URL 來產生 TwiML 回應，另一種選擇是使用 **TwiMLResponse** 物件。

如需 Twilio 動詞、屬性和 TwiML 的詳細資訊，請參閱 [TwiML][twiml]。如需 Twilio API 的詳細資訊，請參閱 [Twilio API][twilio_api]。

## <a id="CreateAccount"></a>建立 Twilio 帳戶
準備取得 Twilio 帳戶時，請至[試用 Twilio][try_twilio] 註冊。您可以先使用免費帳戶，稍後再升級帳戶。

註冊 Twilio 帳戶時，您會收到帳戶識別碼和驗證權杖。兩者皆為呼叫 Twilio API 所需。為了防止未經授權存取您的帳戶，您妥善保管驗證權杖。在 [Twilio 帳戶頁面][twilio_account] (英文) 的 **ACCOUNT SID** 和 **AUTH TOKEN** 欄位中，分別可檢視您的帳戶識別碼和驗證權杖。

## <a id="create_app"></a>建立行動服務
代管已啟用 Twilio 功能之應用程式的行動服務，與其他行動服務並無不同。您只是在服務中新增 Twilio node.js 程式庫，以便從您的行動服務自訂 API 指令碼加以參考而已。如需建立初始行動服務的相關資訊，請參閱[開始使用行動服務](mobile-services-ios-get-started.md)。

## <a id="ConfigureMobileService"></a>設定行動服務以使用 Twilio Node.js 程式庫
Twilio 所提供的 Node.js 程式庫封裝了 Twilio 的各種組件，讓您方便而輕鬆地與 Twilio REST API 和 Twilio 用戶端互動，以產生 TwiML 回應。

若要在行動服務中使用 Twilio node.js 程式庫，您必須利用行動服務 npm 模組支援 (只要將您的指令碼儲存在來源控制中，即可支援)。

1. 完成[在原始檔控制中儲存指令碼](mobile-services-store-scripts-source-control.md)教學課程。這會逐步帶您設定行動服務的原始檔控制，並在 Git 儲存機制中儲存您的伺服器指令碼。

2. 為行動服務設定原始檔控制後，請在您的本機電腦上開啟儲存機制，瀏覽至 `\services` 子資料夾，在文字編輯器中開啟 package.json 檔案，再將下列欄位新增至 **dependencies** 物件：

		"twilio": "~1.7.0"
 
3. 在新增 **dependencies** 物件的 Twilio 封裝參考之後，package.json 檔案看起來應該如下所示：

		{
		  "name": "todolist",
		  "version": "1.0.0",
		  "description": "todolist - hosted on Azure Mobile Services",
		  "main": "server.js",
		  "engines": {
		    "node": ">= 0.8.19"
		  },
		  "dependencies": {
			"twilio": "~1.7.0" 
		  },
		  "devDependencies": {},
		  "scripts": {},
		  "author": "unknown",
		  "licenses": [],
		  "keywords":[]
		}

	>[AZURE.NOTE]應將 Twilio 的相依性新增為 `"twilio": "~1.7.0"`，包含 (~)。不支援使用插入號 (^) 的參考。

4. 認可此檔案更新，並將更新推回行動服務。

	此項 package.json 檔案更新會重新啟動您的行動服務。
	
行動服務現在會安裝並載入 Twilio 封裝，以便您可以在自訂的 API 和資料表指令碼中參考及使用 Twilio 程式庫。

## <a id="howto_make_call"></a>作法：撥出電話
下列指令碼將說明如何使用 **makeCall** 函數從您的行動服務起始對話通話。此程式碼也使用 Twilio 提供的網站來傳回 Twilio 標記語言 (TwiML) 回應。請將 **From** 和 **To** 電話號碼換成您的值，在執行程式碼之前，請記得先驗證 Twilio 帳戶的 **From** 電話號碼。

    var twilio = require('twilio');

    exports.post = function(request, response) {

        var client = new twilio.RestClient('[ACCOUNT_SID]', 'AUTH_TOKEN');

        client.makeCall({
            to:'+16515556677', 
            from: '+14506667788',
            url: 'http://www.example.com/twiml.php' 

        }, function(err, responseData) {
            console.log(responseData.from); 
            response.send(200, '');
        });
    };

如需有關傳入至 **client.makeCall** 函數之參數的詳細資訊，請參閱 [http://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls]。

如前所述，此程式碼使用 Twilio 提供的網站來傳回 TwiML 回應。您可以改用您自己的網站來提供 TwiML 回應。如需詳細資訊，請參閱[作法：從您自己的網站提供 TwiML 回應](#howto_provide_twiml_responses)。

## <a id="howto_send_sms"></a>作法：傳送簡訊
下列程式碼將說明如何使用 **sendSms** 函數來傳送簡訊。**From** 號碼由 Twilio 提供給試用帳戶來傳送簡訊。執行程式碼之前，必須驗證 Twilio 帳戶的 **To** 號碼。

    var twilio = require('twilio');

    exports.post = function(request, response) {

        var client = new twilio.RestClient('[ACCOUNT_SID]', 'AUTH_TOKEN');
 
        client.sendSms({
            to:'[]',
            from:'[]',
            body:'ahoy hoy! Testing Twilio and node.js'
        }, function(error, message) {
    
            // The "error" variable will contain error information, if any.
            // If the request was successful, this value will be "false"
            if (!error) {
                console.log('Success! The SID for this SMS message is: ' + message.sid);
                console.log('Message sent on: ' + message.dateCreated);
            }
            else {
                console.log('Oops! There was an error.');
            }
            response.send(200, { error : error } );
        });
    };


## <a id="howto_provide_twiml_responses"></a>作法：從您自己的網站提供 TwiML 回應

當應用程式開始呼叫 Twilio API 時 (例如，透過 client.InitiateOutboundCall 方法)，Twilio 會傳送要求至 URL，然後應該會傳回 TwiML 回應。在＜作法：撥出電話＞中的範例使用 Twilio 提供的 URL http://twimlets.com/message 來傳回回應。

> [AZURE.NOTE]雖然 TwiML 是專供 Web 服務使用，但您也可以在瀏覽器中檢視 TwiML。例如，您可以按一下 [twimlet_message_url](http://twimlets.com/message) 以檢視空的 &lt;Response&gt; 元素，或按一下 [twimlet_message_url_hello_world](http://twimlets.com/message?Message%5B0%5D=Hello%20World) 以檢視包含 &lt;Say&gt; 元素的 &lt;Response&gt; 元素。

除了依賴 Twilio 提供的 URL，您也可以建立自己的 URL 網站來傳回 HTTP 回應。您可以使用任何可傳回 HTTP 回應的語言來建立網站。本主題假設您從 ASP.NET 通用處理常式來裝載 URL。

下列指令碼會產生一個在來電中顯示 Hello World 的 TwiML 回應。

    var twilio = require('twilio');

    exports.post = function(request, response) {
        var resp = new twilio.TwimlResponse();
        resp.say({voice:'woman'}, 'ahoy hoy! Testing Twilio and node.js');
        response.set('Content-Type', 'text/xml');
        response.send(200, resp.toString());
    };

如需 TwiML 的詳細資訊，請參閱 [https://www.twilio.com/docs/api/twiml](https://www.twilio.com/docs/api/twiml)。

設定好如何提供 TwiML 回應之後，您可以將 URL 傳入 **client.makeCall** 方法中，如下列程式碼範例所示：
    
    var twilio = require('twilio');

    exports.post = function(request, response) {

        var client = new twilio.RestClient('[ACCOUNT_SID]', 'AUTH_TOKEN');

        client.makeCall({
            to:'+16515556677', 
            from: '+14506667788',
            url: 'http://<your_mobile_service>.azure-mobile.net/api/makeCall' 

        }, function(err, responseData) {

            console.log(responseData.from);
            response.send(200, '');
        });
    };

[AZURE.INCLUDE [twilio_additional_services_and_next_steps](../../includes/twilio_additional_services_and_next_steps.md)]


[twilio_rest_making_calls]: http://www.twilio.com/docs/api/rest/making-calls

[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]: https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#


[azure_twilio_howto_dotnet]: /develop/net/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_java]: /develop/java/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_node]: /develop/nodejs/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_ruby]: /develop/ruby/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_python]: /develop/python/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_php]: /develop/php/how-to-guides/twilio-voice-and-sms-service/
 

<!---HONumber=July15_HO2-->