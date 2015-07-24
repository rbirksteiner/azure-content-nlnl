

最後，您必須在 TodoItem 資料表上更新對插入作業註冊的指令碼，以傳送通知。

1. 按一下 [TodoItem]，按一下 [指令碼]，然後選取 [插入]。 

2. 使用下列程式碼來取代 insert 函數，然後按一下 **[儲存]**：

		function insert(item, user, request) {
		// Define a payload for the Windows Store toast notification.
		var payload = '<?xml version="1.0" encoding="utf-8"?><toast><visual>' +    
		    '<binding template="ToastText01">  <text id="1">' +
		    item.text + '</text></binding></visual></toast>';
		
		request.execute({
		    success: function() {
		        // If the insert succeeds, send a notification.
		    	push.wns.send(null, payload, 'wns/toast', {
		            success: function(pushResponse) {
		                console.log("Sent push:", pushResponse);
						request.respond();
		                },              
		                error: function (pushResponse) {
		                    console.log("Error Sending push:", pushResponse);
							request.respond(500, { error: pushResponse });
		                    }
		                });
		            }
		        });
		}

	這個 insert 指令碼會在插入成功後，傳送推播通知 (含所插入項目的文字) 給 Windows 市集應用程式註冊。

<!---HONumber=July15_HO2-->