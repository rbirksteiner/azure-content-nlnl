
1. 在管理入口網站中，按一下 [資料] 索引標籤，然後按一下 [TodoItem] 資料表。 
 
2. 在 [todoitem] 中，按一下 [指令碼] 索引標籤，然後選取 [插入]。
   
   	如此即會顯示 [TodoItem] 資料表中發生插入時所叫用的函數。

3. 使用下列程式碼來取代 insert 函數，然後按一下 **[儲存]**：

		function insert(item, user, request) {
		// Define a simple payload for a GCM notification.
	    var payload = {
	        "data": {
	            "message": item.text
	        }
	    };		
		request.execute({
		    success: function() {
		        // If the insert succeeds, send a notification.
		        push.gcm.send(null, payload, {
		            success: function(pushResponse) {
		                console.log("Sent push:", pushResponse, payload);
		                request.respond();
		                },              
		            error: function (pushResponse) {
		                console.log("Error Sending push:", pushResponse);
		                request.respond(500, { error: pushResponse });
		                }
		            });
		        },
		    error: function(err) {
		        console.log("request.execute error", err)
		        request.respond();
		    }
		  });
		}

   	如此即會註冊新的 insert 指令碼，該指令碼會在插入成功之後，使用 [gcm 物件](http://go.microsoft.com/fwlink/p/?LinkId=282645)將推播通知傳送給所有已註冊的裝置。

<!---HONumber=July15_HO2-->