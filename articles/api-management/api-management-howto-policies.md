<properties 
	pageTitle="Azure API 管理中的原則" 
	description="了解如何在 API 管理中建立、編輯和設定原則。" 
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


#Azure API 管理中的原則

在 Azure API 管理中，原則是系統的一項強大功能，可讓發行者透過組態來變更 API 的行為。原則是「陳述式」的集合，會在 API 的要求或回應上循序執行。常見的「陳述式」包括從 XML 至 JSON 的格式轉換，以及利用呼叫速率限制來限制開發人員傳入的呼叫數量。還有許多現成的原則可用。

如需原則陳述式及其設定的完整清單，請參閱[原則參考文件][]。

原則是在位於 API 取用者與受管理 API 之間的 Proxy 內套用。Proxy 會接收所有要求，然後通常原封不動地轉送至基礎 API。不過，原則可以套用至輸入要求和輸出要求。

如果原則不另行指定，則可以在任何 API 管理原則中，使用原則運算式做為屬性值或文字值。某些原則是以原則運算式為基礎，例如[控制流程][]和[設定變數][]原則。如需詳細資訊，請參閱[進階原則][]和[原則運算式][]。

## <a name="scopes"> </a>如何設定原則
原則可以整體設定，或在[產品][]、[API][] 或[操作][]的範圍上設定。若要設定原則，請瀏覽至發行者入口網站的原則編輯器。

![Policies menu][policies-menu]

原則編輯器包含三個主要區段：原則範圍 (上)、編輯原則的原則定義 (左) 和陳述式清單 (右)：

![Policies editor][policies-editor]

若要開始設定原則，您必須先選取要套用原則的範圍。以下的螢幕擷取畫面中選取了 Starter 產品。請注意，原則名稱旁邊的正方形符號表示此層級上已套用原則。

![Scope][policies-scope]

因為已套用原則，定義檢視中會顯示組態。

![設定][policies-configure]

原則最初會以唯讀顯示。為了編輯定義，請按一下 [設定原則] 動作。

![Edit][policies-edit]

原則定義是一份簡單的 XML 文件，描述一連串輸入和輸出陳述式。可直接在定義視窗中編輯 XML。右邊提供陳述式的清單，而適用於目前範圍的陳述式會啟用並反白，如以上螢幕擷取畫面中的 [限制呼叫速率] 陳述式所示。

按一下已啟用的陳述式會在定義檢視中的游標位置上加入適當的 XML。

[原則參考文件][]中提供原則陳述式及其設定的完整清單。

例如，若要加入新的陳述式以限制只有指定的 IP 位址才能傳入要求，請將游標移至 "inbound" XML 元素的內容之內，然後按一下 [Restrict caller IPs] 陳述式。

![Restriction policies][policies-restrict]

這樣會將 XML 片段加入至 "inbound" 元素，以提供如何設定陳述式的指引。

	<ip-filter action="allow | forbid">
		<address>address</address>
		<address-range from="address" to="address"/>
	</ip-filter>

若要限制輸入要求，只接受來自 IP 位址 1.2.3.4 的要求，請如下修改 XML：

	<ip-filter action="allow">
		<address>1.2.3.4</address>
	</ip-filter>

![Save][policies-save]

完成設定原則的陳述式時，按一下 [儲存]，變更會立即傳播至 API 管理 Proxy。

##<a name="sections"> </a>了解原則組態

原則是針對要求和回應而依序執行的一連串陳述式。組態會適當地劃分成輸入 (要求) 和輸出 (原則)，如下列組態所示。

	<policies>
		<inbound>
			<!-- statements to be applied to the request go here -->
		</inbound>
		<outbound>
			<!-- statements to be applied to the response go here -->
		</outbound>
	</policies>

因為原則可以在不同層級上指定 (全域、產品、api 和操作)，所以組態可讓您相對於父系原則，指定此定義的陳述式的執行順序。

例如，如果您在全域層級有一個原則，還有一個為 API 設定的原則，則每當使用該特定的 API 時 - 兩個原則都會套用。API 管理可透過 base 元素來指定組合式原則陳述式的固定順序。

	<policies>
    	<inbound>
        	<cross-domain />
        	<base />
        	<find-and-replace from="xyz" to="abc" />
    	</inbound>
	</policies>

在上述的原則定義範例中，cross-domain 陳述式會在任何更高的原則之前執行，而這些原則後面又接著 find-and-replace 原則。

注意：全域原則沒有父系原則，在全域原則中使用 `<base>` 項目沒有任何作用。

## 後續步驟

請查看有關原則運算式的下列影片。

> [AZURE.VIDEO policy-expressions-in-azure-api-management]

[原則參考文件]: api-management-policy-reference.md
[產品]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md#add-apis
[操作]: api-management-howto-add-operations.md

[進階原則]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[控制流程]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[設定變數]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[原則運算式]: https://msdn.microsoft.com/library/azure/dn910913.aspx

[policies-menu]: ./media/api-management-howto-policies/api-management-policies-menu.png
[policies-editor]: ./media/api-management-howto-policies/api-management-policies-editor.png
[policies-scope]: ./media/api-management-howto-policies/api-management-policies-scope.png
[policies-configure]: ./media/api-management-howto-policies/api-management-policies-configure.png
[policies-edit]: ./media/api-management-howto-policies/api-management-policies-edit.png
[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
[policies-save]: ./media/api-management-howto-policies/api-management-policies-save.png

<!---HONumber=62-->