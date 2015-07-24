資料是從 Wikipedia (<a href="http://www.wikipedia.org/">http://www.wikipedia.org/</a>) 上每家 S&P 500 公司的文章衍生而來 (儲存為 XML 資料)。<p> </p>上傳至 Azure ML Studio 之前，資料集已經過下列處理：<ul><li>擷取每家特定公司的文字內容</li><li>移除 wiki 格式</li><li>移除非英數字元</li><li>將所有的文字轉換成小寫</li><li>新增了知名公司類別</li></ul><p> </p>請注意，對某些公司而言，找不到某篇文章，所以記錄筆數小於 500 筆。

<!---HONumber=July15_HO2-->