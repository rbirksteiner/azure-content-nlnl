1. 在 **[頻道]** 資料表的 insert.js 檔案中，尋找下列程式碼行，將它們標示為註釋或將它們從檔案中移除，然後儲存變更。

		sendNotifications(item.channelUri);

		function sendNotifications(uri) {
		    console.log("Uri: ", uri);
		    push.wns.sendToastText01(uri, {
		        text1: "Sample toast from sample insert"
		    }, {
		        success: function (pushResponse) {
		            console.log("Sent push:", pushResponse);
		        }
		    });
		}
		
	當您將變更儲存到 insert.js 檔案時，新版的指令碼會上傳到您的行動服務。

2. 在 [伺服器總管] 中，展開 TodoItem 資料表、開啟 insert.js 檔案，並使用下列程式碼來取代目前的 insert 函數，然後儲存變更：

		function insert(item, user, request) {
			request.execute({
				success: function() {
					request.respond();
					sendNotifications();
				}
			});
		
			function sendNotifications() {
				var channelsTable = tables.getTable('channels');
				channelsTable.read({
					success: function(devices) {
						devices.forEach(function(device) {
							push.wns.sendToastText04(device.channelUri, {
								text1: item.text
							}, {
								success: function(pushResponse) {
									console.log("Sent push:", pushResponse);
								}
							});
						});
					}
				});
			}
		}
		
	現在當您插入新的 TodoItem 時，所有已註冊裝置都會收到推送通知。

<!---HONumber=July15_HO2-->